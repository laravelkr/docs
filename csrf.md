# CSRF 보호

- [소개하기](#csrf-introduction)
- [특정 URI 제외시키기](#csrf-excluding-uris)
- [X-XSRF-Token](#csrf-x-xsrf-token)

<a name="csrf-introduction"></a>
## 소개하기

라라벨은 [크로스-사이트 요청 위조 공격](https://en.wikipedia.org/wiki/Cross-site_request_forgery) (CSRF)으로부터 애플리케이션을 손쉽게 보호할 수 있도록 해줍니다. 사이트 간 요청 위조는 인증된 사용자를 대신해서 승인되지 않은 커맨드를 악의적으로 활용하는 것입니다.

라라벨은 애플리케이션에 의해 관리되는 모든 활성화된 사용자 세션마다 CSRF "토큰"을 자동으로 만들어 줍니다. 이 토큰은 인증된 사용자가 애플리케이션에 request-요청을 할 수 있는 고유한 사용자라는 것을 확인하는데 사용됩니다.

HTML 폼을 정의할 때, CSRF 토큰을 hidden 필드로 포함시켜서, CSRF 보호 미들웨어가 request-요청을 검증(validate)할 수 있도록 해야합니다. 토큰 필드를 생성하기 위해 `@csrf` 블레이드 지시어를 사용할 수 있습니다.

    <form method="POST" action="/profile">
        @csrf
        ...
    </form>

`web` 미들웨어 그룹에 속한 `VerifyCsrfToken` [미들웨어](/docs/{{version}}/middleware)는 자동으로 request-요청에 포함된 토큰이 세션에 저장된 토큰과 일치하는지 확인할 것입니다.

#### CSRF 토큰 & JavaScript

자바스크립트를 기반으로한 애플리케이션을 구성할 때는, 자바스크립트 HTTP 라이브러리에서 모든 서버 request-요청에 CSRF 토큰을 자동으로 추가해주도록 하면 편리합니다. 기본적으로 `resources/js/bootstrap.js`  파일에 제공된 Axios HTTP 라이브러리는 암호화된 `XSRF-TOKEN` 쿠키의 값을 사용하여 `X-XSRF-TOKEN` 헤더를 자동으로 전송합니다. 이 라이브러리를 사용하지 않는 경우 애플리케이션에 대해 직접 구동해야 합니다.

<a name="csrf-excluding-uris"></a>
## CSRF 보호로 부터 특정 URI를 제외시키기

URI를 CSRF 보호에서 제외시키고 싶을 때가 있습니다. 예를 들어서, 결제를 진행하기 위해 [Stripe](https://stripe.com)를 사용하고 있고, Stripe의 웹훅 시스템을 활용하고 있다면, Stripe 웹훅 핸들러 라우트를 CSRF 보호로 부터 제외시킬 필요가 있을 것입니다. 왜냐하면 Stripe 는 여러분의 라우트에 어떤 CSRF 토큰을 보내야 하는지 모르기 때문입니다.

일반적으로, 제외하고 싶은 라우트를 `RouteServiceProvie`가 `routes/web.php`에 있는 모든 라우트에 적용하는 `web` 미들웨어 그룹 외부에 두어야 합니다. 이외에도, `VerifyCsrfToken` 미들웨어의 `$except` 속성에 URI를 추가하여 제외시킬 수도 있습니다.

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

    class VerifyCsrfToken extends Middleware
    {
        /**
         * The URIs that should be excluded from CSRF verification.
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

POST 파라미터로 넘어오는 CSRF 토큰을 체크하는 것에 더하여, `VerifyCsrfToken` 미들웨어는 `X-CSRF-TOKEN` request-요청 헤더 또한 확인합니다. 예를들자면, HTML `meta` 태그에 토큰을 저장할 수 있습니다.

    <meta name="csrf-token" content="{{ csrf_token() }}">

그리고, `meta` 태그를 만들고나서, jQeury와 같은 라이브러리를 사용하여 자동적으로 모든 헤더에 토큰을 추가할 수 있습니다. 이러한 방식은 AJAX 기반 애플리케이션을 위한 간단하고 편리한 CSRF 보호 방법을 제공해줍니다.

    $.ajaxSetup({
        headers: {
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        }
    });

<a name="csrf-x-xsrf-token"></a>
## X-XSRF-TOKEN

라라벨은 현재의 CSRF 토큰을 프레임워크가 생성하는 모든 요청에 포함되어 있는 `XSRF-TOKEN` 쿠키에 저장합니다. `X-XSRF-TOKEN` request-요청 헤더를 세팅하기 위해 쿠키 값을 사용할 수 있습니다.

이 쿠키는 주로 편의를 위해 보내집니다. 왜냐하면 Angular 와 Axios 같은 자바스크립트 프레임워크나 라이브러리는 그 값을 `X-XSRF-TOKEN` 헤더에 자동으로 설정하기 때문에 주로 편의상 발송됩니다.

> {tip} 기본적으로 `/js/bootstrap.js` 파일에는 자동으로 이를 전송하는 Axios HTTP 라이브러리가 포함되어 있습니다.