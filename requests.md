# HTTP Requests

- [요청 인스턴스 획득하기](#obtaining-a-request-instance)
- [입력값 검색하기](#retrieving-input)
- [이전 입력값](#old-input)
- [쿠기](#cookies)
- [파일](#files)
- [기타 Request에 대한 정보](#other-request-information)

<a name="obtaining-a-request-instance"></a>
## Request 인스턴스 획득하기

### 파사드를 이용한 방법

`Request` 파사드는 컨테이너와 결합된 현재의 Request에 엑세스 할 수 있도록 해줍니다. 예를 들면:

	$name = Request::input('name');

만약 특정 네임스페이스 아래에서 `Request` 파사드를 사용하고자 한다면 클래스 상단부분에 `use Request;` 구문을 추가해야 된다는 것을 기억하십시오.

### 의존성 주입을 통한 방법

현재의 의존성 주입을 통해서 HTTP request을 획득하기 위해서는 여러분의 컨트롤러 생성자나 메소드에서 타입힌트를 지정해야 합니다. 현재의 request의 인스턴스는 [서비스 컨테이너](/docs/5.0/container)에 의해서 자동으로 주입될것입니다:

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

만약 컨트롤러 메소드에서 라우터 파라미터로부터 입력값을 받아야 한다면 의존성을 지정한 뒤에 라우트 인자를 나열하면 됩니다:

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

<!--chak-comment-HTTP-Requests-Request-인스턴스-획득하기-->

<a name="retrieving-input"></a>
## 입력값 검색하기

#### 입력값 검색하기

간단한 메소드를 통해서 `Illuminate\Http\Request` 인스턴스 모든 사용자 입력값에 엑세스 할 수 있습니다. request에서 어떤 HTTP 메소드를 사용했는지에 대해서는 걱정할 필요 없이 모든 HTTP 메소드에 대해서 같은 방법으로 입력값에 대해 엑세스가 가능합니다.

	$name = Request::input('name');

#### 입력값이 없을 때 기본값 가져오기

	$name = Request::input('name', 'Sally');

#### 입력값이 존재하는지 확인하기

	if (Request::has('name'))
	{
		//
	}

#### 전체 입력값 가져오기

	$input = Request::all();

#### Request 입력중에서 몇개의 값만 가져오기

	$input = Request::only('username', 'password');

	$input = Request::except('credit_card');

입력폼에 배열로 값이 전달된다면 ‘점’으로 구분하여 입력값에 엑세스 할 수 있습니다:

	$input = Request::input('products.0.name');

<!--chak-comment-HTTP-Requests-입력값-검색하기-->

<a name="old-input"></a>
## 이전 입력

라라벨에서는 현재 request의 입력값을 다음 request까지 유지하는 방법을 제공합니다. 예를 들어, 폼의 입력값 체크에서 에러가 발생하면 작성한 값들을 다시 채워줘야 할 필요가 있을 수 있습니다.

#### 입력값들 세션에 저장하기

`flash` 메소드는 현재의 입력들을 [세션](/docs/5.0/session)에 저장하여 사용자가 다음번에 request를 보내도 사용가능하게 만들어 줍니다.

	Request::flash();

#### 몇개의 입력값만 세션에 저장하기

	Request::flashOnly('username', 'email');

	Request::flashExcept('password');

#### 플래쉬 & 리다이렉트

대부분 이전 페이지로 리다이렉트 하면서 입력값을 플래슁 하기를 원하는 데, 이 경우 리다이렉트와 함께 입력값 플래싱을 메소드 체이닝으로 사용할 수 있습니다.

	return redirect('form')->withInput();

	return redirect('form')->withInput(Request::except('password'));

#### 이전 입력값 검색하기

이전 Request에 대해 저장된 입력값을 검색하기 위해서는 `Request` 인스턴스의 `old` 메소드를 사용하면 됩니다.

	$username = Request::old('username');

블레이드 템플릿 안에서 지난 입력값을 보여주려면 `old` 헬퍼함수를 사용하는 것이 보다 편리합니다:

	{{ old('username') }}

<!--chak-comment-HTTP-Requests-이전-입력-->

<a name="cookies"></a>
## 쿠키

모든 쿠키는 라라벨 프레임워크에서 인증 코드와 함께 암호화 됩니다. 이 말은 클라이언트가 변경되었을 때 쿠키가 유효하지 않다는 것을 의미합니다.

#### 쿠키 값 가져오기

	$value = Request::cookie('name');

#### Response에 새로운 쿠키 추가하기

`cookie` 헬퍼함수는 새로운 `Symfony\Component\HttpFoundation\Cookie` 인스턴스를 생성하기 위한 간단한 팩토리로 작동합니다. 쿠키를 `Response` 인스턴스에 추가하려면 `withCookie` 메소드를 사용하면 됩니다:

	$response = new Illuminate\Http\Response('Hello World');

	$response->withCookie(cookie('name', 'value', $minutes));

#### 영원히 남게되는 쿠키 생성하기

_영원히는 실제로는 5년을 의미합니다._

	$response->withCookie(cookie()->forever('name', 'value'));

#### 쿠키 큐처리 하기

해당 Response가 작성되기 전에 반환되는 Response에 추가할 쿠키를 “큐 처리”할 수 있습니다:

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

<!--chak-comment-HTTP-Requests-쿠키-->

<a name="files"></a>
## 파일 처리

#### 업로드한 파일 가져오기

	$file = Request::file('photo');

#### 파일이 업로드 되었는지 확인하기

	if (Request::hasFile('photo'))
	{
		//
	}

`file` 메소드는 `SplFileInfo` 클래스를 상속하고 있는 `Symfony\Component\HttpFoundation\File\UploadedFile`의 인스턴스를 반환하고 파일에 대한 처리를 할 수 있는 다양한 메소드를 제공합니다.

#### 업로드한 파일이 유효한지 판단하기

	if (Request::file('photo')->isValid())
	{
		//
	}

#### 업로드한 파일 이동하기

	Request::file('photo')->move($destinationPath);

	Request::file('photo')->move($destinationPath, $fileName);

### 기타 파일 메소드

그 밖에도 다양한 메소드들이 `UploadedFile` 인스턴스에 준비되어 있습니다. 추가적인 메소들에 대한 정보는 [API 문서](http://api.symfony.com/2.5/Symfony/Component/HttpFoundation/File/UploadedFile.html)를 참고하십시오.

<!--chak-comment-HTTP-Requests-파일-처리-->

<a name="other-request-information"></a>
## 기타 Request에 대한 정보

`Request` 클래스는 `Symfony\Component\HttpFoundation\Request` 클래스를 상속하고 있으며 어플리케이션을 위한 HTTP request을 확인하는 많은 메소드를 제공하고 있습니다. 다음은 몇몇 예시들입니다.

#### Request URI 가져오기

	$uri = Request::path();

#### Request 가 AJAX 요청인지 확인

	if (Request::ajax())
	{
		//
	}

#### Request 메소드 확인하기

	$method = Request::method();

	if (Request::isMethod('post'))
	{
		//
	}

#### 현재 request가 패턴에 일치하는지 확인하기

	if (Request::is('admin/*'))
	{
		//
	}

#### 현재 request URL 가져오기

	$url = Request::url();

<!--chak-comment-HTTP-Requests-기타-Request에-대한-정보-->
