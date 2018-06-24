# Command Bus 커맨드 버스

- [Introduction 소개](#introduction)
- [Creating Commands 커맨드 생성하기](#creating-commands)
- [Dispatching Commands 커맨드 디스패치](#dispatching-commands)
- [Queued Commands 큐 커맨드](#queued-commands)
- [Command Pipeline 커맨드 파이프라인](#command-pipeline)

<a name="introduction"></a>
## Introduction
## 소개

The Laravel command bus provides a convenient method of encapsulating tasks your application needs to perform into simple, easy to understand "commands". 라라벨의 커맨드 버스는 애플리케이션이 수행할 필요가 있는 작업들을 캡슐화 하는 편리한 방법을 제공합니다. To help us understand the purpose of commands, let's pretend we are building an application that allows users to purchase podcasts. 커맨드 목적의 이해를 돕기 위해서, 사용자가 팟캐스트를 구매 할 수 있는 애플리케이션을 만들고 있다고 가정해봅시다.

When a user purchases a podcast, there are a variety of things that need to happen. 사용자가 팟캐스트를 구매할 때 필요한 다양한 작업들이 있습니다. For example, we may need to charge the user's credit card, add a record to our database that represents the purchase, and send a confirmation e-mail of the purchase. 예를 들어, 사용자의 신용 카드로 요금 청구를 하고, 데이터베이스에 구매를 나타내는  레코드를 추가하고, 구매 확인 메일을 보내야 할 것 입니다. Perhaps we also need to perform some kind of validation as to whether the user is allowed to purchase podcasts. 어쩌면 우리는 사용자가 팟캐스트를 구매 할 수 있는지에 대해 어떠한 검증을 해야 할 수도 있습니다. 

We could put all of this logic inside a controller method; however, this has several disadvantages. 이러한 로직들을 하나의 컨트롤러 메소드에 모두 넣을 수 있지만, 그럴경우 몇가지 단점이 있습니다. The first disadvantage is that our controller probably handles several other incoming HTTP actions, and including complicated logic in each controller method will soon bloat our controller and make it harder to read. 첫째로, 컨트롤러는 아마도 여러개의 각기 다른 HTTP 액션들을 처리하게 되는데, 각각의 컨트롤러 메소드가 복잡한 로직을 포함하고 있으면 컨트롤러를 비대하게 하고 읽기 어렵게 만들 것입니다. Secondly, it is difficult to re-use the purchase podcast logic outside of the controller context. 두번째로, 컨트롤러 외부에서 팟캐스트를 구매하는 로직을 재사용하는 것이 어렵습니다. Thirdly, it is more difficult to unit-test the command as we must also generate a stub HTTP request and make a full request to the application to test the purchase podcast logic. 세번째로, 팟캐스트 구매 로직을 테스트하기 위해 해당하는 HTTP 요청을 생성하고 응용 프로그램에 보내는 요청을 직접 수행 하지 않으면 안되기 때문에, 커맨드의 유닛 테스트가 훨씬 어려워질 것입니다.

Instead of putting this logic in the controller, we may choose to encapsulate it within a "command" object, such as a `PurchasePodcast` command. 컨트롤러에 이러한 로직을 넣는 것 대신, `PurchasePodcast` 커맨드와 같은 “명령어” 객체 안에 캡슐화하는 방법을 선택할 수 있습니다. 

<a name="creating-commands"></a>
## Creating Commands
## 커맨드 생성하기

The Artisan CLI can generate new command classes using the `make:command` command: 아티즌 CLI에서는 `make:command` 명령어를 사용하여 새로운 커맨드 클래스를 생성할 수 있습니다. 

	php artisan make:command PurchasePodcast

The newly generated class will be placed in the `app/Commands` directory. 새롭게 생성되는 클래스는 `app/Commands` 디렉토리에 들어갑니다. By default, the command contains two methods: the constructor and the `handle` method. 기본적으로 커맨드는 생성자와 `handle` 메소드의 2가지 메소드를 가지고 있습니다. Of course, the constructor allows you to pass any relevant objects to the command, while the `handle` method executes the command. For example: 물론 생성자에 `handle` 메소드에 의해서 실행되는 커맨드와 관련된 객체를 전달 할 수 있습니다. 

	class PurchasePodcast extends Command implements SelfHandling {

		protected $user, $podcast;

		/**
		 * Create a new command instance.
		 *
		 * @return void
		 */
		public function __construct(User $user, Podcast $podcast)
		{
			$this->user = $user;
			$this->podcast = $podcast;
		}

		/**
		 * Execute the command.
		 *
		 * @return void
		 */
		public function handle()
		{
			// Handle the logic to purchase the podcast...

			event(new PodcastWasPurchased($this->user, $this->podcast));
		}

	}

The `handle` method may also type-hint dependencies, and they will be automatically injected by the [service container](/docs/{{version}}/container). 또한 `handle` 메소드에서도 타입힌트를 사용하여 [서비스 컨테이너](/docs/{{version}}/container)가 의존 객체를 자동으로 주입할 수 있도록 할 수 있습니다. For example: 예를 들어

		/**
		 * Execute the command.
		 *
		 * @return void
		 */
		public function handle(BillingGateway $billing)
		{
			// Handle the logic to purchase the podcast...
		}

<a name="dispatching-commands"></a>
## Dispatching Commands
## 커맨드 디스패치

So, once we have created a command, how do we dispatch it? 이제 커맨드를 생성하였드면 어떻게 처리할 까요? Of course, we could call the `handle` method directly; however, dispatching the command through the Laravel "command bus" has several advantages which we will discuss later. 물론 직접 `handle` 메소드를 호출할 수도 있지만, 라라벨의 “커맨드 버스”를 통해서 처리하면 앞으로 이야기할 몇몇 장점들이 있습니다. 

If you glance at your application's base controller, you will see the `DispatchesCommands` trait. 만약 여러분이 애플리케이션의 베이스 컨트롤러를 살펴 본다면`DispatchesCommands` trait 를 볼 수 있습니다. This trait allows us to call the `dispatch` method from any of our controllers. For example: 이 trait 는 어느 컨트롤러에서라도 `dispatch` 메소드를 호출할 수 있게 해줍니다. 

	public function purchasePodcast($podcastId)
	{
		$this->dispatch(
			new PurchasePodcast(Auth::user(), Podcast::findOrFail($podcastId))
		);
	}

The command bus will take care of executing the command and calling the IoC container to inject any needed dependencies into the `handle` method. 커맨드 버스는 커맨드의 실행을 시켜주고 `handle` 메소드 에서 필요한 의존 객체들을 주입해주기 위해서 IoC 컨테이너를 호출합니다. 

You may add the `Illuminate\Foundation\Bus\DispatchesCommands` trait to any class you wish. 원한다면 아무 클래스에 `Illuminate\Foundation\Bus\DispatchesCommands` trait 를 추가할 수도 있습니다. If you would like to receive a command bus instance through the constructor of any of your classes, you may type-hint the `Illuminate\Contracts\Bus\Dispatcher` interface. 만약 어떤 클래스에서든 생성자에 커맨드 버스 인스턴스를 받고자 한다면 `Illuminate\Contracts\Bus\Dispatcher` 인터페이스를 타입힌트 하면 됩니다. Finally, you may also use the `Bus` facade to quickly dispatch commands: 끝으로 빠르게 커맨드를 처리하기 위해서 `Bus` 파사드를 사용할수도 있습니다. 

		Bus::dispatch(
			new PurchasePodcast(Auth::user(), Podcast::findOrFail($podcastId))
		);

### Mapping Command Properties From Requests
### 요청으로부터 커맨드 프로퍼티 맵핑

It is very common to map HTTP request variables into commands. 명령어로 HTTP 요청 변수를 매핑하는 것은 매우 일반적입니다. So, instead of forcing you to do this manually for each request, Laravel provides some helper methods to make it a cinch. 그래서 힘들게 여러분이 각각의 요청에 일일이 작업 하게 하는대신 라라벨에서는 몇개의 헬퍼 메소드를 제공하여 처리할 수 있도록 해줍니다. Let's take a look at the `dispatchFrom` method available on the `DispatchesCommands` trait: `DispatchesCommands` trait 에서 사용 가능한 `dispatchFrom` 메소드를 살펴보도록 합시다. 

	$this->dispatchFrom('Command\Class\Name', $request);

This method will examine the constructor of the command class it is given, and then extract variables from the HTTP request (or any other `ArrayAccess` object) to fill the needed constructor parameters of the command. 이 메서드는 주어진 커맨드 클래스의 생성자를 검사하여  HTTP 요청(또는 어떠한 다른 `ArrayAccess` 객체)으로 부터 변수를 추출할 것입니다. So, if our command class accepts a `firstName` variable in its constructor, the command bus will attempt to pull the `firstName` parameter from the HTTP request. 만약 커맨드 클래스의 생성자에서 `firstName` 변수를 필요로 한다면 커맨드 버스는 HTTP 요청으로 부터 `firstName` 파라미터를 가져올 것입니다. 

You may also pass an array as the third argument to the `dispatchFrom` method. `dispatchFrom` 메소드의 세번째 인자로 배열을 전달할 수 있습니다. This array will be used to fill any constructor parameters that are not available on the request: 이 배열은 HTTP 요청에서 얻을 수 없는 파라미터를 채우는데 사용됩니다. 

	$this->dispatchFrom('Command\Class\Name', $request, [
		'firstName' => 'Taylor',
	]);

<a name="queued-commands"></a>
## Queued Commands
## 큐 커맨드

The command bus is not just for synchronous jobs that run during the current request cycle, but also serves as the primary way to build queued jobs in Laravel. 커맨드 버스는 현재의 요청 라이프사이클동안 수행되는 동기 작업들 뿐만 아니라 라라벨의 큐작업들을 생성하기 위한 주요한 방법도 제공합니다. So, how do we instruct command bus to queue our job for background processing instead of running it synchronously? 그러면, 어떻게하면 커맨드 버스가 동기적 처리 대신에 백그라운드 처리를 위한 큐 작업을 실행하도록 할수 있을까요? It's easy. 매우 쉽습니다. Firstly, when generating a new command, just add the `--queued` flag to the command: 먼저 새로운 커맨드를 생성할 때 아티즌 명령어에 `--queued` 옵션을 덧붙이면 됩니다. 

	php artisan make:command PurchasePodcast --queued

As you will see, this adds a few more features to the command, namely the `Illuminate\Contracts\Queue\ShouldBeQueued` interface and the `SerializesModels` trait. 여러분이 확인할 수 있듯이 이 옵션은 커맨드에 `Illuminate\Contracts\Queue\ShouldBeQueued` 인터페이스와 `SerializesModels` trait 기능을 추가합니다. These instruct the command bus to queue the command, as well as gracefully serialize and deserialize any Eloquent models your command stores as properties. 이 기능들은 커맨드 버스가 커맨드를 큐에 추가하도록 지시할 뿐만 아니라, 시리얼라이즈(serialize) 합니다. 그리고 커맨드의 프로퍼티로 저장된 Eloquent 모델들은 역시리얼라이즈(deserializa) 합니다. 

If you would like to convert an existing command into a queued command, simply implement the `Illuminate\Contracts\Queue\ShouldBeQueued` interface on the class manually. 만약 이미 생성한 커맨드를 큐 커맨드로 변환하고 싶다면 `Illuminate\Contracts\Queue\ShouldBeQueued` 인터페이스를 구현하도록 클래스에 수동으로 추가해주면 됩니다. It contains no methods, and merely serves as a "marker interface" for the dispatcher. 인터페이스에 어떤 메소드는 없고, 단지 디스패처에게 알리기 위한 “표시용 인터페이스” 역활을 합니다. 

Then, just write your command normally. 그 다음에는, 정상적으로 커맨드를 작성하면 됩니다. When you dispatch it to the bus that bus will automatically queue the command for background processing.  커맨드가 처리될 때 버스가 자동으로 해당 커맨드를 백그라운드 처리를 위해 큐에 추가합니다. It doesn't get any easier than that. 이보다 더 쉬울 순 없습니다.

For more information on interacting with queued commands, view the full [queue documentation](/docs/{{version}}/queues). 큐 커맨드의 상호 작용에 대한 보다 자세한 사항은 [큐 문서](/docs/{{version}}/queues)를 참고하십시오. 

<a name="command-pipeline"></a>
## Command Pipeline
## 커맨드 파이프라인

Before a command is dispatched to a handler, you may pass it through other classes in a "pipeline". 커맨드가 핸들러에서 처리되기 전에 “파이프라인” 안에서 다른 클래스로 전달할 수 있습니다. Command pipes work just like HTTP middleware, except for your commands! 커맨드 파이프는 여러분의 커맨드를 제외하고, HTTP 미들웨어 처럼 동작합니다. For example, a command pipe could wrap the entire command operation within a database transaction, or simply log its execution.  예를 들어 커맨드 파이프는 하나의 데이터 베이스 트랜잭션 안에서 전체 커맨드 연산을 랩핑(wrap) 할 수도 있고, 간단하게 실행을 로그로 남길 수도 있습니다. 

To add a pipe to your bus, call the `pipeThrough` method of the dispatcher from your `App\Providers\BusServiceProvider::boot` method: 버스에 파이프를 추가하려면 `App\Providers\BusServiceProvider::boot`메소드에서 디스패처의 `pipeThrough` 메소드를 호출하면 됩니다. 

	$dispatcher->pipeThrough(['UseDatabaseTransactions', 'LogCommand']);

A command pipe is defined with a `handle` method, just like a middleware:
커맨드 파이프는 미들웨어와 마찬가지로 `handle` 메소드를 정의합니다. 

	class UseDatabaseTransactions {

		public function handle($command, $next)
		{
			return DB::transaction(function() use ($command, $next)
			{
				return $next($command);
			});
		}

	}

Command pipe classes are resolved through the [IoC container](/docs/{{version}}/container), so feel free to type-hint any dependencies you need within their constructors. 커맨드 파이프 클래스는 [IoC 컨테이너](/docs/{{version}}/container)를 통해서 의존성이 해결되므로 생성자에서 타입힌트를 통한 의존성을 지정할 수 있습니다. 

You may even define a `Closure` as a command pipe: 커맨드 파이프에는 `Closure` 도 지정할 수 있습니다. 

	$dispatcher->pipeThrough([function($command, $next)
	{
		return DB::transaction(function() use ($command, $next)
		{
			return $next($command);
		});
	}]);
