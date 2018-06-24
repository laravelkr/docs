# Service Providers
# 서비스 프로바이더

- [Introduction 소개](#introduction)
- [Basic Provider Example 기본적인 프로바이더 예제](#basic-provider-example)
- [Registering Providers 프로바이더 등록하기](#registering-providers)
- [Deferred Providers 지연 프로바이더](#deferred-providers)

<a name="introduction"></a>
## Introduction
## 소개

Service providers are the central place of all Laravel application bootstrapping. 서비스 프로바이더는 라라벨 애플리케이션의 부팅(부트스트래핑)의 가장 핵심이라고 할 수 있습니다. Your own application, as well as all of Laravel's core services are bootstrapped via service providers. 여러분의 애플리케이션과 마찬가지로 라라벨의 모든 코어 서비스는 서비스 프로바이더를 통해서 부트스트래핑 됩니다. 

But, what do we mean by "bootstrapped"? 그런데 “부트스트래핑 '이란 과련 무엇을 의미하는 것일까 요? In general, we mean **registering** things, including registering service container bindings, event listeners, filters, and even routes. 일반적으로는 서비스 컨테이너에 바인딩을 등록하는 것을 포함해서 이벤트 리스터, 필터 그리고 라우트등을 **등록** 하는 것을 의미합니다. Service providers are the central place to configure your application. 서비스 프로바이더는 애플리케이션 구성의 핵심입니다.

If you open the `config/app.php` file included with Laravel, you will see a `providers` array. 라라벨에 포함되어 있는 `config/app.php` 파일을 열어 본다면 `providers` 배열을 볼 수 있을 것입니다. These are all of the service provider classes that will be loaded for your application. 배열 안에 있는 모든 서비스 프로바이더 클래스가 애플리케이션에 로드됩니다. Of course, many of them are "deferred" providers, meaning they will not be loaded on every request, but only when the services they provide are actually needed. 물론, 대부분의 프로바이더는 “지연된” 프로바이더입니다. 이 말은 모든 요청에 대해서 반드시 로드되지 않고 실제로 필요할 때에 로드 된다는 것을 의미합니다. 

In this overview you will learn how to write your own service providers and register them with your Laravel application. 여기에서는 서비스 프로바이더를 작성하는 방법과 라라벨 애플리케이션에 등록하는 방법을 배워봅시다. 

<a name="basic-provider-example"></a>
## Basic Provider Example
## 기본적인 프로바이더 예제

All service providers extend the `Illuminate\Support\ServiceProvider` class. 모든 서비스 프로바이더는 `Illuminate\Support\ServiceProvider` 클래스를 상속받습니다. This abstract class requires that you define at least one method on your provider: `register`. 이 추상 클래스는 여러분의 프로바이더에 최소한 `register` 메소드가  정의되어야 한다는 것을 지정합니다. Within the `register` method, you should **only bind things into the [service container](/docs/{{version}}/container)**. `register` 메소드에서는 **[서비스 컨테이너](/docs/{{version}}/container)에 등록만을 하도록 합니다** You should never attempt to register any event listeners, routes, or any other piece of functionality within the `register` method. `register` 메소드안에서는 다른 이벤트 리스너나 라우트 또는 기타 기능의 일부등을 등록하지 않아야 합니다. 

The Artisan CLI can easily generate a new provider via the `make:provider` command: 아티즌 CLI에서는 `make:provider` 명령어를 통해서 손쉽게 새로운 프로바이더를 생성할 수 있습니다. 

	php artisan make:provider RiakServiceProvider

### The Register Method
###  Register 메소드 

Now, let's take a look at a basic service provider:
이제 기본적인 서비스 프로바이더를 살펴봅시다. 

	<?php namespace App\Providers;

	use Riak\Connection;
	use Illuminate\Support\ServiceProvider;

	class RiakServiceProvider extends ServiceProvider {

		/**
		 * Register bindings in the container.
		 *
		 * @return void
		 */
		public function register()
		{
			$this->app->singleton('Riak\Contracts\Connection', function($app)
			{
				return new Connection($app['config']['riak']);
			});
		}

	}

This service provider only defines a `register` method, and uses that method to define an implementation of `Riak\Contracts\Connection` in the service container. 이 서비스 프로바이더는 `register` 메소드만 정의되어 있습니다. 그리고 이 메소드를 통해서 서비스 컨테이너에 `Riak\Contracts\Connection` 구현 객체를 정의하고 있습니다. If you don't understand how the service container works, don't worry, [we'll cover that soon](/docs/{{version}}/container). 서비스 컨테이너가 어떻게 작동하는지 이해하지 못하더라도 걱정하지 마십시오. [곧바로 알 수 있습니다.](/docs/{{version}}/container)

This class is namespaced under `App\Providers` since that is the default location for service providers in Laravel. 이 클래스는 라라벨의 기본 설치 장소에 있으므로 `App\Providers` 네임스페이스를 가집니다. However, you are free to change this as you wish. 그렇지만 원한다면 네임스페이스를 변경할 수도 있습니다. Your service providers may be placed anywhere that Composer can autoload them. 여러분의 서비스 프로바이더는 컴포저가 오토로딩을 할 수 있는 곳이라면 어디에 위치하더라도 상관없습니다. 

### The Boot Method
### Boot 메소드

So, what if we need to register an event listener within our service provider? 그럼 이제 서비스 프로바이더 안에서 이벤트 리스너를 등록할 필요가 있다면 어떻게 해야 할까요? This should be done within the `boot` method. 그런 작업은 `boot` 메소드 안에서 해야합니다. **This method is called after all other service providers have been registered**, meaning you have access to all other services that have been registered by the framework. **이 메소드는 모든 다른 서비스 프로바이더들이 등록된 이후에 호출됩니다** 즉, 프레임 워크에 의해 등록된 다른 모든 서비스들에 액세스 할 수 있다는 것을 의미합니다.

	<?php namespace App\Providers;

	use Event;
	use Illuminate\Support\ServiceProvider;

	class EventServiceProvider extends ServiceProvider {

		/**
		 * Perform post-registration booting of services.
		 *
		 * @return void
		 */
		public function boot()
		{
			Event::listen('SomeEvent', 'SomeEventHandler');
		}

		/**
		 * Register bindings in the container.
		 *
		 * @return void
		 */
		public function register()
		{
			//
		}

	}

We are able to type-hint dependencies for our `boot` method. `boot` 메소드에서는 의존성 주입을 위해서 타입 힌트를 사용할 수 있습니다. The service container will automatically inject any dependencies you need: 서비스 컨테이너는 자동으로 필요한 의존 객체를 주입할 것입니다. 

	use Illuminate\Contracts\Events\Dispatcher;

	public function boot(Dispatcher $events)
	{
		$events->listen('SomeEvent', 'SomeEventHandler');
	}

<a name="registering-providers"></a>
## Registering Providers
## 프로바이더 등록하기

All service providers are registered in the `config/app.php` configuration file. 모든 서비스 프로바이더들은 `config/app.php` 설정 파일에 등록되어 있습니다. This file contains a `providers` array where you can list the names of your service providers. 이 파일에는 서비스 프로바이더들의 이름을 나열하고 등록할 수 있는 `providers` 배열이 포함되어 있습니다. By default, a set of Laravel core service providers are listed in this array. 기본적으로는 라라벨의 코어 서비스 프로바이더들이 배열에 나열되어 있습니다. These providers bootstrap the core Laravel components, such as the mailer, queue, cache, and others. 이 프로바이더들이 라라벨의 메일러, 큐, 캐시등과 같은 핵심적인 컴포넌트들을 부트스트랩핑 하게 됩니다. 

To register your provider, simply add it to the array:
여러분의 프로바이더들을 등록하려면 이 배열에 추가만 하면 됩니다. 

	'providers' => [
		// Other Service Providers

		'App\Providers\AppServiceProvider',
	],

<a name="deferred-providers"></a>
## Deferred Providers
## 지연(deferred) 프로바이더

If your provider is **only** registering bindings in the [service container](/docs/{{version}}/container), you may choose to defer its registration until one of the registered bindings is actually needed. 만약 여러분의 프로바이더가 **단지** [서비스 컨테이너](/docs/{{version}}/container)에 바인딩을 등록하기만 한다면, 등록된 바인딩이 실제로 필요할때까지 등록 자체를 지연(deferred) 시킬 수 있습니다. Deferring the loading of such a provider will improve the performance of your application, since it is not loaded from the filesystem on every request. 이러한 프로바이더 로딩의 지연(deferred)은 모든 요청에 프로바이더를 파일 시스템에서 로드하지 않으므로 애플리케이션의 성능을 향상시킬 것입니다. 

To defer the loading of a provider, set the `defer` property to `true` and define a `provides` method. 프로바이더를 지연(defer) 로딩 하려면 프로바이더의 `defer` 프로퍼티를 `true`로 설정하고 `provides` 메소드를 정의하면 됩니다. The `provides` method returns the service container bindings that the provider registers: `provides` 메소드는 서비스 컨테이너에 프로바이더를 등록하는 바인딩을 리턴합니다. 

	<?php namespace App\Providers;

	use Riak\Connection;
	use Illuminate\Support\ServiceProvider;

	class RiakServiceProvider extends ServiceProvider {

		/**
		 * Indicates if loading of the provider is deferred.
		 *
		 * @var bool
		 */
		protected $defer = true;

		/**
		 * Register the service provider.
		 *
		 * @return void
		 */
		public function register()
		{
			$this->app->singleton('Riak\Contracts\Connection', function($app)
			{
				return new Connection($app['config']['riak']);
			});
		}

		/**
		 * Get the services provided by the provider.
		 *
		 * @return array
		 */
		public function provides()
		{
			return ['Riak\Contracts\Connection'];
		}

	}

Laravel compiles and stores a list of all of the services supplied by deferred service providers, along with the name of its service provider class. 라라벨은 지연된 서비스 프로바이더가 제공하는 모든 서비스들의 리스트를 서비스 프로바이더 클래스 이름과 함께 컴파일하고 저장합니다. Then, only when you attempt to resolve one of these services does Laravel load the service provider. 그러면 라라벨은 지연된 서비스 프로바이더들 중에 하나에 대한 의존성 해결이 필요할 때에만 프로바이더를 로드합니다. 