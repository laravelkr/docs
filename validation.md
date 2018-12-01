# Validation-유효성검사

- [시작하기](#introduction)
- [Validation 퀵스타트](#validation-quickstart)
    - [라우트 정의하기](#quick-defining-the-routes)
    - [컨트롤러 생성하기](#quick-creating-the-controller)
    - [Validation 로직 작성하기](#quick-writing-the-validation-logic)
    - [Validation 에러 표시하기](#quick-displaying-the-validation-errors)
    - [AJAX Requests & Validation](#quick-ajax-requests-and-validation)
- [다른 Validation 방법](#other-validation-approaches)
    - [수동으로 Validator 생성하기](#manually-creating-validators)
    - [Form Request Validation](#form-request-validation)
- [에러 메시지 사용하기](#working-with-error-messages)
    - [사용자 지정(커스텀) 에러 메세지](#custom-error-messages)
- [사용가능한 유효성 검사 규칙들](#available-validation-rules)
- [조건부 룰 추가하기](#conditionally-adding-rules)
- [사용자 정의 유효성 검사 룰](#custom-validation-rules)

<a name="introduction"></a>
## 시작하기

라라벨은 애플리케이션에 유입되는 데이터의 유효성을 검사하기 위한 다양한 방법을 제공합니다. 기본적으로, 라라벨의 베이스 컨트롤러 클래스는 다양하고 강력한 유효성 검사 규칙을 적용하여 편리하게 HTTP 요청을 승인하는 메소드를 제공하는 `ValidatesRequests` 트레이트-trait을 사용하고 있습니다.

<a name="validation-quickstart"></a>
## Validation 퀵스타트

라라벨의 강력한 유효성 검사 기능에 대해 알아보기 위해서, form을 확인한 뒤 사용자에게 에러 메세지를 보여주는 예제를 살펴보도록 하겠습니다. 

<a name="quick-defining-the-routes"></a>
### 라우트 정의하기

우선 다음의 라우트들이 `app/Http/routes.php` 파일에 정의되어 있다고 가정해 보겠습니다:

    // Display a form to create a blog post...
    Route::get('post/create', 'PostController@create');

    // Store a new blog post...
    Route::post('post', 'PostController@store');

`GET` 라우트는 사용자가 새로운 블로그 포스트를 생성하기 위한 form을 나타낼 것이고, `POST` 라우트는 데이터베이스에 새로운 블로그 포스트를 저장할 것입니다. 

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
         * Show the form the create a new blog post.
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
### Validation-유효성검사 로직 작성하기

이제 새로운 블로그 포스트에 대해 유효성을 검사하는 로직을 `store` 메소드에 채워넣을 준비가 되었습니다. 애플리케이션의 베이스 컨트롤러(`App\Http\Controllers\Controller`) 클래스를 살펴보면 클래스가 `ValidatesRequests` 트레이트-trait을 사용한다는 것을 알 수 있습니다. 이 트레이트-trait은 모든 컨트롤러에 편리하게 사용할 수 있는 `validate` 메소드를 제공합니다. 

`validate` 메소드는 HTTP 요청의 유입과 유효성 검사 룰의 집합을 전달 받습니다. 유효성 검사 룰들을 통과하게되면 코드는 계속해서 정상적으로 실행될 것입니다. 하지만 유효성 검사를 통과하지 못할 경우, 예외-exception가 던져지고 적절한 오류 응답이 사용자에게 자동으로 보내질 것입니다. 전통적인 HTTP 요청의 경우, 리다이렉트 응답이 생성될 것이며 AJAX 요청에는 JSON 응답이 보내질 것입니다.  

`validate` 메소드에 대해 더 잘 이해하기 위해, 다시 `store` 메소드로 돌아가 보겠습니다: 

    /**
     * Store a new blog post.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $this->validate($request, [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ]);

        // The blog post is valid, store in database...
    }

위에서 볼 수 있듯이, 간단하게 유입되는 HTTP 요청과 유효성 검사 룰들을 `validate` 메소드로 전달하면 됩니다. 이 때에도 유효 확인이 실패하면 적절한 응답이 생성될 것입니다. 유효성 검사를 통과하면 컨트롤러는 계속해서 정상적으로 수행합니다.

#### 중첩된 속성에 대한 유의사항

HTTP 요청이 "중첩된" 파라미터를 가지고 있다면 ".(점)" 문법을 사용하여 유효성 확인 규칙을 지정할 수 있습니다:

    $this->validate($request, [
        'title' => 'required|unique:posts|max:255',
        'author.name' => 'required',
        'author.description' => 'required',
    ]);

<a name="quick-displaying-the-validation-errors"></a>
### Validation-유효성 검사 에러 표시하기

그럼 입력되는 요청-request의 파라미터들이 유효성 검사를 통과하지 못하는 경우에는 어떻게 될까요? 이 경우 라라벨은 앞서 언급한대로, 사용자를 자동으로 이전의 위치로  리다이렉트합니다. 또한 모든 유효성 확인 에러는 자동으로 [세션에 임시 저장](/docs/{{version}}/session#flash-data)될 것입니다. 

이번에도 `GET` 라우트에서 에러 메세지를 뷰와 명시적으로 연결하지 않아도 됩니다. 왜냐하면, 라라벨이 항상 세션 데이터에서 에러가 있는지 확인하고, 에러가 있다면 뷰에 자동으로 연결해 주기 때문입니다. **그렇기 때문에 모든 요청에 대해서 뷰에서는 항상 `$errors` 변수가 존재한다는 것을 아는 것은 중요합니다.** 따라서 여러분은 `$errors` 변수가 항상 정의되어 있으며 사용 가능하다고 마음 편하게 가정할 수 있습니다. `$errors` 변수는 `Illuminate\Support\MessageBag`의 인스턴스일 것입니다. 이 객체를 다루는 법에 대해 더 알아보고 싶다면 [문서을 확인해보시기 바랍니다](#working-with-error-messages).

따라서, 이 예제에서, 유효성 검사를 통과하지 못할 경우 사용자는 컨트롤러의 `create` 메소드로 리다이렉트 될것이고, 뷰에서는 에러 메세지가 표시됩니다: 

    <!-- /resources/views/post/create.blade.php -->

    <h1>Create Post</h1>

    @if (count($errors) > 0)
        <div class="alert alert-danger">
            <ul>
                @foreach ($errors->all() as $error)
                    <li>{{ $error }}</li>
                @endforeach
            </ul>
        </div>
    @endif

    <!-- Create Post Form -->

<a name="quick-customizing-the-flashed-error-format"></a>
#### 임시저장된 에러의 포맷을 임의로 지정하기

만약 유효성 검사가 실패했을 때 세션에 저장되는 에러의 형식을 커스터마이징하고 싶다면, 베이스 컨트롤러 클래스의 `formatErrors` 메소드를 오버라이딩하면 됩니다. 이때, 파일 상단에서 `Illuminate\Validation\Validator`를 임포트하는 것을 잊지 마십시오:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Foundation\Bus\DispatchesJobs;
    use Illuminate\Contracts\Validation\Validator;
    use Illuminate\Routing\Controller as BaseController;
    use Illuminate\Foundation\Validation\ValidatesRequests;

    abstract class Controller extends BaseController
    {
        use DispatchesJobs, ValidatesRequests;

        /**
         * {@inheritdoc}
         */
        protected function formatValidationErrors(Validator $validator)
        {
            return $validator->errors()->all();
        }
    }

<a name="quick-ajax-requests-and-validation"></a>
### AJAX 요청과 유효성 검사

이 예제에서는, 애플리케이션에 전통적인 form을 이용하여 데이터를 보냈습니다. 하지만 많은 애플리케이션이 AJAX 요청을 사용합니다. AJAX reqeust 중에서 `validate` 메소드를 사용한다면 라라벨은 리다이렉트 응답을 생성하지 않을 것입니다. 대신 라라벨은 유효성 검사의 모든 실패 에러들을 포함하는 JSON 응답을 생성할 것입니다. 이 JSON 응답은 422 HTTP 상태 코드와 함께 보내질 것입니다.

<a name="other-validation-approaches"></a>
## 다른 Validation-유효성 검사 방법

<a name="manually-creating-validators"></a>
### 수동으로 Validator 생성하기

`ValidatesRequests` 트레이트-trait의 `validate` 메소드를 사용하고 싶지 않다면 `Validator` [파사드](/docs/{{version}}/facades)를 사용하여 validator 인스턴스를 수동으로 생성할 수 있습니다. 파사드에 `make` 메소드를 사용하면 새로운 validator 인스턴스가 생성됩니다:

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

request-요청이 유효성 검사를 통과하지 못한 것을 확인였다면 `withErrors` 메소드로 세션에 에러 메세지를 임시저장-flash 할 수 있습니다. 이 메소드를 사용하면 리다이렉트 후에 `$errors` 변수가 자동으로 뷰에서 공유되어 손쉽게 사용자에게 보여질 수 있습니다. `withErrors` 메소드는 validator, `MessageBag`, 혹은 PHP `array`를 전달 받습니다. 

#### 이름이 지정된 Error Bags

한 페이지 안에서 여러개의 form을 가지고 있다면 에러들의 `MessageBag`에 이름을 붙여 지정한 form에 맞는 에러 메세지를 조회할 수 있도록 할 수 있습니다. 단순히 `withErrors`에 이름을 두번째 인자로 전달하면 됩니다:

    return redirect('register')
                ->withErrors($validator, 'login');

그러면 `$errors` 변수에서 지정된 `MessageBag` 인스턴스에 접근할 수 있습니다. 

    {{ $errors->login->first('email') }}

#### 유효성 검사 이후에 후킹하기

유효성 검사가 완료된 후에 실행하고자 하는 콜백함수를 validator에 추가할 수 있습니다. 이를 통하면, 손쉽게 더 많은 유효성 검사를 실행할 수 있도록 하고, 에러 메시지 컬렉션에 에러 메시지를 더 추가할 수도 있습니다. 다음처럼 validator 인스턴스의 `after` 메소드를 사용하면 됩니다:

    $validator = Validator::make(...);

    $validator->after(function($validator) {
        if ($this->somethingElseIsInvalid()) {
            $validator->errors()->add('field', 'Something is wrong with this field!');
        }
    });

    if ($validator->fails()) {
        //
    }

<a name="form-request-validation"></a>
### Form request-요청 validation-유효성 검사

보다 복잡한 유효성 검사 시나리오를 고려해보면, "폼 요청(form request)" 클래스를 만들고자 할 수도 있습니다. form reqeust는 유효성검사 로직을 내장하고 있는 사용자 정의 request-요청 클래스입니다. 하나의 form request-요청 클래스를 만드려면, `make:request` 아티즌 명령을 사용하면 됩니다.

    php artisan make:request StoreBlogPostRequest

생성된 request-요청 클래스는 `app/Http/Requests` 디렉토리에 위치합니다. 유효성 검사 규칙 몇개를 `rules` 메소드에 추가해보겠습니다:

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

그럼 어떻게 이 유효성 검사 규칙이 실행되도록 할 수 있을까요? 필요한 것은 여러분의 컨트롤러 메소드에 해당 request-요청 클래스의 타입힌트를 넣어주는 것입니다. form request-요청은 컨트롤러 메소드가 호출되기 전에 미리 유효성 검사를 거칩니다. 이는 유효성 검사 로직을 위해 여러분의 컨트롤러를 지저분하게 할 필요가 없다는 것을 의미합니다.

    /**
     * Store the incoming blog post.
     *
     * @param  StoreBlogPostRequest  $request
     * @return Response
     */
    public function store(StoreBlogPostRequest $request)
    {
        // The incoming request is valid...
    }

만약 유효성 검사가 실패하면, 이전 페이지로의 리다이렉트 응답이 생성되어 사용자에게 보내집니다. 에러들 또한 화면에 출력될 수 있도록 세션에 기록될 것입니다. 만약 요청이 AJAX 요청이면, 422 상태 코드의 HTTP 응답이 JSON 형식의 오류 메시지를 포함한 채 사용자에게 반환됩니다.

#### Form Requests authorizing-승인

또한 form request-요청 클래스는 `authorize` 메소드를 가지고 있습니다. 이 메소드 안에서, 여러분은 사용자가 실제로 주어진 리소스를 업데이트할 권한을 가졌는지 확인 할 수 있습니다. 다음은 만약 사용자가 블로그 포스트의 댓글을 업데이트하려고 시도할 때, 실제로 사용자가 그 댓글의 소유자인가를 확인하는 예제입니다:

    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        $commentId = $this->route('comment');

        return Comment::where('id', $commentId)
                      ->where('user_id', Auth::id())->exists();
    }

이 예제에서 `route` 메소드를 호출하는 것에 주목하십시오. 예제에서 이 메소드는 `{comment}` 파라미터와 같이 파라미터가 정의된 라우트의 URI 에 여러분이 엑세스 접근할 수 있는지 권한을 확인합니다:

    Route::post('comment/{comment}');

만약 `authorize` 메소드가 `false`를 리턴하면, 403 HTTP 응답이 자동적으로 반환되고 컨트롤러 메소드는 실행되지 않을 것입니다.

여러분이 애플리케이션의 다른 부분에 있는 인증로직을 사용할 계획이라면, 그냥 `authorize` 메소드에서 `true`를 리턴하면 됩니다.

    /**
     * Determine if the user is authorized to make this request.
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

#### 임시저장된 에러의 포맷을 임의로 지정하기

만약 유효성 검사가 실패했을 때 세션에 저장되는 에러의 형식을 커스터마이징하고 싶다면, 베이스 request-요청 클래스(`App\Http\Requests\Request`)의 `formatErrors` 메소드를 오버라이딩하면 됩니다. 이때, 파일 상단에서 `Illuminate\Validation\Validator`를 임포트하는 것을 잊지 마십시오:

    /**
     * {@inheritdoc}
     */
    protected function formatErrors(Validator $validator)
    {
        return $validator->errors()->all();
    }

#### 에러 메세지를 사용자 정의하기(커스터마이징하기)

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

<a name="working-with-error-messages"></a>
## 에러 메시지 사용하기

`Validator` 인스턴스의 `messages` 메소드를 호출하면, 에러 메시지를 편하게 사용할 수 있는 다양한 메소드를 가진 `MessageBag` 인스턴스를 받을 수 있습니다.

#### 하나의 필드에 대한 첫번째 에러 메시지 조회하기

특정 필드에 대한 첫번째 에러 메세지를 조회하려면 `first` 메소드를 사용하면 됩니다: 

    $messages = $validator->errors();

    echo $messages->first('email');

#### 하나의 필드에 대한 모든 에러 메세지 조회하기

간단하게 하나의 필드에 대한 모든 에러 메세지를 조회하고 싶다면 `get` 메소드를 사용하면 됩니다:

    foreach ($messages->get('email') as $message) {
        //
    }

#### 모든 필드에 대한 모든 에러 메세지 조회하기

모든 필드에 대한 모든 에러 메세지를 조회하기 위해서는 `all` 메소들 사용하면 됩니다: 

    foreach ($messages->all() as $message) {
        //
    }

#### 하나의 필드에 대하여 에러 메시지가 존재하는지 검사하기

    if ($messages->has('email')) {
        //
    }

#### 특정 형식으로 에러 메시지 획득하기

    echo $messages->first('email', '<p>:message</p>');

#### 특정 형식으로 모든 에러 메시지 획득하기

    foreach ($messages->all('<li>:message</li>') as $message) {
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
        'between' => 'The :attribute must be between :min - :max.',
        'in'      => 'The :attribute must be one of the following types: :values',
    ];

#### 주어진 속성에 대해 커스텀 메세지 지정하기 

종종 하나의 특정 필드에 대해서만 커스텀 오류 메세지를 지정해야 하는 경우가 있습니다. 이것은 ".(점)" 표기법을 통해서 할 수 있습니다. 속성의 이름을 먼저 지정하고, 규칙을 명시하면됩니다: 

    $messages = [
        'email.required' => 'We need to know your e-mail address!',
    ];

<a name="localization"></a>
#### 언어 파일에 커스텀 메세지 지정하기 

많은 경우에서, `Validator`에 직접 메세지를 전달하는 대신, 언어 파일에 속성에 따른 커스텀 메세지를 지정하기 원할 수 있습니다. 이렇게 하기 위해서는 `resources/lang/xx/validation.php` 언어 파일의 `custom` 배열에 메제지를 추가하면 됩니다.

    'custom' => [
        'email' => [
            'required' => 'We need to know your e-mail address!',
        ],
    ],

<a name="available-validation-rules"></a>
## 사용가능한 유효성 검사 규칙들

다음은 모든 유효성 검사 규칙과 그들의 함수 목록입니다.

<style>
    .collection-method-list > p {
        column-count: 3; -moz-column-count: 3; -webkit-column-count: 3;
        column-gap: 2em; -moz-column-gap: 2em; -webkit-column-gap: 2em;
    }

    .collection-method-list a {
        display: block;
    }
</style>

[Accepted](#rule-accepted)
[Active URL](#rule-active-url)
[After (Date)](#rule-after)
[Alpha](#rule-alpha)
[Alpha Dash](#rule-alpha-dash)
[Alpha Numeric](#rule-alpha-num)
[Array](#rule-array)
[Before (Date)](#rule-before)
[Between](#rule-between)
[Boolean](#rule-boolean)
[Confirmed](#rule-confirmed)
[Date](#rule-date)
[Date Format](#rule-date-format)
[Different](#rule-different)
[Digits](#rule-digits)
[Digits Between](#rule-digits-between)
[E-Mail](#rule-email)
[Exists (Database)](#rule-exists)
[Image (File)](#rule-image)
[In](#rule-in)
[Integer](#rule-integer)
[IP Address](#rule-ip)
[JSON](#rule-json)
[Max](#rule-max)
[MIME Types (File)](#rule-mimes)
[Min](#rule-min)
[Not In](#rule-not-in)
[Numeric](#rule-numeric)
[Regular Expression](#rule-regex)
[Required](#rule-required)
[Required If](#rule-required-if)
[Required Unless](#rule-required-unless)
[Required With](#rule-required-with)
[Required With All](#rule-required-with-all)
[Required Without](#rule-required-without)
[Required Without All](#rule-required-without-all)
[Same](#rule-same)
[Size](#rule-size)
[String](#rule-string)
[Timezone](#rule-timezone)
[Unique (Database)](#rule-unique)
[URL](#rule-url)

<a name="rule-accepted"></a>
#### accepted

필드의 값이 _yes_, _on_, _1_, 또는 _true_이어야 합니다. 이 것은 "이용약관" 동의와 같은 필드의 검사에 유용합니다.

<a name="rule-active-url"></a>
#### active_url

필드의 값이 PHP 함수 `checkdnsrr`에 기반하여 올바른 URL이어야 합니다.

<a name="rule-after"></a>
#### after:_date_

필드의 값이 주어진 날짜 이후여야 합니다. 이때 날짜는 `strtotime` PHP 함수를 통해 생성된 값입니다.

    'start_date' => 'required|date|after:tomorrow'

`strtotime`에 의해 계산될 날짜 문자열을 전달하는 대신 날짜와 비교할 다른 필드를 명시할 수 있습니다: 

    'finish_date' => 'required|date|after:start_date'

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

<a name="rule-before"></a>
#### before:_date_

필드의 값이 반드시 주어진 날짜보다 앞서야 합니다. 날짜는 `strtotime` PHP 함수를 통해 비교됩니다.

<a name="rule-between"></a>
#### between:_min_,_max_

필드의 값이, 주어진 _min_ 과 _max_의 사이의 값이어야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 룰에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-boolean"></a>
#### boolean

필드의 값이 반드시 boolean으로 캐스팅될 수 있어야 합니다. 허용되는 값은 `true`, `false`, `1`, `0`, `"1"`, `"0"` 입니다.

<a name="rule-confirmed"></a>
#### confirmed

필드의 값이 `foo_confirmation`의 매칭되는 필드를 가져야 합니다. 예를 들어 만약 필드가 `password`라면, `password_confirmation`라는 필드가 입력값 중에 있어야 합니다.

<a name="rule-date"></a>
#### date

필드의 값이 `strtotime` PHP 함수에서 인식할 수 있는 올바른 날짜여야 합니다.

<a name="rule-date-format"></a>
#### date_format:_format_

필드의 값이 반드시 주어진 _format_과 일지해야 합니다. 주어진 포맷은 `date_parse_from_format` PHP 함수에 의해서 연산될 것입니다. 필드의 유효성을 검사할 때에는 `date`와 `date_format` 중 **하나만** 사용해야 합니다.

<a name="rule-different"></a>
#### different:_field_

필드의 값이 주어진 _field_의 값과 달라야 합니다.

<a name="rule-digits"></a>
#### digits:_value_

필드의 값이 반드시 _숫자_여야 하고, 길이가 _value_이어야 합니다.

<a name="rule-digits-between"></a>
#### digits_between:_min_,_max_

필드의 값이 주어진 _min_과 _max_ 사이의 길이를 가져야 합니다.

<a name="rule-email"></a>
#### email

필드의 값이 이메일 주소 형식이어야 합니다.

<a name="rule-exists"></a>
#### exists:_table_,_column_

필드의 값이 주어진 데이터베이스 테이블에 존재하는 값이어야 합니다.

#### exists 룰의 기본 사용법

    'state' => 'exists:states'

#### 특정 컬럼명 지정하기

    'state' => 'exists:states,abbreviation'

쿼리문의 "where" 구문에 추가될 더 많은 조건을 지정할 수도 있습니다: 

    'email' => 'exists:staff,email,account_id,1'

"where" 구분에 `NULL` 혹은 `NOT_NULL`을 전달할 수도 있습니다: 

    'email' => 'exists:staff,email,deleted_at,NULL'

    'email' => 'exists:staff,email,deleted_at,NOT_NULL'

<a name="rule-image"></a>
#### image

이미지 파일(jpeg, png, bmp, gif, svg)이어야 합니다.

<a name="rule-in"></a>
#### in:_foo_,_bar_,...

필드의 값이 주어진 목록에 포함돼 있어야 합니다.

<a name="rule-integer"></a>
#### integer

필드의 값이 정수여야 합니다.

<a name="rule-ip"></a>
#### ip

필드의 값이 IP 주소여야 합니다.

<a name="rule-json"></a>
#### json

필드의 값이 유효한 JSON 문자열이어야 합니다. 

<a name="rule-max"></a>
#### max:_value_

필드의 값이 반드시 _value_보다 작거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 룰에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-mimes"></a>
#### mimes:_foo_,_bar_,...

파일의 MIME 타입이 주어진 확장자 리스트 중에 하나와 일치해야 합니다.

#### MIME 룰의 기본 사용법

    'photo' => 'mimes:jpeg,bmp,png'

여러분은 확장자를 지정하기만 하면 되지만, 이 경우 파일의 컨텐트를 읽고 MIME 타입을 추정함으로써 이 파일의 MIME의 유효성을 검사합니다.

MIME 타입과 그에 상응하는 확장의 전체 목록은 다음의 위치에서 확인하실 수 있습니다: [http://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](http://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

<a name="rule-min"></a>
#### min:_value_

필드의 값이 반드시 _value_ 보다 크거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 룰에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-not-in"></a>
#### not_in:_foo_,_bar_,...

필드의 값이 주어진 목록에 존재하지 않아야 합니다.

<a name="rule-numeric"></a>
#### numeric

필드의 값이 숫자여야 합니다.

<a name="rule-regex"></a>
#### regex:_pattern_

필드의 값이 주어진 정규식 표현과 일치해야 합니다.

**참고:** `regex` 패턴을 사용할 때, 특히 정규 표현식에 파이프 문자열이 있다면, 파이프 구분자를 사용하는 대신 배열 형식을 사용하여 룰을 지정할 필요가 있습니다.

<a name="rule-required"></a>
#### required

입력 값 중에 해당 필드가 존재해야 하며 비어 있어서는 안됩니다. 필드는 다음의 조건 중 하나를 충족하면 "빈(empty)" 것으로 간주됩니다: 

- 값이 `null`인 경우.
- 값이 비어있는 문자열인 경우.
- 값이 비어있는 배열이거나, 비어있는 `Countable` 객체인경우 
- 값이 경로없이 업로드된 파일인 경우

<a name="rule-required-if"></a>
#### required_if:_anotherfield_,_value_,...

만약 _filed_의 값이 _value_중의 하나와 일치한다면, 해당 필드가 반드시 존재해야 합니다.

<a name="rule-required-unless"></a>
#### required_unless:_anotherfield_,_value_,...

_anotherfield_가 어떤 _value_와 동일하지 않은 이상 필드가 존재해야 합니다. 

<a name="rule-required-with"></a>
#### required_with:_foo_,_bar_,...

다른 지정된 필드중 하나라도 존재한다면, 해당 필드가 반드시 존재해야 합니다.

<a name="rule-required-with-all"></a>
#### required_with_all:_foo_,_bar_,...

다른 지정된 필드가 모두 존재한다면, 해당 필드가 반드시 존재해야 합니다.

<a name="rule-required-without"></a>
#### required_without:_foo_,_bar_,...

다른 지정된 필드중 하나라도 존재하지 않으면, 해당 필드가 반드시 존재해야 합니다.

<a name="rule-required-without-all"></a>
#### required_without_all:_foo_,_bar_,...

다른 지정된 필드가 모두 존재하지 않으면, 해당 필드가 존재해야 합니다.

<a name="rule-same"></a>
#### same:_field_

필드의 값이 주어진 _field_의 값과 일치해야 합니다.

<a name="rule-size"></a>
#### size:_value_

필드의 값이 주어진 _value_와 일치하는 크기를 가져야 합니다. 문자열 데이터에서는 문자의 개수가 _value_와 일치해야 합니다. 숫자형식의 데이터에서는 주어진 정수값이 _value_와 일치해야 합니다. 파일에서는 킬로바이트 형식의 파일 사이즈가 _size_와 일치해야 합니다.

<a name="rule-string"></a>
#### string

필드의 값이 반드시 문자열이어야 합니다.

<a name="rule-timezone"></a>
#### timezone

필드의 값이 `timezone_identifiers_list` PHP 함수에서 인식 가능한 유효한 timezone 식별자여야 합니다.

<a name="rule-unique"></a>
#### unique:_table_,_column_,_except_,_idColumn_

필드의 값이 주어진 데이터베이스 테이블에서 고유한 값이어야 합니다. 만약 `column`이 지정돼 있지 않다면 필드의 이름이 사용됩니다.

**특정 컬럼명 지정하기:**

    'email' => 'unique:users,email_address'

**특정 데이터베이스 커넥션**

때때로, 여러분은 Validator에 의해서 생성되는 데이터베이스 쿼리에 사용자가 지정한 커넥션을 필요로 할지도 모릅니다. 위에서의 검증 규칙 `unique:users` 에서는 데이터베이스를 쿼리하기 위해 기본 데이터 베이스 커넥션이 사용됩니다. 이를 재지정하려면 테이블 이름 후에 "." 표기법으로 커넥션을 지정하십시오:

    'email' => 'unique:connection.users,email_address'

**주어진 ID에 대해서 유니크 규칙을 무시하도록 강제하기:**

때때로 유니크 검사를 할 때 특정 ID를 무시하고자 할 수 있습니다. 예를 들어 사용자 이름, 이메일 주소 그리고 위치를 포함하는 "프로필 업데이트" 화면이 있습니다. 물론 이메일 주소가 고유하다는 것을 확인하고 싶을 것입니다. 하지만 사용자가 이름 필드만 바꾸고 이베일 필드를 바꾸지 않는다면 사용자가 이미 이메일 주소의 주인이기 때문에 유효 검사 오류가 던져지지 않아야 합니다. 유효 검사 오류는 사용자가 다른 사용자가 이미 사용하고 있는 이메일 주소를 제공할 때에만 던져져야 합니다. 유니크 규칙에 사용자 ID를 무시하라고 강제하기 위해서는 세번째 파라미터로 ID를 전달해야 합니다: 

    'email' => 'unique:users,email_address,'.$user->id

테이블이 `id`가 아닌 primary 키 컬럼 이름을 사용한다면 그 이름을 네번째 파라미터로 지정하면 됩니다: 

    'email' => 'unique:users,email_address,'.$user->id.',user_id'

**추가적인 Where 구문 추가하기:**

더 추가되는 조건은 쿼리에 추가될 "where" 구문에 대해서 지정될 것입니다. 

    'email' => 'unique:users,email_address,NULL,id,account_id,1'

위의 룰에서는, `account_id`가 `1`인 데이터 만이 유니크 검사에 포함됩니다. 

<a name="rule-url"></a>
#### url

필드는 반드시 PHP  `filter_var` 함수에 따라 유효한 URL이어야 합니다. 

<a name="conditionally-adding-rules"></a>
## 조건부 룰 추가하기

어떤 상황에서는 필드가 입력 배열에 존재할 때에만 그 필드의 유효성 검사를 실행하고 싶을수도 있습니다. 이를 위해서는 룰의 목록에 `sometimes`를 추가하기만 하면 됩니다:

    $v = Validator::make($data, [
        'email' => 'sometimes|required|email',
    ]);

이 예제에서는 `$data` 배열에 `email` 필드가 존재할 경우에만 그 필드의 유효성 검사가 실행됩니다.

#### 복잡한 조건부 유효성 검사

때때로 여러분은 한가지 이상의 복잡한 로직에 대해서 유효성 규칙을 추가하고자 할 수도 있습니다. 예를 들어, 어떤 다른 필드가 100 이상의 값을 가질때에만 주어진 필드가 반드시 존재하길 바랄 수도 있습니다. 또는 다른 필드가 존재할 때에만 두개의 필드가 주어진 값을 가질 필요가 있을수도 있습니다. 이러한 유효성 검사 룰을 추가하는 것이 어렵지 않을 수 있습니다. 우선 _고정 룰_을 변경할 필요 없이 그대로 사용하여 `Validator` 인스턴스를 생성합니다:

    $v = Validator::make($data, [
        'email' => 'required|email',
        'games' => 'required|numeric',
    ]);

여러분의 웹 애플리케이션이 게임 수집가들을 위한 사이트라고 가정해보겠습니다. 만약 100개 이상의 게임을 소유하고 있는 게임 수집가가 우리 사이트에 가입을 한다면, 우리는 그들이 왜 그렇게 많은 게임을 소유하고 있는지 설명을 듣고 싶을수 있습니다. 예를 들어, 아마 그들이 중고게임 판매점을 운영하거나, 단순히 수집을 취미로 할 수도 있습니다. 이런 요구사항을 조건부로 추가하기 위하여 `Validator` 인스턴스의 `sometimes` 메소드를 사용할 수 있습니다.

    $v->sometimes('reason', 'required|max:500', function($input) {
        return $input->games >= 100;
    });

`sometimes` 메소드에 전달되는 첫번째 인자는 필드의 이름입니다. 두번째 인자는 추가하려는 룰입니다. 만약 세번째 파라메터로 전달된 `Closure`가 `true`를 리턴한다면 그 룰은 유효성 검사에 추가될 것입니다. 이 메소드는 복잡한 조건부 유효성 검사의 구성을 손쉽게 만들어 주며, 한번에 여러개의 필드에 대한 조건부 유효성 검사를 추가할 수도 있습니다.

    $v->sometimes(['reason', 'cost'], 'required', function($input) {
        return $input->games >= 100;
    });

> **참고:** `Closure`로 전달된 `$input` 파라메터는 `Illuminate\Support\Fluent`의 인스턴스입니다. 그리고 입력된 데이터와 파일에 접근하기 위해 이 오브젝트가 사용할 수 있습니다.

<a name="custom-validation-rules"></a>
## 사용자 정의 유효성 검사 룰

라라벨은 다양하고 유용한 유효성 검사 룰을 제공합니다; 하지만, 여러분은 여러분만의 유효성 검사 룰을 정의하길 바랄수도 있습니다. 커스텀 유효성 검사 룰을 등록하는 방법중 하나는 `Validator` [파사드](/docs/{{version}}/facades)에 `extend` 메소드를 사용하는 것입니다. 이 메소드를 [서비스 프로바이더](/docs/{{version}}/providers) 내에서 사용하여 커스텀 유효성 검사 룰을 등록합니다:

    <?php

    namespace App\Providers;

    use Validator;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Validator::extend('foo', function($attribute, $value, $parameters, $validator) {
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

또한 클로저 대신 클래스명과 메소드명을 `extend` 메소드로 전달할 수도 있습니다.

    Validator::extend('foo', 'FooValidator@validate');

#### 에러 메세지 정의하기

커스텀 룰을 위한 에러 메세지 또한 정의해야 합니다. 인라인 커스텀 메세지 배열을 사용하거나 유효 검사 언어 파일에 엔트리를 추가하면 됩니다. 이 메세지는 `custom` 배열 안이 아닌 배열의 첫번째 레벨에 놓여야 합니다. `custom` 배열은 특정 속성에 따른 에러 메세지를 담당합니다:

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

        Validator::replacer('foo', function($message, $attribute, $rule, $parameters) {
            return str_replace(...);
        });
    }

#### 묵시적 확장

기본적으로 유효성 검사를 받는 속성이 존재하지 않거나 [`required`](#rule-required) 룰의 정의에 따라 빈 값을 가지고 있다면, 커스텀 확장을 포함한 정상적인 유효성 검사 룰은 실행되지 않을 것입니다. 예를 들어 `null` 값에는  [`integer`](#rule-integer) 룰이 실행되지 않을 것입니다: 

    $rules = ['count' => 'integer'];

    $input = ['count' => null];

    Validator::make($input, $rules)->passes(); // true

속성이 비었을 때도 룰이 실행되기 위해서는 룰에 속성이 필요하다는 것이 내포되어 있어야 합니다. 이런 "묵시적"인 확장을 만드려면 `Validator::extendImplicit()` 메소드를 사용하세요: 

    Validator::extendImplicit('foo', function($attribute, $value, $parameters, $validator) {
        return $value == 'foo';
    });

> **주의:** "묵시적" 확장은 단지 속성이 필요하다는 것을 _암시(내포)_합니다. 없거나 빈 속성의 유효성을 실제로 부정하는지는 여러분이 결정합니다.
