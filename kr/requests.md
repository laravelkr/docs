# HTTP Requests 
# HTTP Requests

- [Obtaining A Request Instance 요청 인스턴스 획득하기](#obtaining-a-request-instance)
- [Retrieving Input 입력값 검색하기](#retrieving-input)
- [Old Input 이전 입력값](#old-input)
- [Cookies 쿠기](#cookies)
- [Files 파일](#files)
- [Other Request Information 기타 Request에 대한 정보](#other-request-information)

<a name="obtaining-a-request-instance"></a>
## Obtaining A Request Instance
## Request 인스턴스 획득하기

### Via Facade
### 파사드를 이용한 방법

The `Request` facade will grant you access to the current request that is bound in the container. For example: `Request` 파사드는 컨테이너와 결합된 현재의 Request에 엑세스 할 수 있도록 해줍니다. 예를 들면 : 

	$name = Request::input('name');

Remember, if you are in a namespace, you will have to import the `Request` facade using a `use Request;` statement at the top of your class file. 만약 특정 네임스페이스 아래에서 `Request` 파사드를 사용하고자 한다면 클래스 상단부분에 `use Request;` 구문을 추가해야 된다는 것을 기억하십시오. 

### Via Dependency Injection
### 의존성 주입을 통한 방법 

To obtain an instance of the current HTTP request via dependency injection, you should type-hint the class on your controller constructor or method. 현재의 의존성 주입을 통해서 HTTP request 을 획득하기 위해서는 여러분의 컨트롤러 생성자나 메소드에서 타입힌트를 지정해야 합니다. The current request instance will automatically be injected by the [service container](/docs/{{version}}/container): 현재의  request의 인스턴스는 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 자동으로 주입될것 입니다. 

	<?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use Illuminate\Routing\Controller;

	class UserController extends Controller {

		/**
		 * Store a new user.
		 *
		 * @param  Request  $request
		 * @return Response
		 */
		public function store(Request $request)
		{
			$name = $request->input('name');

			//
		}

	}

If your controller method is also expecting input from a route parameter, simply list your route arguments after your other dependencies: 만약 컨트롤러 메소드에서 라우터 파라미터로 부터 입력값을 받아야 한다면 의존성을 지정한 뒤에 라우트 인자를 나열하면 됩니다. 

	<?php namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use Illuminate\Routing\Controller;

	class UserController extends Controller {

		/**
		 * Update the specified user.
		 *
		 * @param  Request  $request
		 * @param  int  $id
		 * @return Response
		 */
		public function update(Request $request, $id)
		{
			//
		}

	}

<a name="retrieving-input"></a>
## Retrieving Input
## 입력값 검색하기

#### Retrieving An Input Value
#### 입력값 검색하기

Using a few simple methods, you may access all user input from your `Illuminate\Http\Request` instance. 간단한 메소드를 통해서 `Illuminate\Http\Request` 인스턴스 모든 사용자 입력값에 엑세스 할 수 있습니다. You do not need to worry about the HTTP verb used for the request, as input is accessed in the same way for all verbs. request 에서 어떤 HTTP 메소드를 사용했는지에 대해서는 걱정할 필요 없이 모든 HTTP 메소드에 대해서 같은 방법으로 입력값에 대해 엑세스가 가능합니다. 

	$name = Request::input('name');

#### Retrieving A Default Value If The Input Value Is Absent
#### 입력값이 없을 때 기본값 가져오기

	$name = Request::input('name', 'Sally');

#### Determining If An Input Value Is Present
#### 입력값이 존재하는지 확인하기

	if (Request::has('name'))
	{
		//
	}

#### Getting All Input For The Request
#### 전체 입력값 가져오기

	$input = Request::all();

#### Getting Only Some Of The Request Input
#### Request 입력중에서 몇개의 값만 가져오기

	$input = Request::only('username', 'password');

	$input = Request::except('credit_card');

When working on forms with "array" inputs, you may use dot notation to access the arrays:
입력폼에 배열로 값이 전달된다면 ‘점’으로 구분하여 입력값에 엑세스 할 수 있습니다. 

	$input = Request::input('products.0.name');

<a name="old-input"></a>
## Old Input
## 이전 입력

Laravel also allows you to keep input from one request during the next request. 라라벨에서는 현재 request 의 입력값을 다음 request 까지 유지하는 방법을 제공합니다. For example, you may need to re-populate a form after checking it for validation errors. 예를 들어 폼의 입력값 체크에서 에러가 발생하면 작성한 값들을 다시 채워줘야 할 필요가 있을 수 있습니다. 

#### Flashing Input To The Session
#### 입력값들 세션에 저장하기

The `flash` method will flash the current input to the [session](/docs/{{version}}/session) so that it is available during the user's next request to the application: `flash` 메소드는 현재의 입력들을 [세션](/docs/{{version}}/session)에 저장하여 사용자가 다음번에 request 를 보내도 사용가능하게 만들어 줍니다. 

	Request::flash();

#### Flashing Only Some Input To The Session
#### 몇개의 입력값만 세션에 저장하기

	Request::flashOnly('username', 'email');

	Request::flashExcept('password');

#### Flash & Redirect
#### 플래쉬 & 리다이렉트

Since you often will want to flash input in association with a redirect to the previous page, you may easily chain input flashing onto a redirect. 대부분 이전 페이지로 리다이렉트 하면서 입력값을 플래슁 하기를 원하는데, 이 경우 리다이렉트와 함께 입력값 플래싱을 메소드 체이닝으로 사용할 수 있습니다. 

	return redirect('form')->withInput();

	return redirect('form')->withInput(Request::except('password'));

#### Retrieving Old Data
#### 이전 입력값 검색하기

To retrieve flashed input from the previous request, use the `old` method on the `Request` instance. 이전 Request 에 대해 저장된 입력값을 검색하기 위해서는 `Request` 인스턴스의 `old` 메소드를 사용하면 됩니다. 

	$username = Request::old('username');

If you are displaying old input within a Blade template, it is more convenient to use the `old` helper: 블레이드 템플릿 안에서 지난 입력값을 보여주려면 `old` 헬퍼 함수를 사용하는 것이 보다 편리합니다. 

	{{ old('username') }}

<a name="cookies"></a>
## Cookies
## 쿠키

All cookies created by the Laravel framework are encrypted and signed with an authentication code, meaning they will be considered invalid if they have been changed by the client. 모든 쿠키는 라라벨 프레임워크에서 인증 코드와 함께 암호화 됩니다. 이 말은 클라이언트가 변경되었을 때 쿠키가 유효하지 않다는 것을 의미합니다. 

#### Retrieving A Cookie Value
#### 쿠키 값 가져오기

	$value = Request::cookie('name');

#### Attaching A New Cookie To A Response
#### Response 에 새로운 쿠키 추가하기

The `cookie` helper serves as a simple factory for generating new `Symfony\Component\HttpFoundation\Cookie` instances. `cookie` 헬퍼 함수는 새로운 `Symfony\Component\HttpFoundation\Cookie` 인스턴스를 생성하기 위한 간단한 팩토리로 작동합니다. The cookies may be attached to a `Response` instance using the `withCookie` method: 쿠키를 `Response` 인스턴스에 추가하려면 `withCookie` 메소드를 사용하면 됩니다. 

	$response = new Illuminate\Http\Response('Hello World');

	$response->withCookie(cookie('name', 'value', $minutes));

#### Creating A Cookie That Lasts Forever*
#### 영원히 남게되는 쿠키 생성하기

_By "forever", we really mean five years._
_영원히는 실제로는 5년을 의미합니다._

	$response->withCookie(cookie()->forever('name', 'value'));

#### Queueing Cookies
#### 쿠키 큐처리 하기

You may also "queue" a cookie to be added to the outgoing response, even before that response has been created: 해당 Response가 작성되기 전에 반환되는 Response에 추가 할 쿠키를 “큐 처리”할 수 있습니다 :

	<?php namespace App\Http\Controllers;

	use Cookie;
	use Illuminate\Routing\Controller;

	class UserController extends Controller
	{
		/**
		 * Update a resource
		 *
		 * @return Response
		 */
		 public function update()
		 {
		 	Cookie::queue('name', 'value');

		 	return response('Hello World');
		 }
	}

<a name="files"></a>
## Files
## 파일

#### Retrieving An Uploaded File
#### 업로드한 파일 가져오기

	$file = Request::file('photo');

#### Determining If A File Was Uploaded
#### 파일이 업로드 되었는지 확인하기

	if (Request::hasFile('photo'))
	{
		//
	}

The object returned by the `file` method is an instance of the `Symfony\Component\HttpFoundation\File\UploadedFile` class, which extends the PHP `SplFileInfo` class and provides a variety of methods for interacting with the file. `file` 메소드는 `SplFileInfo` 클래스를 상속하고 있는 `Symfony\Component\HttpFoundation\File\UploadedFile`의 인스턴스를 반환하고 파일에 대한 처리를 할 수 있는 다양한 메소드를 제공합니다. 

#### Determining If An Uploaded File Is Valid
#### 업로드한 파일이 유효한지 판단하기

	if (Request::file('photo')->isValid())
	{
		//
	}

#### Moving An Uploaded File
#### 업로드한 파일 이동하기

	Request::file('photo')->move($destinationPath);

	Request::file('photo')->move($destinationPath, $fileName);

### Other File Methods
### 기타 파일 메소드

There are a variety of other methods available on `UploadedFile` instances. 그 밖에도 다양한 메소드들이 `UploadedFile` 인스턴스에 준비되어 있습니다. Check out the [API documentation for the class](http://api.symfony.com/2.5/Symfony/Component/HttpFoundation/File/UploadedFile.html) for more information regarding these methods. 추가적인 메소들에 대한 정보는 [API 문서](http://api.symfony.com/2.5/Symfony/Component/HttpFoundation/File/UploadedFile.html)를 참고하십시오. 

<a name="other-request-information"></a>
## Other Request Information
## 기타 Request에 대한 정보

The `Request` class provides many methods for examining the HTTP request for your application and extends the `Symfony\Component\HttpFoundation\Request` class. Here are some of the highlights. `Request` 클래스는 `Symfony\Component\HttpFoundation\Request` 클래스를 상속하고 있으며 애플리케이션을 위한 HTTP request을 확인하는 많은 메소드를 제공하고 있습니다. 다음은 몇몇 예시들입니다. 

#### Retrieving The Request URI
#### Request URI 가져오기

	$uri = Request::path();
	
#### Determine If The Request Is Using AJAX
#### Request 가 AJAX 요청인지 확인

	if (Request::ajax())
	{
		//
	}

#### Retrieving The Request Method
#### Request 메소드 확인하기

	$method = Request::method();

	if (Request::isMethod('post'))
	{
		//
	}

#### Determining If The Request Path Matches A Pattern
#### 현재 request 가 패턴에 일치하는지 확인하기

	if (Request::is('admin/*'))
	{
		//
	}

#### Get The Current Request URL
#### 현재 request URL 가져오기

	$url = Request::url();
