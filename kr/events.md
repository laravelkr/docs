# Events
# 이벤트

- [Basic Usage 기본적인 사용법](#basic-usage)
- [Queued Event Handlers 큐에 저장된 이벤트 핸들러](#queued-event-handlers)
- [Event Subscribers 이벤트 구독](#event-subscribers)

<a name="basic-usage"></a>
## Basic Usage
## 기본적인 사용법

The Laravel event facilities provides a simple observer implementation, allowing you to subscribe and listen for events in your application. 라라벨 이벤트 기능은 애플리케이션의 이벤트를 구독하고 수신 대기 할 수 있는 관찰자(옵저버) 구현을 제공합니다. Event classes are typically stored in the `app/Events` directory, while their handlers are stored in `app/Handlers/Events`. 일반적으로 이벤트 클래스는 `app/Events` 디렉토리에, 이벤트 핸들러는 `app/Handlers/Events` 디렉토리에 저장됩니다. 

You can generate a new event class using the Artisan CLI tool:
아티즌 CLI 툴을 사용해서 새로운 이벤트 클래스를 생성할 수 있습니다. 

	php artisan make:event PodcastWasPurchased

#### Subscribing To An Event
#### 이벤트 구독하기

The `EventServiceProvider` included with your Laravel application provides a convenient place to register all event handlers. 라라벨 애플리케이션에 포함되어 있는 `EventServiceProvider` 는 모든 이벤트 핸들러를 등록하는 편리한 방법을 제공합니다. The `listen` property contains an array of all events (keys) and their handlers (values). `listen` 프로퍼티는 이벤트(키)와 핸들러(값)으로 구성된 배열을 가지고 있습니다. Of course, you may add as many events to this array as your application requires. 여러분의 애플리케이션에서 필요로 하는 많은 이벤트들을 이 배열에 추가할 수 있습니다. For example, let's add our `PodcastWasPurchased` event: 예를 들어 `PodcastWasPurchased` 이벤트를 추가한다고 가정해 봅시다. 

	/**
	 * The event handler mappings for the application.
	 *
	 * @var array
	 */
	protected $listen = [
		'App\Events\PodcastWasPurchased' => [
			'App\Handlers\Events\EmailPurchaseConfirmation',
		],
	];

To generate a handler for an event, use the `handler:event` Artisan CLI command: 이벤트에 대한 핸들러를 생성하기 위해서는 아티즌 CLI 명령어 `handler:event`를 사용하십시오. 

	php artisan handler:event EmailPurchaseConfirmation --event=PodcastWasPurchased

Of course, manually running the `make:event` and `handler:event` commands each time you need a handler or event is cumbersome. 매번 핸들러나 이벤트가 필요할 때 직접 `make:event` 와 `handler:event` 명령어를 나누어 실행하는 것은 번거로운 일입니다. Instead, simply add handlers and events to your `EventServiceProvider` and use the `event:generate` command. 대신에 `EventServiceProvider`클래스에 핸들러와 이벤트를 추가하고 `event:generate` 명령어를 사용할 수도 있습니다. This command will generate any events or handlers that are listed in your `EventServiceProvider`: 이 명령어는 여러분의 `EventServiceProvider`에 나열된 이벤트와 핸들러를 생성해줄 것입니다. 

	php artisan event:generate

#### Firing An Event
#### 이벤트 발생시키기 

Now we are ready to fire our event using the `Event` facade:
이제 `Event` 파사드를 사용해서 이벤트를 발생시킬 수 있습니다. 

	$response = Event::fire(new PodcastWasPurchased($podcast));

The `fire` method returns an array of responses that you can use to control what happens next in your application. `fire` 메소드는 애플리케이션에서 다음에 일어날 일을 제어하는 데 사용할 수 있는 배열을 반환합니다.

You may also use the `event` helper to fire an event: 
이벤트를 발생시키는데에는 `event` 핼퍼 함수를 사용할 수도 있습니다. 

	event(new PodcastWasPurchased($podcast));

#### Closure Listeners
#### 클로저 리스너

You can even listen to events without creating a separate handler class at all. 이벤트를 처리하기 위해서 항상 별도의 핸들러 클래스를 생성할 필요는 없습니다. For example, in the `boot` method of your `EventServiceProvider`, you could do the following: 예를 들어 `EventServiceProvider`의 `boot` 메소드에서 다음처럼 지정 할 수도 있습니다. 

	Event::listen('App\Events\PodcastWasPurchased', function($event)
	{
		// Handle the event...
	});

#### Stopping The Propagation Of An Event
#### 이벤트 전달 중단하기

Sometimes, you may wish to stop the propagation of an event to other listeners. 경우에 따라서 이벤트가 다른 리스너에게 전달되는 것을 중지하기를 원할 수도 있습니다. You may do so using by returning `false` from your handler: 이러한 경우에는 핸들러에서 `false` 를 리턴하면 됩니다. 

	Event::listen('App\Events\PodcastWasPurchased', function($event)
	{
		// Handle the event...

		return false;
	});

<a name="queued-event-handlers"></a>
## Queued Event Handlers
## 큐에 저장된 이벤트 핸들러

Need to [queue](/docs/{{version}}/queues) an event handler? 이벤트 핸들러에 [큐](/docs/{{version}}/queues)가 필요한가요? It couldn't be any easier. 간단하게 할 수 있습니다. When generating the handler, simply use the `--queued` flag: 핸들러를 생성할 때 `--queued` 플래스를 사용하면 됩니다. 

	php artisan handler:event SendPurchaseConfirmation --event=PodcastWasPurchased --queued

This will generate a handler class that implements the `Illuminate\Contracts\Queue\ShouldBeQueued` interface. 이렇게 하면 핸들러 클래스는 `Illuminate\Contracts\Queue\ShouldBeQueued` 인터페이스의 구현 클래스가 됩니다. That's it! 이걸로 끝입니다. Now when this handler is called for an event, it will be queued automatically by the event dispatcher. 이제 핸들러가 이벤트에 의해서 호출될 때 이벤트 디스패처에 의해서 자동으로 큐에 지정됩니다. 

If no exceptions are thrown when the handler is executed by the queue, the queued job will be deleted automatically after it has processed. 큐에 의해서 실행되는 핸들러가 아무런 예외도 발생하지 않는다면 큐로 지정된 작업은 수행후 자동으로 삭제 됩니다. If you need to access the queued job's `delete` and `release` methods manually, you may do so. 수동으로 큐로 지정된 작업의 `delete` 와 `release` 메소드에 액세스 해야되는 경우, 여러분은 그렇게 할 수도 있습니다. The `Illuminate\Queue\InteractsWithQueue` trait, which is included by default on queued handlers, gives you access to these methods:  큐로 지정된 핸들러에  기본적으로 포함된 `Illuminate\Queue\InteractsWithQueue` trait 이 다음의 메소드에 엑세스 할 수 있게 해줍니다. 

	public function handle(PodcastWasPurchased $event)
	{
		if (true)
		{
			$this->release(30);
		}
	}

If you have an existing handler that you would like to convert to a queued handler, simply add the `ShouldBeQueued` interface to the class manually. 이미 생성된 핸들러를 큐로 지정된 핸들러로 변경해야 된다면 클래스에 수동으로 `ShouldBeQueued` 인터페이스 를 추가해주시면 됩니다. 

<a name="event-subscribers"></a>
## Event Subscribers
## 이벤트 구독 클래스 

#### Defining An Event Subscriber
#### 이벤트 구독 클래스 정의하기 

Event subscribers are classes that may subscribe to multiple events from within the class itself. 이벤트 구독 클래스는 많은 이벤트를 하나의 클래스 자체에서 구독하기 위한 클래스 입니다. Subscribers should define a `subscribe` method, which will be passed an event dispatcher instance: 구독 클래스는 이벤트 디스패처 인스턴스가 전달되는 `subscribe` 메소드를 정의해야 합니다. 

	class UserEventHandler {

		/**
		 * Handle user login events.
		 */
		public function onUserLogin($event)
		{
			//
		}

		/**
		 * Handle user logout events.
		 */
		public function onUserLogout($event)
		{
			//
		}

		/**
		 * Register the listeners for the subscriber.
		 *
		 * @param  Illuminate\Events\Dispatcher  $events
		 * @return void
		 */
		public function subscribe($events)
		{
			$events->listen('App\Events\UserLoggedIn', 'UserEventHandler@onUserLogin');

			$events->listen('App\Events\UserLoggedOut', 'UserEventHandler@onUserLogout');
		}

	}

#### Registering An Event Subscriber
#### 이벤트 구독 클래스 등록하기

Once the subscriber has been defined, it may be registered with the `Event` class. 이벤트 구독 클래스를 정의하고 나면, `Event` 클래스에 등록하면 됩니다. 

	$subscriber = new UserEventHandler;

	Event::subscribe($subscriber);

You may also use the [service container](/docs/{{version}}/container) to resolve your subscriber. [서비스 컨테이너](/docs/{{version}}/container)를 통해서 구독 클래스의 의존성 해결을 사용할 수 있습니다. To do so, simply pass the name of your subscriber to the `subscribe` method: 이 경우 간단하게 `subscribe` 메소드에 클래스 이름만 넣으면 됩니다. 

	Event::subscribe('UserEventHandler');

