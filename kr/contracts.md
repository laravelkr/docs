# Contracts
# Contracts

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Contracts Vs. Facades](#contracts-vs-facades)
- [When To Use Contracts](#when-to-use-contracts)
- [Contracts 사용시기](#when-to-use-contracts)
- [How To Use Contracts](#how-to-use-contracts)
- [Contracts 사용법](#how-to-use-contracts)
- [Contract Reference](#contract-reference)
- [Contract 참고](#contract-reference)


<a name="introduction"></a>
## Introduction
## 시작하기

Laravel's "contracts" are a set of interfaces that define the core services provided by the framework. For example, an `Illuminate\Contracts\Queue\Queue` contract defines the methods needed for queueing jobs, while the `Illuminate\Contracts\Mail\Mailer` contract defines the methods needed for sending e-mail.

라라벨의 "Contract"는 프레임워크에서 제공하는 코어 서비스들을 정의한 인터페이스들의 모음입니다. 예를 들어, `Illuminate\Contracts\Queue\Queue` Contract에는 어떤 작업들을 큐에서 다룰때 필요한 메소드들이 정의되어 있고, `Illuminate\Contracts\Mail\Mailer` Contract에는 이메일을 보내기 위해 필요한 메소드들을 정의되어 있습니다.

Each contract has a corresponding implementation provided by the framework. For example, Laravel provides a queue implementation with a variety of drivers, and a mailer implementation that is powered by [Symfony Mailer](https://symfony.com/doc/6.0/mailer.html).

라라벨 프레임워크에는 각각의 Contract에 상응하는 구현체(구현 클래스)가 있습니다. 예를 들어, 라라벨은 다양한 드라이버로 구현된 queue의 구현체를 가지고 있고, [Symfony Mailer](https://symfony.com/doc/6.0/mailer.html)를 mailer의 구현체로 가지고 있습니다.

All of the Laravel contracts live in [their own GitHub repository](https://github.com/illuminate/contracts). This provides a quick reference point for all available contracts, as well as a single, decoupled package that may be utilized when building packages that interact with Laravel services.

라라벨의 모든 Contract는 [각각의 Github 저장소](https://github.com/illuminate/contracts)를 가지고 있습니다. 이것은 사용가능한 모든 인터페이스에 대해 확인할 수 있는 역할을 합니다. 또한 라라벨 서비스와 작용하는 패키지를 만들때 사용할 수 있는 독립적인 인터페이스를 제공합니다.

<a name="contracts-vs-facades"></a>
### Contracts Vs. Facades
### Contracts VS Facades

Laravel's [facades](/docs/{{version}}/facades) and helper functions provide a simple way of utilizing Laravel's services without needing to type-hint and resolve contracts out of the service container. In most cases, each facade has an equivalent contract.

라라벨의 [파사드](/docs/{{version}}/facades)는 서비스 컨테이너 외부에서 타입 힌트나, contracts 의 의존성 없이도 라라벨의 서비스를 시용할 수 있는 쉬운 방법을 제공합니다.

Unlike facades, which do not require you to require them in your class' constructor, contracts allow you to define explicit dependencies for your classes. Some developers prefer to explicitly define their dependencies in this way and therefore prefer to use contracts, while other developers enjoy the convenience of facades. **In general, most applications can use facades without issue during development.**

클래스 생성자에서 요구하지 않아도 되는 파사드와 달리 contracts를 통해 클래스에 대한 명시적 의존성을 정의 할 수 있습니다. 일부 개발자는 이러한 방식으로 의존성을 명시적으로 정의하는 contracts를 선호하지만 대다수의 개발자는 파사드의 편리함을 누리고 있습니다. **일반적으로 대부분의 애플리케이션은 개발 중에 문제 없이 파사드를 사용할 수 있습니다.**

<a name="when-to-use-contracts"></a>
## When To Use Contracts
## Contracts 사용 시기

The decision to use contracts or facades will come down to personal taste and the tastes of your development team. Both contracts and facades can be used to create robust, well-tested Laravel applications. Contracts and facades are not mutually exclusive. Some parts of your applications may use facades while others depend on contracts. As long as you are keeping your class' responsibilities focused, you will notice very few practical differences between using contracts and facades.

contract나 파사드 둘 중 어느것을 사용할지는 개인적인 그리고 개발팀의 취향에 달려 있습니다. contract와 파사드 모두 강력하고 잘 테스트 된 라라벨 애플리케이션을 작성하는 데 사용할 수 있습니다. Contract와 파사드는 상호 배타적이지 않기 때문에 애플리케이션의 일부에는 파사드를 사용하고, 다른쪽에서는 Contract를 사용할 수도 있습니다. Contract와 파사드를 사용하는 것은 클래스의 역할에 집중하는 관점에서 실제적인 차이점가 거의 없습니다.

In general, most applications can use facades without issue during development. If you are building a package that integrates with multiple PHP frameworks you may wish to use the `illuminate/contracts` package to define your integration with Laravel's services without the need to require Laravel's concrete implementations in your package's `composer.json` file.

일반적으로, 대부분의 애플리케이션은 개발중에 파사드를 사용하는데 별다른 문제가 없습니다. 여러 PHP 프레임워크와 통합되는 패키지를 작성하는 경우 패키지에서 라라벨의 구체적인 구현서비스를 호출할 필요없이 `composer.json` 파일에 라라벨의 `illuminate/contracts` 패키지를 추가하여 통합을 정의할 수 있습니다.

<a name="how-to-use-contracts"></a>
## How To Use Contracts
## Contract는 어떻게 사용할 수 있는가?

So, how do you get an implementation of a contract? It's actually quite simple.

그럼 어떻게 Contract의 구현체를 얻을 수 있을까요? 사실 매우 간단합니다.

Many types of classes in Laravel are resolved through the [service container](/docs/{{version}}/container), including controllers, event listeners, middleware, queued jobs, and even route closures. So, to get an implementation of a contract, you can just "type-hint" the interface in the constructor of the class being resolved.

라라벨에 있는 여러 종류의 클래스들은 컨트롤러, 이벤트리스너, 미들웨어, 큐 작업, 라우트 클로저들을 관리하는 [서비스 컨테이너](/docs/{{version}}/container)를 통해 의존성 해결(resolve) 되고 있습니다. 따라서 의존성이 해결되는 어떤 클래스가 특정 Contract의 구현체를 얻으려면 그 클래스의 생성자에 그 인터페이스를 "type-hint"로 지정해놓으면 됩니다

For example, take a look at this event listener:

그 예로 아래의 이벤트 리스너를 살펴보겠습니다.

    <?php

    namespace App\Listeners;

    use App\Events\OrderWasPlaced;
    use App\Models\User;
    use Illuminate\Contracts\Redis\Factory;

    class CacheOrderInformation
    {
        /**
         * The Redis factory implementation.
         *
         * @var \Illuminate\Contracts\Redis\Factory
         */
        protected $redis;

        /**
         * Create a new event handler instance.
         *
         * @param  \Illuminate\Contracts\Redis\Factory  $redis
         * @return void
         */
        public function __construct(Factory $redis)
        {
            $this->redis = $redis;
        }

        /**
         * Handle the event.
         *
         * @param  \App\Events\OrderWasPlaced  $event
         * @return void
         */
        public function handle(OrderWasPlaced $event)
        {
            //
        }
    }

When the event listener is resolved, the service container will read the type-hints on the constructor of the class, and inject the appropriate value. To learn more about registering things in the service container, check out [its documentation](/docs/{{version}}/container).

이벤트리스너가 의존성 해결될 때, 서비스 컨테이너는 클래스의 생성자에 있는 타입힌트를 읽고, 그에 적합한 객체 인스턴스를 주입해 줍니다. 서비스 컨테이너에 무언가를 등록하는 것에 대하여 더 알고싶다면, [이 문서](/docs/{{version}}/container)를 참고하시기 바랍니다.

<a name="contract-reference"></a>
## Contract Reference
## Contract 참조

This table provides a quick reference to all of the Laravel contracts and their equivalent facades:

아래는 대부분의 라라벨 Contract와 그에 대응되는 파사드들의 레퍼런스입니다.

| Contract                                                                                                                                               | References Facade         |
|--------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [Illuminate\Contracts\Auth\Access\Authorizable](https://github.com/illuminate/contracts/blob/{{version}}/Auth/Access/Authorizable.php)                 |  &nbsp;                   |
| [Illuminate\Contracts\Auth\Access\Gate](https://github.com/illuminate/contracts/blob/{{version}}/Auth/Access/Gate.php)                                 | `Gate`                    |
| [Illuminate\Contracts\Auth\Authenticatable](https://github.com/illuminate/contracts/blob/{{version}}/Auth/Authenticatable.php)                         |  &nbsp;                   |
| [Illuminate\Contracts\Auth\CanResetPassword](https://github.com/illuminate/contracts/blob/{{version}}/Auth/CanResetPassword.php)                       | &nbsp;                    |
| [Illuminate\Contracts\Auth\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Auth/Factory.php)                                         | `Auth`                    |
| [Illuminate\Contracts\Auth\Guard](https://github.com/illuminate/contracts/blob/{{version}}/Auth/Guard.php)                                             | `Auth::guard()`           |
| [Illuminate\Contracts\Auth\PasswordBroker](https://github.com/illuminate/contracts/blob/{{version}}/Auth/PasswordBroker.php)                           | `Password::broker()`      |
| [Illuminate\Contracts\Auth\PasswordBrokerFactory](https://github.com/illuminate/contracts/blob/{{version}}/Auth/PasswordBrokerFactory.php)             | `Password`                |
| [Illuminate\Contracts\Auth\StatefulGuard](https://github.com/illuminate/contracts/blob/{{version}}/Auth/StatefulGuard.php)                             | &nbsp;                    |
| [Illuminate\Contracts\Auth\SupportsBasicAuth](https://github.com/illuminate/contracts/blob/{{version}}/Auth/SupportsBasicAuth.php)                     | &nbsp;                    |
| [Illuminate\Contracts\Auth\UserProvider](https://github.com/illuminate/contracts/blob/{{version}}/Auth/UserProvider.php)                               | &nbsp;                    |
| [Illuminate\Contracts\Bus\Dispatcher](https://github.com/illuminate/contracts/blob/{{version}}/Bus/Dispatcher.php)                                     | `Bus`                     |
| [Illuminate\Contracts\Bus\QueueingDispatcher](https://github.com/illuminate/contracts/blob/{{version}}/Bus/QueueingDispatcher.php)                     | `Bus::dispatchToQueue()`  |
| [Illuminate\Contracts\Broadcasting\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Broadcasting/Factory.php)                         | `Broadcast`               |
| [Illuminate\Contracts\Broadcasting\Broadcaster](https://github.com/illuminate/contracts/blob/{{version}}/Broadcasting/Broadcaster.php)                 | `Broadcast::connection()` |
| [Illuminate\Contracts\Broadcasting\ShouldBroadcast](https://github.com/illuminate/contracts/blob/{{version}}/Broadcasting/ShouldBroadcast.php)         | &nbsp;                    |
| [Illuminate\Contracts\Broadcasting\ShouldBroadcastNow](https://github.com/illuminate/contracts/blob/{{version}}/Broadcasting/ShouldBroadcastNow.php)   | &nbsp;                    |
| [Illuminate\Contracts\Cache\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Cache/Factory.php)                                       | `Cache`                   |
| [Illuminate\Contracts\Cache\Lock](https://github.com/illuminate/contracts/blob/{{version}}/Cache/Lock.php)                                             | &nbsp;                    |
| [Illuminate\Contracts\Cache\LockProvider](https://github.com/illuminate/contracts/blob/{{version}}/Cache/LockProvider.php)                             | &nbsp;                    |
| [Illuminate\Contracts\Cache\Repository](https://github.com/illuminate/contracts/blob/{{version}}/Cache/Repository.php)                                 | `Cache::driver()`         |
| [Illuminate\Contracts\Cache\Store](https://github.com/illuminate/contracts/blob/{{version}}/Cache/Store.php)                                           | &nbsp;                    |
| [Illuminate\Contracts\Config\Repository](https://github.com/illuminate/contracts/blob/{{version}}/Config/Repository.php)                               | `Config`                  |
| [Illuminate\Contracts\Console\Application](https://github.com/illuminate/contracts/blob/{{version}}/Console/Application.php)                           | &nbsp;                    |
| [Illuminate\Contracts\Console\Kernel](https://github.com/illuminate/contracts/blob/{{version}}/Console/Kernel.php)                                     | `Artisan`                 |
| [Illuminate\Contracts\Container\Container](https://github.com/illuminate/contracts/blob/{{version}}/Container/Container.php)                           | `App`                     |
| [Illuminate\Contracts\Cookie\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Cookie/Factory.php)                                     | `Cookie`                  |
| [Illuminate\Contracts\Cookie\QueueingFactory](https://github.com/illuminate/contracts/blob/{{version}}/Cookie/QueueingFactory.php)                     | `Cookie::queue()`         |
| [Illuminate\Contracts\Database\ModelIdentifier](https://github.com/illuminate/contracts/blob/{{version}}/Database/ModelIdentifier.php)                 | &nbsp;                    |
| [Illuminate\Contracts\Debug\ExceptionHandler](https://github.com/illuminate/contracts/blob/{{version}}/Debug/ExceptionHandler.php)                     | &nbsp;                    |
| [Illuminate\Contracts\Encryption\Encrypter](https://github.com/illuminate/contracts/blob/{{version}}/Encryption/Encrypter.php)                         | `Crypt`                   |
| [Illuminate\Contracts\Events\Dispatcher](https://github.com/illuminate/contracts/blob/{{version}}/Events/Dispatcher.php)                               | `Event`                   |
| [Illuminate\Contracts\Filesystem\Cloud](https://github.com/illuminate/contracts/blob/{{version}}/Filesystem/Cloud.php)                                 | `Storage::cloud()`        |
| [Illuminate\Contracts\Filesystem\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Filesystem/Factory.php)                             | `Storage`                 |
| [Illuminate\Contracts\Filesystem\Filesystem](https://github.com/illuminate/contracts/blob/{{version}}/Filesystem/Filesystem.php)                       | `Storage::disk()`         |
| [Illuminate\Contracts\Foundation\Application](https://github.com/illuminate/contracts/blob/{{version}}/Foundation/Application.php)                     | `App`                     |
| [Illuminate\Contracts\Hashing\Hasher](https://github.com/illuminate/contracts/blob/{{version}}/Hashing/Hasher.php)                                     | `Hash`                    |
| [Illuminate\Contracts\Http\Kernel](https://github.com/illuminate/contracts/blob/{{version}}/Http/Kernel.php)                                           | &nbsp;                    |
| [Illuminate\Contracts\Mail\MailQueue](https://github.com/illuminate/contracts/blob/{{version}}/Mail/MailQueue.php)                                     | `Mail::queue()`           |
| [Illuminate\Contracts\Mail\Mailable](https://github.com/illuminate/contracts/blob/{{version}}/Mail/Mailable.php)                                       | &nbsp;                    |
| [Illuminate\Contracts\Mail\Mailer](https://github.com/illuminate/contracts/blob/{{version}}/Mail/Mailer.php)                                           | `Mail`                    |
| [Illuminate\Contracts\Notifications\Dispatcher](https://github.com/illuminate/contracts/blob/{{version}}/Notifications/Dispatcher.php)                 | `Notification`            |
| [Illuminate\Contracts\Notifications\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Notifications/Factory.php)                       | `Notification`            |
| [Illuminate\Contracts\Pagination\LengthAwarePaginator](https://github.com/illuminate/contracts/blob/{{version}}/Pagination/LengthAwarePaginator.php)   | &nbsp;                    |
| [Illuminate\Contracts\Pagination\Paginator](https://github.com/illuminate/contracts/blob/{{version}}/Pagination/Paginator.php)                         | &nbsp;                    |
| [Illuminate\Contracts\Pipeline\Hub](https://github.com/illuminate/contracts/blob/{{version}}/Pipeline/Hub.php)                                         | &nbsp;                    |
| [Illuminate\Contracts\Pipeline\Pipeline](https://github.com/illuminate/contracts/blob/{{version}}/Pipeline/Pipeline.php)                               | &nbsp;                    |
| [Illuminate\Contracts\Queue\EntityResolver](https://github.com/illuminate/contracts/blob/{{version}}/Queue/EntityResolver.php)                         | &nbsp;                    |
| [Illuminate\Contracts\Queue\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Queue/Factory.php)                                       | `Queue`                   |
| [Illuminate\Contracts\Queue\Job](https://github.com/illuminate/contracts/blob/{{version}}/Queue/Job.php)                                               | &nbsp;                    |
| [Illuminate\Contracts\Queue\Monitor](https://github.com/illuminate/contracts/blob/{{version}}/Queue/Monitor.php)                                       | `Queue`                   |
| [Illuminate\Contracts\Queue\Queue](https://github.com/illuminate/contracts/blob/{{version}}/Queue/Queue.php)                                           | `Queue::connection()`     |
| [Illuminate\Contracts\Queue\QueueableCollection](https://github.com/illuminate/contracts/blob/{{version}}/Queue/QueueableCollection.php)               | &nbsp;                    |
| [Illuminate\Contracts\Queue\QueueableEntity](https://github.com/illuminate/contracts/blob/{{version}}/Queue/QueueableEntity.php)                       | &nbsp;                    |
| [Illuminate\Contracts\Queue\ShouldQueue](https://github.com/illuminate/contracts/blob/{{version}}/Queue/ShouldQueue.php)                               | &nbsp;                    |
| [Illuminate\Contracts\Redis\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Redis/Factory.php)                                       | `Redis`                   |
| [Illuminate\Contracts\Routing\BindingRegistrar](https://github.com/illuminate/contracts/blob/{{version}}/Routing/BindingRegistrar.php)                 | `Route`                   |
| [Illuminate\Contracts\Routing\Registrar](https://github.com/illuminate/contracts/blob/{{version}}/Routing/Registrar.php)                               | `Route`                   |
| [Illuminate\Contracts\Routing\ResponseFactory](https://github.com/illuminate/contracts/blob/{{version}}/Routing/ResponseFactory.php)                   | `Response`                |
| [Illuminate\Contracts\Routing\UrlGenerator](https://github.com/illuminate/contracts/blob/{{version}}/Routing/UrlGenerator.php)                         | `URL`                     |
| [Illuminate\Contracts\Routing\UrlRoutable](https://github.com/illuminate/contracts/blob/{{version}}/Routing/UrlRoutable.php)                           | &nbsp;                    |
| [Illuminate\Contracts\Session\Session](https://github.com/illuminate/contracts/blob/{{version}}/Session/Session.php)                                   | `Session::driver()`       |
| [Illuminate\Contracts\Support\Arrayable](https://github.com/illuminate/contracts/blob/{{version}}/Support/Arrayable.php)                               | &nbsp;                    |
| [Illuminate\Contracts\Support\Htmlable](https://github.com/illuminate/contracts/blob/{{version}}/Support/Htmlable.php)                                 | &nbsp;                    |
| [Illuminate\Contracts\Support\Jsonable](https://github.com/illuminate/contracts/blob/{{version}}/Support/Jsonable.php)                                 | &nbsp;                    |
| [Illuminate\Contracts\Support\MessageBag](https://github.com/illuminate/contracts/blob/{{version}}/Support/MessageBag.php)                             | &nbsp;                    |
| [Illuminate\Contracts\Support\MessageProvider](https://github.com/illuminate/contracts/blob/{{version}}/Support/MessageProvider.php)                   | &nbsp;                    |
| [Illuminate\Contracts\Support\Renderable](https://github.com/illuminate/contracts/blob/{{version}}/Support/Renderable.php)                             | &nbsp;                    |
| [Illuminate\Contracts\Support\Responsable](https://github.com/illuminate/contracts/blob/{{version}}/Support/Responsable.php)                           | &nbsp;                    |
| [Illuminate\Contracts\Translation\Loader](https://github.com/illuminate/contracts/blob/{{version}}/Translation/Loader.php)                             | &nbsp;                    |
| [Illuminate\Contracts\Translation\Translator](https://github.com/illuminate/contracts/blob/{{version}}/Translation/Translator.php)                     | `Lang`                    |
| [Illuminate\Contracts\Validation\Factory](https://github.com/illuminate/contracts/blob/{{version}}/Validation/Factory.php)                             | `Validator`               |
| [Illuminate\Contracts\Validation\ImplicitRule](https://github.com/illuminate/contracts/blob/{{version}}/Validation/ImplicitRule.php)                   | &nbsp;                    |
| [Illuminate\Contracts\Validation\Rule](https://github.com/illuminate/contracts/blob/{{version}}/Validation/Rule.php)                                   | &nbsp;                    |
| [Illuminate\Contracts\Validation\ValidatesWhenResolved](https://github.com/illuminate/contracts/blob/{{version}}/Validation/ValidatesWhenResolved.php) | &nbsp;                    |
| [Illuminate\Contracts\Validation\Validator](https://github.com/illuminate/contracts/blob/{{version}}/Validation/Validator.php)                         | `Validator::make()`       |
| [Illuminate\Contracts\View\Engine](https://github.com/illuminate/contracts/blob/{{version}}/View/Engine.php)                                           | &nbsp;                    |
| [Illuminate\Contracts\View\Factory](https://github.com/illuminate/contracts/blob/{{version}}/View/Factory.php)                                         | `View`                    |
| [Illuminate\Contracts\View\View](https://github.com/illuminate/contracts/blob/{{version}}/View/View.php) 
