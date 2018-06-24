# HTTP Middleware HTTP 미들웨어

- [Introduction](#introduction) [소개](#introduction) 
- [Defining Middleware](#defining-middleware) [미들웨어 정의하기](#defining-middleware)
- [Registering Middleware](#registering-middleware) [미들웨어 등록하기](#registering-middleware)
- [Terminable Middleware](#terminable-middleware) [종료가능한 미들웨어](#terminable-middleware)

<a name="introduction"></a>
## Introduction 소개

HTTP middleware provide a convenient mechanism for filtering HTTP requests entering your application. HTTP 미들웨어는 애플리케이션으로 들어온 HTTP 요청을 간편하게 필터링할 수 있는 방법을 제공합니다. For example, Laravel includes a middleware that verifies the user of your application is authenticated. 예를 들어, 라라벨은 애플리케이션의 사용자가 인증되었는지 검사하는 미들웨어를 내장하고 있습니다. If the user is not authenticated, the middleware will redirect the user to the login screen. 만약 인증되지 않은 사용자라면, 미들웨어는 그 사용자를 로그인 화면으로 리다이렉트 시킬 것입니다. However, if the user is authenticated, the middleware will allow the request to proceed further into the application. 반대로,  인증된 사용자라면, 미들웨어는 애플리케이션에서 HTTP 요청이 계속해서 더 처리되도록 허용할 것입니다.

Of course, middleware can be written to perform a variety of tasks besides authentication. 물론, 미들웨어는 인증 이외에도 다양한 작업을 수행하도록 작성될 수 있습니다. A CORS middleware might be responsible for adding the proper headers to all responses leaving your application. CORS 미들웨어는 애플리케이션에서 내보내는 모든 응답에 적절한 헤더들을 추가하는 역할을 담당할 수도 있습니다. A logging middleware might log all incoming requests to your application. 로깅 미들웨어는 애플리케이션으로 들어오는 모든 요청을 기록할 수도 있습니다.

There are several middleware included in the Laravel framework, including middleware for maintenance, authentication, CSRF protection, and more. 라라벨 프레임워크에는 보수(maintenance), 인증(authentication), CSRF 보안 등을 위한 미들웨어들이 포함되어 있습니다. All of these middleware are located in the `app/Http/Middleware` directory. 그 미들웨어들은 모두  `app/Http/Middleware` 디렉토리 안에 위치하고 있습니다.

<a name="defining-middleware"></a>
## Defining Middleware 미들웨어 정의하기

To create a new middleware, use the `make:middleware` Artisan command: 새로운 미들웨어를 생성하기 위하여 `make:middleware` Artisan 명령을 사용할 수 있습니다:

	php artisan make:middleware OldMiddleware

This command will place a new `OldMiddleware` class within your `app/Http/Middleware` directory. 이 명령은 `OldMiddleware` 클래스를 생성하여 `app/Http/Middleware` 디렉토리에 위치시킬 것입니다.  In this middleware, we will only allow access to the route if the supplied `age` is greater than 200. 이 미들웨어 에서 우리는 입력받은 `age`가 200보다 클 때에만 요청된 주소에 접근할 수 있도록 허용하려고 합니다. Otherwise, we will redirect the users back to the "home" URI.  그렇지 않으면 우리는 사용자를 "home"으로 리다이렉트할 것입니다.

	<?php namespace App\Http\Middleware;

	use Closure;

	class OldMiddleware {

		/**
		 * Run the request filter.
		 *
		 * @param  \Illuminate\Http\Request  $request
		 * @param  \Closure  $next
		 * @return mixed
		 */
		public function handle($request, Closure $next)
		{
			if ($request->input('age') < 200)
			{
				return redirect('home');
			}

			return $next($request);
		}

	}

As you can see, if the given `age` is less than `200`, the middleware will return an HTTP redirect to the client; 위 코드에서 볼 수 있듯이, 주어진 `age`가 200보다 작으면 미들웨어는 HTTP 리다이렉트를 클라이언트에게 반환할 것입니다; otherwise, the request will be passed further into the application. 그렇지 않으면 요청은 애플리케이션 안으로 더 깊이 전달될 것입니다. To pass the request deeper into the application (allowing the middleware to "pass"), simply call the `$next` callback with the `$request`.  (미들웨어가 "pass"를  허용하는) 요청을 애플리케이션 안으로 더 깊이 전달하기 원한다면, 간단하게 `$next` 콜백함수를 `$request`인자를 넣어 호출하면 됩니다.

It's best to envision middleware as a series of "layers" HTTP requests must pass through before they hit your application. 미들웨어를 HTTP request들이 애플리케이션에 도달하기 전에 반드시 통과해야 하는 일련의 "레이어"라고 상상하는 것이 가장 좋습니다. Each layer can examine the request and even reject it entirely. 각각의 레이어는 요청을 검사할 수 있고 완벽하게 요청을 거절할 수도 있습니다.

### *Before* / *After* Middleware  *Before* / *After* 미들웨어 

Whether a middleware runs before or after a request depends on the middleware itself. 요청을 애플리케이션이 처리하기 전에 미들웨어가 실행될지 처리한 후에 미들웨어가 실행될지는 미들웨어 자신이 결정할 수 있습니다. This middleware would perform some task **before** the request is handled by the application: 아래의 경우, 미들웨어는 요청이 애플리케이션에 의해 처리되기 **전**에 실행될 것입니다.

	<?php namespace App\Http\Middleware;

	use Closure;

	class BeforeMiddleware implements Middleware {

		public function handle($request, Closure $next)
		{
			// Perform action

			return $next($request);
		}
	}

However, this middleware would perform its task **after** the request is handled by the application: 반대로, 아래의 경우에서 미들웨어는 요청이 애플리케이션에 의해 처리된 **후**에 실행될 것입니다.

	<?php namespace App\Http\Middleware;

	use Closure;

	class AfterMiddleware implements Middleware {

		public function handle($request, Closure $next)
		{
			$response = $next($request);

			// Perform action

			return $response;
		}
	}

<a name="registering-middleware"></a>
## Registering Middleware 미들웨어 등록하기

### Global Middleware 전역 미들웨어

If you want a middleware to be run during every HTTP request to your application, simply list the middleware class in the `$middleware` property of your `app/Http/Kernel.php` class. 만약 애플리케이션의 모든 HTTP 요청에 대하여 미들웨어가 작동되기를 원한다면,  `app/Http/Kernel.php` 클래스의 `$middleware` 프로퍼티에 미들웨어를 등록하시면 됩니다.

### Assigning Middleware To Routes 라우트에 미들웨어 할당하기

If you would like to assign middleware to specific routes, you should first assign the middleware a short-hand key in your `app/Http/Kernel.php` file. 미들웨어를 특정 라우트에만 할당하고 싶을 때에는 우선 `app/Http/Kernel.php` 파일에 그 미들웨어의 키(short-hand key)를 지정해야 합니다. By default, the `$routeMiddleware` property of this class contains entries for the middleware included with Laravel.  기본적으로, 이 Kernel 클래스의  `$routeMiddleware`  프로퍼티는 라라벨에 포함된 미들웨어들의 목록을 가지고 있습니다. To add your own, simply append it to this list and assign it a key of your choosing. 추가하려는 미들웨어에 원하는 키를 지정하고 이 목록에 붙여넣으십시오.

Once the middleware has been defined in the HTTP kernel, you may use the `middleware` key in the route options array: 미들웨어를 HTTP 커널에 등록했다면, 라우트의 옵션 배열에서 `middleware` 키를 사용할 수 있습니다.

	Route::get('admin/profile', ['middleware' => 'auth', function()
	{
		//
	}]);

<a name="terminable-middleware"></a>
## Terminable Middleware 종료가능한 미들웨어

Sometimes a middleware may need to do some work after the HTTP response has already been sent to the browser. 가끔 미들웨어는 HTTP 응답을 브라우저로 전송하고 난 후에 어떤 작업을 수행할 필요가 있을지도 모릅니다. For example, the "session" middleware included with Laravel writes the session data to storage _after_ the response has been sent to the browser.  예를 들어, 라라벨에 내장된 "session" 미들웨어는 응답이 브라우저로 보내진 _후_에 세션데이터를 저장소에 저장합니다. To accomplish this, you may define the middleware as "terminable". 이런 경우,  미들웨어를 "terminable"로 정의하면 됩니다.

	use Closure;
	use Illuminate\Contracts\Routing\TerminableMiddleware;

	class StartSession implements TerminableMiddleware {

		public function handle($request, Closure $next)
		{
			return $next($request);
		}

		public function terminate($request, $response)
		{
			// Store the session data...
		}

	}

As you can see, in addition to defining a `handle` method, `TerminableMiddleware` define a `terminate` method. 위와 같이 `handle` 메소드를 정의하고 나서 `TerminableMiddleware`는 `terminate` 메소드를 추가로 정의합니다.  This method receives both the request and the response. 이 메소드는 요청 변수와 응답 변수를 모두 인자로 받습니다. Once you have defined a terminable middleware, you should add it to the list of global middlewares in your HTTP kernel. 종료 가능한 미들웨어를 정의했다면, 그것을 HTTP 커널의 전역 미들웨어 목록에 추가해 주어야 합니다.
