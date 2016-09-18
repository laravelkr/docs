# 이벤트(Events)

- [기본적인 사용법](#basic-usage)
- [큐에 저장된 이벤트 핸들러](#queued-event-handlers)
- [이벤트 구독](#event-subscribers)

<a name="basic-usage"></a>
## 기본적인 사용법

라라벨 이벤트 기능은 어플리케이션의 이벤트를 구독하고 수신 대기 할 수 있는 관찰자(옵저버) 구현을 제공합니다. 일반적으로 이벤트 클래스는 `app/Events` 디렉토리에, 이벤트 핸들러는 `app/Handlers/Events` 디렉토리에 저장됩니다.

아티즌 CLI 툴을 사용해서 새로운 이벤트 클래스를 생성할 수 있습니다:

	php artisan make:event PodcastWasPurchased

#### 이벤트 구독하기

라라벨 어플리케이션에 포함되어 있는 `EventServiceProvider`는 모든 이벤트 핸들러를 등록하는 편리한 방법을 제공합니다. `listen` 프로퍼티는 이벤트(키)와 핸들러(값)으로 구성된 배열을 가지고 있습니다. 여러분의 어플리케이션에서 필요로 하는 많은 이벤트들을 이 배열에 추가할 수 있습니다. 예를 들어, `PodcastWasPurchased` 이벤트를 추가한다고 가정해 봅시다:

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

이벤트에 대한 핸들러를 생성하기 위해서는 아티즌 CLI 명령어 `handler:event`를 사용하십시오:

	php artisan handler:event EmailPurchaseConfirmation --event=PodcastWasPurchased

매번 핸들러나 이벤트가 필요할 때 직접 `make:event`와 `handler:event` 명령어를 나누어 실행하는 것은 번거로운 일입니다. 대신에 `EventServiceProvider`클래스에 핸들러와 이벤트를 추가하고 `event:generate` 명령어를 사용할 수도 있습니다. 이 명령어는 여러분의 `EventServiceProvider`에 나열된 이벤트와 핸들러를 생성해줄 것입니다:

	php artisan event:generate

#### 이벤트 발생시키기

이제 `Event` 파사드를 사용해서 이벤트를 발생시킬 수 있습니다:

	$response = Event::fire(new PodcastWasPurchased($podcast));

`fire` 메소드는 어플리케이션에서 다음에 일어날 일을 제어하는 데 사용할 수 있는 배열을 반환합니다.

이벤트를 발생시키는데에는 `event` 핼퍼 함수를 사용할 수도 있습니다:

	event(new PodcastWasPurchased($podcast));

#### 클로저 리스너

이벤트를 처리하기 위해서 항상 별도의 핸들러 클래스를 생성할 필요는 없습니다. 예를 들어, `EventServiceProvider`의 `boot` 메소드에서 다음처럼 지정 할 수도 있습니다:

	Event::listen('App\Events\PodcastWasPurchased', function($event)
	{
		// Handle the event...
	});

#### 이벤트 전달 중단하기

경우에 따라서 이벤트가 다른 리스너에게 전달되는 것을 중지하기를 원할 수도 있습니다. 이러한 경우에는 핸들러에서 `false`를 리턴하면 됩니다:

	Event::listen('App\Events\PodcastWasPurchased', function($event)
	{
		// Handle the event...

		return false;
	});

<!--chak-comment-이벤트(Events)-기본적인-사용법-->

<a name="queued-event-handlers"></a>
## 큐에 저장된 이벤트 핸들러

이벤트 핸들러에 [큐](/docs/5.0/queues)가 필요한가요? 간단하게 할 수 있습니다. 핸들러를 생성할 때 `--queued` 플래스를 사용하면 됩니다:

	php artisan handler:event SendPurchaseConfirmation --event=PodcastWasPurchased --queued

이렇게 하면 핸들러 클래스는 `Illuminate\Contracts\Queue\ShouldBeQueued` 인터페이스의 구현 클래스가 됩니다. 이걸로 끝입니다! 이제 핸들러가 이벤트에 의해서 호출될 때 이벤트 디스패처에 의해서 자동으로 큐에 지정됩니다.

큐에 의해서 실행되는 핸들러가 아무런 예외도 발생하지 않는다면 큐로 지정된 작업은 수행후 자동으로 삭제 됩니다. 수동으로 큐로 지정된 작업의 `delete`와 `release` 메소드에 액세스 해야되는 경우, 여러분은 그렇게 할 수도 있습니다. 큐로 지정된 핸들러에  기본적으로 포함된 `Illuminate\Queue\InteractsWithQueue` trait 이 다음의 메소드에 엑세스 할 수 있게 해줍니다:

	public function handle(PodcastWasPurchased $event)
	{
		if (true)
		{
			$this->release(30);
		}
	}

이미 생성된 핸들러를 큐로 지정된 핸들러로 변경해야 된다면 클래스에 수동으로 `ShouldBeQueued` 인터페이스를 추가해주시면 됩니다.

<!--chak-comment-이벤트(Events)-큐에-저장된-이벤트-핸들러-->

<a name="event-subscribers"></a>
## 이벤트 구독 클래스

#### 이벤트 구독 클래스 정의하기

이벤트 구독 클래스는 많은 이벤트를 하나의 클래스 자체에서 구독하기 위한 클래스입니다. 구독 클래스는 이벤트 디스패처 인스턴스가 전달되는 `subscribe` 메소드를 정의해야 합니다:

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

#### 이벤트 구독 클래스 등록하기

이벤트 구독 클래스를 정의하고 나면, `Event` 클래스에 등록하면 됩니다.

	$subscriber = new UserEventHandler;

	Event::subscribe($subscriber);

서비스 컨테이너를 통해서 구독 클래스의 의존성 해결을 사용할 수 있습니다. 이 경우 간단하게 `subscribe` 메소드에 클래스 이름만 넣으면 됩니다:

	Event::subscribe('UserEventHandler');

<!--chak-comment-이벤트(Events)-이벤트-구독-클래스-->
