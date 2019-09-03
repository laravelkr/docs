# Events
# 이벤트

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Registering Events & Listeners](#registering-events-and-listeners)
- [이벤트 & 리스너 등록하기](#registering-events-and-listeners)
    - [Generating Events & Listeners](#generating-events-and-listeners)
    - [이벤트 & 리스너 생성하기](#generating-events-and-listeners)
    - [Manually Registering Events](#manually-registering-events)
    - [수동으로 이벤트 등록하기](#manually-registering-events)
    - [Event Discovery](#event-discovery)
    - [이벤트 Discovery](#event-discovery)
- [Defining Events](#defining-events)
- [이벤트 정의하기](#defining-events)
- [Defining Listeners](#defining-listeners)
- [리스너 정의하기](#defining-listeners)
- [Queued Event Listeners](#queued-event-listeners)
- [Queue-큐로 처리하는 이벤트 리스너](#queued-event-listeners)
    - [Manually Accessing The Queue](#manually-accessing-the-queue)
    - [수동으로 Queue-큐에 엑세스하기](#manually-accessing-the-queue)
    - [Handling Failed Jobs](#handling-failed-jobs)
    - [실패한 job 처리하기](#handling-failed-jobs)
- [Dispatching Events](#dispatching-events)
- [이벤트 처리하기](#dispatching-events)
- [Event Subscribers](#event-subscribers)
- [이벤트 Subscribers](#event-subscribers)
    - [Writing Event Subscribers](#writing-event-subscribers)
    - [이벤트 Subscribers 작성하기](#writing-event-subscribers)
    - [Registering Event Subscribers](#registering-event-subscribers)
    - [이벤트 Subscribers 등록하기](#registering-event-subscribers)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel's events provide a simple observer implementation, allowing you to subscribe and listen for various events that occur in your application. Event classes are typically stored in the `app/Events` directory, while their listeners are stored in `app/Listeners`. Don't worry if you don't see these directories in your application, since they will be created for you as you generate events and listeners using Artisan console commands.

라라벨의 이벤트는 단순한 옵저버 구현을 제공하여, 애플리케이션에서 발행하는 다양한 이벤트에 대해 리스닝 할 수 있도록 해줍니다. 이벤트 클래스는 보통 `app/Events` 디렉토리에 저장되고 리스너 클래스들은 `app/Listeners`에 저장됩니다. 여러분의 애플리케이션에 해당 디렉토리가 보이지 않더라도 걱정하지 마십시오. 아티즌 콘솔 명령어를 통해서 이벤트와 리스너를 생성하면 해당 디렉토리가 새롭게 생겨날 것입니다.

Events serve as a great way to decouple various aspects of your application, since a single event can have multiple listeners that do not depend on each other. For example, you may wish to send a Slack notification to your user each time an order has shipped. Instead of coupling your order processing code to your Slack notification code, you can raise an `OrderShipped` event, which a listener can receive and transform into a Slack notification.

하나의 이벤트는 서로 의존하지 않는 여러개의 리스너를 가질 수 있으므로, 이를 통해서 애플리케이션의 다양한 레이어들이 서로 의존하지 않도록 만드는 멋진 방법을 제공할 수 있습니다. 예를 들어 주문에 대한 배송이 처리될 때마다 사용자에게 Slack으로 알림을 보내려고 할 수 있습니다. 슬랙에 알림을 보내는 코드를 주문 처리 부분에 결합하는 대신에, `OrderShipped` 이벤트를 발생시키고 리스너가 이를 받아 슬랙 알림으로 변환하도록 할 수 있습니다.

<a name="registering-events-and-listeners"></a>
## Registering Events & Listeners
## 이벤트 & 리스너 등록하기

The `EventServiceProvider` included with your Laravel application provides a convenient place to register all of your application's event listeners. The `listen` property contains an array of all events (keys) and their listeners (values). You may add as many events to this array as your application requires. For example, let's add a `OrderShipped` event:

라라벨 애플리케이션에 포함된 `EventServiceProvider`는 애플리케이션의 모든 이벤트 리스너들을 등록하는 편리한 장소 입니다. `listen` 속성은 모든 이벤트(키)와 리스너(값)의 배열을 가집니다. 애플리케이션이 필요로 하는 모든 이벤트들을 이 배열에 추가할 수 있습니다. 다음과 같이, 예를 들어 `OrderShipped ` 이벤트를 추가할 수 있습니다:

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'App\Events\OrderShipped' => [
            'App\Listeners\SendShipmentNotification',
        ],
    ];

<a name="generating-events-and-listeners"></a>
### Generating Events & Listeners
### 이벤트 & 리스너 생성하기

Of course, manually creating the files for each event and listener is cumbersome. Instead, add listeners and events to your `EventServiceProvider` and use the `event:generate` command. This command will generate any events or listeners that are listed in your `EventServiceProvider`. Events and listeners that already exist will be left untouched:

물론 각각의 이벤트와 리스너를의 파일을 일일히 생성하는 것은 불편한 일입니다. 대신에 `EventServiceProvider`에 리스너와 이벤트를 추가하고 `event:generate` 명령어를 사용하십시오. 이 명령어는 `EventServiceProvider`의 리스트에 포함된 이벤트나 리스너를 생성할 수 있습니다. 이미 존재하는 이벤트나 리스너는 영향을 받지 않을 것입니다:

    php artisan event:generate

<a name="manually-registering-events"></a>
### Manually Registering Events
### 수동으로 이벤트 등록하기

Typically, events should be registered via the `EventServiceProvider` `$listen` array; however, you may also register Closure based events manually in the `boot` method of your `EventServiceProvider`:

이벤트는 보통 `EventServiceProvider`의 `$listen` 배열을 통해 등록됩니다. 하지만 `EventServiceProvider`의 `boot` 메소드 안에서 클로저 기반의 이벤트를 수동으로 등록할 수도 있습니다:

    /**
     * Register any other events for your application.
     *
     * @return void
     */
    public function boot()
    {
        parent::boot();

        Event::listen('event.name', function ($foo, $bar) {
            //
        });
    }

#### Wildcard Event Listeners
#### 와일드 카드(여러개의 이벤트를 수신하는) 이벤트 리스너

You may even register listeners using the `*` as a wildcard parameter, allowing you to catch multiple events on the same listener. Wildcard listeners receive the event name as their first argument, and the entire event data array as their second argument:

`*`를 와일드카드 파라미터로 사용하여 리스너를 등록하면 동일한 리스너 에서 여러 개의 이벤트에 대응 할 수 있습니다. 와일드카드 리스너는 이벤트 이름을 첫번째 인자로, 전체 이벤트 데이터 배열을 두번째 인자로 받습니다:

    Event::listen('event.*', function ($eventName, array $data) {
        //
    });

<a name="event-discovery"></a>
### Event Discovery
### 이벤트 Discovery

> {note} Event Discovery is available for Laravel 5.8.9 or later.

> {note} 이벤트 Discovery는 Laravel 5.8.9 이상에서 사용할 수 있습니다.

Instead of registering events and listeners manually in the `$listen` array of the `EventServiceProvider`, you can enable automatic event discovery. When event discovery is enabled, Laravel will automatically find and register your events and listeners by scanning your application's `Listeners` directory. In addition, any explicitly defined events listed in the `EventServiceProvider` will still be registered.

`EventServiceProvider`의 `$listen` 배열에 이벤트와 리스너를 수동으로 등록하는 대신, 자동으로 이벤트 Discovery를 가능하게 할 수 있습니다. 이벤트 검색이 활성화되면 Laravel은 애플리케이션의 Listeners 디렉터리를 검색하여 이벤트와 리스너를 자동으로 찾아 등록합니다. 또한 `EventServiceProvider`에 명시적으로 정의 된 이벤트는 여전히 등록됩니다.

Laravel finds event listeners by scanning the listener classes using reflection. When Laravel finds any listener class method that begins with `handle`, Laravel will register those methods as event listeners for the event that is type-hinted in the method's signature:

Laravel은 리퍼러를 사용하여 리스너 클래스를 검색하여 이벤트 리스너를 찾습니다. Laravel이 `handle`로 시작하는 리스너 클래스 메소드를 발견하면, Laravel은 메소드의 시그니처에 타입 힌트 된 이벤트에 대한 이벤트 리스너로 해당 메소드를 등록합니다.

    use App\Events\PodcastProcessed;

    class SendPodcastProcessedNotification
    {
        /**
         * Handle the given event.
         *
         * @param  \App\Events\PodcastProcessed
         * @return void
         */
        public function handle(PodcastProcessed $event)
        {
            //
        }
    }

Event discovery is disabled by default, but you can enable it by overriding the `shouldDiscoverEvents` method of your application's `EventServiceProvider`:

이벤트 discovery는 기본적으로 비활성화되어 있지만, 애플리케이션의 `EventServiceProvider`의 `shouldDiscoverEvents` 메소드를 오버라이드하여 활성화시킬 수 있습니다 :

    /**
     * Determine if events and listeners should be automatically discovered.
     *
     * @return bool
     */
    public function shouldDiscoverEvents()
    {
        return true;
    }

By default, all listeners within your application's Listeners directory will be scanned. If you would like to define additional directories to scan, you may override the `discoverEventsWithin` method in your `EventServiceProvider`:

기본적으로 애플리케이션의 Listeners 디렉토리에 있는 모든 리스너가 검사됩니다. 검사 할 디렉토리를 추가로 정의하고 싶다면, `EventServiceProvider`에서 `discoverEventsWithin` 메소드를 오버라이드 할 수 있습니다 :

    /**
     * Get the listener directories that should be used to discover events.
     *
     * @return array
     */
    protected function discoverEventsWithin()
    {
        return [
            $this->app->path('Listeners'),
        ];
    }

In production, you likely do not want the framework to scan all of your listeners on every request. Therefore, during your deployment process, you should run the `event:cache` Artisan command to cache a manifest of all of your application's events and listeners. This manifest will be used by the framework to speed up the event registration process. The `event:clear` command may be used to destroy the cache.

프로덕션 환경에서는 프레임워크가 모든 요청에 대해 모든 리스너를 검색하지 않도록 할 가능성이 높습니다. 그러므로 배포 프로세스 중에 `event:cache` Artisan 명령을 실행하여 모든 애플리케이션 이벤트와 리스너의 목록을 캐시해야합니다. 이 매니페스트는 프레임워크에서 이벤트 등록 프로세스의 속도를 높이기 위해 사용됩니다. 캐시를 제거하기 위해 `event:clear` 명령을 사용할 수 있습니다.

> {tip} The `event:list` command may be used to display a list of all events and listeners registered by your application.

> {tip}`event : list` 명령은 애플리케이션에 등록 된 모든 이벤트와 리스너의 목록을 표시하는 데 사용될 수 있습니다.

<a name="defining-events"></a>
## Defining Events
## 이벤트 정의하기

An event class is a data container which holds the information related to the event. For example, let's assume our generated `OrderShipped` event receives an [Eloquent ORM](/docs/{{version}}/eloquent) object:

이벤트 클래스는 이벤트와 관련된 정보를 가지고 있는 데이터 컨테이너입니다. 예를 들어 `OrderShipped` 이벤트가 [Eloquent ORM](/docs/{{version}}/eloquent) 객체를 를 받는다고 해보겠습니다:

    <?php

    namespace App\Events;

    use App\Order;
    use Illuminate\Queue\SerializesModels;

    class OrderShipped
    {
        use SerializesModels;

        public $order;

        /**
         * Create a new event instance.
         *
         * @param  \App\Order  $order
         * @return void
         */
        public function __construct(Order $order)
        {
            $this->order = $order;
        }
    }

As you can see, this event class contains no logic. It is a container for the `Order` instance that was purchased. The `SerializesModels` trait used by the event will gracefully serialize any Eloquent models if the event object is serialized using PHP's `serialize` function.

보시다시피, 이 이벤트 클래스는 로직을 가지고 있지 않습니다. 이는 구매한 `Order` 인스턴스의 컨테이너입니다. 이벤트가 사용하는 `SerializesModels` 속성은 이벤트 객체가 PHP의 `serialize` 기능을 통해 serialize될 경우 Eloquent 모델도 serialize 하게 할 것입니다.

<a name="defining-listeners"></a>
## Defining Listeners
## 리스너 정의하기

Next, let's take a look at the listener for our example event. Event listeners receive the event instance in their `handle` method. The `event:generate` command will automatically import the proper event class and type-hint the event on the `handle` method. Within the `handle` method, you may perform any actions necessary to respond to the event:

다음으로, 이벤트 예제에 대한 리스너를 살펴보겠습니다. 이벤트 리스너는 `handle` 메소드에서 이벤트 인스턴스를 전달 받습니다. `event:generate` 커맨드는 자동으로 적절한 이벤트 클래스를 가져오고 `handle` 메소드에 이벤트를 타입힌트로 추가할 것입니다. `handle` 메소드 내에서는 이벤트에 대응하기 위한 그 어떤 액션이라도 구성할 수 있습니다:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;

    class SendShipmentNotification
    {
        /**
         * Create the event listener.
         *
         * @return void
         */
        public function __construct()
        {
            //
        }

        /**
         * Handle the event.
         *
         * @param  \App\Events\OrderShipped  $event
         * @return void
         */
        public function handle(OrderShipped $event)
        {
            // Access the order using $event->order...
        }
    }

> {tip} Your event listeners may also type-hint any dependencies they need on their constructors. All event listeners are resolved via the Laravel [service container](/docs/{{version}}/container), so dependencies will be injected automatically.

> {tip} 이벤트 리스너는 또한 생성자에서, 필요한 모든 의존성들을 타입힌트 할 수 있습니다. 모든 이벤트 리스너는 [서비스 컨테이너](/docs/{{version}}/container)를 통해 처리되기 때문에 의존성은 자동으로 주입됩니다:

#### Stopping The Propagation Of An Event
#### 이벤트 전달 중단하기

Sometimes, you may wish to stop the propagation of an event to other listeners. You may do so by returning `false` from your listener's `handle` method.

경우에 따라서 이벤트가 다른 리스너에게 전달되는 것을 중단 하기를 원할 수도 있습니다. 이러한 경우에는 리스너의 `handle` 메소드에서 `false`를 반환하면 됩니다.

<a name="queued-event-listeners"></a>
## Queued Event Listeners
## Queue-큐로 처리하는 이벤트 리스너

Queueing listeners can be beneficial if your listener is going to perform a slow task such as sending an e-mail or making an HTTP request. Before getting started with queued listeners, make sure to [configure your queue](/docs/{{version}}/queues) and start a queue listener on your server or local development environment.

Queue-큐를 통해서 처리하는 리스너는 만약 여러분의 리스너가 이메일을 보내거나 HTTP Request를 생성하는 오래 걸리는 작업을 수행할 때 아주 유용합니다. 큐를 통한 리스너에 대해서 알아보기 전에 [큐 설정하기](/docs/{{version}}/queues)를 확인하고 서버나 로컬 개발 환경에서 큐 리스너를 시작하십시오.

To specify that a listener should be queued, add the `ShouldQueue` interface to the listener class. Listeners generated by the `event:generate` Artisan command already have this interface imported into the current namespace, so you can use it immediately:

리스너를 큐로 처리하도록 지정하기 위해서는, 리스너 클래스에 `ShouldQueue` 인터페이스를 추가하면 됩니다. `event:generate` 아티즌 명령어를 통해서 생성된 리스너라면 이미 이 인터페이스가 현재의 네임 스페이스 아래에 삽입되어 있기 때문에 즉시 사용할 수 있습니다:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        //
    }

That's it! Now, when this listener is called for an event, it will be automatically queued by the event dispatcher using Laravel's [queue system](/docs/{{version}}/queues). If no exceptions are thrown when the listener is executed by the queue, the queued job will automatically be deleted after it has finished processing.

이게 다입니다! 이제 이 리스너가 이벤트를 통해 호출된다면 라라벨의 [Queue-큐 시스템](/docs/{{version}}/queues)을 이용하는 이벤트 dispatcher에 의해 자동으로 Queue-큐에 저장될 것입니다. Queue-큐를 통해서 리스너가 실행되고, 어떤 exception 도 발생하지 않았다면, Queue-큐에 저장된 작업은 실행이 종료된 뒤 자동으로 삭제될 것입니다.

#### Customizing The Queue Connection & Queue Name
#### Queue-큐 커넥션과 Queue-큐 이름 커스터마이징 하기

If you would like to customize the queue connection, queue name, or queue delay time of an event listener, you may define the `$connection`, `$queue`, or `$delay` properties on your listener class:

이벤트 리스너에서 사용되는 Queue-큐 커넥션과 Queue-큐의 이름을 커스터마이징 하려면, 리스너 클래스에 `$connection`, `$queue` 또는 `$delay` 속성을 정의하면 됩니다:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        /**
         * The name of the connection the job should be sent to.
         *
         * @var string|null
         */
        public $connection = 'sqs';

        /**
         * The name of the queue the job should be sent to.
         *
         * @var string|null
         */
        public $queue = 'listeners';

        /**
         * The time (seconds) before the job should be processed.
         *
         * @var int
         */
        public $delay = 60;
    }

#### Conditionally Queueing Listeners
#### 조건부 대기열-Queueing 리스너

Sometimes, you may need to determine whether a listener should be queued based on some data that's only available at runtime. To accomplish this, a `shouldQueue` method may be added to a listener to determine whether the listener should be queued and executed synchronously:

때로는 런타임에만 사용 가능한 일부 데이터를 기반으로 대기열-Queueing에 대기해야 하는지 여부를 결정해야 할 수도 있습니다. 이것을 달성하기 위해서, 리스너에 `shouldQueue` 메소드를 추가해, 리스너를 큐에 넣고 동기 실행해야 할 지 어떨지를 판단 할 수 있습니다.

    <?php

    namespace App\Listeners;

    use App\Events\OrderPlaced;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class RewardGiftCard implements ShouldQueue
    {
        /**
         * Reward a gift card to the customer.
         *
         * @param  \App\Events\OrderPlaced  $event
         * @return void
         */
        public function handle(OrderPlaced $event)
        {
            //
        }

        /**
         * Determine whether the listener should be queued.
         *
         * @param  \App\Events\OrderPlaced  $event
         * @return bool
         */
        public function shouldQueue(OrderPlaced $event)
        {
            return $event->order->subtotal >= 5000;
        }
    }

<a name="manually-accessing-the-queue"></a>
### Manually Accessing The Queue
### 수동으로 Queue-큐에 엑세스하기

If you need to manually access the listener's underlying queue job's `delete` and `release` methods, you may do so using the `Illuminate\Queue\InteractsWithQueue` trait. This trait is imported by default on generated listeners and provides access to these methods:

리스너에 의해서 실행되는 Queue-큐 작업의 `delete` 와 `release` 메소드에 수동으로 엑세스할 필요가 있다면, `Illuminate\Queue\InteractsWithQueue` 트레이트를 사용할 수 있습니다. 이 트레이트는 리스너에서 기본적으로 사용이 선언되어(imported) 있으며 다음의 메소드에 엑세스 할 수 있게 해줍니다:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        use InteractsWithQueue;

        /**
         * Handle the event.
         *
         * @param  \App\Events\OrderShipped  $event
         * @return void
         */
        public function handle(OrderShipped $event)
        {
            if (true) {
                $this->release(30);
            }
        }
    }

<a name="handling-failed-jobs"></a>
### Handling Failed Jobs
### 실패한 job 처리하기

Sometimes your queued event listeners may fail. If queued listener exceeds the maximum number of attempts as defined by your queue worker, the `failed` method will be called on your listener. The `failed` method receives the event instance and the exception that caused the failure:

때때로 큐를 통한 이벤트 리스너가 실패할 수도 있습니다. 큐를 통한 리스너가 큐 워커에 정의된 재시도 횟수를 넘게 되면, 리스너 클래스의 `failed` 메소드가 호출됩니다. `failed` 메소드는 이벤트 인스턴스와 실패를 발생시킨 예외-exception를 인자로 받습니다:

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        use InteractsWithQueue;

        /**
         * Handle the event.
         *
         * @param  \App\Events\OrderShipped  $event
         * @return void
         */
        public function handle(OrderShipped $event)
        {
            //
        }

        /**
         * Handle a job failure.
         *
         * @param  \App\Events\OrderShipped  $event
         * @param  \Exception  $exception
         * @return void
         */
        public function failed(OrderShipped $event, $exception)
        {
            //
        }
    }

<a name="dispatching-events"></a>
## Dispatching Events
## 이벤트 처리하기

To dispatch an event, you may pass an instance of the event to the `event` helper. The helper will dispatch the event to all of its registered listeners. Since the `event` helper is globally available, you may call it from anywhere in your application:

이벤트를 처리하기 위해서는 `event` 헬퍼 함수에 이벤트의 인스턴스를 전달하면 됩니다. 이 헬퍼는 이 이벤트를 수신하고 있는 리스너들에게 자동으로 전달될 것입니다. `event` 헬퍼함수는 글로벌 영역에서 사용할 수 있기 때문에, 애플리케이션의 어느곳에서나 호출할 수 있습니다:

    <?php

    namespace App\Http\Controllers;

    use App\Order;
    use App\Events\OrderShipped;
    use App\Http\Controllers\Controller;

    class OrderController extends Controller
    {
        /**
         * Ship the given order.
         *
         * @param  int  $orderId
         * @return Response
         */
        public function ship($orderId)
        {
            $order = Order::findOrFail($orderId);

            // Order shipment logic...

            event(new OrderShipped($order));
        }
    }

> {tip} When testing, it can be helpful to assert that certain events were dispatched without actually triggering their listeners. Laravel's [built-in testing helpers](/docs/{{version}}/mocking#event-fake) makes it a cinch.

> {tip} 테스트를 수행할 때에는 실제 이벤트 리스너를 실행하지 않고서도 해당 이벤트가 발생했는지 확인할 수 있습니다. 라라벨의 [내장된 테스팅 헬퍼](/docs/{{version}}/mocking#event-fake) 를 통해서 수행됩니다.

<a name="event-subscribers"></a>
## Event Subscribers
## 이벤트 Subscribers

<a name="writing-event-subscribers"></a>
### Writing Event Subscribers
### 이벤트 Subscribers 작성하기

Event subscribers are classes that may subscribe to multiple events from within the class itself, allowing you to define several event handlers within a single class. Subscribers should define a `subscribe` method, which will be passed an event dispatcher instance. You may call the `listen` method on the given dispatcher to register event listeners:

이벤트 Subscriber는 클래스 안에서 다수의 이벤트에 대한 subscribe를 처리할 수 있는 클래스이며, 하나의 클래스 안에서 여러 개의 이벤트 핸들러를 정의할 수 있습니다. Subscriber는 `subscribe` 메소드를 이용하여 이벤트 dispatcher 인스턴스를 전달받도록 정의되어야 합니다. 이벤트 리스너를 등록하기 위해서 주어진 dispatcher 에서 `listen` 메소드를 호출 할 수 있습니다:

    <?php

    namespace App\Listeners;

    class UserEventSubscriber
    {
        /**
         * Handle user login events.
         */
        public function handleUserLogin($event) {}

        /**
         * Handle user logout events.
         */
        public function handleUserLogout($event) {}

        /**
         * Register the listeners for the subscriber.
         *
         * @param  \Illuminate\Events\Dispatcher  $events
         */
        public function subscribe($events)
        {
            $events->listen(
                'Illuminate\Auth\Events\Login',
                'App\Listeners\UserEventSubscriber@handleUserLogin'
            );

            $events->listen(
                'Illuminate\Auth\Events\Logout',
                'App\Listeners\UserEventSubscriber@handleUserLogout'
            );
        }
    }

<a name="registering-event-subscribers"></a>
### Registering Event Subscribers
### 이벤트 Subscriber 등록하기

After writing the subscriber, you are ready to register it with the event dispatcher. You may register subscribers using the `$subscribe` property on the `EventServiceProvider`. For example, let's add the `UserEventSubscriber` to the list:

subscriber가 작성되었다면 이벤트 dispatcher와 함께 등록할 준비가 되었습니다. `EventServiceProvider`에 `$subscribe` 속성을 사용하여 subscriber를 등록할 수 있습니다. 예를 들어 리스트에 `UserEventListener`를 추가해 보겠습니다:

    <?php

    namespace App\Providers;

    use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;

    class EventServiceProvider extends ServiceProvider
    {
        /**
         * The event listener mappings for the application.
         *
         * @var array
         */
        protected $listen = [
            //
        ];

        /**
         * The subscriber classes to register.
         *
         * @var array
         */
        protected $subscribe = [
            'App\Listeners\UserEventSubscriber',
        ];
    }
