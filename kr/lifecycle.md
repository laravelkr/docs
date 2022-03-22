# Request Lifecycle
# Request-요청 라이프사이클

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Lifecycle Overview](#lifecycle-overview)
- [라이프사이클 개요](#lifecycle-overview)
    - [First Steps](#first-steps)
    - [첫번째 단계](#first-steps)
    - [HTTP / Console Kernels](#http-console-kernels)
    - [HTTP / Console 커널](#http-console-kernels)
    - [Service Providers](#service-providers)
    - [서비스 프로바이더](#service-providers)
    - [Routing](#routing)
    - [라우팅-Routing](#routing)
    - [Finishing Up](#finishing-up)
    - [마무리](#finishing-up)
- [Focus On Service Providers](#focus-on-service-providers)
- [서비스 프로바이더 추가설명](#focus-on-service-providers)

<a name="introduction"></a>
## Introduction
## 시작하기

When using any tool in the "real world", you feel more confident if you understand how that tool works. Application development is no different. When you understand how your development tools function, you feel more comfortable and confident using them.

"현실 세계"에서 어떤 도구를 사용하는 경우, 그 도구가 어떻게 작동하는지 알고 있는 것은 여러분께 자신감을 줍니다. 애플리케이션 개발도 다르지 않습니다. 개발툴이 어떻게 동작하는지 이해한다면 이를 사용하는데 있어서 더 편하고 잘 사용할 수 있습니다. 

The goal of this document is to give you a good, high-level overview of how the Laravel framework works. By getting to know the overall framework better, everything feels less "magical" and you will be more confident building your applications. If you don't understand all of the terms right away, don't lose heart! Just try to get a basic grasp of what is going on, and your knowledge will grow as you explore other sections of the documentation.

이 문서의 목적은 어떻게 라라벨이 동작하는지 높은 수준의 개념을 잘 설명하는 것입니다. 전체 프레임워크를 더 잘 알게 됨으로써 모든 것을 덜 "혼란스러워" 할 수 있으며, 애플리케이션을 구성하는 데 좀 더 자신감을 가질 수 있습니다. 지금 당장 모든 것들을 이해하지 못하더라도 괜찮습니다. 그냥 무엇을 하고 있는지에 대한 기본적인 이해를 얻기 위해 노력하십시오, 여러분이 다른 문서의 여러 부분들을 살펴 볼 수록 지식이 더 쌓일 것입니다.

<a name="lifecycle-overview"></a>
## Lifecycle Overview
## 라이프사이클 개요

<a name="first-steps"></a>
### First Steps
### 첫번째 단계

The entry point for all requests to a Laravel application is the `public/index.php` file. All requests are directed to this file by your web server (Apache / Nginx) configuration. The `index.php` file doesn't contain much code. Rather, it is a starting point for loading the rest of the framework.

라라벨 애플리케이션의 모든 요청에 대한 시작점은 `public/index.php` 파일입니다. 웹서버 (Apache / Nginx)의 설정에 따라 모든 요청은 이 파일에 전달됩니다. `index.php` 파일은 그다지 많은 코드를 가지고 있지 않습니다. 대신 프레임워크의 나머지 부분들을 로딩하기 위한 시작점이 됩니다.

The `index.php` file loads the Composer generated autoloader definition, and then retrieves an instance of the Laravel application from `bootstrap/app.php`. The first action taken by Laravel itself is to create an instance of the application / [service container](/docs/{{version}}/container).

`index.php` 파일은 컴포저가 생성 한 오토로더 정의를 로딩합니다. 그리고, `bootstrap/app.php` 에서 라라벨 애플리케이션의 인스턴스를 가져옵니다. 라라벨 자신의 첫 번째 동작은 [서비스 컨테이너](/docs/{{version}}/container) 인스턴스를 생성하는 것입니다.

<a name="http-console-kernels"></a>
### HTTP / Console Kernels
### HTTP / Console 커널

Next, the incoming request is sent to either the HTTP kernel or the console kernel, depending on the type of request that is entering the application. These two kernels serve as the central location that all requests flow through. For now, let's just focus on the HTTP kernel, which is located in `app/Http/Kernel.php`.

다음으로 애플리케이션이 시작된 유형에 따라 전송된 요청을 HTTP 커널이나 콘솔 커널 둘 중 하나로 보냅니다. 이 두가지의 커널은 모든 요청의 흐름 중심에서 작동하게 됩니다. 여기에서는 `app/Http/Kernel.php` 에있는 HTTP 커널에 초점을 맞춰 봅시다.

The HTTP kernel extends the `Illuminate\Foundation\Http\Kernel` class, which defines an array of `bootstrappers` that will be run before the request is executed. These bootstrappers configure error handling, configure logging, [detect the application environment](/docs/{{version}}/configuration#environment-configuration), and perform other tasks that need to be done before the request is actually handled. Typically, these classes handle internal Laravel configuration that you do not need to worry about.

HTTP 커널은 요청이 실행되기 전에 실행될 `bootstrappers` 배열을 정의하는 `Illuminate\Foundation\Http\Kernel` 클래스를 확장합니다. 이러한 부트스트래퍼는 오류 처리를 구성하고, 로깅을 구성하고, [애플리케이션 환경을 감지](/docs/{{version}}/configuration#environment-configuration)하고, 요청이 실제로 처리되기 전에 수행해야 하는 기타 작업을 수행합니다. 일반적으로 이러한 클래스는 신경 쓸 필요가 없는 라라벨 내부 설정을 처리합니다.

The HTTP kernel also defines a list of HTTP [middleware](/docs/{{version}}/middleware) that all requests must pass through before being handled by the application. These middleware handle reading and writing the [HTTP session](/docs/{{version}}/session), determining if the application is in maintenance mode, [verifying the CSRF token](/docs/{{version}}/csrf), and more. We'll talk more about these soon.

HTTP 커널은 또한 모든 요청이 애플리케이션에서 처리되기 전에 통과해야 하는 HTTP [미들웨어](/docs/{{version}}/middleware) 목록을 관리합니다. 이러한 미들웨어는 [HTTP 세션](/docs/{{version}}/session) 읽기 및 쓰기, 애플리케이션이 유지 관리 모드에 있는지 확인, [CSRF 토큰 확인](/docs/{{version}}/csrf) 등을 처리합니다. 이에 대해 곧 더 자세히 설명하겠습니다.

The method signature for the HTTP kernel's `handle` method is quite simple: it receives a `Request` and returns a `Response`. Think of the kernel as being a big black box that represents your entire application. Feed it HTTP requests and it will return HTTP responses.

HTTP 커널의 `handle` 메서드에 대한 메서드 사용법은 매우 간단합니다. `Request`를 수신하고 `Response`를 반환합니다. 커널을 전체 애플리케이션을 나타내는 큰 블랙박스로 생각하십시오. HTTP 요청을 제공하면 HTTP 응답을 반환합니다.

<a name="service-providers"></a>
### Service Providers
### 서비스 프로바이더-Service Providers

One of the most important kernel bootstrapping actions is loading the [service providers](/docs/{{version}}/providers) for your application. All of the service providers for the application are configured in the `config/app.php` configuration file's `providers` array.

가장 중요한 커널 부트스트랩 작업 중 하나는 애플리케이션에 대한 [서비스 프로바이더](/docs/{{version}}/providers)를 로드하는 것입니다. 애플리케이션의 모든 서비스 프로바이더는 `config/app.php` 설정 파일의 `providers` 배열에서 관리됩니다.

Laravel will iterate through this list of providers and instantiate each of them. After instantiating the providers, the `register` method will be called on all of the providers. Then, once all of the providers have been registered, the `boot` method will be called on each provider. This is so service providers may depend on every container binding being registered and available by the time their `boot` method is executed.

라라벨은 이 서비스 프로바이더 목록을 각각 인스턴스화합니다. 서비스 프로바이더를 인스턴스화한 후 모든 프로바이더에서 `register` 메서드가 호출됩니다. 그런 다음 모든 프로바이더가 등록되면 각 프로바이더에서 `boot` 메서드가 호출됩니다. 이는 서비스 프로바이더가 `boot` 메소드가 실행될 때, 등록되고 사용 가능한 모든 컨테이너 바인딩에 의존할 수 있기 때문입니다.

Service providers are responsible for bootstrapping all of the framework's various components, such as the database, queue, validation, and routing components. Essentially every major feature offered by Laravel is bootstrapped and configured by a service provider. Since they bootstrap and configure so many features offered by the framework, service providers are the most important aspect of the entire Laravel bootstrap process.

서비스 프로바이더는 데이터베이스, 대기열, 유효성 검사 및 라우팅 구성 요소와 같은 프레임워크의 다양한 구성 요소를 모두 부트스트랩해야 합니다. 본질적으로 라라벨이 제공하는 모든 주요 기능은 서비스 프로바이더에서 부트스트랩 및 구성합니다. 프레임워크에서 제공하는 많은 기능을 부트스트랩하고 구성하기 때문에 서비스 프로바이더는 전체 라라벨 부트스트랩 프로세스에서 가장 중요한 곳 입니다.

<a name="routing"></a>
### Routing
### 라우팅-Routing

One of the most important service providers in your application is the `App\Providers\RouteServiceProvider`. This service provider loads the route files contained within your application's `routes` directory. Go ahead, crack open the `RouteServiceProvider` code and take a look at how it works!

애플리케이션에서 가장 중요한 서비스 프로바이더 중 하나는 `App\Providers\RouteServiceProvider`입니다. 이 서비스 프로바이더는 애플리케이션의 `routes` 디렉토리에 포함된 경로-route 파일을 로드합니다. 계속해서 `RouteServiceProvider` 코드를 열고 작동 방식을 살펴보세요!

Once the application has been bootstrapped and all service providers have been registered, the `Request` will be handed off to the router for dispatching. The router will dispatch the request to a route or controller, as well as run any route specific middleware.

애플리케이션이 부트스트랩되고 모든 서비스 프로바이더가 등록되면 `Request`가 실행을 위해 라우터로 전달됩니다. 라우터는 request를 라우트나 컨트롤러로 디스패치하고 라우트별 미들웨어를 실행합니다.

Middleware provide a convenient mechanism for filtering or examining HTTP requests entering your application. For example, Laravel includes a middleware that verifies if the user of your application is authenticated. If the user is not authenticated, the middleware will redirect the user to the login screen. However, if the user is authenticated, the middleware will allow the request to proceed further into the application. Some middleware are assigned to all routes within the application, like those defined in the `$middleware` property of your HTTP kernel, while some are only assigned to specific routes or route groups. You can learn more about middleware by reading the complete [middleware documentation](/docs/{{version}}/middleware).

미들웨어는 애플리케이션에 들어오는 HTTP 요청을 필터링하거나 검사하기 위한 편리한 메커니즘을 제공합니다. 예를 들어, 라라벨에는 애플리케이션 사용자가 인증되었는지 확인하는 미들웨어가 포함되어 있습니다. 사용자가 인증되지 않은 경우 미들웨어는 사용자를 로그인 화면으로 리디렉션합니다. 그러나 사용자가 인증되면 미들웨어는 요청이 애플리케이션으로 더 진행되도록 허용해줍니다. 일부 미들웨어는 HTTP 커널의 `middleware` 속성에 정의된 것과 같이 애플리케이션 내의 모든 경로에 할당되는 반면 일부는 특정 경로 또는 경로 그룹에만 할당됩니다. 전체 [미들웨어 문서](/docs/{{version}}/middleware)를 읽으면 미들웨어에 대해 자세히 알아볼 수 있습니다.

If the request passes through all of the matched route's assigned middleware, the route or controller method will be executed and the response returned by the route or controller method will be sent back through the route's chain of middleware.

요청이 일치하는 경로에 할당된 모든 미들웨어를 통과하면 경로 또는 컨트롤러 메서드가 실행되고 경로 또는 컨트롤러 메서드에서 반환된 응답은 경로의 미들웨어 체인을 통해 다시 전송됩니다.

<a name="finishing-up"></a>
### Finishing Up
### 마무리

Once the route or controller method returns a response, the response will travel back outward through the route's middleware, giving the application a chance to modify or examine the outgoing response.

경로 또는 컨트롤러 메서드가 응답을 반환하면, 응답은 경로의 미들웨어를 통해 외부로 다시 이동하여 애플리케이션이 나가는 응답을 수정하거나 검사할 수 있는 기회를 제공합니다.

Finally, once the response travels back through the middleware, the HTTP kernel's `handle` method returns the response object and the `index.php` file calls the `send` method on the returned response. The `send` method sends the response content to the user's web browser. We've finished our journey through the entire Laravel request lifecycle!

마지막으로 응답이 미들웨어를 통해 다시 이동하면 HTTP 커널의 `handle` 메서드가 응답 개체를 반환하고 `index.php` 파일이 반환된 응답에 대해 `send` 메서드를 호출합니다. `send` 메소드는 응답 내용을 사용자의 웹 브라우저로 보냅니다. 전체 라라벨 요청 수명 주기를 통한 여정을 마쳤습니다!

<a name="focus-on-service-providers"></a>
## Focus On Service Providers
## 서비스 프로바이더 추가설명

Service providers are truly the key to bootstrapping a Laravel application. The application instance is created, the service providers are registered, and the request is handed to the bootstrapped application. It's really that simple!

서비스 프로바이더는 라라벨 애플리케이션의 부팅(부트스트래핑) 단계의 주요한 핵심입니다. 애플리케이션의 인스턴스가 생성되고, 서비스 프로바이더가 등록된후 부트스트래핑 과정을 마친 프로그램이 요청을 처리합니다. 매우 간단합니다!

Having a firm grasp of how a Laravel application is built and bootstrapped via service providers is very valuable. Your application's default service providers are stored in the `app/Providers` directory.

라라벨 애플리케이션이 어떻게 구성되어 있는지, 서비스 프로바이더를 통해 부트스트랩되는 과정을 구체적으로 이해하는 것은 매우 중요합니다. 여러분의 애플리케이션을 위한 기본 서비스 프로바이더는 `app/Providers` 디렉토리에 있습니다.

By default, the `AppServiceProvider` is fairly empty. This provider is a great place to add your application's own bootstrapping and service container bindings. For large applications, you may wish to create several service providers, each with more granular bootstrapping for specific services used by your application.

기본적으로 `AppServiceProvider` 는 거의 빈채로 만들어져 있습니다. 이 프로바이더는 여러분의 고유한 부트스트래핑과 서비스 컨테이너 바인딩 코드를 추가하기 위한 곳입니다. 대규모 애플리케이션의 경우, 애플리케이션에서 사용하는 특정 서비스에 대해 더 세분화된 부트스트래핑을 관리하는 여러개의 서비스 공급자를 만들 수 있습니다.
