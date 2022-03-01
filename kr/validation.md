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
    - [Repopulating Forms](#repopulating-forms)
    - [양식-form 다시 채우기](#repopulating-forms)
    - [A Note On Optional Fields](#a-note-on-optional-fields)
    - [옵션 필드에 대한 주의사항](#a-note-on-optional-fields)
- [Form Request Validation](#form-request-validation)
- [Form Request 유효성 검증](#form-request-validation)
    - [Creating Form Requests](#creating-form-requests)
    - [Form Requests 생성하기](#creating-form-requests)
    - [Authorizing Form Requests](#authorizing-form-requests)
    - [Form Requests 사용자 승인](#authorizing-form-requests)
    - [Customizing The Error Messages](#customizing-the-error-messages)
    - [에러 메세지 사용자 정의하기](#customizing-the-error-messages)
    - [Preparing Input For Validation](#preparing-input-for-validation)
    - [Input 검증을 위한 사전 준비](#preparing-input-for-validation)
- [Manually Creating Validators](#manually-creating-validators)
- [Validators 수동으로 생성하기](#manually-creating-validators)
    - [Automatic Redirection](#automatic-redirection)
    - [자동으로 리다이렉트하기](#automatic-redirection)
    - [Named Error Bags](#named-error-bags)
    - [이름이 지정된 Error Bags](#named-error-bags)
    - [Customizing The Error Messages](#manual-customizing-the-error-messages)
    - [오류 메시지 사용자 정의](#manual-customizing-the-error-messages)
    - [After Validation Hook](#after-validation-hook)
    - [유효성 검사 이후에 후킹하기](#after-validation-hook)
- [Working With Validated Input](#working-with-validated-input)
- [검증된 입력값 사용하기](#working-with-validated-input)
- [Working With Error Messages](#working-with-error-messages)
- [에러 메세지 지정하기](#working-with-error-messages)
    - [Specifying Custom Messages In Language Files](#specifying-custom-messages-in-language-files)
    - [언어 파일에서 사용자 정의 메시지 지정하기](#specifying-custom-messages-in-language-files)
    - [Specifying Attributes In Language Files](#specifying-attribute-in-language-files)
    - [언어 파일에 속성 지정하기](#specifying-attribute-in-language-files)
    - [Specifying Values In Language Files](#specifying-values-in-language-files)
    - [언어 파일에 값 지정하기](#specifying-values-in-language-files)
- [Available Validation Rules](#available-validation-rules)
- [사용가능한 유효성 검증 규칙](#available-validation-rules)
- [Conditionally Adding Rules](#conditionally-adding-rules)
- [조건부로 규칙 추가하기](#conditionally-adding-rules)
- [Validating Arrays](#validating-arrays)
- [배열값 유효성 검증](#validating-arrays)
    - [Excluding Unvalidated Array Keys](#excluding-unvalidated-array-keys)
    - [검증되지 않은 배열 키 제외](#excluding-unvalidated-array-keys)
    - [Validating Nested Array Input](#validating-nested-array-input)
    - [중첩 배열 입력 검증](#validating-nested-array-input)
- [Validating Passwords](#validating-passwords)
- [비밀번호 검증](#validating-passwords)
- [Custom Validation Rules](#custom-validation-rules)
- [사용자 정의 유효성 검증 규칙](#custom-validation-rules)
    - [Using Rule Objects](#using-rule-objects)
    - [Rule 객체 사용하기](#using-rule-objects)
    - [Using Closures](#using-closures)
    - [클로저 사용하기](#using-closures)
    - [Implicit Rules](#implicit-rules)
    - [묵시적 규칙](#implicit-rules)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel provides several different approaches to validate your application's incoming data. It is most common to use the `validate` method available on all incoming HTTP requests. However, we will discuss other approaches to validation as well.

라라벨은 애플리케이션의 수신 데이터를 검증하기 위해 여러 가지 접근 방식을 제공합니다. 들어오는 모든 HTTP 요청에서 사용할 수 있는 `validate` 메서드를 사용하는 것이 가장 일반적입니다. 그러나 유효성 검사에 대한 다른 접근 방식도 논의할 것입니다.

Laravel includes a wide variety of convenient validation rules that you may apply to data, even providing the ability to validate if values are unique in a given database table. We'll cover each of these validation rules in detail so that you are familiar with all of Laravel's validation features.

라라벨에는 데이터에 적용할 수 있는 다양한 편리한 유효성 검사 규칙이 포함되어 있으며, 주어진 데이터베이스 테이블에서 값이 고유한지 확인하는 기능도 제공합니다. 라라벨의 모든 유효성 검사 기능에 익숙해질 수 있도록 이러한 유효성 검사 규칙 각각을 자세히 다룰 것입니다.

<a name="validation-quickstart"></a>
## Validation Quickstart
## 빠르게 유효성 검사 살펴보기

To learn about Laravel's powerful validation features, let's look at a complete example of validating a form and displaying the error messages back to the user. By reading this high-level overview, you'll be able to gain a good general understanding of how to validate incoming request data using Laravel:

라라벨의 강력한 유효성 검사 기능에 대해 알아보기 위해 양식 유효성을 검사하고 사용자에게 오류 메시지를 다시 표시하는 전체 예제를 살펴보겠습니다. 이 높은 수준의 개요를 읽으면 라라벨을 사용하여 들어오는 요청 데이터의 유효성을 검사하는 방법에 대한 일반적인 이해를 얻을 수 있습니다.

<a name="quick-defining-the-routes"></a>
### Defining The Routes
### 라우트 정의하기

First, let's assume we have the following routes defined in our `routes/web.php` file:

우선 다음의 라우트들이 `routes/web.php` 파일에 정의되어 있다고 가정해 보겠습니다.

    use App\Http\Controllers\PostController;

    Route::get('/post/create', [PostController::class, 'create']);
    Route::post('/post', [PostController::class, 'store']);

The `GET` route will display a form for the user to create a new blog post, while the `POST` route will store the new blog post in the database.

`GET` 라우트는 사용자가 새로운 블로그 포스트를 생성하기 위한 form을 나타낼 것이고, `POST` 라우트는 데이터베이스에 새로운 블로그 포스트를 저장할 것입니다.

<a name="quick-creating-the-controller"></a>
### Creating The Controller
### 컨트롤러 생성하기

Next, let's take a look at a simple controller that handles incoming requests to these routes. We'll leave the `store` method empty for now:

다음으로 이러한 경로로 들어오는 요청을 처리하는 간단한 컨트롤러를 살펴보겠습니다. 지금은 `store` 메소드를 비워둘 것입니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;

    class PostController extends Controller
    {
        /**
         * Show the form to create a new blog post.
         *
         * @return \Illuminate\View\View
         */
        public function create()
        {
            return view('post.create');
        }

        /**
         * Store a new blog post.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            // Validate and store the blog post...
        }
    }

<a name="quick-writing-the-validation-logic"></a>
### Writing The Validation Logic
### 유효성 검사 로직 작성하기

Now we are ready to fill in our `store` method with the logic to validate the new blog post. To do this, we will use the `validate` method provided by the `Illuminate\Http\Request` object. If the validation rules pass, your code will keep executing normally; however, if validation fails, an `Illuminate\Validation\ValidationException` exception will be thrown and the proper error response will automatically be sent back to the user.

이제 새 블로그 게시물의 유효성을 검사하는 논리로 `store` 메서드를 채울 준비가 되었습니다. 이를 위해 `Illuminate\Http\Request` 객체가 제공하는 `validate` 메소드를 사용할 것입니다. 유효성 검사 규칙을 통과하면 코드가 계속 정상적으로 실행됩니다. 그러나 유효성 검사가 실패하면 `Illuminate\Validation\ValidationException` 예외가 발생하고 적절한 오류 응답이 자동으로 사용자에게 다시 전송됩니다.

If validation fails during a traditional HTTP request, a redirect response to the previous URL will be generated. If the incoming request is an XHR request, a JSON response containing the validation error messages will be returned.

기존 HTTP 요청 중에 유효성 검사가 실패하면 이전 URL에 대한 리디렉션 응답이 생성됩니다. 들어오는 요청이 XHR 요청인 경우 유효성 검사 오류 메시지가 포함된 JSON 응답이 반환됩니다.

To get a better understanding of the `validate` method, let's jump back into the `store` method:

`validate` 메소드에 대해 더 잘 이해하기 위해, 다시 `store` 메소드로 돌아가 보겠습니다.

    /**
     * Store a new blog post.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        $validated = $request->validate([
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ]);

        // The blog post is valid...
    }

As you can see, the validation rules are passed into the `validate` method. Don't worry - all available validation rules are [documented](#available-validation-rules). Again, if the validation fails, the proper response will automatically be generated. If the validation passes, our controller will continue executing normally.

보시다시피 유효성 검사 규칙은 `validate` 메서드로 전달됩니다. 걱정하지 마십시오. 사용 가능한 모든 유효성 검사 규칙은 [문서화되어 있습니다](#available-validation-rules). 다시 한 번, 유효성 검사가 실패하면 적절한 응답이 자동으로 생성됩니다. 유효성 검사가 통과하면 컨트롤러가 계속 정상적으로 실행됩니다.

Alternatively, validation rules may be specified as arrays of rules instead of a single `|` delimited string:

또한, 유효성 검사 규칙은 구분자 `|` 대신 배열로 정의할 수 있습니다.

    $validatedData = $request->validate([
        'title' => ['required', 'unique:posts', 'max:255'],
        'body' => ['required'],
    ]);

In addition, you may use the `validateWithBag` method to validate a request and store any error messages within a [named error bag](#named-error-bags):

또한 `validateWithBag` 메소드를 사용하여 요청을 확인하고 오류 메시지를 [named error bag](#named-error-bags) 내에 저장할 수 있습니다.

    $validatedData = $request->validateWithBag('post', [
        'title' => ['required', 'unique:posts', 'max:255'],
        'body' => ['required'],
    ]);

<a name="stopping-on-first-validation-failure"></a>
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

<a name="a-note-on-nested-attributes"></a>
#### A Note On Nested Attributes
#### 중첩된 속성에 대한 유의사항

If the incoming HTTP request contains "nested" field data, you may specify these fields in your validation rules using "dot" syntax:

들어오는 HTTP 요청에 "중첩된" 필드 데이터가 포함된 경우 "점" 구문을 사용하여 유효성 검사 규칙에 다음 필드를 지정할 수 있습니다.

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

So, what if the incoming request fields do not pass the given validation rules? As mentioned previously, Laravel will automatically redirect the user back to their previous location. In addition, all of the validation errors and [request input](/docs/{{version}}/requests#retrieving-old-input) will automatically be [flashed to the session](/docs/{{version}}/session#flash-data).

그러면 들어오는 요청 필드가 주어진 유효성 검사 규칙을 통과하지 못하면 어떻게 될까요? 앞서 언급했듯이 라라벨은 자동으로 사용자를 이전 위치로 다시 리디렉션합니다. 또한 모든 유효성 검사 오류와 [입력 요청](/docs/{{version}}/requests#retriving-old-input)은 자동으로 [세션으로 플래시](/docs/{{version}}/session#flash-data)됩니다.

An `$errors` variable is shared with all of your application's views by the `Illuminate\View\Middleware\ShareErrorsFromSession` middleware, which is provided by the `web` middleware group. When this middleware is applied an `$errors` variable will always be available in your views, allowing you to conveniently assume the `$errors` variable is always defined and can be safely used. The `$errors` variable will be an instance of `Illuminate\Support\MessageBag`. For more information on working with this object, [check out its documentation](#working-with-error-messages).

`$errors` 변수는 `web` 미들웨어 그룹에서 제공하는 `Illuminate\View\Middleware\ShareErrorsFromSession` 미들웨어에 의해 모든 애플리케이션의 뷰와 공유됩니다. 이 미들웨어가 적용되면 `$errors` 변수는 항상 뷰에서 사용할 수 있으므로 `$errors` 변수가 항상 정의되어있고, 안전하게 사용할 수 있습니다. `$errors` 변수는 `Illuminate\Support\MessageBag`의 인스턴스가 됩니다. 이 개체로 작업하는 방법에 대한 자세한 내용은 [해당 문서를 확인하세요](#working-with-error-messages).

So, in our example, the user will be redirected to our controller's `create` method when validation fails, allowing us to display the error messages in the view:

따라서, 이 예제에서, 유효성 검사를 통과하지 못할 경우 사용자는 컨트롤러의 `create` 메소드로 리다이렉트 될것이고, 뷰에서는 에러 메세지가 표시됩니다.

```html
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
```

<a name="quick-customizing-the-error-messages"></a>
#### Customizing The Error Messages
#### 오류 메시지 사용자 정의

Laravel's built-in validation rules each has an error message that is located in your application's `resources/lang/en/validation.php` file. Within this file, you will find a translation entry for each validation rule. You are free to change or modify these messages based on the needs of your application.

라라벨의 내장 유효성 검사 규칙에는 각각 애플리케이션의 `resources/lang/en/validation.php` 파일에 오류 메시지가 존재합니다. 이 파일 내에서 각 유효성 검사 규칙에 대한 번역 항목을 찾을 수 있습니다. 애플리케이션의 필요에 따라 이러한 메시지를 자유롭게 변경하거나 수정할 수 있습니다.

In addition, you may copy this file to another translation language directory to translate the messages for your application's language. To learn more about Laravel localization, check out the complete [localization documentation](/docs/{{version}}/localization).

또한 이 파일을 다른 번역 언어 디렉토리에 복사하여 애플리케이션 언어로 메시지를 번역할 수 있습니다. 라라벨 현지화에 대해 자세히 알아보려면 전체 [현지화 문서](/docs/{{version}}/localization)를 확인하세요.

<a name="quick-xhr-requests-and-validation"></a>
#### XHR Requests & Validation
#### XHR 요청 및 검증

In this example, we used a traditional form to send data to the application. However, many applications receive XHR requests from a JavaScript powered frontend. When using the `validate` method during an XHR request, Laravel will not generate a redirect response. Instead, Laravel generates a JSON response containing all of the validation errors. This JSON response will be sent with a 422 HTTP status code.

이 예제에서는 기존의 form을 사용하여 애플리케이션에 데이터를 전송했습니다. 그러나 많은 애플리케이션이 JavaScript 기반 프론트엔드에서 XHR 요청을 수신합니다. XHR 요청 중에 `validate` 메소드를 사용할 때 라라벨은 리디렉션 응답을 생성하지 않습니다. 대신 라라벨은 모든 유효성 검사 오류가 포함된 JSON 응답을 생성합니다. 이 JSON 응답은 422 HTTP 상태 코드와 함께 전송됩니다.

<a name="the-at-error-directive"></a>
#### The `@error` Directive
#### `@error` 지시어

You may use the `@error` [Blade](/docs/{{version}}/blade) directive to quickly determine if validation error messages exist for a given attribute. Within an `@error` directive, you may echo the `$message` variable to display the error message:

`@error` [Blade](/docs/{{version}}/blade) 지시문을 사용하여 주어진 속성에 대한 유효성 검사 오류 메시지가 있는지 빠르게 확인할 수 있습니다. `@error` 지시문 내에서 `$message` 변수를 출력하여 오류 메시지를 표시할 수 있습니다.

```html
<!-- /resources/views/post/create.blade.php -->

<label for="title">Post Title</label>

<input id="title" type="text" name="title" class="@error('title') is-invalid @enderror">

@error('title')
<div class="alert alert-danger">{{ $message }}</div>
@enderror
```

If you are using [named error bags](#named-error-bags), you may pass the name of the error bag as the second argument to the `@error` directive:

[named error bags](#named-error-bags)를 사용하는 경우 오류 백의 이름을 `@error` 지시문에 대한 두 번째 인수로 전달할 수 있습니다.

```html
<input ... class="@error('title', 'post') is-invalid @enderror">
```

<a name="repopulating-forms"></a>
### Repopulating Forms
### Form 다시 채우기

When Laravel generates a redirect response due to a validation error, the framework will automatically [flash all of the request's input to the session](/docs/{{version}}/session#flash-data). This is done so that you may conveniently access the input during the next request and repopulate the form that the user attempted to submit.

라라벨이 유효성 검사 오류로 인해 리디렉션 응답을 생성하면 프레임워크는 자동으로 [세션에 대한 모든 요청 입력을 플래시](/docs/{{version}}/session#flash-data)합니다. 이는 다음 요청 중에 입력에 편리하게 액세스하고 사용자가 제출하려고 시도한 양식을 다시 채울 수 있도록 수행됩니다.

To retrieve flashed input from the previous request, invoke the `old` method on an instance of `Illuminate\Http\Request`. The `old` method will pull the previously flashed input data from the [session](/docs/{{version}}/session):

이전 요청에서 플래시 입력을 검색하려면 `Illuminate\Http\Request` 인스턴스에서 `old` 메서드를 호출하세요. `old` 메소드는 [세션](/docs/{{version}}/session)에서 이전에 플래시된 입력 데이터를 가져옵니다.

    $title = $request->old('title');

Laravel also provides a global `old` helper. If you are displaying old input within a [Blade template](/docs/{{version}}/blade), it is more convenient to use the `old` helper to repopulate the form. If no old input exists for the given field, `null` will be returned:

라라벨은 또한 전역 `old` 헬퍼를 제공합니다. [Blade 템플릿](/docs/{{version}}/blade) 내에서 이전 입력을 표시하는 경우 `old` 헬퍼를 사용하여 양식을 다시 채우는 것이 더 편리합니다. 주어진 필드에 대한 이전 입력이 없으면 `null`이 반환됩니다.

    <input type="text" name="title" value="{{ old('title') }}">

<a name="a-note-on-optional-fields"></a>
### A Note On Optional Fields
### 옵션 필드에 대한 주의사항

By default, Laravel includes the `TrimStrings` and `ConvertEmptyStringsToNull` middleware in your application's global middleware stack. These middleware are listed in the stack by the `App\Http\Kernel` class. Because of this, you will often need to mark your "optional" request fields as `nullable` if you do not want the validator to consider `null` values as invalid. For example:

기본적으로 라라벨은 애플리케이션의 글로벌 미들웨어 스택에 `TrimStrings` 그리고 `ConvertEmptyStringsToNull` 미들웨어를 포함하고 있습니다. 이 미들웨어는 `App\Http\Kernel` 클래스의 미들웨어 스택에 나열되어 있습니다. 이때문에, 유효성 검사에서 `null`이 유효하지 않은것으로 간주하지 않으려면 "선택적-optional" request-요청 필드를 `nullable`로 표시할 필요도 있습니다. 예를들면

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

In this example, we are specifying that the `publish_at` field may be either `null` or a valid date representation. If the `nullable` modifier is not added to the rule definition, the validator would consider `null` an invalid date.

이 예제에서는 `publish_at` 필드가 `null`이거나 유효한 날짜 형식이라고 지정했습니다. 만약 `nullable` 규칙이 추가되지 않은 경우 `null`값은 유효하지 않다고 결정됩니다.

<a name="form-request-validation"></a>
## Form Request Validation
## Form Request 유효성 검사

<a name="creating-form-requests"></a>
### Creating Form Requests
### Form Requests 생성하기

For more complex validation scenarios, you may wish to create a "form request". Form requests are custom request classes that encapsulate their own validation and authorization logic. To create a form request class, you may use the `make:request` Artisan CLI command:

더 복잡한 유효성 검사 시나리오의 경우 "form request"를 생성할 수 있습니다. form request는 자체 유효성 검사 및 권한 부여 논리를 캡슐화하는 사용자 지정 요청 클래스입니다. form request 클래스를 생성하려면 `make:request` Artisan CLI 명령을 사용할 수 있습니다.

    php artisan make:request StorePostRequest

The generated form request class will be placed in the `app/Http/Requests` directory. If this directory does not exist, it will be created when you run the `make:request` command. Each form request generated by Laravel has two methods: `authorize` and `rules`.

생성한 form request 클래스는 `app/Http/Requests` 디렉토리에 만들어집니다. 이 디렉토리가 존재하지 않으면 `make:request` 명령을 실행할 때 생성됩니다. 라라벨이 생성한 각 form request에는 `authorize`와 `rules`의 두 가지 메서드가 있습니다.

As you might have guessed, the `authorize` method is responsible for determining if the currently authenticated user can perform the action represented by the request, while the `rules` method returns the validation rules that should apply to the request's data:

짐작하셨겠지만 `authorize` 메소드는 현재 인증된 사용자가 요청이 실행할 권한이 존재하는지 검사하는 역할을 하는 반면, `rules` 메소드는 요청한 데이터에 적용해야 하는 유효성 검사 규칙을 반환합니다.

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

> {tip} You may type-hint any dependencies you require within the `rules` method's signature. They will automatically be resolved via the Laravel [service container](/docs/{{version}}/container).

> {tip} `rules` 메서드의 서명 내에서 필요한 모든 종속성을 입력할 수 있습니다. 라라벨 [서비스 컨테이너](/docs/{{version}}/container)를 통해 자동으로 해결됩니다.

So, how are the validation rules evaluated? All you need to do is type-hint the request on your controller method. The incoming form request is validated before the controller method is called, meaning you do not need to clutter your controller with any validation logic:

그렇다면 유효성 검사 규칙은 어떻게 실행할까요? 여러분이 해야할일은 컨트롤러 메소드에 request 를 타입-힌트 하는 것입니다. 유입된 form request 는 컨트롤러 메소드가 호출되기 전에 유효성 검사를 수행합니다. 즉 컨트롤러에 유효성 검사 로직을 포함시키지 않아도 됩니다.

    /**
     * Store a new blog post.
     *
     * @param  \App\Http\Requests\StorePostRequest  $request
     * @return Illuminate\Http\Response
     */
    public function store(StorePostRequest $request)
    {
        // The incoming request is valid...

        // Retrieve the validated input data...
        $validated = $request->validated();

        // Retrieve a portion of the validated input data...
        $validated = $request->safe()->only(['name', 'email']);
        $validated = $request->safe()->except(['name', 'email']);
    }

If validation fails, a redirect response will be generated to send the user back to their previous location. The errors will also be flashed to the session so they are available for display. If the request was an XHR request, an HTTP response with a 422 status code will be returned to the user including a JSON representation of the validation errors.

유효성 검사가 실패하면 리디렉션 응답이 생성되어 사용자를 이전 위치로 다시 보냅니다. 오류도 표시할 수 있도록 세션에 플래시됩니다. 요청이 XHR 요청인 경우 유효성 검사 오류의 JSON 표현을 포함하여 422 상태 코드가 포함된 HTTP 응답이 사용자에게 반환됩니다.

<a name="adding-after-hooks-to-form-requests"></a>
#### Adding After Hooks To Form Requests
#### Form Request 에 After 후킹 추가하기

If you would like to add an "after" validation hook to a form request, you may use the `withValidator` method. This method receives the fully constructed validator, allowing you to call any of its methods before the validation rules are actually evaluated:

"이후" 유효성 검사 후크를 form request에 추가하려면 `withValidator` 메서드를 사용할 수 있습니다. 이 메서드는 완전히 구성이 완료된 유효성 검사기를 입력받으므로, 유효성 검사 규칙이 실제로 실행되기 전에 해당 메서드를 호출할 수 있습니다.

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


<a name="request-stopping-on-first-validation-rule-failure"></a>
#### Stopping On First Validation Failure Attribute
#### 유효성 검사가 처음 실패한 속성에서 중단하기

By adding a `stopOnFirstFailure` property to your request class, you may inform the validator that it should stop validating all attributes once a single validation failure has occurred:

요청 클래스에 `stopOnFirstFailure` 속성을 추가하면, 단일 유효성 검사 실패가 발생하면 유효성 검사기에 모든 속성 유효성 검사를 중지해야 한다고 알릴 수 있습니다.

    /**
     * Indicates if the validator should stop on the first rule failure.
     *
     * @var bool
     */
    protected $stopOnFirstFailure = true;

<a name="customizing-the-redirect-location"></a>
#### Customizing The Redirect Location
#### 리디렉션 위치 사용자 지정

As previously discussed, a redirect response will be generated to send the user back to their previous location when form request validation fails. However, you are free to customize this behavior. To do so, define a `$redirect` property on your form request:

이전에 논의한 바와 같이, form request 유효성 검사가 실패할 때 사용자를 이전 위치로 다시 보내기 위해 리디렉션 응답이 생성됩니다. 그러나 이 동작을 자유롭게 사용자 지정할 수 있습니다. 이렇게 하려면 form request에 `redirect` 속성을 정의하십시오.

    /**
     * The URI that users should be redirected to if validation fails.
     *
     * @var string
     */
    protected $redirect = '/dashboard';

Or, if you would like to redirect users to a named route, you may define a `$redirectRoute` property instead:

또는 사용자를 이름이 지정된 경로로 리디렉션하려면 `redirectRoute` 속성을 대신 정의할 수 있습니다.

    /**
     * The route that users should be redirected to if validation fails.
     *
     * @var string
     */
    protected $redirectRoute = 'dashboard';

<a name="authorizing-form-requests"></a>
### Authorizing Form Requests
### Form Requests 사용자 승인

The form request class also contains an `authorize` method. Within this method, you may determine if the authenticated user actually has the authority to update a given resource. For example, you may determine if a user actually owns a blog comment they are attempting to update. Most likely, you will interact with your [authorization gates and policies](/docs/{{version}}/authorization) within this method:

form request 클래스에는 `authorize` 메서드도 포함되어 있습니다. 이 메서드 내에서 인증된 사용자에게 실제로 주어진 리소스를 업데이트할 권한이 있는지 확인할 수 있습니다. 예를 들어 사용자가 업데이트하려는 블로그 댓글을 실제로 소유하고 있는지 확인할 수 있습니다. 대부분 다음 메서드 내에서 [인증 게이트 및 정책](/docs/{{version}}/authorization)과 상호 작용합니다.

    use App\Models\Comment;

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

Since all form requests extend the base Laravel request class, we may use the `user` method to access the currently authenticated user. Also, note the call to the `route` method in the example above. This method grants you access to the URI parameters defined on the route being called, such as the `{comment}` parameter in the example below:

모든 form request는 기본 라라벨 요청 클래스를 확장하므로 `user` 메소드를 사용하여 현재 인증된 사용자에 액세스할 수 있습니다. 또한 위의 예에서 `route` 메서드에 대한 호출을 확인하세요. 이 메소드는 아래 예의 `{comment}` 매개변수와 같이 호출되는 경로에 정의된 URI 매개변수에 대한 액세스 권한을 부여합니다.

    Route::post('/comment/{comment}');

Therefore, if your application is taking advantage of [route model binding](/docs/{{version}}/routing#route-model-binding), your code may be made even more succinct by accessing the resolved model as a property of the request:

따라서 애플리케이션이 [경로 모델 바인딩](/docs/{{version}}/routing#route-model-binding)을 활용하는 경우, 확인된 모델을 요청의 속성으로 액세스하여 코드를 훨씬 더 간결하게 만들 수 있습니다.

    return $this->user()->can('update', $this->comment);

If the `authorize` method returns `false`, an HTTP response with a 403 status code will automatically be returned and your controller method will not execute.

`authorize` 메서드가 `false`를 반환하면 403 상태 코드가 포함된 HTTP 응답이 자동으로 반환되고 컨트롤러 메서드가 실행되지 않습니다.

If you plan to handle authorization logic for the request in another part of your application, you may simply return `true` from the `authorize` method:

애플리케이션의 다른 부분에서 요청에 대한 권한 부여 논리를 처리하려는 경우 `authorize` 메서드에서 `true`를 반환하기만 하면 됩니다.

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

> {tip} `run` 메소드에 필요한 모든 의존성의 타입힌트를 지정할 수 있습니다. 이 의존성은 라라벨 [서비스 컨테이너](/docs/{{version}}/container)를 통해 자동으로 처리됩니다.

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
#### Customizing The Validation Attributes
#### 검증 속성 사용자 정의

Many of Laravel's built-in validation rule error messages contain an `:attribute` placeholder. If you would like the `:attribute` placeholder of your validation message to be replaced with a custom attribute name, you may specify the custom names by overriding the `attributes` method. This method should return an array of attribute / name pairs:

라라벨의 내장 유효성 검사 규칙 오류 메시지 중 많은 부분이 `:attribute` 자리 표시자를 포함합니다. 유효성 검사 메시지의 `:attribute` 자리 표시자를 사용자 지정 속성 이름으로 바꾸려면 `attributes` 메서드를 재정의하여 사용자 지정 이름을 지정할 수 있습니다. 이 메서드는 속성 이름 쌍의 배열을 반환해야 합니다.

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

<a name="preparing-input-for-validation"></a>
### Preparing Input For Validation
### 검증을 위한 입력 준비

If you need to prepare or sanitize any data from the request before you apply your validation rules, you may use the `prepareForValidation` method:

유효성 검사 규칙을 적용하기 전에 요청의 데이터를 준비하거나 삭제해야 하는 경우 `prepareForValidation` 메서드를 사용할 수 있습니다.

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

            // Retrieve the validated input...
            $validated = $validator->validated();

            // Retrieve a portion of the validated input...
            $validated = $validator->safe()->only(['name', 'email']);
            $validated = $validator->safe()->except(['name', 'email']);

            // Store the blog post...
        }
    }

The first argument passed to the `make` method is the data under validation. The second argument is an array of the validation rules that should be applied to the data.

`make` 메서드에 전달된 첫 번째 인수는 유효성 검사 중인 데이터입니다. 두 번째 인수는 데이터에 적용해야 하는 유효성 검사 규칙의 배열입니다.

After determining whether the request validation failed, you may use the `withErrors` method to flash the error messages to the session. When using this method, the `$errors` variable will automatically be shared with your views after redirection, allowing you to easily display them back to the user. The `withErrors` method accepts a validator, a `MessageBag`, or a PHP `array`.

요청 유효성 검사가 실패했는지 확인한 후 `withErrors` 메서드를 사용하여 세션에 오류 메시지를 플래시할 수 있습니다. 이 방법을 사용할 때 `errors` 변수는 리디렉션 후 뷰와 자동으로 공유되어 사용자에게 쉽게 다시 표시할 수 있습니다. `withErrors` 메소드는 유효성 검사기, `MessageBag` 또는 PHP `array`를 허용합니다.

#### Stopping On First Validation Failure
#### 유효성 검사가 처음 실패하면 검사 중단하기

The `stopOnFirstFailure` method will inform the validator that it should stop validating all attributes once a single validation failure has occurred:

`stopOnFirstFailure` 메서드는 단일 유효성 검사 실패가 발생하면 모든 속성 유효성 검사를 중지해야 한다고 유효성 검사기에 알립니다.

    if ($validator->stopOnFirstFailure()->fails()) {
        // ...
    }

<a name="automatic-redirection"></a>
### Automatic Redirection
### 자동으로 리다이렉트하기

If you would like to create a validator instance manually but still take advantage of the automatic redirection offered by the HTTP request's `validate` method, you may call the `validate` method on an existing validator instance. If validation fails, the user will automatically be redirected or, in the case of an XHR request, a JSON response will be returned:

유효성 검사기 인스턴스를 수동으로 만들고 싶지만 HTTP 요청의 `validate` 메서드가 제공하는 자동 리디렉션을 계속 활용하려면, 기존 유효성 검사기 인스턴스에서 `validate` 메서드를 호출할 수 있습니다. 유효성 검사가 실패하면 사용자가 자동으로 리디렉션되거나 XHR 요청의 경우 JSON 응답이 반환됩니다.

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

If you have multiple forms on a single page, you may wish to name the `MessageBag` containing the validation errors, allowing you to retrieve the error messages for a specific form. To achieve this, pass a name as the second argument to `withErrors`:

한 페이지에 여러 양식이 있는 경우 유효성 검사 오류가 포함된 `MessageBag`의 이름을 지정하여 특정 양식에 대한 오류 메시지를 검색할 수 있습니다. 이를 달성하려면 `withErrors`에 두 번째 인수로 이름을 전달하세요.

    return redirect('register')->withErrors($validator, 'login');

You may then access the named `MessageBag` instance from the `$errors` variable:

그러면 `$errors` 변수에서 지정된 `MessageBag` 인스턴스에 접근할 수 있습니다.

    {{ $errors->login->first('email') }}

<a name="manual-customizing-the-error-messages"></a>
### Customizing The Error Messages
### 오류 메시지 사용자 정의

If needed, you may provide custom error messages that a validator instance should use instead of the default error messages provided by Laravel. There are several ways to specify custom messages. First, you may pass the custom messages as the third argument to the `Validator::make` method:

필요한 경우, 라라벨에서 제공하는 기본 오류 메시지 대신 유효성 검사기 인스턴스가 사용해야 하는 사용자 정의 오류 메시지를 제공할 수 있습니다. 사용자 지정 메시지를 지정하는 방법에는 여러 가지가 있습니다. 먼저 사용자 정의 메시지를 `Validator::make` 메소드에 세 번째 인수로 전달할 수 있습니다.

    $validator = Validator::make($input, $rules, $messages = [
        'required' => 'The :attribute field is required.',
    ]);

In this example, the `:attribute` placeholder will be replaced by the actual name of the field under validation. You may also utilize other placeholders in validation messages. For example:

이 예제에서 `:attribute` 자리 표시자는 유효성 검사 중인 필드의 실제 이름으로 대체됩니다. 유효성 검사 메시지에서 다른 자리 표시자를 활용할 수도 있습니다. 예를 들어

    $messages = [
        'same' => 'The :attribute and :other must match.',
        'size' => 'The :attribute must be exactly :size.',
        'between' => 'The :attribute value :input is not between :min - :max.',
        'in' => 'The :attribute must be one of the following types: :values',
    ];

<a name="specifying-a-custom-message-for-a-given-attribute"></a>
#### Specifying A Custom Message For A Given Attribute
#### 주어진 속성에 대한 사용자 정의 메시지 지정

Sometimes you may wish to specify a custom error message only for a specific attribute. You may do so using "dot" notation. Specify the attribute's name first, followed by the rule:

때로는 특정 속성에 대해서만 사용자 정의 오류 메시지를 지정하고자 할 수 있습니다. "점" 표기법을 사용하여 그렇게 할 수 있습니다. 속성의 이름을 먼저 지정하고 그 다음에 규칙을 지정합니다.

    $messages = [
        'email.required' => 'We need to know your email address!',
    ];

<a name="specifying-custom-attribute-values"></a>
#### Specifying Custom Attribute Values
#### 사용자 정의 속성 값 지정

Many of Laravel's built-in error messages include an `:attribute` placeholder that is replaced with the name of the field or attribute under validation. To customize the values used to replace these placeholders for specific fields, you may pass an array of custom attributes as the fourth argument to the `Validator::make` method:

라라벨에 내장된 많은 오류 메시지에는 검증 중인 필드 또는 속성의 이름으로 대체되는 `:attribute` 자리 표시자가 포함되어 있습니다. 특정 필드에 대한 이러한 자리 표시자를 대체하는 데 사용되는 값을 사용자 지정하려면 `Validator::make` 메서드에 대한 네 번째 인수로 사용자 지정 속성 배열을 전달할 수 있습니다.

    $validator = Validator::make($input, $rules, $messages, [
        'email' => 'email address',
    ]);

<a name="after-validation-hook"></a>
### After Validation Hook
### 유효성 검사 이후에 후킹하기

You may also attach callbacks to be run after validation is completed. This allows you to easily perform further validation and even add more error messages to the message collection. To get started, call the `after` method on a validator instance:

유효성 검사가 완료된 후 실행할 콜백을 첨부할 수도 있습니다. 이를 통해 추가 유효성 검사를 쉽게 수행하고 메시지 컬렉션에 더 많은 오류 메시지를 추가할 수 있습니다. 시작하려면 유효성 검사기 인스턴스에서 `after` 메서드를 호출하세요.

    $validator = Validator::make(...);

    $validator->after(function ($validator) {
        if ($this->somethingElseIsInvalid()) {
            $validator->errors()->add(
                'field', 'Something is wrong with this field!'
            );
        }
    });

    if ($validator->fails()) {
        //
    }

<a name="working-with-validated-input"></a>
## Working With Validated Input
## 검증된 입력값 사용하기

After validating incoming request data using a form request or a manually created validator instance, you may wish to retrieve the incoming request data that actually underwent validation. This can be accomplished in several ways. First, you may call the `validated` method on a form request or validator instance. This method returns an array of the data that was validated:

form request 또는 수동으로 생성된 유효성 검사기 인스턴스를 사용하여 들어오는 요청 데이터의 유효성을 검사한 후 실제로 유효성 검사를 거친 들어오는 요청 데이터를 검색할 수 있습니다. 이것은 여러 가지 방법으로 수행할 수 있습니다. 먼저 form request 또는 유효성 검사기 인스턴스에서 `validated` 메서드를 호출할 수 있습니다. 이 메서드는 검증된 데이터의 배열을 반환합니다.

    $validated = $request->validated();

    $validated = $validator->validated();

Alternatively, you may call the `safe` method on a form request or validator instance. This method returns an instance of `Illuminate\Support\ValidatedInput`. This object exposes `only`, `except`, and `all` methods to retrieve a subset of the validated data or the entire array of validated data:

또는 form request 또는 유효성 검사기 인스턴스에서 `safe` 메서드를 호출할 수 있습니다. 이 메소드는 `Illuminate\Support\ValidatedInput`의 인스턴스를 반환합니다. 이 객체는 검증된 데이터의 하위 집합 또는 검증된 데이터의 전체 배열을 검색하기 위해 `only`, `except` 및 `all` 메서드를 노출합니다.

    $validated = $request->safe()->only(['name', 'email']);

    $validated = $request->safe()->except(['name', 'email']);

    $validated = $request->safe()->all();

In addition, the `Illuminate\Support\ValidatedInput` instance may be iterated over and accessed like an array:

또한 `Illuminate\Support\ValidatedInput` 인스턴스는 배열처럼 반복하거나 액세스할 수 있습니다.

    // Validated data may be iterated...
    foreach ($request->safe() as $key => $value) {
        //
    }

    // Validated data may be accessed as an array...
    $validated = $request->safe();

    $email = $validated['email'];

If you would like to add additional fields to the validated data, you may call the `merge` method:

검증된 데이터에 추가 필드를 추가하려면 `merge` 메소드를 호출할 수 있습니다.

    $validated = $request->safe()->merge(['name' => 'Taylor Otwell']);

If you would like to retrieve the validated data as a [collection](/docs/{{version}}/collections) instance, you may call the `collect` method:

검증된 데이터를 [collection](/docs/{{version}}/collections) 인스턴스로 검색하려면 `collect` 메소드를 호출하면 됩니다.

    $collection = $request->safe()->collect();

<a name="working-with-error-messages"></a>
## Working With Error Messages
## 에러 메시지 작업하기

After calling the `errors` method on a `Validator` instance, you will receive an `Illuminate\Support\MessageBag` instance, which has a variety of convenient methods for working with error messages. The `$errors` variable that is automatically made available to all views is also an instance of the `MessageBag` class.

`Validator` 인스턴스의 `errors` 메소드를 호출하면, 에러 메시지를 편하게 사용할 수 있는 다양한 메소드를 가진 `MessageBag` 인스턴스를 받을 수 있습니다, 이것은 오류메세지를 처리하기 위한 다양하고 편리한 메소드들을 가집니다. `$errors` 변수는 자동으로 모든 뷰에서 `MessageBag` 클래스 인스턴스로써 사용가능합니다.

<a name="retrieving-the-first-error-message-for-a-field"></a>
#### Retrieving The First Error Message For A Field
#### 하나의 필드에 대한 첫번째 에러 메시지 조회하기

To retrieve the first error message for a given field, use the `first` method:

특정 필드에 대한 첫번째 에러 메세지를 조회하려면 `first` 메소드를 사용하면 됩니다.

    $errors = $validator->errors();

    echo $errors->first('email');

<a name="retrieving-all-error-messages-for-a-field"></a>
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

<a name="retrieving-all-error-messages-for-all-fields"></a>
#### Retrieving All Error Messages For All Fields
#### 모든 필드에 대한 모든 에러 메세지 조회하기

To retrieve an array of all messages for all fields, use the `all` method:

모든 필드에 대한 모든 에러 메세지를 조회하기 위해서는 `all` 메소들 사용하면 됩니다.

    foreach ($errors->all() as $message) {
        //
    }

<a name="determining-if-messages-exist-for-a-field"></a>
#### Determining If Messages Exist For A Field
#### 하나의 필드에 대하여 에러 메시지가 존재하는지 검사하기

The `has` method may be used to determine if any error messages exist for a given field:

`has` 메소드는 특정 필드에 대한 에러 메세지가 존재하는지 확인하는데 사용합니다.

    if ($errors->has('email')) {
        //
    }

<a name="specifying-custom-messages-in-language-files"></a>
### Specifying Custom Messages In Language Files
### 언어 파일에서 사용자 정의 메시지 지정하기

Laravel's built-in validation rules each has an error message that is located in your application's `resources/lang/en/validation.php` file. Within this file, you will find a translation entry for each validation rule. You are free to change or modify these messages based on the needs of your application.

라라벨의 내장 유효성 검사 규칙은 각각 애플리케이션의 `resources/lang/en/validation.php` 파일에 오류 메시지가 있습니다. 이 파일 내에서 각 유효성 검사 규칙에 대한 번역 항목을 찾을 수 있습니다. 애플리케이션의 필요에 따라 이러한 메시지를 자유롭게 변경하거나 수정할 수 있습니다.

In addition, you may copy this file to another translation language directory to translate the messages for your application's language. To learn more about Laravel localization, check out the complete [localization documentation](/docs/{{version}}/localization).

또한 이 파일을 다른 번역 언어 디렉토리에 복사하여 애플리케이션 언어로 메시지를 번역할 수 있습니다. 라라벨 현지화에 대해 자세히 알아보려면 전체 [현지화 문서](/docs/{{version}}/localization)를 확인하세요.

<a name="custom-messages-for-specific-attributes"></a>
#### Custom Messages For Specific Attributes
#### 특정 속성에 대한 사용자 정의 메시지

You may customize the error messages used for specified attribute and rule combinations within your application's validation language files. To do so, add your message customizations to the `custom` array of your application's `resources/lang/xx/validation.php` language file:

애플리케이션의 검증 언어 파일 내에서 지정된 속성 및 규칙 조합에 사용되는 오류 메시지를 사용자 정의할 수 있습니다. 그렇게 하려면 애플리케이션의 `resources/lang/xx/validation.php` 언어 파일의 `custom` 배열에 메시지 사용자 정의를 추가하십시오.

    'custom' => [
        'email' => [
            'required' => 'We need to know your email address!',
            'max' => 'Your email address is too long!'
        ],
    ],

<a name="specifying-attribute-in-language-files"></a>
### Specifying Attributes In Language Files
### 언어 파일에 속성 지정하기

Many of Laravel's built-in error messages include an `:attribute` placeholder that is replaced with the name of the field or attribute under validation. If you would like the `:attribute` portion of your validation message to be replaced with a custom value, you may specify the custom attribute name in the `attributes` array of your `resources/lang/xx/validation.php` language file:

라라벨에 내장된 많은 오류 메시지에는 검증 중인 필드 또는 속성의 이름으로 대체되는 `:attribute` 자리 표시자가 포함되어 있습니다. 유효성 검사 메시지의 `:attribute` 부분을 사용자 정의 값으로 바꾸려면 `resources/lang/xx/validation.php` 언어 파일의 `attributes` 배열에 사용자 정의 속성 이름을 지정할 수 있습니다.

    'attributes' => [
        'email' => 'email address',
    ],

<a name="specifying-values-in-language-files"></a>
### Specifying Values In Language Files
### 언어 파일에 값 지정하기

Some of Laravel's built-in validation rule error messages contain a `:value` placeholder that is replaced with the current value of the request attribute. However, you may occasionally need the `:value` portion of your validation message to be replaced with a custom representation of the value. For example, consider the following rule that specifies that a credit card number is required if the `payment_type` has a value of `cc`:

라라벨의 내장 유효성 검사 규칙 오류 메시지 중 일부는 요청 속성의 현재 값으로 대체되는 `:value` 자리 표시자를 포함합니다. 그러나 때때로 유효성 검사 메시지의 `:value` 부분을 값의 사용자 지정 표현으로 대체해야 할 수도 있습니다. 예를 들어 `payment_type`의 값이 `cc`인 경우 신용 카드 번호가 필요하다고 지정하는 다음 규칙을 고려하십시오.

    Validator::make($request->all(), [
        'credit_card_number' => 'required_if:payment_type,cc'
    ]);

If this validation rule fails, it will produce the following error message:

이 유효성 검사 규칙이 실패하면 다음과 같은 오류 메시지가 나타납니다.

    The credit card number field is required when payment type is cc.

Instead of displaying `cc` as the payment type value, you may specify a more user-friendly value representation in your `resources/lang/xx/validation.php` language file by defining a `values` array:

결제 유형 값으로 `cc`를 표시하는 대신 `values` 배열을 정의하여 `resources/lang/xx/validation.php` 언어 파일에 보다 사용자 친화적인 값 표현을 지정할 수 있습니다.

    'values' => [
        'payment_type' => [
            'cc' => 'credit card'
        ],
    ],

After defining this value, the validation rule will produce the following error message:

이 값을 정의한 후 유효성 검사 규칙은 다음 오류 메시지를 생성합니다.

    The credit card number field is required when payment type is credit card.

<a name="available-validation-rules"></a>
## Available Validation Rules
## 사용가능한 유효성 검사 규칙

Below is a list of all available validation rules and their function:

다음은 사용 가능한 모든 유효성 검사 규칙과 그 기능의 목록입니다.

- [Accepted](#rule-accepted)
- [Accepted If](#rule-accepted-if)
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
- [Current Password](#rule-current-password)
- [Date](#rule-date)
- [Date Equals](#rule-date-equals)
- [Date Format](#rule-date-format)
- [Declined](#rule-declined)
- [Declined If](#rule-declined-if)
- [Different](#rule-different)
- [Digits](#rule-digits)
- [Digits Between](#rule-digits-between)
- [Dimensions (Image Files)](#rule-dimensions)
- [Distinct](#rule-distinct)
- [Email](#rule-email)
- [Ends With](#rule-ends-with)
- [Enum](#rule-enum)
- [Exclude](#rule-exclude)
- [Exclude If](#rule-exclude-if)
- [Exclude Unless](#rule-exclude-unless)
- [Exclude Without](#rule-exclude-without)
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
- [MAC Address](#rule-mac)
- [JSON](#rule-json)
- [Less Than](#rule-lt)
- [Less Than Or Equal](#rule-lte)
- [Max](#rule-max)
- [MIME Types](#rule-mimetypes)
- [MIME Type By File Extension](#rule-mimes)
- [Min](#rule-min)
- [Multiple Of](#multiple-of)
- [Not In](#rule-not-in)
- [Not Regex](#rule-not-regex)
- [Nullable](#rule-nullable)
- [Numeric](#rule-numeric)
- [Password](#rule-password)
- [Present](#rule-present)
- [Prohibited](#rule-prohibited)
- [Prohibited If](#rule-prohibited-if)
- [Prohibited Unless](#rule-prohibited-unless)
- [Prohibits](#rule-prohibits)
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
- [Sometimes](#validating-when-present)
- [Starts With](#rule-starts-with)
- [String](#rule-string)
- [Timezone](#rule-timezone)
- [Unique (Database)](#rule-unique)
- [URL](#rule-url)
- [UUID](#rule-uuid)

<a name="rule-accepted"></a>
#### accepted
#### accepted

The field under validation must be `"yes"`, `"on"`, `1`, or `true`. This is useful for validating "Terms of Service" acceptance or similar fields.

필드의 값이 `"yes"`, `"on"`, `1`, 또는 `true` 여야 합니다. 이 것은 "이용약관" 동의와 같은 필드의 검사에 유용합니다.

<a name="rule-accepted-if"></a>
#### accepted_if:anotherfield,value,...
#### accepted_if:anotherfield,value,...

The field under validation must be `"yes"`, `"on"`, `1`, or `true` if another field under validation is equal to a specified value. This is useful for validating "Terms of Service" acceptance or similar fields.

검증 중인 다른 필드가 지정된 값과 같은 경우 검증 중인 필드는 `"yes"`, `"on"`, `1` 또는 `true`여야 합니다. 이는 "서비스 약관" 수락 또는 유사한 필드의 유효성을 검사하는 데 유용합니다.

<a name="rule-active-url"></a>
#### active_url
#### active_url

The field under validation must have a valid A or AAAA record according to the `dns_get_record` PHP function. The hostname of the provided URL is extracted using the `parse_url` PHP function before being passed to `dns_get_record`.

검증중인 필드는 `dns_get_record` PHP 함수에 따라 유효한 A 또는 AAAA 레코드를 가져야합니다. 제공된 URL의 호스트 이름은 `pars_url` PHP 함수를 사용하여 추출되어 `dns_get_record`에 전달됩니다.

<a name="rule-after"></a>
#### after:_date_
#### after:_date_

The field under validation must be a value after a given date. The dates will be passed into the `strtotime` PHP function in order to be converted to a valid `DateTime` instance:

검증 중인 필드는 지정된 날짜 이후의 값이어야 합니다. 날짜는 유효한 `DateTime` 인스턴스로 변환하기 위해 `strtotime` PHP 함수로 전달됩니다.

    'start_date' => 'required|date|after:tomorrow'

Instead of passing a date string to be evaluated by `strtotime`, you may specify another field to compare against the date:

`strtotime`에 의해 계산될 날짜 문자열을 전달하는 대신 날짜와 비교할 다른 필드를 명시할 수 있습니다.

    'finish_date' => 'required|date|after:start_date'

<a name="rule-after-or-equal"></a>
#### after\_or\_equal:_date_
#### after\_or\_equal:_date_

The field under validation must be a value after or equal to the given date. For more information, see the [after](#rule-after) rule.

필드의 값이 주어진 날짜와 동일하거나, 이후여야 합니다. 보다 자세한 사항은 [after](#rule-after) 규칙을 확인하십시오.

<a name="rule-alpha"></a>
#### alpha
#### alpha

The field under validation must be entirely alphabetic characters.

필드의 값이 완벽하게 (숫자나 기호가 아닌) 알파벳[자음과 모음] 문자로 이루어져야 합니다.

(역자주: 영문 알파벳만을 의미하지 않고, 숫자나 기호가 아닌경우에 해당하여, 한글도 허용합니다.)

<a name="rule-alpha-dash"></a>
#### alpha_dash
#### alpha_dash

The field under validation may have alpha-numeric characters, as well as dashes and underscores.

필드의 값이 (숫자나 기호가 아닌) 알파벳[자음과 모음] 문자 및 숫자와 dash(-), underscore(_)로 이루어져야 합니다.

<a name="rule-alpha-num"></a>
#### alpha_num
#### alpha_num

The field under validation must be entirely alpha-numeric characters.

필드의 값이 완벽하게 (숫자나 기호가 아닌) 알파벳[자음과 모음] 문자 및 숫자로 이루어져야 합니다.

<a name="rule-array"></a>
#### array
#### array

The field under validation must be a PHP `array`.

필드의 값이 반드시 PHP `array` 형태이어야 합니다.

When additional values are provided to the `array` rule, each key in the input array must be present within the list of values provided to the rule. In the following example, the `admin` key in the input array is invalid since it is not contained in the list of values provided to the `array` rule:

`array` 규칙에 추가 값이 제공되면 입력 배열의 각 키는 규칙에 제공된 값 목록 내에 있어야 합니다. 다음 예에서 입력 배열의 `admin` 키는 `array` 규칙에 제공된 값 목록에 포함되어 있지 않기 때문에 유효하지 않습니다.

    use Illuminate\Support\Facades\Validator;

    $input = [
        'user' => [
            'name' => 'Taylor Otwell',
            'username' => 'taylorotwell',
            'admin' => true,
        ],
    ];

    Validator::make($input, [
        'user' => 'array:username,locale',
    ]);

In general, you should always specify the array keys that are allowed to be present within your array. Otherwise, the validator's `validate` and `validated` methods will return all of the validated data, including the array and all of its keys, even if those keys were not validated by other nested array validation rules.

일반적으로 배열 내에 존재하도록 허용된 배열 키를 항상 지정해야 합니다. 그렇지 않으면 유효성 검사기의 `validate` 및 `validated` 메서드는 해당 키가 다른 중첩된 배열 유효성 검사 규칙에 의해 유효성이 검사되지 않은 경우에도 배열 및 모든 키를 포함하여 유효성이 검사된 모든 데이터를 반환합니다.

If you would like, you may instruct Laravel's validator to never include unvalidated array keys in the "validated" data it returns, even if you use the `array` rule without specifying a list of allowed keys. To accomplish this, you may call the validator's `excludeUnvalidatedArrayKeys` method in the `boot` method of your application's `AppServiceProvider`. After doing so, the validator will include array keys in the "validated" data it returns only when those keys were specifically validated by [nested array rules](#validating-arrays):

원한다면, 허용된 키 목록을 지정하지 않고 `array` 규칙을 사용하더라도 라라벨의 유효성 검사기가 반환하는 "검증된" 데이터에 검증되지 않은 배열 키를 포함하지 않도록 지시할 수 있습니다. 이를 수행하기 위해 애플리케이션의 `AppServiceProvider`의 `boot` 메소드에서 유효성 검사기의 `excludeUnvalidatedArrayKeys` 메소드를 호출할 수 있습니다. 그렇게 하면 유효성 검사기는 해당 키가 [중첩 배열 규칙](#validating-arrays)에 의해 특별히 검증된 경우에만 반환하는 "검증된" 데이터에 배열 키를 포함합니다.

```php
use Illuminate\Support\Facades\Validator;

/**
 * Register any application services.
 *
 * @return void
 */
public function boot()
{
    Validator::excludeUnvalidatedArrayKeys();
}
```

<a name="rule-bail"></a>
#### bail
#### bail

Stop running validation rules for the field after the first validation failure.

첫 번째 유효성 검사 실패 후 필드에 대한 유효성 검사 규칙 실행을 중지합니다.

While the `bail` rule will only stop validating a specific field when it encounters a validation failure, the `stopOnFirstFailure` method will inform the validator that it should stop validating all attributes once a single validation failure has occurred:

`bail` 규칙은 유효성 검사 실패가 발생한 경우에만 특정 필드의 유효성 검사를 중지하지만, `stopOnFirstFailure` 메서드는 단일 유효성 검사 실패가 발생하면 모든 속성 유효성 검사를 중지해야 한다고 유효성 검사기에 알립니다.

    if ($validator->stopOnFirstFailure()->fails()) {
        // ...
    }

<a name="rule-before"></a>
#### before:_date_
#### before:_date_

The field under validation must be a value preceding the given date. The dates will be passed into the PHP `strtotime` function in order to be converted into a valid `DateTime` instance. In addition, like the [`after`](#rule-after) rule, the name of another field under validation may be supplied as the value of `date`.

검증 중인 필드는 지정된 날짜 이전의 값이어야 합니다. 날짜는 유효한 `DateTime` 인스턴스로 변환하기 위해 PHP `strtotime` 함수를 사용합니다. 또한, [`after`](#rule-after) 규칙과 마찬가지로 유효성 검사 중인 다른 필드의 이름이 `date` 값으로 제공될 수 있습니다.

<a name="rule-before-or-equal"></a>
#### before\_or\_equal:_date_
#### before\_or\_equal:_date_

The field under validation must be a value preceding or equal to the given date. The dates will be passed into the PHP `strtotime` function in order to be converted into a valid `DateTime` instance. In addition, like the [`after`](#rule-after) rule, the name of another field under validation may be supplied as the value of `date`.

유효성 검사 중인 필드는 지정된 날짜 이전 또는 같은 값이어야 합니다. 날짜는 유효한 `DateTime` 인스턴스로 변환하기 위해 PHP `strtotime` 함수를 사용합니다. 또한, [`after`](#rule-after) 규칙과 마찬가지로 유효성 검사 중인 다른 필드의 이름이 `date` 값으로 제공될 수 있습니다.

<a name="rule-between"></a>
#### between:_min_,_max_
#### between:_min_,_max_

The field under validation must have a size between the given _min_ and _max_. Strings, numerics, arrays, and files are evaluated in the same fashion as the [`size`](#rule-size) rule.

필드의 값이, 주어진 _min_ 과 _max_ 의 사이의 값이어야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 규칙에 의해 같은 방식으로 계산될 수 있습니다.

<a name="rule-boolean"></a>
#### boolean
#### boolean

The field under validation must be able to be cast as a boolean. Accepted input are `true`, `false`, `1`, `0`, `"1"`, and `"0"`.

필드의 값이 반드시 boolean으로 캐스팅될 수 있어야 합니다. 허용되는 값은 `true`, `false`, `1`, `0`, `"1"`, `"0"` 입니다.

<a name="rule-confirmed"></a>
#### confirmed
#### confirmed

The field under validation must have a matching field of `{field}_confirmation`. For example, if the field under validation is `password`, a matching `password_confirmation` field must be present in the input.

검증 중인 필드에는 `{field}_confirmation`과 일치하는 필드가 있어야 합니다. 예를 들어 검증 중인 필드가 `password`인 경우 일치하는 `password_confirmation` 필드가 입력에 있어야 합니다.

<a name="rule-current-password"></a>
#### current_password
#### current_password

The field under validation must match the authenticated user's password. You may specify an [authentication guard](/docs/{{version}}/authentication) using the rule's first parameter:

유효성 검사 필드는 인증된 사용자의 비밀번호와 일치해야 합니다. 규칙의 첫 번째 매개변수를 사용하여 [authentication guard](/docs/{{version}}/authentication)를 지정할 수 있습니다.

    'password' => 'current_password:api'

<a name="rule-date"></a>
#### date
#### date

The field under validation must be a valid, non-relative date according to the `strtotime` PHP function.

유효성 검사중인 필드는 `strtotime` PHP 함수에 따라 유효한 비 상대(non-relative) 날짜 여야합니다.

<a name="rule-date-equals"></a>
#### date_equals:_date_
#### date_equals:_date_

The field under validation must be equal to the given date. The dates will be passed into the PHP `strtotime` function in order to be converted into a valid `DateTime` instance.

검증 중인 필드는 지정된 날짜와 같아야 합니다. 날짜는 유효한 `DateTime` 인스턴스로 변환하기 위해 PHP `strtotime` 함수를 사용합니다.

<a name="rule-date-format"></a>
#### date_format:_format_
#### date_format:_format_

The field under validation must match the given _format_. You should use **either** `date` or `date_format` when validating a field, not both. This validation rule supports all formats supported by PHP's [DateTime](https://www.php.net/manual/en/class.datetime.php) class.

검증 중인 필드는 지정된 _format_ 과 일치해야 합니다. 필드의 유효성을 검사할 때 `date` 또는 `date_format` 중 **하나만** 사용해야 합니다. 이 유효성 검사 규칙은 PHP의 [DateTime](https://www.php.net/manual/en/class.datetime.php) 클래스에서 지원하는 모든 형식을 지원합니다.

<a name="rule-declined"></a>
#### declined
#### declined

The field under validation must be `"no"`, `"off"`, `0`, or `false`.

검증 중인 필드는 `"no"`, `"off"`, `0` 또는 `false`여야 합니다.

<a name="rule-declined-if"></a>
#### declined_if:anotherfield,value,...
#### declined_if:anotherfield,value,...

The field under validation must be `"no"`, `"off"`, `0`, or `false` if another field under validation is equal to a specified value.

검증 중인 다른 필드가 지정된 값과 같은 경우 검증 중인 필드는 `"no"`, `"off"`, `0` 또는 `false`여야 합니다.

<a name="rule-different"></a>
#### different:_field_
#### different:_field_

The field under validation must have a different value than _field_.

필드의 값이 주어진 _field_ 의 값과 달라야 합니다.

<a name="rule-digits"></a>
#### digits:_value_
#### digits:_value_

The field under validation must be _numeric_ and must have an exact length of _value_.

필드의 값이 반드시 _숫자_ 여야 하고, 길이가 _value_ 이어야 합니다.

<a name="rule-digits-between"></a>
#### digits_between:_min_,_max_
#### digits_between:_min_,_max_

The field under validation must be _numeric_ and must have a length between the given _min_ and _max_.

필드의 값이 반드시 _숫자_ 여야 하고, 주어진 _min_ 과 _max_ 사이의 길이를 가져야 합니다.

<a name="rule-dimensions"></a>
#### dimensions
#### dimensions

The file under validation must be an image meeting the dimension constraints as specified by the rule's parameters:

필드의 값이 규칙에 지정된 파라미터들을 만족하는 이미지이어야 합니다.

    'avatar' => 'dimensions:min_width=100,min_height=200'

Available constraints are: _min\_width_, _max\_width_, _min\_height_, _max\_height_, _width_, _height_, _ratio_.

사용가능한 제약은:  _min\_width_, _max\_width_, _min\_height_, _max\_height_, _width_, _height_, _ratio_ 입니다.

A _ratio_ constraint should be represented as width divided by height. This can be specified either by a fraction like `3/2` or a float like `1.5`:

_ratio_ 제약 조건은 너비를 높이로 나눈 값으로 표시해야 합니다. 이것은 `3/2`와 같은 분수 또는 `1.5`와 같은 부동 소수점으로 지정할 수 있습니다.

    'avatar' => 'dimensions:ratio=3/2'

Since this rule requires several arguments, you may use the `Rule::dimensions` method to fluently construct the rule:

이 규칙은 여러개의 인자를 필요로 하는데, 다음처럼 `Rule:dimensions` 메소드를 사용하여 유연하게 규칙을 생성할 수 있습니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'avatar' => [
            'required',
            Rule::dimensions()->maxWidth(1000)->maxHeight(500)->ratio(3 / 2),
        ],
    ]);

<a name="rule-distinct"></a>
#### distinct
#### distinct

When validating arrays, the field under validation must not have any duplicate values:

배열을 검증할 때 검증 중인 필드에는 중복 값이 없어야 합니다.

    'foo.*.id' => 'distinct'

Distinct uses loose variable comparisons by default. To use strict comparisons, you may add the `strict` parameter to your validation rule definition:

Distinct는 기본적으로 느슨한 변수 비교를 사용합니다. 엄격한 비교를 사용하려면 유효성 검사 규칙 정의에 `strict` 매개변수를 추가할 수 있습니다.

    'foo.*.id' => 'distinct:strict'

You may add `ignore_case` to the validation rule's arguments to make the rule ignore capitalization differences:

유효성 검사 규칙의 인수에 `ignore_case`를 추가하여 규칙이 대소문자 차이를 무시하도록 할 수 있습니다.

    'foo.*.id' => 'distinct:ignore_case'

<a name="rule-email"></a>
#### email
#### email

The field under validation must be formatted as an email address. This validation rule utilizes the [`egulias/email-validator`](https://github.com/egulias/EmailValidator) package for validating the email address. By default, the `RFCValidation` validator is applied, but you can apply other validation styles as well:

검증 중인 필드는 이메일 주소 형식이어야 합니다. 이 검증 규칙은 이메일 주소 검증을 위해 [`egulias/email-validator`](https://github.com/egulias/EmailValidator) 패키지를 사용합니다. 기본적으로 `RFCValidation` 유효성 검사기가 적용되지만 다른 유효성 검사 스타일도 적용할 수 있습니다.

    'email' => 'email:rfc,dns'

The example above will apply the `RFCValidation` and `DNSCheckValidation` validations. Here's a full list of validation styles you can apply:

위의 예시는 `RFCValidation`와 `DNSCheckValidation` 유효성 검사를 적용합니다. 가능한 모든 유효성 검사 방법은 아래를 참조하십시오.

- `rfc`: `RFCValidation`
- `strict`: `NoRFCWarningsValidation`
- `dns`: `DNSCheckValidation`
- `spoof`: `SpoofCheckValidation`
- `filter`: `FilterEmailValidation`

The `filter` validator, which uses PHP's `filter_var` function, ships with Laravel and was Laravel's default email validation behavior prior to Laravel version 5.8.

PHP의 `filter_var` 기능을 사용하는 `filter` 유효성 검사기는 라라벨과 함께 제공되며 라라벨 버전 5.8 이전에 라라벨의 기본 이메일 유효성 검사 동작이었습니다.

> {note} The `dns` and `spoof` validators require the PHP `intl` extension.

> {note} `dns` 및 `spoof` 유효성 검사기는 PHP `intl` 확장이 필요합니다.

<a name="rule-ends-with"></a>
#### ends_with:_foo_,_bar_,...
#### ends_with:_foo_,_bar_,...

The field under validation must end with one of the given values.

필드의 값이 주어진 값 중 하나로 끝나야합니다.

<a name="rule-enum"></a>
#### enum
#### enum

The `Enum` rule is a class based rule that validates whether the field under validation contains a valid enum value. The `Enum` rule accepts the name of the enum as its only constructor argument:

`Enum` 규칙은 유효성 검사 중인 필드에 유효한 열거형 값이 포함되어 있는지 확인하는 클래스 기반 규칙입니다. `Enum` 규칙은 enum의 이름을 유일한 생성자 인수로 허용합니다.

    use App\Enums\ServerStatus;
    use Illuminate\Validation\Rules\Enum;

    $request->validate([
        'status' => [new Enum(ServerStatus::class)],
    ]);

> {note} Enums are only available on PHP 8.1+.

> {note} enum은 PHP 8.1 이상에서만 사용할 수 있습니다.

<a name="rule-exclude"></a>
#### exclude
#### exclude

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods.

유효성 검사 중인 필드는 `validate` 및 `validated` 메서드에서 반환된 요청 데이터에서 제외됩니다.

<a name="rule-exclude-if"></a>
#### exclude_if:_anotherfield_,_value_
#### exclude_if:_anotherfield_,_value_

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods if the _anotherfield_ field is equal to _value_.

_anotherfield_ 필드가 _value_ 와 같은 경우 유효성 검증 대상 필드는 `validate` 및 `validated` 메소드가 리턴 한 request 데이터에서 제외됩니다.

<a name="rule-exclude-unless"></a>
#### exclude_unless:_anotherfield_,_value_
#### exclude_unless:_anotherfield_,_value_

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods unless _anotherfield_'s field is equal to _value_. If _value_ is `null` (`exclude_unless:name,null`), the field under validation will be excluded unless the comparison field is `null` or the comparison field is missing from the request data.

유효성 검사 중인 필드는 _anotherfield_ 의 필드가 _value_ 와 같지 않은 한 `validate` 및 `validated` 메서드가 반환하는 요청 데이터에서 제외됩니다. _value_ 가 `null`(`exclude_unless:name,null`)이면 비교 필드가 `null`이 아니거나 요청 데이터에서 비교 필드가 누락되지 않는 한 유효성 검사 중인 필드가 제외됩니다.

<a name="rule-exclude-without"></a>
#### exclude_without:_anotherfield_
#### exclude_without:_anotherfield_

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods if the _anotherfield_ field is not present.

유효성 검사 중인 필드는 _anotherfield_ 필드가 없으면 `validate` 및 `validated` 메서드가 반환하는 요청 데이터에서 제외됩니다.

<a name="rule-exists"></a>
#### exists:_table_,_column_
#### exists:_table_,_column_

The field under validation must exist in a given database table.

검증 중인 필드는 지정된 데이터베이스 테이블에 있어야 합니다.

<a name="basic-usage-of-exists-rule"></a>
#### Basic Usage Of Exists Rule
#### Basic Usage Of Exists Rule

    'state' => 'exists:states'

If the `column` option is not specified, the field name will be used. So, in this case, the rule will validate that the `states` database table contains a record with a `state` column value matching the request's `state` attribute value.

`column` 옵션을 지정하지 않으면 필드 이름이 사용됩니다. 따라서 이 경우 규칙은 `states` 데이터베이스 테이블에 요청의 `state` 속성 값과 일치하는 `state` 열 값이 있는 레코드가 포함되어 있는지 확인합니다.

<a name="specifying-a-custom-column-name"></a>
#### Specifying A Custom Column Name
#### Specifying A Custom Column Name

You may explicitly specify the database column name that should be used by the validation rule by placing it after the database table name:

데이터베이스 테이블 이름 뒤에 배치하여 유효성 검사 규칙에서 사용해야 하는 데이터베이스 열 이름을 명시적으로 지정할 수 있습니다.

    'state' => 'exists:states,abbreviation'

Occasionally, you may need to specify a specific database connection to be used for the `exists` query. You can accomplish this by prepending the connection name to the table name:

경우에 따라 `exists` 쿼리에 사용할 특정 데이터베이스 연결을 지정해야 할 수도 있습니다. 테이블 이름 앞에 연결 이름을 추가하여 이를 수행할 수 있습니다.

    'email' => 'exists:connection.staff,email'

Instead of specifying the table name directly, you may specify the Eloquent model which should be used to determine the table name:

테이블 이름을 직접 지정하는 대신 테이블 이름을 결정하는 데 사용해야하는 Eloquent 모델을 지정할 수 있습니다.

    'user_id' => 'exists:App\Models\User,id'

If you would like to customize the query executed by the validation rule, you may use the `Rule` class to fluently define the rule. In this example, we'll also specify the validation rules as an array instead of using the `|` character to delimit them:

유효성 검사 규칙에 의해서 실행되는 쿼리를 커스터마이징 하고자 한다면, 규칙에 `Rule` 클래스를 정의해서 사용할 수 있습니다. 다음 예제에서 `|` 문자를 구분자로 사용하는 대신에 유효성 검사 규칙을 배열로 지정하고 있습니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'email' => [
            'required',
            Rule::exists('staff')->where(function ($query) {
                return $query->where('account_id', 1);
            }),
        ],
    ]);

<a name="rule-file"></a>
#### file
#### file

The field under validation must be a successfully uploaded file.

필드의 값이 완전히 업로드된 파일이어야 합니다.

<a name="rule-filled"></a>
#### filled
#### filled

The field under validation must not be empty when it is present.

필드가 존재하는 경우 값이 비어있으면 안됩니다.

<a name="rule-gt"></a>
#### gt:_field_
#### gt:_field_

The field under validation must be greater than the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

필드의 값이 주어진 다른 필드의 값보다 커야합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 계산됩니다.

<a name="rule-gte"></a>
#### gte:_field_
#### gte:_field_

The field under validation must be greater than or equal to the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

필드의 값이 주어진 다른 필드의 값보다 크거나 같아야합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 계산됩니다.

<a name="rule-image"></a>
#### image
#### image

The file under validation must be an image (jpg, jpeg, png, bmp, gif, svg, or webp).

검증 중인 파일은 이미지(jpg, jpeg, png, bmp, gif, svg 또는 webp)여야 합니다.

<a name="rule-in"></a>
#### in:_foo_,_bar_,...
#### in:_foo_,_bar_,...

The field under validation must be included in the given list of values. Since this rule often requires you to `implode` an array, the `Rule::in` method may be used to fluently construct the rule:

필드의 값이 주어진 목록에 포함돼 있어야 합니다. 이 규칙은 자주 배열을 `implode` 하는 것을 필요로 하며, 다음처럼 `Rule:in` 메소드를 사용하여 편리하게 생성할 수 있습니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'zones' => [
            'required',
            Rule::in(['first-zone', 'second-zone']),
        ],
    ]);

When the `in` rule is combined with the `array` rule, each value in the input array must be present within the list of values provided to the `in` rule. In the following example, the `LAS` airport code in the input array is invalid since it is not contained in the list of airports provided to the `in` rule:

`in` 규칙이 `array` 규칙과 결합되면 입력 배열의 각 값이 `in` 규칙에 제공된 값 목록 내에 있어야 합니다. 다음 예에서 입력 배열의 `LAS` 공항 코드는 `in` 규칙에 제공된 공항 목록에 포함되어 있지 않기 때문에 유효하지 않습니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    $input = [
        'airports' => ['NYC', 'LAS'],
    ];

    Validator::make($input, [
        'airports' => [
            'required',
            'array',
            Rule::in(['NYC', 'LIT']),
        ],
    ]);

<a name="rule-in-array"></a>
#### in_array:_anotherfield_.*
#### in_array:_anotherfield_.*

The field under validation must exist in _anotherfield_'s values.

필드의 값이 주어진 _다른 필드_ 의 값안에 존재해야만 합니다.

<a name="rule-integer"></a>
#### integer
#### integer

The field under validation must be an integer.

필드의 값이 정수여야 합니다.

> {note} This validation rule does not verify that the input is of the "integer" variable type, only that the input is of a type accepted by PHP's `FILTER_VALIDATE_INT` rule. If you need to validate the input as being a number please use this rule in combination with [the `numeric` validation rule](#rule-numeric).

> {note} 이 유효성 검사 규칙은 입력이 "정수" 변수 유형인지 확인하지 않고 입력이 PHP의 `FILTER_VALIDATE_INT` 규칙에 의해 허용되는 유형인지만 확인합니다. 입력이 숫자인지 확인해야 하는 경우 ['숫자' 유효성 검사 규칙](#rule-numeric)과 함께 이 규칙을 사용하세요.

<a name="rule-ip"></a>
#### ip
#### ip

The field under validation must be an IP address.

필드의 값이 IP 주소여야 합니다.

<a name="ipv4"></a>
#### ipv4
#### ipv4

The field under validation must be an IPv4 address.

필드의 값이 IPv4 주소여야 합니다.

<a name="ipv6"></a>
#### ipv6
#### ipv6

The field under validation must be an IPv6 address.

필드의 값이 IPv6 주소여야 합니다.

<a name="rule-mac"></a>
#### mac_address
#### mac_address

The field under validation must be a MAC address.

검증 중인 필드는 MAC 주소여야 합니다.

<a name="rule-json"></a>
#### json
#### json

The field under validation must be a valid JSON string.

필드의 값이 유효한 JSON 문자열이어야 합니다.

<a name="rule-lt"></a>
#### lt:_field_
#### lt:_field_

The field under validation must be less than the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

필드의 값이 주어진 다른 필드의 값보다 작아야 합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 계산됩니다.

<a name="rule-lte"></a>
#### lte:_field_
#### lte:_field_

The field under validation must be less than or equal to the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

필드의 값이 주어진 다른 필드의 값보다 적거나 같아야 합니다. 두개의 필드는 동일한 타입이어야 하며, 문자열, 숫자형, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 계산됩니다.

<a name="rule-max"></a>
#### max:_value_
#### max:_value_

The field under validation must be less than or equal to a maximum _value_. Strings, numerics, arrays, and files are evaluated in the same fashion as the [`size`](#rule-size) rule.

필드의 값이 반드시 _value_ 보다 작거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 규칙에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-mimetypes"></a>
#### mimetypes:_text/plain_,...
#### mimetypes:_text/plain_,...

The file under validation must match one of the given MIME types:

파일이 주어진 MIME 타입들 중 하나와 일치해야만 합니다.

    'video' => 'mimetypes:video/avi,video/mpeg,video/quicktime'

To determine the MIME type of the uploaded file, the file's contents will be read and the framework will attempt to guess the MIME type, which may be different from the client's provided MIME type.

업로드된 파일의 MIME 유형을 결정하기 위해, 파일의 내용을 읽고 프레임워크는 클라이언트가 제공한 MIME 유형과 다를 수 있는 MIME 유형을 추측하려고 시도합니다.

<a name="rule-mimes"></a>
#### mimes:_foo_,_bar_,...
#### mimes:_foo_,_bar_,...

The file under validation must have a MIME type corresponding to one of the listed extensions.

파일의 MIME 타입이 주어진 확장자 리스트 중에 하나와 일치해야 합니다.

<a name="basic-usage-of-mime-rule"></a>
#### Basic Usage Of MIME Rule
#### MIME 규칙의 기본 사용법

    'photo' => 'mimes:jpg,bmp,png'

Even though you only need to specify the extensions, this rule actually validates the MIME type of the file by reading the file's contents and guessing its MIME type. A full listing of MIME types and their corresponding extensions may be found at the following location:

확장자만 지정하면 되지만, 이 규칙은 실제로 파일 내용을 읽고 MIME 유형을 추측하여 파일의 MIME 유형을 확인합니다. MIME 유형 및 해당 확장의 전체 목록은 다음 위치에서 찾을 수 있습니다.

[https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

[https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

<a name="rule-min"></a>
#### min:_value_
#### min:_value_

The field under validation must have a minimum _value_. Strings, numerics, arrays, and files are evaluated in the same fashion as the [`size`](#rule-size) rule.

필드의 값이 반드시 _value_ 보다 크거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 규칙에 의해 같은 방식으로 평가될 수 있습니다.

<a name="multiple-of"></a>
#### multiple_of:_value_
#### multiple_of:_value_

The field under validation must be a multiple of _value_.

검증 중인 필드는 _value_ 의 배수여야 합니다.

> {note} The [`bcmath` PHP extension](https://www.php.net/manual/en/book.bc.php) is required in order to use the `multiple_of` rule.

> {참고} `multiple_of` 규칙을 사용하려면 [`bcmath` PHP 확장 프로그램](https://www.php.net/manual/en/book.bc.php) 이 필요합니다.

<a name="rule-not-in"></a>
#### not_in:_foo_,_bar_,...
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
#### not_regex:_pattern_

The field under validation must not match the given regular expression.

필드의 값이 주어진 정규표현식과 매칭되지 않는 것을 확인합니다.

Internally, this rule uses the PHP `preg_match` function. The pattern specified should obey the same formatting required by `preg_match` and thus also include valid delimiters. For example: `'email' => 'not_regex:/^.+$/i'`.

이 규칙은 내부적으로 PHP의 `preg_match` 함수를 사용합니다. 지정된 패턴은 `preg_match` 에 필요한 것과 동일한 양식을 따라야하며 유효한 구분 기호를 포함해야합니다. 예를 들면 다음과 같습니다. `'email' => 'not_regex:/^.+@.+$/i'`.

> {note} When using the `regex` / `not_regex` patterns, it may be necessary to specify your validation rules using an array instead of using `|` delimiters, especially if the regular expression contains a `|` character.

> {note} `regex` / `not_regex` 패턴을 사용할 때, 특히 정규 표현식에 `|` 문자가 포함된 경우 `|` 구분 기호를 사용하는 대신 배열을 사용하여 유효성 검사 규칙을 지정해야 할 수도 있습니다.

<a name="rule-nullable"></a>
#### nullable
#### nullable

The field under validation may be `null`.

검증 중인 필드는 `null`일 수 있습니다.

<a name="rule-numeric"></a>
#### numeric
#### numeric

The field under validation must be [numeric](https://www.php.net/manual/en/function.is-numeric.php).

유효성 검사 필드는 [숫자](https://www.php.net/manual/en/function.is-numeric.php) 여야 합니다.

<a name="rule-password"></a>
#### password
#### password

The field under validation must match the authenticated user's password.

유효성 검사 필드는 인증된 사용자의 비밀번호와 일치해야 합니다.

> {note} This rule was renamed to `current_password` with the intention of removing it in Laravel 9. Please use the [Current Password](#rule-current-password) rule instead.

> {note} 이 규칙은 라라벨 9에서 제거하기 위해 `current_password`로 이름이 변경되었습니다. 대신 [Current Password](#rule-current-password) 규칙을 사용하세요.

<a name="rule-present"></a>
#### present
#### present

The field under validation must be present in the input data but can be empty.

필드가 존재하고 있는지 확인하지만, 값이 비어있을 수 있습니다.

<a name="rule-prohibited"></a>
#### prohibited
#### prohibited

The field under validation must be empty or not present.

검증 중인 필드는 비어 있거나 존재하지 않아야 합니다.

<a name="rule-prohibited-if"></a>
#### prohibited_if:_anotherfield_,_value_,...
#### prohibited_if:_anotherfield_,_value_,...

The field under validation must be empty or not present if the _anotherfield_ field is equal to any _value_.

_anotherfield_ 필드가 _value_ 와 동일한 경우 유효성 검사 중인 필드는 비어 있거나 존재하지 않아야 합니다.

<a name="rule-prohibited-unless"></a>
#### prohibited_unless:_anotherfield_,_value_,...
#### prohibited_unless:_anotherfield_,_value_,...

The field under validation must be empty or not present unless the _anotherfield_ field is equal to any _value_.

유효성 검사 중인 필드는 _anotherfield_ 필드가 _value_ 와 같지 않으면 비어 있거나 존재하지 않아야 합니다.

<a name="rule-prohibits"></a>
#### prohibits:_anotherfield_,...
#### prohibits:_anotherfield_,...

If the field under validation is present, no fields in _anotherfield_ can be present, even if empty.

유효성 검사 중인 필드가 있는 경우 비어 있더라도 _anotherfield_ 에 필드가 있을 수 없습니다.

<a name="rule-regex"></a>
#### regex:_pattern_
#### regex:_pattern_

The field under validation must match the given regular expression.

필드의 값이 주어진 정규식 표현과 일치해야 합니다.

Internally, this rule uses the PHP `preg_match` function. The pattern specified should obey the same formatting required by `preg_match` and thus also include valid delimiters. For example: `'email' => 'regex:/^.+@.+$/i'`.

이 규칙은 내부적으로 PHP의 `preg_match` 함수를 사용합니다. 지정된 패턴은 `preg_match` 에 필요한 것과 동일한 양식을 따라야하며 유효한 구분 기호를 포함해야합니다. 예를 들면 다음과 같습니다. `'email' => 'regex:/^.+@.+$/i'`.

> {note} When using the `regex` / `not_regex` patterns, it may be necessary to specify rules in an array instead of using `|` delimiters, especially if the regular expression contains a `|` character.

> {note} `regex` / `not_regex` 패턴을 사용할 때, 특히 정규 표현식에 `|` 문자가 포함된 경우 `|` 구분 기호를 사용하는 대신 배열에 규칙을 지정해야 할 수도 있습니다.

<a name="rule-required"></a>
#### required
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
#### required_if:_anotherfield_,_value_,...

The field under validation must be present and not empty if the _anotherfield_ field is equal to any _value_.

만약 _anotherfield_ 의 값이 _value_ 중의 하나와 일치한다면, 해당 필드는 존재하고 비어있지 않아야 합니다.

If you would like to construct a more complex condition for the `required_if` rule, you may use the `Rule::requiredIf` method. This method accepts a boolean or a closure. When passed a closure, the closure should return `true` or `false` to indicate if the field under validation is required:

`required_if` 규칙에 대해 더 복잡한 조건을 구성하려면 `Rule::requiredIf` 메서드를 사용할 수 있습니다. 이 메서드는 boolean 또는 클로저를 입력받습니다. 클로저가 전달되면 클로저는 `true` 또는 `false`를 반환하여 유효성 검사 중인 필드가 필요한지 여부를 나타냅니다.

    use Illuminate\Support\Facades\Validator;
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
#### required_unless:_anotherfield_,_value_,...

The field under validation must be present and not empty unless the _anotherfield_ field is equal to any _value_. This also means _anotherfield_ must be present in the request data unless _value_ is `null`. If _value_ is `null` (`required_unless:name,null`), the field under validation will be required unless the comparison field is `null` or the comparison field is missing from the request data.

검증 중인 필드는 _anotherfield_ 필드가 _value_ 와 동일하지 않은 경우 존재해야 하며 비어 있지 않아야 합니다. 이것은 또한 _value_ 가 `null`이 아닌 한 _anotherfield_ 가 요청 데이터에 있어야 함을 의미합니다. _value_ 가 `null`(`required_unless:name,null`)인 경우 비교 필드가 `null`이거나 요청 데이터에서 비교 필드가 누락되지 않는 한 유효성 검사 중인 필드가 필요합니다.

<a name="rule-required-with"></a>
#### required_with:_foo_,_bar_,...
#### required_with:_foo_,_bar_,...

The field under validation must be present and not empty _only if_ any of the other specified fields are present and not empty.

지정된 다른 필드중 하나라도 선언이 되어있고 값이 비어있지 _않다면_, 해당 필드가 반드시 존재하고 비어있지 않아야 합니다.

<a name="rule-required-with-all"></a>
#### required_with_all:_foo_,_bar_,...
#### required_with_all:_foo_,_bar_,...

The field under validation must be present and not empty _only if_ all of the other specified fields are present and not empty.

지정된 다른 필드가 모두 선언이 되어있고 값이 비어있지 _않다면_, 해당 필드가 반드시 존재하고 비어있지 않아야 합니다.

<a name="rule-required-without"></a>
#### required_without:_foo_,_bar_,...
#### required_without:_foo_,_bar_,...

The field under validation must be present and not empty _only when_ any of the other specified fields are empty or not present.

지정된 다른 필드중 하나라도 선언이 되어있지않거나 값이 비어 _있다면_, 해당 필드가 반드시 존재하고 비어있지 않아야 합니다.

<a name="rule-required-without-all"></a>
#### required_without_all:_foo_,_bar_,...
#### required_without_all:_foo_,_bar_,...

The field under validation must be present and not empty _only when_ all of the other specified fields are empty or not present.

지정된 다른 필드가 모두 선언이 되어있지않거나 값이 비어 _있다면_, 해당 필드가 반드시 존재하고 비어있지 않아야 합니다.

<a name="rule-same"></a>
#### same:_field_
#### same:_field_

The given _field_ must match the field under validation.

필드의 값이 주어진 _field_ 의 값과 일치해야 합니다.

<a name="rule-size"></a>
#### size:_value_
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
#### starts_with:_foo_,_bar_,...

The field under validation must start with one of the given values.

유효성 검사중인 필드는 주어진 값 중 하나로 시작해야합니다.

<a name="rule-string"></a>
#### string
#### string

The field under validation must be a string. If you would like to allow the field to also be `null`, you should assign the `nullable` rule to the field.

필드의 값이 반드시 문자열이어야 합니다. 필드가 `null` 인것을 허용하려면 규칙에 `nullable` 을 할당해야만 합니다.

<a name="rule-timezone"></a>
#### timezone
#### timezone

The field under validation must be a valid timezone identifier according to the `timezone_identifiers_list` PHP function.

필드의 값이 `timezone_identifiers_list` PHP 함수에서 인식 가능한 유효한 timezone 식별자여야 합니다.

<a name="rule-unique"></a>
#### unique:_table_,_column_
#### unique:_table_,_column_

The field under validation must not exist within the given database table.

필드의 값이 주어진 데이터베이스 테이블에서 고유한 값이어야 합니다. 만약 `column`이 지정돼 있지 않다면 필드의 이름이 사용됩니다.

**Specifying A Custom Table / Column Name:**
**특정 컬럼명 지정하기:**

Instead of specifying the table name directly, you may specify the Eloquent model which should be used to determine the table name:

테이블 이름을 직접 지정하는 대신 테이블 이름을 결정하는 데 사용해야하는 Eloquent 모델을 지정할 수 있습니다.

    'email' => 'unique:App\Models\User,email_address'

The `column` option may be used to specify the field's corresponding database column. If the `column` option is not specified, the name of the field under validation will be used.

`column` 옵션은 필드의 해당 데이터베이스 열을 지정하는 데 사용할 수 있습니다. `column` 옵션이 지정되지 않은 경우 유효성 검사 중인 필드의 이름이 사용됩니다.

    'email' => 'unique:users,email_address'

**Specifying A Custom Database Connection**
**사용자 지정 데이터베이스 연결 지정**

Occasionally, you may need to set a custom connection for database queries made by the Validator. To accomplish this, you may prepend the connection name to the table name:

경우에 따라 Validator에서 만든 데이터베이스 쿼리에 대한 사용자 지정 연결을 설정해야 할 수도 있습니다. 이를 위해 테이블 이름 앞에 연결 이름을 추가할 수 있습니다.

    'email' => 'unique:connection.users,email_address'

**Forcing A Unique Rule To Ignore A Given ID:**
**주어진 ID에 대해서 유니크 규칙을 무시하도록 강제하기:**

Sometimes, you may wish to ignore a given ID during unique validation. For example, consider an "update profile" screen that includes the user's name, email address, and location. You will probably want to verify that the email address is unique. However, if the user only changes the name field and not the email field, you do not want a validation error to be thrown because the user is already the owner of the email address in question.

경우에 따라 고유한 유효성 검사 중에 지정된 ID를 무시하고 싶을 수 있습니다. 예를 들어 사용자 이름, 이메일 주소 및 위치가 포함된 "프로필 업데이트" 화면을 생각해보세요. 이메일 주소가 고유한지 확인하고 싶을 것입니다. 그러나 사용자가 이메일 필드가 아닌 이름 필드만 변경하는 경우 해당 사용자가 이미 해당 이메일 주소의 소유자이기 때문에 유효성 검사 오류가 발생하는 것을 원하지 않습니다.

To instruct the validator to ignore the user's ID, we'll use the `Rule` class to fluently define the rule. In this example, we'll also specify the validation rules as an array instead of using the `|` character to delimit the rules:

사용자 ID를 무시하도록 지시하려면, 규칙을 유연하게 정의할 수 있는 `Rule` 클래스를 사용하면 됩니다. 다음 예제에서 규칙을 `|` 문자를 구분자로 사용하는 대신에 유효성 검사 규칙을 배열로 지정하고 있습니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'email' => [
            'required',
            Rule::unique('users')->ignore($user->id),
        ],
    ]);

> {note} You should never pass any user controlled request input into the `ignore` method. Instead, you should only pass a system generated unique ID such as an auto-incrementing ID or UUID from an Eloquent model instance. Otherwise, your application will be vulnerable to an SQL injection attack.

> {note} `ignore` 메소드에 유저가 전달한 리퀘스트 입력값을 절대 입력해서는 안 됩니다. 대신, 엘로퀀트 모델 인스턴스의 자동 증가 ID나 UUID 등 시스템이 생성한 고유 ID를 전달하십시오. 그렇지 않으면 어플리케이션이 SQL 인젝션 공격에 취약하게 됩니다.

Instead of passing the model key's value to the `ignore` method, you may also pass the entire model instance. Laravel will automatically extract the key from the model:

`ignore` 메소드에 모델 키 값을 전달하는 대신 전체 모델 인스턴스를 전달할 수도 있습니다. 라라벨은 자동으로 모델에서 키를 추출합니다.

    Rule::unique('users')->ignore($user)

If your table uses a primary key column name other than `id`, you may specify the name of the column when calling the `ignore` method:

테이블이 `id`가 아닌 primary 키 컬럼 이름을 사용한다면, `ignore` 메소드를 호출할 때 컬럼의 이름을 지정하면 됩니다.

    Rule::unique('users')->ignore($user->id, 'user_id')

By default, the `unique` rule will check the uniqueness of the column matching the name of the attribute being validated. However, you may pass a different column name as the second argument to the `unique` method:

기본적으로 `unique`규칙은 유효성 검사중인 속성의 이름과 일치하는 열의 고유성을 검사합니다. 그러나 `unique` 메소드의 두 번째 인자에 다른 칼럼의 이름을 전달할 수 있습니다.

    Rule::unique('users', 'email_address')->ignore($user->id),

**Adding Additional Where Clauses:**
**추가적인 Where 구문 추가하기:**

You may specify additional query conditions by customizing the query using the `where` method. For example, let's add a query condition that scopes the query to only search records that have an `account_id` column value of `1`:

`where` 메서드를 사용하여 쿼리를 사용자 지정하여 추가 쿼리 조건을 지정할 수 있습니다. 예를 들어 `account_id` 열 값이 `1`인 레코드만 검색하도록 쿼리 범위를 지정하는 쿼리 조건을 추가해 보겠습니다.

    'email' => Rule::unique('users')->where(function ($query) {
        return $query->where('account_id', 1);
    })

<a name="rule-url"></a>
#### url
#### url

The field under validation must be a valid URL.

필드는 반드시 유효한 URL이어야 합니다.

<a name="rule-uuid"></a>
#### uuid
#### uuid

The field under validation must be a valid RFC 4122 (version 1, 3, 4, or 5) universally unique identifier (UUID).

유효성 검사중인 필드는 유효한 RFC 4122 (버전 1, 3, 4 또는 5) 범용 고유 식별자 (UUID) 여야합니다.

<a name="conditionally-adding-rules"></a>
## Conditionally Adding Rules
## Conditionally Adding Rules

<a name="skipping-validation-when-fields-have-certain-values"></a>
#### Skipping Validation When Fields Have Certain Values
#### Skipping Validation When Fields Have Certain Values

You may occasionally wish to not validate a given field if another field has a given value. You may accomplish this using the `exclude_if` validation rule. In this example, the `appointment_date` and `doctor_name` fields will not be validated if the `has_appointment` field has a value of `false`:

때때로 다른 필드에 지정된 값이 있는 경우 주어진 필드의 유효성을 검사를 원하지 않는 경우가 있습니다. `exclude_if` 검증 규칙을 사용하여 이를 수행 할 수 있습니다. 이 예에서 `has_appointment` 필드의 값이 `false`이면 `appointment_date` 및 `doctor_name` 필드의 유효성은 검사되지 않습니다.

    use Illuminate\Support\Facades\Validator;

    $validator = Validator::make($data, [
        'has_appointment' => 'required|boolean',
        'appointment_date' => 'exclude_if:has_appointment,false|required|date',
        'doctor_name' => 'exclude_if:has_appointment,false|required|string',
    ]);

Alternatively, you may use the `exclude_unless` rule to not validate a given field unless another field has a given value:

또는 다른 필드에 지정된 값이 없으면 `exclude_unless` 규칙을 사용하여 지정된 필드의 유효성을 검사하지 않을 수 있습니다.

    $validator = Validator::make($data, [
        'has_appointment' => 'required|boolean',
        'appointment_date' => 'exclude_unless:has_appointment,true|required|date',
        'doctor_name' => 'exclude_unless:has_appointment,true|required|string',
    ]);

<a name="validating-when-present"></a>
#### Validating When Present
#### Validating When Present

In some situations, you may wish to run validation checks against a field **only** if that field is present in the data being validated. To quickly accomplish this, add the `sometimes` rule to your rule list:

경우에 따라 해당 필드가 유효성 검사 중인 데이터에 있는 경우 **만** 해당 필드에 대해 유효성 검사를 실행할 수 있습니다. 이를 빠르게 수행하려면 `sometimes` 규칙을 규칙 목록에 추가하세요.

    $v = Validator::make($data, [
        'email' => 'sometimes|required|email',
    ]);

In the example above, the `email` field will only be validated if it is present in the `$data` array.

이 예제에서는 `$data` 배열에 `email` 필드가 존재할 경우에만 그 필드의 유효성 검사가 실행됩니다.

> {tip} If you are attempting to validate a field that should always be present but may be empty, check out [this note on optional fields](#a-note-on-optional-fields).

> {tip} 항상 있어야 하지만 비어 있을 수 있는 필드의 유효성을 검사하려는 경우 [선택 필드에 대한 참고 사항](#a-note-on-optional-fields)을 확인하세요.

<a name="complex-conditional-validation"></a>
#### Complex Conditional Validation
#### 복잡한 조건부 유효성 검사

Sometimes you may wish to add validation rules based on more complex conditional logic. For example, you may wish to require a given field only if another field has a greater value than 100. Or, you may need two fields to have a given value only when another field is present. Adding these validation rules doesn't have to be a pain. First, create a `Validator` instance with your _static rules_ that never change:

때때로 여러분은 한가지 이상의 복잡한 로직에 대해서 유효성 규칙을 추가하고자 할 수도 있습니다. 예를 들어, 어떤 다른 필드가 100 이상의 값을 가질때에만 주어진 필드가 반드시 존재하길 바랄 수도 있습니다. 또는 다른 필드가 존재할 때에만 두개의 필드가 주어진 값을 가질 필요가 있을수도 있습니다. 이러한 유효성 검사 규칙을 추가하는 것이 어렵지 않을 수 있습니다. 우선 _고정 규칙_ 을 변경할 필요 없이 그대로 사용하여 `Validator` 인스턴스를 생성합니다.

    use Illuminate\Support\Facades\Validator;

    $validator = Validator::make($request->all(), [
        'email' => 'required|email',
        'games' => 'required|numeric',
    ]);

Let's assume our web application is for game collectors. If a game collector registers with our application and they own more than 100 games, we want them to explain why they own so many games. For example, perhaps they run a game resale shop, or maybe they just enjoy collecting games. To conditionally add this requirement, we can use the `sometimes` method on the `Validator` instance.

웹 애플리케이션이 게임 수집가용이라고 가정해 보겠습니다. 게임 수집가가 우리 애플리케이션에 등록하고 그들이 100개 이상의 게임을 소유하고 있다면 우리는 그들이 왜 그렇게 많은 게임을 소유하고 있는지 설명하기를 원합니다. 예를 들어 게임 재판매 상점을 운영하거나 게임 수집을 즐기는 경우가 있습니다. 이 요구 사항을 조건부로 추가하려면 `Validator` 인스턴스에서 `sometimes` 메서드를 사용할 수 있습니다.

    $validator->sometimes('reason', 'required|max:500', function ($input) {
        return $input->games >= 100;
    });

The first argument passed to the `sometimes` method is the name of the field we are conditionally validating. The second argument is a list of the rules we want to add. If the closure passed as the third argument returns `true`, the rules will be added. This method makes it a breeze to build complex conditional validations. You may even add conditional validations for several fields at once:

`sometimes` 메소드에 전달된 첫 번째 인수는 조건부로 유효성을 검사하는 필드의 이름입니다. 두 번째 인수는 추가하려는 규칙 목록입니다. 세 번째 인수로 전달된 클로저가 `true`를 반환하면 규칙이 추가됩니다. 이 방법을 사용하면 복잡한 조건부 유효성 검사를 쉽게 구축할 수 있습니다. 한 번에 여러 필드에 대한 조건부 유효성 검사를 추가할 수도 있습니다.

    $validator->sometimes(['reason', 'cost'], 'required', function ($input) {
        return $input->games >= 100;
    });

> {tip} The `$input` parameter passed to your closure will be an instance of `Illuminate\Support\Fluent` and may be used to access your input and files under validation.

> {tip} 클로저에 전달된 `$input` 매개변수는 `Illuminate\Support\Fluent`의 인스턴스가 되며 유효성 검사 중인 입력 및 파일에 액세스하는 데 사용할 수 있습니다.

<a name="complex-conditional-array-validation"></a>
#### Complex Conditional Array Validation
#### 복잡한 조건부 배열 검증

Sometimes you may want to validate a field based on another field in the same nested array whose index you do not know. In these situations, you may allow your closure to receive a second argument which will be the current individual item in the array being validated:

인덱스를 모르는 동일한 중첩 배열의 다른 필드를 기반으로 필드의 유효성을 검사해야 하는 경우가 있습니다. 이러한 상황에서 클로저가 유효성 검사 중인 배열의 현재 개별 항목이 될 두 번째 인수를 수신하도록 허용할 수 있습니다.

    $input = [
        'channels' => [
            [
                'type' => 'email',
                'address' => 'abigail@example.com',
            ],
            [
                'type' => 'url',
                'address' => 'https://example.com',
            ],
        ],
    ];

    $validator->sometimes('channels.*.address', 'email', function($input, $item) {
        return $item->type === 'email';
    });

    $validator->sometimes('channels.*.address', 'url', function($input, $item) {
        return $item->type !== 'email';
    });

Like the `$input` parameter passed to the closure, the `$item` parameter is an instance of `Illuminate\Support\Fluent` when the attribute data is an array; otherwise, it is a string.

클로저에 전달된 `$input` 매개변수와 마찬가지로 `$item` 매개변수는 속성 데이터가 배열일 때 `Illuminate\Support\Fluent`의 인스턴스입니다. 그렇지 않으면 문자열입니다.

<a name="validating-arrays"></a>
## Validating Arrays
## 배열값 유효성 검사

As discussed in the [`array` validation rule documentation](#rule-array), the `array` rule accepts a list of allowed array keys. If any additional keys are present within the array, validation will fail:

[`array` 유효성 검사 규칙 문서](#rule-array)에 설명된 대로 `array` 규칙은 허용되는 배열 키 목록을 허용합니다. 배열 내에 추가 키가 있는 경우 유효성 검사가 실패합니다.

    use Illuminate\Support\Facades\Validator;

    $input = [
        'user' => [
            'name' => 'Taylor Otwell',
            'username' => 'taylorotwell',
            'admin' => true,
        ],
    ];

    Validator::make($input, [
        'user' => 'array:username,locale',
    ]);

In general, you should always specify the array keys that are allowed to be present within your array. Otherwise, the validator's `validate` and `validated` methods will return all of the validated data, including the array and all of its keys, even if those keys were not validated by other nested array validation rules.

일반적으로 배열 내에 존재하도록 허용된 배열 키를 항상 지정해야 합니다. 그렇지 않으면 유효성 검사기의 `validate` 및 `validated` 메서드는 해당 키가 다른 중첩된 배열 유효성 검사 규칙에 의해 유효성이 검사되지 않은 경우에도 배열 및 모든 키를 포함하여 유효성이 검사된 모든 데이터를 반환합니다.

<a name="excluding-unvalidated-array-keys"></a>
### Excluding Unvalidated Array Keys
### 검증되지 않은 배열 키 제외

If you would like, you may instruct Laravel's validator to never include unvalidated array keys in the "validated" data it returns, even if you use the `array` rule without specifying a list of allowed keys. To accomplish this, you may call the validator's `excludeUnvalidatedArrayKeys` method in the `boot` method of your application's `AppServiceProvider`. After doing so, the validator will include array keys in the "validated" data it returns only when those keys were specifically validated by [nested array rules](#validating-arrays):

원한다면, 허용된 키 목록을 지정하지 않고 `array` 규칙을 사용하더라도 라라벨의 유효성 검사기가 반환하는 "검증된" 데이터에 검증되지 않은 배열 키를 포함하지 않도록 지시할 수 있습니다. 이를 수행하기 위해 애플리케이션의 `AppServiceProvider`의 `boot` 메소드에서 유효성 검사기의 `excludeUnvalidatedArrayKeys` 메소드를 호출할 수 있습니다. 그렇게 한 후 유효성 검사기는 해당 키가 [중첩 배열 규칙](#validating-arrays)에 의해 구체적으로 검증된 경우에만 반환하는 "검증된" 데이터에 배열 키를 포함합니다.

```php
use Illuminate\Support\Facades\Validator;

/**
 * Register any application services.
 *
 * @return void
 */
public function boot()
{
    Validator::excludeUnvalidatedArrayKeys();
}
```

<a name="validating-nested-array-input"></a>
### Validating Nested Array Input
### 중첩 배열 입력 검증

Validating nested array based form input fields doesn't have to be a pain. You may use "dot notation" to validate attributes within an array. For example, if the incoming HTTP request contains a `photos[profile]` field, you may validate it like so:

중첩 배열 기반 form 입력 필드의 유효성을 검사하는 것에 대해 괴로워 할 필요가 없습니다. "점 표기법"을 사용하여 배열 내의 속성을 확인할 수 있습니다. 예를 들어 들어오는 HTTP 요청에 `photos[profile]` 필드가 포함되어 있으면 다음과 같이 유효성을 검사할 수 있습니다.

    use Illuminate\Support\Facades\Validator;

    $validator = Validator::make($request->all(), [
        'photos.profile' => 'required|image',
    ]);

You may also validate each element of an array. For example, to validate that each email in a given array input field is unique, you may do the following:

배열의 각 요소를 확인할 수도 있습니다. 예를 들어, 주어진 배열 입력 필드의 각 이메일이 고유한지 확인하려면 다음을 수행할 수 있습니다.

    $validator = Validator::make($request->all(), [
        'person.*.email' => 'email|unique:users',
        'person.*.first_name' => 'required_with:person.*.last_name',
    ]);

Likewise, you may use the `*` character when specifying [custom validation messages in your language files](#custom-messages-for-specific-attributes), making it a breeze to use a single validation message for array based fields:

마찬가지로, [언어 파일의 사용자 정의 유효성 검사 메시지](#custom-messages-for-specific-attributes)를 지정할 때 `*` 문자를 사용할 수 있으므로 배열 기반 필드에 단일 유효성 검사 메시지를 사용하는 것이 간편합니다.

    'custom' => [
        'person.*.email' => [
            'unique' => 'Each person must have a unique email address',
        ]
    ],

<a name="validating-passwords"></a>
## Validating Passwords
## 비밀번호 검증

To ensure that passwords have an adequate level of complexity, you may use Laravel's `Password` rule object:

비밀번호가 적절한 수준의 복잡성을 갖도록 하려면 라라벨의 `Password` 규칙 객체를 사용할 수 있습니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rules\Password;

    $validator = Validator::make($request->all(), [
        'password' => ['required', 'confirmed', Password::min(8)],
    ]);

The `Password` rule object allows you to easily customize the password complexity requirements for your application, such as specifying that passwords require at least one letter, number, symbol, or characters with mixed casing:

`Password` 규칙 개체를 사용하면 비밀번호에 문자, 숫자, 기호 또는 대소문자가 혼합된 문자가 하나 이상 필요하도록 지정하는 것과 같이 애플리케이션에 대한 비밀번호 복잡성 요구 사항을 쉽게 사용자 지정할 수 있습니다.

    // Require at least 8 characters...
    Password::min(8)

    // Require at least one letter...
    Password::min(8)->letters()

    // Require at least one uppercase and one lowercase letter...
    Password::min(8)->mixedCase()

    // Require at least one number...
    Password::min(8)->numbers()

    // Require at least one symbol...
    Password::min(8)->symbols()

In addition, you may ensure that a password has not been compromised in a public password data breach leak using the `uncompromised` method:

또한 `uncompromised` 메서드를 사용하여 공개된 비밀번호 데이터 유출 리스트를 통해 비밀번호가 위험한지 확인할 수 있습니다.

    Password::min(8)->uncompromised()

Internally, the `Password` rule object uses the [k-Anonymity](https://en.wikipedia.org/wiki/K-anonymity) model to determine if a password has been leaked via the [haveibeenpwned.com](https://haveibeenpwned.com) service without sacrificing the user's privacy or security.

내부적으로 `Password` 규칙 객체는 [k-Anonymity](https://en.wikipedia.org/wiki/K-anonymity) 모델을 사용하여 [haveibeenpwned.com](https://haveibeenpwned.com) 을 통해 비밀번호가 유출되었는지 확인합니다. 사용자의 개인 정보 또는 보안을 희생하지 않고 서비스를 제공합니다.

By default, if a password appears at least once in a data leak, it will be considered compromised. You can customize this threshold using the first argument of the `uncompromised` method:

기본적으로 암호가 데이터 유출에서 한 번 이상 나타나면 손상된 것으로 간주됩니다. `uncompromised` 메소드의 첫 번째 인수를 사용하여 이 임계값을 사용자 정의할 수 있습니다.

    // Ensure the password appears less than 3 times in the same data leak...
    Password::min(8)->uncompromised(3);

Of course, you may chain all the methods in the examples above:

물론 위의 예에서 모든 메서드를 연결할 수 있습니다.

    Password::min(8)
        ->letters()
        ->mixedCase()
        ->numbers()
        ->symbols()
        ->uncompromised()

<a name="defining-default-password-rules"></a>
#### Defining Default Password Rules
#### 기본 비밀번호 규칙 정의

You may find it convenient to specify the default validation rules for passwords in a single location of your application. You can easily accomplish this using the `Password::defaults` method, which accepts a closure. The closure given to the `defaults` method should return the default configuration of the Password rule. Typically, the `defaults` rule should be called within the `boot` method of one of your application's service providers:

애플리케이션의 단일 위치에서 암호에 대한 기본 유효성 검사 규칙을 지정하는 것이 편리할 수 있습니다. 클로저를 입력받는 `Password::defaults` 메소드를 사용하여 이를 쉽게 달성할 수 있습니다. `defaults` 메소드에 주어진 클로저는 Password 규칙의 기본 구성을 반환해야 합니다. 일반적으로 `defaults` 규칙은 애플리케이션 서비스 제공자 중 하나의 `boot` 메소드 내에서 호출되어야 합니다.

```php
use Illuminate\Validation\Rules\Password;

/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Password::defaults(function () {
        $rule = Password::min(8);

        return $this->app->isProduction()
                    ? $rule->mixedCase()->uncompromised()
                    : $rule;
    });
}
```

Then, when you would like to apply the default rules to a particular password undergoing validation, you may invoke the `defaults` method with no arguments:

그런 다음 유효성 검사를 받는 특정 비밀번호에 기본 규칙을 적용하려면 인수 없이 `defaults` 메소드를 호출하면 됩니다.

    'password' => ['required', Password::defaults()],

Occasionally, you may want to attach additional validation rules to your default password validation rules. You may use the `rules` method to accomplish this:

경우에 따라 기본 암호 유효성 검사 규칙에 추가 유효성 검사 규칙을 연결할 수 있습니다. 이를 수행하기 위해 `rules` 메소드를 사용할 수 있습니다.

    use App\Rules\ZxcvbnRule;

    Password::defaults(function () {
        $rule = Password::min(8)->rules([new ZxcvbnRule]);

        // ...
    });

<a name="custom-validation-rules"></a>
## Custom Validation Rules
## 사용자 정의 유효성 검사 규칙

<a name="using-rule-objects"></a>
### Using Rule Objects
### Rule 객체 사용하기

Laravel provides a variety of helpful validation rules; however, you may wish to specify some of your own. One method of registering custom validation rules is using rule objects. To generate a new rule object, you may use the `make:rule` Artisan command. Let's use this command to generate a rule that verifies a string is uppercase. Laravel will place the new rule in the `app/Rules` directory. If this directory does not exist, Laravel will create it when you execute the Artisan command to create your rule:

라라벨은 다양하고 유용한 유효성 검사 규칙을 제공합니다. 그러나 일부를 지정하고 싶을 수도 있습니다. 사용자 정의 유효성 검사 규칙을 등록하는 한 가지 방법은 규칙 개체를 사용하는 것입니다. 새 규칙 개체를 생성하려면 `make:rule` Artisan 명령을 사용할 수 있습니다. 이 명령을 사용하여 문자열이 대문자인지 확인하는 규칙을 생성해 보겠습니다. 라라벨은 `app/Rules` 디렉토리에 새 규칙을 배치합니다. 이 디렉토리가 존재하지 않는 경우 라라벨은 Artisan 명령을 실행하여 규칙을 생성할 때 생성합니다.

    php artisan make:rule Uppercase

Once the rule has been created, we are ready to define its behavior. A rule object contains two methods: `passes` and `message`. The `passes` method receives the attribute value and name, and should return `true` or `false` depending on whether the attribute value is valid or not. The `message` method should return the validation error message that should be used when validation fails:

rule 객체가 생성되고나면, 유효성 검사가 동작하는 방식을 정해야 합니다. rule 객체는 두개의 메소드(`passes` 와 `message`)를 가지고 있습니다. `passes` 메소드는 속성 값과 이름을 전달받아, 속성 값이 유효한지 아닌지에 따라, `true` 또는 `false` 를 반환해야 합니다. `message` 메소드는 유효성 검사가 실패했을 때 사용하는 에러 메세지를 반환해야 합니다.

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

#### Accessing Additional Data
#### 추가 데이터 액세스

If your custom validation rule class needs to access all of the other data undergoing validation, your rule class may implement the `Illuminate\Contracts\Validation\DataAwareRule` interface. This interface requires your class to define a `setData` method. This method will automatically be invoked by Laravel (before validation proceeds) with all of the data under validation:

사용자 정의 유효성 검사 규칙 클래스가 유효성 검사를 받는 다른 모든 데이터에 액세스해야 하는 경우 규칙 클래스는 `Illuminate\Contracts\Validation\DataAwareRule` 인터페이스를 구현할 수 있습니다. 이 인터페이스를 사용하려면 클래스에서 `setData` 메서드를 정의해야 합니다. 이 메소드는 검증 중인 모든 데이터와 함께 라라벨에 의해 (검증이 진행되기 전) 자동으로 호출됩니다.

    <?php

    namespace App\Rules;

    use Illuminate\Contracts\Validation\Rule;
    use Illuminate\Contracts\Validation\DataAwareRule;

    class Uppercase implements Rule, DataAwareRule
    {
        /**
         * All of the data under validation.
         *
         * @var array
         */
        protected $data = [];

        // ...

        /**
         * Set the data under validation.
         *
         * @param  array  $data
         * @return $this
         */
        public function setData($data)
        {
            $this->data = $data;

            return $this;
        }
    }

Or, if your validation rule requires access to the validator instance performing the validation, you may implement the `ValidatorAwareRule` interface:

또는 유효성 검사 규칙에 유효성 검사를 수행하는 유효성 검사기 인스턴스에 대한 액세스가 필요한 경우 `ValidatorAwareRule` 인터페이스를 구현할 수 있습니다.

    <?php

    namespace App\Rules;

    use Illuminate\Contracts\Validation\Rule;
    use Illuminate\Contracts\Validation\ValidatorAwareRule;

    class Uppercase implements Rule, ValidatorAwareRule
    {
        /**
         * The validator instance.
         *
         * @var \Illuminate\Validation\Validator
         */
        protected $validator;

        // ...

        /**
         * Set the current validator.
         *
         * @param  \Illuminate\Validation\Validator  $validator
         * @return $this
         */
        public function setValidator($validator)
        {
            $this->validator = $validator;

            return $this;
        }
    }

<a name="using-closures"></a>
### Using Closures
### 클로저 사용하기

If you only need the functionality of a custom rule once throughout your application, you may use a closure instead of a rule object. The closure receives the attribute's name, the attribute's value, and a `$fail` callback that should be called if validation fails:

애플리케이션 전체에서 사용자 지정 규칙의 기능이 한 번만 필요한 경우 규칙 개체 대신 클로저를 사용할 수 있습니다. 클로저는 속성 이름, 속성 값, 유효성 검사가 실패하면 호출되어야 하는 `$fail` 콜백을 입력받습니다.

    use Illuminate\Support\Facades\Validator;

    $validator = Validator::make($request->all(), [
        'title' => [
            'required',
            'max:255',
            function ($attribute, $value, $fail) {
                if ($value === 'foo') {
                    $fail('The '.$attribute.' is invalid.');
                }
            },
        ],
    ]);

<a name="implicit-rules"></a>
### Implicit Rules
### 묵시적 규칙

By default, when an attribute being validated is not present or contains an empty string, normal validation rules, including custom rules, are not run. For example, the [`unique`](#rule-unique) rule will not be run against an empty string:

기본적으로 유효성 검사 중인 속성이 없거나 빈 문자열이 포함된 경우, 사용자 지정 규칙을 포함한 일반 유효성 검사 규칙이 실행되지 않습니다. 예를 들어, [`unique`](#rule-unique) 규칙은 빈 문자열에 대해 실행되지 않습니다.

    use Illuminate\Support\Facades\Validator;

    $rules = ['name' => 'unique:users,name'];

    $input = ['name' => ''];

    Validator::make($input, $rules)->passes(); // true

For a custom rule to run even when an attribute is empty, the rule must imply that the attribute is required. To create an "implicit" rule, implement the `Illuminate\Contracts\Validation\ImplicitRule` interface. This interface serves as a "marker interface" for the validator; therefore, it does not contain any additional methods you need to implement beyond the methods required by the typical `Rule` interface.

속성이 비어 있는 경우에도, 사용자 지정 규칙이 실행되려면 규칙이 속성이 필수임을 묵시해야 합니다. "묵시적" 규칙을 생성하려면 `Illuminate\Contracts\Validation\ImplicitRule` 인터페이스를 구현하십시오. 이 인터페이스는 유효성 검사기의 "마커 인터페이스" 역할을 합니다. 따라서 일반적인 `Rule` 인터페이스에서 요구하는 메서드 외에 구현해야 하는 추가 메서드가 포함되어 있지 않습니다.

To generate a new implicit rule object, you may use the `make:rule` Artisan command with the `--implicit` option :

새로운 묵시적 규칙 객체를 생성하려면 `--implicit` 옵션과 함께 `make:rule` Artisan 명령을 사용할 수 있습니다.

     php artisan make:rule Uppercase --implicit

> {note} An "implicit" rule only _implies_ that the attribute is required. Whether it actually invalidates a missing or empty attribute is up to you.

> {note} "묵시적" 규칙은 속성이 필요하다는 것을 _묵시합니다_. 실제로 누락되거나 비어 있는 속성을 무효화하는지 여부는 사용자에게 달려 있습니다.
