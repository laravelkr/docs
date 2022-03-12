# 브로드캐스팅

- [시작하기](#introduction)
- [서버 측 설치](#server-side-installation)
    - [설정하기](#configuration)
    - [푸셔 채널](#pusher-channels)
    - [Ably](#ably)
    - [오픈 소스 대안](#open-source-alternatives)
- [클라이언트 측 설치](#client-side-installation)
    - [푸셔 채널](#client-pusher-channels)
    - [Ably](#client-ably)
- [컨셉 개요](#concept-overview)
    - [예제 애플리케이션 사용하기](#using-example-application)
- [브로드캐스트 이벤트 정의하기](#defining-broadcast-events)
    - [브로드캐스트 이름](#broadcast-name)
    - [브로드캐스트 데이터](#broadcast-data)
    - [브로드캐스트 큐](#broadcast-queue)
    - [브로드캐스트 조건](#broadcast-conditions)
    - [브로드캐스트 및 데이터베이스 트랜잭션](#broadcasting-and-database-transactions)
- [승인 채널](#authorizing-channels)
    - [승인 라우트 정의하기](#defining-authorization-routes)
    - [승인 콜백 정의하기](#defining-authorization-callbacks)
    - [채널 클래스 정의하기](#defining-channel-classes)
- [브로드캐스팅 이벤트](#broadcasting-events)
    - [오직 다른이에게만](#only-to-others)
    - [커넥션 커스터마이징](#customizing-the-connection)
- [브로드캐스트 수신하기](#receiving-broadcasts)
    - [이벤트 리스닝](#listening-for-events)
    - [채널 나가기](#leaving-a-channel)
    - [네임스페이스](#namespaces)
- [Presence 채널](#presence-channels)
    - [Presence 채널 승인하기](#authorizing-presence-channels)
    - [Presence 채널에 들어가기](#joining-presence-channels)
    - [Presence 채널에 브로드캐스트하기](#broadcasting-to-presence-channels)
- [모델 브로드캐스팅](#model-broadcasting)
    - [모델 브로드캐스팅 규약](#model-broadcasting-conventions)
    - [모델 브로드캐스팅 수신](#listening-for-model-broadcasts)
- [클라이언트 이벤트](#client-events)
- [알림](#notifications)

<a name="introduction"></a>
## 시작하기

많은 최신 웹 애플리케이션에서 WebSocket은 실시간, 라이브 업데이트되는 사용자 인터페이스를 구현하는 데 사용됩니다. 서버에서 일부 데이터가 업데이트되면 일반적으로 메시지가 WebSocket 연결을 통해 전송되어 클라이언트가 처리합니다. WebSocket은 UI에 반영되어야 하는 데이터 변경 사항에 대해 애플리케이션 서버를 지속적으로 폴링하는 보다 효율적인 대안을 제공합니다.

예를 들어, 애플리케이션이 사용자의 데이터를 CSV 파일로 내보내고 이메일로 보낼 수 있다고 상상해 보십시오. 그러나 이 CSV 파일을 만드는 데 몇 분이 걸리므로 [대기 중인 작업](/docs/{{version}}/queues) 내에서 CSV를 만들고 메일로 보내도록 선택합니다. CSV가 생성되어 사용자에게 메일이 발송되면 이벤트 브로드캐스팅을 사용하여 애플리케이션의 JavaScript에서 수신한 `App\Events\UserDataExported` 이벤트를 전달할 수 있습니다. 이벤트가 수신되면 페이지를 새로고침을 할 필요 없이, CSV가 이메일로 전송되었다는 메시지를 사용자에게 표시할 수 있습니다.

이러한 형태의 기능을 구축하는 데 도움이 되도록 라라벨은 WebSocket 연결을 통해 서버 측 라라벨 [이벤트](/docs/{{version}}/events)을 쉽게 "브로드캐스트"합니다. 라라벨 이벤트를 브로드캐스트하면 서버 측 라라벨 애플리케이션과 클라이언트 측 JavaScript 애플리케이션 간에 동일한 이벤트 이름과 데이터를 공유할 수 있습니다.

브로드캐스팅의 핵심 개념은 간단합니다. 클라이언트는 프론트엔드의 명명된 채널에 연결하고 라라벨 애플리케이션은 백엔드의 이러한 채널에 이벤트를 브로드캐스트합니다. 이러한 이벤트에는 프런트엔드에서 사용할 수 있도록 하려는 추가 데이터가 포함될 수 있습니다.

<a name="supported-drivers"></a>
#### 지원되는 드라이버

기본적으로 라라벨에는 [Pusher Channels](https://pusher.com/channels) 및 [Ably](https://ably.io) 중에서 선택할 수 있는 두 가지 서버 측 브로드캐스팅 드라이버가 포함되어 있습니다. 그러나 [laravel-websockets](https://beyondco.de/docs/laravel-websockets/getting-started/introduction) 및 [soketi](https://docs.soketi.app/) 와 같은 커뮤니티 기반 패키지는 상업용 브로드캐스팅 시스템이 필요하지 않은 추가적인 브로드캐스팅 시스템 드라이버를 제공합니다.

> {tip} 이벤트 브로드캐스팅을 시작하기 전에 [events and listeners](/docs/{{version}}/events)에 대한 라라벨 문서를 읽어보십시오.

<a name="server-side-installation"></a>
## 서버 측 설치

라라벨의 이벤트 브로드캐스팅을 시작하려면 라라벨 애플리케이션 내에서 몇 가지 환경설정을 수정하고 몇 가지 패키지를 설치해야 합니다.

이벤트 브로드캐스팅은 라라벨 Echo(JavaScript 라이브러리)가 브라우저 클라이언트 내에서 이벤트를 수신할 수 있도록, 라라벨 이벤트를 브로드캐스트하는 서버 측 브로드캐스팅 드라이버에 의해 수행됩니다. 걱정하지 마세요. 설치 프로세스의 각 부분을 단계별로 안내해 드리겠습니다.

<a name="configuration"></a>
### 설정하기

애플리케이션의 모든 이벤트 브로드캐스팅 환경설정은 `config/broadcasting.php` 설정 파일에 저장됩니다. 라라벨은 [Pusher Channels](https://pusher.com/channels), [Redis](/docs/{{version}}/redis) 및 로컬 개발 및 디버깅을 위한 `log` 드라이버와 같은 여러 브로드캐스트 드라이버를 즉시 지원합니다. 또한 테스트 중에 브로드캐스트를 완전히 비활성화할 수 있는 `null` 드라이버가 포함되어 있습니다. `config/broadcasting.php` 설정 파일에 이러한 드라이버들 각각에 대한 설정 예제가 포함되어 있습니다.

<a name="broadcast-service-provider"></a>
#### 브로드캐스트 서비스 프로바이더

이벤트를 브로드캐스팅하기 전에 먼저 `App\Providers\BroadcastServiceProvider`를 등록해야 합니다. 새로운 라라벨 애플리케이션에서는 `config/app.php` 설정 파일의 `providers` 배열에서 이 공급자의 주석을 제거하기만 하면 됩니다. 이 `BroadcastServiceProvider`에는 브로드캐스팅 인증 경로 및 콜백을 등록하는 데 필요한 코드가 포함되어 있습니다.

<a name="queue-configuration"></a>
#### 대기열 설정

또한 [대기열 작업자](/docs/{{version}}/queues)를 설정하고 실행해야 합니다. 모든 이벤트 브로드캐스팅은 대기 중인 작업을 통해 수행되므로 브로드캐스트되는 이벤트에 의해 애플리케이션의 응답 시간이 심각하게 영향을 받지 않습니다.

<a name="pusher-channels"></a>
### 푸셔 채널

[푸셔 채널](https://pusher.com/channels) 을 사용하여 이벤트를 브로드캐스트하려면, Composer 패키지 관리자를 사용하여 푸셔 채널 PHP SDK를 설치해야 합니다.

    composer require pusher/pusher-php-server

다음으로 `config/broadcasting.php` 설정 파일에서 푸셔 채널 자격 증명을 설정해야 합니다. 이 파일에는 푸셔 채널 설정의 예제가 이미 포함되어 있어서 key, secret 및 애플리케이션 ID를 빠르게 지정할 수 있습니다. 일반적으로 이러한 값은 `PUSHER_APP_KEY`, `PUSHER_APP_SECRET` 및 `PUSHER_APP_ID` [환경 변수](/docs/{{version}}/configuration#environment-configuration)를 통해 설정해야 합니다.

    PUSHER_APP_ID=your-pusher-app-id
    PUSHER_APP_KEY=your-pusher-key
    PUSHER_APP_SECRET=your-pusher-secret
    PUSHER_APP_CLUSTER=mt1

`config/broadcasting.php` 파일의 `pusher` 설정을 사용하면 클러스터와 같이 채널에서 지원하는 `options`을 추가로 지정할 수도 있습니다.

다음으로 `.env` 파일에서 브로드캐스트 드라이버를 `pusher`로 변경해야 합니다.

    BROADCAST_DRIVER=pusher

마지막으로 클라이언트 측에서 브로드캐스트 이벤트를 수신할 [Laravel Echo](#client-side-installation)를 설치하면 설정을 마무리 할 준비가 됩니다.

<a name="pusher-compatible-open-source-alternatives"></a>
#### 오픈 소스 푸셔 대안

[laravel-websockets](https://github.com/beyondcode/laravel-websockets) 와 [soketi](https://docs.soketi.app/) 패키지는 라라벨 푸셔와 호환되는 WebSocket 서버를 제공합니다. 이 패키지를 사용하면 상용 WebSocket 공급자 없이도 라라벨 브로드캐스팅의 모든 기능을 활용할 수 있습니다. 이러한 패키지 설치 및 사용에 대한 자세한 내용은 [오픈 소스 대안](#open-source-alternatives)에 대한 설명서를 참조하세요.

<a name="ably"></a>
### Ably

[Ably](https://ably.io)를 사용하여 이벤트를 브로드캐스트하려면 Composer 패키지 관리자를 사용하여 Ably PHP SDK를 설치해야 합니다.

    composer require ably/ably-php

다음으로 `config/broadcasting.php` 설정 파일에서 Ably 자격 증명을 설정해야 합니다. 이 파일에 Aly 설정의 예제가 이미 포함되어 있어 키를 빠르게 지정할 수 있습니다. 일반적으로 이 값은 `ABLY_KEY` [환경 변수](/docs/{{version}}/configuration#environment-configuration)를 통해 설정해야 합니다.

    ABLY_KEY=your-ably-key

다음으로 `.env` 파일에서 브로드캐스트 드라이버를 `ably`로 변경해야 합니다.

    BROADCAST_DRIVER=ably

마지막으로 클라이언트 측에서 브로드캐스트 이벤트를 수신할 [Laravel Echo](#client-side-installation)를 설치하면 설정을 마무리 할 준비가 됩니다.

<a name="open-source-alternatives"></a>
### 오픈 소스 대안

<a name="open-source-alternatives-php"></a>
#### PHP

[laravel-websockets](https://github.com/beyondcode/laravel-websockets) 패키지는 순수 PHP, 라라벨용 Pusher 호환 WebSocket 패키지입니다. 이 패키지를 사용하면 상용 WebSocket 공급자 없이도 라라벨 브로드캐스팅의 모든 기능을 활용할 수 있습니다. 이 패키지 설치 및 사용에 대한 자세한 내용은 [공식 문서](https://beyondco.de/docs/laravel-websockets) 를 참조하세요.

<a name="open-source-alternatives-node"></a>
#### Node

[Soketi](https://github.com/soketi/soketi) is a Node based, Pusher compatible WebSocket server for Laravel. Under the hood, Soketi utilizes µWebSockets.js for extreme scalability and speed. This package allows you to leverage the full power of Laravel broadcasting without a commercial WebSocket provider. For more information on installing and using this package, please consult its [official documentation](https://docs.soketi.app/).

[Soketi](https://github.com/soketi/soketi) 는 Node 기반의 라라벨용 Pusher 호환 WebSocket 서버입니다. 내부적으로 Soketi는 극도의 확장성과 속도를 위해 WebSockets.js를 활용합니다. 이 패키지를 사용하면 상용 WebSocket 공급자 없이도 라라벨 브로드캐스팅의 모든 기능을 활용할 수 있습니다. 이 패키지의 설치 및 사용에 대한 자세한 내용은 [공식 문서](https://docs.soketi.app)를 참조하십시오.

<a name="client-side-installation"></a>
## 클라이언트 측 설치

<a name="client-pusher-channels"></a>
### 푸셔 채널

[Laravel Echo](https://github.com/laravel/echo) 는 손쉽게 채널을 구독하고, 서버 측 브로드캐스팅 드라이버에서 브로드캐스트하는 이벤트를 수신할 수 있도록 하는 JavaScript 라이브러리입니다. NPM 패키지 관리자를 통해 Echo를 설치할 수 있습니다. 이 예제에서는 푸셔 채널 브로드캐스터를 사용할 것이기 때문에 `pusher-js` 패키지도 설치합니다.

```bash
npm install --save-dev laravel-echo pusher-js
```

Echo가 설치되면 애플리케이션의 JavaScript에서 새로운 Echo 인스턴스를 생성할 준비가 된 것입니다. 라라벨 프레임워크에 포함된 `resources/js/bootstrap.js` 파일의 맨 아래에 있습니다. 기본적으로 Echo 설정 예제가 이 파일에 이미 포함되어 있습니다. 주석 처리를 제거하기만 하면 됩니다.

```js
import Echo from 'laravel-echo';

window.Pusher = require('pusher-js');

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: process.env.MIX_PUSHER_APP_KEY,
    cluster: process.env.MIX_PUSHER_APP_CLUSTER,
    forceTLS: true
});
```

주석을 제거하고 필요에 따라 Echo 설정을 조정한 후에는 애플리케이션의 자산-assets을 컴파일할 수 있습니다.

    npm run dev

> {tip} 애플리케이션의 JavaScript 자산-assets 컴파일에 대한 자세한 내용은 [Laravel Mix](/docs/{{version}}/mix)에 대한 문서를 참조하세요.

<a name="using-an-existing-client-instance"></a>
#### 기존 클라이언트 인스턴스 사용

Echo에서 활용하고 싶은, 미리 설정된 Pusher Channels 클라이언트 인스턴스가 이미 있는 경우 `client` 설정 옵션을 통해 Echo에 전달할 수 있습니다.

```js
import Echo from 'laravel-echo';

const client = require('pusher-js');

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: 'your-pusher-channels-key',
    client: client
});
```

<a name="client-ably"></a>
### Ably

[Laravel Echo](https://github.com/laravel/echo) 는 손쉽게 채널을 구독하고, 서버 측 브로드캐스팅 드라이버에서 브로드캐스트하는 이벤트를 수신할 수 있도록 하는 JavaScript 라이브러리입니다. NPM 패키지 관리자를 통해 Echo를 설치할 수 있습니다. 이 예제에서는 `pusher-js` 패키지도 설치합니다.

우리가 이벤트를 브로드캐스트하기 위해 Ably를 사용하고 있음에도 불구하고 `pusher-js` JavaScript 라이브러리를 설치하는 이유가 궁금할 것입니다. 고맙게도 Ably에는 클라이언트 측 애플리케이션에서 이벤트를 수신 대기할 때 Pusher 프로토콜을 사용할 수 있는 Pusher 호환 모드가 포함되어 있습니다.

```bash
npm install --save-dev laravel-echo pusher-js
```

**계속하기 전에 Ably 애플리케이션 설정에서 푸셔 프로토콜 지원을 활성화해야 합니다. Ably 애플리케이션 설정 대시보드의 "프로토콜 어댑터 설정" 부분에서 이 기능을 활성화할 수 있습니다.**

Echo가 설치되면 애플리케이션의 JavaScript에서 새로운 Echo 인스턴스를 생성할 준비가 된 것입니다. 라라벨 프레임워크에 포함된 `resources/js/bootstrap.js` 파일의 맨 아래에 있습니다. 기본적으로 예제 Echo 설정은 이 파일에 이미 포함되어 있습니다. 그러나 `bootstrap.js` 파일의 기본 설정은 푸셔를 위한 것입니다. 아래 설정을 복사하여 설정을 Aly로 전환할 수 있습니다.

```js
import Echo from 'laravel-echo';

window.Pusher = require('pusher-js');

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: process.env.MIX_ABLY_PUBLIC_KEY,
    wsHost: 'realtime-pusher.ably.io',
    wsPort: 443,
    disableStats: true,
    encrypted: true,
});
```

Ably Echo 설정은 `MIX_ABLY_PUBLIC_KEY` 환경 변수를 참조합니다. 이 변수의 값은 Ably 공개 키여야 합니다. 공개 키는 `:` 문자 앞에 나오는 Ably 키 부분입니다.

주석을 제거하고 필요에 따라 Echo 설정을 조정한 후에는 애플리케이션의 자산-assets을 컴파일할 수 있습니다.

    npm run dev

> {tip} 애플리케이션의 JavaScript 자산-assets 컴파일에 대한 자세한 내용은 [Laravel Mix](/docs/{{version}}/mix)에 대한 문서를 참조하세요.

<a name="concept-overview"></a>
## 컨셉 개요

라라벨의 이벤트 브로드캐스팅을 사용하면 WebSocket에 대한 드라이버 기반 접근 방식을 사용하여 서버측 라라벨 이벤트를 클라이언트측 JavaScript 애플리케이션에 브로드캐스트할 수 있습니다. 현재 라라벨은 [Pusher Channels](https://pusher.com/channels) 및 [Ably](https://ably.io) 드라이버와 함께 제공됩니다. 이벤트는 [Laravel Echo](#client-side-installation) JavaScript 패키지를 사용하여 클라이언트 측에서 쉽게 사용할 수 있습니다.

이벤트는 공개 또는 비공개로 지정할 수 있는 "채널"을 통해 브로드캐스트됩니다. 애플리케이션 방문자는 인증이나 승인 없이 공개 채널을 구독할 수 있습니다. 그러나 개인 채널을 구독하려면 사용자가 인증을 받고 해당 채널에서 수신할 수 있는 권한이 있어야 합니다.

> {tip} 푸셔의 오픈 소스 대안을 탐색하고 싶다면 [오픈 소스 대안](#open-source-alternatives)을 확인하세요.

<a name="using-example-application"></a>
### 예제 애플리케이션 사용하기

이벤트 브로드캐스팅의 각 설정 값에 대해 자세히 알아보기 전에, 전자 상거래 상점을 예로 들어 높은 수준의 개요를 살펴보겠습니다.

우리 애플리케이션에서 사용자가 주문에 대한 배송 상태를 볼 수 있는 페이지가 있다고 가정해 보겠습니다. 또한 애플리케이션에서 배송 상태 업데이트를 처리할 때 `OrderShipmentStatusUpdated` 이벤트가 발생한다고 가정해 보겠습니다.

    use App\Events\OrderShipmentStatusUpdated;

    OrderShipmentStatusUpdated::dispatch($order);

<a name="the-shouldbroadcast-interface"></a>
#### `ShouldBroadcast` 인터페이스

사용자가 주문 중 하나를 볼 때, 상태가 변경 된 것을 확인하기 위해 페이지를 새로 고쳐야 하는 것을 원하지 않습니다. 대신 애플리케이션이 업데이트 될 때 브로드캐스트하려고 합니다. 따라서 `ShouldBroadcast` 인터페이스를 `OrderShipmentStatusUpdated` 이벤트에 추가해줘야 합니다. 이것은 라라벨이 이벤트가 발생할 때 이벤트를 브로드캐스트하도록 지시합니다.

    <?php

    namespace App\Events;

    use App\Order;
    use Illuminate\Broadcasting\Channel;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Broadcasting\PresenceChannel;
    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Queue\SerializesModels;

    class OrderShipmentStatusUpdated implements ShouldBroadcast
    {
        /**
         * The order instance.
         *
         * @var \App\Order
         */
        public $order;
    }

`ShouldBroadcast` 인터페이스는 `broadcastOn` 메소드를 정의하기 위해서 이벤트를 필요로 합니다. 이 메소드는 이벤트가 전송되어야 할 채널을 돌려주는 역할을 합니다. 생성된 이벤트 클래스에는 비어있는 메소드가 이미 정의되어 있기 때문에, 구체적인 내용만 채워넣으면 됩니다. 우리는 주문을 생성한 사람들만이 갱신된 상태를 볼 수 있도록 하고 싶으므로, 이 주문과 관련된 채널들에만 비공개적으로 이벤트를 전송할 것입니다.

    /**
     * Get the channels the event should broadcast on.
     *
     * @return \Illuminate\Broadcasting\PrivateChannel
     */
    public function broadcastOn()
    {
        return new PrivateChannel('orders.'.$this->order->id);
    }

<a name="example-application-authorizing-channels"></a>
#### 채널 인증하기

사용자는 private 채널에서 청취할 수 있는 권한이 있어야 합니다. 애플리케이션의 `routes/channels.php` 파일에서 채널 인증 규칙을 정의할 수 있습니다. 이 예제에서 우리는 비공개 채널 `order.1` 에서 수신을 시도하는 모든 사용자가 실제로 주문의 작성자인지 확인해야 합니다.

    use App\Models\Order;

    Broadcast::channel('orders.{orderId}', function ($user, $orderId) {
        return $user->id === Order::findOrNew($orderId)->user_id;
    });

`channel` 메소드는 두 개의 인자(채널명과 사용자가 채널을 들을 수 있도록 인증되었는지를 나타내는 `true` 혹은 `false`를 되돌려주는 콜백)를 받습니다.

모든 승인 콜백은 현재 인증된 사용자를 첫번째 인수로 받고, 다음 인수로 추가적인 와일드카드 파라미터들을 받습니다. 이 예제에서, 채널명의 "ID" 부분을 가리키기 위해 `{orderId}` 플레이스 홀더를 사용합니다.

<a name="listening-for-event-broadcasts"></a>
#### 이벤트 브로드캐스트 수신하기

다음으로 남은 것은 JavaScript 애플리케이션에서 이벤트를 수신하는 것뿐입니다. [Laravel Echo](#client-side-installation)를 사용하여 이 작업을 수행할 수 있습니다. 먼저 `private` 메서드를 사용하여 비공개 채널을 구독합니다. 그런 다음 `listen` 메소드를 사용하여 `OrderShipmentStatusUpdated` 이벤트를 수신할 수 있습니다. 기본적으로 이벤트의 모든 public 속성은 브로드캐스트 이벤트에 포함됩니다.

```js
Echo.private(`orders.${orderId}`)
    .listen('OrderShipmentStatusUpdated', (e) => {
        console.log(e.order);
    });
```

<a name="defining-broadcast-events"></a>
## 브로드캐스트 이벤트 정의하기

라라벨에 주어진 이벤트가 브로드캐스트되어야 한다는 것을 알리려면, 이벤트 클래스에 `Illuminate\Contracts\Broadcasting\ShouldBroadcast` 인터페이스를 구현해야 합니다. 이 인터페이스는 프레임워크에서 생성한 모든 이벤트 클래스에 이미 추가되어 있기 때문에, 모든 이벤트에 쉽게 추가할 수 있습니다.

`ShouldBroadcast` 인터페이스를 사용하려면 `broadcastOn` 메서드 하나만 구현하면 됩니다. `broadcastOn` 메서드는 이벤트가 브로드캐스트되어야 하는 채널 또는 채널 배열을 반환해야 합니다. 채널은 `Channel`, `PrivateChannel` 또는 `PresenceChannel`의 인스턴스여야 합니다. `Channel`의 인스턴스는 모든 사용자가 구독할 수 있는 public 채널을 나타내고 `PrivateChannels` 및 `PresenceChannels`는 [채널 인증](#authorizing-channels)이 필요한 private 채널을 나타냅니다.

    <?php

    namespace App\Events;

    use App\Models\User;
    use Illuminate\Broadcasting\Channel;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Broadcasting\PresenceChannel;
    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Queue\SerializesModels;

    class ServerCreated implements ShouldBroadcast
    {
        use SerializesModels;

        /**
         * The user that created the server.
         *
         * @var \App\Models\User
         */
        public $user;

        /**
         * Create a new event instance.
         *
         * @param  \App\Models\User  $user
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

`ShouldBroadcast` 인터페이스를 구현한 후에는 평소와 같이 [이벤트 발생](/docs/{{version}}/events)만 시키면 됩니다. 이벤트가 시작되면 [대기 중인 작업](/docs/{{version}}/queues)이 지정된 브로드캐스트 드라이버를 사용하여 이벤트를 자동으로 브로드캐스트합니다.

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

이벤트가 브로드캐스트될 때, 이벤트의 모든 `public` 속성들은 자동적으로 시리얼라이즈되어 이벤트의 데이터로 브로드캐스트되고, 자바스크립트 애플리케이션에서 이벤트의 모든 공개적인 데이터에 접근할 수 있도록 해줍니다. 그래서 예를들면, 이벤트가 엘로퀀트 모델을 담고 있는 `$user`라는 public 속성 하나를 가지고 있다면, 이벤트의 데이터는 다음과 같이 될 것입니다.

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

기본적으로 각 브로드캐스트 이벤트는 `queue.php` 설정 파일에 지정된 기본 대기열 연결의 기본 대기열에 배치됩니다. 이벤트 클래스에서 `connection` 및 `queue` 속성을 정의하여 브로드캐스터가 사용하는 대기열 연결 및 이름을 사용자 정의할 수 있습니다.

    /**
     * The name of the queue connection to use when broadcasting the event.
     *
     * @var string
     */
    public $connection = 'redis';

    /**
     * The name of the queue on which to place the broadcasting job.
     *
     * @var string
     */
    public $queue = 'default';

또는 이벤트에 `broadcastQueue` 메서드를 정의하여 대기열 이름을 사용자 정의할 수 있습니다.

    /**
     * The name of the queue on which to place the broadcasting job.
     *
     * @return string
     */
    public function broadcastQueue()
    {
        return 'default';
    }

기본 대기열 드라이버 대신 `sync` 대기열을 사용하여 이벤트를 브로드캐스트하려면 `ShouldBroadcast` 대신 `ShouldBroadcastNow` 인터페이스를 구현할 수 있습니다.

    <?php

    use Illuminate\Contracts\Broadcasting\ShouldBroadcastNow;

    class OrderShipmentStatusUpdated implements ShouldBroadcastNow
    {
        //
    }

<a name="broadcast-conditions"></a>
### 조건 브로드캐스트

때로는 특정 조건이 참일 경우만 이벤트를 브로드캐스팅하고 싶은 경우가 있습니다. 이러한 경우 이벤트 클래스에 `broadcastWhen` 메소드를 추가하여 브로드캐스팅을 실행 시키는 조건을 정의 할 수 있습니다.

    /**
     * Determine if this event should broadcast.
     *
     * @return bool
     */
    public function broadcastWhen()
    {
        return $this->order->value > 100;
    }

<a name="broadcasting-and-database-transactions"></a>
#### 브로드캐스팅 및 데이터베이스 거래

브로드캐스트 이벤트가 데이터베이스 트랜잭션 내에서 전달되면 데이터베이스 트랜잭션이 커밋되기 전에 큐에서 처리될 수 있습니다. 이 경우 데이터베이스 트랜잭션 중 모델 또는 데이터베이스 레코드에 대한 업데이트가 아직 데이터베이스에 반영되지 않을 수 있습니다. 또한 트랜잭션 내에서 생성된 모델 또는 데이터베이스 레코드는 데이터베이스에 존재하지 않을 수 있습니다. 이벤트가 이러한 모델에 종속된 경우 이벤트를 브로드캐스트하는 작업이 처리될 때 예기치 않은 오류가 발생할 수 있습니다.

대기열 연결의 `after_commit` 설정 옵션이 `false`로 설정된 경우, 이벤트 클래스에서 `afterCommit` 속성을 정의하여 모든 열린 데이터베이스 트랜잭션이 커밋된 후에 특정 브로드캐스트 이벤트가 전달되어야 함을 계속 표시할 수 있습니다.

    <?php

    namespace App\Events;

    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Queue\SerializesModels;

    class ServerCreated implements ShouldBroadcast
    {
        use SerializesModels;

        public $afterCommit = true;
    }

> {tip} 이러한 문제를 해결하는 방법에 대해 자세히 알아보려면 [대기 중인 작업 및 데이터베이스 트랜잭션](/docs/{{version}}/queues#jobs-and-database-transactions)에 관한 문서를 확인하세요.

<a name="authorizing-channels"></a>
## 채널 승인하기

비공개 채널을 사용하려면, 현재 인증된 사용자가 실제로 채널에서 청취할 수 있도록 승인해줘야 합니다. 이는 채널 이름으로 라라벨 애플리케이션에 HTTP 요청을 하고 애플리케이션이 사용자가 해당 채널에서 수신할 수 있는지 여부를 결정할 수 있도록 함으로써 이루어집니다. [Laravel Echo](#client-side-installation)를 사용할 때 비공개 채널에 대한 구독을 승인하기 위한 HTTP 요청이 자동으로 만들어집니다. 그러나 이러한 요청에 응답하려면 적절한 경로를 정의해야 합니다.

<a name="defining-authorization-routes"></a>
### 승인 라우트 정의하기

고맙게도 라라벨은 채널 승인 요청에 응답하기 위한 경로를 쉽게 정의할 수 있습니다. 라라벨 애플리케이션에 포함된 `App\Providers\BroadcastServiceProvider`에서 `Broadcast::routes` 메소드에 대한 호출을 볼 수 있습니다. 이 메소드는 승인 요청을 처리하기 위해 `/broadcasting/auth` 경로를 등록합니다.

    Broadcast::routes();

`Broadcast::routes` 메소드는 자동으로 라우트를 `web` 미들웨어 그룹에 위치시킬 것입니다. 그렇지만 할당된 속성들을 커스터마이징하기 위해서는 메소드에 라우트 속성 배열을 전달해줘야 합니다.

    Broadcast::routes($attributes);

<a name="customizing-the-authorization-endpoint"></a>
#### Authorization Endpoint의 커스터마이징

기본적으로 Echo는 `/broadcasting/auth` 라는 엔드포인트를 사용하여 채널 액세스 권한을 부여합니다. 그러나 Echo 인스턴스에 `authEndpoint` 설정 옵션을 전달하여 여러분 만의 인증 엔드포인트를 지정할 수 있습니다.

    window.Echo = new Echo({
        broadcaster: 'pusher',
        // ...
        authEndpoint: '/custom/endpoint/auth'
    });

<a name="customizing-the-authorization-request"></a>
#### 승인 요청 사용자 정의

Echo를 초기화할 때 사용자 지정 권한 부여자를 제공하여 Laravel Echo가 권한 부여 요청을 수행하는 방법을 사용자 지정할 수 있습니다.

    window.Echo = new Echo({
        // ...
        authorizer: (channel, options) => {
            return {
                authorize: (socketId, callback) => {
                    axios.post('/api/broadcasting/auth', {
                        socket_id: socketId,
                        channel_name: channel.name
                    })
                    .then(response => {
                        callback(false, response.data);
                    })
                    .catch(error => {
                        callback(true, error);
                    });
                }
            };
        },
    })

<a name="defining-authorization-callbacks"></a>
### 승인 콜백 정의하기

다음으로, 현재 인증된 사용자가 주어진 채널을 수신할 수 있는지 여부를 실제로 결정하는 로직을 정의해야 합니다. 이것은 애플리케이션에 포함된 `routes/channels.php` 파일에서 수행됩니다. 이 파일에서 `Broadcast::channel` 메소드를 사용하여 채널 인증 콜백을 등록할 수 있습니다.

    Broadcast::channel('orders.{orderId}', function ($user, $orderId) {
        return $user->id === Order::findOrNew($orderId)->user_id;
    });

`channel` 메소드는 두 개의 인수(채널명과 사용자가 채널을 수신하도록 승인되었는지 여부를 나타내는 `true` 혹은 `false` 값을 리턴하는 콜백)를 받습니다.

모든 권한 부여 콜백은 현재 인증된 사용자를 첫 번째 인수로 수신하고 추가 와일드카드 매개변수를 후속 인수로 수신합니다. 이 예에서는 `{orderId}` 자리 표시자를 사용하여 채널 이름의 "ID" 부분이 와일드카드임을 나타냅니다.

<a name="authorization-callback-model-binding"></a>
#### 승인 콜백의 모델 바인딩

HTTP 경로와 마찬가지로 채널 경로도 암시적 및 명시적 [경로 모델 바인딩](/docs/{{version}}/routing#route-model-binding)을 활용할 수 있습니다. 예를 들어 문자열이나 숫자 주문 ID를 받는 대신 실제 `Order` 모델 인스턴스를 요청할 수 있습니다.

    use App\Models\Order;

    Broadcast::channel('orders.{order}', function ($user, Order $order) {
        return $user->id === $order->user_id;
    });

> {note} HTTP 경로 모델 바인딩과 달리 채널 모델 바인딩은 자동 [암시적 모델 바인딩 범위 지정](/docs/{{version}}/routing#implicit-model-binding-scoping)을 지원하지 않습니다. 그러나 대부분의 채널이 단일 모델의 고유한 기본 키를 기반으로 범위가 지정될 수 있기 때문에 이는 거의 문제가 되지 않습니다.

<a name="authorization-callback-authentication"></a>
#### 인증 권한 부여 콜백

Private 및 presence 브로드캐스트 채널은 애플리케이션의 기본 인증 가드를 통해 현재 사용자를 인증합니다. 사용자가 인증되지 않으면 채널 권한이 자동으로 거부되고 권한 콜백이 실행되지 않습니다. 그러나 필요한 경우 들어오는 Request에 인증해야하는 사용자 지정 가드를 여러 개 지정할 수 있습니다.

    Broadcast::channel('channel', function () {
        // ...
    }, ['guards' => ['web', 'admin']]);

<a name="defining-channel-classes"></a>
### 채널 클래스 정의하기

애플리케이션이 다양한 채널을 사용하는 경우 `routes/channels.php` 파일이 크기가 커질 수 있습니다. 따라서 클로저를 사용하여 채널을 승인하는 대신 채널 클래스를 사용할 수 있습니다. 채널 클래스를 생성하려면 `make:channel` Artisan 명령을 사용하세요. 이 명령은 `AppBroadcasting` 디렉토리에 새 채널 클래스를 배치합니다.

    php artisan make:channel OrderChannel

그 다음, `routes/channels.php` 파일에 생성된 채널을 등록할 수 있습니다.

    use App\Broadcasting\OrderChannel;

    Broadcast::channel('orders.{order}', OrderChannel::class);

마지막으로 채널 클래스의 `join` 메서드에 채널에 대한 권한 부여 논리를 배치할 수 있습니다. 이 `join` 메소드는 일반적으로 채널 승인 클로저에 배치하는 것과 동일한 논리를 수용합니다. 채널 모델 바인딩을 활용할 수도 있습니다.

    <?php

    namespace App\Broadcasting;

    use App\Models\Order;
    use App\Models\User;

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
         * @param  \App\Models\User  $user
         * @param  \App\Models\Order  $order
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

이벤트를 정의하고 `ShouldBroadcast` 인터페이스로 표시한 후에는 이벤트의 디스패치 메서드를 사용하여 이벤트를 시작하기만 하면 됩니다. 이벤트 디스패처는 이벤트가 `ShouldBroadcast` 인터페이스로 표시되어 있음을 확인하고 브로드캐스팅을 위해 이벤트를 대기열에 넣습니다.

    use App\Events\OrderShipmentStatusUpdated;

    OrderShipmentStatusUpdated::dispatch($order);

<a name="only-to-others"></a>
### 오직 다른이에게만

이벤트 브로드캐스팅을 활용하는 애플리케이션을 구축할 때 현재 사용자를 제외한 특정 채널의 모든 구독자에게 이벤트를 브로드캐스트해야 하는 경우가 있습니다. `broadcast` 헬퍼와 `toOthers` 메서드를 사용하여 이 작업을 수행할 수 있습니다.

    use App\Events\OrderShipmentStatusUpdated;

    broadcast(new OrderShipmentStatusUpdated($update))->toOthers();

`toOthers` 메서드를 사용하려는 경우를 더 잘 이해하기 위해, 사용자가 작업 이름을 입력하여 새 작업을 생성할 수 있는 작업 목록 애플리케이션을 상상해 보겠습니다. 작업을 생성하기 위해 애플리케이션은 작업 생성을 브로드캐스트하고 새 작업의 JSON 표현을 반환하는 `/task` URL에 요청을 보낼 수 있습니다. JavaScript 애플리케이션이 엔드포인트에서 응답을 받으면 다음과 같이 새 작업을 작업 목록에 직접 삽입할 수 있습니다.

    axios.post('/task', task)
        .then((response) => {
            this.tasks.push(response.data);
        });

그러나 태스크 생성도 브로드캐스트한다는 것을 기억하십시오. JavaScript 애플리케이션이 작업 목록에 작업을 추가하기 위해 이 이벤트도 수신하는 경우 목록에 중복 작업이 있을 것입니다. 하나는 엔드포인트에서, 다른 하나는 브로드캐스트에서입니다. 브로드캐스터가 현재 사용자에게 이벤트를 브로드캐스트하지 않도록 지시하는 `toOthers` 메서드를 사용하여 이 문제를 해결할 수 있습니다.

> {note} `toOthers` 메소드를 호출할려면 반드시 이벤트에 `Illuminate\Broadcasting\InteractsWithSockets` 트레이트를 사용해야 합니다.

<a name="only-to-others-configuration"></a>
#### 설정

Laravel Echo 인스턴스를 초기화할 때 소켓 ID가 연결에 할당됩니다. 글로벌 [Axios](https://github.com/mzabriskie/axios) 인스턴스를 사용하여 JavaScript 애플리케이션에서 HTTP 요청을 만드는 경우 소켓 ID는 자동으로 모든 발신 요청에 `X-Socket-ID` 헤더로 첨부됩니다. 그런 다음 `toOthers` 메소드를 호출하면 라라벨은 헤더에서 소켓 ID를 추출하고 브로드캐스터에게 해당 소켓 ID를 가진 연결로 브로드캐스트하지 않도록 지시합니다.

전역 Axios 인스턴스를 사용하지 않는 경우 모든 발신 요청과 함께 'X-Socket-ID' 헤더를 보내도록 JavaScript 애플리케이션을 수동으로 설정해야 합니다. `Echo.socketId` 메소드를 사용하여 소켓 ID를 검색할 수 있습니다.

    var socketId = Echo.socketId();

<a name="customizing-the-connection"></a>
### 연결 사용자 정의

애플리케이션이 여러 브로드캐스트 연결과 상호 작용하고 기본값이 아닌 브로드캐스터를 사용하여 이벤트를 브로드캐스트하려는 경우 `via` 메서드를 사용하여 이벤트를 푸시할 연결을 지정할 수 있습니다.

    use App\Events\OrderShipmentStatusUpdated;

    broadcast(new OrderShipmentStatusUpdated($update))->via('pusher');

또는 이벤트 생성자 내에서 `broadcastVia` 메서드를 호출하여 이벤트의 브로드캐스트 연결을 지정할 수 있습니다. 그러나 그렇게 하기 전에 이벤트 클래스가 `InteractsWithBroadcasting` 특성-Trait을 사용하는지 확인해야 합니다.

    <?php

    namespace App\Events;

    use Illuminate\Broadcasting\Channel;
    use Illuminate\Broadcasting\InteractsWithBroadcasting;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Broadcasting\PresenceChannel;
    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Queue\SerializesModels;

    class OrderShipmentStatusUpdated implements ShouldBroadcast
    {
        use InteractsWithBroadcasting;

        /**
         * Create a new event instance.
         *
         * @return void
         */
        public function __construct()
        {
            $this->broadcastVia('pusher');
        }
    }

<a name="receiving-broadcasts"></a>
## 브로드캐스트 받기

<a name="listening-for-events"></a>
### 이벤트 수신하기

[Laravel Echo를 설치 및 인스턴스화](#client-side-installation)하면 라라벨 애플리케이션에서 브로드캐스트되는 이벤트 수신을 시작할 준비가 된 것입니다. 먼저 `channel` 메서드를 사용하여 채널의 인스턴스를 검색한 다음 `listen` 메서드를 호출하여 지정된 이벤트를 수신합니다.

```js
Echo.channel(`orders.${this.order.id}`)
    .listen('OrderShipmentStatusUpdated', (e) => {
        console.log(e.order.name);
    });
```

비공개 채널에서 이벤트를 수신하고 싶으면, `private` 메소드를 사용하세요. 하나의 채널에있는 여러 이벤트를 수신하고 싶으면 `listen` 메소드를 연결해서 사용할 수 있습니다.

```js
Echo.private(`orders.${this.order.id}`)
    .listen(...)
    .listen(...)
    .listen(...);
```

<a name="stop-listening-for-events"></a>
#### 이벤트 수신 중지

[채널을 떠나지 않고](#leaving-a-channel) 주어진 이벤트에 대한 청취를 중단하려면 `stopListening` 메서드를 사용할 수 있습니다.

```js
Echo.private(`orders.${this.order.id}`)
    .stopListening('OrderShipmentStatusUpdated')
```

<a name="leaving-a-channel"></a>
### 채널 나가기

채널을 나가기 위해서는, 에코 인스턴스에서 `leaveChannel` 메소드를 호출하면 됩니다.

```js
Echo.leaveChannel(`orders.${this.order.id}`);
```

채널을 나가면서 연관된 비공개 현재 채널 또한 나가려면, 다음과 같이 `leave` 메소드를 호출하면 됩니다.

```js
Echo.leave(`orders.${this.order.id}`);
```
<a name="namespaces"></a>
### 네임스페이스

위의 예에서 이벤트 클래스에 대한 전체 `App\Events` 네임스페이스를 지정하지 않았음을 알 수 있습니다. 이는 Echo가 `App\Events` 네임스페이스에 이벤트가 있다고 자동으로 가정하기 때문입니다. 그러나 `namespace` 설정 옵션을 전달하여 Echo를 인스턴스화할 때 루트 네임스페이스를 설정할 수 있습니다.

```js
window.Echo = new Echo({
    broadcaster: 'pusher',
    // ...
    namespace: 'App.Other.Namespace'
});
```

이 대신에, 에코를 이용하여 이벤트를 구독할 때 이벤트 클래스 앞에 `.`를 붙일 수 있습니다. 이렇게 하면 항상 정규화된 클래스명을 명시할 수 있습니다.

```js
Echo.channel('orders')
    .listen('.Namespace\\Event\\Class', (e) => {
        //
    });
```

<a name="presence-channels"></a>
## 프레젠스 채널

프레젠스 채널은 비공개 채널의 보안을 기반으로 구축되는 동시에 채널을 구독하는 사람을 인식하는 추가 기능을 노출합니다. 이를 통해 다른 사용자가 같은 페이지를 보고 있을 때 사용자에게 알리거나 대화방의 거주자를 나열하는 것과 같은 강력한 협업 애플리케이션 기능을 쉽게 구축할 수 있습니다.

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
        })
        .error((error) => {
            console.error(error);
        });

`here` 콜백은 채널이 성공적으로 연결되면 즉시 실행되며 현재 채널을 구독하고 있는 다른 모든 사용자에 대한 사용자 정보가 포함된 배열을 수신합니다. `joining` 메서드는 새로운 사용자가 채널에 합류할 때 실행되고, `leaving` 메서드는 사용자가 채널을 떠날 때 실행됩니다. `error` 메서드는 인증 엔드포인트가 200이 아닌 HTTP 상태 코드를 반환하거나 반환된 JSON을 구문 분석하는 데 문제가 있는 경우 실행됩니다.

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

다른 이벤트와 마찬가지로 `broadcast` 헬퍼와 `toOthers` 메서드를 사용하여 현재 사용자가 브로드캐스트를 수신하지 못하도록 제외할 수 있습니다.

    broadcast(new NewMessage($message));

    broadcast(new NewMessage($message))->toOthers();

다른 유형의 이벤트와 마찬가지로 Echo의 `listen` 메서드를 사용하여 현재 상태 채널로 전송된 이벤트를 수신할 수 있습니다.

    Echo.join(`chat.${roomId}`)
        .here(...)
        .joining(...)
        .leaving(...)
        .listen('NewMessage', (e) => {
            //
        });

<a name="model-broadcasting"></a>
## 모델 브로드캐스팅

> {note} 모델 브로드캐스팅에 대한 다음 문서를 읽기 전에 라라벨의 모델 브로드캐스팅 서비스에 대한 일반적인 개념과 브로드캐스트 이벤트를 수동으로 생성하고 수신하는 방법을 숙지하는 것이 좋습니다.

애플리케이션의 [Eloquent 모델](/docs/{{version}}/eloquent)이 생성, 업데이트 또는 삭제될 때 이벤트를 브로드캐스트하는 것이 일반적입니다. 물론 이것은 수동으로 [Eloquent 모델 상태 변경에 대한 사용자 정의 이벤트 정의](/docs/{{version}}/eloquent#events) 및 `ShouldBroadcast` 인터페이스로 해당 이벤트를 표시하여 쉽게 수행할 수 있습니다.

그러나 이러한 이벤트를 애플리케이션에서 다른 목적으로 사용하지 않는 경우, 브로드캐스트할 목적으로만 이벤트 클래스를 만드는 것은 번거로울 수 있습니다. 이 문제를 해결하기 위해 라라벨은 Eloquent 모델이 자동으로 상태 변경을 브로드캐스트해야 함을 나타낼 수 있습니다.

시작하려면 Eloquent 모델이 `Illuminate\Database\Eloquent\BroadcastsEvents` 특성-Trait을 사용해야 합니다. 또한 모델은 `broadcastsOn` 메서드를 정의해야 합니다. 이 메서드는 모델의 이벤트가 브로드캐스트해야 하는 채널 배열을 반환합니다.

```php
<?php

namespace App\Models;

use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Database\Eloquent\BroadcastsEvents;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    use BroadcastsEvents, HasFactory;

    /**
     * Get the user that the post belongs to.
     */
    public function user()
    {
        return $this->belongsTo(User::class);
    }

    /**
     * Get the channels that model events should broadcast on.
     *
     * @param  string  $event
     * @return \Illuminate\Broadcasting\Channel|array
     */
    public function broadcastOn($event)
    {
        return [$this, $this->user];
    }
}
```

모델에 이 특성-trait이 포함되고 브로드캐스트 채널이 정의되면 모델 인스턴스가 생성, 업데이트, 삭제, 휴지통 또는 복원될 때 이벤트 브로드캐스트가 자동으로 시작됩니다.

또한 `broadcastOn` 메소드가 문자열 `event` 인수를 수신하는 것을 눈치채셨을 것입니다. 이 인수는 모델에서 발생한 이벤트 유형을 포함하며 `created`, `updated`, `deleted`, `trashed` 또는 `restored` 값을 갖습니다. 이 변수의 값을 검사하여 특정 이벤트에 대해 모델이 브로드캐스트해야 하는 채널(있는 경우)을 결정할 수 있습니다.

```php
/**
 * Get the channels that model events should broadcast on.
 *
 * @param  string  $event
 * @return \Illuminate\Broadcasting\Channel|array
 */
public function broadcastOn($event)
{
    return match ($event) {
        'deleted' => [],
        default => [$this, $this->user],
    };
}
```

<a name="customizing-model-broadcasting-event-creation"></a>
#### 모델 브로드캐스팅 이벤트 생성 커스터마이징

경우에 따라 라라벨이 기본 모델 브로드캐스팅 이벤트를 생성하는 방법을 사용자 정의하고 싶을 수 있습니다. Eloquent 모델에서 `newBroadcastableEvent` 메소드를 정의하여 이를 수행할 수 있습니다. 이 메소드는 `Illuminate\Database\Eloquent\BroadcastableModelEventOccurred` 인스턴스를 반환해야 합니다.

```php
use Illuminate\Database\Eloquent\BroadcastableModelEventOccurred

/**
 * Create a new broadcastable model event for the model.
 *
 * @param  string  $event
 * @return \Illuminate\Database\Eloquent\BroadcastableModelEventOccurred
 */
protected function newBroadcastableEvent($event)
{
    return (new BroadcastableModelEventOccurred(
        $this, $event
    ))->dontBroadcastToCurrentUser();
}
```

<a name="model-broadcasting-conventions"></a>
### 모델 브로드캐스팅 규약

<a name="model-broadcasting-channel-conventions"></a>
#### 채널 규칙

눈치채셨겠지만 위의 모델 예시에서 `broadcastOn` 메소드는 `Channel` 인스턴스를 반환하지 않았습니다. 대신 Eloquent 모델이 직접 반환되었습니다. Eloquent 모델 인스턴스가 모델의 `broadcastOn` 메소드에 의해 반환되는 경우(또는 메소드에 의해 반환된 배열에 포함된 경우), 라라벨은 모델의 클래스 이름과 기본 키 식별자를 채널로 사용하여 모델에 대한 개인 채널 인스턴스를 자동으로 인스턴스화합니다.

따라서 `id`가 `1`인 `App\Models\User` 모델은 이름이 `App.Models.User.1`인 `Illuminate\Broadcasting\PrivateChannel` 인스턴스로 변환됩니다. 물론 모델의 `broadcastOn` 메소드에서 Eloquent 모델 인스턴스를 반환하는 것 외에도 모델의 채널 이름을 완전히 제어하기 위해 완전한 `Channel` 인스턴스를 반환할 수 있습니다.

```php
use Illuminate\Broadcasting\PrivateChannel;

/**
 * Get the channels that model events should broadcast on.
 *
 * @param  string  $event
 * @return \Illuminate\Broadcasting\Channel|array
 */
public function broadcastOn($event)
{
    return [new PrivateChannel('user.'.$this->id)];
}
```

모델의 `broadcastOn` 메서드에서 채널 인스턴스를 명시적으로 반환하려는 경우 Eloquent 모델 인스턴스를 채널 생성자에 전달할 수 있습니다. 그렇게 할 때 라라벨은 위에서 논의한 모델 채널 규칙을 사용하여 Eloquent 모델을 채널 이름 문자열로 변환합니다.

```php
return [new Channel($this->user)];
```

모델의 채널 이름을 확인해야 하는 경우 모든 모델 인스턴스에서 `broadcastChannel` 메서드를 호출할 수 있습니다. 예를 들어 이 메소드는 `id`가 `1`인 `App\Models\User` 모델에 대해 `App.Models.User.1` 문자열을 반환합니다.

```php
$user->broadcastChannel()
```

<a name="model-broadcasting-event-conventions"></a>
#### 이벤트 컨벤션

모델 브로드캐스트 이벤트는 애플리케이션의 `App\Events` 디렉터리 내 "실제" 이벤트와 연결되지 않으므로 규칙에 따라 이름과 페이로드가 할당됩니다. 라라벨의 규칙은 모델의 클래스 이름(네임스페이스 제외)과 브로드캐스트를 트리거한 모델 이벤트의 이름을 사용하여 이벤트를 브로드캐스트하는 것입니다.

따라서 예를 들어 `App\Models\Post` 모델에 대한 업데이트는 다음 페이로드와 함께 `PostUpdated`로 클라이언트 측 애플리케이션에 이벤트를 브로드캐스트합니다.

    {
        "model": {
            "id": 1,
            "title": "My first post"
            ...
        },
        ...
        "socket": "someSocketId",
    }

`App\Models\User` 모델을 삭제하면 `UserDeleted`라는 이벤트가 브로드캐스트됩니다.

원하는 경우 모델에 `broadcastAs` 및 `broadcastWith` 메소드를 추가하여 사용자 정의 브로드캐스트 이름과 페이로드를 정의할 수 있습니다. 이러한 메서드는 발생하는 모델 이벤트/작업의 이름을 수신하므로 각 모델 작업에 대한 이벤트 이름과 페이로드를 사용자 지정할 수 있습니다. `broadcastAs` 메소드에서 `null`이 반환되면 라라벨은 이벤트를 브로드캐스트할 때 위에서 논의한 모델 브로드캐스트 이벤트 이름 규칙을 사용합니다.

```php
/**
 * The model event's broadcast name.
 *
 * @param  string  $event
 * @return string|null
 */
public function broadcastAs($event)
{
    return match ($event) {
        'created' => 'post.created',
        default => null,
    };
}

/**
 * Get the data to broadcast for the model.
 *
 * @param  string  $event
 * @return array
 */
public function broadcastWith($event)
{
    return match ($event) {
        'created' => ['title' => $this->title],
        default => ['model' => $this],
    };
}
```

<a name="listening-for-model-broadcasts"></a>
### 모델 브로드캐스팅 듣기

모델에 `BroadcastsEvents` 특성-trait을 추가하고 모델의 `broadcastOn` 메서드를 정의하면 클라이언트 측 애플리케이션 내에서 브로드캐스트된 모델 이벤트 수신을 시작할 준비가 된 것입니다. 시작하기 전에 [이벤트 수신](#listening-for-events)에 대한 전체 문서를 참조할 수 있습니다.

먼저 `private` 메서드를 사용하여 채널의 인스턴스를 검색한 다음 `listen` 메서드를 호출하여 지정된 이벤트를 수신 대기합니다. 일반적으로 `private` 메소드에 부여되는 채널 이름은 라라벨의 [모델 브로드캐스팅 규약](#model-broadcasting-conventions)과 일치해야 합니다.

채널 인스턴스를 얻으면 `listen` 메소드를 사용하여 특정 이벤트를 수신할 수 있습니다. 모델 브로드캐스트 이벤트는 애플리케이션의 `App\Events` 디렉터리 내 "실제" 이벤트와 연결되어 있지 않기 때문에 [이벤트 이름](#model-broadcasting-event-conventions) 앞에 '.'가 붙어야 하고 이것은 특정 네임스페이스에 속하지 않습니다. 각 모델 브로드캐스트 이벤트에는 모델의 브로드캐스트 가능한 모든 속성이 포함된 `model` 속성이 있습니다.

```js
Echo.private(`App.Models.User.${this.user.id}`)
    .listen('.PostUpdated', (e) => {
        console.log(e.model);
    });
```

<a name="client-events"></a>
## 클라이언트 이벤트

> {tip} [Pusher Channels](https://pusher.com/channels) 를 사용하는 경우, 클라이언트 이벤트를 전송하려면 [애플리케이션 대시 보드](https://dashboard.pusher.com/) 의 "App Settings"섹션에서 "Client Events" 옵션을 활성화해야합니다.

때로는 라라벨 애플리케이션을 거치지 않고, 연결된 다른 클라이언트에게 이벤트를 브로드캐스트 해야할 수도 있습니다. 이는 특정한 경우 유용할 수 있는데, 어떤 사용자가 화면에 메세지를 "입력"하고 있다는 것을 다른 사용자에게 알리는 경우가 그렇습니다. 

클라이언트 이벤트를 브로드 캐스트하려면, Echo의 `whisper` 메소드를 사용하면 됩니다.

    Echo.private(`chat.${roomId}`)
        .whisper('typing', {
            name: this.user.name
        });

클라이언트 이벤트를 수신하려면, `listenForWhisper` 메소드를 사용하면 됩니다.

    Echo.private(`chat.${roomId}`)
        .listenForWhisper('typing', (e) => {
            console.log(e.name);
        });

<a name="notifications"></a>
## 알림

이벤트 브로드캐스팅을 [notifications](/docs/{{version}}/notifications)와 페어링하면 JavaScript 애플리케이션이 페이지를 새로 고칠 필요 없이 새로운 알림을 수신할 수 있습니다. 시작하기 전에 [브로드캐스트 알림 채널](/docs/{{version}}/notifications#broadcast-notifications) 사용에 대한 설명서를 읽어보세요.

브로드캐스트 채널을 사용하는 것으로 알림을 설정하고나면, 에코의 `notification`메소드를 이용해 브로드캐스트 이벤트를 수신할 수 있습니다. 채널명은 알림을 받는 엔티티의 클래스명과 동일해야 합니다.

    Echo.private(`App.Models.User.${userId}`)
        .notification((notification) => {
            console.log(notification.type);
        });

이 예제에서 `broadcast` 채널을 통해 `App\Models\User` 인스턴스로 전송된 모든 알림은 콜백에서 수신됩니다. `App.Models.User.{id}` 채널에 대한 채널 인증 콜백은 라라벨 프레임워크와 함께 제공되는 기본 `BroadcastServiceProvider`에 포함되어 있습니다.