# Validation
# Validation-유효성 검사

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Validation Quickstart](#validation-quickstart)
- [빠르게 유효성 검사 살펴보기](#validation-quickstart)
    - [Defining The Routes](#quick-defining-the-routes)
    - [라우트 정의하기](#quick-defining-the-routes)
    - [Creating The Controller](#quick-creating-the-controller)
    - [컨트롤러 생성하기](#quick-creating-the-controller)
    - [Writing The Validation Logic](#quick-writing-the-validation-logic)
    - [유효성 검사 로직 작성하기](#quick-writing-the-validation-logic)
    - [Displaying The Validation Errors](#quick-displaying-the-validation-errors)
    - [유효성 검사 에러 표시하기](#quick-displaying-the-validation-errors)
    - [A Note On Optional Fields](#a-note-on-optional-fields)
    - [옵션 필드에 대한 주의사항](#a-note-on-optional-fields)
- [Form Request Validation](#form-request-validation)
- [Form Request 유효성 검사](#form-request-validation)
    - [Creating Form Requests](#creating-form-requests)
    - [Form Requests 생성하기](#creating-form-requests)
    - [Authorizing Form Requests](#authorizing-form-requests)
    - [Form Requests 사용자 승인](#authorizing-form-requests)
    - [Customizing The Error Messages](#customizing-the-error-messages)
    - [에러 메세지 사용자 정의하기](#customizing-the-error-messages)
    - [Customizing The Validation Attributes](#customizing-the-validation-attributes)
    - [유효성 검사 속성 사용자 정의하기](#customizing-the-validation-attributes)
    - [Prepare Input For Validation](#prepare-input-for-validation)
    - [Input 검증을 위한 사전 준비](#prepare-input-for-validation)
- [Manually Creating Validators](#manually-creating-validators)
- [Validators 수동으로 생성하기](#manually-creating-validators)
    - [Automatic Redirection](#automatic-redirection)
    - [자동으로 리다이렉트하기](#automatic-redirection)
    - [Named Error Bags](#named-error-bags)
    - [이름이 지정된 Error Bags](#named-error-bags)
    - [After Validation Hook](#after-validation-hook)
    - [유효성 검사 이후에 후킹하기](#after-validation-hook)
- [Working With Error Messages](#working-with-error-messages)
- [에러 메세지 작업하기](#working-with-error-messages)
    - [Custom Error Messages](#custom-error-messages)
    - [사용자 정의 에러 메세지](#custom-error-messages)
- [Available Validation Rules](#available-validation-rules)
- [사용가능한 유효성 검사 규칙](#available-validation-rules)
- [Conditionally Adding Rules](#conditionally-adding-rules)
- [조건부로 규칙 추가하기](#conditionally-adding-rules)
- [Validating Arrays](#validating-arrays)
- [배열값 유효성 검사](#validating-arrays)
- [Custom Validation Rules](#custom-validation-rules)
- [사용자 정의 유효성 검사 규칙](#custom-validation-rules)
    - [Using Rule Objects](#using-rule-objects)
    - [Rule 객체 사용하기](#using-rule-objects)
    - [Using Closures](#using-closures)
    - [클로저 사용하기](#using-closures)
    - [Using Extensions](#using-extensions)
    - [확장기능 사용하기](#using-extensions)
    - [Implicit Extensions](#implicit-extensions)
    - [묵시적 확장기능](#implicit-extensions)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel provides several different approaches to validate your application's incoming data. By default, Laravel's base controller class uses a `ValidatesRequests` trait which provides a convenient method to validate incoming HTTP requests with a variety of powerful validation rules.

라라벨은 애플리케이션에 유입되는 데이터의 유효성을 검사하기 위한 다양한 방법을 제공합니다. 기본적으로, 라라벨의 베이스 콘트롤러 클래스는 다양하고 강력한 유효성 검사 규칙을 적용하여 유입되는 HTTP 요청의 유효성 검사를 위한 편리한 메소드를 제공하는 ValidatesRequests 트레이트-trait을 사용하고 있습니다.

<a name="validation-quickstart"></a>
## Validation Quickstart
## 빠르게 유효성 검사 살펴보기

To learn about Laravel's powerful validation features, let's look at a complete example of validating a form and displaying the error messages back to the user.

라라벨의 강력한 유효성 검사 기능에 대해 알아보기 위해서, form을 유효성 검사한 뒤 사용자에게 에러 메세지를 보여주는 예제를 살펴보도록 하겠습니다.

<a name="quick-defining-the-routes"></a>
### Defining The Routes
### 라우트 정의하기

First, let's assume we have the following routes defined in our `routes/web.php` file:

우선 다음의 라우트들이 `routes/web.php` 파일에 정의되어 있다고 가정해 보겠습니다.
    
    use App\Http\Controllers\PostController;

    Route::get('post/create', [PostController::class, 'create']);

    Route::post('post', [PostController::class, 'store']);

The `GET` route will display a form for the user to create a new blog post, while the `POST` route will store the new blog post in the database.

`GET` 라우트는 사용자가 새로운 블로그 포스트를 생성하기 위한 form을 나타낼 것이고, `POST` 라우트는 데이터베이스에 새로운 블로그 포스트를 저장할 것입니다.

<a name="quick-creating-the-controller"></a>
### Creating The Controller
### 컨트롤러 생성하기

Next, let's take a look at a simple controller that handles these routes. We'll leave the `store` method empty for now:

다음으로, 이 라우트들을 다루는 간단한 컨트롤러를 살펴보겠습니다. 지금은 `store` 메소드를 비어있는 채로 둘 것입니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;

    class PostController extends Controller
    {
        /**
         * Show the form to create a new blog post.
         *
         * @return Response
         */
        public function create()
        {
            return view('post.create');
        }

        /**
         * Store a new blog post.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            // Validate and store the blog post...
        }
    }

<a name="quick-writing-the-validation-logic"></a>
### Writing The Validation Logic
### 유효성 검사 로직 작성하기

Now we are ready to fill in our `store` method with the logic to validate the new blog post. To do this, we will use the `validate` method provided by the `Illuminate\Http\Request` object. If the validation rules pass, your code will keep executing normally; however, if validation fails, an exception will be thrown and the proper error response will automatically be sent back to the user. In the case of a traditional HTTP request, a redirect response will be generated, while a JSON response will be sent for AJAX requests.

이제 새로운 블로그 포스트에 대해 유효성을 검사하는 로직을 `store` 메소드에 채워넣을 준비가 되었습니다. 이를 위해서 `Illuminate\Http\Request` 객체에 제공되는 `validate` 메소드를 사용할 것입니다. 유효성 검사 규칙들을 통과하게되면 코드는 계속해서 정상적으로 실행됩니다. 하지만 유효성 검사를 통과하지 못할 경우, 예외-exception가 던져지고 적절한 오류 응답이 사용자에게 자동으로 보내질 것입니다. 전통적인 HTTP 요청의 경우, 리다이렉트 응답이 생성될 것이며 AJAX 요청에는 JSON 응답이 보내질 것입니다.

To get a better understanding of the `validate` method, let's jump back into the `store` method:

`validate` 메소드에 대해 더 잘 이해하기 위해, 다시 `store` 메소드로 돌아가 보겠습니다.

    /**
     * Store a new blog post.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $validatedData = $request->validate([
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ]);

        // The blog post is valid...
    }

As you can see, we pass the desired validation rules into the `validate` method. Again, if the validation fails, the proper response will automatically be generated. If the validation passes, our controller will continue executing normally.

보시다시피, 원하는 유효성 검사 규칙을 메소드에 전달하기만 하면 됩니다. 다시말해, 유효성 검사가 실패하면, 적절한 응답이 자동으로 생성됩니다. 유효성 검사를 통과하면 컨트롤러가 계속해서 정상적으로 로직을 수행합니다.

Alternatively, validation rules may be specified as arrays of rules instead of a single `|` delimited string:

또한, 유효성 검사 규칙은 구분자 `|` 대신 배열로 정의할 수 있습니다.

    $validatedData = $request->validate([
        'title' => ['required', 'unique:posts', 'max:255'],
        'body' => ['required'],
    ]);

You may use the `validateWithBag` method to validate a request and store any error messages within a [named error bag](#named-error-bags):

`validateWithBag` 메소드를 사용하여 요청을 검증하고 오류 메시지를 [named error bag](#named-error-bags)안에 저장할 수 있습니다.

    $validatedData = $request->validateWithBag('post', [
        'title' => ['required', 'unique:posts', 'max:255'],
        'body' => ['required'],
    ]);

#### Stopping On First Validation Failure
#### 유효성 검사가 처음 실패하면 검사 중단하기

Sometimes you may wish to stop running validation rules on an attribute after the first validation failure. To do so, assign the `bail` rule to the attribute:

경우에 따라서 유효성 검사가 처음 실패하면 유효 속성값에 대한 검사를 중단하기를 원할수도 있습니다. 이러한 경우 `bail` 규칙을 지정하면 됩니다.

    $request->validate([
        'title' => 'bail|required|unique:posts|max:255',
        'body' => 'required',
    ]);

In this example, if the `unique` rule on the `title` attribute fails, the `max` rule will not be checked. Rules will be validated in the order they are assigned.

이 예제에서는, `unique` 규칙으로 지정된 `title` 속성의 유효성 검사가 실패하면 `max` 규칙은 확인하지 않습니다. 유효성 검사 규칙은 선언된 순서대로 검사될 것입니다.

#### A Note On Nested Attributes
#### 중첩된 속성에 대한 유의사항

If your HTTP request contains "nested" parameters, you may specify them in your validation rules using "dot" syntax:

HTTP 요청이 "중첩된" 파라미터를 가지고 있다면 ".(점)" 문법을 사용하여 유효성 확인 규칙을 지정할 수 있습니다.

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'author.name' => 'required',
        'author.description' => 'required',
    ]);

On the other hand, if your field name contains a literal period, you can explicitly prevent this from being interpreted as "dot" syntax by escaping the period with a backslash:

반면에 필드 이름에 리터럴 마침표가 포함된 경우 마침표를 백슬래시로 이스케이프 하여 점 구문으로 해석되지 않도록 명시적으로 방지할 수 있습니다.

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'v1\.0' => 'required',
    ]);

<a name="quick-displaying-the-validation-errors"></a>
### Displaying The Validation Errors
### 유효성 검사 에러 표시하기

So, what if the incoming request parameters do not pass the given validation rules? As mentioned previously, Laravel will automatically redirect the user back to their previous location. In addition, all of the validation errors will automatically be [flashed to the session](/docs/{{version}}/session#flash-data).

그럼 입력되는 요청-request의 파라미터들이 유효성 검사를 통과하지 못하는 경우에는 어떻게 될까요? 앞서 언급한대로, 라라벨은 사용자를 자동으로 이전의 위치로 리다이렉트합니다. 또한 모든 유효성 확인 에러는 자동으로 [세션에 임시 저장](/docs/{{version}}/session#flash-data)될 것입니다.

Again, notice that we did not have to explicitly bind the error messages to the view in our `GET` route. This is because Laravel will check for errors in the session data, and automatically bind them to the view if they are available. The `$errors` variable will be an instance of `Illuminate\Support\MessageBag`. For more information on working with this object, [check out its documentation](#working-with-error-messages).

다시 말하지만, `GET` 라우트에서 에러 메세지를 뷰와 명시적으로 연결하지 않아도 됩니다. 왜냐하면, 라라벨이 세션 데이터에서 에러가 있는지 확인하고, 에러가 있다면 뷰에 자동으로 연결해 주기 때문입니다. `$errors` 변수는 `Illuminate\Support\MessageBag`의 인스턴스일 것입니다. 이 객체를 다루는 법에 대해 더 알아보고 싶다면 [문서을 확인해보시기 바랍니다](#working-with-error-messages).

> {tip} The `$errors` variable is bound to the view by the `Illuminate\View\Middleware\ShareErrorsFromSession` middleware, which is provided by the `web` middleware group. **When this middleware is applied an `$errors` variable will always be available in your views**, allowing you to conveniently assume the `$errors` variable is always defined and can be safely used.

> {tip} `$errors` 변수는 `web` 미들웨어 그룹에 의해서 제공되는 `Illuminate\View\Middleware\ShareErrorsFromSession` 미들웨어에 의해서 뷰와 연결됩니다. **이 미들웨어가 지정되었을 때 `$errors` 변수는 뷰 안에서 항상 사용가능 할 것이므로**, 편하게 생각하면 `$errors` 변수는 항상 선언되어 있다고 생각하고 안정하게 사용할 수 있습니다.

So, in our example, the user will be redirected to our controller's `create` method when validation fails, allowing us to display the error messages in the view:

따라서, 이 예제에서, 유효성 검사를 통과하지 못할 경우 사용자는 컨트롤러의 `create` 메소드로 리다이렉트 될것이고, 뷰에서는 에러 메세지가 표시됩니다.

    <!-- /resources/views/post/create.blade.php -->

    <h1>Create Post</h1>

    @if ($errors->any())
        <div class="alert alert-danger">
            <ul>
                @foreach ($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
            </ul>
        </div>
    @endif

    <!-- Create Post Form -->

#### The `@error` Directive
#### `@error` 지시어

You may also use the `@error` [Blade](/docs/{{version}}/blade) directive to quickly check if validation error messages exist for a given attribute. Within an `@error` directive, you may echo the `$message` variable to display the error message:

`@error` [Blade](/docs/{{version}}/blade) 지시어를 사용하여 주어진 속성에 대해 유효성 검사 오류 메시지가 있는지 빠르게 확인할 수도 있습니다. `@error` 지시어 내에서 `$message` 변수를 echo하여 에러 메시지를 표시 할 수 있습니다.

    <!-- /resources/views/post/create.blade.php -->

    <label for="title">Post Title</label>

    <input id="title" type="text" class="@error('title') is-invalid @enderror">

    @error('title')
        <div class="alert alert-danger">{{ $message }}</div>
    @enderror

<a name="a-note-on-optional-fields"></a>
### A Note On Optional Fields
### 옵션 필드에 대한 주의사항

By default, Laravel includes the `TrimStrings` and `ConvertEmptyStringsToNull` middleware in your application's global middleware stack. These middleware are listed in the stack by the `App\Http\Kernel` class. Because of this, you will often need to mark your "optional" request fields as `nullable` if you do not want the validator to consider `null` values as invalid. For example:

기본적으로 라라벨은 애플리케이션의 글로벌 미들웨어 스택에 `TrimStrings` 그리고 `ConvertEmptyStringsToNull` 미들웨어를 포함하고 있습니다. 이 미들웨어는 `App\Http\Kernel` 클래스의 미들웨어 스택에 나열되어 있습니다. 이때문에, 유효성 검사에서 `null`이 유효하지 않은것으로 간주하지 않으려면 "선택적-optional" request-요청 필드를 `nullable`로 표시할 필요도 있습니다. 예를들면:


    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

In this example, we are specifying that the `publish_at` field may be either `null` or a valid date representation. If the `nullable` modifier is not added to the rule definition, the validator would consider `null` an invalid date.

이 예제에서는 `publish_at` 필드가 `null`이거나 유효한 날짜 형식이라고 지정했습니다. 만약 `nullable` 규칙이 추가되지 않은 경우 `null`값은 유효하지 않다고 결정됩니다.

<a name="quick-ajax-requests-and-validation"></a>
#### AJAX Requests & Validation
#### AJAX 요청과 유효성 검사

In this example, we used a traditional form to send data to the application. However, many applications use AJAX requests. When using the `validate` method during an AJAX request, Laravel will not generate a redirect response. Instead, Laravel generates a JSON response containing all of the validation errors. This JSON response will be sent with a 422 HTTP status code.

이 예제에서는, 애플리케이션에 전통적인 form을 이용하여 데이터를 보냈습니다. 하지만 많은 애플리케이션이 AJAX 요청을 사용합니다. AJAX reqeust 중에서 `validate` 메소드를 사용한다면 라라벨은 리다이렉트 응답을 생성하지 않을 것입니다. 대신 라라벨은 유효성 검사의 모든 실패 에러들을 포함하는 JSON 응답을 생성할 것입니다. 이 JSON 응답은 422 HTTP 상태 코드와 함께 보내질 것입니다.

<a name="form-request-validation"></a>
## Form Request Validation
## Form Request 유효성 검사

<a name="creating-form-requests"></a>
### Creating Form Requests
### Form Requests 생성하기

For more complex validation scenarios, you may wish to create a "form request". Form requests are custom request classes that contain validation logic. To create a form request class, use the `make:request` Artisan CLI command:

보다 복잡한 유효성 검사 시나리오의 경우, "form request"를 작성하는 것이 좋습니다. Form request는 request 클래스의 사용자 정의(커스텀) 클래스로, 유효성 검사 로직을 가지고 있습니다. form request 클래스를 생성하기 위해서는 `make:request` 아티즌 CLI 명령어를 사용하십시오.

    php artisan make:request StoreBlogPost

The generated class will be placed in the `app/Http/Requests` directory. If this directory does not exist, it will be created when you run the `make:request` command. Let's add a few validation rules to the `rules` method:

생성된 클래스는 `app/Http/Requests` 디렉토리에 저장됩니다. 이 디렉토리가 존재하지 않는다면 `make:request` 명령어가 실행될 때 생성됩니다. `rules` 메소드에 몇가지 유효성 검사 규칙을 추가해 보겠습니다.


    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    public function rules()
    {
        return [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ];
    }

> {tip} You may type-hint any dependencies you need within the `rules` method's signature. They will automatically be resolved via the Laravel [service container](/docs/{{version}}/container).

> {tip} `rules` 메소드에 필요한 모든 의존성의 타입힌트를 지정할 수 있습니다. 이 의존성은 Laravel [서비스 컨테이너](/docs/{{version}}/container)를 통해 자동으로 처리됩니다.

So, how are the validation rules evaluated? All you need to do is type-hint the request on your controller method. The incoming form request is validated before the controller method is called, meaning you do not need to clutter your controller with any validation logic:

그렇다면 유효성 검사 규칙은 어떻게 실행할까요? 여러분이 해야할일은 컨트롤러 메소드에 request 를 타입-힌트 하는 것입니다. 유입된 form request 는 컨트롤러 메소드가 호출되기 전에 유효성 검사를 수행합니다. 즉 컨트롤러에 유효성 검사 로직을 포함시키지 않아도 됩니다.

    /**
     * Store the incoming blog post.
     *
     * @param  StoreBlogPost  $request
     * @return Response
     */
    public function store(StoreBlogPost $request)
    {
        // The incoming request is valid...

        // Retrieve the validated input data...
        $validated = $request->validated();
    }

If validation fails, a redirect response will be generated to send the user back to their previous location. The errors will also be flashed to the session so they are available for display. If the request was an AJAX request, a HTTP response with a 422 status code will be returned to the user including a JSON representation of the validation errors.

유효성 검사가 실패하면 리다이렉션 응답-response이 생성되어 사용자를 이전 위치로 되돌려 보냅니다. 오류를 세션에 임시저장하여 화면에 표시 할 수도 있습니다. AJAX 요청인 경우 유효성 검사 오류가 JSON 형식으로 재구성되어 422 상태 코드가있는 HTTP 응답이 사용자에게 반환됩니다.

#### Adding After Hooks To Form Requests
#### Form Request 에 After 후킹 추가하기

If you would like to add an "after" hook to a form request, you may use the `withValidator` method. This method receives the fully constructed validator, allowing you to call any of its methods before the validation rules are actually evaluated:

form request 에 "after" 후킹을 추가하려면, `withValidator` 메소드를 사용하면 됩니다. 이 메소드는 생성된 완전한 validator를 전달 받는데, 유효성 검사 규칙이 실제로 수행되기 전에 메소드 중 하나를 호출할 수 있도록 해줍니다.

    /**
     * Configure the validator instance.
     *
     * @param  \Illuminate\Validation\Validator  $validator
     * @return void
     */
    public function withValidator($validator)
    {
        $validator->after(function ($validator) {
            if ($this->somethingElseIsInvalid()) {
                $validator->errors()->add('field', 'Something is wrong with this field!');
            }
        });
    }

<a name="authorizing-form-requests"></a>
### Authorizing Form Requests
### Form Requests 사용자 승인

The form request class also contains an `authorize` method. Within this method, you may check if the authenticated user actually has the authority to update a given resource. For example, you may determine if a user actually owns a blog comment they are attempting to update:

form request 클래스는 또한 `authorize` 메소드를 가지고 있습니다. 이 메소드 안에서 여러분은 인증된 사용자가 주어진 리소스에 대해서 수정할 수 있는 권한이 있는지 확인할 수 있습니다. 예를 들어, 사용자가 블로그 포스트의 댓글을 수정하려고 시도할 때, 그 본인의 코멘트인지 확인할 수 있습니다.

    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        $comment = Comment::find($this->route('comment'));

        return $comment && $this->user()->can('update', $comment);
    }

Since all form requests extend the base Laravel request class, we may use the `user` method to access the currently authenticated user. Also note the call to the `route` method in the example above. This method grants you access to the URI parameters defined on the route being called, such as the `{comment}` parameter in the example below:

모든 form request는 라라벨의 베이스 request 클래스를 확장하고 있기 때문에, 현재 인증된 사용자에 엑세스 하기 위해서 `user` 메소드를 사용할 수 있습니다. 다음 예제에서 `route` 메소드를 호출하는것에 주목하십시오. 예제에서 이 메소드는 `{comment}` 파라미터와 같이 파라미터가 정의된 라우트의 URI 에 여러분이 엑세스 접근할 수 있는지 권한을 확인합니다.

    Route::post('comment/{comment}');

If the `authorize` method returns `false`, a HTTP response with a 403 status code will automatically be returned and your controller method will not execute.

만약 `authorize` 메소드가 `false`를 리턴하면, 403 HTTP 응답이 자동적으로 반환되고 컨트롤러 메소드는 실행되지 않을 것입니다.

If you plan to have authorization logic in another part of your application, return `true` from the `authorize` method:

여러분이 애플리케이션의 다른 부분에 있는 인증로직을 사용할 계획이라면, `authorize` 메소드에서 `true`를 리턴하면 됩니다.

    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

> {tip} You may type-hint any dependencies you need within the `authorize` method's signature. They will automatically be resolved via the Laravel [service container](/docs/{{version}}/container).

> {tip} `run` 메소드에 필요한 모든 의존성의 타입힌트를 지정할 수 있습니다. 이 의존성은 Laravel [서비스 컨테이너](/docs/{{version}}/container)를 통해 자동으로 처리됩니다.

<a name="customizing-the-error-messages"></a>
### Customizing The Error Messages
### 에러 메세지를 사용자 정의하기(커스터마이징하기)

You may customize the error messages used by the form request by overriding the `messages` method. This method should return an array of attribute / rule pairs and their corresponding error messages:

`messages` 메소드를 대체하면 form request-요청이 사용하는 에러 메세지를 커스터마이즈할 수 있습니다. 이 메소드는 속성 / 규칙의 쌍으로된 배열과 그에 상응하는 오류 메세지를 반환합니다.

    /**
     * Get the error messages for the defined validation rules.
     *
     * @return array
     */
    public function messages()
    {
        return [
            'title.required' => 'A title is required',
            'body.required' => 'A message is required',
        ];
    }

<a name="customizing-the-validation-attributes"></a>
### Customizing The Validation Attributes
### 유효성 검사 속성 사용자 정의하기

If you would like the `:attribute` portion of your validation message to be replaced with a custom attribute name, you may specify the custom names by overriding the `attributes` method. This method should return an array of attribute / name pairs:

유효성 검사 메시지의 `:attribute` 부분을 커스텀 속성 이름으로 대체하기를 원할 경우, `attributes` 메소드를 오버라이드하여 커스텀 이름을 지정할 수 있습니다. 이 메소드는 속성 / 이름의 쌍을 가진 배열을 반환해야합니다.

    /**
     * Get custom attributes for validator errors.
     *
     * @return array
     */
    public function attributes()
    {
        return [
            'email' => 'email address',
        ];
    }

<a name="prepare-input-for-validation"></a>
### Prepare Input For Validation
### Input 검증을 위한 사전 준비

If you need to sanitize any data from the request before you apply your validation rules, you can use the `prepareForValidation` method:

유효성 검사 규칙을 적용하기 전에 요청에서 데이터를 삭제해야하는 경우 `prepareForValidation` 메서드를 사용할 수 있습니다.

    use Illuminate\Support\Str;

    /**
     * Prepare the data for validation.
     *
     * @return void
     */
    protected function prepareForValidation()
    {
        $this->merge([
            'slug' => Str::slug($this->slug),
        ]);
    }

<a name="manually-creating-validators"></a>
## Manually Creating Validators
## Validator 수동으로 생성하기

If you do not want to use the `validate` method on the request, you may create a validator instance manually using the `Validator` [facade](/docs/{{version}}/facades). The `make` method on the facade generates a new validator instance:

request 의 `validate` 메소드를 사용하고 싶지 않다면, `Validator` [파사드](/docs/{{version}}/facades)를 사용하여 validator 인스턴스를 수동으로 생성할 수 있습니다. 파사드에 `make` 메소드를 사용하면 새로운 validator 인스턴스가 생성됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Validator;

    class PostController extends Controller
    {
        /**
         * Store a new blog post.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            $validator = Validator::make($request->all(), [
                'title' => 'required|unique:posts|max:255',
                'body' => 'required',
            ]);

            if ($validator->fails()) {
                return redirect('post/create')
                            ->withErrors($validator)
                            ->withInput();
            }

            // Store the blog post...
        }
    }

The first argument passed to the `make` method is the data under validation. The second argument is the validation rules that should be applied to the data.

`make` 메소드로 전달되는 첫번째 인자는 유효성 검사를 받을 데이터입니다. 두번째 인자는 데이터에 적용되어야 하는 유효성 검사 규칙들입니다.

After checking if the request validation failed, you may use the `withErrors` method to flash the error messages to the session. When using this method, the `$errors` variable will automatically be shared with your views after redirection, allowing you to easily display them back to the user. The `withErrors` method accepts a validator, a `MessageBag`, or a PHP `array`.

request-요청이 유효성 검사에 실패하였는지 확인한 후에 `withErrors` 메소드로 세션에 에러 메세지를 임시저장-flash 할 수 있습니다. 이 메소드를 사용하면 리다이렉트 후에 `$errors` 변수가 자동으로 뷰에서 공유되어 손쉽게 사용자에게 보여질 수 있습니다. `withErrors` 메소드는 validator, `MessageBag`, 혹은 PHP `array`를 전달 받습니다.

<a name="automatic-redirection"></a>
### Automatic Redirection
### 자동으로 리다이렉트하기

If you would like to create a validator instance manually but still take advantage of the automatic redirection offered by the request's `validate` method, you may call the `validate` method on an existing validator instance. If validation fails, the user will automatically be redirected or, in the case of an AJAX request, a JSON response will be returned:

수동으로 validator 인스턴스를 생성하더라도, request의 `validate` 메소드에 의해서 자동으로 리다이렉트 되는 이점을 유지하고 싶다면, validator 인스턴스에 `validate` 메소드를 호출하면 됩니다. 유효성 검사가 실패하는 경우, 사용자는 자동으로 리다이렉트 되거나, 또는 AJAX 요청인 경우, JSON이 반환됩니다.

    Validator::make($request->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ])->validate();

You may use the `validateWithBag` method to store the error messages in a [named error bag](#named-error-bags) if validation fails:

유효성 검사에에 실패하면 `validateWithBag` 메소드를 사용하여 [named error bag](#named-error-bags)안에 오류 메시지를 저장할 수 있습니다.

    Validator::make($request->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ])->validateWithBag('post');
    
<a name="named-error-bags"></a>
### Named Error Bags
### 이름이 지정된 Error Bags

If you have multiple forms on a single page, you may wish to name the `MessageBag` of errors, allowing you to retrieve the error messages for a specific form. Pass a name as the second argument to `withErrors`:

한 페이지 안에서 여러개의 form을 가지고 있다면 에러들의 `MessageBag`에 이름을 붙여 지정한 form에 맞는 에러 메세지를 조회할 수 있도록 할 수 있습니다. `withErrors`에 이름을 두번째 인자로 전달하면 됩니다.

    return redirect('register')
                ->withErrors($validator, 'login');

You may then access the named `MessageBag` instance from the `$errors` variable:

그러면 `$errors` 변수에서 지정된 `MessageBag` 인스턴스에 접근할 수 있습니다.

    {{ $errors->login->first('email') }}

<a name="after-validation-hook"></a>
### After Validation Hook
### 유효성 검사 이후에 후킹하기

The validator also allows you to attach callbacks to be run after validation is completed. This allows you to easily perform further validation and even add more error messages to the message collection. To get started, use the `after` method on a validator instance:

유효성 검사가 완료된 후에 실행하고자 하는 콜백함수를 validator에 추가할 수 있습니다. 이를 통하면, 손쉽게 더 많은 유효성 검사를 실행할 수 있도록 하고, 에러 메시지 컬렉션에 에러 메시지를 더 추가할 수도 있습니다. 다음처럼 validator 인스턴스의 `after` 메소드를 사용하면 됩니다.

    $validator = Validator::make(...);

    $validator->after(function ($validator) {
        if ($this->somethingElseIsInvalid()) {
            $validator->errors()->add('field', 'Something is wrong with this field!');
        }
    });

    if ($validator->fails()) {
        //
    }

<a name="working-with-error-messages"></a>
## Working With Error Messages
## 에러 메시지 작업하기

After calling the `errors` method on a `Validator` instance, you will receive an `Illuminate\Support\MessageBag` instance, which has a variety of convenient methods for working with error messages. The `$errors` variable that is automatically made available to all views is also an instance of the `MessageBag` class.

`Validator` 인스턴스의 `errors` 메소드를 호출하면, 에러 메시지를 편하게 사용할 수 있는 다양한 메소드를 가진 `MessageBag` 인스턴스를 받을 수 있습니다, 이것은 오류메세지를 처리하기 위한 다양하고 편리한 메소드들을 가집니다. `$errors` 변수는 자동으로 모든 뷰에서 `MessageBag` 클래스 인스턴스로써 사용가능합니다.

#### Retrieving The First Error Message For A Field
#### 하나의 필드에 대한 첫번째 에러 메시지 조회하기

To retrieve the first error message for a given field, use the `first` method:

특정 필드에 대한 첫번째 에러 메세지를 조회하려면 `first` 메소드를 사용하면 됩니다.

    $errors = $validator->errors();

    echo $errors->first('email');

#### Retrieving All Error Messages For A Field
#### 하나의 필드에 대한 모든 에러 메세지 조회하기

If you need to retrieve an array of all the messages for a given field, use the `get` method:

특정 필드에 대한 모든 메세지에 대한 배열을 조회하고자 한다면, `get` 메소드를 사용하면 됩니다.

    foreach ($errors->get('email') as $message) {
        //
    }

If you are validating an array form field, you may retrieve all of the messages for each of the array elements using the `*` character:

배열형태의 form 필드에 대해서 유효성 검사를 하려면, 배열의 각각의 요소는 `*` 문자열을 사용하여 모든 메세지를 조회할 수 있습니다.

    foreach ($errors->get('attachments.*') as $message) {
        //
    }

#### Retrieving All Error Messages For All Fields
#### 모든 필드에 대한 모든 에러 메세지 조회하기

To retrieve an array of all messages for all fields, use the `all` method:

모든 필드에 대한 모든 에러 메세지를 조회하기 위해서는 `all` 메소들 사용하면 됩니다.

    foreach ($errors->all() as $message) {
        //
    }

#### Determining If Messages Exist For A Field
#### 하나의 필드에 대하여 에러 메시지가 존재하는지 검사하기

The `has` method may be used to determine if any error messages exist for a given field:

`has` 메소드는 특정 필드에 대한 에러 메세지가 존재하는지 확인하는데 사용합니다.

    if ($errors->has('email')) {
        //
    }

<a name="custom-error-messages"></a>
### Custom Error Messages
### 사용자 지정(커스텀) 에러 메세지

If needed, you may use custom error messages for validation instead of the defaults. There are several ways to specify custom messages. First, you may pass the custom messages as the third argument to the `Validator::make` method:

필요하다면 기본적인 에러 메세지 대신에 커스텀 에러 메세지를 유효성 검사에 사용할 수 있습니다. 커스텀 메세지를 지정하는 데에는 여러가지 방법이 있습니다. 먼저 `Validator::make` 메소드에 커스텀 메세지를 세번째 인자로 전달할 수 있습니다.

    $messages = [
        'required' => 'The :attribute field is required.',
    ];

    $validator = Validator::make($input, $rules, $messages);

In this example, the `:attribute` placeholder will be replaced by the actual name of the field under validation. You may also utilize other placeholders in validation messages. For example:

다음의 예에서 `:attribute` 플레이스홀더는 유효성 검사를 받는 필드의 실제 이름으로 대체됩니다. 유효성 검사 메세지에서 다른 플레이스 홀더들 또한 활용할 수 있습니다. 예를 들어:

    $messages = [
        'same' => 'The :attribute and :other must match.',
        'size' => 'The :attribute must be exactly :size.',
        'between' => 'The :attribute value :input is not between :min - :max.',
        'in' => 'The :attribute must be one of the following types: :values',
    ];

#### Specifying A Custom Message For A Given Attribute
#### 주어진 속성에 대해 커스텀 메세지 지정하기

Sometimes you may wish to specify a custom error message only for a specific field. You may do so using "dot" notation. Specify the attribute's name first, followed by the rule:

종종 하나의 특정 필드에 대해서만 커스텀 오류 메세지를 지정해야 하는 경우가 있습니다. 이것은 ".(점)" 표기법을 통해서 할 수 있습니다. 속성의 이름을 먼저 지정하고, 규칙을 명시하면됩니다.

    $messages = [
        'email.required' => 'We need to know your e-mail address!',
    ];

<a name="localization"></a>
#### Specifying Custom Messages In Language Files
#### 언어 파일에 커스텀 메세지 지정하기

In most cases, you will probably specify your custom messages in a language file instead of passing them directly to the `Validator`. To do so, add your messages to `custom` array in the `resources/lang/xx/validation.php` language file.

대부분의 경우에서, `Validator`에 직접 메세지를 전달하는 대신, 언어 파일의 커스텀 메세지를 지정하기 원할 수 있습니다. 이렇게 하기 위해서는 `resources/lang/xx/validation.php` 언어 파일의 `custom` 배열에 메세지를 추가하면 됩니다.

    'custom' => [
        'email' => [
            'required' => 'We need to know your e-mail address!',
        ],
    ],

#### Specifying Custom Attribute Values
#### 사용자 지정 속성 값 지정

If you would like the `:attribute` portion of your validation message to be replaced with a custom attribute name, you may specify the custom name in the `attributes` array of your `resources/lang/xx/validation.php` language file:

유효성 검사 메세지의 `:attribute` 부분을 사용자 정의 속성 이름으로 교체하려면 `resources/lang/xx/validation.php`언어 파일의 `attributes` 배열에 사용자 정의 이름을 지정하면 됩니다.

    'attributes' => [
        'email' => 'email address',
    ],

You may also pass the custom attributes as the fourth argument to the `Validator::make` method:

사용자 정의 속성을 `Validator::make` 메소드의 네 번째 인수로 전달할 수도 있습니다.

    $customAttributes = [
        'email' => 'email address',
    ];

    $validator = Validator::make($input, $rules, $messages, $customAttributes);

#### Specifying Custom Values In Language Files
#### 언어 파일에서 사용자 값 지정

Sometimes you may need the `:value` portion of your validation message to be replaced with a custom representation of the value. For example, consider the following rule that specifies that a credit card number is required if the `payment_type` has a value of `cc`:

때로는 유효성 검사 메시지의 `:value` 부분의 값을 사용자 정의 표현으로 대체해야 할 수도 있습니다. 예를 들어 다음과 같이 `payment_type` 의 값이 `cc` 인 경우 신용 카드 번호가 필요하다는 규칙을 생각해 보겠습니다.

    $request->validate([
        'credit_card_number' => 'required_if:payment_type,cc'
    ]);

If this validation rule fails, it will produce the following error message:

이 유효성 검사 규칙이 실패하면 다음과 같은 오류 메시지가 나타납니다.

    The credit card number field is required when payment type is cc.

Instead of displaying `cc` as the payment type value, you may specify a custom value representation in your `validation` language file by defining a `values` array:

결제 유형 값으로 `cc` 를 표시하는 대신 `values` 배열을 정의하여 `validation` 언어 파일에 사용자 정의 값 표현을 지정할 수 있습니다.

    'values' => [
        'payment_type' => [
            'cc' => 'credit card'
        ],
    ],

Now if the validation rule fails it will produce the following message:

이제 유효성 검사 규칙이 실패하면 다음 메시지가 생성됩니다.

    The credit card number field is required when payment type is credit card.

<a name="available-validation-rules"></a>
## Available Validation Rules
## 사용가능한 유효성 검사 규칙

Below is a list of all available validation rules and their function:

다음은 사용 가능한 모든 유효성 검사 규칙과 그 기능의 목록입니다.

<style>
    .collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    .collection-method-list a {
        display: block;
    }
</style>

<div class="collection-method-list" markdown="1">


- [Accepted](#rule-accepted)
- [Active URL](#rule-active-url)
- [After (Date)](#rule-after)
- [After Or Equal (Date)](#rule-after-or-equal)
- [Alpha](#rule-alpha)
- [Alpha Dash](#rule-alpha-dash)
- [Alpha Numeric](#rule-alpha-num)
- [Array](#rule-array)
- [Bail](#rule-bail)
- [Before (Date)](#rule-before)
- [Before Or Equal (Date)](#rule-before-or-equal)
- [Between](#rule-between)
- [Boolean](#rule-boolean)
- [Confirmed](#rule-confirmed)
- [Date](#rule-date)
- [Date Equals](#rule-date-equals)
- [Date Format](#rule-date-format)
- [Different](#rule-different)
- [Digits](#rule-digits)
- [Digits Between](#rule-digits-between)
- [Dimensions (Image Files)](#rule-dimensions)
- [Distinct](#rule-distinct)
- [Email](#rule-email)
- [Ends With](#rule-ends-with)
- [Exclude If](#rule-exclude-if)
- [Exclude Unless](#rule-exclude-unless)
- [Exists (Database)](#rule-exists)
- [File](#rule-file)
- [Filled](#rule-filled)
- [Greater Than](#rule-gt)
- [Greater Than Or Equal](#rule-gte)
- [Image (File)](#rule-image)
- [In](#rule-in)
- [In Array](#rule-in-array)
- [Integer](#rule-integer)
- [IP Address](#rule-ip)
- [JSON](#rule-json)
- [Less Than](#rule-lt)
- [Less Than Or Equal](#rule-lte)
- [Max](#rule-max)
- [MIME Types](#rule-mimetypes)
- [MIME Type By File Extension](#rule-mimes)
- [Min](#rule-min)
- [Not In](#rule-not-in)
- [Not Regex](#rule-not-regex)
- [Nullable](#rule-nullable)
- [Numeric](#rule-numeric)
- [Password](#rule-password)
- [Present](#rule-present)
- [Regular Expression](#rule-regex)
- [Required](#rule-required)
- [Required If](#rule-required-if)
- [Required Unless](#rule-required-unless)
- [Required With](#rule-required-with)
- [Required With All](#rule-required-with-all)
- [Required Without](#rule-required-without)
- [Required Without All](#rule-required-without-all)
- [Same](#rule-same)
- [Size](#rule-size)
- [Sometimes](#conditionally-adding-rules)
- [Starts With](#rule-starts-with)
- [String](#rule-string)
- [Timezone](#rule-timezone)
- [Unique (Database)](#rule-unique)
- [URL](#rule-url)
- [UUID](#rule-uuid)


</div>

<a name="rule-accepted"></a>
#### accepted

The field under validation must be _yes_, _on_, _1_, or _true_. This is useful for validating "Terms of Service" acceptance.

필드의 값이 _yes_, _on_, _1_, 또는 _true_ 이어야 합니다. 이 것은 "이용약관" 동의와 같은 필드의 검사에 유용합니다.

<a name="rule-active-url"></a>
#### active_url

The field under validation must have a valid A or AAAA record according to the `dns_get_record` PHP function. The hostname of the provided URL is extracted using the `parse_url` PHP function before being passed to `dns_get_record`.

검증중인 필드는 `dns_get_record` PHP 함수에 따라 유효한 A 또는 AAAA 레코드를 가져야합니다. 제공된 URL의 호스트 이름은 `pars_url` PHP 함수를 사용하여 추출되어 `dns_get_record`에 전달됩니다.

<a name="rule-after"></a>
#### after:_date_

The field under validation must be a value after a given date. The dates will be passed into the `strtotime` PHP function:

필드의 값이 주어진 날짜 이후여야 합니다. 이때 날짜는 `strtotime` PHP 함수를 통해 생성된 값입니다.

    'start_date' => 'required|date|after:tomorrow'

Instead of passing a date string to be evaluated by `strtotime`, you may specify another field to compare against the date:

`strtotime`에 의해 계산될 날짜 문자열을 전달하는 대신 날짜와 비교할 다른 필드를 명시할 수 있습니다.

    'finish_date' => 'required|date|after:start_date'

<a name="rule-after-or-equal"></a>
#### after\_or\_equal:_date_

The field under validation must be a value after or equal to the given date. For more information, see the [after](#rule-after) rule.

필드의 값이 주어진 날짜와 동일하거나, 이후여야 합니다. 보다 자세한 사항은 [after](#rule-after) 규칙을 확인하십시오.

<a name="rule-alpha"></a>
#### alpha

The field under validation must be entirely alphabetic characters.

필드의 값이 완벽하게 (숫자나 기호가 아닌) 알파벳[자음과 모음] 문자로 이루어져야 합니다.

(역자주: 영문 알파벳만을 의미하지 않고, 숫자나 기호가 아닌경우에 해당하여, 한글도 허용합니다.)

<a name="rule-alpha-dash"></a>
#### alpha_dash

The field under validation may have alpha-numeric characters, as well as dashes and underscores.

필드의 값이 (숫자나 기호가 아닌) 알파벳[자음과 모음] 문자 및 숫자와 dash(-), underscore(_)로 이루어져야 합니다.

<a name="rule-alpha-num"></a>
#### alpha_num

The field under validation must be entirely alpha-numeric characters.

필드의 값이 완벽하게 (숫자나 기호가 아닌) 알파벳[자음과 모음] 문자 및 숫자로 이루어져야 합니다.

<a name="rule-array"></a>
#### array

The field under validation must be a PHP `array`.

필드의 값이 반드시 PHP 배열 형태이어야 합니다.

<a name="rule-bail"></a>
#### bail

Stop running validation rules after the first validation failure.

유효성 검사가 처음 실패하면 유효성 검사를 중단합니다.

<a name="rule-before"></a>
#### before:_date_

The field under validation must be a value preceding the given date. The dates will be passed into the PHP `strtotime` function. In addition, like the [`after`](#rule-after) rule, the name of another field under validation may be supplied as the value of `date`.

필드의 값이 반드시 주어진 날짜보다 앞서야 합니다. 날짜는 `strtotime` PHP 함수를 통해 비교됩니다. 또한, [`after`](#rule-after) 규칙처럼 유효성 검사중인 다른 필드의 이름이 `date` 의 값으로 제공 될 수 있습니다.

<a name="rule-before-or-equal"></a>
#### before\_or\_equal:_date_

The field under validation must be a value preceding or equal to the given date. The dates will be passed into the PHP `strtotime` function. In addition, like the [`after`](#rule-after) rule, the name of another field under validation may be supplied as the value of `date`.

필드의 값이 주어진 날짜보다 앞서거나, 같아야 합니다. 날짜는 `strtotime` PHP 함수를 통해서 비교됩니다. 또한, [`after`](#rule-after) 규칙처럼 유효성 검사중인 다른 필드의 이름이 `date` 의 값으로 제공 될 수 있습니다.

<a name="rule-between"></a>
#### between:_min_,_max_

The field under validation must have a size between the given _min_ and _max_. Strings, numerics, arrays, and files are evaluated in the same fashion as the [`size`](#rule-size) rule.

필드의 값이, 주어진 _min_ 과 _max_ 의 사이의 값이어야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 규칙에 의해 같은 방식으로 계산될 수 있습니다.

<a name="rule-boolean"></a>
#### boolean

The field under validation must be able to be cast as a boolean. Accepted input are `true`, `false`, `1`, `0`, `"1"`, and `"0"`.

필드의 값이 반드시 boolean으로 캐스팅될 수 있어야 합니다. 허용되는 값은 `true`, `false`, `1`, `0`, `"1"`, `"0"` 입니다.

<a name="rule-confirmed"></a>
#### confirmed

The field under validation must have a matching field of `foo_confirmation`. For example, if the field under validation is `password`, a matching `password_confirmation` field must be present in the input.

필드의 값이 `foo_confirmation`의 매칭되는 필드를 가져야 합니다. 예를 들어 만약 필드가 `password`라면, `password_confirmation`라는 필드가 입력값 중에 있어야 합니다.

<a name="rule-date"></a>
#### date

The field under validation must be a valid, non-relative date according to the `strtotime` PHP function.

유효성 검사중인 필드는 `strtotime` PHP 함수에 따라 유효한 비 상대(non-relative) 날짜 여야합니다.

<a name="rule-date-equals"></a>
#### date_equals:_date_

The field under validation must be equal to the given date. The dates will be passed into the PHP `strtotime` function.

필드의 값이 주어진 날짜와 일치해야 합니다. 날짜값은 PHP의 `strtotime` 함수로 전달됩니다.

<a name="rule-date-format"></a>
#### date_format:_format_

The field under validation must match the given _format_. You should use **either** `date` or `date_format` when validating a field, not both. This validation rule supports all formats supported by PHP's [DateTime](https://www.php.net/manual/en/class.datetime.php) class.

필드의 값이 반드시 주어진 _format_ 과 일지해야 합니다. 필드의 유효성을 검사할 때에는 `date`와 `date_format` 중 **하나만** 사용해야 합니다. 이 유효성 규칙은 PHP의 [DateTime](https://www.php.net/manual/en/class.datetime.php) 클래스에서 지원하는 모든 포맷을 지원합니다.

<a name="rule-different"></a>
#### different:_field_

The field under validation must have a different value than _field_.

필드의 값이 주어진 _field_ 의 값과 달라야 합니다.

<a name="rule-digits"></a>
#### digits:_value_

The field under validation must be _numeric_ and must have an exact length of _value_.

필드의 값이 반드시 _숫자_여야 하고, 길이가 _value_이어야 합니다.

<a name="rule-digits-between"></a>
#### digits_between:_min_,_max_

The field under validation must be _numeric_ and must have a length between the given _min_ and _max_.

필드의 값이 반드시 _숫자_ 여야 하고, 주어진 _min_ 과 _max_ 사이의 길이를 가져야 합니다.

<a name="rule-dimensions"></a>
#### dimensions

The file under validation must be an image meeting the dimension constraints as specified by the rule's parameters:

필드의 값이 규칙에 지정된 파라미터들을 만족하는 이미지이어야 합니다.

    'avatar' => 'dimensions:min_width=100,min_height=200'

Available constraints are: _min\_width_, _max\_width_, _min\_height_, _max\_height_, _width_, _height_, _ratio_.

사용가능한 제약은:  _min\_width_, _max\_width_, _min\_height_, _max\_height_, _width_, _height_, _ratio_ 입니다.

A _ratio_ constraint should be represented as width divided by height. This can be specified either by a statement like `3/2` or a float like `1.5`:

_ratio_ 제약은 가로를 세로로 나눈 비율을 표현해야합니다. 이는 `3/2` 또는 소수점 `1.5` 처럼 지정될 수 있습니다.

    'avatar' => 'dimensions:ratio=3/2'

Since this rule requires several arguments, you may use the `Rule::dimensions` method to fluently construct the rule:

이 규칙은 여러개의 인자를 필요로 하는데, 다음처럼 `Rule:dimensions` 메소드를 사용하여 유연하게 규칙을 생성할 수 있습니다.

    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'avatar' => [
            'required',
            Rule::dimensions()->maxWidth(1000)->maxHeight(500)->ratio(3 / 2),
        ],
    ]);

<a name="rule-distinct"></a>
#### distinct

When working with arrays, the field under validation must not have any duplicate values.

배열에서 동작하며, 필드의 값이 배열안의 다른 값과 중복되지 않아야 합니다.

    'foo.*.id' => 'distinct'

<a name="rule-email"></a>
#### email

The field under validation must be formatted as an e-mail address. Under the hood, this validation rule makes use of the [`egulias/email-validator`](https://github.com/egulias/EmailValidator) package for validating the email address. By default the `RFCValidation` validator is applied, but you can apply other validation styles as well:

필드의 값이 이메일 주소 형식이어야 합니다. 다시 말하면, 이 규칙은 이메일 주소의 유효성 검사를 할 때  [`egulias/email-validator`](https://github.com/egulias/EmailValidator) 패키지를 이용합니다. `RFCValidation` validator가 기본값으로 설정되어 있지만, 다른 유효성 검사 방법을 적용할 수 있습니다.

    'email' => 'email:rfc,dns'

The example above will apply the `RFCValidation` and `DNSCheckValidation` validations. Here's a full list of validation styles you can apply:

위의 예시는 `RFCValidation`와 `DNSCheckValidation` 유효성 검사를 적용합니다. 가능한 모든 유효성 검사 방법은 아래를 참조하십시오.

<div class="content-list" markdown="1">
- `rfc`: `RFCValidation`
- `strict`: `NoRFCWarningsValidation`
- `dns`: `DNSCheckValidation`
- `spoof`: `SpoofCheckValidation`
- `filter`: `FilterEmailValidation`
</div>

The `filter` validator, which uses PHP's `filter_var` function under the hood, ships with Laravel and is Laravel's pre-5.8 behavior. The `dns` and `spoof` validators require the PHP `intl` extension.

PHP의 `filter_var` 함수를 사용하는 `filter` validator는 라라벨에 포함되어 있으며 5.8 이전 버전의 동작입니다. `dns`와 `spoof` 유효성 검사에는 PHP `intl` 확장모듈이 필요합니다.

<a name="rule-ends-with"></a>
#### ends_with:_foo_,_bar_,...

The field under validation must end with one of the given values.

필드의 값이 주어진 값 중 하나로 끝나야합니다.

<a name="rule-exclude-if"></a>
#### exclude_if:_anotherfield_,_value_

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods if the _anotherfield_ field is equal to _value_.

_anotherfield_ 필드가 _value_ 와 같은 경우 유효성 검증 대상 필드는 `validate` 및 `validated` 메소드가 리턴 한 request 데이터에서 제외됩니다.

<a name="rule-exclude-unless"></a>
#### exclude_unless:_anotherfield_,_value_

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods unless _anotherfield_'s field is equal to _value_.

_anotherfield_ 의 필드가 _value_ 와 같지 않으면 유효성 검증 대상 필드는 `validate` 및 `validated` 메소드가 리턴 한 request 데이터에서 제외됩니다.

<a name="rule-exists"></a>
#### exists:_table_,_column_

The field under validation must exist on a given database table.

필드의 값이 주어진 데이터베이스 테이블에 존재하는 값이어야 합니다.

#### Basic Usage Of Exists Rule
#### exists 규칙의 기본 사용법

    'state' => 'exists:states'

If the `column` option is not specified, the field name will be used.

`column` 옵션을 지정하지 않으면 필드 이름이 사용됩니다.

#### Specifying A Custom Column Name
#### 특정 컬럼명 지정하기

    'state' => 'exists:states,abbreviation'

Occasionally, you may need to specify a specific database connection to be used for the `exists` query. You can accomplish this by prepending the connection name to the table name using "dot" syntax:

때때로, `exists` 쿼리에서 사용할 데이터베이스 커넥션을 지정하고자 할 수도 있습니다. 이경우 커넥션의 이름을 테이블 이름 앞에 "점" 문법을 사용하여 표기할 수도 있습니다.

    'email' => 'exists:connection.staff,email'

Instead of specifying the table name directly, you may specify the Eloquent model which should be used to determine the table name:

테이블 이름을 직접 지정하는 대신 테이블 이름을 결정하는 데 사용해야하는 Eloquent 모델을 지정할 수 있습니다.

    'user_id' => 'exists:App\Models\User,id'

If you would like to customize the query executed by the validation rule, you may use the `Rule` class to fluently define the rule. In this example, we'll also specify the validation rules as an array instead of using the `|` character to delimit them:

유효성 검사 규칙에 의해서 실행되는 쿼리를 커스터마이징 하고자 한다면, 규칙에 `Rule` 클래스를 정의해서 사용할 수 있습니다. 다음 예제에서 `|` 문자를 구분자로 사용하는 대신에 유효성 검사 규칙을 배열로 지정하고 있습니다.

    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'email' => [
            'required',
            Rule::exists('staff')->where(function ($query) {
                $query->where('account_id', 1);
            }),
        ],
    ]);

<a name="rule-file"></a>
#### file

The field under validation must be a successfully uploaded file.

필드의 값이 완전히 업로드된 파일이어야 합니다.

<a name="rule-filled"></a>
#### filled

The field under validation must not be empty when it is present.

필드가 존재하는 경우 값이 비어있으면 안됩니다.

<a name="rule-gt"></a>
#### gt:_field_

The field under validation must be greater than the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

필드의 값이 주어진 다른 필드의 값보다 커야합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 계산됩니다.

<a name="rule-gte"></a>
#### gte:_field_

The field under validation must be greater than or equal to the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

필드의 값이 주어진 다른 필드의 값보다 크거나 같아야합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 계산됩니다.

<a name="rule-image"></a>
#### image

The file under validation must be an image (jpeg, png, bmp, gif, svg, or webp)

이미지 파일(jpeg, png, bmp, gif, svg 또는 webp)이어야 합니다.

<a name="rule-in"></a>
#### in:_foo_,_bar_,...

The field under validation must be included in the given list of values. Since this rule often requires you to `implode` an array, the `Rule::in` method may be used to fluently construct the rule:

필드의 값이 주어진 목록에 포함돼 있어야 합니다. 이 규칙은 자주 배열을 `implode` 하는 것을 필요로 하며, 다음처럼 `Rule:in` 메소드를 사용하여 편리하게 생성할 수 있습니다.

    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'zones' => [
            'required',
            Rule::in(['first-zone', 'second-zone']),
        ],
    ]);

<a name="rule-in-array"></a>
#### in_array:_anotherfield_.*

The field under validation must exist in _anotherfield_'s values.

필드의 값이 주어진 다른 필드의 값안에 존재해야만 합니다.

<a name="rule-integer"></a>
#### integer

The field under validation must be an integer.

필드의 값이 정수여야 합니다.

> {note} This validation rule does not verify that the input is of the "integer" variable type, only that the input is a string or numeric value that contains an integer.

> {note} 이 유효성 검사 규칙은 입력값이 "정수" 변수 타입인지 검사하는 것이 아니라 정수를 포함하는 문자열 혹은 숫자인지 검사합니다.

<a name="rule-ip"></a>
#### ip

The field under validation must be an IP address.

필드의 값이 IP 주소여야 합니다.

#### ipv4

The field under validation must be an IPv4 address.

필드의 값이 IPv4 주소여야 합니다.

#### ipv6

The field under validation must be an IPv6 address.

필드의 값이 IPv6 주소여야 합니다.

<a name="rule-json"></a>
#### json

The field under validation must be a valid JSON string.

필드의 값이 유효한 JSON 문자열이어야 합니다.

<a name="rule-lt"></a>
#### lt:_field_

The field under validation must be less than the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

필드의 값이 주어진 다른 필드의 값보다 작아야 합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 계산됩니다.

<a name="rule-lte"></a>
#### lte:_field_

The field under validation must be less than or equal to the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

필드의 값이 주어진 다른 필드의 값보다 적거나 같아야 합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 계산됩니다.

<a name="rule-max"></a>
#### max:_value_

The field under validation must be less than or equal to a maximum _value_. Strings, numerics, arrays, and files are evaluated in the same fashion as the [`size`](#rule-size) rule.

필드의 값이 반드시 _value_ 보다 작거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 규칙에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-mimetypes"></a>
#### mimetypes:_text/plain_,...

The file under validation must match one of the given MIME types:

파일이 주어진 MIME 타입들 중 하나와 일치해야만 합니다.

    'video' => 'mimetypes:video/avi,video/mpeg,video/quicktime'

To determine the MIME type of the uploaded file, the file's contents will be read and the framework will attempt to guess the MIME type, which may be different from the client provided MIME type.

업로드 파일의 MIME 타입을 지정하고자 한다면, 프레임 워크는 파일의 내용을 읽어 들여 MIME 타입을 추측하게 되며 클라이언트가 제공하는 MIME 타입과 달라질 수 있습니다.

<a name="rule-mimes"></a>
#### mimes:_foo_,_bar_,...

The file under validation must have a MIME type corresponding to one of the listed extensions.

파일의 MIME 타입이 주어진 확장자 리스트 중에 하나와 일치해야 합니다.

#### Basic Usage Of MIME Rule
#### MIME 규칙의 기본 사용법

    'photo' => 'mimes:jpeg,bmp,png'

Even though you only need to specify the extensions, this rule actually validates against the MIME type of the file by reading the file's contents and guessing its MIME type.

여러분은 확장자를 지정하기만 하면 되지만, 이 경우 파일의 컨텐트를 읽고 MIME 타입을 추정함으로써 이 파일의 MIME의 유효성을 검사합니다.

A full listing of MIME types and their corresponding extensions may be found at the following location: [https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

MIME 타입과 그에 상응하는 확장의 전체 목록은 다음의 위치에서 확인하실 수 있습니다. [https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

<a name="rule-min"></a>
#### min:_value_

The field under validation must have a minimum _value_. Strings, numerics, arrays, and files are evaluated in the same fashion as the [`size`](#rule-size) rule.

필드의 값이 반드시 _value_ 보다 크거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 규칙에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-not-in"></a>
#### not_in:_foo_,_bar_,...

The field under validation must not be included in the given list of values. The `Rule::notIn` method may be used to fluently construct the rule:

필드의 값이 주어진 목록에 존재하지 않아야 합니다. `Rule::notIn` 메소드는 검사 규칙을 보다 유연하게 구성하는데 사용할 수 있습니다.

    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'toppings' => [
            'required',
            Rule::notIn(['sprinkles', 'cherries']),
        ],
    ]);

<a name="rule-not-regex"></a>
#### not_regex:_pattern_

The field under validation must not match the given regular expression.

필드의 값이 주어진 정규표현식과 매칭되지 않는 것을 확인합니다.

Internally, this rule uses the PHP `preg_match` function. The pattern specified should obey the same formatting required by `preg_match` and thus also include valid delimiters. For example: `'email' => 'not_regex:/^.+$/i'`.

이 규칙은 내부적으로 PHP의 `preg_match` 함수를 사용합니다. 지정된 패턴은 `preg_match` 에 필요한 것과 동일한 양식을 따라야하며 유효한 구분 기호를 포함해야합니다. 예를 들면 다음과 같습니다. `'email' => 'not_regex:/^.+@.+$/i'`.

**Note:** When using the `regex` / `not_regex` patterns, it may be necessary to specify rules in an array instead of using pipe delimiters, especially if the regular expression contains a pipe character.

**Note:** `regex` / `not_regex` 패턴을 사용할 때, 특히 정규표현식에 파이프 문자가 포함 된 경우, 파이프 구분자를 사용하는 대신에 배열에 규칙을 지정해야 할 수 있습니다.

<a name="rule-nullable"></a>
#### nullable

The field under validation may be `null`. This is particularly useful when validating primitive such as strings and integers that can contain `null` values.

필드의 값이 `null` 일 수 있습니다. 이것은 특히 `null` 값을 포함 할 수 있는 문자열 및 정수형과 같은 프리미티브 타입의 유효성을 검사 할 때 유용합니다.

<a name="rule-numeric"></a>
#### numeric

The field under validation must be numeric.

필드의 값이 숫자여야 합니다.

<a name="rule-password"></a>
#### password

The field under validation must match the authenticated user's password. You may specify an authentication guard using the rule's first parameter:

필드의 값이 인증 된 사용자의 비밀번호와 일치해야합니다. 규칙의 첫 번째 파라메터를 사용하여 인증 가드를 지정할 수 있습니다.

    'password' => 'password:api'

<a name="rule-present"></a>
#### present

The field under validation must be present in the input data but can be empty.

필드가 존재하고 있는지 확인하지만, 값이 비어있을 수 있습니다.

<a name="rule-regex"></a>
#### regex:_pattern_

The field under validation must match the given regular expression.

필드의 값이 주어진 정규식 표현과 일치해야 합니다.

Internally, this rule uses the PHP `preg_match` function. The pattern specified should obey the same formatting required by `preg_match` and thus also include valid delimiters. For example: `'email' => 'regex:/^.+@.+$/i'`.

이 규칙은 내부적으로 PHP의 `preg_match` 함수를 사용합니다. 지정된 패턴은 `preg_match` 에 필요한 것과 동일한 양식을 따라야하며 유효한 구분 기호를 포함해야합니다. 예를 들면 다음과 같습니다. `'email' => 'regex:/^.+@.+$/i'`.

**Note:** When using the `regex` / `not_regex` patterns, it may be necessary to specify rules in an array instead of using pipe delimiters, especially if the regular expression contains a pipe character.

**참고:** `regex` / `not_regex` 패턴을 사용할 때, 특히 정규 표현식에 파이프 문자열이 있다면, 파이프 구분자를 사용하는 대신 배열 형식을 사용하여 규칙을 지정할 필요가 있습니다.

<a name="rule-required"></a>
#### required

The field under validation must be present in the input data and not empty. A field is considered "empty" if one of the following conditions are true:

입력 값 중에 해당 필드가 존재해야 하며 비어 있어서는 안됩니다. 필드는 다음의 조건 중 하나를 충족하면 "빈(empty)" 것으로 간주됩니다.

- The value is `null`.
- The value is an empty string.
- The value is an empty array or empty `Countable` object.
- The value is an uploaded file with no path.

- 값이 `null`인 경우.
- 값이 비어있는 문자열인 경우.
- 값이 비어있는 배열이거나, 비어있는 `Countable` 객체인경우
- 값이 경로없이 업로드된 파일인 경우

<a name="rule-required-if"></a>
#### required_if:_anotherfield_,_value_,...

The field under validation must be present and not empty if the _anotherfield_ field is equal to any _value_.

만약 _anotherfield_ 의 값이 _value_ 중의 하나와 일치한다면, 해당 필드는 존재하고 비어있지 않아야 합니다.

If you would like to construct a more complex condition for the `required_if` rule, you may use the `Rule::requiredIf` method. This methods accepts a boolean or a Closure. When passed a Closure, the Closure should return `true` or `false` to indicate if the field under validation is required:

`required_if` 규칙에 대해 좀 더 복잡한 조건을 만들고 싶다면 `Rule::requiredIf` 메소드를 사용할 수 있습니다. 이 메소드는 boolean 또는 Closure를 허용합니다. Closure를 통과 할 때 클로저는 유효성 검사가 필요한 필드인지를 나타 내기 위해 true 또는 false를 반환해야합니다.

    use Illuminate\Validation\Rule;

    Validator::make($request->all(), [
        'role_id' => Rule::requiredIf($request->user()->is_admin),
    ]);

    Validator::make($request->all(), [
        'role_id' => Rule::requiredIf(function () use ($request) {
            return $request->user()->is_admin;
        }),
    ]);

<a name="rule-required-unless"></a>
#### required_unless:_anotherfield_,_value_,...

The field under validation must be present and not empty unless the _anotherfield_ field is equal to any _value_.

_anotherfield_ 가 어떤 _value_ 와도 값이 일치하지 않다면 해당 필드는 존재하고 비어있지 않아야 합니다.

<a name="rule-required-with"></a>
#### required_with:_foo_,_bar_,...

The field under validation must be present and not empty _only if_ any of the other specified fields are present.

지정된 다른 필드중 하나라도 존재한다면, 해당 필드가 반드시 존재하고 비어있지 않아야 합니다.

<a name="rule-required-with-all"></a>
#### required_with_all:_foo_,_bar_,...

The field under validation must be present and not empty _only if_ all of the other specified fields are present.

지정된 다른 필드가 모두 존재한다면, 해당 필드가 반드시 존재하고 비어있지 않아야 합니다.

<a name="rule-required-without"></a>
#### required_without:_foo_,_bar_,...

The field under validation must be present and not empty _only when_ any of the other specified fields are not present.

지정된 다른 필드중 하나라도 존재하지 않으면, 해당 필드가 반드시 존재하고 비어있지 않아야 합니다.

<a name="rule-required-without-all"></a>
#### required_without_all:_foo_,_bar_,...

The field under validation must be present and not empty _only when_ all of the other specified fields are not present.

지정된 다른 필드들이 모두 존재하지 않으면, 해당 필드가 존재하고 비어있지 않아야 합니다.

<a name="rule-same"></a>
#### same:_field_

The given _field_ must match the field under validation.

필드의 값이 주어진 _field_ 의 값과 일치해야 합니다.

<a name="rule-size"></a>
#### size:_value_

The field under validation must have a size matching the given _value_. For string data, _value_ corresponds to the number of characters. For numeric data, _value_ corresponds to a given integer value (the attribute must also have the `numeric` or `integer` rule). For an array, _size_ corresponds to the `count` of the array. For files, _size_ corresponds to the file size in kilobytes. Let's look at some examples:

필드의 값이 주어진 _value_ 와 일치하는 크기를 가져야 합니다. 문자열 데이터에서는 문자의 개수가 _value_ 와 일치해야 합니다. 숫자형식의 데이터에서는 주어진 정수값이 _value_ 와 일치해야 합니다(속성에는 `numeric` 또는 `integer` 규칙도 있어야 함). 배열에서는 배열의 `count` 와 일치해야 합니다. 파일에서는 킬로바이트 형식의 파일 사이즈가 _size_ 와 일치해야 합니다. 몇 가지 예를 살펴 보겠습니다.


    // Validate that a string is exactly 12 characters long...
    'title' => 'size:12';

    // Validate that a provided integer equals 10...
    'seats' => 'integer|size:10';

    // Validate that an array has exactly 5 elements...
    'tags' => 'array|size:5';

    // Validate that an uploaded file is exactly 512 kilobytes...
    'image' => 'file|size:512';

<a name="rule-starts-with"></a>
#### starts_with:_foo_,_bar_,...

The field under validation must start with one of the given values.

유효성 검사중인 필드는 주어진 값 중 하나로 시작해야합니다.

<a name="rule-string"></a>
#### string

The field under validation must be a string. If you would like to allow the field to also be `null`, you should assign the `nullable` rule to the field.

필드의 값이 반드시 문자열이어야 합니다. 필드가 `null` 인것을 허용하려면 규칙에 `nullable` 을 할당해야만 합니다.

<a name="rule-timezone"></a>
#### timezone

The field under validation must be a valid timezone identifier according to the `timezone_identifiers_list` PHP function.

필드의 값이 `timezone_identifiers_list` PHP 함수에서 인식 가능한 유효한 timezone 식별자여야 합니다.

<a name="rule-unique"></a>
#### unique:_table_,_column_,_except_,_idColumn_

The field under validation must not exist within the given database table.

필드의 값이 주어진 데이터베이스 테이블에서 고유한 값이어야 합니다. 만약 `column`이 지정돼 있지 않다면 필드의 이름이 사용됩니다.

**Specifying A Custom Table / Column Name:**
**특정 컬럼명 지정하기:**

Instead of specifying the table name directly, you may specify the Eloquent model which should be used to determine the table name:

테이블 이름을 직접 지정하는 대신 테이블 이름을 결정하는 데 사용해야하는 Eloquent 모델을 지정할 수 있습니다.

    'email' => 'unique:App\Models\User,email_address'

The `column` option may be used to specify the field's corresponding database column. If the `column` option is not specified, the field name will be used.

`column` 옵션은 필드의 해당 데이터베이스 컬럼을 지정하는 데 사용될 수 있습니다. `column` 옵션을 지정하지 않으면 필드 이름이 사용됩니다.

    'email' => 'unique:users,email_address'

**Custom Database Connection**
**특정 데이터베이스 커넥션**

Occasionally, you may need to set a custom connection for database queries made by the Validator. As seen above, setting `unique:users` as a validation rule will use the default database connection to query the database. To override this, specify the connection and the table name using "dot" syntax:

때때로, 여러분은 Validator에 의해서 생성되는 데이터베이스 쿼리에 사용자가 지정한 커넥션을 필요로 할지도 모릅니다. 위에서의 검증 규칙 `unique:users` 에서는 데이터베이스를 쿼리하기 위해 기본 데이터 베이스 커넥션이 사용됩니다. 이를 오버라이드 하려면 테이블 이름 후에 "." 표기법으로 커넥션을 지정하십시오.

    'email' => 'unique:connection.users,email_address'

**Forcing A Unique Rule To Ignore A Given ID:**
**주어진 ID에 대해서 유니크 규칙을 무시하도록 강제하기:**

Sometimes, you may wish to ignore a given ID during the unique check. For example, consider an "update profile" screen that includes the user's name, e-mail address, and location. You will probably want to verify that the e-mail address is unique. However, if the user only changes the name field and not the e-mail field, you do not want a validation error to be thrown because the user is already the owner of the e-mail address.

때때로 유니크 검사를 할 때 특정 ID를 무시하고자 할 수 있습니다. 예를 들어 사용자 이름, 이메일 주소 그리고 위치를 포함하는 "프로필 업데이트" 화면이 있습니다. 이메일 주소가 고유하다는 것을 확인하는 것이 좋습니다. 하지만 사용자가 이름 필드만 바꾸고 이메일 필드를 바꾸지 않는다면 사용자가 이미 이메일 주소의 주인이기 때문에 유효 검사 오류가 던져지지 않아야 합니다.

To instruct the validator to ignore the user's ID, we'll use the `Rule` class to fluently define the rule. In this example, we'll also specify the validation rules as an array instead of using the `|` character to delimit the rules:

사용자 ID를 무시하도록 지시하려면, 규칙을 유연하게 정의할 수 있는 `Rule` 클래스를 사용하면 됩니다. 다음 예제에서 규칙을 `|` 문자를 구분자로 사용하는 대신에 유효성 검사 규칙을 배열로 지정하고 있습니다.

    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'email' => [
            'required',
            Rule::unique('users')->ignore($user->id),
        ],
    ]);

> {note} You should never pass any user controlled request input into the `ignore` method. Instead, you should only pass a system generated unique ID such as an auto-incrementing ID or UUID from an Eloquent model instance. Otherwise, your application will be vulnerable to an SQL injection attack.

> {note} `ignore` 메소드에 유저가 컨트롤한 리퀘스트 입력값을 절대 전달해서는 안 됩니다. 대신, 엘로퀀트 모델 인스턴스의 자동 증가 ID나 UUID 등 시스템이 생성한 고유 ID를 전달하십시오. 그렇지 않으면 어플리케이션이 SQL 인젝션 공격에 취약하게 됩니다.

Instead of passing the model key's value to the `ignore` method, you may pass the entire model instance. Laravel will automatically extract the key from the model:

모델 키의 값을 `ignore` 메소드에 전달하는 대신 전체 모델 인스턴스를 전달할 수 있습니다. Laravel은 자동으로 모델에서 키를 추출 할 것입니다.

    Rule::unique('users')->ignore($user)

If your table uses a primary key column name other than `id`, you may specify the name of the column when calling the `ignore` method:

테이블이 `id`가 아닌 primary 키 컬럼 이름을 사용한다면, `ignore` 메소드를 호출할 때 컬럼의 이름을 지정하면 됩니다.

    Rule::unique('users')->ignore($user->id, 'user_id')

By default, the `unique` rule will check the uniqueness of the column matching the name of the attribute being validated. However, you may pass a different column name as the second argument to the `unique` method:

기본적으로 `unique`규칙은 유효성 검사중인 속성의 이름과 일치하는 열의 고유성을 검사합니다. 그러나 `unique` 메소드의 두 번째 인자에 다른 칼럼의 이름을 전달할 수 있습니다.

    Rule::unique('users', 'email_address')->ignore($user->id),

**Adding Additional Where Clauses:**
**추가적인 Where 구문 추가하기:**

You may also specify additional query constraints by customizing the query using the `where` method. For example, let's add a constraint that verifies the `account_id` is `1`:

`where` 메소드를 사용하여 쿼리를 커스터마이징하는 추가 제약을 지정할 수 있습니다. 예를 들어, `account_id` 이 `1` 인지 확인하는 제약 조건을 추가해 보겠습니다.

    'email' => Rule::unique('users')->where(function ($query) {
        return $query->where('account_id', 1);
    })

<a name="rule-url"></a>
#### url

The field under validation must be a valid URL.

필드는 반드시 유효한 URL이어야 합니다.

<a name="rule-uuid"></a>
#### uuid

The field under validation must be a valid RFC 4122 (version 1, 3, 4, or 5) universally unique identifier (UUID).

유효성 검사중인 필드는 유효한 RFC 4122 (버전 1, 3, 4 또는 5) 범용 고유 식별자 (UUID) 여야합니다.

<a name="conditionally-adding-rules"></a>
## Conditionally Adding Rules
## 조건부로 규칙 추가하기

#### Skipping Validation When Fields Have Certain Values
#### 필드에 특정 값이 있는 경우 유효성 검사 건너뛰기

You may occasionally wish to not validate a given field if another field has a given value. You may accomplish this using the `exclude_if` validation rule. In this example, the `appointment_date` and `doctor_name` fields will not be validated if the `has_appointment` field has a value of `false`:

때때로 다른 필드에 지정된 값이 있는 경우 주어진 필드의 유효성을 검사를 원하지 않는 경우가 있습니다. `exclude_if` 검증 규칙을 사용하여 이를 수행 할 수 있습니다. 이 예에서 `has_appointment` 필드의 값이 `false`이면 `appointment_date` 및 `doctor_name` 필드의 유효성은 검사되지 않습니다.

    $v = Validator::make($data, [
        'has_appointment' => 'required|bool',
        'appointment_date' => 'exclude_if:has_appointment,false|required|date',
        'doctor_name' => 'exclude_if:has_appointment,false|required|string',
    ]);

Alternatively, you may use the `exclude_unless` rule to not validate a given field unless another field has a given value:

또는 다른 필드에 지정된 값이 없으면 `exclude_unless` 규칙을 사용하여 지정된 필드의 유효성을 검사하지 않을 수 있습니다.

    $v = Validator::make($data, [
        'has_appointment' => 'required|bool',
        'appointment_date' => 'exclude_unless:has_appointment,true|required|date',
        'doctor_name' => 'exclude_unless:has_appointment,true|required|string',
    ]);
    
#### Validating When Present
#### 현재 값이 존재할때 유효성 검사하기

In some situations, you may wish to run validation checks against a field **only** if that field is present in the input array. To quickly accomplish this, add the `sometimes` rule to your rule list:

어떤 상황에서는 필드가 입력 배열에 **존재할 때에만** 그 필드의 유효성 검사를 실행하고 싶을수도 있습니다. 이를 위해서는 규칙의 목록에 `sometimes`를 추가하기만 하면 됩니다.

    $v = Validator::make($data, [
        'email' => 'sometimes|required|email',
    ]);

In the example above, the `email` field will only be validated if it is present in the `$data` array.

이 예제에서는 `$data` 배열에 `email` 필드가 존재할 경우에만 그 필드의 유효성 검사가 실행됩니다.

> {tip} If you are attempting to validate a field that should always be present but may be empty, check out [this note on optional fields](#a-note-on-optional-fields)

> {tip} 필드가 항상 존재하고 비어있지 않은지 확인하고자 한다면, [옵션 필드에 대한 주의사항](#a-note-on-optional-fields)부분을 참고하십시오.

#### Complex Conditional Validation
#### 복잡한 조건부 유효성 검사

Sometimes you may wish to add validation rules based on more complex conditional logic. For example, you may wish to require a given field only if another field has a greater value than 100. Or, you may need two fields to have a given value only when another field is present. Adding these validation rules doesn't have to be a pain. First, create a `Validator` instance with your _static rules_ that never change:

때때로 여러분은 한가지 이상의 복잡한 로직에 대해서 유효성 규칙을 추가하고자 할 수도 있습니다. 예를 들어, 어떤 다른 필드가 100 이상의 값을 가질때에만 주어진 필드가 반드시 존재하길 바랄 수도 있습니다. 또는 다른 필드가 존재할 때에만 두개의 필드가 주어진 값을 가질 필요가 있을수도 있습니다. 이러한 유효성 검사 규칙을 추가하는 것이 어렵지 않을 수 있습니다. 우선 _고정 규칙_ 을 변경할 필요 없이 그대로 사용하여 `Validator` 인스턴스를 생성합니다.

    $v = Validator::make($data, [
        'email' => 'required|email',
        'games' => 'required|numeric',
    ]);

Let's assume our web application is for game collectors. If a game collector registers with our application and they own more than 100 games, we want them to explain why they own so many games. For example, perhaps they run a game resale shop, or maybe they just enjoy collecting. To conditionally add this requirement, we can use the `sometimes` method on the `Validator` instance.

여러분의 웹 애플리케이션이 게임 수집가들을 위한 사이트라고 가정해보겠습니다. 만약 100개 이상의 게임을 소유하고 있는 게임 수집가가 우리 사이트에 가입을 한다면, 우리는 그들이 왜 그렇게 많은 게임을 소유하고 있는지 설명을 듣고 싶을수 있습니다. 예를 들어, 아마 그들이 중고게임 판매점을 운영하거나, 단순히 수집을 취미로 할 수도 있습니다. 이런 요구사항을 조건부로 추가하기 위하여 `Validator` 인스턴스의 `sometimes` 메소드를 사용할 수 있습니다.

    $v->sometimes('reason', 'required|max:500', function ($input) {
        return $input->games >= 100;
    });

The first argument passed to the `sometimes` method is the name of the field we are conditionally validating. The second argument is the rules we want to add. If the `Closure` passed as the third argument returns `true`, the rules will be added. This method makes it a breeze to build complex conditional validations. You may even add conditional validations for several fields at once:

`sometimes` 메소드에 전달되는 첫번째 인자는 필드의 이름입니다. 두번째 인자는 추가하려는 규칙입니다. 만약 세번째 파라미터로 전달된 `Closure`가 `true`를 리턴한다면 그 규칙은 유효성 검사에 추가될 것입니다. 이 메소드는 복잡한 조건부 유효성 검사의 구성을 손쉽게 만들어 주며, 한번에 여러개의 필드에 대한 조건부 유효성 검사를 추가할 수도 있습니다.

    $v->sometimes(['reason', 'cost'], 'required', function ($input) {
        return $input->games >= 100;
    });

> {tip} The `$input` parameter passed to your `Closure` will be an instance of `Illuminate\Support\Fluent` and may be used to access your input and files.

> {tip} `Closure`로 전달된 `$input` 파라미터는 `Illuminate\Support\Fluent`의 인스턴스입니다. 그리고 입력된 데이터와 파일에 접근하기 위해 이 오브젝트가 사용할 수 있습니다.

<a name="validating-arrays"></a>
## Validating Arrays
## 배열값 유효성 검사

Validating array based form input fields doesn't have to be a pain. You may use "dot notation" to validate attributes within an array. For example, if the incoming HTTP request contains a `photos[profile]` field, you may validate it like so:

form 입력필드의 배열을 유효성 검사하는 것을 어렵게 할 필요 없습니다. "점 표기법"을 사용하여 배열안에 있는 유효성 속성을 지정할 수 있습니다. 예를 들어 HTTP request가 가지고 있는 `photos[profile]` 필드에 대해서 다음과 같이 검사할 수 있습니다.

    $validator = Validator::make($request->all(), [
        'photos.profile' => 'required|image',
    ]);

You may also validate each element of an array. For example, to validate that each e-mail in a given array input field is unique, you may do the following:

또한 배열의 각각의 요소들을 검사할 수 있습니다. 예들 들어 주어진 배열 입력값이 각각의 유니크한 이메일인지 확인하려면 다음과 같이 하면 됩니다.

    $validator = Validator::make($request->all(), [
        'person.*.email' => 'email|unique:users',
        'person.*.first_name' => 'required_with:person.*.last_name',
    ]);

Likewise, you may use the `*` character when specifying your validation messages in your language files, making it a breeze to use a single validation message for array based fields:

마찬가지로, 여러분은 배열을 기반으로한 단일 유효성 검사 메세지 를 사용하는데 `*` 문자를 여러분의 언어파일에 들어 있는 유효성 검사 메세지를 지정할 때 사용할 수 있습니다.

    'custom' => [
        'person.*.email' => [
            'unique' => 'Each person must have a unique e-mail address',
        ]
    ],

<a name="custom-validation-rules"></a>
## Custom Validation Rules
## 사용자 정의 유효성 검사 규칙

<a name="using-rule-objects"></a>
### Using Rule Objects
### Rule 객체 사용하기

Laravel provides a variety of helpful validation rules; however, you may wish to specify some of your own. One method of registering custom validation rules is using rule objects. To generate a new rule object, you may use the `make:rule` Artisan command. Let's use this command to generate a rule that verifies a string is uppercase. Laravel will place the new rule in the `app/Rules` directory:

라라벨은 다양하고 유용한 유효성 검사 규칙을 제공합니다; 하지만, 여러분은 여러분만의 유효성 검사 규칙을 정의하길 바랄수도 있습니다. 커스텀 유효성 검사 규칙을 등록하는 방법중 하나는 rule 객체를 사용하는 방법입니다. 새로운 rule 객체를 생성하기 위해서 `make:rule` 아티즌 명령어를 실행할 수 있습니다. 문자열이 대문자로 구성되었는지 확인하는 rule을 생성하기 위해서 명령어를 사용해보겠습니다. 라라벨은 `app/Rules` 디렉토리에 새로운 rule 객체를 생성합니다.

    php artisan make:rule Uppercase

Once the rule has been created, we are ready to define its behavior. A rule object contains two methods: `passes` and `message`. The `passes` method receives the attribute value and name, and should return `true` or `false` depending on whether the attribute value is valid or not. The `message` method should return the validation error message that should be used when validation fails:

rule 객체가 생성되고나면, 유효성 검사가 동작하는 방식을 정해야 합니다. rule 객체는 두개의 메소드 : `passes` 와 `message` 를 가지고 있습니다. `passes` 메소드는 속성 값과 이름을 전달받아, 속성 값이 유효한지 아닌지에 따라, `true` 또는 `false` 를 반환해야 합니다. `message` 메소드는 유효성 검사가 실패했을 때 사용하는 에러 메세지를 반환해야 합니다.

    <?php

    namespace App\Rules;

    use Illuminate\Contracts\Validation\Rule;

    class Uppercase implements Rule
    {
        /**
         * Determine if the validation rule passes.
         *
         * @param  string  $attribute
         * @param  mixed  $value
         * @return bool
         */
        public function passes($attribute, $value)
        {
            return strtoupper($value) === $value;
        }

        /**
         * Get the validation error message.
         *
         * @return string
         */
        public function message()
        {
            return 'The :attribute must be uppercase.';
        }
    }

You may call the `trans` helper from your `message` method if you would like to return an error message from your translation files:

여러분이 언어 파일로부터 변환된 에러 메세지를 전달해주고자 한다면, `messgae` 메소드 안에서 `trans` 헬퍼 함수를 호출할 수 있습니다.

    /**
     * Get the validation error message.
     *
     * @return string
     */
    public function message()
    {
        return trans('validation.uppercase');
    }

Once the rule has been defined, you may attach it to a validator by passing an instance of the rule object with your other validation rules:

rule 을 정의하고 나면, 다른 유효성 검사 rule 객체들과 함께, rule 객체의 인스턴스를 전달하여, validator 에 추가할 수 있습니다.

    use App\Rules\Uppercase;

    $request->validate([
        'name' => ['required', 'string', new Uppercase],
    ]);

<a name="using-closures"></a>
### Using Closures
### 클로저 사용하기

If you only need the functionality of a custom rule once throughout your application, you may use a Closure instead of a rule object. The Closure receives the attribute's name, the attribute's value, and a `$fail` callback that should be called if validation fails:

만약 애플리케이션을 통틀어 사용자 정의 규칙의 기능을 한번만 필요로 하는 경우, rule 객체 대신 클로저를 사용할 수 있습니다. 클로저는 속성의 이름, 속성의 값, 그리고 만약 유효성 검사가 실패할 시 호출될 `$fail` 콜백을 받습니다.


    $validator = Validator::make($request->all(), [
        'title' => [
            'required',
            'max:255',
            function ($attribute, $value, $fail) {
                if ($value === 'foo') {
                    $fail($attribute.' is invalid.');
                }
            },
        ],
    ]);

<a name="using-extensions"></a>
### Using Extensions
### 확장기능 사용하기

Another method of registering custom validation rules is using the `extend` method on the `Validator` [facade](/docs/{{version}}/facades). Let's use this method within a [service provider](/docs/{{version}}/providers) to register a custom validation rule:

또다른 사용자 정의 유효성 검사 규칙을 등록하는 방법중 하나는 `Validator` [파사드](/docs/{{version}}/facades)에 `extend` 메소드를 사용하는 것입니다. 이 메소드를 [서비스 프로바이더](/docs/{{version}}/providers) 내에서 사용하여 사용자 정의 유효성 검사 규칙을 등록합니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;
    use Illuminate\Support\Facades\Validator;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Validator::extend('foo', function ($attribute, $value, $parameters, $validator) {
                return $value == 'foo';
            });
        }
    }

The custom validator Closure receives four arguments: the name of the `$attribute` being validated, the `$value` of the attribute, an array of `$parameters` passed to the rule, and the `Validator` instance.

커스텀 유효성 검사 클로저는 4개의 인자를 받습니다. 유효성 검사를 할 필드(`$attribute`)의 이름, 필드의 값(`$value`), 그리고 규칙에 전달될 파라미터들(`$parameters`)의 배열, 그리고 `Validator` 인스턴스 입니다.

You may also pass a class and method to the `extend` method instead of a Closure:

또한 클로저 대신 클래스명과 메소드명을 `extend` 메소드로 전달할 수도 있습니다.

    Validator::extend('foo', 'FooValidator@validate');

#### Defining The Error Message
#### 에러 메세지 정의하기

You will also need to define an error message for your custom rule. You can do so either using an inline custom message array or by adding an entry in the validation language file. This message should be placed in the first level of the array, not within the `custom` array, which is only for attribute-specific error messages:

사용자 정의(커스텀) 규칙을 위한 에러 메세지 또한 정의해야 합니다. 인라인 사용자 정의(커스텀) 메세지 배열을 사용하거나 유효 검사 언어 파일에 엔트리를 추가하면 됩니다. 이 메세지는 `custom` 배열 안이 아닌 배열의 첫번째 레벨에 놓여야 합니다. `custom` 배열은 특정 속성에 따른 에러 메세지를 담당합니다.

    "foo" => "Your input was invalid!",

    "accepted" => "The :attribute must be accepted.",

    // The rest of the validation error messages...

When creating a custom validation rule, you may sometimes need to define custom placeholder replacements for error messages. You may do so by creating a custom Validator as described above then making a call to the `replacer` method on the `Validator` facade. You may do this within the `boot` method of a [service provider](/docs/{{version}}/providers):

커스텀 유효 검사 규칙을 생성할 때 종종 에러 메세지를 위한 커스텀 플레이스홀더 대체제를 정의해야 할 수도 있습니다. 이전의 설명에 따라 커스텀 Validator를 생성하고 `Validator` 파사드에 `replacer` 메소드를 호출하십시오. 이는 [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드 안에서 할 수 있습니다.

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Validator::extend(...);

        Validator::replacer('foo', function ($message, $attribute, $rule, $parameters) {
            return str_replace(...);
        });
    }

<a name="implicit-extensions"></a>
### Implicit Extensions
### 묵시적 확장

By default, when an attribute being validated is not present or contains an empty string, normal validation rules, including custom extensions, are not run. For example, the [`unique`](#rule-unique) rule will not be run against an empty string:

기본적으로 유효성 검사를 받는 속성이 존재하지 않거나 빈 문자열을 가지고 있다면, 사용자 정의(커스텀) 확장을 포함한 정상적인 유효성 검사 규칙은 실행되지 않을 것입니다. 예를 들어 빈 문자열에는 [`unique`](#rule-unique) 규칙이 실행되지 않을 것입니다.

    $rules = ['name' => 'unique:users,name'];

    $input = ['name' => ''];

    Validator::make($input, $rules)->passes(); // true

For a rule to run even when an attribute is empty, the rule must imply that the attribute is required. To create such an "implicit" extension, use the `Validator::extendImplicit()` method:

속성이 비었을 때도 규칙이 실행되기 위해서는 규칙에 속성이 필요하다는 것이 내포되어 있어야 합니다. 이런 "묵시적"인 확장을 만드려면 `Validator::extendImplicit()` 메소드를 사용하세요:

    Validator::extendImplicit('foo', function ($attribute, $value, $parameters, $validator) {
        return $value == 'foo';
    });

> {note} An "implicit" extension only _implies_ that the attribute is required. Whether it actually invalidates a missing or empty attribute is up to you.

> {note} "묵시적" 확장은 단지 속성이 필요하다는 것을 _암시(내포)_합니다. 없거나 빈 속성의 유효성을 실제로 부정하는지는 여러분이 결정합니다.

#### Implicit Rule Objects
#### 묵시적 규칙 객체

If you would like a rule object to run when an attribute is empty, you should implement the `Illuminate\Contracts\Validation\ImplicitRule` interface. This interface serves as a "marker interface" for the validator; therefore, it does not contain any methods you need to implement.

속성이 비어있을 때 규칙 객체를 실행하려면 `Illuminate\Contracts\Validation\ImplicitRule` 인터페이스를 구현해야 합니다. 이 인터페이스는 유효성 검사기의 "marker interface" 역할을 합니다. 따라서 구현해야 하는 메소드가 포함되어 있지 않습니다.
