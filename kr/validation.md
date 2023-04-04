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
    - [form-폼 다시 채우기](#repopulating-forms)
    - [A Note On Optional Fields](#a-note-on-optional-fields)
    - [옵션 필드에 대한 주의사항](#a-note-on-optional-fields)
    - [Validation Error Response Format](#validation-error-response-format)
    - [유효성 검증 에러 리스폰스 형식](#validation-error-response-format)
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
    - [에러 메시지 사용자 정의](#manual-customizing-the-error-messages)
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
    - [Validating Nested Array Input](#validating-nested-array-input)
    - [중첩 배열 입력 검증](#validating-nested-array-input)
    - [Error Message Indexes & Positions](#error-message-indexes-and-positions)
    - [에러 메세지에서 순서와 위치 참조하기](#error-message-indexes-and-positions)
- [Validating Files](#validating-files)
- [파일 유효성 검사](#validating-files)
- [Validating Passwords](#validating-passwords)
- [비밀번호 검증](#validating-passwords)
- [Custom Validation Rules](#custom-validation-rules)
- [사용자 정의 유효성 검증 규칙](#custom-validation-rules)
    - [Using Rule Objects](#using-rule-objects)
    - [Rule 객체 사용하기](#using-rule-objects)
    - [Using Closures](#using-closures)
    - [클로저 사용하기](#using-closures)
    - [Implicit Rules](#implicit-rules)
    - [암묵적 규칙](#implicit-rules)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel provides several different approaches to validate your application's incoming data. It is most common to use the `validate` method available on all incoming HTTP requests. However, we will discuss other approaches to validation as well.

라라벨은 애플리케이션의 수신 데이터를 검증하기 위해 여러 가지 접근 방식을 제공합니다. 들어오는 모든 HTTP 리퀘스트에서 사용할 수 있는 `validate` 메서드를 사용하는 것이 가장 일반적입니다. 그러나 리퀘스트 인스턴스의 `validate` 메소드를 사용하는 것 이외의 유효성 검사에 방식도 다룰 것입니다.

Laravel includes a wide variety of convenient validation rules that you may apply to data, even providing the ability to validate if values are unique in a given database table. We'll cover each of these validation rules in detail so that you are familiar with all of Laravel's validation features.

라라벨에는 데이터에 적용할 수 있는 다양한 편리한 유효성 검사 규칙이 포함되어 있으며, 주어진 데이터베이스 테이블에서 값이 고유한지 확인하는 기능도 제공합니다. 라라벨의 모든 유효성 검사 기능에 익숙해질 수 있도록 이러한 유효성 검사 규칙 각각을 자세히 다룰 것입니다.

<a name="validation-quickstart"></a>
## Validation Quickstart
## 빠르게 유효성 검사 살펴보기

To learn about Laravel's powerful validation features, let's look at a complete example of validating a form and displaying the error messages back to the user. By reading this high-level overview, you'll be able to gain a good general understanding of how to validate incoming request data using Laravel:

라라벨의 강력한 유효성 검사 기능에 대해 알아보기 위해 form-폼 유효성을 검사하고 사용자에게 에러 메시지를 다시 표시하는 전체 예제를 살펴보겠습니다. 이 높은 수준의 개요를 읽으면 라라벨을 사용하여 들어오는 리퀘스트 데이터의 유효성을 검사하는 방법에 대한 일반적인 이해를 얻을 수 있습니다.

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

다음으로 이러한 라우트로 들어오는 리퀘스트을 처리하는 간단한 컨트롤러를 살펴보겠습니다. 지금은 `store` 메소드를 비워둘 것입니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\RedirectResponse;
    use Illuminate\Http\Request;
    use Illuminate\View\View;

    class PostController extends Controller
    {
        /**
         * Show the form to create a new blog post.
         */
        public function create(): View
        {
            return view('post.create');
        }

        /**
         * Store a new blog post.
         */
        public function store(Request $request): RedirectResponse
        {
            // Validate and store the blog post...

            $post = /** ... */

            return to_route('post.show', ['post' => $post->id]);
        }
    }

<a name="quick-writing-the-validation-logic"></a>
### Writing The Validation Logic
### 유효성 검사 로직 작성하기

Now we are ready to fill in our `store` method with the logic to validate the new blog post. To do this, we will use the `validate` method provided by the `Illuminate\Http\Request` object. If the validation rules pass, your code will keep executing normally; however, if validation fails, an `Illuminate\Validation\ValidationException` exception will be thrown and the proper error response will automatically be sent back to the user.

이제 새 블로그 게시물의 유효성을 검사하는 논리로 `store` 메서드를 채울 준비가 되었습니다. 이를 위해 `Illuminate\Http\Request` 객체가 제공하는 `validate` 메소드를 사용할 것입니다. 유효성 검사 규칙을 통과하면 코드가 계속 정상적으로 실행됩니다. 그러나 유효성 검사가 실패하면 `Illuminate\Validation\ValidationException` 예외가 발생하고 적절한 에러 리스폰스가 자동으로 사용자에게 다시 전송됩니다.

If validation fails during a traditional HTTP request, a redirect response to the previous URL will be generated. If the incoming request is an XHR request, a [JSON response containing the validation error messages](#validation-error-response-format) will be returned.

기존 HTTP 리퀘스트 중에 유효성 검사가 실패하면 이전 URL에 대한 리디렉션 응답이 생성됩니다. 들어오는 리퀘스트이 XHR 리퀘스트인 경우 [유효성 검사 에러 메시지가 포함된 JSON 리스폰스](#validation-error-response-format)가 반환됩니다.

To get a better understanding of the `validate` method, let's jump back into the `store` method:

`validate` 메소드에 대해 더 잘 이해하기 위해, 다시 `store` 메소드로 돌아가 보겠습니다.

    /**
     * Store a new blog post.
     */
    public function store(Request $request): RedirectResponse
    {
        $validated = $request->validate([
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ]);

        // The blog post is valid...

        return redirect('/posts');
    }

As you can see, the validation rules are passed into the `validate` method. Don't worry - all available validation rules are [documented](#available-validation-rules). Again, if the validation fails, the proper response will automatically be generated. If the validation passes, our controller will continue executing normally.

보시다시피 유효성 검사 규칙은 `validate` 메서드로 전달됩니다. 걱정하지 마십시오. 사용 가능한 모든 유효성 검사 규칙은 [문서화되어 있습니다](#available-validation-rules). 다시 말하지만, 유효성 검사가 실패하면 적절한 리스폰스가 자동으로 생성됩니다. 유효성 검사가 통과하면 컨트롤러가 계속 정상적으로 실행됩니다.

Alternatively, validation rules may be specified as arrays of rules instead of a single `|` delimited string:

또한, 유효성 검사 규칙은 구분자 `|`로 구성된 단일한 문자열을 사용하는 대신 배열로 정의할 수 있습니다.

    $validatedData = $request->validate([
        'title' => ['required', 'unique:posts', 'max:255'],
        'body' => ['required'],
    ]);

In addition, you may use the `validateWithBag` method to validate a request and store any error messages within a [named error bag](#named-error-bags):

또한 `validateWithBag` 메소드를 사용하여 리퀘스트을 유효성을 검사하고 에러 메시지를 [named error bag](#named-error-bags) 내에 저장할 수 있습니다.

    $validatedData = $request->validateWithBag('post', [
        'title' => ['required', 'unique:posts', 'max:255'],
        'body' => ['required'],
    ]);

<a name="stopping-on-first-validation-failure"></a>
#### Stopping On First Validation Failure
#### 유효성 검사가 처음 실패하면 검사 중단하기

Sometimes you may wish to stop running validation rules on an attribute after the first validation failure. To do so, assign the `bail` rule to the attribute:

경우에 따라서 유효성 검사가 처음 실패하면 속성값에 대한 유효성 검사를 중단하기를 원할수도 있습니다. 이러한 경우 `bail` 규칙을 지정하면 됩니다.

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

들어오는 HTTP 리퀘스트에 "nested-중첩된" 필드 데이터가 포함된 경우 "점" 구문을 사용하여 유효성 검사 규칙에 다음 필드를 지정할 수 있습니다.

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'author.name' => 'required',
        'author.description' => 'required',
    ]);

On the other hand, if your field name contains a literal period, you can explicitly prevent this from being interpreted as "dot" syntax by escaping the period with a backslash:

반면에 필드 이름에 리터럴 마침표가 포함된 경우 마침표를 백슬래시로 이스케이프 하여 "점" 구문으로 해석되지 않도록 명시적으로 방지할 수 있습니다.

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'v1\.0' => 'required',
    ]);

<a name="quick-displaying-the-validation-errors"></a>
### Displaying The Validation Errors
### 유효성 검사 에러 표시하기

So, what if the incoming request fields do not pass the given validation rules? As mentioned previously, Laravel will automatically redirect the user back to their previous location. In addition, all of the validation errors and [request input](/docs/{{version}}/requests#retrieving-old-input) will automatically be [flashed to the session](/docs/{{version}}/session#flash-data).

그러면 들어오는 리퀘스트 필드가 주어진 유효성 검사 규칙을 통과하지 못하면 어떻게 될까요? 앞서 언급했듯이 라라벨은 자동으로 사용자를 이전 로케이션으로 다시 리디렉션합니다. 또한 모든 유효성 검사 오류와 [리퀘스트 input-입력](/docs/{{version}}/requests#retriving-old-input)은 유효성 검사 메시지를 전달하기 위해 한 번 사용 후 소멸되는 플레시 기능을 이용한 [세션에 플레시 데이터로 저장하기](/docs/{{version}}/session#flash-data)를 자동으로 수행 합니다.

An `$errors` variable is shared with all of your application's views by the `Illuminate\View\Middleware\ShareErrorsFromSession` middleware, which is provided by the `web` middleware group. When this middleware is applied an `$errors` variable will always be available in your views, allowing you to conveniently assume the `$errors` variable is always defined and can be safely used. The `$errors` variable will be an instance of `Illuminate\Support\MessageBag`. For more information on working with this object, [check out its documentation](#working-with-error-messages).

`$errors` 변수는 `web` 미들웨어 그룹에서 제공하는 `Illuminate\View\Middleware\ShareErrorsFromSession` 미들웨어에 의해 애플리케이션의 모든 view-뷰와 공유됩니다. 이 미들웨어가 적용되면 `$errors` 변수는 항상 view-뷰에서 사용할 수 있으므로 `$errors` 변수가 항상 정의되어있고, 안전하게 사용할 수 있다고 생각하고 사용하면 됩니다. `$errors` 변수는 `Illuminate\Support\MessageBag`의 인스턴스입니다. 이 객체로 작업하는 방법에 대한 자세한 내용은 [해당 문서](#working-with-error-messages)를 확인하세요.

So, in our example, the user will be redirected to our controller's `create` method when validation fails, allowing us to display the error messages in the view:

따라서, 이 예제에서, 유효성 검사가 실패하면 사용자는 컨트롤러의 `create` 메소드로 리디렉션될 것이고, 뷰에서는 에러 메세지가 표시됩니다.

```blade
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
#### 에러 메시지 사용자 정의

Laravel's built-in validation rules each have an error message that is located in your application's `lang/en/validation.php` file. Within this file, you will find a translation entry for each validation rule. You are free to change or modify these messages based on the needs of your application.

라라벨의 내장 유효성 검사 규칙 각각에는 에러 메시지가 존재합니다. 애플리케이션의 `lang/en/validation.php` 파일에 정의되어 있으므로 이 파일 내에서 각 유효성 검사 규칙에 대한 번역 항목을 찾을 수 있습니다. 애플리케이션의 필요에 따라 이러한 메시지를 자유롭게 변경하거나 수정할 수 있습니다.

In addition, you may copy this file to another language directory to translate the messages for your application's language. To learn more about Laravel localization, check out the complete [localization documentation](/docs/{{version}}/localization).

또한 이 파일을 다른 언어 디렉토리에 복사하여 애플리케이션에서 지정한 언어로 메시지를 번역할 수 있습니다. 라라벨 현지화에 대해 자세히 알아보려면 전체 [현지화 문서](/docs/{{version}}/localization)를 확인하세요.

> **Warning**
> By default, the Laravel application skeleton does not include the `lang` directory. If you would like to customize Laravel's language files, you may publish them via the `lang:publish` Artisan command.

기본적으로 라라벨 애플리케이션을 처음 설치할 때 생성되는 파일 및 폴더 구조인 스켈레톤에는 `lang` 디렉터리가 포함되어 있지 않습니다. 라라벨의 언어 파일을 사용자 정의하려면 `lang:publish` 아티즌 명령를 통해 publish-게시할 수 있습니다.

<a name="quick-xhr-requests-and-validation"></a>
#### XHR Requests & Validation
#### XHR 리퀘스트 및 검증

In this example, we used a traditional form to send data to the application. However, many applications receive XHR requests from a JavaScript powered frontend. When using the `validate` method during an XHR request, Laravel will not generate a redirect response. Instead, Laravel generates a [JSON response containing all of the validation errors](#validation-error-response-format). This JSON response will be sent with a 422 HTTP status code.

이 예제에서는 기존의 form-폼을 사용하여 애플리케이션에 데이터를 전송했습니다. 그러나 많은 애플리케이션이 자바스크립트 기반 프론트엔드에서 XHR 리퀘스트을 수신합니다. XHR 리퀘스트 내의 `validate` 메소드를 사용할 때 라라벨은 리디렉션 응답을 생성하지 않습니다. 대신 라라벨은 [모든 유효성 검사 오류가 포함된 JSON 리스폰스](#validation-error-response-format)을 생성합니다. 이 JSON 리스폰스는 422 HTTP 상태 코드와 함께 전송됩니다.

<a name="the-at-error-directive"></a>
#### The `@error` Directive
#### `@error` 지시어

You may use the `@error` [Blade](/docs/{{version}}/blade) directive to quickly determine if validation error messages exist for a given attribute. Within an `@error` directive, you may echo the `$message` variable to display the error message:

`@error` [Blade](/docs/{{version}}/blade) 지시어을 사용하여 주어진 속성에 대한 유효성 검사 에러 메시지가 있는지 빠르게 확인할 수 있습니다. `@error` 지시문 내에서 `$message` 변수를 출력하여 에러 메시지를 표시할 수 있습니다.

```blade
<!-- /resources/views/post/create.blade.php -->

<label for="title">Post Title</label>

<input id="title"
    type="text"
    name="title"
    class="@error('title') is-invalid @enderror">

@error('title')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

If you are using [named error bags](#named-error-bags), you may pass the name of the error bag as the second argument to the `@error` directive:

[named error bags](#named-error-bags)를 사용하는 경우 `@error` 지시어의 두 번째 인자에 error bag의 이름을 전달할 수 있습니다.

```blade
<input ... class="@error('title', 'post') is-invalid @enderror">
```

<a name="repopulating-forms"></a>
### Repopulating Forms
### Form 다시 채우기

When Laravel generates a redirect response due to a validation error, the framework will automatically [flash all of the request's input to the session](/docs/{{version}}/session#flash-data). This is done so that you may conveniently access the input during the next request and repopulate the form that the user attempted to submit.

라라벨이 유효성 검사 오류로 인해 리디렉션 응답을 생성하면 프레임워크는 자동으로 [세션에 저장해 둔 모든 리퀘스트 input-입력을 플래시](/docs/{{version}}/session#flash-data)하여 저장된 값을 사용자에게 전달하고 저장된 캐시를 날립니다. 이는 유효성 검사를 통과하지 못한 리디렉션된 페이지에서 사용자가 제출하려고 시도한 form-폼을 다시 채울 수 있도록 편리한 액세스를 제공하기 위함입니다.

To retrieve flashed input from the previous request, invoke the `old` method on an instance of `Illuminate\Http\Request`. The `old` method will pull the previously flashed input data from the [session](/docs/{{version}}/session):

이전 리퀘스트에서 플래시 입력을 가져오려면 `Illuminate\Http\Request` 인스턴스에서 `old` 메서드를 호출하세요. `old` 메소드는 [세션](/docs/{{version}}/session)에서 이전에 플래시된 입력 데이터를 가져옵니다.

    $title = $request->old('title');

Laravel also provides a global `old` helper. If you are displaying old input within a [Blade template](/docs/{{version}}/blade), it is more convenient to use the `old` helper to repopulate the form. If no old input exists for the given field, `null` will be returned:

라라벨은 또한 전역 `old` 헬퍼를 제공합니다. [블레이드 템플릿](/docs/{{version}}/blade) 내에서 이전 입력을 표시하는 경우 `old` 헬퍼를 사용하여 form을 다시 채울 수 있는 편리한 기능을 제공합니다. 주어진 필드에 대한 이전 입력이 없으면 `null`을 반환합니다.

```blade
<input type="text" name="title" value="{{ old('title') }}">
```

<a name="a-note-on-optional-fields"></a>
### A Note On Optional Fields
### 옵션 필드에 대한 주의사항

By default, Laravel includes the `TrimStrings` and `ConvertEmptyStringsToNull` middleware in your application's global middleware stack. These middleware are listed in the stack by the `App\Http\Kernel` class. Because of this, you will often need to mark your "optional" request fields as `nullable` if you do not want the validator to consider `null` values as invalid. For example:

기본적으로 라라벨은 `TrimStrings` 미들웨어와 `ConvertEmptyStringsToNull` 미들웨어를 애플리케이션의 글로벌 미들웨어 스택에 포함하고 있습니다. 이들 미들웨어는 `App\Http\Kernel` 클래스의 미들웨어를 스택에 나열되어 있습니다. `ConvertEmptyStringsToNull` 미들웨어에 의해 전달 받은 속성 값이 빈 문자열이면 `null`으로 변환되므로, 빈 문자열을 전달받고 싶다면 유효성 검사에서 `null` 값도 허락해야 할 필요가 있습니다. `null`을 유효하지 않은것이 아닌 유효한 것으로 간주하기 위해서는 리퀘스트 필드를 "optional-선택적"인 필드로 만들기 위해 `nullable`규칙을 지정해야할 필요가 있습니다. 예를들면

    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

In this example, we are specifying that the `publish_at` field may be either `null` or a valid date representation. If the `nullable` modifier is not added to the rule definition, the validator would consider `null` an invalid date.

이 예제에서는 `publish_at` 필드가 `null`이거나 유효한 날짜 형식이라고 지정했습니다. 만약 `nullable` 규칙이 추가되지 않은 경우 `null`값은 유효하지 않은 날짜로 간주합니다.

<a name="validation-error-response-format"></a>
### Validation Error Response Format
### 유효성 검증 에러 리스폰스 형식

When your application throws a `Illuminate\Validation\ValidationException` exception and the incoming HTTP request is expecting a JSON response, Laravel will automatically format the error messages for you and return a `422 Unprocessable Entity` HTTP response.

라라벨이 `Illuminate\Validation\ValidationException` 예외를 던지고 들어온 HTTP 리퀘스트가 JSON 리스폰스를 기대하는 경우 라라벨은 자동으로 에러메시지 포맷을 맞춰주고 `422 Unprocessable Entity` HTTP 리스폰스를 반환합니다.

Below, you can review an example of the JSON response format for validation errors. Note that nested error keys are flattened into "dot" notation format:

아래에서 유효성 검증 에러에 대한 JSON 리스폰스 포맷의 예시를 볼 수 있습니다. nested-중첨된 에러 키는 "점" 표기법 형식으로 병합된 것을 눈여겨보세요.

```json
{
    "message": "The team name must be a string. (and 4 more errors)",
    "errors": {
        "team_name": [
            "The team name must be a string.",
            "The team name must be at least 1 characters."
        ],
        "authorization.role": [
            "The selected authorization.role is invalid."
        ],
        "users.0.email": [
            "The users.0.email field is required."
        ],
        "users.2.email": [
            "The users.2.email must be a valid email address."
        ]
    }
}
```

<a name="form-request-validation"></a>
## Form Request Validation
## Form Request 유효성 검사

<a name="creating-form-requests"></a>
### Creating Form Requests
### Form Requests 생성하기

For more complex validation scenarios, you may wish to create a "form request". Form requests are custom request classes that encapsulate their own validation and authorization logic. To create a form request class, you may use the `make:request` Artisan CLI command:

더 복잡한 유효성 검사 시나리오의 경우 "form request"를 만들어 정의할 수 있습니다. form request는 자체 유효성 검사 및 권한 부여 논리를 캡슐화하는 사용자 지정 리퀘스트 클래스입니다. form request 클래스를 생성하려면 `make:request` 아티즌 CLI 명령를 사용할 수 있습니다.

```shell
php artisan make:request StorePostRequest
```

The generated form request class will be placed in the `app/Http/Requests` directory. If this directory does not exist, it will be created when you run the `make:request` command. Each form request generated by Laravel has two methods: `authorize` and `rules`.

form request 클래스는 `app/Http/Requests` 디렉토리에 만들어집니다. 이 디렉토리가 존재하지 않으면 `make:request` 명령을 실행할 때 디렉토리도 생성됩니다. 각각의 form request 클래스에는 `authorize`와 `rules`의 두 가지 메서드를 포함하고 있습니다.

As you might have guessed, the `authorize` method is responsible for determining if the currently authenticated user can perform the action represented by the request, while the `rules` method returns the validation rules that should apply to the request's data:

짐작할 수 있듯이 `authorize` 메소드는 현재 인증된 사용자가 리퀘스트에 따라 애플리케이션의 내부 로직을 실행할 권한이 있는지 검사하는 역할을 하는 반면, `rules` 메소드는 리퀘스트로 전달된 데이터에 적용해야 하는 유효성 검사 규칙을 반환합니다.

    /**
     * Get the validation rules that apply to the request.
     *
     * @return array<string, \Illuminate\Contracts\Validation\Rule|array|string>
     */
    public function rules(): array
    {
        return [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ];
    }

> **Note**  
> You may type-hint any dependencies you require within the `rules` method's signature. They will automatically be resolved via the Laravel [service container](/docs/{{version}}/container).

> **Note**  
> `rules` 메서드의 signature-선언부를 통해 메소드에 필요한 모든 의존성을 타입힌트로 주입할 수 있습니다. 의존성 주입의 설정은 라라벨 [서비스 컨테이너](/docs/{{version}}/container)를 통해 자동으로 해결됩니다.

(역자주: 메소드의 선언부는 `public function rules(): array`부분을 의미합니다.)

So, how are the validation rules evaluated? All you need to do is type-hint the request on your controller method. The incoming form request is validated before the controller method is called, meaning you do not need to clutter your controller with any validation logic:

그렇다면 유효성 검사 규칙은 어떻게 실행될까요? 여러분이 할 일은 그저 컨트롤러 메소드에 request 클래스 타입을 타입힌트 하는 것입니다. 유입된 form request는 컨트롤러 메소드가 호출되기 전에 유효성 검사를 수행합니다. 이러면 컨트롤러에 유효성 검사 로직을 포함시키지 않아도 되므로 컨트롤러가 좀 더 깔끔해 질 수 있습니다.

    /**
     * Store a new blog post.
     */
    public function store(StorePostRequest $request): RedirectResponse
    {
        // The incoming request is valid...

        // Retrieve the validated input data...
        $validated = $request->validated();

        // Retrieve a portion of the validated input data...
        $validated = $request->safe()->only(['name', 'email']);
        $validated = $request->safe()->except(['name', 'email']);

        // Store the blog post...

        return redirect('/posts');
    }

If validation fails, a redirect response will be generated to send the user back to their previous location. The errors will also be flashed to the session so they are available for display. If the request was an XHR request, an HTTP response with a 422 status code will be returned to the user including a [JSON representation of the validation errors](#validation-error-response-format).

유효성 검사가 실패하면 리디렉션 리스폰스가 생성되어 사용자를 이전 위치로 되돌려 보냅니다. 이 때, 유효성 검사 오류는 세션에 저장되어 있다가 리스폰스와 함께 전송되며 동시에 저장되어 있던 세션은 초기화되는 플래시 작업이 일어납니다. 리퀘스트가 XHR 리퀘스트인 경우에는 422 스테이터스 코드가 포함된 HTTP 리스폰스를 사용자에게 반환하며 [유효성 검사 오류의 JSON 표현](#validation-error-response-format)을 포함하여 전달합니다.

<a name="adding-after-hooks-to-form-requests"></a>
#### Adding After Hooks To Form Requests
#### Form Request에 After Hooks 추가하기

If you would like to add an "after" validation hook to a form request, you may use the `withValidator` method. This method receives the fully constructed validator, allowing you to call any of its methods before the validation rules are actually evaluated:

"after" 유효성 검사 후크를 form request에 추가하려면 `withValidator` 메서드를 사용할 수 있습니다. 이 메서드는 벨리데이션 작업을 수행할 수 있는 구성이 완료된 validator를 입력받습니다. 유효성 검사 규칙이 실제로 실행되기 전에 "after" 후크 메서드를 호출할 수 있습니다.

    use Illuminate\Validation\Validator;

    /**
     * Configure the validator instance.
     */
    public function withValidator(Validator $validator): void
    {
        $validator->after(function (Validator $validator) {
            if ($this->somethingElseIsInvalid()) {
                $validator->errors()->add('field', 'Something is wrong with this field!');
            }
        });
    }


<a name="request-stopping-on-first-validation-rule-failure"></a>
#### Stopping On First Validation Failure Attribute
#### 유효성 검사가 처음 실패한 속성에서 중단하기

By adding a `stopOnFirstFailure` property to your request class, you may inform the validator that it should stop validating all attributes once a single validation failure has occurred:

리퀘스트 클래스에 `stopOnFirstFailure` 속성을 추가하면, 단일 유효성 검사 실패할 때, validator에 모든 속성의 유효성 검사를 중지해야 한다고 알릴 수 있습니다.

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

이전에 논의한 바와 같이, form request 유효성 검사가 실패할 때 사용자를 이전 위치로 다시 보내기 위해 리디렉션 리스폰스가 생성됩니다. 사용자를 되돌려 보낼 위치는 커스터마이징 할 수 있습니다. 이렇게 하려면 form request에 `$redirect` 속성을 정의하십시오.

    /**
     * The URI that users should be redirected to if validation fails.
     *
     * @var string
     */
    protected $redirect = '/dashboard';

Or, if you would like to redirect users to a named route, you may define a `$redirectRoute` property instead:

또는 사용자를 이름이 지정된 라우트로 리디렉션하려면 `$redirectRoute` 속성을 `$redirect` 속성 대신 정의할 수 있습니다.

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

form request 클래스에는 `authorize` 메서드도 포함되어 있습니다. 이 메서드 내에서 인증된 사용자에게 실제로 리퀘스트에 포함된 값에 따라 애플리케이션의 리소스를 업데이트할 때 업데이트할 권한이 있는지 확인할 수 있습니다. 예를 들어 사용자가 업데이트하려는 블로그 댓글을 실제로 소유하고 있는지 확인할 수 있습니다. 대부분 다음 메서드 내에서 [인증 게이트 및 정책](/docs/{{version}}/authorization)과 상호 작용합니다.

    use App\Models\Comment;

    /**
     * Determine if the user is authorized to make this request.
     */
    public function authorize(): bool
    {
        $comment = Comment::find($this->route('comment'));

        return $comment && $this->user()->can('update', $comment);
    }

Since all form requests extend the base Laravel request class, we may use the `user` method to access the currently authenticated user. Also, note the call to the `route` method in the example above. This method grants you access to the URI parameters defined on the route being called, such as the `{comment}` parameter in the example below:

모든 form request는 라라벨의 기본 리퀘스트 클래스를 확장하므로 `user` 메소드를 사용하여 현재 인증된 사용자에 액세스할 수 있습니다. 그리고 위의 예에서 `route` 메서드에 대한 호출을 보면, 이 메소드는 아래 예의 `{comment}` 매개변수와 같이 호출되는 라우트에 정의된 URI 매개변수에 대한 액세스 권한을 부여합니다.

    Route::post('/comment/{comment}');

Therefore, if your application is taking advantage of [route model binding](/docs/{{version}}/routing#route-model-binding), your code may be made even more succinct by accessing the resolved model as a property of the request:

따라서 애플리케이션이 [라우트 모델 바인딩](/docs/{{version}}/routing#route-model-binding)을 활용하는 경우, 리퀘스트의 속성으로 확인된 모델에 액세스하여 코드를 더욱 간결하게 만들 수 있습니다.

    return $this->user()->can('update', $this->comment);

If the `authorize` method returns `false`, an HTTP response with a 403 status code will automatically be returned and your controller method will not execute.

`authorize` 메서드가 `false`를 반환하면 403 스테이터스 코드가 포함된 HTTP 리스폰스가 자동으로 반환되고 컨트롤러 메서드가 실행되지 않습니다.

If you plan to handle authorization logic for the request in another part of your application, you may simply return `true` from the `authorize` method:

애플리케이션의 다른 부분에서 리퀘스트에 대한 권한 부여 로직을 처리하려는 경우 `authorize` 메서드에서 `true`를 반환하면 됩니다.

    /**
     * Determine if the user is authorized to make this request.
     */
    public function authorize(): bool
    {
        return true;
    }

> **Note**  
> You may type-hint any dependencies you need within the `authorize` method's signature. They will automatically be resolved via the Laravel [service container](/docs/{{version}}/container).

> **Note**  
> `rules` 메서드의 signature-선언부를 통해 메소드에 필요한 모든 의존성을 타입힌트로 주입할 수 있습니다. 의존성 주입의 설정은 라라벨 [서비스 컨테이너](/docs/{{version}}/container)를 통해 자동으로 해결됩니다.

(역자주: 메소드의 선언부는 `public function rules(): array)`부분을 의미합니다.)

<a name="customizing-the-error-messages"></a>
### Customizing The Error Messages
### 에러 메세지를 사용자 정의하기

You may customize the error messages used by the form request by overriding the `messages` method. This method should return an array of attribute / rule pairs and their corresponding error messages:

`messages` 메서드를 재정의하여 form request에 사용되는 에러 메세지를 커스터마이즈할 수 있습니다. 이 메소드는 속성/규칙의 쌍으로된 배열과 그에 상응하는 에러 메세지를 반환합니다.

    /**
     * Get the error messages for the defined validation rules.
     *
     * @return array<string, string>
     */
    public function messages(): array
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

라라벨의 내장 유효성 검사 규칙의 에러 메시지 중 상당수가 `:attribute` 플레이스홀더를 포함합니다. 이 플레이스홀더는 에러메시지를 표시할 때 플레이스홀더 부분을 유효성 검사 대상 속성 이름으로 표시합니다. 어떤 속성이 유효성 검사를 통과하지 못하여 에러 메시지의 `:attribute` 플레이스홀더에 해당 속성 이름이 아닌 해당 속성 대신에 사용하기 위한 지정한 이름으로 바꾸어 표기하려면 `attributes` 메서드를 재정의하여 기존 속성이 아닌 사용자 지정 속성 이름을 표기할 수 있습니다. `attributes` 메서드는 속성/이름 쌍의 배열을 반환해야 합니다. 아래의 예제는 `email` 속성의 유효성 검사가 실패한 경우 에러 메시지의 플레이스 홀더에 `email`이 아닌 `email address`를 표기하도록 지정합니다.

    /**
     * Get custom attributes for validator errors.
     *
     * @return array<string, string>
     */
    public function attributes(): array
    {
        return [
            'email' => 'email address',
        ];
    }

<a name="preparing-input-for-validation"></a>
### Preparing Input For Validation
### 검증을 위한 입력 준비

If you need to prepare or sanitize any data from the request before you apply your validation rules, you may use the `prepareForValidation` method:

유효성 검사 규칙을 적용하기 전에 리퀘스트의 데이터를 원하는 형태로 가공하고자 할 때 `prepareForValidation` 메서드를 사용할 수 있습니다. 아래 예제는 리퀘스트 속성으로 전달된 `$this->slug`의 값을 유효성 검사할 때는 `Str::slug($this->slug)`값에 대해 유효성을 판단하도록 합니다.

    use Illuminate\Support\Str;

    /**
     * Prepare the data for validation.
     */
    protected function prepareForValidation(): void
    {
        $this->merge([
            'slug' => Str::slug($this->slug),
        ]);
    }

Likewise, if you need to normalize any request data after validation is complete, you may use the `passedValidation` method:

또한 유효성 검사가 완료된 후에 리퀘스트 데이터를 어떤 특정한 형태로 변환하는 정규화 작업을 할 필요가 있는 경우 `passedValidation` 메서드를 사용할 수 있습니다. 아래의 예시는 리퀘스트의 'name' 속성의 값을 'Taylor'으로 변환하도록 합니다.

    use Illuminate\Support\Str;

    /**
     * Handle a passed validation attempt.
     */
    protected function passedValidation(): void
    {
        $this->replace(['name' => 'Taylor']);
    }

<a name="manually-creating-validators"></a>
## Manually Creating Validators
## Validator 수동으로 생성하기

If you do not want to use the `validate` method on the request, you may create a validator instance manually using the `Validator` [facade](/docs/{{version}}/facades). The `make` method on the facade generates a new validator instance:

리퀘스트의 `validate` 메소드를 사용하고 싶지 않다면, `Validator` [파사드](/docs/{{version}}/facades)를 사용하여 validator 인스턴스를 수동으로 생성할 수 있습니다. 파사드에 `make` 메소드를 사용하면 새로운 validator 인스턴스가 생성됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\RedirectResponse;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Validator;

    class PostController extends Controller
    {
        /**
         * Store a new blog post.
         */
        public function store(Request $request): RedirectResponse
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

            return redirect('/posts');
        }
    }

The first argument passed to the `make` method is the data under validation. The second argument is an array of the validation rules that should be applied to the data.

`make` 메서드의 첫 번째 인자는 유효성 검사 대상 데이터입니다. 두 번째 인자는 대상 데이터에 적용해야 하는 유효성 검사 규칙을 정의하고 있는 배열입니다.

After determining whether the request validation failed, you may use the `withErrors` method to flash the error messages to the session. When using this method, the `$errors` variable will automatically be shared with your views after redirection, allowing you to easily display them back to the user. The `withErrors` method accepts a validator, a `MessageBag`, or a PHP `array`.

리퀘스트 유효성 검사가 실패했는지 확인한 후 `withErrors` 메서드를 사용하여 세션을 플래시하여 에러 메시지를 가져올 수 있습니다. 이 메소드를 사용하면 리디렉션 후 `errors` 변수는 자동으로 view-뷰에 공유되므로 사용자에게 에러 메시지를 쉽게 표시할 수 있습니다. `withErrors` 메소드는 validator 객체, `MessageBag` 객체 또는 PHP `array`를 인자로 받을 수 있습니다.

#### Stopping On First Validation Failure
#### 유효성 검사가 처음 실패하면 검사 중단하기

The `stopOnFirstFailure` method will inform the validator that it should stop validating all attributes once a single validation failure has occurred:

`stopOnFirstFailure` 메서드는 validator의 유효성 검사 수행 중 어느 하나의 속성이라도 실패가 발생하면 나머지 속성의 유효성 검사를 중지하게 합니다.

    if ($validator->stopOnFirstFailure()->fails()) {
        // ...
    }

<a name="automatic-redirection"></a>
### Automatic Redirection
### 자동으로 리다이렉트하기

If you would like to create a validator instance manually but still take advantage of the automatic redirection offered by the HTTP request's `validate` method, you may call the `validate` method on an existing validator instance. If validation fails, the user will automatically be redirected or, in the case of an XHR request, a [JSON response will be returned](#validation-error-response-format):

Validator 파사드의 make 메소드를 사용하면 수동으로 validator 인스턴스를 생성할 수 있습니다. 이 때, HTTP 요청의 `validate` 메서드가 제공하는 자동 리디렉션을 활용하려면 기존 validator 인스턴스에서 `validate` 메서드를 호출하면 됩니다. 유효성 검사에 실패하면 사용자가 자동으로 리디렉션되거나, XHR 요청의 경우 [JSON 리스폰스](#validation-error-response-format)가 반환됩니다.

    Validator::make($request->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ])->validate();

You may use the `validateWithBag` method to store the error messages in a [named error bag](#named-error-bags) if validation fails:

유효성 검사가 실패하면 `validateWithBag` 메소드를 사용하여 [이름 붙여진 error bag](#named-error-bags)안에 에러 메시지를 저장할 수 있습니다.

    Validator::make($request->all(), [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ])->validateWithBag('post');

<a name="named-error-bags"></a>
### Named Error Bags
### 이름 붙여진 Error Bags

If you have multiple forms on a single page, you may wish to name the `MessageBag` containing the validation errors, allowing you to retrieve the error messages for a specific form. To achieve this, pass a name as the second argument to `withErrors`:

한 페이지에 여러 form-폼이 있는 경우 유효성 검사 오류가 포함된 `MessageBag`의 이름을 지정하여 특정 form-폼에 대한 에러 메시지를 검색할 수 있습니다. 이렇게 하려면 `withErrors`에 두 번째 인자로 이름을 전달하세요.

    return redirect('register')->withErrors($validator, 'login');

You may then access the named `MessageBag` instance from the `$errors` variable:

그러면 `$errors` 변수에서 지정된 `MessageBag` 인스턴스에 접근할 수 있습니다.

```blade
{{ $errors->login->first('email') }}
```

<a name="manual-customizing-the-error-messages"></a>
### Customizing The Error Messages
### 에러 메시지 사용자 정의

If needed, you may provide custom error messages that a validator instance should use instead of the default error messages provided by Laravel. There are several ways to specify custom messages. First, you may pass the custom messages as the third argument to the `Validator::make` method:

필요한 경우, 라라벨에서 제공하는 기본 에러 메시지 대신 validator 인스턴스가 사용해야 하는 사용자 정의 에러 메시지를 제공할 수 있습니다. 사용자 지정 메시지를 지정하는 방법에는 여러 가지가 있습니다. 먼저 사용자 정의 메시지를 `Validator::make` 메소드에 세 번째 인자로 전달할 수 있습니다.

    $validator = Validator::make($input, $rules, $messages = [
        'required' => 'The :attribute field is required.',
    ]);

In this example, the `:attribute` placeholder will be replaced by the actual name of the field under validation. You may also utilize other placeholders in validation messages. For example:

이 예제에서 `:attribute` 플레이스홀더는 유효성 검사 중인 필드의 실제 필드 이름으로 대체됩니다. 유효성 검사 메시지에서 다른 플레이스홀더를 활용할 수도 있습니다. 아래의 예제에서 `:other`, `:size`, `:min`, `:max`, `:values`를 눈여겨 보세요. 예를 들어

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

때로는 특정 속성에 대해서만 사용자 정의 에러 메시지를 지정하고자 할 수 있습니다. "점" 표기법을 사용하면 됩니다. 속성의 이름을 먼저 지정하고 그 다음에 규칙을 지정합니다.

    $messages = [
        'email.required' => 'We need to know your email address!',
    ];

<a name="specifying-custom-attribute-values"></a>
#### Specifying Custom Attribute Values
#### 사용자 정의 속성 값 지정

Many of Laravel's built-in error messages include an `:attribute` placeholder that is replaced with the name of the field or attribute under validation. To customize the values used to replace these placeholders for specific fields, you may pass an array of custom attributes as the fourth argument to the `Validator::make` method:

라라벨에 내장된 많은 에러 메시지에는 검증 중인 필드 또는 속성의 이름으로 대체되는 `:attribute` 플레이스홀더가 포함되어 있습니다. 특정 필드에 대한 에러 메시지의 정의하는 부분에서 플레이스홀더를 대체하는 데 사용되는 값을 사용자 정의된 값으로 바꾸려면 `Validator::make` 메서드에 대한 네 번째 인자로 사용자 지정한 속성 배열을 전달하면 됩니다. 아래의 예제는 'email' 속성의 유효성 검사가 실패한 경우 에러 메시지의 플레이스 홀더에 'email'이 아닌 'email address'를 표기하도록 지정합니다.

    $validator = Validator::make($input, $rules, $messages, [
        'email' => 'email address',
    ]);

<a name="after-validation-hook"></a>
### After Validation Hook
### 유효성 검사 이후에 후킹하기

You may also attach callbacks to be run after validation is completed. This allows you to easily perform further validation and even add more error messages to the message collection. To get started, call the `after` method on a validator instance:

유효성 검사가 완료된 후 실행할 콜백을 첨부할 수도 있습니다. 이를 통해 추가 유효성 검사를 쉽게 수행하고 메시지 컬렉션에 더 많은 에러 메시지를 추가할 수 있습니다. 시작하려면 validator 인스턴스에서 `after` 메서드를 호출하세요.

    use Illuminate\Support\Facades;
    use Illuminate\Validation\Validator;

    $validator = Facades\Validator::make(/* ... */);

    $validator->after(function (Validator $validator) {
        if ($this->somethingElseIsInvalid()) {
            $validator->errors()->add(
                'field', 'Something is wrong with this field!'
            );
        }
    });

    if ($validator->fails()) {
        // ...
    }

<a name="working-with-validated-input"></a>
## Working With Validated Input
## 검증된 입력값 사용하기

After validating incoming request data using a form request or a manually created validator instance, you may wish to retrieve the incoming request data that actually underwent validation. This can be accomplished in several ways. First, you may call the `validated` method on a form request or validator instance. This method returns an array of the data that was validated:

form request 또는 수동으로 생성된 validator 인스턴스를 사용하여 들어오는 리퀘스트 데이터의 유효성을 검사한 후 실제로 유효성 검사를 거친 들어오는 리퀘스트 데이터를 검색할 수 있습니다. 이것은 여러 가지 방법으로 수행할 수 있습니다. 먼저 form request 또는 validator 인스턴스에서 `validated` 메서드를 호출할 수 있습니다. 이 메서드는 검증된 데이터의 배열을 반환합니다.

    $validated = $request->validated();

    $validated = $validator->validated();

Alternatively, you may call the `safe` method on a form request or validator instance. This method returns an instance of `Illuminate\Support\ValidatedInput`. This object exposes `only`, `except`, and `all` methods to retrieve a subset of the validated data or the entire array of validated data:

또는 form request 또는 validator 인스턴스에서 `safe` 메서드를 호출할 수 있습니다. 이 메소드는 `Illuminate\Support\ValidatedInput`의 인스턴스를 반환합니다. 이 객체는 검증된 데이터의 하위 집합 또는 검증된 데이터의 전체 배열을 취득하기 위해 `only`, `except` 및 `all` 메서드에 exposes-공개할 대상을 지정합니다.

    $validated = $request->safe()->only(['name', 'email']);

    $validated = $request->safe()->except(['name', 'email']);

    $validated = $request->safe()->all();

In addition, the `Illuminate\Support\ValidatedInput` instance may be iterated over and accessed like an array:

또한 `Illuminate\Support\ValidatedInput` 인스턴스는 배열처럼 반복하거나 액세스할 수 있습니다.

    // Validated data may be iterated...
    foreach ($request->safe() as $key => $value) {
        // ...
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

`Validator` 인스턴스에서 `errors` 메서드를 호출하면 오류 메시지 작업을 위한 편하게 사용할 수 있는 다양한 메소드를 가진 `Illuminate\Support\MessageBag` 인스턴스를 받게 됩니다. 모든 view-뷰에서 특별한 설정 없이 자동으로 전달되어 사용되는 `$errors` 변수 역시 `MessageBag` 클래스의 인스턴스입니다.

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
        // ...
    }

If you are validating an array form field, you may retrieve all of the messages for each of the array elements using the `*` character:

배열형태의 form 필드에 대해서 유효성 검사를 하려면, 배열의 각각의 요소는 `*` 문자열을 사용하여 모든 메세지를 조회할 수 있습니다.

    foreach ($errors->get('attachments.*') as $message) {
        // ...
    }

<a name="retrieving-all-error-messages-for-all-fields"></a>
#### Retrieving All Error Messages For All Fields
#### 모든 필드에 대한 모든 에러 메세지 조회하기

To retrieve an array of all messages for all fields, use the `all` method:

모든 필드에 대한 모든 에러 메세지를 조회하기 위해서는 `all` 메소들 사용하면 됩니다.

    foreach ($errors->all() as $message) {
        // ...
    }

<a name="determining-if-messages-exist-for-a-field"></a>
#### Determining If Messages Exist For A Field
#### 하나의 필드에 대하여 에러 메시지가 존재하는지 검사하기

The `has` method may be used to determine if any error messages exist for a given field:

`has` 메소드는 특정 필드에 대한 에러 메세지가 존재하는지 확인하는데 사용합니다.

    if ($errors->has('email')) {
        // ...
    }

<a name="specifying-custom-messages-in-language-files"></a>
### Specifying Custom Messages In Language Files
### 언어 파일에서 사용자 정의 메시지 지정하기

Laravel's built-in validation rules each have an error message that is located in your application's `lang/en/validation.php` file. Within this file, you will find a translation entry for each validation rule. You are free to change or modify these messages based on the needs of your application.

라라벨의 내장 유효성 검사 규칙 각각에 대한 에러메시지는 애플리케이션의 `lang/en/validation.php` 파일에 정의되어 있습니다. 이 파일 내에서 각 유효성 검사 규칙에 대한 번역 항목을 찾을 수 있습니다. 애플리케이션의 필요에 따라 이러한 메시지를 자유롭게 변경하거나 수정할 수 있습니다.

In addition, you may copy this file to another language directory to translate the messages for your application's language. To learn more about Laravel localization, check out the complete [localization documentation](/docs/{{version}}/localization).

또한 이 파일을 다른 언어 디렉토리에 복사하여 애플리케이션 언어로 메시지를 번역할 수 있습니다. 라라벨 현지화에 대해 자세히 알아보려면 전체 [현지화 문서](/docs/{{version}}/localization)를 확인하세요.

> **Warning**
> By default, the Laravel application skeleton does not include the `lang` directory. If you would like to customize Laravel's language files, you may publish them via the `lang:publish` Artisan command.

기본적으로 라라벨 애플리케이션을 처음 설치할 때 생성되는 파일 및 폴더 구조인 스켈레톤에는 `lang` 디렉터리가 포함되어 있지 않습니다. 라라벨의 언어 파일을 사용자 정의하려면 `lang:publish` 아티즌 명령을 통해 publish-게시할 수 있습니다.

<a name="custom-messages-for-specific-attributes"></a>
#### Custom Messages For Specific Attributes
#### 특정 속성에 대한 사용자 정의 메시지

You may customize the error messages used for specified attribute and rule combinations within your application's validation language files. To do so, add your message customizations to the `custom` array of your application's `lang/xx/validation.php` language file:

애플리케이션의 유효성 검사 언어 파일 내에서 지정된 속성 및 규칙 조합에 사용되는 에러 메시지를 사용자 정의할 수 있습니다. 그렇게 하려면 애플리케이션의 `lang/xx/validation.php` 언어 파일의 `custom` 배열에 메시지 사용자 정의를 추가하십시오.

    'custom' => [
        'email' => [
            'required' => 'We need to know your email address!',
            'max' => 'Your email address is too long!'
        ],
    ],

<a name="specifying-attribute-in-language-files"></a>
### Specifying Attributes In Language Files
### 언어 파일에 속성 지정하기

Many of Laravel's built-in error messages include an `:attribute` placeholder that is replaced with the name of the field or attribute under validation. If you would like the `:attribute` portion of your validation message to be replaced with a custom value, you may specify the custom attribute name in the `attributes` array of your `lang/xx/validation.php` language file:

라라벨에 내장된 많은 에러 메시지에는 검증 중인 필드 또는 속성의 이름으로 대체되는 `:attribute` 플레이스홀더가 포함되어 있습니다. 유효성 검사 메시지의 `:attribute` 부분을 사용자 정의 값으로 바꾸려면 `lang/xx/validation.php` 언어 파일의 `attributes` 배열에 사용자 정의 속성 이름을 지정할 수 있습니다. 아래의 예제는 'email' 속성의 유효성 검사가 실패한 경우 에러 메시지의 플레이스 홀더에 'email'이 아닌 'email address'를 표기하도록 지정합니다.

    'attributes' => [
        'email' => 'email address',
    ],

> **Warning**
> By default, the Laravel application skeleton does not include the `lang` directory. If you would like to customize Laravel's language files, you may publish them via the `lang:publish` Artisan command.

기본적으로 라라벨 애플리케이션을 처음 설치할 때 생성되는 파일 및 폴더 구조인 스켈레톤에는 `lang` 디렉터리가 포함되어 있지 않습니다. 라라벨의 언어 파일을 사용자 정의하려면 `lang:publish` 아티즌 명령을 통해 publish-게시할 수 있습니다.

<a name="specifying-values-in-language-files"></a>
### Specifying Values In Language Files
### 언어 파일에 값 지정하기

Some of Laravel's built-in validation rule error messages contain a `:value` placeholder that is replaced with the current value of the request attribute. However, you may occasionally need the `:value` portion of your validation message to be replaced with a custom representation of the value. For example, consider the following rule that specifies that a credit card number is required if the `payment_type` has a value of `cc`:

라라벨의 내장 유효성 검사 규칙의 에러 메시지 중 일부는 리퀘스트 객체의 현재 검사가 되고 있는 속성이 가진 값으로 대체되는 `:value` 플레이스홀더를 포함합니다. 그러나 때때로 유효성 검사 메시지의 `:value` 부분을 값의 사용자 지정 표현으로 대체해야 할 수도 있습니다. 예를 들어 `payment_type`의 값이 'credit card' 를 의미하는 `cc`인 경우 신용카드 번호가 필요하다고 지정하는 다음 규칙을 생각해 보겠습니다.

    Validator::make($request->all(), [
        'credit_card_number' => 'required_if:payment_type,cc'
    ]);

If this validation rule fails, it will produce the following error message:

이 유효성 검사 규칙이 실패하면 다음과 같은 에러 메시지가 나타납니다.

```none
The credit card number field is required when payment type is cc.
```

Instead of displaying `cc` as the payment type value, you may specify a more user-friendly value representation in your `lang/xx/validation.php` language file by defining a `values` array:

결제 유형 값으로 `cc`를 표시하는 대신 `values` 배열을 정의하여 `lang/xx/validation.php` 언어 파일에 보다 사용자 친화적인 값 표현을 지정할 수 있습니다. 이렇게 하면 에러 메시지에 'cc'가 아닌 'credit card'가 표시됩니다.

    'values' => [
        'payment_type' => [
            'cc' => 'credit card'
        ],
    ],

> **Warning**
> By default, the Laravel application skeleton does not include the `lang` directory. If you would like to customize Laravel's language files, you may publish them via the `lang:publish` Artisan command.

기본적으로 라라벨 애플리케이션을 처음 설치할 때 생성되는 파일 및 폴더 구조인 스켈레톤에는 `lang` 디렉터리가 포함되어 있지 않습니다. 라라벨의 언어 파일을 사용자 정의하려면 `lang:publish` 아티즌 명령을 통해 publish-게시할 수 있습니다.

After defining this value, the validation rule will produce the following error message:

이 값을 정의한 후 유효성 검사 규칙은 다음 에러 메시지를 생성합니다.

```none
The credit card number field is required when payment type is credit card.
```

<a name="available-validation-rules"></a>
## Available Validation Rules
## 사용가능한 유효성 검사 규칙

Below is a list of all available validation rules and their function:

다음은 사용 가능한 모든 유효성 검사 규칙과 그 기능의 목록입니다.

<style>
    .collection-method-list > p {
        columns: 10.8em 3; -moz-columns: 10.8em 3; -webkit-columns: 10.8em 3;
    }

    .collection-method-list a {
        display: block;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
    }
</style>

<div class="collection-method-list" markdown="1">

[Accepted](#rule-accepted)
[Accepted If](#rule-accepted-if)
[Active URL](#rule-active-url)
[After (Date)](#rule-after)
[After Or Equal (Date)](#rule-after-or-equal)
[Alpha](#rule-alpha)
[Alpha Dash](#rule-alpha-dash)
[Alpha Numeric](#rule-alpha-num)
[Array](#rule-array)
[Ascii](#rule-ascii)
[Bail](#rule-bail)
[Before (Date)](#rule-before)
[Before Or Equal (Date)](#rule-before-or-equal)
[Between](#rule-between)
[Boolean](#rule-boolean)
[Confirmed](#rule-confirmed)
[Current Password](#rule-current-password)
[Date](#rule-date)
[Date Equals](#rule-date-equals)
[Date Format](#rule-date-format)
[Decimal](#rule-decimal)
[Declined](#rule-declined)
[Declined If](#rule-declined-if)
[Different](#rule-different)
[Digits](#rule-digits)
[Digits Between](#rule-digits-between)
[Dimensions (Image Files)](#rule-dimensions)
[Distinct](#rule-distinct)
[Doesnt Start With](#rule-doesnt-start-with)
[Doesnt End With](#rule-doesnt-end-with)
[Email](#rule-email)
[Ends With](#rule-ends-with)
[Enum](#rule-enum)
[Exclude](#rule-exclude)
[Exclude If](#rule-exclude-if)
[Exclude Unless](#rule-exclude-unless)
[Exclude With](#rule-exclude-with)
[Exclude Without](#rule-exclude-without)
[Exists (Database)](#rule-exists)
[File](#rule-file)
[Filled](#rule-filled)
[Greater Than](#rule-gt)
[Greater Than Or Equal](#rule-gte)
[Image (File)](#rule-image)
[In](#rule-in)
[In Array](#rule-in-array)
[Integer](#rule-integer)
[IP Address](#rule-ip)
[JSON](#rule-json)
[Less Than](#rule-lt)
[Less Than Or Equal](#rule-lte)
[Lowercase](#rule-lowercase)
[MAC Address](#rule-mac)
[Max](#rule-max)
[Max Digits](#rule-max-digits)
[MIME Types](#rule-mimetypes)
[MIME Type By File Extension](#rule-mimes)
[Min](#rule-min)
[Min Digits](#rule-min-digits)
[Missing](#rule-missing)
[Missing If](#rule-missing-if)
[Missing Unless](#rule-missing-unless)
[Missing With](#rule-missing-with)
[Missing With All](#rule-missing-with-all)
[Multiple Of](#rule-multiple-of)
[Not In](#rule-not-in)
[Not Regex](#rule-not-regex)
[Nullable](#rule-nullable)
[Numeric](#rule-numeric)
[Password](#rule-password)
[Present](#rule-present)
[Prohibited](#rule-prohibited)
[Prohibited If](#rule-prohibited-if)
[Prohibited Unless](#rule-prohibited-unless)
[Prohibits](#rule-prohibits)
[Regular Expression](#rule-regex)
[Required](#rule-required)
[Required If](#rule-required-if)
[Required Unless](#rule-required-unless)
[Required With](#rule-required-with)
[Required With All](#rule-required-with-all)
[Required Without](#rule-required-without)
[Required Without All](#rule-required-without-all)
[Required Array Keys](#rule-required-array-keys)
[Same](#rule-same)
[Size](#rule-size)
[Sometimes](#validating-when-present)
[Starts With](#rule-starts-with)
[String](#rule-string)
[Timezone](#rule-timezone)
[Unique (Database)](#rule-unique)
[Uppercase](#rule-uppercase)
[URL](#rule-url)
[ULID](#rule-ulid)
[UUID](#rule-uuid)

</div>

<a name="rule-accepted"></a>
#### accepted
#### accepted

The field under validation must be `"yes"`, `"on"`, `1`, or `true`. This is useful for validating "Terms of Service" acceptance or similar fields.

필드의 값이 `"yes"`, `"on"`, `1`, 또는 `true` 여야 합니다. 이 것은 "이용약관" 동의와 같은 필드의 검사에 유용합니다.

<a name="rule-accepted-if"></a>
#### accepted_if:anotherfield,value,...
#### accepted_if:anotherfield,value,...

The field under validation must be `"yes"`, `"on"`, `1`, or `true` if another field under validation is equal to a specified value. This is useful for validating "Terms of Service" acceptance or similar fields.

검증 중인 다른 필드가 지정된 값과 같은 경우 검증 중인 필드는 `"yes"`, `"on"`, `1` 또는 `true`여야 합니다. 이는 "이용약관" 동의 또는 유사한 필드의 유효성을 검사하는 데 유용합니다.

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

유효성 검사 중인 필드는 완벽하게 알파벳 문자로만 구성되어야 합니다.

The field under validation must be entirely Unicode alphabetic characters contained in [`\p{L}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AL%3A%5D&g=&i=) and [`\p{M}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AM%3A%5D&g=&i=).

유효성 검사 대상 필드는 [`\p{L}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AL%3A%5D&g=&i=) 및 [`\p{M}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AM%3A%5D&g=&i=)에 포함되어 있는 유니코드 알파벳 문자로만 구성되어야합니다.

(역자주: 영문 알파벳만을 의미하지 않고, 숫자나 기호가 아닌경우에 해당하여, 한글도 허용합니다.)

To restrict this validation rule to characters in the ASCII range (`a-z` and `A-Z`), you may provide the `ascii` option to the validation rule:

이 유효성 검사 규칙을 ASCII 범위의 문자(`a-z` 및 `A-Z`)로 제한하려면 유효성 검사 규칙에 `ascii` 옵션을 제공하면 됩니다.

```php
'username' => 'alpha:ascii',
```

<a name="rule-alpha-dash"></a>
#### alpha_dash
#### alpha_dash

The field under validation must be entirely Unicode alpha-numeric characters contained in [`\p{L}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AL%3A%5D&g=&i=), [`\p{M}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AM%3A%5D&g=&i=), [`\p{N}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AN%3A%5D&g=&i=), as well as ASCII dashes (`-`) and ASCII underscores (`_`).

검증 중인 필드는 다음에 포함된 전체 유니코드 alpha-numeric 문자여야 합니다. [`\p{L}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AL%3A%5D&g=&i=)
, [`\p{M}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AM%3A%5D&g=&i=)
, [`\p{N}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AN%3A%5D&g=&i=)
에 포함된 유니코드 및 숫자와 ASCII 대시(`-`) 및 ASCII 밑줄(`_`)로만 구성되어야 합니다.

(역자주: 영문 알파벳만을 의미하지 않고, 숫자나 기호가 아닌경우에 해당하여, 한글도 허용합니다.)

To restrict this validation rule to characters in the ASCII range (`a-z` and `A-Z`), you may provide the `ascii` option to the validation rule:

이 유효성 검사 규칙을 ASCII 범위의 문자(`a-z` 및 `A-Z`)로 제한하려면 유효성 검사 규칙에 `ascii` 옵션을 제공하면 됩니다.

```php
'username' => 'alpha_dash:ascii',
```

<a name="rule-alpha-num"></a>
#### alpha_num
#### alpha_num

The field under validation must be entirely Unicode alpha-numeric characters contained in [`\p{L}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AL%3A%5D&g=&i=), [`\p{M}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AM%3A%5D&g=&i=), and [`\p{N}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AN%3A%5D&g=&i=).

검증 중인 필드는 다음에 포함된 전체 유니코드 alpha-numeric 문자여야 합니다. [`\p{L}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AL%3A%5D&g=&i=)
, [`\p{M}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AM%3A%5D&g=&i=)
, [`\p{N}`](https://util.unicode.org/UnicodeJsps/list-unicodeset.jsp?a=%5B%3AN%3A%5D&g=&i=)
에 포함된 유니코드 및 숫자로만 구성되어야 합니다.

(역자주: 영문 알파벳만을 의미하지 않고, 숫자나 기호가 아닌경우에 해당하여, 한글도 허용합니다.)

To restrict this validation rule to characters in the ASCII range (`a-z` and `A-Z`), you may provide the `ascii` option to the validation rule:

이 유효성 검사 규칙을 ASCII 범위의 문자(`a-z` 및 `A-Z`)로 제한하려면 유효성 검사 규칙에 `ascii` 옵션을 제공하면 됩니다.

```php
'username' => 'alpha_num:ascii',
```

<a name="rule-array"></a>
#### array
#### array

The field under validation must be a PHP `array`.

필드의 값이 반드시 PHP `array` 형태이어야 합니다.

When additional values are provided to the `array` rule, each key in the input array must be present within the list of values provided to the rule. In the following example, the `admin` key in the input array is invalid since it is not contained in the list of values provided to the `array` rule:

`array` 규칙에 추가 값이 제공되면 input-입력 배열의 각 키는 규칙에 제공된 값 목록 내에 있어야 합니다. 다음 예에서 input-입력 배열의 `admin` 키는 `array` 규칙에 제공된 값인 `array:name,username`의 목록에 포함되어 있지 않기 때문에 유효하지 않습니다.

    use Illuminate\Support\Facades\Validator;

    $input = [
        'user' => [
            'name' => 'Taylor Otwell',
            'username' => 'taylorotwell',
            'admin' => true,
        ],
    ];

    Validator::make($input, [
        'user' => 'array:name,username',
    ]);

In general, you should always specify the array keys that are allowed to be present within your array.

일반적으로 검사 대상이 되어야하는 필드는 항상 유효성 검사 규칙을 나열하는 배열 내에 배열의 키로 지정하여 허가하지 않은 값이 통과되지 않도록 합니다.

<a name="rule-ascii"></a>
#### ascii
#### ascii

The field under validation must be entirely 7-bit ASCII characters.

필드의 값이 완벽하게 7비트의 ASCII 문자로 이루어져야 합니다.

<a name="rule-bail"></a>
#### bail
#### bail

Stop running validation rules for the field after the first validation failure.

첫 번째 유효성 검사 실패 후 필드에 대한 유효성 검사 규칙 실행을 중지합니다.

While the `bail` rule will only stop validating a specific field when it encounters a validation failure, the `stopOnFirstFailure` method will inform the validator that it should stop validating all attributes once a single validation failure has occurred:

`bail` 규칙은 유효성 검사 실패가 발생한 경우에만 특정 필드의 유효성 검사를 중지하지만, `stopOnFirstFailure` 메서드는 단일 유효성 검사 실패가 발생하면 모든 속성의 유효성 검사를 중지해야 한다고 validator에 지시합니다.

    if ($validator->stopOnFirstFailure()->fails()) {
        // ...
    }

<a name="rule-before"></a>
#### before:_date_
#### before:_date_

The field under validation must be a value preceding the given date. The dates will be passed into the PHP `strtotime` function in order to be converted into a valid `DateTime` instance. In addition, like the [`after`](#rule-after) rule, the name of another field under validation may be supplied as the value of `date`.

검증 중인 필드는 지정된 날짜 이전의 날짜이어야 합니다. 날짜는 유효한 `DateTime` 인스턴스로 변환하기 위해 PHP `strtotime` 함수를 사용합니다. 또한, [`after`](#rule-after) 규칙과 마찬가지로 유효성 검사 중인 다른 필드의 이름이 `date` 값으로 제공될 수 있습니다.

<a name="rule-before-or-equal"></a>
#### before\_or\_equal:_date_
#### before\_or\_equal:_date_

The field under validation must be a value preceding or equal to the given date. The dates will be passed into the PHP `strtotime` function in order to be converted into a valid `DateTime` instance. In addition, like the [`after`](#rule-after) rule, the name of another field under validation may be supplied as the value of `date`.

검증 중인 필드는 지정된 날짜 이전 또는 같은 값이어야 합니다. 날짜는 유효한 `DateTime` 인스턴스로 변환하기 위해 PHP `strtotime` 함수를 사용합니다. 또한, [`after`](#rule-after) 규칙과 마찬가지로 유효성 검사 중인 다른 필드의 이름이 `date` 값으로 제공될 수 있습니다.

<a name="rule-between"></a>
#### between:_min_,_max_
#### between:_min_,_max_

The field under validation must have a size between the given _min_ and _max_ (inclusive). Strings, numerics, arrays, and files are evaluated in the same fashion as the [`size`](#rule-size) rule.

검증 중인 필드의 값이, 주어진 _min_ 과 _max_ 의 사이(포함)의 값이어야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 규칙에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-boolean"></a>
#### boolean
#### boolean

The field under validation must be able to be cast as a boolean. Accepted input are `true`, `false`, `1`, `0`, `"1"`, and `"0"`.

검사 중인 필드의 값이 반드시 불리언으로 형변환이 될 수 있어야 합니다. 허용되는 값은 `true`, `false`, `1`, `0`, `"1"`, `"0"` 입니다.

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
#### date_format:_format_,...
#### date_format:_format_,...

The field under validation must match one of the given _formats_. You should use **either** `date` or `date_format` when validating a field, not both. This validation rule supports all formats supported by PHP's [DateTime](https://www.php.net/manual/en/class.datetime.php) class.

검증 중인 필드는 지정된 _formats_ 중 하나와 일치해야 합니다. 필드의 유효성을 검사할 때 `date` 또는 `date_format` 중 **하나만** 사용해야 합니다. 이 유효성 검사 규칙은 PHP의 [DateTime](https://www.php.net/manual/en/class.datetime.php) 클래스에서 지원하는 모든 형식을 지원합니다.

<a name="rule-decimal"></a>
#### decimal:_min_,_max_
#### decimal:_min_,_max_

The field under validation must be numeric and must contain the specified number of decimal places:

검증 중인 필드는 numeric이고 지정된 소수점 자릿수를 포함해야 합니다.

    // Must have exactly two decimal places (9.99)...
    'price' => 'decimal:2'

    // Must have between 2 and 4 decimal places...
    'price' => 'decimal:2,4'

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

The integer under validation must have an exact length of _value_.

유효성 검증하는 integer 길이가 정확히 _value_ 이어야 합니다.

<a name="rule-digits-between"></a>
#### digits_between:_min_,_max_
#### digits_between:_min_,_max_

The integer validation must have a length between the given _min_ and _max_.

정수 유효성 검증은 주어진 _min_ 과 _max_ 사이의 길이를 가져야 합니다.

<a name="rule-dimensions"></a>
#### dimensions
#### dimensions

The file under validation must be an image meeting the dimension constraints as specified by the rule's parameters:

검중 중인 파일은 필드의 값이 규칙에 지정된 파라미터들을 만족하는 이미지여야 합니다.

    'avatar' => 'dimensions:min_width=100,min_height=200'

Available constraints are: _min\_width_, _max\_width_, _min\_height_, _max\_height_, _width_, _height_, _ratio_.

사용가능한 제약은:  _min\_width_, _max\_width_, _min\_height_, _max\_height_, _width_, _height_, _ratio_ 입니다.

A _ratio_ constraint should be represented as width divided by height. This can be specified either by a fraction like `3/2` or a float like `1.5`:

_ratio_ 제약 조건은 너비를 높이로 나눈 값으로 표시해야 합니다. 이것은 `3/2`와 같은 분수 또는 `1.5`와 같은 부동 소수점으로 지정할 수 있습니다.

    'avatar' => 'dimensions:ratio=3/2'

Since this rule requires several arguments, you may use the `Rule::dimensions` method to fluently construct the rule:

이 규칙은 여러개의 인자를 필요로 하는데, 다음처럼 `Rule:dimensions` 메소드를 사용하여 우아하게 규칙을 생성할 수 있습니다.

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

`Distinct`는 기본적으로 느슨한 변수 비교를 사용합니다. 엄격한 비교를 사용하려면 유효성 검사 규칙 정의에 `strict` 매개변수를 추가하면 됩니다.

    'foo.*.id' => 'distinct:strict'

You may add `ignore_case` to the validation rule's arguments to make the rule ignore capitalization differences:

유효성 검사 규칙의 인자에 `ignore_case`를 추가하여 규칙이 대소문자 차이를 무시하도록 할 수 있습니다.

    'foo.*.id' => 'distinct:ignore_case'

<a name="rule-doesnt-start-with"></a>
#### doesnt_start_with:_foo_,_bar_,...
#### doesnt_start_with:_foo_,_bar_,...

The field under validation must not start with one of the given values.

유효성 검사 중인 필드는 지정된 값 중 하나로 시작해서는 안 됩니다.

<a name="rule-doesnt-end-with"></a>
#### doesnt_end_with:_foo_,_bar_,...
#### doesnt_end_with:_foo_,_bar_,...

The field under validation must not end with one of the given values.
유효성 검사 중인 필드는 지정된 값 중 하나로 끝나지 않아야 합니다.

<a name="rule-email"></a>
#### email
#### email

The field under validation must be formatted as an email address. This validation rule utilizes the [`egulias/email-validator`](https://github.com/egulias/EmailValidator) package for validating the email address. By default, the `RFCValidation` validator is applied, but you can apply other validation styles as well:

검증 중인 필드는 이메일 주소 형식이어야 합니다. 이 검증 규칙은 이메일 주소 검증을 위해 [`egulias/email-validator`](https://github.com/egulias/EmailValidator) 패키지를 사용합니다. 기본적으로 `RFCValidation` validator가 적용되지만 다른 유효성 검사 스타일도 적용할 수 있습니다.

    'email' => 'email:rfc,dns'

The example above will apply the `RFCValidation` and `DNSCheckValidation` validations. Here's a full list of validation styles you can apply:

위의 예시는 `RFCValidation`와 `DNSCheckValidation` 유효성 검사를 적용합니다. 가능한 모든 유효성 검사 방법은 아래를 참조하십시오.

<div class="content-list" markdown="1">

- `rfc`: `RFCValidation`
- `strict`: `NoRFCWarningsValidation`
- `dns`: `DNSCheckValidation`
- `spoof`: `SpoofCheckValidation`
- `filter`: `FilterEmailValidation`
- `filter_unicode`: `FilterEmailValidation::unicode()`

</div>

The `filter` validator, which uses PHP's `filter_var` function, ships with Laravel and was Laravel's default email validation behavior prior to Laravel version 5.8.

PHP의 `filter_var` 기능을 사용하는 `filter` validator는 라라벨과 함께 제공되며 라라벨 버전 5.8 이전에 라라벨의 기본 이메일 유효성 검사 동작이었습니다.

> **Warning**  
> The `dns` and `spoof` validators require the PHP `intl` extension.

> **Warning**  
> `dns` 및 `spoof` validator는 PHP `intl` 확장이 필요합니다.

<a name="rule-ends-with"></a>
#### ends_with:_foo_,_bar_,...
#### ends_with:_foo_,_bar_,...

The field under validation must end with one of the given values.

검증 중인 필드의 값이 주어진 값 중 하나로 끝나야합니다.

<a name="rule-enum"></a>
#### enum
#### enum

The `Enum` rule is a class based rule that validates whether the field under validation contains a valid enum value. The `Enum` rule accepts the name of the enum as its only constructor argument:

`Enum` 규칙은 클래스 기반 규칙입니다. `Enum` 규칙은 유효성 검사 중인 필드가 enum 값에 포함되어 있는 유효한 값인지 검증합니다. `Enum` 규칙은 생성자 인자로 enum의 이름만을 받습니다. 아래 예시에서 ServerStatus라는 enum 형식의 클래스를 인스턴스화한 값을 받아 input-입력 데이터의 'status' 필드의 값이 ServerStatus라는 enum 클래스에 열거된 값과 일치하는지 확인합니다.

    use App\Enums\ServerStatus;
    use Illuminate\Validation\Rules\Enum;

    $request->validate([
        'status' => [new Enum(ServerStatus::class)],
    ]);

> **Warning**
> Enums are only available on PHP 8.1+.

> **Warning**
> enum은 PHP 8.1 이상에서만 사용할 수 있습니다.

<a name="rule-exclude"></a>
#### exclude
#### exclude

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods.

검증 중인 필드는 유효성 검사를 수행하지 않고 `validate` 및 `validated` 메서드에서 리퀘스트 데이터를 반환할 때 해당 필드를 제외합니다.

<a name="rule-exclude-if"></a>
#### exclude_if:_anotherfield_,_value_
#### exclude_if:_anotherfield_,_value_

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods if the _anotherfield_ field is equal to _value_.

다른필드 _anotherfield_ 의 필드값이 _value_ 와 같은 경우 검증 중인 필드는 유효성 검사를 수행하지 않고 `validate` 및 `validated` 메서드에서 리퀘스트 데이터를 반환할 때 해당 필드를 제외합니다.

If complex conditional exclusion logic is required, you may utilize the `Rule::excludeIf` method. This method accepts a boolean or a closure. When given a closure, the closure should return `true` or `false` to indicate if the field under validation should be excluded:

복잡한 조건부 제외 로직이 필요하다면 `Rule:excludeIf` 메서드를 활용할 수 있습니다. 이 메서드는 부울 또는 클로저를 받습니다. 클로저가 주어지는 경우 클로저는 검증하려는 필드가 제외되어야 하는지 나타내기 위해 `true` 나 `false` 를 반환해야 합니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($request->all(), [
        'role_id' => Rule::excludeIf($request->user()->is_admin),
    ]);

    Validator::make($request->all(), [
        'role_id' => Rule::excludeIf(fn () => $request->user()->is_admin),
    ]);

<a name="rule-exclude-unless"></a>
#### exclude_unless:_anotherfield_,_value_
#### exclude_unless:_anotherfield_,_value_

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods unless _anotherfield_'s field is equal to _value_. If _value_ is `null` (`exclude_unless:name,null`), the field under validation will be excluded unless the comparison field is `null` or the comparison field is missing from the request data.

유효성 검사 중인 필드는 다른 필드 _anotherfield_ 의 필드값이 _value_ 와 같지 않은 한 `validate` 및 `validated` 메서드가 반환하는 리퀘스트 데이터에서 해당 필드는 제외됩니다. _value_ 가 `null`(`exclude_unless:name,null`)이면 비교 필드가 `null`이 아니거나 리퀘스트 데이터에서 비교 필드가 누락되지 않는 한 유효성 검사 중인 필드가 제외됩니다.

<a name="rule-exclude-with"></a>
#### exclude_with:_anotherfield_
#### exclude_with:_anotherfield_

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods if the _anotherfield_ field is present.

_anotherfield_ 필드가 존재하면 검증 중인 필드는 `validate` 와 `validated` 메서드에 의해 반환되는 리퀘스트 데이터에서 제외될 것입니다.

<a name="rule-exclude-without"></a>
#### exclude_without:_anotherfield_
#### exclude_without:_anotherfield_

The field under validation will be excluded from the request data returned by the `validate` and `validated` methods if the _anotherfield_ field is not present.

유효성 검사 중인 필드는 _anotherfield_ 필드가 없으면 `validate` 및 `validated` 메서드가 반환하는 리퀘스트 데이터에서 제외됩니다.

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

`column` 옵션을 지정하지 않으면 필드 이름이 사용됩니다. 따라서 이 경우 규칙은 `states` 데이터베이스 테이블의 `state` 컬럼에서 리퀘스트의 `state` 속성 값과 일치하는 값의 레코드가 포함되어 있는지 확인합니다.

<a name="specifying-a-custom-column-name"></a>
#### Specifying A Custom Column Name
#### Specifying A Custom Column Name

You may explicitly specify the database column name that should be used by the validation rule by placing it after the database table name:

레코드에 값이 있는지 확인하기 위해 대상 컬럼을 지정할 때, 데이터베이스 테이블 이름을 바로 뒤에 나열하여, 지정한 컬럼 이름이 어느 테이블에 속해 있는지 명시적으로 지정할 수 있습니다.

    'state' => 'exists:states,abbreviation'

Occasionally, you may need to specify a specific database connection to be used for the `exists` query. You can accomplish this by prepending the connection name to the table name:

경우에 따라 `exists` 쿼리에 사용할 특정 데이터베이스 커넥션을 지정해야 할 수도 있습니다. 테이블 이름 앞에 커넥션 이름을 추가하여 이를 수행할 수 있습니다.

    'email' => 'exists:connection.staff,email'

Instead of specifying the table name directly, you may specify the Eloquent model which should be used to determine the table name:

테이블 이름을 직접 지정하는 대신 테이블 이름을 결정하는 데 사용해야하는 엘로퀀트 모델을 지정할 수 있습니다.

    'user_id' => 'exists:App\Models\User,id'

If you would like to customize the query executed by the validation rule, you may use the `Rule` class to fluently define the rule. In this example, we'll also specify the validation rules as an array instead of using the `|` character to delimit them:

유효성 검사 규칙에 의해서 실행되는 쿼리를 커스터마이징 하고자 한다면, 규칙에 `Rule` 클래스를 정의해서 사용할 수 있습니다. 다음 예제에서는 `|` 문자를 구분자로 사용하는 대신에 유효성 검사 규칙을 배열로 지정하고 있습니다.

    use Illuminate\Database\Query\Builder;
    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'email' => [
            'required',
            Rule::exists('staff')->where(function (Builder $query) {
                return $query->where('account_id', 1);
            }),
        ],
    ]);

You may explicitly specify the database column name that should be used by the `exists` rule generated by the `Rule::exists` method by providing the column name as the second argument to the `exists` method:

`exists` 메서드의 두 번째 인자로 컬럼명을 제공함으로써 `Rule::exists` 메서드에 의해 생성된 `exists` 규칙에서 사용되는 데이터베이스 컬럼 이름이 어느 테이블에 속해 있는지 명시적으로 지정할 수 있습니다. 아래의 예시는 `states` 컬럼이 `abbreviation` 테이블에 속해 있음을 나타냅니다.

    'state' => Rule::exists('states', 'abbreviation'),

<a name="rule-file"></a>
#### file
#### file

The field under validation must be a successfully uploaded file.

검증 중인 필드는 성공적으로 업로드된 파일이어야 합니다.

<a name="rule-filled"></a>
#### filled
#### filled

The field under validation must not be empty when it is present.

필드가 존재하는 경우 값이 비어있으면 안됩니다.

<a name="rule-gt"></a>
#### gt:_field_
#### gt:_field_

The field under validation must be greater than the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

필드의 값이 지정한 _field_ 의 값보다 커야합니다. 검증 중인 필드와 지정된 두 필드는 동일한 타입이어야 하며, 문자열, numerics, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 계산됩니다.

<a name="rule-gte"></a>
#### gte:_field_
#### gte:_field_

The field under validation must be greater than or equal to the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

필드의 값이 지정한 _field_ 의 값보다 크거나 같아야합니다. 검증 중인 필드와 지정된 두 필드는 동일한 타입이어야 하며, 문자열, numeric, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 계산됩니다.

<a name="rule-image"></a>
#### image
#### image

The file under validation must be an image (jpg, jpeg, png, bmp, gif, svg, or webp).

검증 중인 파일은 이미지(jpg, jpeg, png, bmp, gif, svg 또는 webp)여야 합니다.

<a name="rule-in"></a>
#### in:_foo_,_bar_,...
#### in:_foo_,_bar_,...

The field under validation must be included in the given list of values. Since this rule often requires you to `implode` an array, the `Rule::in` method may be used to fluently construct the rule:

검증 중인 필드는 주어진 목록에 포함돼 있어야 합니다. 이 규칙은 `in:_foo_,_bar_,...`으로 단일한 문자열으로 적는 방법도 있지만 때때로 `Rule::in` 메소드로 배열을 `implode`하는 방식으로 우아하게 구성할 수도 있습니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'zones' => [
            'required',
            Rule::in(['first-zone', 'second-zone']),
        ],
    ]);

When the `in` rule is combined with the `array` rule, each value in the input array must be present within the list of values provided to the `in` rule. In the following example, the `LAS` airport code in the input array is invalid since it is not contained in the list of airports provided to the `in` rule:

`in` 규칙이 `array` 규칙과 결합되면 input-입력 배열의 각 값이 `in` 규칙에 제공된 값 목록 내에 있어야 합니다. 다음 예에서 input-입력 배열의 `LAS` 공항 코드는 `in` 규칙에 제공된 공항 목록에 포함되어 있지 않기 때문에 유효하지 않습니다.

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
        'airports.*' => Rule::in(['NYC', 'LIT']),
    ]);

<a name="rule-in-array"></a>
#### in_array:_anotherfield_.*
#### in_array:_anotherfield_.*

The field under validation must exist in _anotherfield_'s values.

검중 중인 필드의 값은 다른 필드 _anotherfield_ 에 있는 값의 목록에 존재해야 합니다. `_anotherfield_.*`에서 뒤에 `*`가 있는 것은 _anotherfield_ 필드에 속한 값의 목록을 지정하기 위해서입니다.

<a name="rule-integer"></a>
#### integer
#### integer

The field under validation must be an integer.

검증 중인 필드의 값은 정수여야 합니다.

> **Warning**  
> This validation rule does not verify that the input is of the "integer" variable type, only that the input is of a type accepted by PHP's `FILTER_VALIDATE_INT` rule. If you need to validate the input as being a number please use this rule in combination with [the `numeric` validation rule](#rule-numeric).

> **Warning**
> 이 유효성 검사 규칙은 input-입력이 "정수" 변수 유형인지 확인하지 않고 입력이 PHP의 `FILTER_VALIDATE_INT` 규칙에 의해 허용되는 유형인지만 확인합니다. 입력이 숫자인지 확인해야 하는 경우 [numeric 유효성 검사 규칙](#rule-numeric)과 함께 이 규칙을 사용하세요.

<a name="rule-ip"></a>
#### ip
#### ip

The field under validation must be an IP address.

검증 중인 필드의 값이 IP 주소여야 합니다.

<a name="ipv4"></a>
#### ipv4
#### ipv4

The field under validation must be an IPv4 address.

검증 중인 필드의 값이 IPv4 주소여야 합니다.

<a name="ipv6"></a>
#### ipv6
#### ipv6

The field under validation must be an IPv6 address.

검증 중인 필드의 값이 IPv6 주소여야 합니다.

<a name="rule-json"></a>
#### json
#### json

The field under validation must be a valid JSON string.

검증 중인 필드의 값이 유효한 JSON 문자열이어야 합니다.

<a name="rule-lt"></a>
#### lt:_field_
#### lt:_field_

The field under validation must be less than the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

검증 중인 필드는 지정된 _field_의 값보다 작아야 합니다. 검증 중인 필드와 지정한 필드는 동일한 타입이어야 하며, 문자열, numerics, 배열 및 파일은 [`size`](#rule-size) 규칙과 동일한 규칙을 사용하여 평가됩니다.

<a name="rule-lte"></a>
#### lte:_field_
#### lte:_field_

The field under validation must be less than or equal to the given _field_. The two fields must be of the same type. Strings, numerics, arrays, and files are evaluated using the same conventions as the [`size`](#rule-size) rule.

검증 중인 필드의 값이 주어진 다른 필드 _field_ 의 값과 같거나 보다 작아야 합니다. 검증 중인 필드와 지정된 다른 두 필드는 동일한 타입이어야 하며, 문자열, numerics, 배열 그리고 파일 타입은 [`size`](#rule-size) 규칙에 따라서 평가됩니다.

<a name="rule-lowercase"></a>
#### lowercase
#### lowercase

The field under validation must be lowercase.

검증 중인 필드는 소문자여야 합니다.

<a name="rule-mac"></a>
#### mac_address
#### mac_address

The field under validation must be a MAC address.

검증 중인 필드는 MAC 주소여야 합니다.

<a name="rule-max"></a>
#### max:_value_
#### max:_value_

The field under validation must be less than or equal to a maximum _value_. Strings, numerics, arrays, and files are evaluated in the same fashion as the [`size`](#rule-size) rule.

필드의 값이 반드시 최대값인 _value_ 보다 작거나 같아야 합니다. 문자열, numerics, 그리고 파일이 [`size`](#rule-size) 규칙에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-max-digits"></a>
#### max_digits:_value_
#### max_digits:_value_

The integer under validation must have a maximum length of _value_.

검증 중인 정수의 최대 길이는 _value_ 여야 합니다.

<a name="rule-mimetypes"></a>
#### mimetypes:_text/plain_,...
#### mimetypes:_text/plain_,...

The file under validation must match one of the given MIME types:

검증 중인 파일은 주어진 MIME 타입들 중 하나와 일치해야만 합니다.

    'video' => 'mimetypes:video/avi,video/mpeg,video/quicktime'

To determine the MIME type of the uploaded file, the file's contents will be read and the framework will attempt to guess the MIME type, which may be different from the client's provided MIME type.

업로드된 파일의 MIME 유형을 확인하기 위해, 프레임워크는 클라이언트가 업로드한 파일의 내용을 읽고 파일의 MIME 유형을 추측하려고 시도합니다.
클라이언트가 제공한 파일의 MIME 유형과 프레임워크가 추측한 MIME 유형이 다르다면 라라벨이 판단한 MIME 유형을 따르도록 합니다.

<a name="rule-mimes"></a>
#### mimes:_foo_,_bar_,...
#### mimes:_foo_,_bar_,...

The file under validation must have a MIME type corresponding to one of the listed extensions.

검증 중인 파일의 MIME 타입이 주어진 확장자 리스트 중에 하나와 일치해야 합니다.

<a name="basic-usage-of-mime-rule"></a>
#### Basic Usage Of MIME Rule
#### MIME 규칙의 기본 사용법

    'photo' => 'mimes:jpg,bmp,png'

Even though you only need to specify the extensions, this rule actually validates the MIME type of the file by reading the file's contents and guessing its MIME type. A full listing of MIME types and their corresponding extensions may be found at the following location:

확장자만 지정하면 되지만, 이 규칙은 실제로 파일 내용을 읽고 MIME 유형을 추측하여 파일의 MIME 유형을 검증합니다. MIME 유형 및 해당 확장의 전체 목록은 다음 위치에서 확인할 수 있습니다.

[https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

[https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

<a name="rule-min"></a>
#### min:_value_
#### min:_value_

The field under validation must have a minimum _value_. Strings, numerics, arrays, and files are evaluated in the same fashion as the [`size`](#rule-size) rule.

필드의 값이 반드시 최소 _value_ 보다 크거나 같아야 합니다. 문자열, numerics, 그리고 파일이 [`size`](#rule-size) 규칙에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-min-digits"></a>
#### min_digits:_value_
#### min_digits:_value_

The integer under validation must have a minimum length of _value_.
검증 중인 정수의 최소 길이는 _value_ 여야 합니다.

<a name="rule-multiple-of"></a>
#### multiple_of:_value_
#### multiple_of:_value_

The field under validation must be a multiple of _value_.

검증 중인 필드는 _value_ 의 배수여야 합니다.

<a name="rule-missing"></a>
#### missing

The field under validation must not be present in the input data.

검증 중인 필드는 input-입력 데이터에 존재하지 않아야 합니다.

 <a name="rule-missing-if"></a>
 #### missing_if:_anotherfield_,_value_,...

 The field under validation must not be present if the _anotherfield_ field is equal to any _value_.

 검증 중인 필드가 다른 필드 _anotherfield_ 의 값인 _value_ 와 같으면 검증 중인 필드는 존재하지 않아야 합니다.

 <a name="rule-missing-unless"></a>
 #### missing_unless:_anotherfield_,_value_

The field under validation must not be present unless the _anotherfield_ field is equal to any _value_.

검증 중인 필드가 다른 필드 _anotherfield_ 의 값인 _value_ 와 같지 않으면 검증 중인 필드는 존재하지 않아야 합니다.

 <a name="rule-missing-with"></a>
 #### missing_with:_foo_,_bar_,...

 The field under validation must not be present _only if_ any of the other specified fields are present.

 지정된 다른 필드 중 어느 하나라도 존재하면 검증 중인 필드는 존재하지 않아야 합니다. _foo_ _bar_ 는 _only if_ 으로 표현되며 _only if_ 는 '~인 경우에만'이란 뜻을 가지고 있습니다.

 <a name="rule-missing-with-all"></a>
 #### missing_with_all:_foo_,_bar_,...

 The field under validation must not be present _only if_ all of the other specified fields are present.

 지정된 다른 필드 모두가 존재하면 검증 중인 필드는 존재하지 않아야 합니다. _foo_ _bar_ 는 _only if_ 으로 표현되며 _only if_ 는 '~인 경우에만'이란 뜻을 가지고 있습니다.

<a name="rule-not-in"></a>
#### not_in:_foo_,_bar_,...
#### not_in:_foo_,_bar_,...

The field under validation must not be included in the given list of values. The `Rule::notIn` method may be used to fluently construct the rule:

검증 중인 필드는 주어진 목록에 포함되지 않아야 합니다. 이 규칙은 `'not_in:_foo_,_bar_,...'`으로 단일한 문자열으로 적는 방법도 있지만 `Rule::notIn` 메소드를 사용하여 우아하게 구성할 수도 있습니다.

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

검증 중인 필드의 값이 주어진 정규표현식과 매칭되지 않아야 합니다.

Internally, this rule uses the PHP `preg_match` function. The pattern specified should obey the same formatting required by `preg_match` and thus also include valid delimiters. For example: `'email' => 'not_regex:/^.+$/i'`.

이 규칙은 내부적으로 PHP의 `preg_match` 함수를 사용합니다. 지정된 패턴은 `preg_match` 에서 요구하는 것과 동일한 formatting-형식을 따라야하며 유효한 구분 기호(`/`)를 포함해야합니다. 예를 들면 다음과 같습니다. `'email' => 'not_regex:/^.+@.+$/i'`.

> **Warning**  
> When using the `regex` / `not_regex` patterns, it may be necessary to specify your validation rules using an array instead of using `|` delimiters, especially if the regular expression contains a `|` character.

> **Warning**
> `regex` / `not_regex` 패턴을 사용할 때, 특히 정규 표현식에 `|` 문자가 포함된 경우 `|` 구분 기호를 사용하는 대신 배열을 사용하여 유효성 검사 규칙을 지정해야 할 수도 있습니다.

<a name="rule-nullable"></a>
#### nullable
#### nullable

The field under validation may be `null`.

검증 중인 필드는 `null`일 수 있습니다.

<a name="rule-numeric"></a>
#### numeric
#### numeric

The field under validation must be [numeric](https://www.php.net/manual/en/function.is-numeric.php).

유효성 검사 필드는 [numeric](https://www.php.net/manual/en/function.is-numeric.php) 여야 합니다.

<a name="rule-password"></a>
#### password
#### password

The field under validation must match the authenticated user's password.

검증 중인 필드는 인증된 사용자의 비밀번호와 일치해야 합니다.

> **Warning**  
> This rule was renamed to `current_password` with the intention of removing it in Laravel 9. Please use the [Current Password](#rule-current-password) rule instead.

> **Warning**
> 라라벨 9에서 `password` 규칙은 `current_password`로 이름이 변경되었습니다. 이름이 변경되었으므로 기존의 `password` 규칙은 나중에 삭제될 예정입니다. [Current Password](#rule-current-password) 규칙을 대신 사용하세요.

<a name="rule-present"></a>
#### present
#### present

The field under validation must be present in the input data but can be empty.

검증 중인 필드는 input-입력 데이터에 존재해야 하지만 비어 있을 수도 있습니다.

The field under validation must exist in the input data.

검증 중인 필드는 input-입력 데이터에 반드시 존재해야 합니다.

<a name="rule-prohibited"></a>
#### prohibited
#### prohibited

The field under validation must be missing or empty. A field is "empty" if it meets one of the following criteria:

검증 중인 필드가 누락되거나 비어있어야 합니다. 필드는 다음 기준 중 하나를 충족하면 "비어있는" 것으로 간주됩니다.

<div class="content-list" markdown="1">

- The value is `null`.
- The value is an empty string.
- The value is an empty array or empty `Countable` object.
- The value is an uploaded file with an empty path.

- 필드의 값이 `null`입니다.
- 필드의 값이 빈 문자열입니다.
- 필드의 값이 빈 배열이거나 빈 `Countable` 객체입니다.
- 필드의 값이 경로가 없는 업로드된 파일입니다.

</div>

<a name="rule-prohibited-if"></a>
#### prohibited_if:_anotherfield_,_value_,...
#### prohibited_if:_anotherfield_,_value_,...

The field under validation must be missing or empty if the _anotherfield_ field is equal to any _value_. A field is "empty" if it meets one of the following criteria:

검증 중인 필드가 지정된 다른 필드 _anotherfield_ 의 _value_ 와 같은 경우 검증 중인 필드는 누락되거나 비어있어야 합니다. 필드는 다음 기준 중 하나를 충족하면 "비어있는" 것으로 간주됩니다.

<div class="content-list" markdown="1">

- The value is `null`.
- The value is an empty string.
- The value is an empty array or empty `Countable` object.
- The value is an uploaded file with an empty path.

- 필드의 값이 `null`입니다.
- 필드의 값이 빈 문자열입니다.
- 필드의 값이 빈 배열이거나 빈 `Countable` 객체입니다.
- 필드의 값이 경로가 없는 업로드된 파일입니다.

</div>

If complex conditional prohibition logic is required, you may utilize the `Rule::prohibitedIf` method. This method accepts a boolean or a closure. When given a closure, the closure should return `true` or `false` to indicate if the field under validation should be prohibited:

복잡한 조건부 금지 로직이 필요한 경우 `Rule::prohibitedIf` 메서드를 활용할 수 있습니다. 이 메서드는 부울이나 조건에 따른 금지를 설정할 수 있는 클로저를 받습니다. 클로저가 주어지는 경우 클로저는 유효성 검증 중인 필드가 금지되어야 하는지 나타내기 위해 `true`나 `false`를 반환해야 합니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($request->all(), [
        'role_id' => Rule::prohibitedIf($request->user()->is_admin),
    ]);

    Validator::make($request->all(), [
        'role_id' => Rule::prohibitedIf(fn () => $request->user()->is_admin),
    ]);

<a name="rule-prohibited-unless"></a>
#### prohibited_unless:_anotherfield_,_value_,...
#### prohibited_unless:_anotherfield_,_value_,...

The field under validation must be missing or empty unless the _anotherfield_ field is equal to any _value_. A field is "empty" if it meets one of the following criteria:

검증 중인 필드가 지정된 다른 필드 _anotherfield_ 의 _value_ 와 같지 않은 경우 검증 중인 필드는 누락되거나 비어있어야 합니다. 필드는 다음 기준 중 하나를 충족하면 "비어있는" 것으로 간주됩니다.

<div class="content-list" markdown="1">

- The value is `null`.
- The value is an empty string.
- The value is an empty array or empty `Countable` object.
- The value is an uploaded file with an empty path.

- 필드의 값이 `null`입니다.
- 필드의 값이 빈 문자열입니다.
- 필드의 값이 빈 배열이거나 빈 `Countable` 객체입니다.
- 필드의 값이 경로가 없는 업로드된 파일입니다.

</div>

<a name="rule-prohibits"></a>
#### prohibits:_anotherfield_,...
#### prohibits:_anotherfield_,...

If the field under validation is not missing or empty, all fields in _anotherfield_ must be missing or empty. A field is "empty" if it meets one of the following criteria:

지정된 다른 필드 _anotherfield_ 모두가 존재하지 않거나 비어있는 경우 검증 중인 필드는 존재해야하며 비어있지 않아야 합니다. 필드는 다음 기준 중 하나를 충족하면 "비어있는" 것으로 간주됩니다.

<div class="content-list" markdown="1">

- The value is `null`.
- The value is an empty string.
- The value is an empty array or empty `Countable` object.
- The value is an uploaded file with an empty path.

- 필드의 값이 `null`입니다.
- 필드의 값이 빈 문자열입니다.
- 필드의 값이 빈 배열이거나 빈 `Countable` 객체입니다.
- 필드의 값이 경로가 없는 업로드된 파일입니다.

</div>

<a name="rule-regex"></a>
#### regex:_pattern_
#### regex:_pattern_

The field under validation must match the given regular expression.

검증 중인 필드의 값이 주어진 정규식 표현과 일치해야 합니다.

Internally, this rule uses the PHP `preg_match` function. The pattern specified should obey the same formatting required by `preg_match` and thus also include valid delimiters. For example: `'email' => 'regex:/^.+@.+$/i'`.

이 규칙은 내부적으로 PHP의 `preg_match` 함수를 사용합니다. 지정된 패턴은 `preg_match` 에서 요구하는 것과 동일한 formatting-형식을 따라야하며 유효한 구분 기호(`/`)를 포함해야합니다. 예를 들면 다음과 같습니다. `'email' => 'regex:/^.+@.+$/i'`.


> **Warning**  
> When using the `regex` / `not_regex` patterns, it may be necessary to specify rules in an array instead of using `|` delimiters, especially if the regular expression contains a `|` character.

> **Warning**
> `regex` / `not_regex` 패턴을 사용할 때, 특히 정규 표현식에 `|` 문자가 포함된 경우 `|` 구분 기호를 사용하는 대신 배열을 사용하여 유효성 검사 규칙을 지정해야 할 수도 있습니다.

<a name="rule-required"></a>
#### required
#### required

The field under validation must be present in the input data and not empty. A field is "empty" if it meets one of the following criteria:

검증 중인 필드는 input-입력 데이터에 존재해야 하며 비어 있어서는 안됩니다. 필드는 다음 기준 중 하나를 충족하면 "비어있는" 것으로 간주됩니다.

<div class="content-list" markdown="1">

- The value is `null`.
- The value is an empty string.
- The value is an empty array or empty `Countable` object.
- The value is an uploaded file with no path.

</div>

- 값이 `null`인 경우.
- 값이 비어있는 문자열인 경우.
- 값이 비어있는 배열이거나, 비어있는 `Countable` 객체인경우
- 값이 경로없이 업로드된 파일인 경우

<a name="rule-required-if"></a>
#### required_if:_anotherfield_,_value_,...
#### required_if:_anotherfield_,_value_,...

The field under validation must be present and not empty if the _anotherfield_ field is equal to any _value_.

지정한 다른 필드 _anotherfield_ 의 값이 _value_ 과 같으면, 검증 중인 필드는 존재하고 비어있지 않아야 합니다.

If you would like to construct a more complex condition for the `required_if` rule, you may use the `Rule::requiredIf` method. This method accepts a boolean or a closure. When passed a closure, the closure should return `true` or `false` to indicate if the field under validation is required:

`required_if` 규칙에 대해 더 복잡한 조건을 구성하려면 `Rule::requiredIf` 메서드를 사용할 수 있습니다. 이 메서드는 boolean 또는 클로저를 입력받습니다. 클로저가 전달되면 클로저는 `true` 또는 `false`를 반환하여 유효성 검사 중인 필드가 필요한지 여부를 나타냅니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($request->all(), [
        'role_id' => Rule::requiredIf($request->user()->is_admin),
    ]);

    Validator::make($request->all(), [
        'role_id' => Rule::requiredIf(fn () => $request->user()->is_admin),
    ]);

<a name="rule-required-unless"></a>
#### required_unless:_anotherfield_,_value_,...
#### required_unless:_anotherfield_,_value_,...

The field under validation must be present and not empty unless the _anotherfield_ field is equal to any _value_. This also means _anotherfield_ must be present in the request data unless _value_ is `null`. If _value_ is `null` (`required_unless:name,null`), the field under validation will be required unless the comparison field is `null` or the comparison field is missing from the request data.

다른 필드 _anotherfield_ 의 값이 _value_ 와 같지 않은 경우 검증 중인 필드는 존재해야 하며 비어 있지 않아야 합니다. 이는 _value_ 가 `null`이 아닌 한 지정한 필드 _anotherfield_ 가 리퀘스트 데이터에 존재해야 한다는 의미이기도 합니다. _value_ 가 `null` (`required_unless:name,null`)인 경우 검증 중인 필드의 값은 같지 않아야 하므로 `null`이 아니며, 리퀘스트 데이터의 비교 필드가 누락되지 않고 `null`이면 비교 필드의 값과 검증 중인 필드의 값이 다르므로 검증 중인 필드가 요구됩니다.

<a name="rule-required-with"></a>
#### required_with:_foo_,_bar_,...
#### required_with:_foo_,_bar_,...

The field under validation must be present and not empty _only if_ any of the other specified fields are present and not empty.

지정된 다른 필드 중 하나라도 존재하고 비어 있지 않은 경우에만 검증 중인 필드는 존재하고 비어 있지 않아야 합니다. _foo_ _bar_ 는 _only if_ 으로 표현되며 _only if_ 는 '~인 경우에만'이란 뜻을 가지고 있습니다.

<a name="rule-required-with-all"></a>
#### required_with_all:_foo_,_bar_,...
#### required_with_all:_foo_,_bar_,...

The field under validation must be present and not empty _only if_ all of the other specified fields are present and not empty.

지정된 다른 필드가 모두 존재하고 비어 있지 않은 경우에만 검증 중인 필드는 존재하고 비어있지 않아야 합니다. _foo_ _bar_ 는 _only if_ 으로 표현되며 _only if_ 는 '~인 경우에만'이란 뜻을 가지고 있습니다.

<a name="rule-required-without"></a>
#### required_without:_foo_,_bar_,...
#### required_without:_foo_,_bar_,...

The field under validation must be present and not empty _only when_ any of the other specified fields are empty or not present.

지정된 다른 필드가 비어 있거나 존재하지 않은 때에만 검증 중인 필드는  존재하고 비어 있지 않아야 합니다. _foo_ _bar_ 는 _only when_ 으로 표현되며 _only when_ 는 '~인 때에만'이란 뜻을 가지고 있습니다.

<a name="rule-required-without-all"></a>
#### required_without_all:_foo_,_bar_,...
#### required_without_all:_foo_,_bar_,...

The field under validation must be present and not empty _only when_ all of the other specified fields are empty or not present.

지정된 다른 필드가 모두 비어 있거나 존재하지 않을 때에만 검증 중인 필드는 비어 존재하고 비어 있지 않아야 합니다. _foo_ _bar_ 는 _only when_ 으로 표현되며 _only when_ 는 '~인 때에만'이란 뜻을 가지고 있습니다.

<a name="rule-required-array-keys"></a>
#### required_array_keys:_foo_,_bar_,...
#### required_array_keys:_foo_,_bar_,...

The field under validation must be an array and must contain at least the specified keys.

검증 중인 필드는 배열이어야 하며, 지정된 키를 적어도 하나이상 포함해야합니다.

<a name="rule-same"></a>
#### same:_field_
#### same:_field_

The given _field_ must match the field under validation.

검증 중인 필드의 값이 주어진 _field_ 의 값과 일치해야 합니다.

<a name="rule-size"></a>
#### size:_value_
#### size:_value_

The field under validation must have a size matching the given _value_. For string data, _value_ corresponds to the number of characters. For numeric data, _value_ corresponds to a given integer value (the attribute must also have the `numeric` or `integer` rule). For an array, _size_ corresponds to the `count` of the array. For files, _size_ corresponds to the file size in kilobytes. Let's look at some examples:

검증 중인 필드의 값이 주어진 _value_ 와 일치하는 크기를 가져야 합니다. 문자열 데이터에서는 _value_ 는 문자 수와 일치해야 합니다.  수 데이터 데이터에서는 _value_ 는 지정된 정수 값과 일치해야 합니다 (속성에는 `numeric` 또는 `integer` 규칙도 있어야 함). 배열에서는 배열의 `count`와 일치해야 합니다. 파일의 경우 _size_ 는 파일 크기(킬로바이트)와 일치해야 합니다. 몇 가지 예를 살펴 보겠습니다.

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

검증 중인 필드의 값이 반드시 문자열이어야 합니다. 필드가 `null` 인것을 허용하려면 `nullable` 규칙을 할당해야 합니다.

<a name="rule-timezone"></a>
#### timezone
#### timezone

The field under validation must be a valid timezone identifier according to the `timezone_identifiers_list` PHP function.

검증 중인 필드의 값이 `timezone_identifiers_list` PHP 함수에서 인식 가능한 유효한 timezone 식별자여야 합니다.

<a name="rule-unique"></a>
#### unique:_table_,_column_
#### unique:_table_,_column_

The field under validation must not exist within the given database table.

검증 중인 필드는 지정된 데이터베이스 테이블 내에 존재하지 않아야 합니다. 만약 `column`이 지정돼 있지 않다면 필드의 이름이 사용됩니다.

**Specifying A Custom Table / Column Name:**
**특정 컬럼명 지정하기:**

Instead of specifying the table name directly, you may specify the Eloquent model which should be used to determine the table name:

테이블 이름을 직접 지정하는 대신 테이블 이름을 결정하는 데 사용되는 Eloquent 모델을 지정할 수 있습니다.

    'email' => 'unique:App\Models\User,email_address'

The `column` option may be used to specify the field's corresponding database column. If the `column` option is not specified, the name of the field under validation will be used.

`column` 옵션은 필드의 해당 데이터베이스 열을 지정하는 데 사용할 수 있습니다. `column` 옵션이 지정되지 않은 경우 유효성 검사 중인 필드의 이름이 사용됩니다.

    'email' => 'unique:users,email_address'

**Specifying A Custom Database Connection**
**사용자 지정 데이터베이스 연결 지정**

Occasionally, you may need to set a custom connection for database queries made by the Validator. To accomplish this, you may prepend the connection name to the table name:

경우에 따라 Validator에서 만든 데이터베이스 쿼리에 대한 사용자 지정 커넥션을 설정해야 할 수도 있습니다. 이를 위해 테이블 이름 앞에 커넥션 이름을 추가할 수 있습니다.

    'email' => 'unique:connection.users,email_address'

**Forcing A Unique Rule To Ignore A Given ID:**
**주어진 ID에 대해서 유니크 규칙을 무시하도록 강제하기:**

Sometimes, you may wish to ignore a given ID during unique validation. For example, consider an "update profile" screen that includes the user's name, email address, and location. You will probably want to verify that the email address is unique. However, if the user only changes the name field and not the email field, you do not want a validation error to be thrown because the user is already the owner of the email address in question.

때때로 유니크 규칙의 유효성 검사 중에 지정된 ID를 무시하고 싶을 수 있습니다. 예를 들어 사용자 이름, 이메일 주소 및 위치가 포함된 "프로필 업데이트" 화면을 생각해보세요. 이메일 주소가 고유한지 확인하고 싶을 것입니다. 그러나 사용자가 이메일 필드가 아닌 이름 필드만 변경하는 경우 해당 사용자가 이미 해당 이메일 주소의 소유자이기 때문에 유효성 검사 에러가 발생하는 것을 원하지 않을 것입니다.

To instruct the validator to ignore the user's ID, we'll use the `Rule` class to fluently define the rule. In this example, we'll also specify the validation rules as an array instead of using the `|` character to delimit the rules:

사용자 ID를 무시하도록 지시하려면, 규칙을 유연하게 정의할 수 있는 `Rule` 클래스를 사용하면 됩니다. 다음 예제에서 규칙을 `|` 문자를 구분자로 사용하는 대신에 유효성 검사 규칙을 배열로 지정하고 있습니다.

    use Illuminate\Database\Eloquent\Builder;
    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    Validator::make($data, [
        'email' => [
            'required',
            Rule::unique('users')->ignore($user->id),
        ],
    ]);

> **Warning**  
> You should never pass any user controlled request input into the `ignore` method. Instead, you should only pass a system generated unique ID such as an auto-incrementing ID or UUID from an Eloquent model instance. Otherwise, your application will be vulnerable to an SQL injection attack.

> **Warning**
> `ignore` 메소드에는 유저가 조작한 리퀘스트 input-입력값을 절대 전달해서는 안 됩니다. 대신, auto-incrementing ID나 엘로퀀트 모델 인스턴스의 UUID와 같이 시스템에서 생성된 유니크 ID만 전달하도록 합니다. 그렇지 않으면 어플리케이션이 SQL 인젝션 공격에 취약하게 됩니다.

Instead of passing the model key's value to the `ignore` method, you may also pass the entire model instance. Laravel will automatically extract the key from the model:

`ignore` 메소드에 모델 키 값을 전달하는 대신 전체 모델 인스턴스를 전달할 수도 있습니다. 라라벨은 자동으로 모델에서 키를 추출합니다.

    Rule::unique('users')->ignore($user)

If your table uses a primary key column name other than `id`, you may specify the name of the column when calling the `ignore` method:

테이블이 `id`가 아닌 primary 키 컬럼 이름을 사용한다면, `ignore` 메소드를 호출할 때 컬럼의 이름을 지정하면 됩니다.

    Rule::unique('users')->ignore($user->id, 'user_id')

By default, the `unique` rule will check the uniqueness of the column matching the name of the attribute being validated. However, you may pass a different column name as the second argument to the `unique` method:

기본적으로 `unique`규칙은 유효성 검사중인 속성의 이름과 일치하는 컬럼의 데이터가 유니크인지 검사합니다. 그러나 `unique` 메소드의 두 번째 인자에 다른 칼럼의 이름을 전달할 수 있습니다.

    Rule::unique('users', 'email_address')->ignore($user->id)

**Adding Additional Where Clauses:**
**추가적인 Where 구문 추가하기:**

You may specify additional query conditions by customizing the query using the `where` method. For example, let's add a query condition that scopes the query to only search records that have an `account_id` column value of `1`:

`where` 메서드를 사용하여 쿼리를 사용자 지정하여 추가 쿼리 조건을 지정할 수 있습니다. 예를 들어 `account_id` 컬럼 값이 `1`인 레코드만 검색하도록 쿼리 범위를 지정하는 쿼리 조건을 추가해 보겠습니다.

    'email' => Rule::unique('users')->where(fn (Builder $query) => $query->where('account_id', 1))

<a name="rule-uppercase"></a>
#### uppercase
#### uppercase

The field under validation must be uppercase.

검증 중인 필드는 반드시 대문자여야 합니다.

<a name="rule-url"></a>
#### url
#### url

The field under validation must be a valid URL.

필드는 반드시 유효한 URL이어야 합니다.

<a name="rule-ulid"></a>
#### ulid
#### ulid

The field under validation must be a valid [Universally Unique Lexicographically Sortable Identifier](https://github.com/ulid/spec) (ULID).

검증 중인 필드는 반드시 유효한 [Universally Unique Lexicographically Sortable Identifier](https://github.com/ulid/spec) (ULID) 여야 합니다.

<a name="rule-uuid"></a>
#### uuid
#### uuid

The field under validation must be a valid RFC 4122 (version 1, 3, 4, or 5) universally unique identifier (UUID).

유효성 검사중인 필드는 유효한 RFC 4122 (버전 1, 3, 4 또는 5) 범용 고유 식별자 (universally unique identifier, UUID) 여야합니다.

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

지정한 다른 필드에 있는 값이 주어진 값이 아닌 경우에 검증 중인 필드의 유효성 검사를 수행하지 않도록 하려면 `exclude_unless` 규칙을 사용하면 됩니다. 아래 예제에서 'appointment_date' 필드는 has_appointment 필드의 값이 true 값이 아닌 경우 유효성 검사를 수행하지 않습니다.

    $validator = Validator::make($data, [
        'has_appointment' => 'required|boolean',
        'appointment_date' => 'exclude_unless:has_appointment,true|required|date',
        'doctor_name' => 'exclude_unless:has_appointment,true|required|string',
    ]);

<a name="validating-when-present"></a>
#### Validating When Present
#### Validating When Present

In some situations, you may wish to run validation checks against a field **only** if that field is present in the data being validated. To quickly accomplish this, add the `sometimes` rule to your rule list:

어떤 경우에는 검증되고 있는 데이터에 **오직** 검증 중인 필드가 존재하는 경우에**만** 유효성 검사를 하길 원할 수 있습니다. 이를 빠르게 수행하려면 규칙 목록에 `sometimes` 규칙을 추가하세요.

    $v = Validator::make($data, [
        'email' => 'sometimes|required|email',
    ]);

In the example above, the `email` field will only be validated if it is present in the `$data` array.

이 예제에서는 유효성 검사 중인 데이터인 `$data` 배열에 `email` 필드가 존재할 경우에만 그 필드의 유효성 검사가 되도록 `sometimes` 규칙이 사용되었습니다.

> **Note**  
> If you are attempting to validate a field that should always be present but may be empty, check out [this note on optional fields](#a-note-on-optional-fields).

> **Note**
> 항상 존재해야하며 비어있지 않은 필드인지를 확인하는 유효성 검사를 하려는 경우 [선택 필드에 대한 참고 사항](#a-note-on-optional-fields)을 확인하세요.

<a name="complex-conditional-validation"></a>
#### Complex Conditional Validation
#### 복잡한 조건부 유효성 검사

Sometimes you may wish to add validation rules based on more complex conditional logic. For example, you may wish to require a given field only if another field has a greater value than 100. Or, you may need two fields to have a given value only when another field is present. Adding these validation rules doesn't have to be a pain. First, create a `Validator` instance with your _static rules_ that never change:

때로는 더 복잡한 조건부 논리를 기반으로 유효성 검사 규칙을 추가하고 싶을 수도 있습니다. 예를 들어, 다른 필드의 값이 100보다 큰 경우에만 지정한 필드를 요구할 수 있습니다. 또는 다른 필드가 존재할 때만 두 필드가 지정한 값을 갖도록 요구할 수도 있습니다. 이러한 유효성 검사 규칙을 추가하는 것은 어렵지 않습니다. 먼저, 절대 변경되지 않는 정적 규칙 _static rules_ 를 정의한 `Validator` 인스턴스를 생성합니다.

    use Illuminate\Support\Facades\Validator;

    $validator = Validator::make($request->all(), [
        'email' => 'required|email',
        'games' => 'required|numeric',
    ]);

Let's assume our web application is for game collectors. If a game collector registers with our application and they own more than 100 games, we want them to explain why they own so many games. For example, perhaps they run a game resale shop, or maybe they just enjoy collecting games. To conditionally add this requirement, we can use the `sometimes` method on the `Validator` instance.

게임 수집가를 위한 웹 애플리케이션이라고 가정해 봅시다. 게임 수집가가 애플리케이션에 등록하고 100개 이상의 게임을 소유한 경우, 그들이 왜 그렇게 많은 게임을 소유하고 있는지 설명을 추가로 받는 로직을 만든다고 생각해 봅시다. 예를 들어, 많은 게임을 소유한 이유로는 게임 재판매 상점을 운영하거나 단순히 게임 수집을 즐기는 것 등의 이유가 있을 수 있습니다. 이 요구 사항을 조건부로 추가하려면 `Validator` 인스턴스에서 `sometimes` 메서드를 사용하면 됩니다. 아래 예제는 게임이 100개 이상인 경우, 왜 게임을 100개 이상 가지고 있는지 500자 이하의 설명을 필수로 요구하는 'reason' 필드를 받도록 유효성 검사를 수행한다는 의미입니다.

    use Illuminate\Support\Fluent;

    $validator->sometimes('reason', 'required|max:500', function (Fluent $input) {
        return $input->games >= 100;
    });

The first argument passed to the `sometimes` method is the name of the field we are conditionally validating. The second argument is a list of the rules we want to add. If the closure passed as the third argument returns `true`, the rules will be added. This method makes it a breeze to build complex conditional validations. You may even add conditional validations for several fields at once:

`sometimes` 메소드에 전달된 첫 번째 인자는 조건부로 유효성을 검사하는 필드의 이름입니다. 두 번째 인자는 추가하려는 규칙 목록입니다. 세 번째 인자는 조건부 규칙을 정하는 클로저를 받으며 전달된 클로저가 `true`를 반환하면 첫 번째 인자에 주어진 필드는 두  번째 인자로 지정한 유효성 검사 규칙을 적용하여 유효성 검사를 합니다. 이 방법을 사용하면 복잡한 조건부 유효성 검사를 쉽게 구축할 수 있습니다. 한 번에 여러 필드에 대한 조건부 유효성 검사를 추가할 수도 있습니다.

    $validator->sometimes(['reason', 'cost'], 'required', function (Fluent $input) {
        return $input->games >= 100;
    });

> **Note**  
> The `$input` parameter passed to your closure will be an instance of `Illuminate\Support\Fluent` and may be used to access your input and files under validation.

> **Note**
> 클로저에 전달된 `$input` 매개변수는 `Illuminate\Support\Fluent`의 인스턴스가 되며 유효성 검사 중인 입력 및 파일에 액세스하는 데 사용할 수 있습니다.

<a name="complex-conditional-array-validation"></a>
#### Complex Conditional Array Validation
#### 복잡한 조건부 배열 검증

Sometimes you may want to validate a field based on another field in the same nested array whose index you do not know. In these situations, you may allow your closure to receive a second argument which will be the current individual item in the array being validated:

검증 데이터의 중첩된 배열 구조에서 어떤 배열은 키가 아닌 인덱스로 이뤄진 배열일 수 있습니다. 유효성 검사는 배열의 키에 해당하는 필드를 지정해야 하는데 배열에 키가 없고 인덱스로 된 배열인 경우 입력 데이터에 따라 `type`이 `'email'`인 배열이 먼저인지 `type`이 `'url'`인 배열이 먼저인지 인덱스를 모르는 동일한 위치의 배열의 유효성 검사를 해야 할 때가 있습니다. 아래의 예시에서 `channels` 필드는 키가 없는 두 배열을 포함하고 있습니다. 두 배열의 `address` 필드는 `type` 필드의 값을 기반으로 유효성 검사를 하려고 합니다. 이러한 상황에서 클로저의 두 번째 인자로 파라메터를 지정하여 현재 검증 중인 배열의 개별 항목을 받도록 허용할 수 있습니다.

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

    $validator->sometimes('channels.*.address', 'email', function (Fluent $input, Fluent $item) {
        return $item->type === 'email';
    });

    $validator->sometimes('channels.*.address', 'url', function (Fluent $input, Fluent $item) {
        return $item->type !== 'email';
    });

Like the `$input` parameter passed to the closure, the `$item` parameter is an instance of `Illuminate\Support\Fluent` when the attribute data is an array; otherwise, it is a string.

클로저에 전달된 `$input` 매개변수와 마찬가지로 `$item` 매개변수는 속성 데이터가 배열일 때 `Illuminate\Support\Fluent`의 인스턴스이며 배열이 아닌 경우에는 문자열입니다.

<a name="validating-arrays"></a>
## Validating Arrays
## 배열값 유효성 검사

As discussed in the [`array` validation rule documentation](#rule-array), the `array` rule accepts a list of allowed array keys. If any additional keys are present within the array, validation will fail:

[`array` 유효성 검사 규칙 문서](#rule-array)에 설명된 대로 `array` 규칙은 검증 중인 필드가 지정한 키 목록을 가진 배열만을 허용합니다. 배열 내에 추가 키가 있는 경우 유효성 검사가 실패합니다. 아래의 예시에서 `array:username,locale` 규칙에 의해 검증 대상 배열에는 `username`과 `locale` 필드만 허가 대상이지만 지정하지 않은 `name`, `admin` 필드가 추가되어 있기 때문에 검증이 실패합니다. `locale` 필드는 없지만 허가 되어 있으므로 검증 규칙에 따라 검증이 될 수도 있습니다.

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

일반적으로 배열의 유효성을 검사할 때는 항상 허용된 키 목록을 지정하여 유효성 검사를 수행하도록 합니다. 허용할 키의 목록을 지정하는식으로 사용하지 않으면 validator의 `validate` 및 `validated` 메서드는 배열에서 허용되지 않은 키를 포함한 모든 키를 가진 데이터를 검증된 데이터로 반환하게 될 수 있습니다. 중첩된 배열의 유효성을 검사할 때 검증되지 않은 키가 있음에도 불구하고 검증된 데이터로 반환되므로 보안상의 문제가 발생할 수 있습니다.

<a name="validating-nested-array-input"></a>
### Validating Nested Array Input
### 중첩 배열 입력 검증

Validating nested array based form input fields doesn't have to be a pain. You may use "dot notation" to validate attributes within an array. For example, if the incoming HTTP request contains a `photos[profile]` field, you may validate it like so:

중첩 배열 기반 form input-입력 필드의 유효성을 검사하는 것에 대해 괴로워 할 필요가 없습니다. "점 표기법"을 사용하여 배열 내의 속성을 확인할 수 있습니다. 예를 들어 들어오는 HTTP 리퀘스트에 `photos[profile]` 필드가 포함되어 있으면 다음과 같이 유효성을 검사할 수 있습니다.

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

<a name="accessing-nested-array-data"></a>
#### Accessing Nested Array Data
#### 중첩된 배열 데이터에 엑세스하기

Sometimes you may need to access the value for a given nested array element when assigning validation rules to the attribute. You may accomplish this using the `Rule::forEach` method. The `forEach` method accepts a closure that will be invoked for each iteration of the array attribute under validation and will receive the attribute's value and explicit, fully-expanded attribute name. The closure should return an array of rules to assign to the array element:

속성에 유효성 검사 규칙을 할당할 때 주어진 중첩 배열 element-요소의 값에 액세스해야 하는 경우가 있습니다. 이런 경우에는 `Rule::forEach` 메소드를 사용하면 됩니다. `forEach` 메소드는 클로저를 인자로 전달 받습니다. 이 클로저는 유효성 검사 과정에서 배열의 요소에 접근하는 반복문 안에서 호출됩니다. 클로저에는 배열의 
속성 값과 명시적이고 완전히 확장된 속성 이름(fully-expanded attribute name)을 받습니다. 완전히 확장된 속성 이름은 `companies.*.id`에서 점 기호를 통해 확장되어 마지막으로 확장된 이름인 `id` 속성 이름을 의미합니다. 클로저는 각각의 요소에 해당하는 유효성 검사 규칙으로 이루어진 배열을 반환해야합니다.

    use App\Rules\HasPermission;
    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rule;

    $validator = Validator::make($request->all(), [
        'companies.*.id' => Rule::forEach(function (string|null $value, string $attribute) {
            return [
                Rule::exists(Company::class, 'id'),
                new HasPermission('manage-company', $value),
            ];
        }),
    ]);

<a name="error-message-indexes-and-positions"></a>
### Error Message Indexes & Positions
### 에러 메세지에서 순서와 위치 참조하기

When validating arrays, you may want to reference the index or position of a particular item that failed validation within the error message displayed by your application. To accomplish this, you may include the `:index` (starts from `0`) and `:position` (starts from `1`) placeholders within your [custom validation message](#manual-customizing-the-error-messages):

배열의 유효성 검사를 진행할 때, 애플리케이션에서 표시하는 에러 메세지에서 실패한 아이템의 인덱스 번호 또는 몇 번째 위치인지를 참조하고 싶을 수 있습니다. 이렇게 하기 위해서는 [에러 메시지 사용자 정의](#manual-customizing-the-error-messages) 안에서 `:index` (0 부터 시작)와 `:position` (1 부터 시작) 플레이스홀더를 사용하면 됩니다. 

    use Illuminate\Support\Facades\Validator;

    $input = [
        'photos' => [
            [
                'name' => 'BeachVacation.jpg',
                'description' => 'A photo of my beach vacation!',
            ],
            [
                'name' => 'GrandCanyon.jpg',
                'description' => '',
            ],
        ],
    ];

    Validator::validate($input, [
        'photos.*.description' => 'required',
    ], [
        'photos.*.description.required' => 'Please describe photo #:position.',
    ]);

Given the example above, validation will fail and the user will be presented with the following error of _"Please describe photo #2."_

위와 같은 예제의 경우에는 유효성 검사는 실패하고 사용자에게 표시되는 에러 메세지는 _"Please describe photo #2."_ 와 같이 나타납니다. ("두 번째 사진에 설명을 입력해주세요")

<a name="validating-files"></a>
## Validating Files
## 파일 검증

Laravel provides a variety of validation rules that may be used to validate uploaded files, such as `mimes`, `image`, `min`, and `max`. While you are free to specify these rules individually when validating files, Laravel also offers a fluent file validation rule builder that you may find convenient:

라라벨은 `mimes`, `image`, `min`, `max` 같이 업로드된 파일의 유효성을 검사하는 데 사용할 수 있는 다양한 유효성 검사 규칙을 제공합니다. 파일의 유효성을 검사할 때 이러한 규칙을 개별적으로 자유롭게 지정할 수 있지만 라라벨은 우아하게 쓸 수 있는 검사 규칙 빌더를 제공하므로 편리하게 사용할 수 있습니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rules\File;

    Validator::validate($input, [
        'attachment' => [
            'required',
            File::types(['mp3', 'wav'])
                ->min(1024)
                ->max(12 * 1024),
        ],
    ]);

If your application accepts images uploaded by your users, you may use the `File` rule's `image` constructor method to indicate that the uploaded file should be an image. In addition, the `dimensions` rule may be used to limit the dimensions of the image:

애플리케이션에서 사용자가 업로드한 이미지를 허용하는 경우 `File` 규칙의 `image` 생성자 메서드를 사용하여 업로드된 파일이 이미지여야 함을 나타낼 수 있습니다. 추가적으로 이미지의 치수를 제한하기 위해 `dimensions` 규칙을 사용할 수 있습니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rules\File;

    Validator::validate($input, [
        'photo' => [
            'required',
            File::image()
                ->min(1024)
                ->max(12 * 1024)
                ->dimensions(Rule::dimensions()->maxWidth(1000)->maxHeight(500)),
        ],
    ]);

> **Note**  
> More information regarding validating image dimensions may be found in the [dimension rule documentation](#rule-dimensions).

> **Note**  
> 이미지 치수의 유효성 검사에 대한 자세한 내용은 [이미지 치수 문서](#rule-dimensions)에서 찾아볼 수 있습니다.

<a name="validating-files-file-types"></a>
#### File Types
#### 파일 타입

Even though you only need to specify the extensions when invoking the `types` method, this method actually validates the MIME type of the file by reading the file's contents and guessing its MIME type. A full listing of MIME types and their corresponding extensions may be found at the following location:

`types` 메서드를 호출할 때 확장자만 지정하면 되지만, 이 메서드는 실제로 파일의 내용을 읽고 MIME 타입을 추측하여 파일의 MIME 타입을 검증합니다. MIME 유형과 해당 확장자의 전체 목록은 다음 위치에서 확인할 수 있습니다.

[https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

<a name="validating-passwords"></a>
## Validating Passwords
## 비밀번호 검증

To ensure that passwords have an adequate level of complexity, you may use Laravel's `Password` rule object:

비밀번호가 적절한 수준의 복잡성을 갖도록 하려면 라라벨의 `Password` rule 객체를 사용할 수 있습니다.

    use Illuminate\Support\Facades\Validator;
    use Illuminate\Validation\Rules\Password;

    $validator = Validator::make($request->all(), [
        'password' => ['required', 'confirmed', Password::min(8)],
    ]);

The `Password` rule object allows you to easily customize the password complexity requirements for your application, such as specifying that passwords require at least one letter, number, symbol, or characters with mixed casing:

`Password` rule 객체를 사용하면 비밀번호에 문자, 숫자, 기호 또는 대소문자가 혼합된 문자가 하나 이상 필요하도록 지정하는 것과 같이 애플리케이션에 대한 비밀번호 복잡성 요구 사항을 쉽게 사용자 지정할 수 있습니다.

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

내부적으로 `Password` rule 객체는 [k-Anonymity](https://en.wikipedia.org/wiki/K-anonymity) 모델을 사용하여 [haveibeenpwned.com](https://haveibeenpwned.com) 을 통해 비밀번호가 유출되었는지 확인합니다. 사용자의 개인 정보 또는 보안을 희생하지 않고 서비스를 제공합니다.

By default, if a password appears at least once in a data leak, it will be considered compromised. You can customize this threshold using the first argument of the `uncompromised` method:

기본적으로 암호가 데이터 유출에서 한 번 이상 나타나면 유출된 것으로 간주됩니다. `uncompromised` 메소드의 첫 번째 인자를 사용하여 이 임계값을 사용자 정의할 수 있습니다.

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

애플리케이션의 단일 위치에서 암호에 대한 기본 유효성 검사 규칙을 지정하는 것이 편리할 수 있습니다. 클로저를 입력받는 `Password::defaults` 메소드를 사용하여 이를 쉽게 달성할 수 있습니다. `defaults` 메소드에 주어진 클로저는 Password 규칙의 기본 구성을 반환해야 합니다. 일반적으로 애플리케이션의 서비스 프로바이더 중 하나의 `boot` 메서드 내에서 `defaults` 규칙을 호출해야 합니다.

```php
use Illuminate\Validation\Rules\Password;

/**
 * Bootstrap any application services.
 */
public function boot(): void
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

그런 다음 유효성 검사를 받는 특정 비밀번호에 기본 규칙을 적용하려면 인자 없이 `defaults` 메소드를 호출하면 됩니다.

    'password' => ['required', Password::defaults()],

Occasionally, you may want to attach additional validation rules to your default password validation rules. You may use the `rules` method to accomplish this:

경우에 따라 기본 비밀번호 유효성 검사 규칙에 유효성 검사 규칙을 추가하고 싶을 때가 있습니다. 이를 위해 `rules` 메서드를 사용할 수 있습니다.

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

라라벨은 다양하고 유용한 유효성 검사 규칙을 제공합니다. 사용자가 직접 몇 가지 규칙을 지정할 수도 있습니다. 사용자 정의 유효성 검사 규칙을 등록하는 한 가지 방법은 rule 객체를 사용하는 것입니다. 새 rule 객체를 생성하려면 `make:rule` 아티즌 명령를 사용할 수 있습니다. 이 명령을 사용하여 문자열이 대문자인지 확인하는 규칙을 생성해 보겠습니다. 라라벨은 `app/Rules` 디렉토리에 새 rule 클래스를 배치합니다. 이 디렉토리가 존재하지 않는 경우 라라벨은 아티즌 명령를 실행하여 rule 클래스를 생성할 때 폴더도 함께 생성합니다.

```shell
php artisan make:rule Uppercase
```

Once the rule has been created, we are ready to define its behavior. A rule object contains a single method: `validate`. This method receives the attribute name, its value, and a callback that should be invoked on failure with the validation error message:

rule 객체가 생성되고나면, 유효성 검사가 동작하는 방식을 정해야 합니다. rule 객체는 `validate` 메서드 하나를 가지고 있습니다. 이 메서드는 속성 이름, 저징된 이름의 속성이 갖는 값, 유효성 검사 오류 메시지와 함께 실패 시 호출해야 하는 콜백을 받습니다.

    <?php

    namespace App\Rules;

    use Closure;
    use Illuminate\Contracts\Validation\ValidationRule;

    class Uppercase implements ValidationRule
    {
        /**
         * Run the validation rule.
         */
        public function validate(string $attribute, mixed $value, Closure $fail): void
        {
            if (strtoupper($value) !== $value) {
                $fail('The :attribute must be uppercase.');
            }
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

rule 을 정의하고 나면, 다른 유효성 검사 rule 객체들과 함께, rule 객체의 인스턴스를 전달하여, validator 에 유효성 검사 규칙을 추가할 수 있습니다.

    use App\Rules\Uppercase;

    $request->validate([
        'name' => ['required', 'string', new Uppercase],
    ]);

#### Translating Validation Messages

Instead of providing a literal error message to the `$fail` closure, you may also provide a [translation string key](/docs/{{version}}/localization) and instruct Laravel to translate the error message:

`$fail` 클로저 에 문자 그대로의 오류 메시지를 제공하는 대신 [번역 문자열 키](/docs/{{version}}/localization)를 제공하고 라라벨에게 오류 메시지를 번역하도록 지시할 수도 있습니다.

    if (strtoupper($value) !== $value) {
        $fail('validation.uppercase')->translate();
    }

If necessary, you may provide placeholder replacements and the preferred language as the first and second arguments to the `translate` method:

필요한 경우 `translate` 메서드에 대한 첫 번째 및 두 번째 인자로 플레이스홀더 대체자와 번역해서 표기할 기본 언어를 지정하여 번역 파일의 매칭되는 번역 표기를 표시하도록 할 수 있습니다.

    $fail('validation.location')->translate([
        'value' => $this->value,
    ], 'fr')

#### Accessing Additional Data
#### 추가 데이터 액세스

If your custom validation rule class needs to access all of the other data undergoing validation, your rule class may implement the `Illuminate\Contracts\Validation\DataAwareRule` interface. This interface requires your class to define a `setData` method. This method will automatically be invoked by Laravel (before validation proceeds) with all of the data under validation:

사용자 정의 유효성 검사 규칙 클래스가 유효성 검사를 받는 다른 모든 데이터에 액세스해야 하는 경우 rule 클래스는 `Illuminate\Contracts\Validation\DataAwareRule` 인터페이스를 구현할 수 있습니다. 이 인터페이스를 사용하려면 클래스에서 `setData` 메서드를 정의해야 합니다. 이 메소드는 검증 중인 모든 데이터와 함께 라라벨에 의해 (검증이 진행되기 전) 자동으로 호출됩니다.

    <?php

    namespace App\Rules;

    use Illuminate\Contracts\Validation\DataAwareRule;
    use Illuminate\Contracts\Validation\ValidationRule;

    class Uppercase implements DataAwareRule, ValidationRule
    {
        /**
         * All of the data under validation.
         *
         * @var array<string, mixed>
         */
        protected $data = [];

        // ...

        /**
         * Set the data under validation.
         *
         * @param  array<string, mixed>  $data
         */
        public function setData(array $data): static
        {
            $this->data = $data;

            return $this;
        }
    }

Or, if your validation rule requires access to the validator instance performing the validation, you may implement the `ValidatorAwareRule` interface:

또는 유효성 검사 규칙에 유효성 검사를 수행하는 validator 인스턴스에 대한 액세스가 필요한 경우 `ValidatorAwareRule` 인터페이스를 구현할 수 있습니다.

    <?php

    namespace App\Rules;

    use Illuminate\Contracts\Validation\ValidationRule;
    use Illuminate\Contracts\Validation\ValidatorAwareRule;
    use Illuminate\Validation\Validator;

    class Uppercase implements ValidationRule, ValidatorAwareRule
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
         */
        public function setValidator(Validator $validator): static
        {
            $this->validator = $validator;

            return $this;
        }
    }

<a name="using-closures"></a>
### Using Closures
### 클로저 사용하기

If you only need the functionality of a custom rule once throughout your application, you may use a closure instead of a rule object. The closure receives the attribute's name, the attribute's value, and a `$fail` callback that should be called if validation fails:

애플리케이션 전체에서 사용자 지정 규칙의 기능이 한 번만 필요한 경우 rule 객체 대신 클로저를 사용할 수 있습니다. 클로저는 속성 이름, 지정된 속성이 갖는 값, 유효성 검사가 실패하면 호출되어야 하는 `$fail` 콜백을 입력받습니다.

    use Illuminate\Support\Facades\Validator;

    $validator = Validator::make($request->all(), [
        'title' => [
            'required',
            'max:255',
            function (string $attribute, mixed $value, Closure $fail) {
                if ($value === 'foo') {
                    $fail("The {$attribute} is invalid.");
                }
            },
        ],
    ]);

<a name="implicit-rules"></a>
### Implicit Rules
### 암묵적 규칙

By default, when an attribute being validated is not present or contains an empty string, normal validation rules, including custom rules, are not run. For example, the [`unique`](#rule-unique) rule will not be run against an empty string:

기본적으로 유효성 검사 중인 속성이 없거나 빈 문자열이 포함된 경우, 사용자 지정 규칙을 포함한 일반 유효성 검사 규칙이 실행되지 않습니다. 예를 들어, [`unique`](#rule-unique) 규칙은 빈 문자열에 대해 실행되지 않습니다.

    use Illuminate\Support\Facades\Validator;

    $rules = ['name' => 'unique:users,name'];

    $input = ['name' => ''];

    Validator::make($input, $rules)->passes(); // true

For a custom rule to run even when an attribute is empty, the rule must imply that the attribute is required. To quickly generate a new implicit rule object, you may use the `make:rule` Artisan command with the `--implicit` option:

속성이 비어 있는(속성이 없거나 빈 문자열인) 경우에는 유효성 검사 규칙이 실행되지 않지만, 사용자 지정 규칙이 이런 제약없이 속성이 없거나 빈 문자열인 경우에도 유효성 검사 규칙을 실행하게 하려면 유효성 검사 규칙이 해당 속성이 필수로 여기고 비어 있는 속성이더라도 유효성 검사 규칙을 적용하라는 암묵적인 규칙으로 만들어야 합니다. 이러한 암묵적 규칙을 갖는 새로운 rule 객체를 빠르게 생성하려면 `--implicit` 옵션과 함께 `make:rule` 아티즌 명령를 사용하면 됩니다.

```shell
php artisan make:rule Uppercase --implicit
```

> **Warning**  
> An "implicit" rule only _implies_ that the attribute is required. Whether it actually invalidates a missing or empty attribute is up to you.

> **Warning**
> "암묵적" 규칙은 속성이 필요하다는 것을 암묵적으로(_implies_) 포함하고 있습니다. 비어 있을 경우 전달된 속성을 유효성 검사 규칙에 적용하지 않는 일반적인 규칙이 아니라 암묵적 규칙은 누락되거나 비어있더라도 유효성 검사 규칙의 적용을 받게 하므로 검사 규칙을 어떻게 정의하느냐에 따라 실제로 누락되거나 비어 있는 속성을 무효화하는지 여부를 달리할 수 있습니다.
