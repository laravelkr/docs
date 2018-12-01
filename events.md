# Events

- [시작하기](#introduction)
- [Event와 / Listener 등록하기](#registering-events-and-listeners)
- [Event 정의하기](#defining-events)
- [Listener 정의하기](#defining-listeners)
    - [큐에 저장된 이벤트 핸들러](#queued-event-listeners)
- [이벤트 수행하기](#firing-events)
- [이벤트 Broadcasting](#broadcasting-events)
    - [설정하기](#broadcast-configuration)
    - [Broadcast를 위해 이벤트 표시하기](#marking-events-for-broadcast)
    - [Broadcast Data](#broadcast-data)
    - [Event Broadcasts 이용하기](#consuming-event-broadcasts)
- [Event Subscribers](#event-subscribers)
- [프레임워크 이벤트](#framework-events)

<a name="introduction"></a>
## 시작하기

라라벨의 이벤트는 단순한 옵저버 구현을 제공하여, 애플리케이션을 구독하고 이벤트에 대해 리스닝 할 수 있도록 해줍니다. 이벤트 클래스는 보통 `app/Events` 디렉토리에 저장되고 리스너 클래스들은 `app/Listeners`에 저장됩니다. 

<a name="registering-events-and-listeners"></a>
## Events / Listeners 등록하기

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

### Events / Listeners 클래스 생성하기

물론 각각의 이벤트와 listener를의 파일을 일일히 생성하는 것은 불편한 일입니다. 대신 단순히 `EventServiceProvider`에 listener와 이벤트를 추가하고 `event:generate` 명령어를 사용하십시오. 이 명령어는 `EventServiceProvider`의 리스트에 포함된 이벤트나 listener를 생성할 수 있습니다. 물론 이미 존재하는 이벤트나 listener는 영향을 받지 않을 것입니다: 

    php artisan event:generate

### 수동으로 이벤트 등록하기

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

#### 와일드카드 이벤트 리스너

`*`를 와일드카드로 사용하여 listener를 등록하면 동일한 listener 에서 여러 개의 이벤트에 대응 할 수 있습니다. 와일드카드 listener는 전체 이벤트 데이터 배열을 하나의 인자로 받아들입니다: 

    $events->listen('event.*', function (array $data) {
        //
    });

<a name="defining-events"></a>
## 이벤트 정의하기

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

보시다시피, 이 이벤트 클래스는 특별한 로직을 가지고 있지 않습니다. 단순히, 구매한 `Podcast` 객체의 컨테이너입니다. 이벤트가 사용하는 `SerializesModels` 속성은 이벤트 객체가 PHP의 `serialize` 기능을 통해 serialize될 경우 Eloquent 모델도 serialize 하게 할 것입니다.

<a name="defining-listeners"></a>
## Listener 정의하기

다음으로, 이벤트 예제에 대한 listener를 살펴보겠습니다. 이벤트 listener들은 `handle` 메소드에서 이벤트 인스턴스를 전달 받습니다. `event:generate` 커맨드는 자동으로 적절한 이벤트 클래스를 가져오고 `handle` 메소드에 이벤트를 타입힌트로 추가할 것입니다. `handle` 메소드 내에서는 이벤트에 대응하기 위한 그 어떤 로직도 구성할 수 있습니다.

    <?php

    namespace App\Listeners;

    use App\Events\PodcastWasPurchased;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Contracts\Queue\ShouldQueue;

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

이벤트 listener들은 또한 생성자에서, 필요한 모든 의존성들을 타입힌트 할 수 있습니다. 모든 이벤트 listener들은 [서비스 컨테이너](/docs/{{version}}/container)를 통해 처리되기 때문에 의존성은 자동으로 주입됩니다: 

    use Illuminate\Contracts\Mail\Mailer;

    public function __construct(Mailer $mailer)
    {
        $this->mailer = $mailer;
    }

#### 이벤트 전달 중단하기

경우에 따라서 이벤트가 다른 listener에게 전달되는 것을 중단 하기를 원할 수도 있습니다. 이러한 경우에는 listener의 `handle` 메소드에서 `false`를 반환하면 됩니다.

<a name="queued-event-listeners"></a>
### 큐로 처리하는 이벤트 Listener

이벤트 핸들러에 [큐](/docs/{{version}}/queues)가 필요합니까? 손쉽게 할 수 있습니다. 간단하게 listener 클래스에 `ShouldQueue` 인터페이스를 추가하면됩니다. `event:generate` 아티즌 명령어로 생성된 listener들은 이미 현재 네임스페이스 사용이 선언 되어 있기 때문에 바로 이용할 수 있습니다: 

    <?php

    namespace App\Listeners;

    use App\Events\PodcastWasPurchased;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class EmailPurchaseConfirmation implements ShouldQueue
    {
        //
    }

이게 다입니다! 이제 이 listener가 이벤트를 통해 호출된다면 라라벨의 [큐 시스템](/docs/{{version}}/queues)을 이용하는 이벤트 dispatcher에 의해 자동으로 큐에 저장될 것입니다. 큐를 통해서 listener가 실행되고, 어떤 exception 도 발생하지 않았다면, 큐에 저장된 작업은 실행 뒤 자동으로 삭제될 것입니다. 

#### 수동으로 큐에 엑세스 하기

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
## 이벤트 발생시키기

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

그 대신에, 글로벌 `event` 헬퍼 함수을 사용하여 이벤트를 발생시킬 수도 있습니다: 

    event(new PodcastWasPurchased($podcast));

<a name="broadcasting-events"></a>
## 이벤트 broadcasting

많은 현대적인 웹 애플리케이션들은 web socket을 이용하여 실시간 애플리케이션을 구현하며 UI를 실시간으로 업데이트합니다. 일반적으로 서버에 데이터가 변경되면 메세지가 web socket 연결로 보내져 클라이언트에 의해 처리될 수 있도록 합니다. 

이런 애플리케이션을 구성하는데 도움을 주기 위해 라라벨은 websocket 커넥션에 이벤트를 "broadcast"하는 것을 손쉽게 할 수 있도록 해줍니다. 라라벨 이벤트를 broadcast하면 여러분의 서버 쪽 코드와 클라이언트 쪽 자바스크립트 프레임워크가 같은 이벤트 이름을 공유할 수 있습니다. 

<a name="broadcast-configuration"></a>
### 설정하기

모든 이벤트 broadcast 설정 옵션은 `config/broadcasting.php` 의 설정 파일에 저장되어 있습니다. 라라벨은 기본적으로 여러가지의 broacast 드라이버를 지원합니다. [Pusher](https://pusher.com), [Redis](/docs/{{version}}/redis), 그리고 디버깅 용도의 `log` 드라이버를 이미 제공합니다. 설정에 대한 예제는 각각의 드라이버에 포함되어 있습니다.

#### Broadcast 요구사항

이벤트 broadcast를 위해서는 다음의 의존성들이 필요합니다. 

- Pusher: `pusher/pusher-php-server ~2.0`
- Redis: `predis/predis ~1.0`

#### 큐 요구사항

이벤트를 broadcast 하기 전에 [큐 listener](/docs/{{version}}/queues)를 설정하고 실행해야 합니다. 모든 이벤트 broadcast는 애플리케이션의 응답 시간에 큰 영향을 주지 않기 위해 큐에 저장된 작업을 통해 진행됩니다.

<a name="marking-events-for-broadcast"></a>
### Broadcast를 위해 이벤트 표시하기

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

이제, 예상할 수 있듯이 [이벤트를 발생](#firing-events)시키기만 하면 됩니다. 이벤트가 발생하면 [큐에 저장된 작업](/docs/{{version}}/queues)은 지정된 broadcast 드라이버를 통해 자동으로 이벤트를 broadcast할 것입니다. 

<a name="overriding-broadcast-event-name"></a>
#### Broadcast 이벤트 이름 재정의하기

기본적으로 broadcast 이벤트 이름은 이벤트의 전체 네임스페이를 포함한 클래스 이름일 것입니다. 앞서 예로든 클래스를 이용하자면 broadcast 이벤트는 `App\Events\ServerCreated`일 것입니다. `broadcastAs` 메소드를 사용하면 이 broadcast 이벤트 이름을 원하는대로 수정할 수 있습니다: 

    /**
     * Get the broadcast event name.
     *
     * @return string
     */
    public function broadcastAs()
    {
        return 'app.server-created';
    }

<a name="broadcast-data"></a>
### Broadcast Data

이벤트가 broadcast 될 때 이벤트의 모든 `public` 속성은 자동으로 serialize되어 이벤트와 함께 broadcast 되며 자바스크립트 애플리케이션의 모든 public 데이터에 접근할 수 있게 해줍니다. 예를 들어, 이벤트가 Eloquent 모델을 가진 하나의 public `$user` 속성을 가지고 있다면 broadcast payload는 다음과 같을 것입니다: 

    {
        "user": {
            "id": 1,
            "name": "Jonathan Banks"
            ...
        }
    }

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

<a name="consuming-event-broadcasts"></a>
### Event Broadcasts 이용하기

#### Pusher

Pusher의 JavaScript SDK를 통해 [Pusher](https://pusher.com) 드라이버를 이용하면 편리하게 이벤트 broacast를 할 수 있습니다. 

    this.pusher = new Pusher('pusher-key');

    this.pusherChannel = this.pusher.subscribe('user.' + USER_ID);

    this.pusherChannel.bind('App\\Events\\ServerCreated', function(message) {
        console.log(message.user);
    });

#### Redis

Redis broadcaster를 사용하고 있다면 여러분의 Rdis pub/sub sunsumer를 직접 작성하여 메세지를 받고, 원하는 websocket 대상으로 broadcast 할 수 있습니다. 예를 들어, Node에 작성된 인기 있는 [Socket.io](http://socket.io) 라이브러리를 사용할 수 있습니다. 

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
## 이벤트 Subscriber

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

#### 이벤트 Subscriber 등록하기

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

<a name="framework-events"></a>
## 프레임워크 이벤트

라라벨은 프레임워크가 수행하는 동작들을 위한 다양한 "코어-핵심" 이벤트들을 제공합니다. 이 이벤트들은 여러분이 커스텀 이벤트를 구독하는 방법과 같은 방식으로 구독할 수 있습니다. 

Event  |  Parameter(s)
------------- | -----------
artisan.start | $application
auth.attempt | $credentials, $remember, $login
auth.login | $user, $remember
auth.logout | $user
cache.missed | $key
cache.hit | $key, $value
cache.write | $key, $value, $minutes
cache.delete | $key
connection.{name}.beginTransaction | $connection
connection.{name}.committed | $connection
connection.{name}.rollingBack | $connection
illuminate.query | $query, $bindings, $time, $connectionName
illuminate.queue.after | $connection, $job, $data
illuminate.queue.failed | $connection, $job, $data
illuminate.queue.stopping | null
mailer.sending | $message
router.matched | $route, $request
composing:{view name} | $view
creating:{view name} | $view
