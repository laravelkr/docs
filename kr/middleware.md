# HTTP Middleware HTTP 미들웨어

- [Introduction](#introduction)
- [Defining Middleware](#defining-middleware)
- [Registering Middleware](#registering-middleware)
- [Terminable Middleware](#terminable-middleware)

<a name="introduction"></a>
## Introduction 개요

HTTP middleware provide a convenient mechanism for filtering HTTP requests entering your application. HTTP 미들웨어는 어플리케이션으로 들어온 HTTP 요청을 간편하게 필터링할 수 있는 방법을 제공합니다. For example, Laravel includes a middleware that verifies the user of your application is authenticated. 예를 들어, 라라벨은 어플리케이션의 사용자가 인증되었는지 검사하는 미들웨어를 내장하고 있습니다. If the user is not authenticated, the middleware will redirect the user to the login screen. 만약 그 사용자가 인증되지 않은 사용자라면 그 미들웨어는 그 사용자를 로그인 화면으로 리다이렉트 시킬 것입니다. However, if the user is authenticated, the middleware will allow the request to proceed further into the application. 반대로, 만약 그 사용자가 인증된 사용자라면, 그 미들웨어는 어플리케이션에서 HTTP 요청이 계속해서 더 처리되도록 허용할 것입니다.

Of course, middleware can be written to perform a variety of tasks besides authentication. 물론, 미들웨어는 인증 이외에 다른 다양한 작업을 수행하도록 작성될 수도 있습니다. A CORS middleware might be responsible for adding the proper headers to all responses leaving your application. CORS 미들웨어는 어플리케이션에서 내보내는 모든 응답에 적절한 헤더들을 추가하는 역할을 담당할 수도 있습니다. A logging middleware might log all incoming requests to your application. 로깅 미들웨어는 어플리케이션으로 들어오는 모든 요청을 기록할 수도 있습니다.

There are several middleware included in the Laravel framework, including middleware for maintenance, authentication, CSRF protection, and more. 라라벨 프레임워크에는 보수(maintenance), 인증(authentication), CSRF 보안 등의 미들웨어가 포함돼 있습니다. All of these middleware are located in the `app/Http/Middleware` directory. 그 미들웨어들은 모두  `app/Http/Middleware` 디렉토리 안에 위치하고 있습니다.

<a name="defining-middleware"></a>
## Defining Middleware 미들웨어 정의하기

To create a new middleware, use the `make:middleware` Artisan command: 새로운 미들웨어를 생성하기 위하여 `make:middleware` Artisan 명령을 사용하십시오:

	php artisan make:middleware OldMiddleware

This command will place a new `OldMiddleware` class within your `app/Http/Middleware` directory. In this middleware, we will only allow access to the route if the supplied `age` is greater than 200. Otherwise, we will redirect the users back to the "home" URI.

	<?php namespace App\Http\Middleware;

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

As you can see, if the given `age` is less than `200`, the middleware will return an HTTP redirect to the client; otherwise, the request will be passed further into the application. To pass the request deeper into the application (allowing the middleware to "pass"), simply call the `$next` callback with the `$request`.

It's best to envision middleware as a series of "layers" HTTP requests must pass through before they hit your application. Each layer can examine the request and even reject it entirely.

### *Before* / *After* Middleware

Whether a middleware runs before or after a request depends on the middleware itself. This middleware would perform some task **before** the request is handled by the application:

	<?php namespace App\Http\Middleware;

	class BeforeMiddleware implements Middleware {

		public function handle($request, Closure $next)
		{
			// Perform action

			return $next($request);
		}
	}

However, this middleware would perform its task **after** the request is handled by the application:

	<?php namespace App\Http\Middleware;

	class AfterMiddleware implements Middleware {

		public function handle($request, Closure $next)
		{
			$response = $next($request);

			// Perform action

			return $response;
		}
	}

<a name="registering-middleware"></a>
## Registering Middleware

### Global Middleware

If you want a middleware to be run during every HTTP request to your application, simply list the middleware class in the `$middleware` property of your `app/Http/Kernel.php` class.

### Assigning Middleware To Routes

If you would like to assign middleware to specific routes, you should first assign the middleware a short-hand key in your `app/Http/Kernel.php` file. By default, the `$routeMiddleware` property of this class contains entries for the middleware included with Laravel. To add your own, simply append it to this list and assign it a key of your choosing.

Once the middleware has been defined in the HTTP kernel, you may use the `middleware` key in the route options array:

	Route::get('admin/profile', ['middleware' => 'auth', function()
	{
		//
	}]);

<a name="terminable-middleware"></a>
## Terminable Middleware

Sometimes a middleware may need to do some work after the HTTP response has already been sent to the browser. For example, the "session" middleware included with Laravel writes the session data to storage _after_ the response has been sent to the browser. To accomplish this, you may define the middleware as "terminable".

	use Illuminate\Contracts\Routing\TerminableMiddleware;

	class StartSession implements TerminableMiddleware {

		public function handle($request, $next)
		{
			return $next($request);
		}

		public function terminate($request, $response)
		{
			// Store the session data...
		}

	}

As you can see, in addition to defining a `handle` method, `TerminableMiddleware` define a `terminate` method. This method receives both the request and the response. Once you have defined a terminable middleware, you should add it to the list of global middlewares in your HTTP kernel.
