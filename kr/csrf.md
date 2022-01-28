# CSRF 보호

- [소개하기](#csrf-introduction)
- [사이트 간 요청 위조 방지하기](#preventing-csrf-requests)
    - [특정 URI 제외시기키](#csrf-excluding-uris)
- [X-CSRF-Token](#csrf-x-csrf-token)
- [X-XSRF-Token](#csrf-x-xsrf-token)

<a name="csrf-introduction"></a>
## 소개하기

라라벨은 크로스-사이트 요청 위조 공격 (CSRF)으로부터 애플리케이션을 손쉽게 보호할 수 있도록 해줍니다. 사이트 간 요청 위조는 인증된 사용자를 대신해서 승인되지 않은 커맨드를 악의적으로 활용하는 것입니다.

<a name="csrf-explanation"></a>
#### 취약점에 대해 소개하기

사이트 간 요청 위조에 대해 처음 들어본 분들을 위해 이 취약점을 악용할 수 있는 방법을 살펴보겠습니다. POST 요청을 통해 애플리케이션에 등록된 사용자가 자신의 이메일 주소를 변경할 수 있는 `/user/email` 라는 경로가 있다고 생각해 보십시오. 대부분 `email`라는 입력 요소에 사용하고자 하는 이메일 주소를 넣고 제출할 것입니다.

만약 CSRF 보호가 없다면 악의적인 웹사이트에서 애플리케이션의 `/user/email` 경로로 요청을 보낼 수 있는 HTML 폼을 만들어 사용자의 이메일 주소를 악성 사용자의 이메일 주소로 변경할 수 있습니다.

    <form action="https://your-application.com/user/email" method="POST">
        <input type="email" value="malicious-email@example.com">
    </form>

    <script>
        document.forms[0].submit();
    </script>

악성 사용자가 자동으로 폼을 제출하는 악의적인 웹사이트를 만든 뒤 일반 사용자를 유인하여 성공한다면 해당 사용자의 이메일 주소를 악성 사용자의 이메일 주소로 변경 할 수 있습니다.

이 취약점을 방지하기 위해서는 `POST`, `PUT`, `PATCH`, `DELETE`와 같은 요청을 보낼 때 악성 애플리케이션이 접근할 수 없는 비밀 세션 값이 유효한지 검사해야 합니다.

<a name="preventing-csrf-requests"></a>
## 사이트 간 요청 위조 방지하기

라라벨은 애플리케이션에서 관리하는 [HTTP 세션](/docs/{{version}}/session)마다 CSRF 토큰을 자동으로 생성합니다. 이 토큰은 인증된 사용자가 실제로 애플리케이션에서 요청을 했는지 확인하는 데 사용됩니다. 이 토큰은 사용자의 세션에 저장되며 세션이 재생성 될 때마다 변경되므로 악성 애플리케이션이 접근할 수 없습니다.

현재 세션에 대한 CSRF 토큰은 Request 의 session() 메소드나 `csrf_token` 이라는 헬퍼 함수를 통해 접근할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/token', function (Request $request) {
        $token = $request->session()->token();

        $token = csrf_token();

        // ...
    });

애플리케이션에서 `POST`, `PUT`, `PATCH`, `DELETE` 폼을 만들 때 `_token`명칭을 가진 `hidden`타입의 입력 필드를 포함시켜 CSRF 보호 미들웨어가 요청을 확인할 수 있도록 해야 합니다. 편의를 위해 Blade 지시문 중 하나인 `@csrf`를 사용하여 숨겨진 토큰 입력 필드를 만들 수 있습니다.

    <form method="POST" action="/profile">
        @csrf

        <!-- 아래와 같이 사용할 수 있습니다. -->
        <input type="hidden" name="_token" value="{{ csrf_token() }}" />
    </form>

기본적으로 `web` 미들웨어 그룹에 포함된 `App\Http\Middleware\VerifyCsrfToken` [미들웨어](/docs/{{version}}/middleware)는 요청에 포함된 토큰이 일치하는지 자동으로 확인합니다. 세션에 저장된 값과 토큰이 일치하면 인증된 사용자가 요청을 보낸 사용자라는 것을 알 수 있습니다.

<a name="csrf-tokens-and-spas"></a>
### CSRF 토큰과 SPA

라라벨을 API 백앤드로 활용하는 싱글 페이지 애플리케이션(SPA)을 만들고 있다면 [라라벨 Sanctum 문서](/docs/{{version}}/sanctum)에서 API 인증과 CSRF 취약점 보호 방법에 대해 알아보십시오.

<a name="csrf-excluding-uris"></a>
### CSRF 보호로 부터 특정 주소를 제외시키기

특정한 주소를 CSRF 보호에서 제외시키고 싶을 때가 있습니다. 예를 들어서, 결제를 진행하기 위해 [Stripe](https://stripe.com)를 사용하고 있고, Stripe의 웹훅 시스템을 활용하고 있다면, Stripe 웹훅 핸들러 라우트를 CSRF 보호로 부터 제외시킬 필요가 있을 것입니다. 왜냐하면 Stripe 는 여러분의 라우트에 어떤 CSRF 토큰을 보내야 하는지 모르기 때문입니다.

일반적으로, 제외하고 싶은 라우트를 `App\Providers\RouteServiceProvie`가 `routes/web.php`에 있는 모든 라우트에 적용하는 `web` 미들웨어 그룹 외부에 두어야 합니다. 이외에도, `VerifyCsrfToken` 미들웨어의 `$except` 속성에 URI를 추가하여 제외시킬 수도 있습니다.

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

    class VerifyCsrfToken extends Middleware
    {
        /**
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

> {tip} [테스트 실행](/docs/{{version}}/testing) 중에는, CSRF 미들웨어가 자동으로 비활성화 됩니다.

<a name="csrf-x-csrf-token"></a>
## X-CSRF-TOKEN

POST 요청과 함께 넘어온 CSRF 토큰을 확인하는 것 외에도 `App\Http\Middleware\VerifyCsrfToken` 미들웨어는 `X-CSRF-TOKEN` 헤더도 확인합니다. 또 아래와 같이 Html의 Meta 태그에 토큰을 저장할 수 있습니다.

    <meta name="csrf-token" content="{{ csrf_token() }}">

그리고, `meta` 태그를 만들고나서, jQeury와 같은 라이브러리를 사용하여 자동적으로 모든 헤더에 토큰을 추가할 수 있습니다. 이러한 방식은 AJAX 기반 애플리케이션을 위한 간단하고 편리한 CSRF 보호 방법을 제공해줍니다.

    $.ajaxSetup({
        headers: {
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        }
    });

<a name="csrf-x-xsrf-token"></a>
## X-XSRF-TOKEN

라라벨은 프레임워크에 의해 자동으로 생성되어 매 요청마다 포함되는 XSRF-TOKEN 쿠키에 현재 CSRF 토큰을 암호화하여 저장합니다. 저장된 쿠키 값을 통해 X-XSRF-TOKEN 헤더에 값을 전달 할 수 있습니다.

이 쿠키는 Angular 및 Axios와 같은 일부 자바스크립트 프레임워크 및 라이브러리의 개발자에게 편의를 제공하기 위해 전송됩니다. 프레임워크와 라이브러리는 이 쿠키를 통해 X-XSRF-TOKEN 헤더에 값을 설정하여 same-origin 요청을 보낼 수 있도록 합니다.

> {tip} 기본적으로 `resources/js/bootstrap.js` 파일에는 자동으로 이를 전송하는 Axios HTTP 라이브러리가 포함되어 있습니다.