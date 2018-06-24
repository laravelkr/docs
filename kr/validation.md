# Validation

- [Basic Usage](#basic-usage)
- [기본적인 사용법](#basic-usage)
- [Controller Validation](#controller-validation)
- [컨트롤러 단위 유효성 검사 ](#controller-validation)
- [Form Request Validation](#form-request-validation) 
- [폼 요청 유효성 검사](#form-request-validation) 
- [Working With Error Messages](#working-with-error-messages)
- [오류 메시지 사용하기](#working-with-error-messages) 
- [Error Messages & Views](#error-messages-and-views)
- [오류 메시지 & 뷰](#error-messages-and-views)
- [Available Validation Rules](#available-validation-rules)
- [유효성 검사 룰](#available-validation-rules)
- [Conditionally Adding Rules](#conditionally-adding-rules)
- [조건부 룰 추가하기](#conditionally-adding-rules)
- [Custom Error Messages](#custom-error-messages)
- [사용자 정의 오류 메시지](#custom-error-messages)
- [Custom Validation Rules](#custom-validation-rules)
- [커스텀 유효성 검사 룰](#custom-validation-rules)

<a name="basic-usage"></a>
## Basic Usage 
## 기본적인 사용법

Laravel ships with a simple, convenient facility for validating data and retrieving validation error messages via the `Validator` class. 

라라벨은 `Validator` 클래스를 통해 데이터의 유효성을 검사하고, 유효성 검사 실패시 출력될 오류 메시지를 구성할 수 있는 간단하고, 편리한 기능을 제공합니다.

#### Basic Validation Example
#### 기본적인 유효성 검사의 예

	$validator = Validator::make(
		['name' => 'Dayle'],
		['name' => 'required|min:5']
	);

The first argument passed to the `make` method is the data under validation. The second argument is the validation rules that should be applied to the data. 

`make` 메소드는 첫번째 인자로 유효성 검사를 할 데이터를 받습니다. 두번째 인자로는 그 데이터에 적용되어야 하는 유효성 검사 룰을 전달 받습니다.

#### Using Arrays To Specify Rules
#### 배열을 사용하여 룰 지정하기

Multiple rules may be delimited using either a "pipe" character, or as separate elements of an array. 

두개 이상의 룰은 "pipe" 문자나, 배열의 각 엘레먼트로 구분할 수 있습니다.

	$validator = Validator::make(
		['name' => 'Dayle'],
		['name' => ['required', 'min:5']]
	);

#### Validating Multiple Fields 
#### 여러 필드의 유효성 검사

	$validator = Validator::make(
		[
			'name' => 'Dayle',
			'password' => 'lamepassword',
			'email' => 'email@example.com'
		],
		[
			'name' => 'required',
			'password' => 'required|min:8',
			'email' => 'required|email|unique:users'
		]
	);

Once a `Validator` instance has been created, the `fails` (or `passes`) method may be used to perform the validation. 

`Validator` 인스턴스를 생성한 후, `fails`(또는 `passes`) 메소드를 사용하여 유효성 검사를 실행할 수 있습니다.

	if ($validator->fails())
	{
		// The given data did not pass validation
	}

If validation has failed, you may retrieve the error messages from the validator. 

만약 유효성 검사가 실패하면, validator 인스턴스로부터 오류 메시지를 얻을 수 있습니다.

	$messages = $validator->messages();

You may also access an array of the failed validation rules, without messages. To do so, use the `failed` method:  

또한 오류 메시지가 제외된, 실패한 유효성 검사 룰을 배열 형태로 얻을 수도 있습니다:

	$failed = $validator->failed();

#### Validating Files 
#### 파일 유효성 검사

The `Validator` class provides several rules for validating files, such as `size`, `mimes`, and others. When validating files, you may simply pass them into the validator with your other data. 

`Validation` 클래스는 파일 유효성 검사를 위한 `size`, `mimes` 등의 몇가지 룰을 제공합니다. 파일의 유효성을 검사할 때, 간단히 그 파일들을 다른 데이터들과 함께 validator에 전달하십시오.

### After Validation Hook 
### 유효성검사 후킹하기

The validator also allows you to attach callbacks to be run after validation is completed. This allows you to easily perform further validation, and even add more error messages to the message collection. To get started, use the `after` method on a validator instance: 

유효성 검사가 완료된 후에 실행하고자 하는 콜백함수를 validator에 추가할 수 있습니다. 이를 통하면 손쉽게 더 많은 유효성 검사를 실행할 수 있도록 하고, 오류 메시지 모음에 오류 메시지를 더 추가할 수 도 있습니다. 다음처럼 validator 인스턴스의 `after` 메소드를 사용하면 됩니다:

	$validator = Validator::make(...);

	$validator->after(function($validator)
	{
		if ($this->somethingElseIsInvalid())
		{
			$validator->errors()->add('field', 'Something is wrong with this field!');
		}
	});

	if ($validator->fails())
	{
		//
	}

You may add as many `after` callbacks to a validator as needed. 

필요한 경우 `after` 콜백에 validator에 추가할 수도 있습니다. 

<a name="controller-validation"></a>
## Controller Validation 컨트롤러 단위 유효성 검사 

Of course, manually creating and checking a `Validator` instance each time you do validation is a headache. Don't worry, you have other options! The base `App\Http\Controllers\Controller` class included with Laravel uses a `ValidatesRequests` trait. This trait provides a single, convenient method for validating incoming HTTP requests. Here's what it looks like: 

유효성 검사를 실행할 때마다 수동으로 `Validator` 인스턴스를 생성하고 체크한다면 정말 번거로울 것입니다. 하지만 걱정하지 마십시오. 방법이 있습니다. 라라벨에 포함된 `App\Http\Controllers\Controller` 기본 클래스는 `ValidatesRequests` trait을 사용합니다. 이 trait은 들어오는 HTTP 요청의 유효성 검사를 위하여 편리한 하나의 메소드를 제공합니다. 그 메소드는 아래와 같습니다:

	/**
	 * Store the incoming blog post.
	 *
	 * @param  Request  $request
	 * @return Response
	 */
	public function store(Request $request)
	{
		$this->validate($request, [
			'title' => 'required|unique|max:255',
			'body' => 'required',
		]);

		//
	}

If validation passes, your code will keep executing normally. However, if validation fails, an `Illuminate\Contracts\Validation\ValidationException` will be thrown. This exception is automatically caught and a redirect is generated to the user's previous location. The validation errors are even automatically flashed to the session! 

만약 유효성검사가 성공하면, 코드는 정상적으로 실행될 것입니다. 하지만 유효성 검사가 실패하면, `Illuminate\Contracts\Validation\ValidationException`이 발생할 것입니다. 이 예외는 자동으로 catch되고 사용자 페이지는 이전 페이지로 리다이렉트됩니다. 유효성 검사 오류들 역시 자동으로 세션에 기록됩니다!

If the incoming request was an AJAX request, no redirect will be generated. Instead, an HTTP response with a 422 status code will be returned to the browser containing a JSON representation of the validation errors. 

만약 들어온 요청이 AJAX 요청이라면, 리다이렉트는 실행되지 않습니다. 대신, 422 상태코드의 HTTP 응답이 JSON 형식으로 유효성 검사 오류를 포함한 채 브라우저로 반환될 것입니다.

For example, here is the equivalent code written manually: 

이와 동일한 작동을 하는 코드를 수동으로 작성한 예입니다:

	/**
	 * Store the incoming blog post.
	 *
	 * @param  Request  $request
	 * @return Response
	 */
	public function store(Request $request)
	{
		$v = Validator::make($request->all(), [
			'title' => 'required|unique|max:255',
			'body' => 'required',
		]);

		if ($v->fails())
		{
			return redirect()->back()->withErrors($v->errors());
		}

		//
	}

### Customizing The Flashed Error Format 
### 오류 형식 커스터마이징 하기

If you wish to customize the format of the validation errors that are flashed to the session when validation fails, override the `formatValidationErrors` on your base controller. Don't forget to import the `Illuminate\Validation\Validator` class at the top of the file: 

만약 유효성 검사가 실패했을 때 세션에 기록되는 유효성 검사 오류의 형식을 커스터마이징하고 싶다면, base 컨트롤러의 `formatValidationErrors` 메소드를 재정의 하십시오. 이때, 파일 상단에서 `Illuminate\Validation\Validator`를 임포트하는 것을 잊지 마십시오:

	/**
	 * {@inheritdoc}
	 */
	protected function formatValidationErrors(Validator $validator)
	{
		return $validator->errors()->all();
	}

<a name="form-request-validation"></a>
## Form Request Validation 
## 폼 요청 유효성 검사

For more complex validation scenarios, you may wish to create a "form request". Form requests are custom request classes that contain validation logic. To create a form request class, use the `make:request` Artisan CLI command: 

여러분은 "폼 요청(form request)" 클래스를 만들고 싶을수도 있습니다. 폼 요청은 유효성검사 로직을 내장하는 사용자가 정의한 요청 클래스입니다. 폼 요청 클래스를 만드려면, `make:request` 아티즌 명령을 사용하십시오.

	php artisan make:request StoreBlogPostRequest

The generated class will be placed in the `app/Http/Requests` directory. Let's add a few validation rules to the `rules` method: 

생성된 요청 클래스는 `app/Http/Requests` 디렉토리에 위치합니다. 유효성 검사 룰 몇개를 `rules` 메소드에 추가해봅시다:

	/**
	 * Get the validation rules that apply to the request.
	 *
	 * @return array
	 */
	public function rules()
	{
		return [
			'title' => 'required|unique|max:255',
			'body' => 'required',
		];
	}

So, how are the validation rules executed? All you need to do is type-hint the request on your controller method: 

그럼 어떻게 이 유효성 검사 룰이 실행되도록 할 수 있을까요? 여러분의 컨트롤러 메소드에 해당 요청 클래스의 타입힌트를 넣어주기만 하면 됩니다.

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

The incoming form request is validated before the controller method is called, meaning you do not need to clutter your controller with any validation logic. It has already been validated! 

들어온 폼 요청은 컨트롤러 메소드가 실행되기 전에 미리 유효성 검사를 거칩니다. 이는 유효성 검사 로직으로 여러분의 컨트롤러를 지저분하게 할 필요가 없다는 것을 의미합니다. 이미 유효성 검사는 실행된 후입니다!

If validation fails, a redirect response will be generated to send the user back to their previous location. The errors will also be flashed to the session so they are available for display. If the request was an AJAX request, a HTTP response with a 422 status code will be returned to the user including a JSON representation of the validation errors.

만약 유효성 검사가 실패하면 이전 페이지로의 리다이렉트 응답이 생성되어 사용자에게 보내집니다. 오류들 또한 화면에 출력될 수 있도록 세션에 기록될 것입니다. 만약 요청이 AJAX 요청이면, 422 상태 코드의 HTTP 응답이 JSON 형식의 오류 메시지를 포함한 채 사용자에게 반환됩니다.

### Authorizing Form Requests 
### 폼 요청 권한 확인하기

The form request class also contains an `authorize` method. Within this method, you may check if the authenticated user actually has the authority to update a given resource. For example, if a user is attempting to update a blog post comment, do they actually own that comment? For example: 

또한 폼 요청 클래스는 `authorize` 메소드를 가지고 있습니다. 이 메소드 안에서 여러분은 사용자가 실제로 주어진 리소스를 업데이트할 권한을 가졌는지 확인 할 수 있습니다. 다음은 만약 사용자가 블로그 포스트의 댓글을 업데이트하려고 시도할 때, 실제로 그들이 그 댓글의 소유자인가를 확인하는 예제입니다:

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

Note the call to the `route` method in the example above. This method grants you access to the URI parameters defined on the route being called, such as the `{comment}` parameter in the example below: 

이 예제에서 `route`메소드를 호출하는 것에 주목하십시오. 이 메소드는 아래의 예제에서 `{comment}` 파라미터와 같이 호출된 라우트에 정의된 URI 파라미터에 여러분이 접근할 수 있는지 권한을 확인합니다:

	Route::post('comment/{comment}');

If the `authorize` method returns `false`, a HTTP response with a 403 status code will automatically be returned and your controller method will not execute.

만약 `authorize` 메소드가 `false`를 리턴하면, 403 HTTP 응답이 자동적으로 반환되고 컨트롤러 메소드는 실행되지 않을 것입니다.

If you plan to have authorization logic in another part of your application, simply return `true` from the `authorize` method: 

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

### Customizing The Flashed Error Format 
### 세션에 기록되는 오류 형식 커스터마이징 하기

If you wish to customize the format of the validation errors that are flashed to the session when validation fails, override the `formatErrors` on your base request (`App\Http\Requests\Request`). Don't forget to import the `Illuminate\Validation\Validator` class at the top of the file: 

만약 유효성 검사가 실패했을 때 세션에 기록될 유효성 검사 오류의 형식을 커스터마이징하고 싶다면, base 요청 클래스(`App\Http\Requests\Request`)의 `formatErrors` 메소드를 오버라이딩하십시오. 이때, 파일 상단에서 `Illuminate\Validation\Validator`를 임포트하는 것을 잊지 마십시오.

	/**
	 * {@inheritdoc}
	 */
	protected function formatErrors(Validator $validator)
	{
		return $validator->errors()->all();
	}

<a name="working-with-error-messages"></a>
## Working With Error Messages 
## 오류 메시지 사용하기

After calling the `messages` method on a `Validator` instance, you will receive a `MessageBag` instance, which has a variety of convenient methods for working with error messages. 

`Validator` 인스턴스의 `messages` 메소드를 실행하면, 오류 메시지를 편하게 사용할 수 있는 다양한 메소드를 가진 `MessageBag` 인스턴스를 받을 수 있습니다.

#### Retrieving The First Error Message For A Field 
#### 하나의 필드에 대한 첫번째 오류 메시지 조회하기

	echo $messages->first('email');

#### Retrieving All Error Messages For A Field 
#### 하나의 필드에 대한 모든 오류 메시지 조회하기

	foreach ($messages->get('email') as $message)
	{
		//
	}

#### Retrieving All Error Messages For All Fields 
#### 모든 필드에 대한 모든 오류 메시지 조회하기

	foreach ($messages->all() as $message)
	{
		//
	}

#### Determining If Messages Exist For A Field 
#### 하나의 필드에 대하여 오류 메시지가 존재하는지 검사하기

	if ($messages->has('email'))
	{
		//
	}

#### Retrieving An Error Message With A Format 
#### 특정 형식으로 오류 메시지 조회해 오기

	echo $messages->first('email', '<p>:message</p>');

> **Note:** By default, messages are formatted using Bootstrap compatible syntax. 

> **참고:** 기본적으로 메시지들은 Bootstrap에 호환되는 구문을 사용하여 구성되어 있습니다.

#### Retrieving All Error Messages With A Format 
#### 특정 형식으로 모든 오류 메시지 조회해 오기

	foreach ($messages->all('<li>:message</li>') as $message)
	{
		//
	}

<a name="error-messages-and-views"></a>
## Error Messages & Views 
## 오류 메시지 & 뷰

Once you have performed validation, you will need an easy way to get the error messages back to your views. This is conveniently handled by Laravel. Consider the following routes as an example: 

여러분이 유효성 검사를 실행하면, 뷰에서 오류 메시지를 쉽게 얻을 수 있는 방법이 필요할 것입니다. 라라벨에서는 이 것을 손쉽게 할 수 있도록 제공하고 있습니다. 아래의 라우트를 예로 들어 봅시다:

	Route::get('register', function()
	{
		return View::make('user.register');
	});

	Route::post('register', function()
	{
		$rules = [...];

		$validator = Validator::make(Input::all(), $rules);

		if ($validator->fails())
		{
			return redirect('register')->withErrors($validator);
		}
	});

Note that when validation fails, we pass the `Validator` instance to the Redirect using the `withErrors` method. This method will flash the error messages to the session so that they are available on the next request. 

유효성 검사가 실패했을 때, 우리는 `withErrors` 메소드를 사용하여 리다이렉트에 `Validator` 인스턴스를 전달합니다. 이 메소드는 오류 메시지를 다음 요청에서 사용할 수 있도록 세션에 기록합니다.

However, notice that we do not have to explicitly bind the error messages to the view in our GET route. This is because Laravel will always check for errors in the session data, and automatically bind them to the view if they are available. **So, it is important to note that an `$errors` variable will always be available in all of your views, on every request**, allowing you to conveniently assume the `$errors` variable is always defined and can be safely used. The `$errors` variable will be an instance of `MessageBag`. 

명시적으로 오류메시지를 GET 라우트의 뷰에 바인딩하지 말아야 하는 것을 유념해야 합니다. 왜냐하면 라라벨은 항상 세션 데이터에 유효성 검사 오류가 있는지 체크하고, 자동으로 오류들을 뷰에 바인딩하기 때문입니다. **따라서, `$errors` 변수는 항상 모든 뷰에서 사용될 수 있다는 것을 알고 있는 것이 중요합니다.** 이는 여러분이 `$errors` 변수가 항상 정의돼 있고 아무 지장없이 사용될 수 있다고 가정할 수 있도록 합니다. `$errors` 변수는 `MessageBag`의 인스턴스입니다.

So, after redirection, you may utilize the automatically bound `$errors` variable in your view: 

그렇기 때문에, 리다이렉트된 후 여러분은 자동으로 바인딩 된 `$errors` 변수를 뷰에서 활용할 수 있습니다.

	<?php echo $errors->first('email'); ?>

### Named Error Bags 
### 이름이 지정된 오류 메시지 백

If you have multiple forms on a single page, you may wish to name the `MessageBag` of errors. This will allow you to retrieve the error messages for a specific form. Simply pass a name as the second argument to `withErrors`: 

만약 여러개의 폼을 단일 페이지에서 사용한다면, 여러분은 오류의 `MessageBag`에 이름을 지정하고 싶을지도 모릅니다. 이는 여러분이 특정 폼에 대한 오류 메시지를 조회할 수 있도록 해줍니다. `withErrors` 메소드의 두번째 파라메터로 이름을 전달하기만 하면 됩니다.

	return redirect('register')->withErrors($validator, 'login');

You may then access the named `MessageBag` instance from the `$errors` variable: 

그 다음 `$errors` 변수로부터 이름이 지정된 `MessageBag` 인스턴스에 접근할 수 있습니다.

	<?php echo $errors->login->first('email'); ?>

<a name="available-validation-rules"></a>
## Available Validation Rules 
## 유효성 검사 룰

Below is a list of all available validation rules and their function: 
다음은 모든 유효성 검사 룰과 그들의 함수 목록입니다.

- [Accepted](#rule-accepted)
- [Active URL](#rule-active-url)
- [After (Date)](#rule-after)
- [Alpha](#rule-alpha)
- [Alpha Dash](#rule-alpha-dash)
- [Alpha Numeric](#rule-alpha-num)
- [Array](#rule-array)
- [Before (Date)](#rule-before)
- [Between](#rule-between)
- [Boolean](#rule-boolean)
- [Confirmed](#rule-confirmed)
- [Date](#rule-date)
- [Date Format](#rule-date-format)
- [Different](#rule-different)
- [Digits](#rule-digits)
- [Digits Between](#rule-digits-between)
- [E-Mail](#rule-email)
- [Exists (Database)](#rule-exists)
- [Image (File)](#rule-image)
- [In](#rule-in)
- [Integer](#rule-integer)
- [IP Address](#rule-ip)
- [Max](#rule-max)
- [MIME Types](#rule-mimes)
- [Min](#rule-min)
- [Not In](#rule-not-in)
- [Numeric](#rule-numeric)
- [Regular Expression](#rule-regex)
- [Required](#rule-required)
- [Required If](#rule-required-if)
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

The field under validation must be _yes_, _on_, _1_, or _true_. This is useful for validating "Terms of Service" acceptance. 

필드의 값이 _yes_, _on_, _1_, 또는 _true_이어야 합니다. 이 것은 "이용약관" 동의와 같은 필드의 검사에 유용합니다.

<a name="rule-active-url"></a>
#### active_url

The field under validation must be a valid URL according to the `checkdnsrr` PHP function. 

필드의 값이 PHP 함수 `checkdnsrr`에 기반하여 올바른 URL이어야 합니다.

<a name="rule-after"></a>
#### after:_date_

The field under validation must be a value after a given date. The dates will be passed into the PHP `strtotime` function. 

필드의 값이 주어진 날짜 이후여야 합니다. 이때 날짜는 PHP 함수 `strtotime`을 통해 생성된 값입니다.

<a name="rule-alpha"></a>
#### alpha

The field under validation must be entirely alphabetic characters. 

필드의 값이 완벽하게 알파벳 문자로 이루어져야 합니다.

<a name="rule-alpha-dash"></a>
#### alpha_dash

The field under validation may have alpha-numeric characters, as well as dashes and underscores. 

필드의 값이 알파벳 및 숫자와 dash(-), underscore(_)로 이루어져야 합니다.

<a name="rule-alpha-num"></a>
#### alpha_num

The field under validation must be entirely alpha-numeric characters. 

필드의 값이 완벽하게 알파벳 및 숫자로 이루어져야 합니다.

<a name="rule-array"></a>
#### array

The field under validation must be of type array. 

필드의 값이 반드시 배열 형태이어야 합니다.

<a name="rule-before"></a>
#### before:_date_

The field under validation must be a value preceding the given date. The dates will be passed into the PHP `strtotime` function. 

필드의 값이 반드시 주어진 날짜보다 앞서야 합니다. 날짜는 `strtotime` PHP 함수를 통해 비교됩니다.

<a name="rule-between"></a>
#### between:_min_,_max_

The field under validation must have a size between the given _min_ and _max_. Strings, numerics, and files are evaluated in the same fashion as the `size` rule. 

필드의 값이 주어진 _min_ 과 _max_의 사이의 값이어야 합니다. 문자열, 숫자, 그리고 파일이 `size` 룰에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-boolean"></a>
#### boolean

The field under validation must be able to be cast as a boolean. Accepted input are `true`, `false`, `1`, `0`, `"1"` and `"0"`. 

필드의 값이 반드시 불리언으로 캐스팅될 수 있어야 합니다. 허용되는 값은 `true`, `false`, `1`, `0`, `"1"`, `"0"` 입니다.

<a name="rule-confirmed"></a>
#### confirmed

The field under validation must have a matching field of `foo_confirmation`. For example, if the field under validation is `password`, a matching `password_confirmation` field must be present in the input. 

필드의 값이 `foo_confirmation`의 매칭되는 필드를 가져야 합니다. 예를 들어 만약 필드가 `password`이면, `password_confirmation` 필드가 입력 중에 있어야 합니다.

<a name="rule-date"></a>
#### date

The field under validation must be a valid date according to the `strtotime` PHP function. 

필드의 값이 `strtotime` PHP 함수에 따라 올바른 날짜여야 합니다.

<a name="rule-date-format"></a>
#### date_format:_format_

The field under validation must match the _format_ defined according to the `date_parse_from_format` PHP function. 

필드의 값이 반드시 정의된 _format_과 일지해야 합니다. `date_parse_from_format` PHP 함수에 따릅니다.

<a name="rule-different"></a>
#### different:_field_

The given _field_ must be different than the field under validation. 

필드의 값이 주어진 _field_의 값과 달라야 합니다.

<a name="rule-digits"></a>
#### digits:_value_

The field under validation must be _numeric_ and must have an exact length of _value_. 

필드의 값이 반드시 _숫자_여야 하고, 길이가 _value_이어야 합니다.

<a name="rule-digits-between"></a>
#### digits_between:_min_,_max_

The field under validation must have a length between the given _min_ and _max_. 

필드의 값이 주어진 _min_과 _max_ 사이의 길이를 갖는 숫자여야 합니다.

<a name="rule-email"></a>
#### email

The field under validation must be formatted as an e-mail address. 

필드의 값이 이메일 주소 형식이어야 합니다.

<a name="rule-exists"></a>
#### exists:_table_,_column_

The field under validation must exist on a given database table. 

필드의 값이 주어진 데이터베이스 테이블의 컬럼에 존재하는 값이어야 합니다.

#### Basic Usage Of Exists Rule 
#### exists 룰의 기본 사용법

	'state' => 'exists:states'

#### Specifying A Custom Column Name 
#### 특정 컬럼명 지정하기

	'state' => 'exists:states,abbreviation'

You may also specify more conditions that will be added as "where" clauses to the query: 

쿼리문의 "where" 구분에 추가될 더 많은 조건을 지정할 수 있습니다.

	'email' => 'exists:staff,email,account_id,1'

Passing `NULL` as a "where" clause value will add a check for a `NULL` database value: 

"where" 구문에 `NULL`을 사용하여 데이터베이스의 `NULL` 값인지 체크할 수 있습니다.

	'email' => 'exists:staff,email,deleted_at,NULL'

<a name="rule-image"></a>
#### image

The file under validation must be an image (jpeg, png, bmp, gif, or svg) 

이미지 파일(jpeg, png, bmp, gif, svg)이어야 합니다.

<a name="rule-in"></a>
#### in:_foo_,_bar_,...

The field under validation must be included in the given list of values. 

필드의 값이 주어진 목록에 포함돼 있어야 합니다.

<a name="rule-integer"></a>
#### integer

The field under validation must have an integer value. 

필드의 값이 정수여야 합니다.

<a name="rule-ip"></a>
#### ip

The field under validation must be formatted as an IP address. 

필드의 값이 IP 주소 형식이어야 합니다.

<a name="rule-max"></a>
#### max:_value_

The field under validation must be less than or equal to a maximum _value_. Strings, numerics, and files are evaluated in the same fashion as the [`size`](#rule-size) rule. 

필드의 값이 반드시 _value_보다 작거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 룰에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-mimes"></a>
#### mimes:_foo_,_bar_,...

The file under validation must have a MIME type corresponding to one of the listed extensions. 

파일의 MIME 타입이 주어진 확장자 리스트중에 하나와 일치해야 합니다.

#### Basic Usage Of MIME Rule 
#### MIME 룰의 기본 사용법

	'photo' => 'mimes:jpeg,bmp,png'

<a name="rule-min"></a>
#### min:_value_

The field under validation must have a minimum _value_. Strings, numerics, and files are evaluated in the same fashion as the [`size`](#rule-size) rule. 

필드의 값이 반드시 _value_ 보다 크거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](#rule-size) 룰에 의해 같은 방식으로 평가될 수 있습니다.

<a name="rule-not-in"></a>
#### not_in:_foo_,_bar_,...

The field under validation must not be included in the given list of values. 

필드의 값이 주어진 목록에 존재하지 않아야 합니다.

<a name="rule-numeric"></a>
#### numeric

The field under validation must have a numeric value. 

필드의 값이 숫자여야 합니다.

<a name="rule-regex"></a>
#### regex:_pattern_

The field under validation must match the given regular expression. 

필드의 값이 주어진 정규식 표현과 일치해야 합니다.

**Note:** When using the `regex` pattern, it may be necessary to specify rules in an array instead of using pipe delimiters, especially if the regular expression contains a pipe character. 

**참고:** `regex` 패턴을 사용할 때, 특히 정규 표현식에 파이프 문자열이 있다면, 파이프 구분자를 사용하는 대신 배열 형식을 사용하여 룰을 지정할 필요가 있습니다.

<a name="rule-required"></a>
#### required

The field under validation must be present in the input data. 

입력 값 중에 해당 필드가 존재해야 합니다.

<a name="rule-required-if"></a>
#### required_if:_field_,_value_,...

The field under validation must be present if the _field_ is equal to any _value_. 

만약 _filed_의 값이 _value_중의 하나와 일치한다면, 해당 필드가 반드시 존재해야 합니다.


<a name="rule-required-with"></a>
#### required_with:_foo_,_bar_,...

The field under validation must be present _only if_ any of the other specified fields are present. 

다른 지정된 필드중 하나라도 존재한다면, 해당 필드가 반드시 존재해야 합니다.

<a name="rule-required-with-all"></a>
#### required_with_all:_foo_,_bar_,...

The field under validation must be present _only if_ all of the other specified fields are present. 

다른 지정된 필드가 모두 존재한다면, 해당 필드가 반드시 존재해야 합니다.

<a name="rule-required-without"></a>
#### required_without:_foo_,_bar_,...

The field under validation must be present _only when_ any of the other specified fields are not present. 

다른 지정된 필드중 하나라도 존재하지 않으면, 해당 필드가 반드시 존재해야 합니다.

<a name="rule-required-without-all"></a>
#### required_without_all:_foo_,_bar_,...

The field under validation must be present _only when_ all of the other specified fields are not present. 

다른 지정된 필드가 모두 존재하지 않으면, 해당 필드가 존재해야 합니다.

<a name="rule-same"></a>
#### same:_field_

The given _field_ must match the field under validation. 

필드의 값이 주어진 _field_의 값과 일치해야 합니다.

<a name="rule-size"></a>
#### size:_value_

The field under validation must have a size matching the given _value_. For string data, _value_ corresponds to the number of characters. For numeric data, _value_ corresponds to a given integer value. For files, _size_ corresponds to the file size in kilobytes. 

필드의 값이 주어진 _value_와 일치하는 크기를 가져야 합니다. 문자열 데이터에서는 문자의 개수가 _value_와 일치해야 합니다. 숫자형식의 데이터에서는 주어진 정수값이 _value_와 일치해야 합니다. 파일에서는 킬로바이트 형식의 파일 사이즈가 _size_와 일치해야 합니다.

<a name="rule-string"></a>
#### string

The field under validation must be a string type. 

필드의 값이 반드시 문자열 형식이어야 합니다.

<a name="rule-timezone"></a>
#### timezone

The field under validation must be a valid timezone identifier according to the `timezone_identifiers_list` PHP function. 

필드의 값이 `timezone_identifiers_list` PHP 함수에 따라 유효한 timezone 식별자여야 합니다.

<a name="rule-unique"></a>
#### unique:_table_,_column_,_except_,_idColumn_

The field under validation must be unique on a given database table. If the `column` option is not specified, the field name will be used. 

필드의 값이 주어진 데이터베이스 테이블에서 고유한 값이어야 합니다. 만약 `column` 지정돼 있지 않다면 필드의 이름이 사용됩니다.

Occasionally, you may need to set a custom connection for database queries made by the Validator. As seen above, setting `unique:users` as a validation rule will use the default database connection to query the database. To override this, do the following:

때때로, 여러분은 Validator에 의해서 생성되는 데이터베이스 쿼리에 사용자가 지정한 커넥션을 필요로 할지도 모릅니다. 위에서의 검증 규칙 `unique:users` 에서는 쿼리에 대해서 기본 데이터 베이스 커넥션이 사용됩니다. 이를 재지정하려면 다음과 같이 하면 됩니다:

	$verifier = App::make('validation.presence');

	$verifier->setConnection('connectionName');

	$validator = Validator::make($input, [
		'name' => 'required',
		'password' => 'required|min:8',
		'email' => 'required|email|unique:users',
	]);

	$validator->setPresenceVerifier($verifier);

#### Basic Usage Of Unique Rule 
#### Unique 룰의 기본 사용법

	'email' => 'unique:users'

#### Specifying A Custom Column Name 
#### 특정 컬럼명 지정하기

	'email' => 'unique:users,email_address'

#### Forcing A Unique Rule To Ignore A Given ID 
#### 주어진 ID는 unique 룰에서 무시하기

	'email' => 'unique:users,email_address,10'

#### Adding Additional Where Clauses 
#### where 구문 추가하기

You may also specify more conditions that will be added as "where" clauses to the query: 

쿼리문의 "where"  구문에 추가할 조건을 더 지정할 수 있습니다:

	'email' => 'unique:users,email_address,NULL,id,account_id,1'

In the rule above, only rows with an `account_id` of `1` would be included in the unique check.

앞서의 룰에서 오직 `account_id`가 `1`인 행만 unique 유효성 검사에 포함됩니다. 

<a name="rule-url"></a>
#### url

The field under validation must be formatted as an URL. 

필드의 값이 URL 형식이어야 합니다.

> **Note:** This function uses PHP's `filter_var` method. 

> **참고:** 이 기능은 PHP의 `filter_var` 함수를 사용합니다.

<a name="conditionally-adding-rules"></a>
## Conditionally Adding Rules 
## 조건부 룰 추가하기

In some situations, you may wish to run validation checks against a field **only** if that field is present in the input array. To quickly accomplish this, add the `sometimes` rule to your rule list: 

어떤 상황에서는 필드가 입력 배열에 존재할 때에만 그 필드의 유효성 검사를 실행하고 싶을수도 있습니다. 이를 위해서는 룰의 목록에 `sometimes`를 추가하기만 하면 됩니다:

	$v = Validator::make($data, [
		'email' => 'sometimes|required|email',
	]);

In the example above, the `email` field will only be validated if it is present in the `$data` array. 

이 예제에서 `$data` 배열에 `email` 필드가 존재할 경우에만 그 필드의 유효성 검사가 실행됩니다.

#### Complex Conditional Validation 
#### 복잡한 조건부 유효성 검사

Sometimes you may wish to require a given field only if another field has a greater value than 100. Or you may need two fields to have a given value only when another field is present. Adding these validation rules doesn't have to be a pain. First, create a `Validator` instance with your _static rules_ that never change: 

때때로 여러분은 다른 필드가 100 이상의 값을 가질때에만 주어진 필드가 반드시 존재하길 바랄 수도 있습니다. 또는 다른 필드가 존재할 때에만 두개의 필드가 주어진 값을 가질 필요가 있을수도 있습니다. 이러한 유효성 검사 룰을 추가하는 것이 어렵지 않을 수 있습니다. 우선 _고정 룰_을 변경할 필요 없이 그대로 사용하여 `Validator` 인스턴스를 생성합니다:

	$v = Validator::make($data, [
		'email' => 'required|email',
		'games' => 'required|numeric',
	]);

Let's assume our web application is for game collectors. If a game collector registers with our application and they own more than 100 games, we want them to explain why they own so many games. For example, perhaps they run a game re-sell shop, or maybe they just enjoy collecting. To conditionally add this requirement, we can use the `sometimes` method on the `Validator` instance. 

여러분의 웹 애플리케이션이 게임 수집가들을 위한 사이트라고 가정해봅시다. 만약 100개 이상의 게임을 소유하고 있는 게임 수집가가 우리 사이트에 가입을 한다면, 우리는 그들이 왜 그렇게 많은 게임을 소유하고 있는지 설명을 듣고 싶을수 있습니다. 아마 그들이 중고게임 판매점을 운영하거나, 단순히 수집을 취미로 할 수도 있습니다. 이런 요구사항을 조건부로 추가하기 위하여 `Validator` 인스턴스의 `sometimes` 메소드를 사용할 수 있습니다.

	$v->sometimes('reason', 'required|max:500', function($input)
	{
		return $input->games >= 100;
	});

The first argument passed to the `sometimes` method is the name of the field we are conditionally validating. The second argument is the rules we want to add. If the `Closure` passed as the third argument returns `true`, the rules will be added. This method makes it a breeze to build complex conditional validations. You may even add conditional validations for several fields at once: 

`sometimes` 메소드의 첫번째 인자는 필드의 이름입니다. 두번째 인자는 추가하려는 룰입니다. 만약 세번째 파라메터로 전달된 `Closure`가 `true`를 리턴한다면 그 룰은 유효성 검사에 추가될 것입니다. 이 메소드는 복잡한 조건부 유효성 검사의 구성을 손쉽게 만들어 주며, 한번에 여러개의 필드에 대한 조건부 유효성 검사를 추가할 수도 있습니다.

	$v->sometimes(['reason', 'cost'], 'required', function($input)
	{
		return $input->games >= 100;
	});

> **Note:** The `$input` parameter passed to your `Closure` will be an instance of `Illuminate\Support\Fluent` and may be used as an object to access your input and files. 

> **참고:** `Closure`로 전달된 `$input` 파라메터는 `Illuminate\Support\Fluent`의 인스턴스입니다. 그리고 입력된 데이터와 파일에 접근하기 위해 이 오브젝트는 사용할 수 있습니다.

<a name="custom-error-messages"></a>
## Custom Error Messages 
## 사용자 정의 오류 메시지

If needed, you may use custom error messages for validation instead of the defaults. There are several ways to specify custom messages. 

만약 필요하다면 유효성 검사에서 기본 오류 메시지 대신 커스터마이징한 오류 메시지를 사용할 수 있습니다. 사용자 정의 오류 메시지를 지정하는 몇개의 방법이 있습니다.

#### Passing Custom Messages Into Validator 
#### Validator로 사용자 정의 메시지 전달하기

	$messages = [
		'required' => 'The :attribute field is required.',
	];

	$validator = Validator::make($input, $rules, $messages);

> *Note:* The `:attribute` place-holder will be replaced by the actual name of the field under validation. You may also utilize other place-holders in validation messages. 

> *참고:* `:attribute` place-holder는 유효성 검사 필드의 실제 이름으로 대체됩니다. 여러분은 유효성 검사 메시지에 있는 다른 place-holder를 활용할 수도 있습니다.

#### Other Validation Place-Holders 
#### 다른 유효성 검사 Place-Holders

	$messages = [
		'same'    => 'The :attribute and :other must match.',
		'size'    => 'The :attribute must be exactly :size.',
		'between' => 'The :attribute must be between :min - :max.',
		'in'      => 'The :attribute must be one of the following types: :values',
	];

#### Specifying A Custom Message For A Given Attribute 
#### 주어진 필드에 대한 사용자 정의 메시지 지정하기

Sometimes you may wish to specify a custom error messages only for a specific field: 
때때로 오직 하나의 특정 필드를 위해 커스텀 오류 메시지를 지정하고 싶을지도 모릅니다.

	$messages = [
		'email.required' => 'We need to know your e-mail address!',
	];

<a name="localization"></a>
#### Specifying Custom Messages In Language Files 
#### 언어 파일에 사용자 정의 메시지 지정하기

In some cases, you may wish to specify your custom messages in a language file instead of passing them directly to the `Validator`. To do so, add your messages to `custom` array in the `resources/lang/xx/validation.php` language file. 

어떤 경우에는 직접 `Validator`로 커스텀 메시지를 전달하는 대신 언어 파일에 커스텀 메시지를 지정하고 싶을 수도 있습니다. 이를 위하여, `resources/lang/xx/validation.php` 언어 파일의 `custom` 배열에 메시지를 추가하십시오.

	'custom' => [
		'email' => [
			'required' => 'We need to know your e-mail address!',
		],
	],

<a name="custom-validation-rules"></a>
## Custom Validation Rules 
## 사용자 정의 유효성 검사 룰

#### Registering A Custom Validation Rule 
#### 사용자 정의 유효성 검사 룰 등록하기

Laravel provides a variety of helpful validation rules; however, you may wish to specify some of your own. One method of registering custom validation rules is using the `Validator::extend` method: 

라라벨은 다양하고 유용한 유효성 검사 룰을 제공합니다; 하지만, 여러분은 여러분만의 유효성 검사 룰을 지정하길 바랄수도 있습니다. 커스텀 유효성 검사 룰을 등록하는 방법중 하나는 `Validator::extend` 메소드를 사용하는 것입니다:

	Validator::extend('foo', function($attribute, $value, $parameters)
	{
		return $value == 'foo';
	});

The custom validator Closure receives three arguments: the name of the `$attribute` being validated, the `$value` of the attribute, and an array of `$parameters` passed to the rule. 

커스텀 유효성 검사 클로저는 3개의 인자를 받습니다: 유효성 검사를 할 필드(`$attribute`)의 이름, 필드의 값(`$value`), 그리고 룰에 전달될 파라미터들(`$parameters`)의 배열입니다. 

You may also pass a class and method to the `extend` method instead of a Closure: 

또한 클로저 대신 클래스명과 메소드명을 `extend` 메소드로 전달할 수도 있습니다.

	Validator::extend('foo', 'FooValidator@validate');

Note that you will also need to define an error message for your custom rules. You can do so either using an inline custom message array or by adding an entry in the validation language file. 

또한 커스텀 룰에 대한 오류 메시지를 정의할 필요가 있다는 것을 유념하십시오. 인라인 커스텀 메시지 배열을 사용할 수도 있고 validation 언어파일에 추가할 수도 있습니다.

#### Extending The Validator Class 
#### Validator 클래스 확장하기

Instead of using Closure callbacks to extend the Validator, you may also extend the Validator class itself. To do so, write a Validator class that extends `Illuminate\Validation\Validator`. You may add validation methods to the class by prefixing them with `validate`: 

Validator를 확장하기 위해 클로저를 사용하는 대신에 Validator 클래스 자체를 확장할 수 있습니다. 이를 위하여, `Illuminate\Validation\Validator`를 상속받는 Validator 클래스를 작성하십시오. 메소드명 앞에 `validate`를 붙이는 방법으로 validation 메소드를 그 클래스에 추가할 수 있습니다:

	<?php
	
	class CustomValidator extends \Illuminate\Validation\Validator {

		public function validateFoo($attribute, $value, $parameters)
		{
			return $value == 'foo';
		}

	}

#### Registering A Custom Validator Resolver 
#### 사용자 정의 Validator Resolver 등록하기

Next, you need to register your custom Validator extension: 

그 다음, 확장한 사용자 정의 Validator를 등록해야 합니다.

	Validator::resolver(function($translator, $data, $rules, $messages)
	{
		return new CustomValidator($translator, $data, $rules, $messages);
	});

When creating a custom validation rule, you may sometimes need to define custom place-holder replacements for error messages. You may do so by creating a custom Validator as described above, and adding a `replaceXXX` function to the validator. 

사용자 정의된 유효성 검사 룰을 생성할 때, 가끔 오류 메시지에서 사용할 커스텀 place-holder를 정의할 필요가 있을수도 있습니다. 앞서 설명했던 사용자 정의 Validator를 생성하고 `replaceXXX` 메소드를 validator에 추가하는 방법을 사용하면 됩니다.

	protected function replaceFoo($message, $attribute, $rule, $parameters)
	{
		return str_replace(':foo', $parameters[0], $message);
	}

If you would like to add a custom message "replacer" without extending the `Validator` class, you may use the `Validator::replacer` method: 

만약 `Validator` 클래스를 확장하지 않고 사용자 정의 메시지 "replacer"를 추가하고 싶다면, `
Validator::replacer` 메소드를 사용하면 됩니다.

	Validator::replacer('rule', function($message, $attribute, $rule, $parameters)
	{
		//
	});
