# CSRF Protection
# CSRF 보호

- [Introduction](#csrf-introduction)
- [소개하기](#csrf-introduction)
- [Preventing CSRF Requests](#preventing-csrf-requests)
- [사이트 간 요청 위조 방지하기](#preventing-csrf-requests)
    - [Excluding URIs](#csrf-excluding-uris)
    - [특정 URI 제외시기키](#csrf-excluding-uris)
- [X-CSRF-Token](#csrf-x-csrf-token)
- [X-CSRF-Token](#csrf-x-csrf-token)
- [X-XSRF-Token](#csrf-x-xsrf-token)
- [X-XSRF-Token](#csrf-x-xsrf-token)

<a name="csrf-introduction"></a>
## Introduction
## 소개하기

Cross-site request forgeries are a type of malicious exploit whereby unauthorized commands are performed on behalf of an authenticated user. Thankfully, Laravel makes it easy to protect your application from [cross-site request forgery](https://en.wikipedia.org/wiki/Cross-site_request_forgery) (CSRF) attacks.  

라라벨은 [크로스-사이트 요청 위조 공격 (CSRF)](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%9A%94%EC%B2%AD_%EC%9C%84%EC%A1%B0)( [영문](https://en.wikipedia.org/wiki/Cross-site_request_forgery))으로부터 애플리케이션을 손쉽게 보호할 수 있도록 해줍니다. 사이트 간 요청 위조는 인증된 사용자를 대신해서 승인되지 않은 커맨드를 악의적으로 활용하는 것입니다.

<a name="csrf-explanation"></a>
#### An Explanation Of The Vulnerability
#### 취약점에 대해 소개하기

In case you're not familiar with cross-site request forgeries, let's discuss an example of how this vulnerability can be exploited. Imagine your application has a `/user/email` route that accepts a `POST` request to change the authenticated user's email address. Most likely, this route expects an `email` input field to contain the email address the user would like to begin using.  

사이트 간 요청 위조에 대해 처음 들어본 분들을 위해 이 취약점을 악용할 수 있는 방법을 살펴보겠습니다. POST 요청을 통해 애플리케이션에 등록된 사용자가 자신의 이메일 주소를 변경할 수 있는 `/user/email` 라는 경로가 있다고 생각해 보십시오. 대부분 `email`라는 입력 요소에 사용하고자 하는 이메일 주소를 넣고 제출할 것입니다.

Without CSRF protection, a malicious website could create an HTML form that points to your application's `/user/email` route and submits the malicious user's own email address  

만약 CSRF 보호가 없다면 악의적인 웹사이트에서 애플리케이션의 `/user/email` 경로로 요청을 보낼 수 있는 HTML 폼을 만들어 사용자의 이메일 주소를 악성 사용자의 이메일 주소로 변경할 수 있습니다.

    <form action="https://your-application.com/user/email" method="POST">
        <input type="email" value="malicious-email@example.com">
    </form>

    <script>
        document.forms[0].submit();
    </script>

 If the malicious website automatically submits the form when the page is loaded, the malicious user only needs to lure an unsuspecting user of your application to visit their website and their email address will be changed in your application.  

악성 사용자가 자동으로 폼을 제출하는 악의적인 웹사이트를 만든 뒤 일반 사용자를 유인하여 성공한다면 해당 사용자의 이메일 주소를 악성 사용자의 이메일 주소로 변경 할 수 있습니다.

 To prevent this vulnerability, we need to inspect every incoming `POST`, `PUT`, `PATCH`, or `DELETE` request for a secret session value that the malicious application is unable to access.  

이 취약점을 방지하기 위해서는 `POST`, `PUT`, `PATCH`, `DELETE`와 같은 요청을 보낼 때 악성 애플리케이션이 접근할 수 없는 비밀 세션 값이 유효한지 검사해야 합니다.

<a name="preventing-csrf-requests"></a>
## Preventing CSRF Requests
## 사이트 간 요청 위조 방지하기

Laravel automatically generates a CSRF "token" for each active [user session](/docs/{{version}}/session) managed by the application. This token is used to verify that the authenticated user is the person actually making the requests to the application. Since this token is stored in the user's session and changes each time the session is regenerated, a malicious application is unable to access it.  

라라벨은 애플리케이션에서 관리하는 [사용자의 세션](/docs/{{version}}/session)마다 CSRF 토큰을 자동으로 생성합니다. 이 토큰은 인증된 사용자가 실제로 애플리케이션에서 요청을 했는지 확인하는 데 사용됩니다. 이 토큰은 사용자의 세션에 저장되며 세션이 재생성 될 때마다 변경되므로 악성 애플리케이션이 접근할 수 없습니다.

The current session's CSRF token can be accessed via the request's session or via the `csrf_token` helper function:  

현재 세션에 대한 CSRF 토큰은 Request 의 session() 메소드나 `csrf_token` 이라는 헬퍼 함수를 통해 접근할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/token', function (Request $request) {
        $token = $request->session()->token();

        $token = csrf_token();

        // ...
    });

Anytime you define a "POST", "PUT", "PATCH", or "DELETE" HTML form in your application, you should include a hidden CSRF `_token` field in the form so that the CSRF protection middleware can validate the request. For convenience, you may use the `@csrf` Blade directive to generate the hidden token input field:  

애플리케이션에서 `POST`, `PUT`, `PATCH`, `DELETE` 폼을 만들 때 `_token`명칭을 가진 `hidden`타입의 입력 필드를 포함시켜 CSRF 보호 미들웨어가 요청을 확인할 수 있도록 해야 합니다. 편의를 위해 Blade 지시문 중 하나인 `@csrf`를 사용하여 숨겨진 토큰 입력 필드를 만들 수 있습니다.

    <form method="POST" action="/profile">
        @csrf

        <!-- 아래와 같이 사용할 수 있습니다. -->
        <input type="hidden" name="_token" value="{{ csrf_token() }}" />
    </form>

The `App\Http\Middleware\VerifyCsrfToken` [middleware](/docs/{{version}}/middleware), which is included in the `web` middleware group by default, will automatically verify that the token in the request input matches the token stored in the session. When these two tokens match, we know that the authenticated user is the one initiating the request.  

기본적으로 `web` 미들웨어 그룹에 포함된 `App\Http\Middleware\VerifyCsrfToken` [미들웨어](/docs/{{version}}/middleware)는 요청에 포함된 토큰이 일치하는지 자동으로 확인합니다. 세션에 저장된 값과 토큰이 일치하면 인증된 사용자가 요청을 보낸 사용자라는 것을 알 수 있습니다.

<a name="csrf-tokens-and-spas"></a>
### CSRF Tokens & SPAs
### CSRF 토큰과 SPA

If you are building an SPA that is utilizing Laravel as an API backend, you should consult the [Laravel Sanctum documentation](/docs/{{version}}/sanctum) for information on authenticating with your API and protecting against CSRF vulnerabilities.  

라라벨을 API 백앤드로 활용하는 싱글 페이지 애플리케이션(SPA)을 만들고 있다면 [라라벨 Sanctum 문서](/docs/{{version}}/sanctum)에서 API 인증과 CSRF 취약점 보호 방법에 대해 알아보십시오.

<a name="csrf-excluding-uris"></a>
### Excluding URIs From CSRF Protection
### CSRF 보호로 부터 특정 주소를 제외시키기

Sometimes you may wish to exclude a set of URIs from CSRF protection. For example, if you are using [Stripe](https://stripe.com) to process payments and are utilizing their webhook system, you will need to exclude your Stripe webhook handler route from CSRF protection since Stripe will not know what CSRF token to send to your routes.  

특정한 주소를 CSRF 보호에서 제외시키고 싶을 때가 있습니다. 예를 들어서, 결제를 진행하기 위해 [Stripe](https://stripe.com)를 사용하고 있고, Stripe의 웹훅 시스템을 활용하고 있다면, Stripe 웹훅 핸들러 라우트를 CSRF 보호로 부터 제외시킬 필요가 있을 것입니다. 왜냐하면 Stripe 는 여러분의 라우트에 어떤 CSRF 토큰을 보내야 하는지 모르기 때문입니다.

Typically, you should place these kinds of routes outside of the `web` middleware group that the `App\Providers\RouteServiceProvider` applies to all routes in the `routes/web.php` file. However, you may also exclude the routes by adding their URIs to the `$except` property of the `VerifyCsrfToken` middleware:  

일반적으로, 제외하고 싶은 라우트를 `App\Providers\RouteServiceProvie`가 `routes/web.php`에 있는 모든 라우트에 적용하는 `web` 미들웨어 그룹 외부에 두어야 합니다. 이외에도, `VerifyCsrfToken` 미들웨어의 `$except` 속성에 URI를 추가하여 제외시킬 수도 있습니다.

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

    class VerifyCsrfToken extends Middleware
    {
        /**
		* The URIs that should be excluded from CSRF verification.  
         * CSRF 검증에서 제외해야 하는 URI입니다.
         *
         * @var array
         */
        protected $except = [
            'stripe/*',
            'http://example.com/foo/bar',
            'http://example.com/foo/*',
        ];
    }

> {tip} For convenience, the CSRF middleware is automatically disabled for all routes when [running tests](/docs/{{version}}/testing).  

> {tip} [테스트 실행](/docs/{{version}}/testing) 중에는, CSRF 미들웨어가 자동으로 비활성화 됩니다.

<a name="csrf-x-csrf-token"></a>
## X-CSRF-TOKEN
## X-CSRF-TOKEN

In addition to checking for the CSRF token as a POST parameter, the `App\Http\Middleware\VerifyCsrfToken` middleware will also check for the `X-CSRF-TOKEN` request header. You could, for example, store the token in an HTML `meta` tag:  

POST 요청과 함께 넘어온 CSRF 토큰을 확인하는 것 외에도 `App\Http\Middleware\VerifyCsrfToken` 미들웨어는 `X-CSRF-TOKEN` 헤더도 확인합니다. 또 아래와 같이 Html의 Meta 태그에 토큰을 저장할 수 있습니다.

    <meta name="csrf-token" content="{{ csrf_token() }}">

Then, you can instruct a library like jQuery to automatically add the token to all request headers. This provides simple, convenient CSRF protection for your AJAX based applications using legacy JavaScript technology:  

`meta` 태그를 만들고나서, jQeury와 같은 라이브러리를 사용하여 자동적으로 모든 헤더에 토큰을 추가할 수 있습니다. 이러한 방식은 AJAX 기반 애플리케이션을 위한 간단하고 편리한 CSRF 보호 방법을 제공해줍니다.

    $.ajaxSetup({
        headers: {
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        }
    });

<a name="csrf-x-xsrf-token"></a>
## X-XSRF-TOKEN
## X-XSRF-TOKEN

Laravel stores the current CSRF token in an encrypted `XSRF-TOKEN` cookie that is included with each response generated by the framework. You can use the cookie value to set the `X-XSRF-TOKEN` request header.  

라라벨은 프레임워크에 의해 자동으로 생성되어 매 요청마다 포함되는 XSRF-TOKEN 쿠키에 현재 CSRF 토큰을 암호화하여 저장합니다. 저장된 쿠키 값을 통해 X-XSRF-TOKEN 헤더에 값을 전달 할 수 있습니다.

This cookie is primarily sent as a developer convenience since some JavaScript frameworks and libraries, like Angular and Axios, automatically place its value in the `X-XSRF-TOKEN` header on same-origin requests.  

이 쿠키는 Angular 및 Axios와 같은 일부 자바스크립트 프레임워크 및 라이브러리의 개발자에게 편의를 제공하기 위해 전송됩니다. 프레임워크와 라이브러리는 이 쿠키를 통해 X-XSRF-TOKEN 헤더에 값을 설정하여 same-origin 요청을 보낼 수 있도록 합니다.

> {tip} By default, the `resources/js/bootstrap.js` file includes the Axios HTTP library which will automatically send the `X-XSRF-TOKEN` header for you.  

> {tip} 기본적으로 `resources/js/bootstrap.js` 파일에는 자동으로 이를 전송하는 Axios HTTP 라이브러리가 포함되어 있습니다.