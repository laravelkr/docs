# Request Lifecycle 
# 요청에 대한 라이프사이클

- [Introduction 소개](#introduction)
- [Lifecycle Overview 라이프사이클 개요](#lifecycle-overview)
- [Focus On Service Providers 서비스 프로바이더](#focus-on-service-providers)

<a name="introduction"></a>
## Introduction
## 소개

When using any tool in the "real world", you feel more confident if you understand how that tool works. "현실 세계"에서 어떤 도구를 사용하는 경우, 그 도구가 어떻게 작동하는지 알고 있는 것은 여러분께 자신감을 줄 것입니다. Application development is no different. 애플리케이션 개발에서도 다르지 않습니다. When you understand how your development tools function, you feel more comfortable and confident using them. 개발툴들이 어떻게 작동하는지 이해한다면 그것들을 사용하는데 있어서 더 편하고 잘 사용할 수 있을 것입니다. 

The goal of this document is to give you a good, high-level overview of how the Laravel framework "works". 이 문서의 목적은 어떻게 라라벨이 “동작” 하는지, 높은 수준의 개념을 잘 설명하는 것입니다. By getting to know the overall framework better, everything feels less "magical" and you will be more confident building your applications. 전체 프레임워크를 더 잘 알게 됨으로써, 모든것을 덜 “혼란스러워“할 것이며 여러분은 애플리케이션을 구축하는데 좀 더 자신감 갖게 될 것입니다.

If you don't understand all of the terms right away, don't lose heart! 지금 당장 모든 것들을 이해하지 못하더라도 괜찮습니다. Just try to get a basic grasp of what is going on, and your knowledge will grow as you explore other sections of the documentation. 그냥 무엇을 하고 있는지에 대한 기본적인 이해를 얻기 위해 노력하십시오, 여러분이  다른 문서의 여러 부분들을 살펴 볼 수록 지식이 더 쌓일 것입니다. 

<a name="lifecycle-overview"></a>
## Lifecycle Overview
## 라이프사이클 개요

#### First Things
#### 시작

The entry point for all requests to a Laravel application is the `public/index.php` file. f라라벨 애플리케이션의 모든 요청에 대한 시작점은 `public/index.php` 파일입니다. All requests are directed to this file by your web server (Apache / Nginx) configuration. 웹서버 (Apache / Nginx)의 설정에 따라 모든 요청은 이 파일에 전달됩니다. The `index.php` file doesn't contain much code. `index.php` 파일은 그다지 많은 코드를 가지고 있지 않습니다. Rather, it is simply a starting point for loading the rest of the framework. 대신 프레임 워크의 나머지를 로딩하기 위한 시작점이 됩니다. 

The `index.php` file loads the Composer generated autoloader definition, and then retrieves an instance of the Laravel application from `bootstrap/app.php` script. `index.php` 파일은 컴포저가 생성 한 오토로더 정의를 로딩합니다. 그리고, `bootstrap/app.php` 스크립트에서 라라벨 애플리케이션의 인스턴스를 가져옵니다. The first action taken by Laravel itself is to create an instance of the application / [service container](/docs/{{version}}/container). 라라벨 자신의 첫 번째 동작은 [서비스 컨테이너](/docs/{{version}}/container) 인스턴스를 생성하는 것입니다.

#### HTTP / Console Kernels
#### HTTP / Console 커널

Next, the incoming request is sent to either the HTTP kernel or the console kernel, depending on the type of request that is entering the application. 다음으로 애플리케이션이 시작된 유형에 따라 전송된 요청을 HTTP 커널이나 콘솔 커널 둘 중 하나로 보냅니다. These two kernels serve as the central location that all requests flow through. 이 두가지의 커널은 모든 요청의 흐름 중심에서 작동하게 됩니다. For now, let's just focus on the HTTP kernel, which is located in `app/Http/Kernel.php`. 여기에서는 `app/Http/Kernel.php` 에있는 HTTP 커널에 초점을 맞춰 봅시다.

The HTTP kernel extends the `Illuminate\Foundation\Http\Kernel` class, which defines an array of `bootstrappers` that will be run before the request is executed. HTTP 커널은 `Illuminate\Foundation\Http\Kernel` 클래스를 상속하고 있으며, 요청을 실행하기 전에 처리되는 `bootstrappers` (시작 코드)의 배열을 정의하고 있습니다. These bootstrappers configure error handling, configure logging, detect the application environment, and perform other tasks that need to be done before the request is actually handled. 이 시작 코드들은 에러 처리, 로그 설정, 애플리케이션 동작 환경의 감지 등 실제로 요청이 처리되기 전에 수행해야 되는 작업들을 의미합니다.

The HTTP kernel also defines a list of HTTP [middleware](/docs/{{version}}/middleware) that all requests must pass through before being handled by the application. 또한 HTTP 커널은 애플리케이션에서 요청이 처리되기 전에 통과해야하는 HTTP [미들웨어](/docs/{{version}}/middleware)의 목록을 정의하고 있습니다. These middleware handle reading and writing the HTTP session, determine if the application is in maintenance mode, verifying the CSRF token, and more.  이 미들웨어들은 HTTP 세션 읽고/쓰고, 애플리케이션이 유지 관리 모드인지 확인하고, CSRF 토큰을 확인 하는 작업들을 처리합니다. 

The method signature for the HTTP kernel's `handle` method is quite simple: receive a `Request` and return a `Response`. HTTP 커널의 `handle` 메소드의 사용법은 매우 간단합니다. 단순하게는 `Request` 를 받고 `Response`를 반환합니다. Think of the Kernel as being a big black box that represents your entire application. 커널을 애플리케이션 전체를 나타내는 하나의 큰 블랙 박스라고 생각해봅시다. Feed it HTTP requests and it will return HTTP responses.  HTTP 요청이 입력되면 HTTP 응답이 반환됩니다.

#### Service Providers
#### 서비스 프로바이더

One of the most important Kernel bootstrapping actions is loading the service providers for your application. 커널 부팅(부트스트래핑) 과정의 가장 중요한 것 중의 하나는 애플리케이션의 서비스 프로바이더를 로딩하는 것입니다. All of the service providers for the application are configured in the `config/app.php` configuration file's `providers` array. 애플리케이션의 모든 서비스 프로바이더는 `config/app.php` 파일의 `providers` 배열에 설정되어 있습니다. First, the `register` method will be called on all providers, then, once all providers have been registered, the `boot` method will be called. 먼저, 모든 서비스 프로바이더의 `register` 메소드가 호출되고, 이후에 등록 된 모든 서비스 프로바이더의 `boot` 메소드가 호출되어 집니다. 

#### Dispatch Request
#### 요청 처리-디스패칭

Once the application has been bootstrapped and all service providers have been registered, the `Request` will be handed off to the router for dispatching. 애플리케이션이 부팅(부트스트래핑)되고 모든 서비스 프로바이더가 등록된 후, `Request`는 라우터 처리리를 위해서 전달될 것입니다. The router will dispatch the request to a route or controller, as well as run any route specific middleware. 라우터는 라우팅 또는 컨트롤러로 요청-request을 전달할뿐만 아니라, 임의의 특정 라우트에 지정된 미들웨어도 실행합니다.

<a name="focus-on-service-providers"></a>
## Focus On Service Providers
## 서비스 제공자

Service providers are truly the key to bootstrapping a Laravel application. 서비스 프로바이더는 라라벨 애플리케이션의 부팅(부트스트래핑) 단계의 주요한 핵심입니다. The application instance is created, the service providers are registered, and the request is handed to the bootstrapped application. 애플리케이션의 인스턴스가 생성되고, 서비스 프로바이더가 등록된후 부트스트래핑 과정을 마친 프로그램이 요청을 처리합니다. It's really that simple! 매우 간단합니다!

Having a firm grasp of how a Laravel application is built and bootstrapped via service providers is very valuable. 라라벨 애플리케이션이 어떻게 구성되어 있는지, 서비스 프로바이더를 통해 부트스트랩되는 과정을 구체적으로 이해하는 것은 매우 중요합니다. Of course, your application's default service providers are stored in the `app/Providers` directory. 물론 여러분의 애플리케이션을 위한 기본 서비스 프로바이더는 `app/Providers` 디렉토리에 있습니다.

By default, the `AppServiceProvider` is fairly empty. 기본적으로 `AppServiceProvider` 는 거의 비어 있습니다. This provider is a great place to add your application's own bootstrapping and service container bindings. 이 프로바이더는 여러분의 고유한 부트스트래핑과 서비스 컨테이너 바인딩 코드를 추가하기 위한 곳입니다. Of course, for large applications, you may wish to create several service providers, each with a more granular type of bootstrapping.  물론 보다 큰 애플리케이션의 경우, 보다 세부적인 유형으로 구분된 종류별로 서비스 프로바이더를 만들 수도 있습니다. 
