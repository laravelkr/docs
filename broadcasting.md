# 브로드캐스팅

- [시작하기](#introduction)
    - [설정하기](#configuration)
    - [드라이버 사전준비사항](#driver-prerequisites)
- [컨셉 개요](#concept-overview)
    - [예제 애플리케이션 사용하기](#using-example-application)
- [브로드캐스트 이벤트 정의하기](#defining-broadcast-events)
    - [브로드캐스트 이름](#broadcast-name)
    - [브로드캐스트 데이터](#broadcast-data)
    - [브로드캐스트 큐](#broadcast-queue)
    - [브로드캐스트 조건](#broadcast-conditions)
- [승인 채널](#authorizing-channels)
    - [승인 라우트 정의하기](#defining-authorization-routes)
    - [승인 콜백 정의하기](#defining-authorization-callbacks)
    - [채널 클래스 정의하기](#defining-channel-classes)
- [브로드캐스팅 이벤트](#broadcasting-events)
    - [오직 다른이에게만](#only-to-others)
- [브로드캐스트 수신하기](#receiving-broadcasts)
    - [라라벨 에코 설치하기](#installing-laravel-echo)
    - [이벤트 리스닝](#listening-for-events)
    - [채널 나가기](#leaving-a-channel)
    - [네임스페이스](#namespaces)
- [주둔 채널](#presence-channels)
    - [주둔 채널 승인하기](#authorizing-presence-channels)
    - [주둔 채널에 들어가기](#joining-presence-channels)
    - [주둔 채널에 브로드캐스트하기](#broadcasting-to-presence-channels)
- [클라이언트 이벤트](#client-events)
- [알림](#notifications)

<a name="introduction"></a>
## 시작하기

많은 현대적인 웹 애플리케이션들은 웹 소캣을 이용하여 실시간 애플리케이션을 구현하며 UI를 실시간으로 업데이트합니다. 일반적으로 서버에 데이터가 변경되면 메세지가 웹 소캣 연결로 보내져 클라이언트에 의해 처리될 수 있도록 합니다. 웹 소켓은 당신의 애플리케이션의 변화를 지속적으로 폴링하는 더 강력하고 효과적인 대안을 제공합니다.

이러한 류의 애플리케이션을 만드는걸 돕기 위해, 라라벨은 당신의 이벤트를 웹소켓 너머로 "브로드캐스트"하기 쉽게 만들었습니다. 라라벨 이벤트를 브로드캐스트하면 여러분의 서버 쪽 코드와 클라이언트 쪽 자바스크립트 프레임워크가 같은 이벤트 이름을 공유할 수 있습니다.

> {tip} 브로드캐스팅에 대해 더 알아보기 전에, 라라벨 [이벤트와 리스너](/docs/{{version}}/events)를 읽으세요.

<a name="configuration"></a>
### 설정하기

모든 이벤트 브로드캐스팅 설정 옵션은 config/broadcasting.php 의 설정 파일에 저장되어 있습니다. 라라벨은 기본적으로 여러가지의 브로드캐스트 드라이버([Pusher Channels](https://pusher.com/channels), [Redis](/docs/{{version}}/redis), 그리고 디버깅 용도의 `log` 드라이버)를 지원합니다. 추가적으로 전체적으로 브로드캐스팅을 끌 수 있도록 해주는 `null` 드라이버도 제공됩니다. 각각의 드라이버의 설정 예제는 `config/broadcasting.php` 설정 파일에 있습니다.

#### 브로드캐스트 서비스 프로바이더

어떤 이벤트던 브로드캐스팅하려면 그 전에, `App\Providers\BroadcastServiceProvider`를 등록해야 합니다. 최신 라라벨 애플리케이션에서는, `config/app.php` 설정 파일에 있는 `providers` 배열에서 이 프로바이더의 주석을 해제 해주기만 하면 됩니다. 이 프로바이더는 브로드캐스트 인증 라우트와 콜백을 등록할 수 있게 해줍니다.

#### CSRF 토큰

현재 세션의 CSRF 토큰에 접근하기 위해서는 [라라벨 Echo](#installing-laravel-echo)가 필요합니다. 여러분은 애플리케이션의 `head` HTML 요소의 `meta` 테그가 CSRF 토큰을 포함하고 있는지 확인해야 합니다.

    <meta name="csrf-token" content="{{ csrf_token() }}">

<a name="driver-prerequisites"></a>
### 드라이버 사전준비사항

#### Pusher Channels

만약 [Pusher Channels](https://pusher.com/channels)를 이용해서 이벤트를 브로드캐스팅한다면, 컴포저 패키지 매니저를 사용해 Pusher PHP SDK를 설치해야 합니다.

    composer require pusher/pusher-php-server "~4.0"

다음으로, `config/broadcasting.php` 설정 파일에서 Channels 인증정보를 설정해야 합니다. 이미 이 파일에는 Channels을 빠르게 설정할 수 있도록 Channels key, 비밀번호, 그리고 애플리케이션 ID의 예제가 포함되어 있습니다. `config/broadcasting.php` 파일의 `pusher` 설정을 이용하면, cluster와 같은 Pusher 에서 지원하는 추가적인 `options`을 지정할 수 있습니다.

    'options' => [
        'cluster' => 'eu',
        'useTLS' => true
    ],

Channels 와 [Laravel Echo](#installing-laravel-echo) 를 쓸 때는, `resources/js/bootstrap.js` 파일에서 Echo 인스턴스를 초기화할 때 `pusher`를 브로드캐스터로 지정해주어야 합니다.

    import Echo from "laravel-echo";

    window.Pusher = require('pusher-js');

    window.Echo = new Echo({
        broadcaster: 'pusher',
        key: 'your-pusher-channels-key'
    });

#### Redis

만약 Redis 브로드캐스터를 사용한다면, Predis 라이브러리를 설치해야 합니다.

    composer require predis/predis

Redis 브로드캐스터는 레디스의 pub / sub 기능을 이용해 메시지를 브로드캐스트 할 것입니다. 하지만 Redis로 부터 메시지를 받아 당신의 웹소캣 채널들에 브로드캐스트 할 수 있도록 레디스와 웹소캣 서버를 페어링해야 합니다.

Redis 브로드캐스터가 이벤트를 발행하면, 이벤트는 이벤트에 명시된 채널명에 발행될 것이고 payload는 이벤트명, `data` payload, 그리고 이벤트 소켓 ID를 생성한 사용자를 담고 있는 JSON 인코딩된 문자가 될 것입니다.

#### Socket.IO

만약 Redis 브로드캐스터와 Socket.IO 서버를 페어링 하고자 한다면, 애플리케이션에 Socket.IO 자바스크립트 클라이언트 라이브리를 인클루드 해야 합니다. 이 라이브러리는 NPM 패키지 매니저를 사용해서 설치할 수 있습니다.

    npm install --save socket.io-client

그리고나서, `socket.io` 커넥터와 `host`로 Echo를 초기화 시켜야 합니다.

    import Echo from "laravel-echo"

    window.io = require('socket.io-client');

    window.Echo = new Echo({
        broadcaster: 'socket.io',
        host: window.location.hostname + ':6001'
    });

마지막으로, 호환되는 Socket.IO 서버를 실행할 필요가 있습니다. 라라벨은 Socket.IO 서버 구현을 포함하지 않고 있습니다. 하지만 커뮤니티가 주도하는 Socket.IO 서버가 현재 [tlaverdure/laravel-echo-server](https://github.com/tlaverdure/laravel-echo-server) 깃헙 라이브러리에서 관리되고 있습니다.

#### 큐 전제조건

이벤트를 브로드캐스트 하기전에, [큐 리스너](/docs/{{version}}/queues)도 설정하고 실행해야 합니다. 모든 이벤트 브로드캐스팅은 대기 행렬화된 작업(queued jobs)을 통해 처리되기 때문에 애플리케이션의 반응 시간에 크게 영향을 미치지 않습니다.

<a name="concept-overview"></a>
## 컨셉 개요

라라벨의 이벤트 브로드캐스팅은 웹소캣에 드라이버 기반 접근법을 사용하여 서버측의 라라벨 이벤트를 클라이언트측의 자바스크립트 애플리케이션에 전송할 수 있도록 해줍니다. 현재 라라벨은 [Pusher Channels](https://pusher.com/channels)와 Redis 드라이버를 제공합니다. 이벤트는 클라이언트단에서 [Laravel Echo](#installing-laravel-echo)라는 자바스크립트 패키지를 통해 손쉽게 사용할 수 있습니다.

이벤트는 공개적이거나 비공개적이라고 명시된 "채널"을 통해 브로드캐스트 됩니다. 모든 방문자는 인증이나 승인 없이도 공개 채널을 구독할 수 있습니다. 하지만 비공개 채널을 구독하기 위해서는 반드시 인증과 승인을 받아야 합니다.

<a name="using-example-application"></a>
### 예제 애플리케이션 사용하기

이벤트 브로드캐스팅의 각 구성요소에 대해 깊게 들어가기 전에, 전자상거래 상점을 예로들어 전반적인 내용을 둘러보도록 하겠습니다. 이 문서의 다른 부분에서 따로 자세하게 다룰 것이기 때문에 [Pusher Channels](https://pusher.com/channels) 나 [Laravel Echo](#installing-laravel-echo) 설정에 대한 자세한 내용은 다루지 않습니다.

애플리케이션에 사용자들이 자신의 주문에 대해 배송상태를 확인할 수 있는 페이지가 있다고 생각해봅시다. 그리고 애플리케이션에 의해 배송 상태가 업데이트 되면 `ShippingStatusUpdated` 라는 이벤트가 발생한다고 가정해봅시다.

    event(new ShippingStatusUpdated($update));

#### `ShouldBroadcast` 인터페이스

우리는 사용자가 그들의 주문을 조회하고 있을 때, 배송 상태가 업데이트 된 것을 보기 위해 페이지를 반드시 리프레시 해야하는 것을 원하지 않습니다. 대신, 갱신되었음을 애플리케이션에 전송하길 원합니다. 우리는 `ShouldBroadcast` 인터페이스로 `ShippingStatusUpdated` 이벤트를 표시할 필요가 있습니다. 이렇게하면 라라벨에게 이벤트가 발생시 이 이벤트를 전송하라고 지시할 것입니다.

    <?php

    namespace App\Events;

    use Illuminate\Broadcasting\Channel;
    use Illuminate\Queue\SerializesModels;
    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Broadcasting\PresenceChannel;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

    class ShippingStatusUpdated implements ShouldBroadcast
    {
        /**
         * Information about the shipping status update.
         *
         * @var string
         */
        public $update;
    }

`ShouldBroadcast` 인터페이스는 `broadcastOn` 메소드를 정의하기 위해서 이벤트를 필요로 합니다. 이 메소드는 이벤트가 전송되어야 할 채널을 돌려주는 역할을 합니다. 생성된 이벤트 클래스에 비어있는 메소드가 이미 정의되어 있기 때문에, 구체적인 내용만 채워넣으면 됩니다. 우리는 주문을 생성한 사람들만이 갱신된 상태를 볼 수 있도록 하고 싶으므로, 이 주문과 관련된 채널들에만 비공개적으로 이벤트를 전송할 것입니다.

    /**
     * Get the channels the event should broadcast on.
     *
     * @return \Illuminate\Broadcasting\PrivateChannel
     */
    public function broadcastOn()
    {
        return new PrivateChannel('order.'.$this->update->order_id);
    }

#### 채널 인증하기

비공개 채널을 수신하기 위해서는 반드시 사용자가 인증되어야 합니다. `routes/channels.php` 파일에서 채널 인증 규칙을 정의할 수 있습니다. 이 예제에서는 비공개 채널인 `order.1` 채널을 수신하고자 시도하는 모든 유저에 대해 해당 주문을 실제로 생성한 사람인지 확인할 필요가 있습니다.

    Broadcast::channel('order.{orderId}', function ($user, $orderId) {
        return $user->id === Order::findOrNew($orderId)->user_id;
    });

`channel` 메소드는 두 개의 인자(채널명과 사용자가 채널을 들을 수 있도록 인증되었는지를 나타내는 `true` 혹은 `false`를 되돌려주는 콜백)를 받습니다.

모든 승인 콜백은 현재 인증된 사용자를 첫번째 인수로 받고, 다음 인수로 추가적인 와일드카드 파라미터들을 받습니다. 이 예제에서, 채널명의 "ID" 부분을 가리키기 위해 `{orderId}` 플레이스 홀더를 사용합니다.

#### 이벤트 브로드캐스트 수신하기

다음으로 자바스크립트 애플리케이션에서 이벤트를 수신하는 것만 남았습니다. 라라벨 에코를 사용해서 이를 처리할 수 있습니다. 우선, 비공개 채널을 수신하기 위해 `private` 메소드를 사용할 것입니다. 그리고 나서, `ShippingStatusUpdated` 이벤트를 수신하기 위해 `listen` 메소드를 사용할 것입니다. 기본적으로, 이벤트의 모든 공개 속성들은 전송하는 브로드캐스트 이벤트에 포함됩니다.

    Echo.private(`order.${orderId}`)
        .listen('ShippingStatusUpdated', (e) => {
            console.log(e.update);
        });

<a name="defining-broadcast-events"></a>
## 브로드캐스트 이벤트 정의하기

라라벨에게 어떤 이벤트가 브로드캐스트 되어야 한다고 알려주기 위해, 이벤트 클래스에 `Illuminate\Contracts\Broadcasting\ShouldBroadcast` 인터페이스를 구현합니다. 이 인터페이스는 프레임워크에 의해 생성된 모든 이벤트 클래스에 이미 포함되어 있으므로, 어떤 이벤트던 이를 쉽게 추가할 수 있을 것입니다.

`ShouldBroadcast` 인터페이스는 `broadcastOn`이라는 단 하나의 메소드를 구현할 것을 요구합니다. `broadcastOn` 메소드는 이벤트를 브로드캐스트 해야하는 채널 혹은 채널의 배열을 반환해야 합니다. 채널은 `Channel`, `PrivateChannel` 혹은 `PresenceChannel` 의 인스턴스여야 합니다. `Channel`의 인스턴스는 모든 사용자가 구독할 수 있는 공개 채널을 나타내는 반면, `PrivateChannels`와 `PresenceChannels`는 [채널 승인(channel suthorization)](#authorizing-channels)을 필요로 하는 비공개 채널을 나타냅니다.

    <?php

    namespace App\Events;

    use App\User;
    use Illuminate\Broadcasting\Channel;
    use Illuminate\Queue\SerializesModels;
    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Broadcasting\PresenceChannel;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

    class ServerCreated implements ShouldBroadcast
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
         * Get the channels the event should broadcast on.
         *
         * @return Channel|array
         */
        public function broadcastOn()
        {
            return new PrivateChannel('user.'.$this->user->id);
        }
    }

그러고나면, 당신이 평소에 하듯 이벤트를 발생시키기만 하면 됩니다. 이벤트가 한 번 발생하고나면, [대기행렬화된 작업(queued job)](/docs/{{version}}/queues)이 자동으로 당신이 지정한 브로드캐스트 드라이버를 통해 이벤트를 자동으로 브로드캐스트 할 것입니다.

<a name="broadcast-name"></a>
### 브로드캐스트 이름

기본적으로, 라라벨은 이벤트의 클래스 이름을 사용하여 이벤트를 브로드캐스팅 합니다. 그렇지만 이벤트에 `broadcastAs` 메소드를 정의하여 브로드캐스트 이름을 변경할 수 있습니다.

    /**
     * The event's broadcast name.
     *
     * @return string
     */
    public function broadcastAs()
    {
        return 'server.created';
    }

`broadcastAs` 메소드를 사용하여 브로드캐스트 이름을 커스터마이징 했다면, `.` 문자를 시작으로 하는 리스너를 등록해야합니다. 이를 통해서 Echo가 애플리케이션의 네임스페이스를 이벤트 앞에 추가하지 않도록 합니다.

    .listen('.server.created', function (e) {
        ....
    });

<a name="broadcast-data"></a>
### 브로드캐스트 데이터

이벤트가 브로드캐스트될 때, 이벤트의 모든 `public` 속성들은 자동적으로 시리얼라이즈되어 이벤트의 데이터로 브로드캐스트되고, 자바스크립트 애플리케이션에서 이벤트의 모든 공개적인 데이터에 접근할 수 있도록 해줍니다. 그래서 예를들면, 이벤트가 엘로퀀트 모델을 담고 있는 `$user`라는 퍼블릭 속성 하나를 가지고 있다면, 이벤트의 데이터는 다음과 같이 될 것입니다.

    {
        "user": {
            "id": 1,
            "name": "Patrick Stewart"
            ...
        }
    }

브로드캐스트 데이터를 더 정교하게 다루고 싶다면 `broadcastWith` 메소드를 사용할 수 있습니다. 이 메소드는 당신이 브로드캐스트하기 원하는 데이터의 배열을 이벤트 데이터로 되돌려 줄 것입니다.

    /**
     * Get the data to broadcast.
     *
     * @return array
     */
    public function broadcastWith()
    {
        return ['id' => $this->user->id];
    }

<a name="broadcast-queue"></a>
### 브로드캐스트 큐-queue

기본적으로, 각 브로드캐스트 이벤트는 `queue.php` 설정 파일에 명시해둔 기본 대기행렬 연결상의 기본 대기행열에 놓이게 될 것입니다. 이벤트 클래스의 `broadcastQueue` 속성을 정의함으로써 대기행렬을 원하는대로 설정할 수 있습니다. 이 속성은 당신이 브로드캐스트할 때 사용하길 원하는 큐의 이름을 지정할 것입니다.

    /**
     * The name of the queue on which to place the event.
     *
     * @var string
     */
    public $broadcastQueue = 'your-queue-name';

기본 큐 드라이버 대신에 `sync` 큐 를 사용하여 이벤트를 브로드캐스팅하려면, `ShouldBroadcast` 대신에 `ShouldBroadcastNow` 인터페이스를 구현해야 하면 됩니다.

    <?php

    use Illuminate\Contracts\Broadcasting\ShouldBroadcastNow;

    class ShippingStatusUpdated implements ShouldBroadcastNow
    {
        //
    }
<a name="broadcast-conditions"></a>
### 조건 브로드캐스트

때로는 특정 조건이 참일 경우만 이벤트를 브로드캐스팅하고 싶은 경우가 있습니다. 이러한 경우 이벤트 클래스에 `broadcastWhen` 메서드를 추가하여 브로드캐스팅을 실행 시키는 조건을 정의 할 수 있습니다.

    /**
     * Determine if this event should broadcast.
     *
     * @return bool
     */
    public function broadcastWhen()
    {
        return $this->value > 100;
    }

<a name="authorizing-channels"></a>
## 채널 승인하기

비공개 채널은 현재 인증된 사용자가 실제로 채널을 구독할 수 있는지 검사할 것을 요구합니다. 이 검사는 라라벨 애플리케이션에 채널명을 포함한 HTTP 요청을 생성하고 애플리케이션으로 하여금 사용자가 채널을 수신할 수 있는지 판단하게 함으로써 이뤄질 수 있습니다. [Laravel Echo](#installing-laravel-echo)를 쓰면 비공개 채널 구독 승인을 위한 HTTP 요청이 자동으로 생성될 것입니다. 하지만 이러한 요청들에 대한 적절한 응답 라우트는 당신이 정의해줄 필요가 있습니다.

<a name="defining-authorization-routes"></a>
### 승인 라우트 정의하기

고맙게도, 라라벨은 채널 승인 요청에 대한 응답 라우트를 쉽게 정의할 수 있게 해줍니다. 라라벨 애플리케이션에 포함된 `BroadcastServiceProvider`에서 `Broadcast::routes`라는 메소드를 볼 수 있을 것입니다. 이 메소드가 승인 요청을 처리하는 라우트인 `/broadcasting/auth`를 등록해 줄 것입니다.

    Broadcast::routes();

`Broadcast::routes` 메소드는 자동적으로 라우트를 `web` 미들웨어 그룹에 위치시킬 것입니다. 그렇지만 할당된 속성들을 커스터마이즈하기 위해서는 메소드에 라우트 속성 배열을 전달해줘야 합니다.

    Broadcast::routes($attributes);
    
#### Authorization Endpoint의 커스터마이징

기본적으로 Echo는 `/broadcast/auth` 라는 엔드포인트를 사용하여 채널 액세스 권한을 부여합니다. 그러나 Echo 인스턴스에 `authEndpoint` 설정 옵션을 전달하여 여러분 만의 인증 엔드포인트를 지정할 수 있습니다 :

    window.Echo = new Echo({
        broadcaster: 'pusher',
        key: 'your-pusher-channels-key',
        authEndpoint: '/custom/endpoint/auth'
    });


<a name="defining-authorization-callbacks"></a>
### 승인 콜백 정의하기

이제 실제로 채널 승인을 수행하는 로직을 정의하는 일이 남았습니다. 애플리케이션의 `routes/channels.php` 파일에서 이를 정의할 수 있습니다. 이 파일에서 채널 승인 콜백을 등록하기 위해 `Broadcast::channel` 을 사용할 수 있습니다.

    Broadcast::channel('order.{orderId}', function ($user, $orderId) {
        return $user->id === Order::findOrNew($orderId)->user_id;
    });

`channel` 메소드는 두 개의 인수(채널명과 사용자가 채널을 수신하도록 승인되었는지 여부를 나타내는 `true` 혹은 `false` 값을 리턴하는 콜백)를 받습니다.

모든 승인 콜백은 현재 인증된 사용자를 첫번째 인수로 받고, 다음 인수로 추가적인 와일드카드 파라미터들을 받습니다. 이 예제에서, 채널명의 "ID" 부분을 가리키기 위해 `{orderId}` 플레이스 홀더를 사용합니다.

#### 승인 콜백의 모델 바인딩

HTTP 라우트와 같이 채널 라우트는 명시적 그리고 묵시적 [라우트 모델 바인딩](/docs/{{version}}/routing#route-model-binding)의 장점을 사용할 수 있습니다. 예를 들어, 문자열이나 숫자형태의 주문 ID를 받는 대신에, 실제 `Order` 모델 인스턴스를 요청할 수 있습니다.

    use App\Order;

    Broadcast::channel('order.{order}', function ($user, Order $order) {
        return $user->id === $order->user_id;
    });

#### 인증 권한 부여 콜백

사설 및 현재 브로드캐스트 채널은 애플리케이션의 기본 인증 가드를 통해 현재 사용자를 인증합니다. 사용자가 인증되지 않으면 채널 권한이 자동으로 거부되고 권한 콜백이 실행되지 않습니다. 그러나 필요한 경우 들어오는 Request에 인증해야하는 사용자 지정 가드를 여러 개 지정할 수 있습니다.

    Broadcast::channel('channel', function() {
        // ...
    }, ['guards' => ['web', 'admin']]);

<a name="defining-channel-classes"></a>
### 채널 클래스 정의하기

만약 여러분의 애플리케이션에서 다양한 채널들을 사용하고 있다면, `routes/channels.php` 파일의 규모가 커질 수 있습니다. 이런 경우, 채널 인증을 클로저 대신 채널 클래스로 사용 하실 수 있습니다. 채널 클래스를 생성하려면 Artisan `make:channel` 커맨드를 이용하시기 바랍니다. 해당 커맨드는 `App / Broadcasting` 디렉토리에 새로운 채널 클래스를 생성합니다.

    php artisan make:channel OrderChannel

그 다음, `routes/channels.php` 파일에 생성된 채널을 등록할 수 있습니다.

    use App\Broadcasting\OrderChannel;

    Broadcast::channel('order.{order}', OrderChannel::class);

마지막으로, 채널 클래스의 `join` 메서드에 해당 채널 인증에 관련된 로직을 작성 할 수 있습니다. `join` 메서드는 일반적으로 채널 승인 클로저에 작성하던 로직과 동일한 로직이 작성됩니다. 채널 모델 바인딩을 활용할 수도 있습니다.

    <?php

    namespace App\Broadcasting;

    use App\User;
    use App\Order;

    class OrderChannel
    {
        /**
         * Create a new channel instance.
         *
         * @return void
         */
        public function __construct()
        {
            //
        }

        /**
         * Authenticate the user's access to the channel.
         *
         * @param  \App\User  $user
         * @param  \App\Order  $order
         * @return array|bool
         */
        public function join(User $user, Order $order)
        {
            return $user->id === $order->user_id;
        }
    }

> {tip} 라라벨에 다른 많은 클래스와 같이, 채널 클래스도 자동으로 [서비스 컨테이너](/docs/{{version}}/container)를 통해 의존성이 해결됩니다. 따라서, 의존성을 가진 객체들을 채널 클래스 생성자에서 타입 힌트로 제공받을 수 있습니다.

<a name="broadcasting-events"></a>
## 이벤트 브로드캐스트하기

이벤트를 정의하고 `ShouldBroadcast` 인터페이스를 이용해 표시했다면, `event` 기능을 이용해서 이벤트를 발생시키기만 하면 됩니다. 이벤트 디스패쳐가 이벤트가 `ShouldBroadcast` 인터페이스를 이용해 표시되었음을 알리고 브로드캐스트하기 위해 이벤트를 대기행렬에 넣을 것입니다.

    event(new ShippingStatusUpdated($update));

<a name="only-to-others"></a>
### 오직 다른이에게만

이벤트 브로드캐스팅을 활용하는 애블리케이션을 만들 때, `event` 기능을 `broadcast` 기능으로 대체할 수 있습니다. `event` 기능과 같이 `broadcast` 기능도 서버단의 리스너들에게 이벤트를 보냅니다.

    broadcast(new ShippingStatusUpdated($update));

그렇지만, `broadcast` 기능은 브로드캐스트 수신자에서 현재 사용자를 제외시켜주는 `toOthers` 메소드를 노출합니다.

    broadcast(new ShippingStatusUpdated($update))->toOthers();

언제 `toOthers` 메소드를 쓰면 좋은지 더 잘 이해하기 위해 할일 이름을 입력함으로써 새로운 할일 을 생성하는 할일 목록 애플리케이션을 상상해봅시다.할일을 생성하기 위해 애플리케이션은 아마도 할일이 생성되었음을 브로드캐스트하고 새 할일의 JSON 표현을 되돌려주는 `/task` 라는 엔드포인트에 요청을 보낼 것입니다. 엔드포인트로부터 자바스크립트 애플리케이션이 응답을 받으면, 다음과 같이 즉각적으로 할일 목록에 새 할일을 추가할 것입니다.

    axios.post('/task', task)
        .then((response) => {
            this.tasks.push(response.data);
        });

그러나, 할일이 생성되었음을 브로드캐스트도 한다는 것을 기억하세요. 만약 자바스크립트 애플리케이션이 할일 목록에 새 할일을 추가하기 위해 이 이벤트를 수신하고 있다면 하나는 엔드 포인트를 통해, 다른 하나는 브로드캐스트를 통해 할일 목록에 할일이 중복으로 생성될 것입니다. 현재 사용자에게는 이벤트를 브로드캐스트 하지 않도록 브로드캐스터에 지시하기 위해 `toOthers` 메소드를 사용함으로써 이러한 문제를 해결할 수 있습니다.

> {note} `toOthers` 메서드를 호출할려면 반드시 이벤트에 `Illuminate\Broadcasting\InteractsWithSockets` 트레이트를 사용해야 합니다.

#### 설정

라라벨 에코 인스턴스를 초기화 할 때, 커넥션에 소켓 ID 가 할당 됩니다. 만약 당신이 [Vue](https://vuejs.org)와 [Axios](https://github.com/mzabriskie/axios)를 사용하고 있다면, 모든 외부로 나가는 리퀘스트에 소켓 ID가 `X-Socket-ID` 헤더로 자동으로 첨부될 것입니다. 이후, `toOthers` 메소드를 호출하면, 라라벨은 헤더에서 소켓 ID를 추출해서 브로드캐스터로 하여금 해당 소켓 ID를 가지고 있는 모든 커넥션에 브로드캐스트 하지 않도록 지시할 것입니다.

만일 Vue와 Axios를 사용하지 않는다면, `X-Socket-ID` 헤더를 전송하기 위해 자바스크립트 애플리케이션을 수동으로 설정해주어야 합니다. `Echo.socketId` 메소드를 이용하여 소켓 ID를 받을 수 있습니다.

    var socketId = Echo.socketId();

<a name="receiving-broadcasts"></a>
## 브로드캐스트 받기

<a name="installing-laravel-echo"></a>
### 라라벨 에코 설치하기

라라벨 에코는 채널을 구독하고 라라벨에 의해 브로드캐스트되는 이벤트를 수신하기 쉽게 해주는 자바스크립트 라이브러리입니다. NPM 패키지 매니저로 에코를 설치할 수 있습니다. 이 예제에서는 Pusher Channels 브로드캐스터를 사용할 것이기 때문에 `pusher-js`도 설치할 것입니다.

    npm install --save laravel-echo pusher-js

에코가 인스톨되고나면, 애플리케이션의 자바스크립트에서 새로운 에코 인스턴스를 생성할 준비가 완료됩니다. 새로운 에코 인스턴스를 생성하기 좋은 위치는 라라벨 프레임워크와 함께 인클루드되는 `resources/js/bootstrap.js` 파일의 하단입니다.

    import Echo from "laravel-echo"

    window.Echo = new Echo({
        broadcaster: 'pusher',
        key: 'your-pusher-channels-key'
    });

`pusher` 커넥터를 사용하는 Echo 인스턴스를 생성 할 때, TLS를 통해 연결해야하는지 여부뿐만 아니라 `cluster`도 지정할 수 있습니다. (기본적으로 `forceTLS`가 `false` 일 때, 페이지가 HTTP를 통해 로드되면 TLS가 아닌 연결이 되거나 TLS 연결이 실패하면 폴백이 됩니다).

    window.Echo = new Echo({
        broadcaster: 'pusher',
        key: 'your-pusher-channels-key',
        cluster: 'eu',
        forceTLS: true
    });
    
#### 기존 클라이언트 인스턴스 사용

Echo가 활용할 Pusher Channels 또는 Socket.io 클라이언트 인스턴스가 이미있는 경우, `client` 설정 옵션을 통해 이것을 Echo에 전달할 수 있습니다 :


    const client = require('pusher-js');

    window.Echo = new Echo({
        broadcaster: 'pusher',
        key: 'your-pusher-channels-key',
        client: client
    });

<a name="listening-for-events"></a>
### 이벤트 수신하기

에코를 인스톨했다면, 이벤트 브로드캐스트를 수신하기 시작할 준비가 된 것입니다. 첫째로, 채널 인스턴스를 받기 위해 `channel` 메소드를 사용한 후, 특정 이벤트를 수신하기 위해 `listen` 메소드를 호출합니다.

    Echo.channel('orders')
        .listen('OrderShipped', (e) => {
            console.log(e.order.name);
        });

비공개 채널에서 이벤트를 수신하고 싶으면, `private` 메소드를 사용하세요. 하나의 채널에있는 여러 이벤트를 수신하고 싶으면 `listen` 메소드를 연결해서 사용할 수 있습니다.

    Echo.private('orders')
        .listen(...)
        .listen(...)
        .listen(...);

<a name="leaving-a-channel"></a>
### 채널 나가기

채널을 나가기 위해서는, 에코 인스턴스에서 `leaveChannel` 메소드를 호출하면 됩니다.

    Echo.leaveChannel('orders');

채널을 나가면서 연관된 비공개 현재 채널 또한 나가려면, 다음과 같이 `leave` 메소드를 호출하면 됩니다.

    Echo.leave('orders');

<a name="namespaces"></a>
### 네임스페이스

위 예제에서 이벤트 클래스의 풀 네임스페이스를 명시하지 않았다는 것을 알아차리셨을지 모르겠습니다. 이는 에코가 자동적으로 이벤트들이 `App\Events` 네임스페이스에 위치하고 있다고 가정하기 때문입니다. 에코를 초기화 할 때 `namespace` 설정 옵션을 넘겨주면 루트 네임스페이스를 변경할 수 있습니다.

    window.Echo = new Echo({
        broadcaster: 'pusher',
        key: 'your-pusher-channels-key',
        namespace: 'App.Other.Namespace'
    });

이 대신에, 에코를 이용하여 이벤트를 구독할 때 이벤트 클래스 앞에 `.`를 붙일 수 있습니다. 이렇게 하면 항상 정규화된 클래스명을 명시할 수 있습니다.

    Echo.channel('orders')
        .listen('.Namespace\\Event\\Class', (e) => {
            //
        });

<a name="presence-channels"></a>
## 프레젠스 채널

프레젠스 채널은 비공개 채널에 누가 채널을 구독하고 있는지 알려주는 기능을 추가해줍니다. 프레젠스 채널은 다른 사용자가 같은 페이지를 보고 있을 때 알림을 보내는 것과 같은 강력하고, 협동적인 애플리케이션을 만들기 쉽게 해줍니다.

<a name="authorizing-presence-channels"></a>
### 프레젠스 채널 승인하기

모든 프레젠스 채널은 비공개 채널이기도 합니다. 그러므로 사용자는 당연히 프레젠스에 접근하기 위해 [승인을 받아야 합니다](#authorizing-channels). 그렇지만, 프레젠스 채널을 위한 승인 콜백을 정의할 때, 채널에 들어오는 것을 승인하기 위해 `true`를 되돌려주도록 하는 대신 사용자의 데이터 배열을 되돌려 주어야 합니다.

승인 콜백에 의해 되돌려받는 데이터는 자바스크립트 애플리케이션의 프레젠스 채널 이벤트 수신자들이 사용할 수 있게 될 것입니다. 만약 사용자가 프레젠스 채널에 들어가는 것이 승인되지 않았으면, `false`나 `null`을 되돌려 주어야 합니다.

    Broadcast::channel('chat.{roomId}', function ($user, $roomId) {
        if ($user->canJoinRoom($roomId)) {
            return ['id' => $user->id, 'name' => $user->name];
        }
    });

<a name="joining-presence-channels"></a>
### 프레젠스 채널에 들어가기

프레젠스 채널에 들어가기 위해 에코의 `join` 메소드를 사용할 수 있습니다. `join` 메소드는 `listen` 메소드를 노출하고 `here`, `joining` 그리고 `leaving` 이벤트를 구독할 수 있게 해주는 `PresenceChannel`을 되돌려줄 것입니다.

    Echo.join(`chat.${roomId}`)
        .here((users) => {
            //
        })
        .joining((user) => {
            console.log(user.name);
        })
        .leaving((user) => {
            console.log(user.name);
        });

`here` 콜백은 채널이 성공적으로 들어가지면 즉각적으로 실행될 것이고, 현재 채널을 구독하고 있는 다른 모든 사용자들의 정보를 담은 배열을 수신하게 됩니다. `joining` 메소드는 새로운 사용자가 채널에 들어오면 실행될 것이고, `leaving` 메소드는 사용자가 떠나면 실행될 것입니다.

<a name="broadcasting-to-presence-channels"></a>
### 프레젠스 채널에 브로드캐스트하기

프레젠스 채널 역시 공개 채널이나 비공개 처널과 마찬가지로 이벤트를 받을 수 있습니다. 채팅방의 예를 사용해보면, 방의 프레젠스 채널에 `NewMessage` 이벤트를 브로드캐스트 하길 원할 수 있습니다. 그러기 위해서는 이벤트의 `broadcastOn` 메소드에서 `PresenceChannel` 인스턴스를 되돌려 줄 것입니다.

    /**
     * Get the channels the event should broadcast on.
     *
     * @return Channel|array
     */
    public function broadcastOn()
    {
        return new PresenceChannel('room.'.$this->message->room_id);
    }

공개 혹은 비공개 이벤트처럼, 프레젠스 채널 이벤트도 `broadcast` 기능을 이용해 브로드캐스트됩니다. 다른 이벤트와 마찬가지로 현재 사용자를 브로드캐스트 받지 않도록 제외하기 위해서 `toOthers` 메소드를 사용할 수 있습니다.

    broadcast(new NewMessage($message));

    broadcast(new NewMessage($message))->toOthers();

에코의 `listen` 메소드를 통해 조인 이벤트를 수신할 수 있습니다.

    Echo.join(`chat.${roomId}`)
        .here(...)
        .joining(...)
        .leaving(...)
        .listen('NewMessage', (e) => {
            //
        });

<a name="client-events"></a>
## 클라이언트 이벤트

> {tip} [Pusher Channels](https://pusher.com/channels)를 사용하는 경우, 클라이언트 이벤트를 전송하려면 [애플리케이션 대시 보드](https://dashboard.pusher.com/)의 "App Settings"섹션에서 "Client Events" 옵션을 활성화해야합니다.

때로는 라라벨 애플리케이션을 거치지 않고, 연결된 다른 클라이언트에게 이벤트를 브로드캐스트 해야할 수도 있습니다. 이는 특정한 경우 유용할 수 있는데, 어떤 사용자가 화면에 메세지를 "입력"하고 있다는 것을 다른 사용자에게 알리는 경우가 그렇습니다. 

클라이언트 이벤트를 브로드 캐스트하려면, Echo의 `whisper` 메소드를 사용하면 됩니다.

    Echo.private('chat')
        .whisper('typing', {
            name: this.user.name
        });

클라이언트 이벤트를 수신하려면, `listenForWhisper` 메소드를 사용하면 됩니다.

    Echo.private('chat')
        .listenForWhisper('typing', (e) => {
            console.log(e.name);
        });


<a name="notifications"></a>
## 알림

이벤트 브로드캐스팅을 [notifications](/docs/{{version}}/notifications)과 엮음으로써, 당신의 자바스크립트 애플리케이션은 페이지를 새로고침 할 필요없이 새로운 알림을 받을 수 있습니다. 우선 [브로드캐스트 알림 채널](/docs/{{version}}/notifications#broadcast-notifications)을 읽으세요.

브로드캐스트 채널을 사용하는 것으로 알림을 설정하고나면, 에코의 `notification`메소드를 이용해 브로드캐스트 이벤트를 수신할 수 있습니다. 채널명은 알림을 받는 엔티티의 클래스명과 동일해야 합니다.

    Echo.private(`App.User.${userId}`)
        .notification((notification) => {
            console.log(notification.type);
        });

이 예제에서 `broadcast` 채널을 통해 `App\User` 인스턴스에 보내지는 모든 알림들은 콜백에 의해 받아집니다. `App.User.{id}` 채널에 대한 채널 승인 콜백이 라라벨 프레임워크와 함께 제공되는 기본 `BroadcastServiceProvider` 에 포함됩니다.
