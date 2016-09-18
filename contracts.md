# Contracts

- [소개](#introduction)
- [왜 Contracts인가?](#why-contracts)
- [Contract 참조](#contract-reference)
- [Contract 사용법](#how-to-use-contracts)

<a name="introduction"></a>
## 소개

라라벨의 Contract는 프레임워크에서 제공하는 코어 서비스들을 정의한 인터페이스들의 모음입니다. 예를 들어, `Queue` Contract에는 어떤 작업들을 큐에서 다룰때 필요한 메소드들이 정의되어 있고, `Mailer` Contract에는 이메일을 보내기 위해 필요한 메소드들을 정의되어 있습니다.

라라벨 프레임워크에는 각각의 Contract에 상응하는 구현체(구현 클래스)가 있습니다. 예를 들어, 라라벨은 다양한 드라이버로 구현된 `Queue`의 구현체를 가지고 있고, `Mailer`의 구현체는 [SwiftMailer](http://swiftmailer.org/)를 통해 가지고 있습니다.

라라벨의 모든 Contract는 [별도 Github 저장소](https://github.com/illuminate/contracts)를 가지고 있습니다. 이것은 별도의 패키지에 의존하지 않는 하나의 단일 패키지로 다른 패키지 개발자들에 의해서 사용될 수 있도록 하는 모든 사용 가능한 contract를 위한 하나의 레퍼런스를 제공합니다.

<!--chak-comment-Contracts-소개-->

<a name="why-contracts"></a>
## 왜 Contract인가?

아마 여러분은 Contracts와 관련해 몇 가지 질문을 가지고 있을 것입니다. 왜 모든 것에 인터페이스를 사용하는 것일까? 인터페이스를 사용하면 더 복잡해지지 않나?

다음의 주제로 인터페이스를 사용하는 이유에 대하여 살펴봅시다: 느슨한 결합과 단순성

### 느슨한 결합

우선, 한 캐시 구현체에 밀접하게 결합돼 있는 코드를 살펴봅시다:

	<?php namespace App\Orders;

	class Repository {

		/**
		 * The cache.
		 */
		protected $cache;

		/**
		 * Create a new repository instance.
		 *
		 * @param  \SomePackage\Cache\Memcached  $cache
		 * @return void
		 */
		public function __construct(\SomePackage\Cache\Memcached $cache)
		{
			$this->cache = $cache;
		}

		/**
		 * Retrieve an Order by ID.
		 *
		 * @param  int  $id
		 * @return Order
		 */
		public function find($id)
		{
			if ($this->cache->has($id))
			{
				//
			}
		}

	}

이 클래스의 코드는 주어진 캐시 구현체와 밀접하게 결합돼 있습니다. 특정 패키지 벤더의 캐시 구상클래스에 의존하기 때문에 이 코드는 캐스 클래스와 밀접하게 결합돼 있는 것입니다. 만약 이 패키지의 API가 변경되면 예로든 코드 또한, 변경되어야 합니다.

또한, 코드가 사용하는 캐시(Memcached)를 다른 것(Redis)으로 변경하고자 한다면, 역시나 Repository 클래스를 다시 수정해야만 할 것입니다. 저장소 클래스는 누가 어떻게 데이터를 제공하는지에 대한 정보를 너무 많이 가지고 있어서는 안 됩니다.

**이렇게 접근하는 대신, 특정 벤더에 구속되지 않고 단순한 인터페이스에 의존하도록 하여 코드를 개선할 수 있습니다:**

	<?php namespace App\Orders;

	use Illuminate\Contracts\Cache\Repository as Cache;

	class Repository {

		/**
		 * Create a new repository instance.
		 *
		 * @param  Cache  $cache
		 * @return void
		 */
		public function __construct(Cache $cache)
		{
			$this->cache = $cache;
		}

	}

이제 코드는 어떤 특정 벤더, 심지어 라라벨과도 결합되지 않습니다. Contract는 구현체를 가지지 않고, 의존성도 없기 때문에, 주어진  Contract의 다른 구현체를 쉽게 작성할 수 있습니다. 캐시를 사용하는 코드를 수정하지 않고도 캐시 구현체를 대체할 수 있습니다.

### 단순성

라라벨의 모든 서비스들이 단순한 인터페이스로 보기좋게 정의돼 있으므로, 그 서비스들에 의해 제공되는 기능을 알아내는 것이 매우 쉽습니다. **Contract들이 프레임워크의 기능들에 대한 간결한 도큐먼트의 역할을 하는 것입니다.**

또한, 여러분이 간단한 인터페이스에 의존하게 되면, 여러분의 코드는 이해하거나 유지보수하기가 더 쉽워집니다. 크고 복잡한 클래스에서 사용할 수 있는 메소드들을 훑어보는 대신, 단순하고 깨끗한 인터페이스를 참고할 수 있습니다.

<!--chak-comment-Contracts-왜-Contract인가--->

<a name="contract-reference"></a>
## Contract 레퍼런스

아래는 대부분의 라라벨 Contract와 그에 대응되는 파사드들의 레퍼런스입니다:

Contract  |  Laravel 5.x Facade
------------- | -------------
[Illuminate\Contracts\Auth\Guard](https://github.com/illuminate/contracts/blob/master/Auth/Guard.php)  |  Auth
[Illuminate\Contracts\Auth\PasswordBroker](https://github.com/illuminate/contracts/blob/master/Auth/PasswordBroker.php)  |  Password
[Illuminate\Contracts\Bus\Dispatcher](https://github.com/illuminate/contracts/blob/master/Bus/Dispatcher.php)  |  Bus
[Illuminate\Contracts\Cache\Repository](https://github.com/illuminate/contracts/blob/master/Cache/Repository.php) | Cache
[Illuminate\Contracts\Cache\Factory](https://github.com/illuminate/contracts/blob/master/Cache/Factory.php) | Cache::driver()
[Illuminate\Contracts\Config\Repository](https://github.com/illuminate/contracts/blob/master/Config/Repository.php) | Config
[Illuminate\Contracts\Container\Container](https://github.com/illuminate/contracts/blob/master/Container/Container.php) | App
[Illuminate\Contracts\Cookie\Factory](https://github.com/illuminate/contracts/blob/master/Cookie/Factory.php) | Cookie
[Illuminate\Contracts\Cookie\QueueingFactory](https://github.com/illuminate/contracts/blob/master/Cookie/QueueingFactory.php) | Cookie::queue()
[Illuminate\Contracts\Encryption\Encrypter](https://github.com/illuminate/contracts/blob/master/Encryption/Encrypter.php) | Crypt
[Illuminate\Contracts\Events\Dispatcher](https://github.com/illuminate/contracts/blob/master/Events/Dispatcher.php) | Event
[Illuminate\Contracts\Filesystem\Cloud](https://github.com/illuminate/contracts/blob/master/Filesystem/Cloud.php) | &nbsp;
[Illuminate\Contracts\Filesystem\Factory](https://github.com/illuminate/contracts/blob/master/Filesystem/Factory.php) | File
[Illuminate\Contracts\Filesystem\Filesystem](https://github.com/illuminate/contracts/blob/master/Filesystem/Filesystem.php) | File
[Illuminate\Contracts\Foundation\Application](https://github.com/illuminate/contracts/blob/master/Foundation/Application.php) | App
[Illuminate\Contracts\Hashing\Hasher](https://github.com/illuminate/contracts/blob/master/Hashing/Hasher.php) | Hash
[Illuminate\Contracts\Logging\Log](https://github.com/illuminate/contracts/blob/master/Logging/Log.php) | Log
[Illuminate\Contracts\Mail\MailQueue](https://github.com/illuminate/contracts/blob/master/Mail/MailQueue.php) | Mail::queue()
[Illuminate\Contracts\Mail\Mailer](https://github.com/illuminate/contracts/blob/master/Mail/Mailer.php) | Mail
[Illuminate\Contracts\Queue\Factory](https://github.com/illuminate/contracts/blob/master/Queue/Factory.php) | Queue::driver()
[Illuminate\Contracts\Queue\Queue](https://github.com/illuminate/contracts/blob/master/Queue/Queue.php) | Queue
[Illuminate\Contracts\Redis\Database](https://github.com/illuminate/contracts/blob/master/Redis/Database.php) | Redis
[Illuminate\Contracts\Routing\Registrar](https://github.com/illuminate/contracts/blob/master/Routing/Registrar.php) | Route
[Illuminate\Contracts\Routing\ResponseFactory](https://github.com/illuminate/contracts/blob/master/Routing/ResponseFactory.php) | Response
[Illuminate\Contracts\Routing\UrlGenerator](https://github.com/illuminate/contracts/blob/master/Routing/UrlGenerator.php) | URL
[Illuminate\Contracts\Support\Arrayable](https://github.com/illuminate/contracts/blob/master/Support/Arrayable.php) | &nbsp;
[Illuminate\Contracts\Support\Jsonable](https://github.com/illuminate/contracts/blob/master/Support/Jsonable.php) | &nbsp;
[Illuminate\Contracts\Support\Renderable](https://github.com/illuminate/contracts/blob/master/Support/Renderable.php) | &nbsp;
[Illuminate\Contracts\Validation\Factory](https://github.com/illuminate/contracts/blob/master/Validation/Factory.php) | Validator::make()
[Illuminate\Contracts\Validation\Validator](https://github.com/illuminate/contracts/blob/master/Validation/Validator.php) | &nbsp;
[Illuminate\Contracts\View\Factory](https://github.com/illuminate/contracts/blob/master/View/Factory.php) | View::make()
[Illuminate\Contracts\View\View](https://github.com/illuminate/contracts/blob/master/View/View.php) | &nbsp;

<!--chak-comment-Contracts-Contract-레퍼런스-->

<a name="how-to-use-contracts"></a>
## Contract 사용법

그럼 어떻게 Contract의 구현체를 얻을 수 있을까요? 사실 매우 간단합니다. 라라벨에 있는 여러 종류의 클래스들은 컨트롤러, 이벤트리스너, 필터, 큐 작업, 라우트 클로저들을 관리하는 [서비스 컨테이너](/docs/5.0/container)를 통해 의존성 해결(resolve) 되고 있습니다. 따라서 의존성이 해결되는 어떤 클래스가 특정 Contract의 구현체를 얻으려면 그 클래스의 생성자에 그 인터페이스를 "type-hint"로 지정해놓으면 됩니다. 그 예로 아래의 이벤트핸들러를 보겠습니다:

	<?php namespace App\Handlers\Events;

	use App\User;
	use App\Events\NewUserRegistered;
	use Illuminate\Contracts\Redis\Database;

	class CacheUserInformation {

		/**
		 * The Redis database implementation.
		 */
		protected $redis;

		/**
		 * Create a new event handler instance.
		 *
		 * @param  Database  $redis
		 * @return void
		 */
		public function __construct(Database $redis)
		{
			$this->redis = $redis;
		}

		/**
		 * Handle the event.
		 *
		 * @param  NewUserRegistered  $event
		 * @return void
		 */
		public function handle(NewUserRegistered $event)
		{
			//
		}

	}

이벤트리스너가 의존성 해결될 때, 서비스 컨테이너는 클래스의 생성자에 있는 타입힌트를 읽고, 그에 적합한 값을 주입해 줍니다. 서비스 컨테이너에 무언가를 등록하는 것에 대하여 더 알고싶다면, [이 문서](/docs/5.0/container)를 보시기 바랍니다.

<!--chak-comment-Contracts-Contract-사용법-->
