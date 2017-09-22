# URL Generation
# URL 생성

- [Introduction](#introduction)
- [소개하기](#introduction)
- [The Basics](#the-basics)
- [기본적인 내용](#the-basics)
    - [Generating Basic URLs](#generating-basic-urls)
    - [기본 URL 생성하기](#generating-basic-urls)
    - [Accessing The Current URL](#accessing-the-current-url)
    - [현재 URL 엑세스하기](#accessing-the-current-url)
- [URLs For Named Routes](#urls-for-named-routes)
- [이름이 지정된 라우트 URL](#urls-for-named-routes)
- [URLs For Controller Actions](#urls-for-controller-actions)
- [컨트롤러 액션 URL](#urls-for-controller-actions)
- [Default Values](#default-values)
- [기본값](#default-values)

<a name="introduction"></a>
## Introduction
## 소개하기

Laravel provides several helpers to assist you in generating URLs for your application. Of course, these are mainly helpful when building links in your templates and API responses, or when generating redirect responses to another part of your application.

라라벨은 어플리케이션에서 URL을 생성하는 것을 도와주는 몇가지 헬퍼 함수를 제공합니다. 당연하게도, 이는 템플릿 안에서 API 응답을 위한 링크를 생성하거나, 어플리케이션의 다른 곳으로 이동하는 리다이렉트 응답-response를 생성할 때 유용하게 사용됩니다.

<a name="the-basics"></a>
## The Basics
## 기본적인 내용

<a name="generating-basic-urls"></a>
### Generating Basic URLs
### 기본 URL 생성하기

The `url` helper may be used to arbitrary URLs for your application. The generated URL will automatically use the scheme (HTTP or HTTPS) and host from the current request:

`url` 헬퍼는 어플리케이션에서 사용하는 어떠한 URL에서도 사용가능합니다. 생성된 URL은 자동으로 현재 요청-request 에서 (HTTP 또는 HTTPS) 스키마와 호스트를 사용합니다:

    $post = App\Post::find(1);

    echo url("/posts/{$post->id}");

    // http://example.com/posts/1

<a name="accessing-the-current-url"></a>
### Accessing The Current URL
### 현재 URL 엑세스하기

If no path is provided to the `url` helper, a `Illuminate\Routing\UrlGenerator` instance is returned, allowing you to access information about the current URL:

`url` 헬퍼에 아무런 경로를 지정하지 않는다면, `Illuminate\Routing\UrlGenerator` 인스턴스가 반환되며, 현재 URL에 대한 정보에 엑세스 할 수 있습니다:

    // Get the current URL without the query string...
    echo url()->current();

    // Get the current URL including the query string...
    echo url()->full();

    // Get the full URL for the previous request...
    echo url()->previous();

Each of these methods may also be accessed via the `URL` [facade](/docs/{{version}}/facades):

각각의 메소드는 `URL` [파사드](/docs/{{version}}/facades)를 통해서도 엑세스 할 수 있습니다:

    use Illuminate\Support\Facades\URL;

    echo URL::current();

<a name="urls-for-named-routes"></a>
## URLs For Named Routes
## 이름이 지정된 라우트 URL

The `route` helper may be used to generate URLs to named routes. Named routes allow you to generate URLs without being coupled to the actual URL defined on the route. Therefore, if the route's URL changes, no changes need to be made to your `route` function calls. For example, imagine your application contains a route defined like the following:

`route` 헬퍼함수는 이름이 지정된 라우트에 대한 URL을 생성하는데 사용할 수 있습니다. 라우트에 이름을 지정하여 사용하면, 라우트에 정의된 실제 URL에 구애받지 않고서도 URL을 생성할 수 있습니다. 따라서 라우트의 URL이 변경되었다고 해서 `route` 함수를 호출한 곳을 모두 수정할 필요가 없습니다. 예를 들자면 어플리케이션에서 다음과 같이 정의된 라우트를 가지고 있다고 가정해보십시오:

    Route::get('/post/{post}', function () {
        //
    })->name('post.show');

To generate a URL to this route, you may use the `route` helper like so:

이 라우에 대한 URL을 생성하려면 `route` 헬퍼 함수를 다음과 같이 사용하면 됩니다:

    echo route('post.show', ['post' => 1]);

    // http://example.com/post/1

You will often be generating URLs using the primary key of [Eloquent models](/docs/{{version}}/eloquent). For this reason, you may pass Eloquent models as parameter values. The `route` helper will automatically extract the model's primary key:

또한 자주 [Eloquent 모델](/docs/{{version}}/eloquent)의 기본 키를 사용하여 URL을 생합니다. 이러한 이유로, 파라미터 값으로 Eloquent 모델을 전달할 수 있습니다. `route` 헬퍼 함수는 자동으로 모델의 기본 키를 추출하여 사용합니다:

    echo route('post.show', ['post' => $post]);

<a name="urls-for-controller-actions"></a>
## URLs For Controller Actions
## 컨트롤러 액션 URL

The `action` function generates a URL for the given controller action. You do not need to pass the full namespace of the controller. Instead, pass the controller class name relative to the `App\Http\Controllers` namespace:

`action` 함수는 주어진 컨트롤러 액션에 대한 URL을 생성합니다. 컨트롤러의 전체 네임스페이스를 전달할 필요는 없습니다. 대신에 `App\Http\Controllers` 네임스페이스로 부터 시작하는 컨트롤러 클래스 이름을 전달하십시오:

    $url = action('HomeController@index');

If the controller method accepts route parameters, you may pass them as the second argument to the function:

컨트롤러 메소드가 라우트 파라미터 인자를 필요로 한다면, 함수의 두번째 인자로 이를 전달할 수 있습니다:

    $url = action('UserController@profile', ['id' => 1]);

<a name="default-values"></a>
## Default Values
## 기본값

For some applications, you may wish to specify request-wide default values for certain URL parameters. For example, imagine many of your routes define a `{locale}` parameter:

몇몇 어플리케이션에서는 특정 URL 파라미터에 대해 요청-reqeust 전의 기본값을 지정할 수 있습니다. 예를 들어, 다수의 라우트에서 `{locale}` 파라미터를 정의한다고 가정해보겠습니다:

    Route::get('/{locale}/posts', function () {
        //
    })->name('post.index');

It is cumbersome to always pass the `locale` every time you call the `route` helper. So, you may use the `URL::defaults` method to define a default value for this parameter that will always be applied during the current request. You may wish to call this method from a [route middleware](/docs/{{version}}/middleware#assigning-middleware-to-routes) so that you have access to the current request:

`route` 헬퍼를 호출할 때마다 `locale` 을 전달해야 하는 것은 번거로운 일일 수 있습니다. 따라서 `URL::defaults` 메소드를 사용하여 현재 요청-request 중에서 항상 적용될 파라미터의 기본값을 정의 할 수 있습니다. 라우트 미들웨어에서 이 메소드를 호출하여 현재의 요청-request에 엑세스 할 수 있습니다:

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Support\Facades\URL;

    class SetDefaultLocaleForUrls
    {
        public function handle($request, Closure $next)
        {
            URL::defaults(['locale' => $request->user()->locale]);

            return $next($request);
        }
    }

Once the default value for the `locale` parameter has been set, you are no longer required to pass its value when generating URLs via the `route` helper.

`locale` 파라미터의 기본값이 설정되면 `rotue` 헬퍼를 통해서 URL을 생성 할 때 더 이상 매번 값을 전달하지 않아도 됩니다.
