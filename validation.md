# Validation-유효성 검사

- [시작하기](#introduction)
- [빠르게 유효성 검사 살펴보기](#validation-quickstart)
    - [라우트 정의하기](#quick-defining-the-routes)
    - [컨트롤러 생성하기](#quick-creating-the-controller)
    - [유효성 검사 로직 작성하기](#quick-writing-the-validation-logic)
    - [유효성 검사 에러 표시하기](#quick-displaying-the-validation-errors)
    - [옵션 필드에 대한 주의사항](#a-note-on-optional-fields)
- [Form Request 유효성 검사](#form-request-validation)
    - [Form Requests 생성하기](#creating-form-requests)
    - [Form Requests 사용자 승인](#authorizing-form-requests)
    - [에러 메세지 사용자 정의하기](#customizing-the-error-messages)
- [Validators 수동으로 생성하기](#manually-creating-validators)
    - [자동으로 리다이렉트하기](#automatic-redirection)
    - [이름이 지정된 Error Bags](#named-error-bags)
    - [유효성 검사 이후에 후킹하기](#after-validation-hook)
- [에러 메세지 작업하기](#working-with-error-messages)
    - [사용자 정의 에러 메세지](#custom-error-messages)
- [사용가능한 유효성 검사 규칙](#available-validation-rules)
- [조건부로 규칙 추가하기](#conditionally-adding-rules)
- [배열값 유효성 검사](#validating-arrays)
- [사용자 정의 유효성 검사 규칙](#custom-validation-rules)
    - [Rule 객체 사용하기](#using-rule-objects)
    - [클로저 사용하기](#using-closures)
    - [확장기능 사용하기](#using-extensions)

<a name="introduction"></a>
## 시작하기

라라벨은 애플리케이션에 유입되는 데이터의 유효성을 검사하기 위한 다양한 방법을 제공합니다. 기본적으로, 라라벨의 베이스 콘트롤러 클래스는 다양하고 강력한 유효성 검사 규칙을 적용하여 유입되는 HTTP 요청의 유효성 검사를 위한 편리한 메소드를 제공하는 ValidatesRequests 트레이트-trait을 사용하고 있습니다.

<a name="validation-quickstart"></a>
## 빠르게 유효성 검사 살펴보기

라라벨의 강력한 유효성 검사 기능에 대해 알아보기 위해서, form을 유효성 검사한 뒤 사용자에게 에러 메세지를 보여주는 예제를 살펴보도록 하겠습니다.

<a name="quick-defining-the-routes"></a>
### 라우트 정의하기

우선 다음의 라우트들이 `routes/web.php` 파일에 정의되어 있다고 가정해 보겠습니다:

    Route::get('post/create', 'PostController@create');

    Route::post('post', 'PostController@store');

당연히, `GET` 라우트는 사용자가 새로운 블로그 포스트를 생성하기 위한 form을 나타낼 것이고, `POST` 라우트는 데이터베이스에 새로운 블로그 포스트를 저장할 것입니다.

<a name="quick-creating-the-controller"></a>
### 컨트롤러 생성하기

다음으로, 이 라우트들을 다루는 간단한 컨트롤러를 살펴보겠습니다. 지금은 `store` 메소드를 비어있는 채로 둘 것입니다:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

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
### 유효성 검사 로직 작성하기

이제 새로운 블로그 포스트에 대해 유효성을 검사하는 로직을 `store` 메소드에 채워넣을 준비가 되었습니다. 이를 위해서 `Illuminate\Http\Request` 객체에 제공되는 `validate` 메소드를 사용할 것입니다. 유효성 검사 룰들을 통과하게되면 코드는 계속해서 정상적으로 실행됩니다. 하지만 유효성 검사를 통과하지 못할 경우, 예외-exception가 던져지고 적절한 오류 응답이 사용자에게 자동으로 보내질 것입니다. 전통적인 HTTP 요청의 경우, 리다이렉트 응답이 생성될 것이며 AJAX 요청에는 JSON 응답이 보내질 것입니다.

`validate` 메소드에 대해 더 잘 이해하기 위해, 다시 `store` 메소드로 돌아가 보겠습니다:

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

보시다시피, 원하는 유효성 검사 룰을 메소드에 전달하기만 하면 됩니다. 다시말해, 유효성 검사가 실패하면, 적절한 응답이 자동으로 생성됩니다. 유효성 검사를 통과하면 컨트롤러가 계속해서 정상적으로 로직을 수행합니다.

#### 첫번째 유효성 검사가 실패하면 중지하기

경우에 따라서 첫번째 유효성 감사가 실패하면 속성값에 대한 검사를 중단하기를 원할수도 있습니다. 이러한 경우 `bail` 규칙을 지정하면 됩니다:

    $request->validate([
        'title' => 'bail|required|unique:posts|max:255',
        'body' => 'required',
    ]);

이 예제에서는, `unique` 규칙으로 지정된 `title` 속성의 유효성 검사가 실패하면 `max` 규칙은 확인하지 않습니다. 유효성 검사 규칙은 선언된 순서대로 검사될 것입니다.

#### 중첩된 속성에 대한 유의사항

HTTP 요청이 "중첩된" 파라미터를 가지고 있다면 ".(점)" 문법을 사용하여 유효성 확인 규칙을 지정할 수 있습니다:

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'author.name' => 'required',
        'author.description' => 'required',
    ]);

<a name="quick-displaying-the-validation-errors"></a>
### 유효성 검사 에러 표시하기

그럼 입력되는 요청-request의 파라미터들이 유효성 검사를 통과하지 못하는 경우에는 어떻게 될까요? 앞서 언급한대로, 라라벨은 사용자를 자동으로 이전의 위치로 리다이렉트합니다. 또한 모든 유효성 확인 에러는 자동으로 [세션에 임시 저장](/docs/{{version}}/session#flash-data)될 것입니다.

다시 말하지만, `GET` 라우트에서 에러 메세지를 뷰와 명시적으로 연결하지 않아도 됩니다. 왜냐하면, 라라벨이 세션 데이터에서 에러가 있는지 확인하고, 에러가 있다면 뷰에 자동으로 연결해 주기 때문입니다. `$errors` 변수는 `Illuminate\Support\MessageBag`의 인스턴스일 것입니다. 이 객체를 다루는 법에 대해 더 알아보고 싶다면 [문서을 확인해보시기 바랍니다](#working-with-error-messages).

> {tip} `$errors` 변수는 `web` 미들웨어 그룹에 의해서 제공되는 `Illuminate\View\Middleware\ShareErrorsFromSession` 미들웨어에 의해서 뷰와 연결됩니다. **이 미들웨어가 지정되었을 때 `$errors` 변수는 뷰 안에서 항상 사용가능 할 것이므로**, 편하게 생각하면 `$errors` 변수는 항상 선언되어 있다고 생각하고 안정하게 사용할 수 있습니다.

따라서, 이 예제에서, 유효성 검사를 통과하지 못할 경우 사용자는 컨트롤러의 `create` 메소드로 리다이렉트 될것이고, 뷰에서는 에러 메세지가 표시됩니다:

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

<a name="a-note-on-optional-fields"></a>
### 옵션 필드에 대한 주의사항

기본적으로 라라벨은 애플리케이션의 글로벌 미들웨어 스택에 `TrimStrings` 그리고 `ConvertEmptyStringsToNull` 미들웨어를 포함하고 있습니다. 이 미들웨어는 `App\Http\Kernel` 클래스의 미들웨어 스택에 나열되어 있습니다. 이때문에, 유효성 검사에서 `null`이 유효하지 않은것으로 간주하지 않으려면 "선택적-optional" request-요청 필드를 `nullable`로 표시할 필요도 있습니다. 예를들면:

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

이 예제에서는 `publish_at` 필드가 `null`이거나 유효한 날짜 형식이라고 지정했습니다. 만약 `nullable` 규칙이 추가되지 않은 경우 `null`값은 유효하지 않다고 결정됩니다.

<a name="quick-ajax-requests-and-validation"></a>
#### AJAX 요청과 유효성 검사

이 예제에서는, 애플리케이션에 전통적인 form을 이용하여 데이터를 보냈습니다. 하지만 많은 애플리케이션이 AJAX 요청을 사용합니다. AJAX reqeust 중에서 `validate` 메소드를 사용한다면 라라벨은 리다이렉트 응답을 생성하지 않을 것입니다. 대신 라라벨은 유효성 검사의 모든 실패 에러들을 포함하는 JSON 응답을 생성할 것입니다. 이 JSON 응답은 422 HTTP 상태 코드와 함께 보내질 것입니다.

<a name="form-request-validation"></a>
## Form Request 유효성 검사

<a name="creating-form-requests"></a>
### Form Requests 생성하기

보다 복잡한 유효성 검사 시나리오의 경우, "form request"를 작성하는 것이 좋습니다. Form request는 request 클래스의 사용자 정의(커스텀) 클래스로, 유효성 검사 로직을 가지고 있습니다. form request 클래스를 생성하기 위해서는 `make:request` 아티즌 CLI 명령어를 사용하십시오:

    php artisan make:request StoreBlogPost

생성된 클래스는 `app/Http/Requests` 디렉토리에 저장됩니다. 이 디렉토리가 존재하지 않는다면 `make:request` 명령어가 실행될 때 생성됩니다. `rules` 메소드에 몇가지 유효성 검사 규칙을 추가해 보겠습니다:


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

> {tip} `rules` 메소드에 필요한 모든 의존성의 타입힌트를 지정할 수 있습니다. 이 의존성은 Laravel [서비스 컨테이너](/docs/{{version}}/container)를 통해 자동으로 처리됩니다.

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

유효성 검사가 실패하면 리다이렉션 응답-response이 생성되어 사용자를 이전 위치로 되돌려 보냅니다. 오류를 세션에 임시저장하여 화면에 표시 할 수도 있습니다. AJAX 요청인 경우 유효성 검사 오류가 JSON 형식으로 재구성되어 422 상태 코드가있는 HTTP 응답이 사용자에게 반환됩니다.

#### Form Request 에 After 후킹 추가하기

form request 에 "after" 후킹을 추가하려면, `withValidator` 메소드를 사용하면 됩니다. 이 메소드는 생성된 완전한 validator를 전달 받는데, 유효성 검사 규칙이 실제로 수행되기 전에 메소드 중 하나를 호출할 수 있도록 해줍니다:

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
### Form Requests 사용자 승인

form request 클래스는 또한 `authorize` 메소드를 가지고 있습니다. 이 메소드 안에서 여러분은 인증된 사용자가 주어진 리소스에 대해서 수정할 수 있는 권한이 있는지 확인할 수 있습니다. 예를 들어, 사용자가 블로그 포스트의 댓글을 수정하려고 시도할 때, 그 본인의 코멘트인지 확인할 수 있습니다:

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

모든 form request는 라라벨의 베이스 request 클래스를 확장하고 있기 때문에, 현재 인증된 사용자에 엑세스 하기 위해서 `user` 메소드를 사용할 수 있습니다. 다음 예제에서 `route` 메소드를 호출하는것에 주목하십시오. 예제에서 이 메소드는 `{comment}` 파라미터와 같이 파라미터가 정의된 라우트의 URI 에 여러분이 엑세스 접근할 수 있는지 권한을 확인합니다:

    Route::post('comment/{comment}');

만약 `authorize` 메소드가 `false`를 리턴하면, 403 HTTP 응답이 자동적으로 반환되고 컨트롤러 메소드는 실행되지 않을 것입니다.

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

> {tip} `run` 메소드에 필요한 모든 의존성의 타입힌트를 지정할 수 있습니다. 이 의존성은 Laravel [서비스 컨테이너](/docs/{{version}}/container)를 통해 자동으로 처리됩니다.

<a name="customizing-the-error-messages"></a>
### 에러 메세지를 사용자 정의하기(커스터마이징하기)

`messages` 메소드를 대체하면 form request-요청이 사용하는 에러 메세지를 커스터마이즈할 수 있습니다. 이 메소드는 속성 / 규칙의 쌍으로된 배열과 그에 상응하는 오류 메세지를 반환합니다:

    /**
     * Get the error messages for the defined validation rules.
     *
     * @return array
     */
    public function messages()
    {
        return [
            'title.required' => 'A title is required',
            'body.required'  => 'A message is required',
        ];
    }

<a name="manually-creating-validators"></a>
## Validator 수동으로 생성하기

request 의 `validate` 메소드를 사용하고 싶지 않다면, `Validator` [파사드](/docs/{{version}}/facades)를 사용하여 validator 인스턴스를 수동으로 생성할 수 있습니다. 파사드에 `make` 메소드를 사용하면 새로운 validator 인스턴스가 생성됩니다:

    <?php

    namespace App\Http\Controllers;

    use Validator;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

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

`make` 메소드로 전달되는 첫번째 인자는 유효성 검사를 받을 데이터입니다. 두번째 인자는 데이터에 적용되어야 하는 유효성 검사 규칙들입니다.

request-요청이 유효성 검사에 실패하였는지 확인한 후에 `withErrors` 메소드로 세션에 에러 메세지를 임시저장-flash 할 수 있습니다. 이 메소드를 사용하면 리다이렉트 후에 `$errors` 변수가 자동으로 뷰에서 공유되어 손쉽게 사용자에게 보여질 수 있습니다. `withErrors` 메소드는 validator, `MessageBag`, 혹은 PHP `array`를 전달 받습니다.

<a name="automatic-redirection"></a>
### 자동으로 리다이렉트하기

수동으로 validator 인스턴스를 생성하더라도, request의 `validate` 메소드에 의해서 자동으로 리다이렉트 되는 이점을 유지하고 싶다면, validator 인스턴스에 `validate` 메소드를 호출하면 됩니다. 유효성 검사가 실패하는 경우, 사용자는 자동으로 리다이렉트 되거나, 또는 AJAX 요청인 경우, JSON이 반환됩니다:

    Validator::make($request->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ])->validate();

<a name="named-error-bags"></a>
### 이름이 지정된 Error Bags

한 페이지 안에서 여러개의 form을 가지고 있다면 에러들의 `MessageBag`에 이름을 붙여 지정한 form에 맞는 에러 메세지를 조회할 수 있도록 할 수 있습니다. `withErrors`에 이름을 두번째 인자로 전달하면 됩니다:

    return redirect('register')
                ->withErrors($validator, 'login');

그러면 `$errors` 변수에서 지정된 `MessageBag` 인스턴스에 접근할 수 있습니다:

    {{ $errors->login->first('email') }}

<a name="after-validation-hook"></a>
### 유효성 검사 이후에 후킹하기

유효성 검사가 완료된 후에 실행하고자 하는 콜백함수를 validator에 추가할 수 있습니다. 이를 통하면, 손쉽게 더 많은 유효성 검사를 실행할 수 있도록 하고, 에러 메시지 컬렉션에 에러 메시지를 더 추가할 수도 있습니다. 다음처럼 validator 인스턴스의 `after` 메소드를 사용하면 됩니다:

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
## 에러 메시지 작업하기

`Validator` 인스턴스의 `errors` 메소드를 호출하면, 에러 메시지를 편하게 사용할 수 있는 다양한 메소드를 가진 `MessageBag` 인스턴스를 받을 수 있습니다, 이것은 오류메세지를 처리하기 위한 다양하고 편리한 메소드들을 가집니다. `$errors` 변수는 자동으로 모든 뷰에서 `MessageBag` 클래스 인스턴스로써 사용가능합니다.

#### 하나의 필드에 대한 첫번째 에러 메시지 조회하기

특정 필드에 대한 첫번째 에러 메세지를 조회하려면 `first` 메소드를 사용하면 됩니다:

    $errors = $validator->errors();

    echo $errors->first('email');

#### 하나의 필드에 대한 모든 에러 메세지 조회하기

특정 필드에 대한 모든 메세지에 대한 배열을 조회하고자 한다면, `get` 메소드를 사용하면 됩니다:

    foreach ($errors->get('email') as $message) {
        //
    }

배열형태의 form 필드에 대해서 유효성 검사를 하려면, 배열의 각각의 요소는 `*` 문자열을 사용하여 모든 메세지를 조회할 수 있습니다:

    foreach ($errors->get('attachments.*') as $message) {
        //
    }

#### 모든 필드에 대한 모든 에러 메세지 조회하기

모든 필드에 대한 모든 에러 메세지를 조회하기 위해서는 `all` 메소들 사용하면 됩니다:

    foreach ($errors->all() as $message) {
        //
    }

#### 하나의 필드에 대하여 에러 메시지가 존재하는지 검사하기

`has` 메소드는 특정 필드에 대한 에러 메세지가 존재하는지 확인하는데 사용합니다:

    if ($errors->has('email')) {
        //
    }

<a name="custom-error-messages"></a>
### 사용자 지정(커스텀) 에러 메세지

필요하다면 기본적인 에러 메세지 대신에 커스텀 에러 메세지를 유효성 검사에 사용할 수 있습니다. 커스텀 메세지를 지정하는 데에는 여러가지 방법이 있습니다. 먼저 `Validator::make` 메소드에 커스텀 메세지를 세번째 인자로 전달할 수 있습니다:

    $messages = [
        'required' => 'The :attribute field is required.',
    ];

    $validator = Validator::make($input, $rules, $messages);

다음의 예에서 `:attribute` 플레이스 홀더는 유효성 검사를 받는 필드의 실제 이름으로 대체됩니다. 유효성 검사 메세지에서 다른 플레이스 홀더들 또한 활용할 수 있습니다. 예를 들어:

    $messages = [
        'same'    => 'The :attribute and :other must match.',
        'size'    => 'The :attribute must be exactly :size.',
        'between' => 'The :attribute value :input is not between :min - :max.',
        'in'      => 'The :attribute must be one of the following types: :values',
    ];

#### 주어진 속성에 대해 커스텀 메세지 지정하기

종종 하나의 특정 필드에 대해서만 커스텀 오류 메세지를 지정해야 하는 경우가 있습니다. 이것은 ".(점)" 표기법을 통해서 할 수 있습니다. 속성의 이름을 먼저 지정하고, 규칙을 명시하면됩니다:

    $messages = [
        'email.required' => 'We need to know your e-mail address!',
    ];

<a name="localization"></a>
#### 언어 파일에 커스텀 메세지 지정하기

대부분의 경우에서, `Validator`에 직접 메세지를 전달하는 대신, 언어 파일의 커스텀 메세지를 지정하기 원할 수 있습니다. 이렇게 하기 위해서는 `resources/lang/xx/validation.php` 언어 파일의 `custom` 배열에 메세지를 추가하면 됩니다.

    'custom' => [
        'email' => [
            'required' => 'We need to know your e-mail address!',
        ],
    ],

#### 언어파일에 커스텀 속성 지정하기

유효성 검사 메세지의 `:attribute` 부분을 사용자 정의 속성 이름으로 교체하려면 `resources/lang/xx/validation.php`언어 파일의 `attributes` 배열에 사용자 정의 이름을 지정하면 됩니다:

    'attributes' => [
        'email' => 'email address',
    ],

<a name="available-validation-rules"></a>
## 사용가능한 유효성 검사 규칙

다음은 사용 가능한 모든 유효성 검사 규칙과 그 기능의 목록입니다:

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
- [E-Mail](#rule-email)
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
- [String](#rule-string)
- [Timezone](#rule-timezone)
- [Unique (Database)](#rule-unique)
- [URL](#rule-url)

<a name="rule-accepted"></a>
#### accepted

필드의 값이 _yes_, _on_, _1_, 또는 _true_이어야 합니다. 이 것은 "이용약관" 동의와 같은 필드의 검사에 유용합니다.

<a name="rule-active-url"></a>
#### active_url

필드의 값이 PHP 함수 `dns_get_record`에서 확인 가능한 올바른 A 또는 AAAA 레코드여야 합니다.

<a name="rule-after"></a>
#### after:_date_

필드의 값이 주어진 날짜 이후여야 합니다. 이때 날짜는 `strtotime` PHP 함수를 통해 생성된 값입니다.

    'start_date' => 'required|date|after:tomorrow'

`strtotime`에 의해 계산될 날짜 문자열을 전달하는 대신 날짜와 비교할 다른 필드를 명시할 수 있습니다:

    'finish_date' => 'required|date|after:start_date'

<a name="rule-after-or-equal"></a>
#### after\_or\_equal:_date_

필드의 값이 주어진 날짜와 동일하거나, 이후여야 합니다. 보다 자세한 사항은 [after](#rule-after) 규칙을 확인하십시오.

<a name="rule-alpha"></a>
#### alpha

필드의 값이 완벽하게 (숫자나 기호가 아닌) 알파벳[자음과 모음] 문자로 이루어져야 합니다.

(역자주: 영문 알파벳만을 의미하지 않고, 숫자나 기호가 아닌경우에 해당하여, 한글도 허용합니다.)

<a name="rule-alpha-dash"></a>
#### alpha_dash

필드의 값이 (숫자나 기호가 아닌) 알파벳[자음과 모음] 문자 및 숫자와 dash(-), underscore(_)로 이루어져야 합니다.

<a name="rule-alpha-num"></a>
#### alpha_num

필드의 값이 완벽하게 (숫자나 기호가 아닌) 알파벳[자음과 모음] 문자 및 숫자로 이루어져야 합니다.

<a name="rule-array"></a>
#### array

필드의 값이 반드시 PHP 배열 형태이어야 합니다.

<a name="rule-bail"></a>
#### bail

첫번째 유효성 검사 룰이 실패하면, 유효성 검사를 중단합니다.

<a name="rule-before"></a>
#### before:_date_

필드의 값이 반드시 주어진 날짜보다 앞서야 합니다. 날짜는 `strtotime` PHP 함수를 통해 비교됩니다.

<a name="rule-before-or-equal"></a>
#### before\_or\_equal:_date_

필드의 값이 주어진 날짜보다 앞서거나, 같아야 합니다. 날짜는 `strtotime` PHP 함수를 통해서 비교됩니다.

<a name="rule-between"></a>
#### between:_min_,_max_

필드의 값이, 주어진 _min_ 과 _max_의 사이의 값이어야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 룰에 의해 같은 방식으로 계산될 수 있습니다.

<a name="rule-boolean"></a>
#### boolean

필드의 값이 반드시 boolean으로 캐스팅될 수 있어야 합니다. 허용되는 값은 `true`, `false`, `1`, `0`, `"1"`, `"0"` 입니다.

<a name="rule-confirmed"></a>
#### confirmed

필드의 값이 `foo_confirmation`의 매칭되는 필드를 가져야 합니다. 예를 들어 만약 필드가 `password`라면, `password_confirmation`라는 필드가 입력값 중에 있어야 합니다.

<a name="rule-date"></a>
#### date

필드의 값이 `strtotime` PHP 함수에서 인식할 수 있는 올바른 날짜여야 합니다.

<a name="rule-date-equals"></a>
#### date_equals:_date_

필드의 값이 주어진 날짜와 일치해야 합니다. 날짜값은 PHP의 `strtotime` 함수로 전달됩니다.

<a name="rule-date-format"></a>
#### date_format:_format_

필드의 값이 반드시 주어진 _format_과 일지해야 합니다. 필드의 유효성을 검사할 때에는 `date`와 `date_format` 중 **하나만** 사용해야 합니다

<a name="rule-different"></a>
#### different:_field_

필드의 값이 주어진 _field_의 값과 달라야 합니다.

<a name="rule-digits"></a>
#### digits:_value_

필드의 값이 반드시 _숫자_여야 하고, 길이가 _value_이어야 합니다.

<a name="rule-digits-between"></a>
#### digits_between:_min_,_max_

필드의 값이 주어진 _min_과 _max_ 사이의 길이를 가져야 합니다.

<a name="rule-dimensions"></a>
#### dimensions

필드의 값이 룰에 지정된 파라미터들을 만족하는 이미지이어야 합니다.

    'avatar' => 'dimensions:min_width=100,min_height=200'

사용가능한 제약은:  _min\_width_, _max\_width_, _min\_height_, _max\_height_, _width_, _height_, _ratio_ 입니다.

_ratio_ 제약은 가로를 세로로 나눈 비율을 표현해야합니다. 이는 `3/2` 또는 소수점 `1.5` 처럼 지정될 수 있습니다:

    'avatar' => 'dimensions:ratio=3/2'

이 룰은 여러개의 인자를 필요로 하는데, 다음처럼 `Rule:dimensions` 메소드를 사용하여 유연하게 룰을 생성할 수 있습니다:

    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'avatar' => [
            'required',
            Rule::dimensions()->maxWidth(1000)->maxHeight(500)->ratio(3 / 2),
        ],
    ]);

<a name="rule-distinct"></a>
#### distinct

배열에서 동작하며, 필드의 값이 배열안의 다른 값과 중복되지 않아야 합니다.

    'foo.*.id' => 'distinct'

<a name="rule-email"></a>
#### email

필드의 값이 이메일 주소 형식이어야 합니다.

<a name="rule-exists"></a>
#### exists:_table_,_column_

필드의 값이 주어진 데이터베이스 테이블에 존재하는 값이어야 합니다.

#### exists 룰의 기본 사용법

    'state' => 'exists:states'

`column` 옵션을 지정하지 않으면 필드 이름이 사용됩니다.

#### 특정 컬럼명 지정하기

    'state' => 'exists:states,abbreviation'

때때로, `exists` 쿼리에서 사용할 데이터베이스 커넥션을 지정하고자 할 수도 있습니다. 이경우 커넥션의 이름을 테이블 이름 앞에 "점" 문법을 사용하여 표기할 수도 있습니다.

    'email' => 'exists:connection.staff,email'

유효성 검사 규칙에 의해서 실행되는 쿼리를 커스터마이징 하고자 한다면, 규칙에 `Rule` 클래스를 정의해서 사용할 수 있습니다. 다음 예제에서 `|` 문자를 구분자로 사용하는 대신에 유효성 검사 규칙을 배열로 지정하고 있습니다:

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

필드의 값이 완전히 업로드된 파일이어야 합니다.

<a name="rule-filled"></a>
#### filled

필드가 존재하는 경우 값이 비어있으면 안됩니다.

<a name="rule-gt"></a>
#### gt:_field_

필드의 값이 주어진 다른 필드의 값보다 커야합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 `size` 룰에 따라서 계산됩니다.

<a name="rule-gte"></a>
#### gte:_field_

필드의 값이 주어진 다른 필드의 값보다 크거나 같아야합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 `size` 룰에 따라서 계산됩니다.

<a name="rule-image"></a>
#### image

이미지 파일(jpeg, png, bmp, gif, svg)이어야 합니다.

<a name="rule-in"></a>
#### in:_foo_,_bar_,...

필드의 값이 주어진 목록에 포함돼 있어야 합니다. 이 룰은 자주 배열을 `implode` 하는 것을 필요로 하며, 다음처럼 `Rule:in` 메소드를 사용하여 편리하게 생성할 수 있습니다:

    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'zones' => [
            'required',
            Rule::in(['first-zone', 'second-zone']),
        ],
    ]);

<a name="rule-in-array"></a>
#### in_array:_anotherfield_

필드의 값이 주어진 다른 필드의 값안에 존재해야만 합니다.

<a name="rule-integer"></a>
#### integer

필드의 값이 정수여야 합니다.

<a name="rule-ip"></a>
#### ip

필드의 값이 IP 주소여야 합니다.

#### ipv4

필드의 값이 IPv4 주소여야 합니다.

#### ipv6

필드의 값이 IPv6 주소여야 합니다.

<a name="rule-json"></a>
#### json

필드의 값이 유효한 JSON 문자열이어야 합니다.

<a name="rule-lt"></a>
#### lt:_field_

The field under validation must be less than the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the `size` rule.

필드의 값이 주어진 다른 필드의 값보다 작아야 합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 `size` 룰에 따라서 계산됩니다.

<a name="rule-lte"></a>
#### lte:_field_

The field under validation must be less than or equal to the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the `size` rule.

필드의 값이 주어진 다른 필드의 값보다 적거나 같아야 합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 `size` 룰에 따라서 계산됩니다.

<a name="rule-max"></a>
#### max:_value_

필드의 값이 반드시 _value_보다 작거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 룰에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-mimetypes"></a>
#### mimetypes:_text/plain_,...

파일이 주어진 MIME 타입중 하나와 일치해야 합니다:

    'video' => 'mimetypes:video/avi,video/mpeg,video/quicktime'

업로드 파일의 MIME 타입을 지정하고자 한다면, 프레임 워크는 파일의 내용을 읽어 들여 MIME 타입을 추측하게 되며 클라이언트가 제공하는 MIME 타입과 달라질 수 있습니다.

<a name="rule-mimes"></a>
#### mimes:_foo_,_bar_,...

파일의 MIME 타입이 주어진 확장자 리스트 중에 하나와 일치해야 합니다.

#### MIME 룰의 기본 사용법

    'photo' => 'mimes:jpeg,bmp,png'

여러분은 확장자를 지정하기만 하면 되지만, 이 경우 파일의 컨텐트를 읽고 MIME 타입을 추정함으로써 이 파일의 MIME의 유효성을 검사합니다.

MIME 타입과 그에 상응하는 확장의 전체 목록은 다음의 위치에서 확인하실 수 있습니다: [https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

<a name="rule-min"></a>
#### min:_value_

필드의 값이 반드시 _value_ 보다 크거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 룰에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-nullable"></a>
#### nullable

필드의 값은 `null` 일 수 있습니다. 이는 특히 `null` 을 포함한 문자열이나 정수형과 같은 기본 타입에 대해서 유효성검사를 할 때 유용합니다.

<a name="rule-not-in"></a>
#### not_in:_foo_,_bar_,...

필드의 값이 주어진 목록에 존재하지 않아야 합니다. `Rule::notIn` 메소드는 검사룰을 보다 유연하게 구성하는데 사용할 수 있습니다:

    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'toppings' => [
            'required',
            Rule::notIn(['sprinkles', 'cherries']),
        ],
    ]);

<a name="rule-not-regex"></a>
#### not_regex:_pattern_

필드의 값이 주어진 정규표현식과 매칭되지 않는 것을 확인합니다.

**Note:** `regex` / `not_regex` 패턴을 사용할 때, 특히 정규표현식에 파이프 문자가 포함 된 경우, 파이프 구분자를 사용하는 대신에 배열에 룰을 지정해야 할 수 있습니다.

<a name="rule-nullable"></a>
#### nullable

필드의 값이 `null` 일 수 있습니다. 이것은 특히 `null` 값을 포함 할 수 있는 문자열 및 정수형과 같은 프리미티브 타입의 유효성을 검사 할 때 유용합니다.

<a name="rule-numeric"></a>
#### numeric

필드의 값이 숫자여야 합니다.

<a name="rule-present"></a>
#### present

필드가 존재하고 있는지 확인하지만, 값이 비어있을 수 있습니다.

<a name="rule-regex"></a>
#### regex:_pattern_

필드의 값이 주어진 정규식 표현과 일치해야 합니다.

**참고:** `regex` / `not_regex` 패턴을 사용할 때, 특히 정규 표현식에 파이프 문자열이 있다면, 파이프 구분자를 사용하는 대신 배열 형식을 사용하여 룰을 지정할 필요가 있습니다.

<a name="rule-required"></a>
#### required

입력 값 중에 해당 필드가 존재해야 하며 비어 있어서는 안됩니다. 필드는 다음의 조건 중 하나를 충족하면 "빈(empty)" 것으로 간주됩니다:

- 값이 `null`인 경우.
- 값이 비어있는 문자열인 경우.
- 값이 비어있는 배열이거나, 비어있는 `Countable` 객체인경우
- 값이 경로없이 업로드된 파일인 경우

<a name="rule-required-if"></a>
#### required_if:_anotherfield_,_value_,...

만약 _anotherfield_의 값이 _value_중의 하나와 일치한다면, 해당 필드는 존재하고 비어있지 않아야 합니다.

<a name="rule-required-unless"></a>
#### required_unless:_anotherfield_,_value_,...

_anotherfield_가 어떤 _value_와도 값이 일치하지 않다면 해당 필드는 존재하고 비어있지 않아야 합니다.

<a name="rule-required-with"></a>
#### required_with:_foo_,_bar_,...

지정된 다른 필드중 하나라도 존재한다면, 해당 필드가 반드시 존재하고 비어있지 않아야 합니다.

<a name="rule-required-with-all"></a>
#### required_with_all:_foo_,_bar_,...

지정된 다른 필드가 모두 존재한다면, 해당 필드가 반드시 존재하고 비어있지 않아야 합니다.

<a name="rule-required-without"></a>
#### required_without:_foo_,_bar_,...

지정된 다른 필드중 하나라도 존재하지 않으면, 해당 필드가 반드시 존재하고 비어있지 않아야 합니다.

<a name="rule-required-without-all"></a>
#### required_without_all:_foo_,_bar_,...

지정된 다른 필드들이 모두 존재하지 않으면, 해당 필드가 존재하고 비어있지 않아야 합니다.

<a name="rule-same"></a>
#### same:_field_

필드의 값이 주어진 _field_의 값과 일치해야 합니다.

<a name="rule-size"></a>
#### size:_value_

필드의 값이 주어진 _value_와 일치하는 크기를 가져야 합니다. 문자열 데이터에서는 문자의 개수가 _value_와 일치해야 합니다. 숫자형식의 데이터에서는 주어진 정수값이 _value_와 일치해야 합니다. 배열에서는 배열의 `count` 와 일치해야 합니다. 파일에서는 킬로바이트 형식의 파일 사이즈가 _size_와 일치해야 합니다.

<a name="rule-string"></a>
#### string

필드의 값이 반드시 문자열이어야 합니다. 필드가 `null` 인것을 허용하려면 규칙에 `nullable` 을 할당해야만 합니다.

<a name="rule-timezone"></a>
#### timezone

필드의 값이 `timezone_identifiers_list` PHP 함수에서 인식 가능한 유효한 timezone 식별자여야 합니다.

<a name="rule-unique"></a>
#### unique:_table_,_column_,_except_,_idColumn_

필드의 값이 주어진 데이터베이스 테이블에서 고유한 값이어야 합니다. 만약 `column`이 지정돼 있지 않다면 필드의 이름이 사용됩니다.

**특정 컬럼명 지정하기:**

    'email' => 'unique:users,email_address'

**특정 데이터베이스 커넥션**

때때로, 여러분은 Validator에 의해서 생성되는 데이터베이스 쿼리에 사용자가 지정한 커넥션을 필요로 할지도 모릅니다. 위에서의 검증 규칙 `unique:users` 에서는 데이터베이스를 쿼리하기 위해 기본 데이터 베이스 커넥션이 사용됩니다. 이를 오버라이드 하려면 테이블 이름 후에 "." 표기법으로 커넥션을 지정하십시오:

    'email' => 'unique:connection.users,email_address'

**주어진 ID에 대해서 유니크 규칙을 무시하도록 강제하기:**

때때로 유니크 검사를 할 때 특정 ID를 무시하고자 할 수 있습니다. 예를 들어 사용자 이름, 이메일 주소 그리고 위치를 포함하는 "프로필 업데이트" 화면이 있습니다. 물론 이메일 주소가 고유하다는 것을 확인하고 싶을 것입니다. 하지만 사용자가 이름 필드만 바꾸고 이메일 필드를 바꾸지 않는다면 사용자가 이미 이메일 주소의 주인이기 때문에 유효 검사 오류가 던져지지 않아야 합니다.

사용자 ID를 무시하도록 지시하려면, 규칙을 유연하게 정의할 수 있는 `Rule` 클래스를 사용하면 됩니다. 다음 예제에서 규칙을 `|` 문자를 구분자로 사용하는 대신에 유효성 검사 규칙을 배열로 지정하고 있습니다:

    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'email' => [
            'required',
            Rule::unique('users')->ignore($user->id),
        ],
    ]);

또한 `unique` 메소드의 두번째 파라미터를 사용하여 유효성 검사를 진행할 컬럼의 이름을 지정할 수 있습니다. 그렇지 않다면, 유효성 검사 룰의 속성 이름이 컬럼의 이름으로 사용됩니다:

    'email' => Rule::unique('users', 'email_address')

테이블이 `id`가 아닌 primary 키 컬럼 이름을 사용한다면, `ignore` 메소드를 호출할 때 컬럼의 이름을 지정하면 됩니다:

    'email' => Rule::unique('users')->ignore($user->id, 'user_id')

**추가적인 Where 구문 추가하기:**

`where` 메소드를 사용하여 쿼리를 커스터마이징하는 추가 제약을 지정할 수 있습니다. 예를 들어, `account_id`이 `1`인지 확인하는 제약 조건을 추가해 보겠습니다:

    'email' => Rule::unique('users')->where(function ($query) {
        return $query->where('account_id', 1);
    })

<a name="rule-url"></a>
#### url

필드는 반드시 유효한 URL이어야 합니다.

<a name="conditionally-adding-rules"></a>
## 조건부로 규칙 추가하기

#### 현재 값이 존재할때 유효성 검사하기

어떤 상황에서는 필드가 입력 배열에 **존재할 때에만** 그 필드의 유효성 검사를 실행하고 싶을수도 있습니다. 이를 위해서는 룰의 목록에 `sometimes`를 추가하기만 하면 됩니다:

    $v = Validator::make($data, [
        'email' => 'sometimes|required|email',
    ]);

이 예제에서는 `$data` 배열에 `email` 필드가 존재할 경우에만 그 필드의 유효성 검사가 실행됩니다.

> {tip} 필드가 항상 존재하고 비어있지 않은지 확인하고자 한다면, [옵션 필드에 대한 주의사항](#a-note-on-optional-fields)부분을 참고하십시오.

#### 복잡한 조건부 유효성 검사

때때로 여러분은 한가지 이상의 복잡한 로직에 대해서 유효성 규칙을 추가하고자 할 수도 있습니다. 예를 들어, 어떤 다른 필드가 100 이상의 값을 가질때에만 주어진 필드가 반드시 존재하길 바랄 수도 있습니다. 또는 다른 필드가 존재할 때에만 두개의 필드가 주어진 값을 가질 필요가 있을수도 있습니다. 이러한 유효성 검사 룰을 추가하는 것이 어렵지 않을 수 있습니다. 우선 _고정 룰_ 을 변경할 필요 없이 그대로 사용하여 `Validator` 인스턴스를 생성합니다:

    $v = Validator::make($data, [
        'email' => 'required|email',
        'games' => 'required|numeric',
    ]);

여러분의 웹 애플리케이션이 게임 수집가들을 위한 사이트라고 가정해보겠습니다. 만약 100개 이상의 게임을 소유하고 있는 게임 수집가가 우리 사이트에 가입을 한다면, 우리는 그들이 왜 그렇게 많은 게임을 소유하고 있는지 설명을 듣고 싶을수 있습니다. 예를 들어, 아마 그들이 중고게임 판매점을 운영하거나, 단순히 수집을 취미로 할 수도 있습니다. 이런 요구사항을 조건부로 추가하기 위하여 `Validator` 인스턴스의 `sometimes` 메소드를 사용할 수 있습니다.

    $v->sometimes('reason', 'required|max:500', function ($input) {
        return $input->games >= 100;
    });

`sometimes` 메소드에 전달되는 첫번째 인자는 필드의 이름입니다. 두번째 인자는 추가하려는 룰입니다. 만약 세번째 파라메터로 전달된 `Closure`가 `true`를 리턴한다면 그 룰은 유효성 검사에 추가될 것입니다. 이 메소드는 복잡한 조건부 유효성 검사의 구성을 손쉽게 만들어 주며, 한번에 여러개의 필드에 대한 조건부 유효성 검사를 추가할 수도 있습니다.

    $v->sometimes(['reason', 'cost'], 'required', function ($input) {
        return $input->games >= 100;
    });

> {tip} `Closure`로 전달된 `$input` 파라메터는 `Illuminate\Support\Fluent`의 인스턴스입니다. 그리고 입력된 데이터와 파일에 접근하기 위해 이 오브젝트가 사용할 수 있습니다.

<a name="validating-arrays"></a>
## 배열값 유효성 검사

form 입력필드의 배열을 유효성 검사하는 것을 어렵게 할 필요 없습니다. "점 표기법"을 사용하여 배열안에 있는 유효성 속성을 지정할 수 있습니다. 예를 들어 HTTP request가 가지고 있는 `photos[profile]` 필드에 대해서 다음과 같이 검사할 수 있습니다:

    $validator = Validator::make($request->all(), [
        'photos.profile' => 'required|image',
    ]);

또한 배열의 각각의 요소들을 검사할 수 있습니다. 예들 들어 주어진 배열 입력값이 각각의 유니크한 이메일인지 확인하려면 다음과 같이 하면 됩니다:

    $validator = Validator::make($request->all(), [
        'person.*.email' => 'email|unique:users',
        'person.*.first_name' => 'required_with:person.*.last_name',
    ]);

마찬가지로, 여러분은 배열을 기반으로한 단일 유효성 검사 메세지 를 사용하는데 `*` 문자를 여러분의 언어파일에 들어 있는 유효성 검사 메세지를 지정할 때 사용할 수 있습니다:

    'custom' => [
        'person.*.email' => [
            'unique' => 'Each person must have a unique e-mail address',
        ]
    ],

<a name="custom-validation-rules"></a>
## 사용자 정의 유효성 검사 규칙

<a name="using-rule-objects"></a>
### Rule 객체 사용하기

라라벨은 다양하고 유용한 유효성 검사 룰을 제공합니다; 하지만, 여러분은 여러분만의 유효성 검사 룰을 정의하길 바랄수도 있습니다. 커스텀 유효성 검사 룰을 등록하는 방법중 하나는 rule 객체를 사용하는 방법입니다. 새로운 rule 객체를 생성하기 위해서 `make:rule` 아티즌 명령어를 실행할 수 있습니다. 문자열이 대문자로 구성되었는지 확인하는 rule을 생성하기 위해서 명령어를 사용해보겠습니다. 라라벨은 `app/Rules` 디렉토리에 새로운 rule 객체를 생성합니다:

    php artisan make:rule Uppercase

rule 객체가 생성되고나면, 유효성 검사가 동작하는 방식을 정해야 합니다. rule 객체는 두개의 메소드 : `passes` 와 `message` 를 가지고 있습니다. `passes` 메소드는 속성 값과 이름을 전달받아, 속성 값이 유효한지 아닌지에 따라, `true` 또는 `false` 를 반환해야 합니다. `message` 메소드는 유효성 검사가 실패했을 때 사용하는 에러 메세지를 반환해야 합니다:

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

당연하게도, 여러분이 언어 파일로부터 변환된 에러 메세지를 전달해주고자 한다면, `messgae` 메소드 안에서 `trans` 헬퍼 함수를 호출할 수 있습니다:

    /**
     * Get the validation error message.
     *
     * @return string
     */
    public function message()
    {
        return trans('validation.uppercase');
    }

rule 을 정의하고 나면, 다른 유효성 검사 rule 객체들과 함께, rule 객체의 인스턴스를 전달하여, validator 에 추가할 수 있습니다:

    use App\Rules\Uppercase;

    $request->validate([
        'name' => ['required', 'string', new Uppercase],
    ]);

<a name="using-closures"></a>
### 클로저 사용하기

만약 애플리케이션을 통틀어 사용자 정의 규칙의 기능을 한번만 필요로 하는 경우, rule 객체 대신 클로저를 사용할 수 있습니다. 클로저는 속성의 이름, 속성의 값, 그리고 만약 유효성 검사가 실패할 시 호출될 `$fail` 콜백을 받습니다.


    $validator = Validator::make($request->all(), [
        'title' => [
            'required',
            'max:255',
            function($attribute, $value, $fail) {
                if ($value === 'foo') {
                    return $fail($attribute.' is invalid.');
                }
            },
        ],
    ]);

<a name="using-extensions"></a>
### 확장기능 사용하기

또다른 사용자 정의 유효성 검사 규칙을 등록하는 방법중 하나는 `Validator` [파사드](/docs/{{version}}/facades)에 `extend` 메소드를 사용하는 것입니다. 이 메소드를 [서비스 프로바이더](/docs/{{version}}/providers) 내에서 사용하여 사용자 정의 유효성 검사 규칙을 등록합니다:

    <?php

    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;
    use Illuminate\Support\Facades\Validator;

    class AppServiceProvider extends ServiceProvider
    {
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

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

커스텀 유효성 검사 클로저는 4개의 인자를 받습니다: 유효성 검사를 할 필드(`$attribute`)의 이름, 필드의 값(`$value`), 그리고 룰에 전달될 파라미터들(`$parameters`)의 배열, 그리고 `Validator` 인스턴스 입니다.

또한 클로저 대신 클래스명과 메소드명을 `extend` 메소드로 전달할 수도 있습니다:

    Validator::extend('foo', 'FooValidator@validate');

#### 에러 메세지 정의하기

사용자 정의(커스텀) 규칙을 위한 에러 메세지 또한 정의해야 합니다. 인라인 사용자 정의(커스텀) 메세지 배열을 사용하거나 유효 검사 언어 파일에 엔트리를 추가하면 됩니다. 이 메세지는 `custom` 배열 안이 아닌 배열의 첫번째 레벨에 놓여야 합니다. `custom` 배열은 특정 속성에 따른 에러 메세지를 담당합니다:

    "foo" => "Your input was invalid!",

    "accepted" => "The :attribute must be accepted.",

    // The rest of the validation error messages...

커스텀 유효 검사 룰을 생성할 때 종종 에러 메세지를 위한 커스텀 플레이스 홀더 대체제를 정의해야 할 수도 있습니다. 이전의 설명에 따라 커스텀 Validator를 생성하고 `Validator` 파사드에 `replacer` 메소드를 호출하십시오. 이는 [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드 안에서 할 수 있습니다:

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

#### 묵시적 확장

기본적으로 유효성 검사를 받는 속성이 존재하지 않거나 [`required`](#rule-required) 규칙의 정의에 따라 빈 값을 가지고 있다면, 사용자 정의(커스텀) 확장을 포함한 정상적인 유효성 검사 규칙은 실행되지 않을 것입니다. 예를 들어 `null` 값에는 [`unique`](#rule-unique) 룰이 실행되지 않을 것입니다:

    $rules = ['name' => 'unique'];

    $input = ['name' => null];

    Validator::make($input, $rules)->passes(); // true

속성이 비었을 때도 규칙이 실행되기 위해서는 규칙에 속성이 필요하다는 것이 내포되어 있어야 합니다. 이런 "묵시적"인 확장을 만드려면 `Validator::extendImplicit()` 메소드를 사용하세요:

    Validator::extendImplicit('foo', function ($attribute, $value, $parameters, $validator) {
        return $value == 'foo';
    });

> {note} "묵시적" 확장은 단지 속성이 필요하다는 것을 _암시(내포)_합니다. 없거나 빈 속성의 유효성을 실제로 부정하는지는 여러분이 결정합니다.
