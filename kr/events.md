# Events
# Events

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Registering Events / Listeners](#registering-events-and-listeners)
- [Event와 / Listener 등록하기](#registering-events-and-listeners)
- [Defining Events](#defining-events)
- [Event 정의하기](#defining-events)
- [Defining Listeners](#defining-listeners)
- [Listener 정의하기](#defining-listeners)
    - [Queued Event Listeners](#queued-event-listeners)
    - [큐에 저장된 이벤트 핸들러](#queued-event-listeners)
- [Firing Events](#firing-events)
- [이벤트 수행하기](#firing-events)
- [Broadcasting Events](#broadcasting-events)
- [이벤트 Broadcasting](#broadcasting-events)
    - [Configuration](#broadcast-configuration)
    - [설정하기](#broadcast-configuration)
    - [Marking Events For Broadcast](#marking-events-for-broadcast)
    - [Broadcast를 위해 이벤트 표시하기](#marking-events-for-broadcast)
    - [Broadcast Data](#broadcast-data)
    - [Broadcast Data](#broadcast-data)
    - [Event Broadcasting Customizations](#event-broadcasting-customizations)
    - [이벤트 Broadcasting 커스터마이징 하기](#event-broadcasting-customizations)
    - [Consuming Event Broadcasts](#consuming-event-broadcasts)
    - [Event Broadcasts 이용하기](#consuming-event-broadcasts)
- [Event Subscribers](#event-subscribers)
- [Event Subscribers](#event-subscribers)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel's events provides a simple observer implementation, allowing you to subscribe and listen for events in your application. Event classes are typically stored in the `app/Events` directory, while their listeners are stored in `app/Listeners`.

라라벨의 이벤트는 단순한 옵저버 구현을 제공하여, 애플리케이션을 구독하고 이벤트에 대해 리스닝 할 수 있도록 해줍니다. 이벤트 클래스는 보통 `app/Events` 디렉토리에 저장되고 리스너 클래스들은 `app/Listeners`에 저장됩니다. 

<a name="registering-events-and-listeners"></a>
## Registering Events / Listeners
## Events / Listeners 등록하기

The `EventServiceProvider` included with your Laravel application provides a convenient place to register all event listeners. The `listen` property contains an array of all events (keys) and their listeners (values). Of course, you may add as many events to this array as your application requires. For example, let's add our `PodcastWasPurchased` event:

라라벨 애플리케이션에 포함된 `EventServiceProvider`는 모든 이벤트 listener들을 등록하는 편리한 장소를 제공합니다. `listen` 속성은 모든 이벤트(키)와 listener들(값)의 배열을 가집니다. 당연하게도, 애플리케이션이 필요로 하는 모든 이벤트들을 이 배열에 추가할 수 있습니다. 다음과 같이, 예를 들어 `PodcastWasPurchased` 이벤트를 추가할 수 있습니다:

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'App\Events\PodcastWasPurchased' => [
            'App\Listeners\EmailPurchaseConfirmation',
        ],
    ];

### Generating Event / Listener Classes
### Generating Event / Listener Classes

Of course, manually creating the files for each event and listener is cumbersome. Instead, simply add listeners and events to your `EventServiceProvider` and use the `event:generate` command. This command will generate any events or listeners that are listed in your `EventServiceProvider`. Of course, events and listeners that already exist will be left untouched:

물론 각각의 이벤트와 listener를의 파일을 일일히 생성하는 것은 불편한 일입니다. 대신 단순히 `EventServiceProvider`에 listener와 이벤트를 추가하고 `event:generate` 명령어를 사용하십시오. 이 명령어는 `EventServiceProvider`의 리스트에 포함된 이벤트나 listener를 생성할 수 있습니다. 물론 이미 존재하는 이벤트나 listener는 영향을 받지 않을 것입니다: 

    php artisan event:generate

### Registering Events Manually
### 수동으로 이벤트 등록하기

Typically, events should be registered via the `EventServiceProvider` `$listen` array; however, you may also register events manually with the event dispatcher using either the `Event` facade or the `Illuminate\Contracts\Events\Dispatcher` contract implementation:

이벤트는 보통 `EventServiceProvider`의 `$listen` 배열을 통해 등록됩니다. 하지만 `Event` 파사드나 `Illuminate\Contracts\Events\Dispatcher` contract의 구현을 이용해 이벤트 dispatcher에 이벤트를 수동으로 등록할 수도 있습니다: 

    /**
     * Register any other events for your application.
     *
     * @param  \Illuminate\Contracts\Events\Dispatcher  $events
     * @return void
     */
    public function boot(DispatcherContract $events)
    {
        parent::boot($events);

        $events->listen('event.name', function ($foo, $bar) {
            //
        });
    }

#### Wildcard Event Listeners
#### Wildcard Event Listeners

You may even register listeners using the `*` as a wildcard, allowing you to catch multiple events on the same listener. Wildcard listeners receive the entire event data array as a single argument:

`*`를 와일드카드로 사용하여 listener를 등록하면 동일한 listener 에서 여러 개의 이벤트에 대응 할 수 있습니다. 와일드카드 listener는 전체 이벤트 데이터 배열을 하나의 인자로 받아들입니다: 

    $events->listen('event.*', function (array $data) {
        //
    });

<a name="defining-events"></a>
## Defining Events
## 이벤트 정의하기

An event class is simply a data container which holds the information related to the event. For example, let's assume our generated `PodcastWasPurchased` event receives an [Eloquent ORM](/docs/{{version}}/eloquent) object:

이벤트 클래스는 단순히 이벤트와 관련된 정보를 가지고 있는 데이터 컨테이너입니다. 예를 들어 `PodcastWasPurchased` 이벤트가 [Eloquent ORM](/docs/{{version}}/eloquent) 객체를 를 받는다고 해보겠습니다: 

    <?php

    namespace App\Events;

    use App\Podcast;
    use App\Events\Event;
    use Illuminate\Queue\SerializesModels;

    class PodcastWasPurchased extends Event
    {
        use SerializesModels;

        public $podcast;

        /**
         * Create a new event instance.
         *
         * @param  Podcast  $podcast
         * @return void
         */
        public function __construct(Podcast $podcast)
        {
            $this->podcast = $podcast;
        }
    }

As you can see, this event class contains no logic. It is simply a container for the `Podcast` object that was purchased. The `SerializesModels` trait used by the event will gracefully serialize any Eloquent models if the event object is serialized using PHP's `serialize` function.

보시다시피, 이 이벤트 클래스는 로직을 가지고 있지 않습니다. 단순히, 구매한 `Podcast` 객체의 컨테이너입니다. 이벤트가 사용하는 `SerializesModels` 속성은 이벤트 객체가 PHP의 `serialize` 기능을 통해 serialize될 경우 Eloquent 모델도 serialize 하게 할 것입니다.

<a name="defining-listeners"></a>
## Defining Listeners
## Listener 정의하기

Next, let's take a look at the listener for our example event. Event listeners receive the event instance in their `handle` method. The `event:generate` command will automatically import the proper event class and type-hint the event on the `handle` method. Within the `handle` method, you may perform any logic necessary to respond to the event.

다음으로, 이벤트 예제에 대한 listener를 살펴보겠습니다. 이벤트 listener들은 `handle` 메소드에서 이벤트 인스턴스를 전달 받습니다. `event:generate` 커맨드는 자동으로 적절한 이벤트 클래스를 가져오고 `handle` 메소드에 이벤트를 타입힌트로 추가할 것입니다. `handle` 메소드 내에서는 이벤트에 대응하기 위한 그 어떤 로직도 구성할 수 있습니다.

    <?php

    namespace App\Listeners;

    use App\Events\PodcastWasPurchased;

    class EmailPurchaseConfirmation
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
         * @param  PodcastWasPurchased  $event
         * @return void
         */
        public function handle(PodcastWasPurchased $event)
        {
            // Access the podcast using $event->podcast...
        }
    }

Your event listeners may also type-hint any dependencies they need on their constructors. All event listeners are resolved via the Laravel [service container](/docs/{{version}}/container), so dependencies will be injected automatically:

이벤트 listener들은 또한 생성자에서, 필요한 모든 의존성들을 타입힌트 할 수 있습니다. 모든 이벤트 listener들은 [서비스 컨테이너](/docs/{{version}}/container)를 통해 처리되기 때문에 의존성은 자동으로 주입됩니다: 

    use Illuminate\Contracts\Mail\Mailer;

    public function __construct(Mailer $mailer)
    {
        $this->mailer = $mailer;
    }

#### Stopping The Propagation Of An Event
#### 이벤트 전달 중단하기

Sometimes, you may wish to stop the propagation of an event to other listeners. You may do so by returning `false` from your listener's `handle` method.

경우에 따라서 이벤트가 다른 listener에게 전달되는 것을 중단 하기를 원할 수도 있습니다. 이러한 경우에는 listener의 `handle` 메소드에서 `false`를 반환하면 됩니다.

<a name="queued-event-listeners"></a>
### Queued Event Listeners
### 큐로 처리하는 이벤트 Listener

Need to [queue](/docs/{{version}}/queues) an event listener? It couldn't be any easier. Simply add the `ShouldQueue` interface to the listener class. Listeners generated by the `event:generate` Artisan command already have this interface imported into the current namespace, so you can use it immediately:

이벤트 핸들러에 [큐](/docs/{{version}}/queues)가 필요합니까? 손쉽게 할 수 있습니다. 간단하게 listener 클래스에 `ShouldQueue` 인터페이스를 추가하면됩니다. `event:generate` 아티즌 명령어로 생성된 listener들은 이미 현재 네임스페이스 사용이 선언 되어 있기 때문에 바로 이용할 수 있습니다: 

    <?php

    namespace App\Listeners;

    use App\Events\PodcastWasPurchased;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class EmailPurchaseConfirmation implements ShouldQueue
    {
        //
    }

That's it! Now, when this listener is called for an event, it will be queued automatically by the event dispatcher using Laravel's [queue system](/docs/{{version}}/queues). If no exceptions are thrown when the listener is executed by the queue, the queued job will automatically be deleted after it has processed.

이게 다입니다! 이제 이 listener가 이벤트를 통해 호출된다면 라라벨의 [큐 시스템](/docs/{{version}}/queues)을 이용하는 이벤트 dispatcher에 의해 자동으로 큐에 저장될 것입니다. 큐를 통해서 listener가 실행되고, 어떤 exception 도 발생하지 않았다면, 큐에 저장된 작업은 실행 뒤 자동으로 삭제될 것입니다. 

#### Manually Accessing The Queue
#### 수동으로 큐에 엑세스 하기

If you need to access the underlying queue job's `delete` and `release` methods manually, you may do so. The `Illuminate\Queue\InteractsWithQueue` trait, which is imported by default on generated listeners, gives you access to these methods:

큐 작업의 `delete`와 `release` 메소드를 수동으로 접근할 필요가 있다면, 그렇게 할 수 있습니다. 생성된 listener들에 의해 자동으로 사용이 선언된(import된) `Illuminate\Queue\InteractsWithQueue` 트레이트가 이 메소드들에 엑세스 할 수 있게 해줍니다: 

    <?php

    namespace App\Listeners;

    use App\Events\PodcastWasPurchased;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class EmailPurchaseConfirmation implements ShouldQueue
    {
        use InteractsWithQueue;

        public function handle(PodcastWasPurchased $event)
        {
            if (true) {
                $this->release(30);
            }
        }
    }

<a name="firing-events"></a>
## Firing Events
## 이벤트 발생시키기

To fire an event, you may use the `Event` [facade](/docs/{{version}}/facades), passing an instance of the event to the `fire` method. The `fire` method will dispatch the event to all of its registered listeners:

이벤트를 발생시키기 위해서, `Event` [파사드](/docs/{{version}}/facades)를 사용하여 이벤트 인스턴스를 `fire` 메소드로 전달할 수 있습니다. `fire` 메소드는 이벤트를 등록되어 있는 모든 listener들에게 보낼 것입니다: 

    <?php

    namespace App\Http\Controllers;

    use Event;
    use App\Podcast;
    use App\Events\PodcastWasPurchased;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  int  $userId
         * @param  int  $podcastId
         * @return Response
         */
        public function purchasePodcast($userId, $podcastId)
        {
            $podcast = Podcast::findOrFail($podcastId);

            // Purchase podcast logic...

            Event::fire(new PodcastWasPurchased($podcast));
        }
    }

Alternatively, you may use the global `event` helper function to fire events:

그 대신에, 글로벌 `event` 헬퍼 함수을 사용하여 이벤트를 발생시킬 수도 있습니다: 

    event(new PodcastWasPurchased($podcast));

<a name="broadcasting-events"></a>
## Broadcasting Events
## 이벤트 broadcasting

In many modern web applications, web sockets are used to implement real-time, live-updating user interfaces. When some data is updated on the server, a message is typically sent over a websocket connection to be handled by the client.

많은 현대적인 웹 애플리케이션들은 web socket을 이용하여 실시간 애플리케이션을 구현하며 UI를 실시간으로 업데이트합니다. 일반적으로 서버에 데이터가 변경되면 메세지가 web socket 연결로 보내져 클라이언트에 의해 처리될 수 있도록 합니다. 

To assist you in building these types of applications, Laravel makes it easy to "broadcast" your events over a websocket connection. Broadcasting your Laravel events allows you to share the same event names between your server-side code and your client-side JavaScript framework.

이런 애플리케이션을 구성하는데 도움을 주기 위해 라라벨은 websocket 커넥션에 이벤트를 "broadcast"하는 것을 손쉽게 할 수 있도록 해줍니다. 라라벨 이벤트를 broadcast하면 여러분의 서버 쪽 코드와 클라이언트 쪽 자바스크립트 프레임워크가 같은 이벤트 이름을 공유할 수 있습니다. 

<a name="broadcast-configuration"></a>
### Configuration
### 설정하기

All of the event broadcasting configuration options are stored in the `config/broadcasting.php` configuration file. Laravel supports several broadcast drivers out of the box: [Pusher](https://pusher.com), [Redis](/docs/{{version}}/redis), and a `log` driver for local development and debugging. A configuration example is included for each of these drivers.

모든 이벤트 broadcast 설정 옵션은 `config/broadcasting.php` 의 설정 파일에 저장되어 있습니다. 라라벨은 기본적으로 여러가지의 broacast 드라이버를 지원합니다. [Pusher](https://pusher.com), [Redis](/docs/{{version}}/redis), 그리고 디버깅 용도의 `log` 드라이버를 이미 제공합니다. 설정에 대한 예제는 각각의 드라이버에 포함되어 있습니다.

#### Broadcast Prerequisites
#### Broadcast 요구사항

The following dependencies are needed for event broadcasting:

이벤트 broadcast를 위해서는 다음의 의존성들이 필요합니다. 

- Pusher: `pusher/pusher-php-server ~2.0`
- Redis: `predis/predis ~1.0`

#### Queue Prerequisites
#### 큐 요구사항

Before broadcasting events, you will also need to configure and run a [queue listener](/docs/{{version}}/queues). All event broadcasting is done via queued jobs so that the response time of your application is not seriously affected. 

이벤트를 broadcast 하기 전에 [큐 listener](/docs/{{version}}/queues)를 설정하고 실행해야 합니다. 모든 이벤트 broadcast는 애플리케이션의 응답 시간에 큰 영향을 주지 않기 위해 큐에 저장된 작업을 통해 진행됩니다.

<a name="marking-events-for-broadcast"></a>
### Marking Events For Broadcast
### Broadcast를 위해 이벤트 표시하기

To inform Laravel that a given event should be broadcast, implement the `Illuminate\Contracts\Broadcasting\ShouldBroadcast` interface on the event class. The `ShouldBroadcast` interface requires you to implement a single method: `broadcastOn`. The `broadcastOn` method should return an array of "channel" names that the event should be broadcast on:

라라벨에게 어떤 이벤트가 broadcast 되어야 할지 알려주기 위해 이벤트 클래스에 `Illuminate\Contracts\Broadcasting\ShouldBroadcast` 인터페이스를 구현합니다. `ShouldBroadcast` 인터페이스는 `broadcastOn`이라는 하나의 메소드를 구현하도록 합니다. `broadcastOn` 메소드는 이벤트가 방송되어야 할 "채널" 이름들의 배열을 반환합니다. 

    <?php

    namespace App\Events;

    use App\User;
    use App\Events\Event;
    use Illuminate\Queue\SerializesModels;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

    class ServerCreated extends Event implements ShouldBroadcast
    {
        use SerializesModels;

        public $user;

        /**
         * Create a new event instance.
         *
         * @return void
         */
        public function __construct(User $user)
        {
            $this->user = $user;
        }

        /**
         * Get the channels the event should be broadcast on.
         *
         * @return array
         */
        public function broadcastOn()
        {
            return ['user.'.$this->user->id];
        }
    }

Then, you only need to [fire the event](#firing-events) as you normally would. Once the event has been fired, a [queued job](/docs/{{version}}/queues) will automatically broadcast the event over your specified broadcast driver.

이제, 예상할 수 있듯이 [이벤트를 발생](#firing-events)시키기만 하면 됩니다. 이벤트가 발생하면 [큐에 저장된 작업](/docs/{{version}}/queues)은 지정된 broadcast 드라이버를 통해 자동으로 이벤트를 broadcast할 것입니다. 

<a name="broadcast-data"></a>
### Broadcast Data
### Broadcast Data

When an event is broadcast, all of its `public` properties are automatically serialized and broadcast as the event's payload, allowing you to access any of its public data from your JavaScript application. So, for example, if your event has a single public `$user` property that contains an Eloquent model, the broadcast payload would be:

이벤트가 broadcast 될 때 이벤트의 모든 `public` 속성은 자동으로 serialize되어 이벤트와 함께 broadcast 되며 자바스크립트 애플리케이션의 모든 public 데이터에 접근할 수 있게 해줍니다. 예를 들어, 이벤트가 Eloquent 모델을 가진 하나의 public `$user` 속성을 가지고 있다면 broadcast payload는 다음과 같을 것입니다: 

    {
        "user": {
            "id": 1,
            "name": "Jonathan Banks"
            ...
        }
    }

However, if you wish to have even more fine-grained control over your broadcast payload, you may add a `broadcastWith` method to your event. This method should return the array of data that you wish to broadcast with the event:

보다 자세하게 broadcast payload를 제어하고자 한다면, 이벤트에 `broadcastWith` 메소드를 추가하면 됩니다. 이 메소드는 이벤트와 함께 broadcast하기 원하는 데이터 배열을 반환합니다: 

    /**
     * Get the data to broadcast.
     *
     * @return array
     */
    public function broadcastWith()
    {
        return ['user' => $this->user->id];
    }

<a name="event-broadcasting-customizations"></a>
### Event Broadcasting Customizations
### 이벤트 Broadcasting 커스터마이징 하기 

#### Customizing The Event Name
#### 이벤트 이름 커스터마이징 하기 

By default, the broadcast event name will be the fully qualified class name of the event. So, if the event's class name is `App\Events\ServerCreated`, the broadcast event would be `App\Events\ServerCreated`. You can customize this broadcast event name using by defining a `broadcastAs` method on your event class:

기본적으로 broadcast 이벤트 이름은 이벤트의 전체 네임스페이를 포함한 클래스 이름일 것입니다. 따라서 이벤트 클래스 이름이 `App\Events\ServerCreated`라면, broadcast 이벤트는 `App\Events\ServerCreated`가 됩니다. 이 broadcast 이벤트 이름은 이벤트 클래스의 `broadcastAs` 메소드를 사용하여 변경할 수 있습니다. 

    /**
     * Get the broadcast event name.
     *
     * @return string
     */
    public function broadcastAs()
    {
        return 'app.server-created';
    }

#### Customizing The Queue
#### 큐-Queue 커스터마이징하기

By default, each event to be broadcast is placed on the default queue for the default queue connection in your `queue.php` configuration file. You may customize the queue used by the event broadcaster by adding an `onQueue` method to your event class. This method should return the name of the queue you wish to use:

기본적으로, 각각의 이벤트는 `queue.php` 설정 파일의 기본 큐 커넥션으로 정의된 큐로 broadcast 됩니다. 이벤트 클래스의 `onQueu` 메소드를 추가하여 이벤트 broadcaster 가 사용할 큐를 변경할 수 있습니다. 이 메소드는 사용하고자 하는 큐의 이름을 반환해야 합니다: 

     /**
     * Set the name of the queue the event should be placed on.
     *
     * @return string
     */
    public function onQueue()
    {
        return 'your-queue-name';
    }

<a name="consuming-event-broadcasts"></a>
### Consuming Event Broadcasts
### Consuming Event Broadcasts

#### Pusher
#### Pusher

You may conveniently consume events broadcast using the [Pusher](https://pusher.com) driver using Pusher's JavaScript SDK. For example, let's consume the `App\Events\ServerCreated` event from our previous examples:

Pusher의 JavaScript SDK를 통해 [Pusher](https://pusher.com) 드라이버를 이용하면 편리하게 이벤트 broacast를 할 수 있습니다. 

    this.pusher = new Pusher('pusher-key');

    this.pusherChannel = this.pusher.subscribe('user.' + USER_ID);

    this.pusherChannel.bind('App\\Events\\ServerCreated', function(message) {
        console.log(message.user);
    });

#### Redis
#### Redis

If you are using the Redis broadcaster, you will need to write your own Redis pub/sub consumer to receive the messages and broadcast them using the websocket technology of your choice. For example, you may choose to use the popular [Socket.io](http://socket.io) library which is written in Node.

Redis broadcaster를 사용하고 있다면 여러분의 Rdis pub/sub sunsumer를 직접 작성하여 메세지를 받고, 원하는 websocket 대상으로 broadcast 할 수 있습니다. 예를 들어, Node에 작성된 인기 있는 [Socket.io](http://socket.io) 라이브러리를 사용할 수 있습니다. 

Using the `socket.io` and `ioredis` Node libraries, you can quickly write an event broadcaster to publish all events that are broadcast by your Laravel application:

`socket.io`와 `ioredis` Node 라이브러리를 사용하면 라라벨 어플리케션이 broadcast하는 모든 이벤트를 발행하는 이벤트 broadcaster를 빠르게 작성할 수 있습니다. 

    var app = require('http').createServer(handler);
    var io = require('socket.io')(app);

    var Redis = require('ioredis');
    var redis = new Redis();

    app.listen(6001, function() {
        console.log('Server is running!');
    });

    function handler(req, res) {
        res.writeHead(200);
        res.end('');
    }

    io.on('connection', function(socket) {
        //
    });

    redis.psubscribe('*', function(err, count) {
        //
    });

    redis.on('pmessage', function(subscribed, channel, message) {
        message = JSON.parse(message);
        io.emit(channel + ':' + message.event, message.data);
    });

<a name="event-subscribers"></a>
## Event Subscribers
## 이벤트 Subscriber

Event subscribers are classes that may subscribe to multiple events from within the class itself, allowing you to define several event handlers within a single class. Subscribers should define a `subscribe` method, which will be passed an event dispatcher instance:

이벤트 Subscriber는 클래스 안에서 다수의 이벤트에 대한 subscribe를 처리할 수 있는 클래스이며, 하나의 클래스 안에서 여러 개의 이벤트 핸들러를 정의할 수 있습니다. Subscriber는 `subscribe` 메소드를 이용하여 이벤트 dispatcher 인스턴스를 전달받도록 정의되어야 합니다:

    <?php

    namespace App\Listeners;

    class UserEventListener
    {
        /**
         * Handle user login events.
         */
        public function onUserLogin($event) {}

        /**
         * Handle user logout events.
         */
        public function onUserLogout($event) {}

        /**
         * Register the listeners for the subscriber.
         *
         * @param  Illuminate\Events\Dispatcher  $events
         */
        public function subscribe($events)
        {
            $events->listen(
                'App\Events\UserLoggedIn',
                'App\Listeners\UserEventListener@onUserLogin'
            );

            $events->listen(
                'App\Events\UserLoggedOut',
                'App\Listeners\UserEventListener@onUserLogout'
            );
        }

    }

#### Registering An Event Subscriber
#### 이벤트 Subscriber 등록하기

Once the subscriber has been defined, it may be registered with the event dispatcher. You may register subscribers using the `$subscribe` property on the `EventServiceProvider`. For example, let's add the `UserEventListener`.

subscriber가 정의되었다면 이벤트 dispatcher와 함께 등록될 수 있습니다. `EventServiceProvider`에 `$subscribe` 속성을 사용하여 subscriber를 등록할 수 있습니다. 예를 들어 다음처럼 `UserEventListener`를 추가할 수 있습니다: 

    <?php

    namespace App\Providers;

    use Illuminate\Contracts\Events\Dispatcher as DispatcherContract;
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
            'App\Listeners\UserEventListener',
        ];
    }
