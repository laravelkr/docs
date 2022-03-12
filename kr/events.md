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
    - [Manually Interacting With The Queue](#manually-interacting-with-the-queue)
    - [수동으로 Queue-큐 작업처리하기](#manually-interacting-with-the-queue)
    - [Queued Event Listeners & Database Transactions](#queued-event-listeners-and-database-transactions)
    - [Queue-큐로 처리되는 이벤트 리스너 & 데이터베이스 트랜젝션](#queued-event-listeners-and-database-transactions)
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

Laravel's events provide a simple observer pattern implementation, allowing you to subscribe and listen for various events that occur within your application. Event classes are typically stored in the `app/Events` directory, while their listeners are stored in `app/Listeners`. Don't worry if you don't see these directories in your application as they will be created for you as you generate events and listeners using Artisan console commands.

라라벨의 이벤트기능은 단순한 옵저버 패턴을 구현한 것이다. 애플리케이션에서 발행하는 다양한 이벤트를 수신할 수 있게 해준다. 이벤트 클래스는 보통 `app/Events` 디렉토리에 저장되고 리스너 클래스들은 `app/Listeners`에 저장됩니다. 여러분의 애플리케이션에 해당 디렉토리가 보이지 않더라도 걱정하지 마십시오. 아티즌 콘솔 명령어를 통해서 이벤트와 리스너를 생성하면 해당 디렉토리가 새롭게 생겨날 것입니다.

Events serve as a great way to decouple various aspects of your application, since a single event can have multiple listeners that do not depend on each other. For example, you may wish to send a Slack notification to your user each time an order has shipped. Instead of coupling your order processing code to your Slack notification code, you can raise an `App\Events\OrderShipped` event which a listener can receive and use to dispatch a Slack notification.

하나의 이벤트는 서로 의존하지 않는 여러개의 리스너를 가질 수 있으므로, 이를 통해서 애플리케이션의 다양한 레이어들이 서로 의존하지 않도록 만드는 멋진 방법을 제공할 수 있습니다. 예를 들어 주문에 대한 배송이 처리될 때마다 사용자에게 Slack으로 알림을 보내려고 할 수 있습니다. 슬랙에 알림을 보내는 코드를 주문 처리 부분에 결합하는 대신에, `App\Events\OrderShipped` 이벤트를 발생시키고 리스너가 이를 받아 슬랙 알림으로 변환하도록 할 수 있습니다.

<a name="registering-events-and-listeners"></a>
## Registering Events & Listeners
## 이벤트 & 리스너 등록하기

The `App\Providers\EventServiceProvider` included with your Laravel application provides a convenient place to register all of your application's event listeners. The `listen` property contains an array of all events (keys) and their listeners (values). You may add as many events to this array as your application requires. For example, let's add an `OrderShipped` event:

라라벨 애플리케이션에 포함된 `App\Providers\EventServiceProvider`는 애플리케이션의 모든 이벤트 리스너들을 등록하는 편리한 장소 입니다. `listen` 속성은 모든 이벤트(키)와 리스너(값)의 배열을 가집니다. 애플리케이션이 필요로 하는 모든 이벤트들을 이 배열에 추가할 수 있습니다. 다음과 같이, 예를 들어 `OrderShipped ` 이벤트를 추가할 수 있습니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        OrderShipped::class => [
            SendShipmentNotification::class,
        ],
    ];

> {tip} The `event:list` command may be used to display a list of all events and listeners registered by your application.

> {tip} `event:list` 명령어는 애플리케이션에 등록된 모든 이벤트와 리스너 목록을 확인하는데 사용할 수 있습니다.

<a name="generating-events-and-listeners"></a>
### Generating Events & Listeners
### 이벤트 & 리스너 생성하기

Of course, manually creating the files for each event and listener is cumbersome. Instead, add listeners and events to your `EventServiceProvider` and use the `event:generate` Artisan command. This command will generate any events or listeners that are listed in your `EventServiceProvider` that do not already exist:

물론 각각의 이벤트와 리스너 파일을 일일히 생성하는 것은 불편한 일입니다. 대신에 `EventServiceProvider`에 리스너와 이벤트를 추가하고 `event:generate` 아티즌 명령어를 사용하십시오. 이 명령어는 `EventServiceProvider`의 리스트에 포함된 이벤트트와 리스너 클래스를 생성해줍니다. 이미 존재하는 이벤트나 리스너는 그대로 유지합니다.

    php artisan event:generate

Alternatively, you may use the `make:event` and `make:listener` Artisan commands to generate individual events and listeners:

이방법 이외에도 `make:event` 또는 `make:listener` 아티즌 명령어를 사용해서 개별 이벤트와 리스너 클래스를 생성할 수도 있습니다.

    php artisan make:event PodcastProcessed

    php artisan make:listener SendPodcastNotification --event=PodcastProcessed

<a name="manually-registering-events"></a>
### Manually Registering Events
### 수동으로 이벤트 등록하기

Typically, events should be registered via the `EventServiceProvider` `$listen` array; however, you may also register class or closure based event listeners manually in the `boot` method of your `EventServiceProvider`:

이벤트는 보통 `EventServiceProvider`의 `$listen` 배열을 통해 등록됩니다. 하지만 `EventServiceProvider`의 `boot` 메소드 안에서 클래스나 클로저 기반의 이벤트를 수동으로 등록할 수도 있습니다.

    use App\Events\PodcastProcessed;
    use App\Listeners\SendPodcastNotification;
    use Illuminate\Support\Facades\Event;

    /**
     * Register any other events for your application.
     *
     * @return void
     */
    public function boot()
    {
        Event::listen(
            PodcastProcessed::class,
            [SendPodcastNotification::class, 'handle']
        );

        Event::listen(function (PodcastProcessed $event) {
            //
        });
    }

<a name="queuable-anonymous-event-listeners"></a>
#### Queueable Anonymous Event Listeners
#### 대기 가능한 익명 이벤트 리스너

When registering closure based event listeners manually, you may wrap the listener closure within the `Illuminate\Events\queueable` function to instruct Laravel to execute the listener using the [queue](/docs/{{version}}/queues):

클로저 기반의 이벤트 리스너를 수동으로 등록 할 때 `Illuminate\Events\queueable` 함수에 리스너 클로저-Closure를 등록하면, [queue](/docs/{{version}}/queues)를 사용해 리스너를 실행하도록 라라벨에 지시 할 수 있습니다.

    use App\Events\PodcastProcessed;
    use function Illuminate\Events\queueable;
    use Illuminate\Support\Facades\Event;

    /**
     * Register any other events for your application.
     *
     * @return void
     */
    public function boot()
    {
        Event::listen(queueable(function (PodcastProcessed $event) {
            //
        }));
    }

Like queued jobs, you may use the `onConnection`, `onQueue`, and `delay` methods to customize the execution of the queued listener:

큐로 처리되는 작업들처럼, 여러분은 `onConnection`, `onQueue`, 그리고 `delay` 메소드를 통해 큐잉된 리스너의 실행을 설정할 수 있습니다.

    Event::listen(queueable(function (PodcastProcessed $event) {
        //
    })->onConnection('redis')->onQueue('podcasts')->delay(now()->addSeconds(10)));

If you would like to handle anonymous queued listener failures, you may provide a closure to the `catch` method while defining the `queueable` listener:

만약 익명의 큐잉된 리스너의 실패에 대해 처리하고 싶다면, `queueable` 리스너를 정의하면서 `catch` 메소드에 클로져를 전달하면 됩니다. 이 클로저는 이벤트 인스턴스와 리스너의 실패가 담겨져 있는 `Throwable` 인스턴스를 전달받습니다.

    use App\Events\PodcastProcessed;
    use function Illuminate\Events\queueable;
    use Illuminate\Support\Facades\Event;
    use Throwable;

    Event::listen(queueable(function (PodcastProcessed $event) {
        //
    })->catch(function (PodcastProcessed $event, Throwable $e) {
        // The queued listener failed...
    }));

<a name="wildcard-event-listeners"></a>
#### Wildcard Event Listeners
#### 와일드 카드(여러개의 이벤트를 수신하는) 이벤트 리스너

You may even register listeners using the `*` as a wildcard parameter, allowing you to catch multiple events on the same listener. Wildcard listeners receive the event name as their first argument and the entire event data array as their second argument:

`*`를 와일드카드 파라미터로 사용하여 리스너를 등록하면 동일한 리스너 에서 여러 개의 이벤트에 대응 할 수 있습니다. 와일드카드 리스너는 이벤트 이름을 첫번째 인자로, 전체 이벤트 데이터 배열을 두번째 인자로 받습니다.

    Event::listen('event.*', function ($eventName, array $data) {
        //
    });

<a name="event-discovery"></a>
### Event Discovery
### 이벤트클래스 탐색(Discovery)

Instead of registering events and listeners manually in the `$listen` array of the `EventServiceProvider`, you can enable automatic event discovery. When event discovery is enabled, Laravel will automatically find and register your events and listeners by scanning your application's `Listeners` directory. In addition, any explicitly defined events listed in the `EventServiceProvider` will still be registered.

`EventServiceProvider`의 `$listen` 배열에 이벤트와 리스너를 수동으로 등록하는 대신, 자동으로 이벤트 클래스를 탐색(Discovery)하도록 할 수 있습니다. 이벤트 클래스 탐색(Discovery)이 활성화되면 라라벨은 애플리케이션의 Listeners 디렉터리를 검색하여 이벤트와 리스너를 자동으로 찾아 등록합니다. 또한 `EventServiceProvider`에 명시적으로 정의 된 이벤트는 여전히 등록됩니다.

Laravel finds event listeners by scanning the listener classes using PHP's reflection services. When Laravel finds any listener class method that begins with `handle`, Laravel will register those methods as event listeners for the event that is type-hinted in the method's signature:

라라벨은 PHP의 리플렉션 서비스를 사용하여 리스너 클래스를 스캔하고, 이벤트 리스너를 찾습니다. 라라벨은 `handle`로 시작하는 리스너 클래스 메소드를 발견하면, 메소드의 시그니처에 타입 힌트 된 이벤트 클래스를 확인하고 이벤트 리스너로 해당 메소드를 등록합니다.

    use App\Events\PodcastProcessed;

    class SendPodcastNotification
    {
        /**
         * Handle the given event.
         *
         * @param  \App\Events\PodcastProcessed  $event
         * @return void
         */
        public function handle(PodcastProcessed $event)
        {
            //
        }
    }

Event discovery is disabled by default, but you can enable it by overriding the `shouldDiscoverEvents` method of your application's `EventServiceProvider`:

이벤트 탐색(Discovery) 기능은 기본적으로 비활성화되어 있지만, 애플리케이션의 `EventServiceProvider`의 `shouldDiscoverEvents` 메소드를 오버라이드하여 활성화시킬 수 있습니다.

    /**
     * Determine if events and listeners should be automatically discovered.
     *
     * @return bool
     */
    public function shouldDiscoverEvents()
    {
        return true;
    }

By default, all listeners within your application's `app/Listeners` directory will be scanned. If you would like to define additional directories to scan, you may override the `discoverEventsWithin` method in your `EventServiceProvider`:

기본적으로 애플리케이션의 `app/Listeners` 디렉토리에서 리스너 클래스가 있는지 확인합니다. 검사 할 디렉토리를 추가로 정의하고 싶다면, `EventServiceProvider`에서 `discoverEventsWithin` 메소드를 오버라이드 하면됩니다.

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

<a name="event-discovery-in-production"></a>
#### Event Discovery In Production
#### 프로덕션 환경에서의 이벤트클래스 탐색(Discovery)

In production, it is not efficient for the framework to scan all of your listeners on every request. Therefore, during your deployment process, you should run the `event:cache` Artisan command to cache a manifest of all of your application's events and listeners. This manifest will be used by the framework to speed up the event registration process. The `event:clear` command may be used to destroy the cache.

프로덕션 환경에서는 프레임워크가 모든 요청을 처리할 때마다 이벤트와 리스너 클래스를 탐색하는 것은 비효율적입니다. 그러므로 배포 프로세스에서 `event:cache` 아티즌 명령어를 실행하여 모든 애플리케이션 이벤트와 리스너의 목록을 캐싱하는 것이 좋습니다. 이 매니페스트는 프레임워크에서 이벤트 등록 프로세스의 속도를 높이기 위해 사용됩니다. 캐시를 제거하기 위해 `event:clear` 명령을 사용할 수 있습니다. 

<a name="defining-events"></a>
## Defining Events
## 이벤트 정의하기

An event class is essentially a data container which holds the information related to the event. For example, let's assume an `App\Events\OrderShipped` event receives an [Eloquent ORM](/docs/{{version}}/eloquent) object:

이벤트 클래스는 이벤트와 관련된 정보를 담고 있는 핵심적인 데이터 컨테이너입니다. 예를 들어 `App\Events\OrderShipped` 이벤트가 [Eloquent ORM](/docs/{{version}}/eloquent) 객체를 전달받는다고 가정해보겠습니다.

    <?php

    namespace App\Events;

    use App\Models\Order;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Foundation\Events\Dispatchable;
    use Illuminate\Queue\SerializesModels;

    class OrderShipped
    {
        use Dispatchable, InteractsWithSockets, SerializesModels;

        /**
         * The order instance.
         *
         * @var \App\Models\Order
         */
        public $order;

        /**
         * Create a new event instance.
         *
         * @param  \App\Models\Order  $order
         * @return void
         */
        public function __construct(Order $order)
        {
            $this->order = $order;
        }
    }

As you can see, this event class contains no logic. It is a container for the `App\Models\Order` instance that was purchased. The `SerializesModels` trait used by the event will gracefully serialize any Eloquent models if the event object is serialized using PHP's `serialize` function, such as when utilizing [queued listeners](#queued-event-listeners).

위에서 볼 수 있듯이, 이 이벤트 클래스는 별다른 로직을 가지고 있지 않습니다. 이 클래스는 구매한 주문-`App\Models\Order` 인스턴스를 담고 있는 컨테이너입니다. 이벤트가 사용하고 있는 `SerializesModels` 트레이트-Trait 은 큐로 처리되는 이벤트 리스너를 사용할 때와 같이 이벤트 객체가 PHP의 `serialize` 기능을 사용해 직렬화(Serialize)할 경우 엘로퀀트 모델도 직렬화 할 수 있게 해줍니다. 

<a name="defining-listeners"></a>
## Defining Listeners
## 리스너 정의하기

Next, let's take a look at the listener for our example event. Event listeners receive event instances in their `handle` method. The `event:generate` and `make:listener` Artisan commands will automatically import the proper event class and type-hint the event on the `handle` method. Within the `handle` method, you may perform any actions necessary to respond to the event:

다음으로, 이벤트 예제를 위한 리스너 클래스를 살펴보겠습니다. 이벤트 리스너는 `handle` 메소드에서 이벤트 인스턴스를 전달 받습니다. `event:generate` 와 `make:listener` 아티즌 명령어는 자동으로 적절한 이벤트 클래스를 확인해 `handle` 메소드에 이벤트를 타입힌트로 추가합니다. `handle` 메소드 안에는 발생한 이벤트를 처리하는 로직을 작성할 수 있습니다.

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

> {tip} 이벤트 리스너는 또한 생성자에서, 필요한 모든 의존성들을 타입힌트 할 수 있습니다. 모든 이벤트 리스너는 [서비스 컨테이너](/docs/{{version}}/container)를 통해 처리되기 때문에 의존성은 자동으로 주입됩니다.

<a name="stopping-the-propagation-of-an-event"></a>
#### Stopping The Propagation Of An Event
#### 이벤트 전달 중단하기

Sometimes, you may wish to stop the propagation of an event to other listeners. You may do so by returning `false` from your listener's `handle` method.

경우에 따라서 이벤트가 다른 리스너에게 전달되는 것을 중단 하기를 원할 수도 있습니다. 이러한 경우에는 리스너의 `handle` 메소드에서 `false`를 반환하면 됩니다.

<a name="queued-event-listeners"></a>
## Queued Event Listeners
## Queue-큐로 처리하는 이벤트 리스너

Queueing listeners can be beneficial if your listener is going to perform a slow task such as sending an email or making an HTTP request. Before using queued listeners, make sure to [configure your queue](/docs/{{version}}/queues) and start a queue worker on your server or local development environment.

만약 여러분의 리스너가 이메일을 보내거나 HTTP Request를 생성하는 오래 걸리는 작업을 수행할 때에는 Queue-큐를 통해서 처리하는 리스너가 아주 유용합니다. 큐를 통한 리스너를 사용하기 전에 [큐 설정하기](/docs/{{version}}/queues)를 확인하고 서버나 로컬 개발 환경에서 큐 리스너를 시작하십시오.

To specify that a listener should be queued, add the `ShouldQueue` interface to the listener class. Listeners generated by the `event:generate` and `make:listener` Artisan commands already have this interface imported into the current namespace so you can use it immediately:

리스너를 큐로 처리하도록 지정하기 위해서는, 리스너 클래스에 `ShouldQueue` 인터페이스를 추가하면 됩니다. `event:generate`와 `make:listener` 아티즌 명령어를 통해서 생성된 리스너라면 이미 이 인터페이스가 현재의 네임 스페이스 아래에 삽입되어 있기 때문에 즉시 사용할 수 있습니다.

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        //
    }

That's it! Now, when an event handled by this listener is dispatched, the listener will automatically be queued by the event dispatcher using Laravel's [queue system](/docs/{{version}}/queues). If no exceptions are thrown when the listener is executed by the queue, the queued job will automatically be deleted after it has finished processing.

이게 다입니다. 이제 리스너를 통해서 이벤트가 처리될 때 리스너는 라라벨의 [Queue-큐 시스템](/docs/{{version}}/queues)을 이용하는 이벤트 디스패처에 의해서 자동으로 Queue-큐에 저장됩니다. 큐-queue를 통해서 리스너가 실행되고, 예외-exception 이 발생하지 않는다면 큐-queue 에 저장된 작업은 실행이 완료된 뒤 자동으로 삭제됩니다. 

#### Customizing The Queue Connection & Queue Name
#### Queue-큐 커넥션과 Queue-큐 이름 커스터마이징 하기

If you would like to customize the queue connection, queue name, or queue delay time of an event listener, you may define the `$connection`, `$queue`, or `$delay` properties on your listener class:

이벤트 리스너에서 사용되는 Queue-큐 커넥션과 Queue-큐의 이름을 커스터마이징 하려면, 리스너 클래스에 `$connection`, `$queue` 또는 `$delay` 속성을 정의하면 됩니다.

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

If you would like to define the listener's queue connection or queue name at runtime, you may define `viaConnection` or `viaQueue` methods on the listener:

런타임에 리스너의 큐 커넥션이나 큐 이름을 정의하고 싶다면, 리스너의 `viaConnection` 이나 `viaQueue` 를 정의하면 됩니다.  

    /**
     * Get the name of the listener's queue connection.
     *
     * @return string
     */
    public function viaConnection()
    {
        return 'sqs';
    }

    /**
     * Get the name of the listener's queue.
     *
     * @return string
     */
    public function viaQueue()
    {
        return 'listeners';
    }

<a name="conditionally-queueing-listeners"></a>
#### Conditionally Queueing Listeners
#### 조건부 대기열-Queueing 리스너

Sometimes, you may need to determine whether a listener should be queued based on some data that are only available at runtime. To accomplish this, a `shouldQueue` method may be added to a listener to determine whether the listener should be queued. If the `shouldQueue` method returns `false`, the listener will not be executed:

때로는 런타임에만 사용 가능한 일부 데이터를 기반으로 대기열-Queueing에 대기해야 하는지 여부를 결정해야 할 수도 있습니다. 이것을 달성하기 위해서, 리스너에 `shouldQueue` 메소드를 추가해, 리스너를 큐에 넣어야하는지 여부를 판단 할 수 있습니다. `shouldQueue` 메소드가 `false`를 반환하면 리스너가 실행되지 않습니다.

    <?php

    namespace App\Listeners;

    use App\Events\OrderCreated;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class RewardGiftCard implements ShouldQueue
    {
        /**
         * Reward a gift card to the customer.
         *
         * @param  \App\Events\OrderCreated  $event
         * @return void
         */
        public function handle(OrderCreated $event)
        {
            //
        }

        /**
         * Determine whether the listener should be queued.
         *
         * @param  \App\Events\OrderCreated  $event
         * @return bool
         */
        public function shouldQueue(OrderCreated $event)
        {
            return $event->order->subtotal >= 5000;
        }
    }

<a name="manually-interacting-with-the-queue"></a>
### Manually Interacting With The Queue
### 수동으로 Queue-큐 작업처리하기

If you need to manually access the listener's underlying queue job's `delete` and `release` methods, you may do so using the `Illuminate\Queue\InteractsWithQueue` trait. This trait is imported by default on generated listeners and provides access to these methods:

리스너에 의해서 실행되는 Queue-큐 작업의 `delete` 와 `release` 메소드에 수동으로 엑세스할 필요가 있다면, `Illuminate\Queue\InteractsWithQueue` 트레이트를 사용할 수 있습니다. 이 트레이트는 리스너에서 기본적으로 사용이 선언되어(imported) 있으며 다음의 메소드에 엑세스 할 수 있게 해줍니다.

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;

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

<a name="queued-event-listeners-and-database-transactions"></a>
### Queued Event Listeners & Database Transactions
### Queue-큐로 처리되는 이벤트 리스너 & 데이터베이스 트랜젝션

When queued listeners are dispatched within database transactions, they may be processed by the queue before the database transaction has committed. When this happens, any updates you have made to models or database records during the database transaction may not yet be reflected in the database. In addition, any models or database records created within the transaction may not exist in the database. If your listener depends on these models, unexpected errors can occur when the job that dispatches the queued listener is processed.

큐 대기열에 있는 리스너가 데이터베이스 트랜젝션 안에서 처리를 시도하는 경우, 데이터베이스 트랜잭션이 커밋되기 전에 큐에 의해서 처리될 수 있습니다. 이 경우 데이터베이스 트랜잭션에서 수행된 모델 또는 데이터베이스 레코드의 변경사항이 아직 실제 데이터베이스에 반영되지 않을 수 있습니다. 또한 트랜잭션 안에서 생성된 모델 또는 데이터베이스 레코드는 실제 데이터베이스 안에 존재하지 않을 수 있습니다. 만약 리스너가 이 모델에 의존하게 된다면 큐에 있는 리스너가 처리될 때 예기치 못한 에러가 발생할 수 있습니다.   

If your queue connection's `after_commit` configuration option is set to `false`, you may still indicate that a particular queued listener should be dispatched after all open database transactions have been committed by defining an `$afterCommit` property on the listener class:

만약 큐 연결의 `after_comit` 설정값이 `false` 로 지정되어 있다면, 리스너 클래스의 `$afterCommit` 속성값을 정의해서 트랜잭션이 커밋된 이후에 큐에 들어 있는 리스너가 처리되어야 함을 알려줄 수 있습니다.  

    <?php

    namespace App\Listeners;

    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        use InteractsWithQueue;

        public $afterCommit = true;
    }

> {tip} To learn more about working around these issues, please review the documentation regarding [queued jobs and database transactions](/docs/{{version}}/queues#jobs-and-database-transactions).

> {tip} 이 이슈에 대한 보다 자세한 내용은 [큐를 통해 처리되는 작업과 데이터베이스 트랜잭션](/docs/{{version}}/queues#jobs-and-database-transactions) 문서를 확인하십시오.

<a name="handling-failed-jobs"></a>
### Handling Failed Jobs
### 실패한 job 처리하기

Sometimes your queued event listeners may fail. If queued listener exceeds the maximum number of attempts as defined by your queue worker, the `failed` method will be called on your listener. The `failed` method receives the event instance and the `Throwable` that caused the failure:

때때로 큐를 통한 이벤트 리스너가 실패할 수도 있습니다. 큐를 통한 리스너가 큐 워커에 정의된 재시도 횟수를 넘게 되면, 리스너 클래스의 `failed` 메소드가 호출됩니다. `failed` 메소드는 이벤트 인스턴스와 실패를 발생시킨 `Throwable` 인스턴스를 인자로 받습니다.

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;

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
         * @param  \Throwable  $exception
         * @return void
         */
        public function failed(OrderShipped $event, $exception)
        {
            //
        }
    }

<a name="specifying-queued-listener-maximum-attempts"></a>
#### Specifying Queued Listener Maximum Attempts
#### 큐로 처리되는 리스너의 최대 시도 횟수 지정하기

If one of your queued listeners is encountering an error, you likely do not want it to keep retrying indefinitely. Therefore, Laravel provides various ways to specify how many times or for how long a listener may be attempted.

큐를 통해서 처리되는 리스너에서 에러가 발생하면 계속해서 재시도하기를 원하지는 않을 것입니다. 그래서 라라벨에서는 리스너가 시도할 수 있는 최대 횟수를 지정하는 방법을 제공합니다.

You may define `$tries` property on your listener class to specify how many times the listener may be attempted before it is considered to have failed:

리스너 클래스의 `$tries` 속성은 리스너가 큐를 통해서 처리될 때 에러가 발생할 때 시도할 수 있는 최대 횟수를 지정할 수 있습니다.

    <?php

    namespace App\Listeners;

    use App\Events\OrderShipped;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;

    class SendShipmentNotification implements ShouldQueue
    {
        use InteractsWithQueue;

        /**
         * The number of times the queued listener may be attempted.
         *
         * @var int
         */
        public $tries = 5;
    }

As an alternative to defining how many times a listener may be attempted before it fails, you may define a time at which the listener should no longer be attempted. This allows a listener to be attempted any number of times within a given time frame. To define the time at which a listener should no longer be attempted, add a `retryUntil` method to your listener class. This method should return a `DateTime` instance:

횟수를 지정하는 대신에 리스너가 시도되는 시간을 정의할 수도 있습니다. 이 경우 리스너가 주어진 시간동안 성공할 때까지 여러번 시도될 수 있습니다. 리스너가 성공할 때까지 재시도할 시간값을 정의하려면 리스너 클래스에 `retryUntil` 메서드를 추가하면 됩니다. 이 메서드는 `DateTime` 인스턴스를 반환해야 합니다.  

    /**
     * Determine the time at which the listener should timeout.
     *
     * @return \DateTime
     */
    public function retryUntil()
    {
        return now()->addMinutes(5);
    }

<a name="dispatching-events"></a>
## Dispatching Events
## 이벤트 처리하기

To dispatch an event, you may call the static `dispatch` method on the event. This method is made available on the event by the `Illuminate\Foundation\Events\Dispatchable` trait. Any arguments passed to the `dispatch` method will be passed to the event's constructor:

이벤트를 처리하기 위해서는 이벤트 클래스의 `dispatch` static 메서드를 호출하면 됩니다. `Illuminate\Foundation\Events\Dispatchable` 트레이트가 이 메서드를 제공합니다. `dispatch` 메서드에 전달되는 인자는 이벤트의 생성자로 전달됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Events\OrderShipped;
    use App\Http\Controllers\Controller;
    use App\Models\Order;
    use Illuminate\Http\Request;

    class OrderShipmentController extends Controller
    {
        /**
         * Ship the given order.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $order = Order::findOrFail($request->order_id);

            // Order shipment logic...

            OrderShipped::dispatch($order);
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

Event subscribers are classes that may subscribe to multiple events from within the subscriber class itself, allowing you to define several event handlers within a single class. Subscribers should define a `subscribe` method, which will be passed an event dispatcher instance. You may call the `listen` method on the given dispatcher to register event listeners:

이벤트 Subscriber는 클래스 안에서 다수의 이벤트에 대한 구독을 처리할 수 있는 구독자(subscriber) 클래스이며, 하나의 클래스 안에서 여러 개의 이벤트 핸들러를 정의할 수 있습니다. Subscriber는 `subscribe` 메소드를 이용하여 이벤트 dispatcher 인스턴스를 전달받도록 정의되어야 합니다. 이벤트 리스너를 등록하기 위해서 주어진 dispatcher 에서 `listen` 메소드를 호출 할 수 있습니다.

    <?php

    namespace App\Listeners;

    use Illuminate\Auth\Events\Login;
    use Illuminate\Auth\Events\Logout;

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
         * @return void
         */
        public function subscribe($events)
        {
            $events->listen(
                Login::class,
                [UserEventSubscriber::class, 'handleUserLogin']
            );

            $events->listen(
                Logout::class,
                [UserEventSubscriber::class, 'handleUserLogout']
            );
        }
    }

If your event listener methods are defined within the subscriber itself, you may find it more convenient to return an array of events and method names from the subscriber's `subscribe` method. Laravel will automatically determine the subscriber's class name when registering the event listeners:

이벤트 리스너 메서드가 구독자-subscriber 안에 정의되어 있는 경우, `subscribe` 메서드에서 이벤트와 메서드 이름으로 매핑된 배열을 반환하는 것이 더 편리할 수 있습니다. 라라벨은 이벤트 리스너를 등록할 때 구독자-Subscriber 클래스 이름을 자동으로 결정합니다. 

    <?php

    namespace App\Listeners;

    use Illuminate\Auth\Events\Login;
    use Illuminate\Auth\Events\Logout;

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
         * @return array
         */
        public function subscribe($events)
        {
            return [
                Login::class => 'handleUserLogin',
                Logout::class => 'handleUserLogout',
            ];
        }
    }

<a name="registering-event-subscribers"></a>
### Registering Event Subscribers
### 이벤트 Subscriber 등록하기

After writing the subscriber, you are ready to register it with the event dispatcher. You may register subscribers using the `$subscribe` property on the `EventServiceProvider`. For example, let's add the `UserEventSubscriber` to the list:

subscriber가 작성되었다면 이벤트 dispatcher와 함께 등록할 준비가 되었습니다. `EventServiceProvider`에 `$subscribe` 속성을 사용하여 subscriber를 등록할 수 있습니다. 예를 들어 리스트에 `UserEventListener`를 추가해 보겠습니다.

    <?php

    namespace App\Providers;

    use App\Listeners\UserEventSubscriber;
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
            UserEventSubscriber::class,
        ];
    }
