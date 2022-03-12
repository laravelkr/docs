# Broadcasting
# 브로드캐스팅

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Server Side Installation](#server-side-installation)
- [서버 측 설치](#server-side-installation)
    - [Configuration](#configuration)
    - [설정하기](#configuration)
    - [Pusher Channels](#pusher-channels)
    - [푸셔 채널](#pusher-channels)
    - [Ably](#ably)
    - [Ably](#ably)
    - [Open Source Alternatives](#open-source-alternatives)
    - [오픈 소스 대안](#open-source-alternatives)
- [Client Side Installation](#client-side-installation)
- [클라이언트 측 설치](#client-side-installation)
    - [Pusher Channels](#client-pusher-channels)
    - [푸셔 채널](#client-pusher-channels)
    - [Ably](#client-ably)
    - [Ably](#client-ably)
- [Concept Overview](#concept-overview)
- [컨셉 개요](#concept-overview)
    - [Using An Example Application](#using-example-application)
    - [예제 애플리케이션 사용하기](#using-example-application)
- [Defining Broadcast Events](#defining-broadcast-events)
- [브로드캐스트 이벤트 정의하기](#defining-broadcast-events)
    - [Broadcast Name](#broadcast-name)
    - [브로드캐스트 이름](#broadcast-name)
    - [Broadcast Data](#broadcast-data)
    - [브로드캐스트 데이터](#broadcast-data)
    - [Broadcast Queue](#broadcast-queue)
    - [브로드캐스트 큐](#broadcast-queue)
    - [Broadcast Conditions](#broadcast-conditions)
    - [브로드캐스트 조건](#broadcast-conditions)
    - [Broadcasting & Database Transactions](#broadcasting-and-database-transactions)
    - [브로드캐스트 및 데이터베이스 트랜잭션](#broadcasting-and-database-transactions)
- [Authorizing Channels](#authorizing-channels)
- [승인 채널](#authorizing-channels)
    - [Defining Authorization Routes](#defining-authorization-routes)
    - [승인 라우트 정의하기](#defining-authorization-routes)
    - [Defining Authorization Callbacks](#defining-authorization-callbacks)
    - [승인 콜백 정의하기](#defining-authorization-callbacks)
    - [Defining Channel Classes](#defining-channel-classes)
    - [채널 클래스 정의하기](#defining-channel-classes)
- [Broadcasting Events](#broadcasting-events)
- [브로드캐스팅 이벤트](#broadcasting-events)
    - [Only To Others](#only-to-others)
    - [오직 다른이에게만](#only-to-others)
    - [Customizing The Connection](#customizing-the-connection)
    - [커넥션 커스터마이징](#customizing-the-connection)
- [Receiving Broadcasts](#receiving-broadcasts)
- [브로드캐스트 수신하기](#receiving-broadcasts)
    - [Listening For Events](#listening-for-events)
    - [이벤트 리스닝](#listening-for-events)
    - [Leaving A Channel](#leaving-a-channel)
    - [채널 나가기](#leaving-a-channel)
    - [Namespaces](#namespaces)
    - [네임스페이스](#namespaces)
- [Presence Channels](#presence-channels)
- [Presence 채널](#presence-channels)
    - [Authorizing Presence Channels](#authorizing-presence-channels)
    - [Presence 채널 승인하기](#authorizing-presence-channels)
    - [Joining Presence Channels](#joining-presence-channels)
    - [Presence 채널에 들어가기](#joining-presence-channels)
    - [Broadcasting To Presence Channels](#broadcasting-to-presence-channels)
    - [Presence 채널에 브로드캐스트하기](#broadcasting-to-presence-channels)
- [Model Broadcasting](#model-broadcasting)
- [모델 브로드캐스팅](#model-broadcasting)
    - [Model Broadcasting Conventions](#model-broadcasting-conventions)
    - [모델 브로드캐스팅 규약](#model-broadcasting-conventions)
    - [Listening For Model Broadcasts](#listening-for-model-broadcasts)
    - [모델 브로드캐스팅 수신](#listening-for-model-broadcasts)
- [Client Events](#client-events)
- [클라이언트 이벤트](#client-events)
- [Notifications](#notifications)
- [알림](#notifications)

<a name="introduction"></a>
## Introduction
## 시작하기

In many modern web applications, WebSockets are used to implement realtime, live-updating user interfaces. When some data is updated on the server, a message is typically sent over a WebSocket connection to be handled by the client. WebSockets provide a more efficient alternative to continually polling your application's server for data changes that should be reflected in your UI.

많은 최신 웹 애플리케이션에서 WebSocket은 실시간, 라이브 업데이트되는 사용자 인터페이스를 구현하는 데 사용됩니다. 서버에서 일부 데이터가 업데이트되면 일반적으로 메시지가 WebSocket 연결을 통해 전송되어 클라이언트가 처리합니다. WebSocket은 UI에 반영되어야 하는 데이터 변경 사항에 대해 애플리케이션 서버를 지속적으로 폴링하는 보다 효율적인 대안을 제공합니다.

For example, imagine your application is able to export a user's data to a CSV file and email it to them. However, creating this CSV file takes several minutes so you choose to create and mail the CSV within a [queued job](/docs/{{version}}/queues). When the CSV has been created and mailed to the user, we can use event broadcasting to dispatch a `App\Events\UserDataExported` event that is received by our application's JavaScript. Once the event is received, we can display a message to the user that their CSV has been emailed to them without them ever needing to refresh the page.

예를 들어, 애플리케이션이 사용자의 데이터를 CSV 파일로 내보내고 이메일로 보낼 수 있다고 상상해 보십시오. 그러나 이 CSV 파일을 만드는 데 몇 분이 걸리므로 [대기 중인 작업](/docs/{{version}}/queues) 내에서 CSV를 만들고 메일로 보내도록 선택합니다. CSV가 생성되어 사용자에게 메일이 발송되면 이벤트 브로드캐스팅을 사용하여 애플리케이션의 JavaScript에서 수신한 `App\Events\UserDataExported` 이벤트를 전달할 수 있습니다. 이벤트가 수신되면 페이지를 새로고침을 할 필요 없이, CSV가 이메일로 전송되었다는 메시지를 사용자에게 표시할 수 있습니다.

To assist you in building these types of features, Laravel makes it easy to "broadcast" your server-side Laravel [events](/docs/{{version}}/events) over a WebSocket connection. Broadcasting your Laravel events allows you to share the same event names and data between your server-side Laravel application and your client-side JavaScript application.

이러한 형태의 기능을 구축하는 데 도움이 되도록 라라벨은 WebSocket 연결을 통해 서버 측 라라벨 [이벤트](/docs/{{version}}/events)을 쉽게 "브로드캐스트"합니다. 라라벨 이벤트를 브로드캐스트하면 서버 측 라라벨 애플리케이션과 클라이언트 측 JavaScript 애플리케이션 간에 동일한 이벤트 이름과 데이터를 공유할 수 있습니다.

The core concepts behind broadcasting are simple: clients connect to named channels on the frontend, while your Laravel application broadcasts events to these channels on the backend. These events can contain any additional data you wish to make available to the frontend.

브로드캐스팅의 핵심 개념은 간단합니다. 클라이언트는 프론트엔드의 명명된 채널에 연결하고 라라벨 애플리케이션은 백엔드의 이러한 채널에 이벤트를 브로드캐스트합니다. 이러한 이벤트에는 프런트엔드에서 사용할 수 있도록 하려는 추가 데이터가 포함될 수 있습니다.

<a name="supported-drivers"></a>
#### Supported Drivers
#### 지원되는 드라이버

By default, Laravel includes two server-side broadcasting drivers for you to choose from: [Pusher Channels](https://pusher.com/channels) and [Ably](https://ably.io). However, community driven packages such as [laravel-websockets](https://beyondco.de/docs/laravel-websockets/getting-started/introduction) and [soketi](https://docs.soketi.app/) provide additional broadcasting drivers that do not require commercial broadcasting providers.

기본적으로 라라벨에는 [Pusher Channels](https://pusher.com/channels) 및 [Ably](https://ably.io) 중에서 선택할 수 있는 두 가지 서버 측 브로드캐스팅 드라이버가 포함되어 있습니다. 그러나 [laravel-websockets](https://beyondco.de/docs/laravel-websockets/getting-started/introduction) 및 [soketi](https://docs.soketi.app/) 와 같은 커뮤니티 기반 패키지는 상업용 브로드캐스팅 시스템이 필요하지 않은 추가적인 브로드캐스팅 시스템 드라이버를 제공합니다.

> {tip} Before diving into event broadcasting, make sure you have read Laravel's documentation on [events and listeners](/docs/{{version}}/events).

> {tip} 이벤트 브로드캐스팅을 시작하기 전에 [events and listeners](/docs/{{version}}/events)에 대한 라라벨 문서를 읽어보십시오.

<a name="server-side-installation"></a>
## Server Side Installation
## 서버 측 설치

To get started using Laravel's event broadcasting, we need to do some configuration within the Laravel application as well as install a few packages.

라라벨의 이벤트 브로드캐스팅을 시작하려면 라라벨 애플리케이션 내에서 몇 가지 환경설정을 수정하고 몇 가지 패키지를 설치해야 합니다.

Event broadcasting is accomplished by a server-side broadcasting driver that broadcasts your Laravel events so that Laravel Echo (a JavaScript library) can receive them within the browser client. Don't worry - we'll walk through each part of the installation process step-by-step.

이벤트 브로드캐스팅은 라라벨 Echo(JavaScript 라이브러리)가 브라우저 클라이언트 내에서 이벤트를 수신할 수 있도록, 라라벨 이벤트를 브로드캐스트하는 서버 측 브로드캐스팅 드라이버에 의해 수행됩니다. 걱정하지 마세요. 설치 프로세스의 각 부분을 단계별로 안내해 드리겠습니다.

<a name="configuration"></a>
### Configuration
### 설정하기

All of your application's event broadcasting configuration is stored in the `config/broadcasting.php` configuration file. Laravel supports several broadcast drivers out of the box: [Pusher Channels](https://pusher.com/channels), [Redis](/docs/{{version}}/redis), and a `log` driver for local development and debugging. Additionally, a `null` driver is included which allows you to totally disable broadcasting during testing. A configuration example is included for each of these drivers in the `config/broadcasting.php` configuration file.

애플리케이션의 모든 이벤트 브로드캐스팅 환경설정은 `config/broadcasting.php` 설정 파일에 저장됩니다. 라라벨은 [Pusher Channels](https://pusher.com/channels), [Redis](/docs/{{version}}/redis) 및 로컬 개발 및 디버깅을 위한 `log` 드라이버와 같은 여러 브로드캐스트 드라이버를 즉시 지원합니다. 또한 테스트 중에 브로드캐스트를 완전히 비활성화할 수 있는 `null` 드라이버가 포함되어 있습니다. `config/broadcasting.php` 설정 파일에 이러한 드라이버들 각각에 대한 설정 예제가 포함되어 있습니다.

<a name="broadcast-service-provider"></a>
#### Broadcast Service Provider
#### 브로드캐스트 서비스 프로바이더

Before broadcasting any events, you will first need to register the `App\Providers\BroadcastServiceProvider`. In new Laravel applications, you only need to uncomment this provider in the `providers` array of your `config/app.php` configuration file. This `BroadcastServiceProvider` contains the code necessary to register the broadcast authorization routes and callbacks.

이벤트를 브로드캐스팅하기 전에 먼저 `App\Providers\BroadcastServiceProvider`를 등록해야 합니다. 새로운 라라벨 애플리케이션에서는 `config/app.php` 설정 파일의 `providers` 배열에서 이 공급자의 주석을 제거하기만 하면 됩니다. 이 `BroadcastServiceProvider`에는 브로드캐스팅 인증 경로 및 콜백을 등록하는 데 필요한 코드가 포함되어 있습니다.

<a name="queue-configuration"></a>
#### Queue Configuration
#### 대기열 설정

You will also need to configure and run a [queue worker](/docs/{{version}}/queues). All event broadcasting is done via queued jobs so that the response time of your application is not seriously affected by events being broadcast.

또한 [대기열 작업자](/docs/{{version}}/queues)를 설정하고 실행해야 합니다. 모든 이벤트 브로드캐스팅은 대기 중인 작업을 통해 수행되므로 브로드캐스트되는 이벤트에 의해 애플리케이션의 응답 시간이 심각하게 영향을 받지 않습니다.

<a name="pusher-channels"></a>
### Pusher Channels
### 푸셔 채널

If you plan to broadcast your events using [Pusher Channels](https://pusher.com/channels), you should install the Pusher Channels PHP SDK using the Composer package manager:

[푸셔 채널](https://pusher.com/channels) 을 사용하여 이벤트를 브로드캐스트하려면, Composer 패키지 관리자를 사용하여 푸셔 채널 PHP SDK를 설치해야 합니다.

    composer require pusher/pusher-php-server

Next, you should configure your Pusher Channels credentials in the `config/broadcasting.php` configuration file. An example Pusher Channels configuration is already included in this file, allowing you to quickly specify your key, secret, and application ID. Typically, these values should be set via the `PUSHER_APP_KEY`, `PUSHER_APP_SECRET`, and `PUSHER_APP_ID` [environment variables](/docs/{{version}}/configuration#environment-configuration):

다음으로 `config/broadcasting.php` 설정 파일에서 푸셔 채널 자격 증명을 설정해야 합니다. 이 파일에는 푸셔 채널 설정의 예제가 이미 포함되어 있어서 key, secret 및 애플리케이션 ID를 빠르게 지정할 수 있습니다. 일반적으로 이러한 값은 `PUSHER_APP_KEY`, `PUSHER_APP_SECRET` 및 `PUSHER_APP_ID` [환경 변수](/docs/{{version}}/configuration#environment-configuration)를 통해 설정해야 합니다.

    PUSHER_APP_ID=your-pusher-app-id
    PUSHER_APP_KEY=your-pusher-key
    PUSHER_APP_SECRET=your-pusher-secret
    PUSHER_APP_CLUSTER=mt1

The `config/broadcasting.php` file's `pusher` configuration also allows you to specify additional `options` that are supported by Channels, such as the cluster.

`config/broadcasting.php` 파일의 `pusher` 설정을 사용하면 클러스터와 같이 채널에서 지원하는 `options`을 추가로 지정할 수도 있습니다.

Next, you will need to change your broadcast driver to `pusher` in your `.env` file:

다음으로 `.env` 파일에서 브로드캐스트 드라이버를 `pusher`로 변경해야 합니다.

    BROADCAST_DRIVER=pusher

Finally, you are ready to install and configure [Laravel Echo](#client-side-installation), which will receive the broadcast events on the client-side.

마지막으로 클라이언트 측에서 브로드캐스트 이벤트를 수신할 [Laravel Echo](#client-side-installation)를 설치하면 설정을 마무리 할 준비가 됩니다.

<a name="pusher-compatible-open-source-alternatives"></a>
#### Open Source Pusher Alternatives
#### 오픈 소스 푸셔 대안

The [laravel-websockets](https://github.com/beyondcode/laravel-websockets) and [soketi](https://docs.soketi.app/) packages provide Pusher compatible WebSocket servers for Laravel. These packages allow you to leverage the full power of Laravel broadcasting without a commercial WebSocket provider. For more information on installing and using these packages, please consult our documentation on [open source alternatives](#open-source-alternatives).

[laravel-websockets](https://github.com/beyondcode/laravel-websockets) 와 [soketi](https://docs.soketi.app/) 패키지는 라라벨 푸셔와 호환되는 WebSocket 서버를 제공합니다. 이 패키지를 사용하면 상용 WebSocket 공급자 없이도 라라벨 브로드캐스팅의 모든 기능을 활용할 수 있습니다. 이러한 패키지 설치 및 사용에 대한 자세한 내용은 [오픈 소스 대안](#open-source-alternatives)에 대한 설명서를 참조하세요.

<a name="ably"></a>
### Ably
### Ably

If you plan to broadcast your events using [Ably](https://ably.io), you should install the Ably PHP SDK using the Composer package manager:

[Ably](https://ably.io)를 사용하여 이벤트를 브로드캐스트하려면 Composer 패키지 관리자를 사용하여 Ably PHP SDK를 설치해야 합니다.

    composer require ably/ably-php

Next, you should configure your Ably credentials in the `config/broadcasting.php` configuration file. An example Ably configuration is already included in this file, allowing you to quickly specify your key. Typically, this value should be set via the `ABLY_KEY` [environment variable](/docs/{{version}}/configuration#environment-configuration):

다음으로 `config/broadcasting.php` 설정 파일에서 Ably 자격 증명을 설정해야 합니다. 이 파일에 Aly 설정의 예제가 이미 포함되어 있어 키를 빠르게 지정할 수 있습니다. 일반적으로 이 값은 `ABLY_KEY` [환경 변수](/docs/{{version}}/configuration#environment-configuration)를 통해 설정해야 합니다.

    ABLY_KEY=your-ably-key

Next, you will need to change your broadcast driver to `ably` in your `.env` file:

다음으로 `.env` 파일에서 브로드캐스트 드라이버를 `ably`로 변경해야 합니다.

    BROADCAST_DRIVER=ably

Finally, you are ready to install and configure [Laravel Echo](#client-side-installation), which will receive the broadcast events on the client-side.

마지막으로 클라이언트 측에서 브로드캐스트 이벤트를 수신할 [Laravel Echo](#client-side-installation)를 설치하면 설정을 마무리 할 준비가 됩니다.

<a name="open-source-alternatives"></a>
### Open Source Alternatives
### 오픈 소스 대안

<a name="open-source-alternatives-php"></a>
#### PHP
#### PHP

The [laravel-websockets](https://github.com/beyondcode/laravel-websockets) package is a pure PHP, Pusher compatible WebSocket package for Laravel. This package allows you to leverage the full power of Laravel broadcasting without a commercial WebSocket provider. For more information on installing and using this package, please consult its [official documentation](https://beyondco.de/docs/laravel-websockets).

[laravel-websockets](https://github.com/beyondcode/laravel-websockets) 패키지는 순수 PHP, 라라벨용 Pusher 호환 WebSocket 패키지입니다. 이 패키지를 사용하면 상용 WebSocket 공급자 없이도 라라벨 브로드캐스팅의 모든 기능을 활용할 수 있습니다. 이 패키지 설치 및 사용에 대한 자세한 내용은 [공식 문서](https://beyondco.de/docs/laravel-websockets) 를 참조하세요.

<a name="open-source-alternatives-node"></a>
#### Node
#### Node

[Soketi](https://github.com/soketi/soketi) is a Node based, Pusher compatible WebSocket server for Laravel. Under the hood, Soketi utilizes µWebSockets.js for extreme scalability and speed. This package allows you to leverage the full power of Laravel broadcasting without a commercial WebSocket provider. For more information on installing and using this package, please consult its [official documentation](https://docs.soketi.app/).

[Soketi](https://github.com/soketi/soketi) 는 Node 기반의 라라벨용 Pusher 호환 WebSocket 서버입니다. 내부적으로 Soketi는 극도의 확장성과 속도를 위해 WebSockets.js를 활용합니다. 이 패키지를 사용하면 상용 WebSocket 공급자 없이도 라라벨 브로드캐스팅의 모든 기능을 활용할 수 있습니다. 이 패키지의 설치 및 사용에 대한 자세한 내용은 [공식 문서](https://docs.soketi.app)를 참조하십시오.

<a name="client-side-installation"></a>
## Client Side Installation
## 클라이언트 측 설치

<a name="client-pusher-channels"></a>
### Pusher Channels
### 푸셔 채널

[Laravel Echo](https://github.com/laravel/echo) is a JavaScript library that makes it painless to subscribe to channels and listen for events broadcast by your server-side broadcasting driver. You may install Echo via the NPM package manager. In this example, we will also install the `pusher-js` package since we will be using the Pusher Channels broadcaster:

[Laravel Echo](https://github.com/laravel/echo) 는 손쉽게 채널을 구독하고, 서버 측 브로드캐스팅 드라이버에서 브로드캐스트하는 이벤트를 수신할 수 있도록 하는 JavaScript 라이브러리입니다. NPM 패키지 관리자를 통해 Echo를 설치할 수 있습니다. 이 예제에서는 푸셔 채널 브로드캐스터를 사용할 것이기 때문에 `pusher-js` 패키지도 설치합니다.

```bash
npm install --save-dev laravel-echo pusher-js
```

Once Echo is installed, you are ready to create a fresh Echo instance in your application's JavaScript. A great place to do this is at the bottom of the `resources/js/bootstrap.js` file that is included with the Laravel framework. By default, an example Echo configuration is already included in this file - you simply need to uncomment it:

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

Once you have uncommented and adjusted the Echo configuration according to your needs, you may compile your application's assets:

주석을 제거하고 필요에 따라 Echo 설정을 조정한 후에는 애플리케이션의 자산-assets을 컴파일할 수 있습니다.

    npm run dev

> {tip} To learn more about compiling your application's JavaScript assets, please consult the documentation on [Laravel Mix](/docs/{{version}}/mix).

> {tip} 애플리케이션의 JavaScript 자산-assets 컴파일에 대한 자세한 내용은 [Laravel Mix](/docs/{{version}}/mix)에 대한 문서를 참조하세요.

<a name="using-an-existing-client-instance"></a>
#### Using An Existing Client Instance
#### 기존 클라이언트 인스턴스 사용

If you already have a pre-configured Pusher Channels client instance that you would like Echo to utilize, you may pass it to Echo via the `client` configuration option:

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
### Ably

[Laravel Echo](https://github.com/laravel/echo) is a JavaScript library that makes it painless to subscribe to channels and listen for events broadcast by your server-side broadcasting driver. You may install Echo via the NPM package manager. In this example, we will also install the `pusher-js` package.

[Laravel Echo](https://github.com/laravel/echo) 는 손쉽게 채널을 구독하고, 서버 측 브로드캐스팅 드라이버에서 브로드캐스트하는 이벤트를 수신할 수 있도록 하는 JavaScript 라이브러리입니다. NPM 패키지 관리자를 통해 Echo를 설치할 수 있습니다. 이 예제에서는 `pusher-js` 패키지도 설치합니다.

You may wonder why we would install the `pusher-js` JavaScript library even though we are using Ably to broadcast our events. Thankfully, Ably includes a Pusher compatibility mode which lets us use the Pusher protocol when listening for events in our client-side application:

우리가 이벤트를 브로드캐스트하기 위해 Ably를 사용하고 있음에도 불구하고 `pusher-js` JavaScript 라이브러리를 설치하는 이유가 궁금할 것입니다. 고맙게도 Ably에는 클라이언트 측 애플리케이션에서 이벤트를 수신 대기할 때 Pusher 프로토콜을 사용할 수 있는 Pusher 호환 모드가 포함되어 있습니다.

```bash
npm install --save-dev laravel-echo pusher-js
```

**Before continuing, you should enable Pusher protocol support in your Ably application settings. You may enable this feature within the "Protocol Adapter Settings" portion of your Ably application's settings dashboard.**

**계속하기 전에 Ably 애플리케이션 설정에서 푸셔 프로토콜 지원을 활성화해야 합니다. Ably 애플리케이션 설정 대시보드의 "프로토콜 어댑터 설정" 부분에서 이 기능을 활성화할 수 있습니다.**

Once Echo is installed, you are ready to create a fresh Echo instance in your application's JavaScript. A great place to do this is at the bottom of the `resources/js/bootstrap.js` file that is included with the Laravel framework. By default, an example Echo configuration is already included in this file; however, the default configuration in the `bootstrap.js` file is intended for Pusher. You may copy the configuration below to transition your configuration to Ably:

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

Note that our Ably Echo configuration references a `MIX_ABLY_PUBLIC_KEY` environment variable. This variable's value should be your Ably public key. Your public key is the portion of your Ably key that occurs before the `:` character.

Ably Echo 설정은 `MIX_ABLY_PUBLIC_KEY` 환경 변수를 참조합니다. 이 변수의 값은 Ably 공개 키여야 합니다. 공개 키는 `:` 문자 앞에 나오는 Ably 키 부분입니다.

Once you have uncommented and adjusted the Echo configuration according to your needs, you may compile your application's assets:

주석을 제거하고 필요에 따라 Echo 설정을 조정한 후에는 애플리케이션의 자산-assets을 컴파일할 수 있습니다.

    npm run dev

> {tip} To learn more about compiling your application's JavaScript assets, please consult the documentation on [Laravel Mix](/docs/{{version}}/mix).

> {tip} 애플리케이션의 JavaScript 자산-assets 컴파일에 대한 자세한 내용은 [Laravel Mix](/docs/{{version}}/mix)에 대한 문서를 참조하세요.

<a name="concept-overview"></a>
## Concept Overview
## 컨셉 개요

Laravel's event broadcasting allows you to broadcast your server-side Laravel events to your client-side JavaScript application using a driver-based approach to WebSockets. Currently, Laravel ships with [Pusher Channels](https://pusher.com/channels) and [Ably](https://ably.io) drivers. The events may be easily consumed on the client-side using the [Laravel Echo](#client-side-installation) JavaScript package.

라라벨의 이벤트 브로드캐스팅을 사용하면 WebSocket에 대한 드라이버 기반 접근 방식을 사용하여 서버측 라라벨 이벤트를 클라이언트측 JavaScript 애플리케이션에 브로드캐스트할 수 있습니다. 현재 라라벨은 [Pusher Channels](https://pusher.com/channels) 및 [Ably](https://ably.io) 드라이버와 함께 제공됩니다. 이벤트는 [Laravel Echo](#client-side-installation) JavaScript 패키지를 사용하여 클라이언트 측에서 쉽게 사용할 수 있습니다.

Events are broadcast over "channels", which may be specified as public or private. Any visitor to your application may subscribe to a public channel without any authentication or authorization; however, in order to subscribe to a private channel, a user must be authenticated and authorized to listen on that channel.

이벤트는 공개 또는 비공개로 지정할 수 있는 "채널"을 통해 브로드캐스트됩니다. 애플리케이션 방문자는 인증이나 승인 없이 공개 채널을 구독할 수 있습니다. 그러나 개인 채널을 구독하려면 사용자가 인증을 받고 해당 채널에서 수신할 수 있는 권한이 있어야 합니다.

> {tip} If you would like to explore open source alternatives to Pusher, check out the [open source alternatives](#open-source-alternatives).

> {tip} 푸셔의 오픈 소스 대안을 탐색하고 싶다면 [오픈 소스 대안](#open-source-alternatives)을 확인하세요.

<a name="using-example-application"></a>
### Using An Example Application
### 예제 애플리케이션 사용하기

Before diving into each component of event broadcasting, let's take a high level overview using an e-commerce store as an example.

이벤트 브로드캐스팅의 각 설정 값에 대해 자세히 알아보기 전에, 전자 상거래 상점을 예로 들어 높은 수준의 개요를 살펴보겠습니다.

In our application, let's assume we have a page that allows users to view the shipping status for their orders. Let's also assume that a `OrderShipmentStatusUpdated` event is fired when a shipping status update is processed by the application:

우리 애플리케이션에서 사용자가 주문에 대한 배송 상태를 볼 수 있는 페이지가 있다고 가정해 보겠습니다. 또한 애플리케이션에서 배송 상태 업데이트를 처리할 때 `OrderShipmentStatusUpdated` 이벤트가 발생한다고 가정해 보겠습니다.

    use App\Events\OrderShipmentStatusUpdated;

    OrderShipmentStatusUpdated::dispatch($order);

<a name="the-shouldbroadcast-interface"></a>
#### The `ShouldBroadcast` Interface
#### `ShouldBroadcast` 인터페이스

When a user is viewing one of their orders, we don't want them to have to refresh the page to view status updates. Instead, we want to broadcast the updates to the application as they are created. So, we need to mark the `OrderShipmentStatusUpdated` event with the `ShouldBroadcast` interface. This will instruct Laravel to broadcast the event when it is fired:

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

The `ShouldBroadcast` interface requires our event to define a `broadcastOn` method. This method is responsible for returning the channels that the event should broadcast on. An empty stub of this method is already defined on generated event classes, so we only need to fill in its details. We only want the creator of the order to be able to view status updates, so we will broadcast the event on a private channel that is tied to the order:

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
#### Authorizing Channels
#### 채널 인증하기

Remember, users must be authorized to listen on private channels. We may define our channel authorization rules in our application's `routes/channels.php` file. In this example, we need to verify that any user attempting to listen on the private `order.1` channel is actually the creator of the order:

사용자는 private 채널에서 청취할 수 있는 권한이 있어야 합니다. 애플리케이션의 `routes/channels.php` 파일에서 채널 인증 규칙을 정의할 수 있습니다. 이 예제에서 우리는 비공개 채널 `order.1` 에서 수신을 시도하는 모든 사용자가 실제로 주문의 작성자인지 확인해야 합니다.

    use App\Models\Order;

    Broadcast::channel('orders.{orderId}', function ($user, $orderId) {
        return $user->id === Order::findOrNew($orderId)->user_id;
    });

The `channel` method accepts two arguments: the name of the channel and a callback which returns `true` or `false` indicating whether the user is authorized to listen on the channel.

`channel` 메소드는 두 개의 인자(채널명과 사용자가 채널을 들을 수 있도록 인증되었는지를 나타내는 `true` 혹은 `false`를 되돌려주는 콜백)를 받습니다.

All authorization callbacks receive the currently authenticated user as their first argument and any additional wildcard parameters as their subsequent arguments. In this example, we are using the `{orderId}` placeholder to indicate that the "ID" portion of the channel name is a wildcard.

모든 승인 콜백은 현재 인증된 사용자를 첫번째 인수로 받고, 다음 인수로 추가적인 와일드카드 파라미터들을 받습니다. 이 예제에서, 채널명의 "ID" 부분을 가리키기 위해 `{orderId}` 플레이스 홀더를 사용합니다.

<a name="listening-for-event-broadcasts"></a>
#### Listening For Event Broadcasts
#### 이벤트 브로드캐스트 수신하기

Next, all that remains is to listen for the event in our JavaScript application. We can do this using [Laravel Echo](#client-side-installation). First, we'll use the `private` method to subscribe to the private channel. Then, we may use the `listen` method to listen for the `OrderShipmentStatusUpdated` event. By default, all of the event's public properties will be included on the broadcast event:

다음으로 남은 것은 JavaScript 애플리케이션에서 이벤트를 수신하는 것뿐입니다. [Laravel Echo](#client-side-installation)를 사용하여 이 작업을 수행할 수 있습니다. 먼저 `private` 메서드를 사용하여 비공개 채널을 구독합니다. 그런 다음 `listen` 메소드를 사용하여 `OrderShipmentStatusUpdated` 이벤트를 수신할 수 있습니다. 기본적으로 이벤트의 모든 public 속성은 브로드캐스트 이벤트에 포함됩니다.

```js
Echo.private(`orders.${orderId}`)
    .listen('OrderShipmentStatusUpdated', (e) => {
        console.log(e.order);
    });
```

<a name="defining-broadcast-events"></a>
## Defining Broadcast Events
## 브로드캐스트 이벤트 정의하기

To inform Laravel that a given event should be broadcast, you must implement the `Illuminate\Contracts\Broadcasting\ShouldBroadcast` interface on the event class. This interface is already imported into all event classes generated by the framework so you may easily add it to any of your events.

라라벨에 주어진 이벤트가 브로드캐스트되어야 한다는 것을 알리려면, 이벤트 클래스에 `Illuminate\Contracts\Broadcasting\ShouldBroadcast` 인터페이스를 구현해야 합니다. 이 인터페이스는 프레임워크에서 생성한 모든 이벤트 클래스에 이미 추가되어 있기 때문에, 모든 이벤트에 쉽게 추가할 수 있습니다.

The `ShouldBroadcast` interface requires you to implement a single method: `broadcastOn`. The `broadcastOn` method should return a channel or array of channels that the event should broadcast on. The channels should be instances of `Channel`, `PrivateChannel`, or `PresenceChannel`. Instances of `Channel` represent public channels that any user may subscribe to, while `PrivateChannels` and `PresenceChannels` represent private channels that require [channel authorization](#authorizing-channels):

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

After implementing the `ShouldBroadcast` interface, you only need to [fire the event](/docs/{{version}}/events) as you normally would. Once the event has been fired, a [queued job](/docs/{{version}}/queues) will automatically broadcast the event using your specified broadcast driver.

`ShouldBroadcast` 인터페이스를 구현한 후에는 평소와 같이 [이벤트 발생](/docs/{{version}}/events)만 시키면 됩니다. 이벤트가 시작되면 [대기 중인 작업](/docs/{{version}}/queues)이 지정된 브로드캐스트 드라이버를 사용하여 이벤트를 자동으로 브로드캐스트합니다.

<a name="broadcast-name"></a>
### Broadcast Name
### 브로드캐스트 이름

By default, Laravel will broadcast the event using the event's class name. However, you may customize the broadcast name by defining a `broadcastAs` method on the event:

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

If you customize the broadcast name using the `broadcastAs` method, you should make sure to register your listener with a leading `.` character. This will instruct Echo to not prepend the application's namespace to the event:

`broadcastAs` 메소드를 사용하여 브로드캐스트 이름을 커스터마이징 했다면, `.` 문자를 시작으로 하는 리스너를 등록해야합니다. 이를 통해서 Echo가 애플리케이션의 네임스페이스를 이벤트 앞에 추가하지 않도록 합니다.

    .listen('.server.created', function (e) {
        ....
    });

<a name="broadcast-data"></a>
### Broadcast Data
### 브로드캐스트 데이터

When an event is broadcast, all of its `public` properties are automatically serialized and broadcast as the event's payload, allowing you to access any of its public data from your JavaScript application. So, for example, if your event has a single public `$user` property that contains an Eloquent model, the event's broadcast payload would be:

이벤트가 브로드캐스트될 때, 이벤트의 모든 `public` 속성들은 자동적으로 시리얼라이즈되어 이벤트의 데이터로 브로드캐스트되고, 자바스크립트 애플리케이션에서 이벤트의 모든 공개적인 데이터에 접근할 수 있도록 해줍니다. 그래서 예를들면, 이벤트가 엘로퀀트 모델을 담고 있는 `$user`라는 public 속성 하나를 가지고 있다면, 이벤트의 데이터는 다음과 같이 될 것입니다.

    {
        "user": {
            "id": 1,
            "name": "Patrick Stewart"
            ...
        }
    }

However, if you wish to have more fine-grained control over your broadcast payload, you may add a `broadcastWith` method to your event. This method should return the array of data that you wish to broadcast as the event payload:

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
### Broadcast Queue
### 브로드캐스트 큐-queue

By default, each broadcast event is placed on the default queue for the default queue connection specified in your `queue.php` configuration file. You may customize the queue connection and name used by the broadcaster by defining `connection` and `queue` properties on your event class:

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

Alternatively, you may customize the queue name by defining a `broadcastQueue` method on your event:

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

If you would like to broadcast your event using the `sync` queue instead of the default queue driver, you can implement the `ShouldBroadcastNow` interface instead of `ShouldBroadcast`:

기본 대기열 드라이버 대신 `sync` 대기열을 사용하여 이벤트를 브로드캐스트하려면 `ShouldBroadcast` 대신 `ShouldBroadcastNow` 인터페이스를 구현할 수 있습니다.

    <?php

    use Illuminate\Contracts\Broadcasting\ShouldBroadcastNow;

    class OrderShipmentStatusUpdated implements ShouldBroadcastNow
    {
        //
    }

<a name="broadcast-conditions"></a>
### Broadcast Conditions
### 조건 브로드캐스트

Sometimes you want to broadcast your event only if a given condition is true. You may define these conditions by adding a `broadcastWhen` method to your event class:

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
#### Broadcasting & Database Transactions
#### 브로드캐스팅 및 데이터베이스 거래

When broadcast events are dispatched within database transactions, they may be processed by the queue before the database transaction has committed. When this happens, any updates you have made to models or database records during the database transaction may not yet be reflected in the database. In addition, any models or database records created within the transaction may not exist in the database. If your event depends on these models, unexpected errors can occur when the job that broadcasts the event is processed.

브로드캐스트 이벤트가 데이터베이스 트랜잭션 내에서 전달되면 데이터베이스 트랜잭션이 커밋되기 전에 큐에서 처리될 수 있습니다. 이 경우 데이터베이스 트랜잭션 중 모델 또는 데이터베이스 레코드에 대한 업데이트가 아직 데이터베이스에 반영되지 않을 수 있습니다. 또한 트랜잭션 내에서 생성된 모델 또는 데이터베이스 레코드는 데이터베이스에 존재하지 않을 수 있습니다. 이벤트가 이러한 모델에 종속된 경우 이벤트를 브로드캐스트하는 작업이 처리될 때 예기치 않은 오류가 발생할 수 있습니다.

If your queue connection's `after_commit` configuration option is set to `false`, you may still indicate that a particular broadcast event should be dispatched after all open database transactions have been committed by defining an `$afterCommit` property on the event class:

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

> {tip} To learn more about working around these issues, please review the documentation regarding [queued jobs and database transactions](/docs/{{version}}/queues#jobs-and-database-transactions).

> {tip} 이러한 문제를 해결하는 방법에 대해 자세히 알아보려면 [대기 중인 작업 및 데이터베이스 트랜잭션](/docs/{{version}}/queues#jobs-and-database-transactions)에 관한 문서를 확인하세요.

<a name="authorizing-channels"></a>
## Authorizing Channels
## 채널 승인하기

Private channels require you to authorize that the currently authenticated user can actually listen on the channel. This is accomplished by making an HTTP request to your Laravel application with the channel name and allowing your application to determine if the user can listen on that channel. When using [Laravel Echo](#client-side-installation), the HTTP request to authorize subscriptions to private channels will be made automatically; however, you do need to define the proper routes to respond to these requests.

비공개 채널을 사용하려면, 현재 인증된 사용자가 실제로 채널에서 청취할 수 있도록 승인해줘야 합니다. 이는 채널 이름으로 라라벨 애플리케이션에 HTTP 요청을 하고 애플리케이션이 사용자가 해당 채널에서 수신할 수 있는지 여부를 결정할 수 있도록 함으로써 이루어집니다. [Laravel Echo](#client-side-installation)를 사용할 때 비공개 채널에 대한 구독을 승인하기 위한 HTTP 요청이 자동으로 만들어집니다. 그러나 이러한 요청에 응답하려면 적절한 경로를 정의해야 합니다.

<a name="defining-authorization-routes"></a>
### Defining Authorization Routes
### 승인 라우트 정의하기

Thankfully, Laravel makes it easy to define the routes to respond to channel authorization requests. In the `App\Providers\BroadcastServiceProvider` included with your Laravel application, you will see a call to the `Broadcast::routes` method. This method will register the `/broadcasting/auth` route to handle authorization requests:

고맙게도 라라벨은 채널 승인 요청에 응답하기 위한 경로를 쉽게 정의할 수 있습니다. 라라벨 애플리케이션에 포함된 `App\Providers\BroadcastServiceProvider`에서 `Broadcast::routes` 메소드에 대한 호출을 볼 수 있습니다. 이 메소드는 승인 요청을 처리하기 위해 `/broadcasting/auth` 경로를 등록합니다.

    Broadcast::routes();

The `Broadcast::routes` method will automatically place its routes within the `web` middleware group; however, you may pass an array of route attributes to the method if you would like to customize the assigned attributes:

`Broadcast::routes` 메소드는 자동으로 라우트를 `web` 미들웨어 그룹에 위치시킬 것입니다. 그렇지만 할당된 속성들을 커스터마이징하기 위해서는 메소드에 라우트 속성 배열을 전달해줘야 합니다.

    Broadcast::routes($attributes);

<a name="customizing-the-authorization-endpoint"></a>
#### Customizing The Authorization Endpoint
#### Authorization Endpoint의 커스터마이징

By default, Echo will use the `/broadcasting/auth` endpoint to authorize channel access. However, you may specify your own authorization endpoint by passing the `authEndpoint` configuration option to your Echo instance:

기본적으로 Echo는 `/broadcasting/auth` 라는 엔드포인트를 사용하여 채널 액세스 권한을 부여합니다. 그러나 Echo 인스턴스에 `authEndpoint` 설정 옵션을 전달하여 여러분 만의 인증 엔드포인트를 지정할 수 있습니다.

    window.Echo = new Echo({
        broadcaster: 'pusher',
        // ...
        authEndpoint: '/custom/endpoint/auth'
    });

<a name="customizing-the-authorization-request"></a>
#### Customizing The Authorization Request
#### 승인 요청 사용자 정의

You can customize how Laravel Echo performs authorization requests by providing a custom authorizer when initializing Echo:

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
### Defining Authorization Callbacks
### 승인 콜백 정의하기

Next, we need to define the logic that will actually determine if the currently authenticated user can listen to a given channel. This is done in the `routes/channels.php` file that is included with your application. In this file, you may use the `Broadcast::channel` method to register channel authorization callbacks:

다음으로, 현재 인증된 사용자가 주어진 채널을 수신할 수 있는지 여부를 실제로 결정하는 로직을 정의해야 합니다. 이것은 애플리케이션에 포함된 `routes/channels.php` 파일에서 수행됩니다. 이 파일에서 `Broadcast::channel` 메소드를 사용하여 채널 인증 콜백을 등록할 수 있습니다.

    Broadcast::channel('orders.{orderId}', function ($user, $orderId) {
        return $user->id === Order::findOrNew($orderId)->user_id;
    });

The `channel` method accepts two arguments: the name of the channel and a callback which returns `true` or `false` indicating whether the user is authorized to listen on the channel.

`channel` 메소드는 두 개의 인수(채널명과 사용자가 채널을 수신하도록 승인되었는지 여부를 나타내는 `true` 혹은 `false` 값을 리턴하는 콜백)를 받습니다.

All authorization callbacks receive the currently authenticated user as their first argument and any additional wildcard parameters as their subsequent arguments. In this example, we are using the `{orderId}` placeholder to indicate that the "ID" portion of the channel name is a wildcard.

모든 권한 부여 콜백은 현재 인증된 사용자를 첫 번째 인수로 수신하고 추가 와일드카드 매개변수를 후속 인수로 수신합니다. 이 예에서는 `{orderId}` 자리 표시자를 사용하여 채널 이름의 "ID" 부분이 와일드카드임을 나타냅니다.

<a name="authorization-callback-model-binding"></a>
#### Authorization Callback Model Binding
#### 승인 콜백의 모델 바인딩

Just like HTTP routes, channel routes may also take advantage of implicit and explicit [route model binding](/docs/{{version}}/routing#route-model-binding). For example, instead of receiving a string or numeric order ID, you may request an actual `Order` model instance:

HTTP 경로와 마찬가지로 채널 경로도 암시적 및 명시적 [경로 모델 바인딩](/docs/{{version}}/routing#route-model-binding)을 활용할 수 있습니다. 예를 들어 문자열이나 숫자 주문 ID를 받는 대신 실제 `Order` 모델 인스턴스를 요청할 수 있습니다.

    use App\Models\Order;

    Broadcast::channel('orders.{order}', function ($user, Order $order) {
        return $user->id === $order->user_id;
    });

> {note} Unlike HTTP route model binding, channel model binding does not support automatic [implicit model binding scoping](/docs/{{version}}/routing#implicit-model-binding-scoping). However, this is rarely a problem because most channels can be scoped based on a single model's unique, primary key.

> {note} HTTP 경로 모델 바인딩과 달리 채널 모델 바인딩은 자동 [암시적 모델 바인딩 범위 지정](/docs/{{version}}/routing#implicit-model-binding-scoping)을 지원하지 않습니다. 그러나 대부분의 채널이 단일 모델의 고유한 기본 키를 기반으로 범위가 지정될 수 있기 때문에 이는 거의 문제가 되지 않습니다.

<a name="authorization-callback-authentication"></a>
#### Authorization Callback Authentication
#### 인증 권한 부여 콜백

Private and presence broadcast channels authenticate the current user via your application's default authentication guard. If the user is not authenticated, channel authorization is automatically denied and the authorization callback is never executed. However, you may assign multiple, custom guards that should authenticate the incoming request if necessary:

Private 및 presence 브로드캐스트 채널은 애플리케이션의 기본 인증 가드를 통해 현재 사용자를 인증합니다. 사용자가 인증되지 않으면 채널 권한이 자동으로 거부되고 권한 콜백이 실행되지 않습니다. 그러나 필요한 경우 들어오는 Request에 인증해야하는 사용자 지정 가드를 여러 개 지정할 수 있습니다.

    Broadcast::channel('channel', function () {
        // ...
    }, ['guards' => ['web', 'admin']]);

<a name="defining-channel-classes"></a>
### Defining Channel Classes
### 채널 클래스 정의하기

If your application is consuming many different channels, your `routes/channels.php` file could become bulky. So, instead of using closures to authorize channels, you may use channel classes. To generate a channel class, use the `make:channel` Artisan command. This command will place a new channel class in the `App/Broadcasting` directory.

애플리케이션이 다양한 채널을 사용하는 경우 `routes/channels.php` 파일이 크기가 커질 수 있습니다. 따라서 클로저를 사용하여 채널을 승인하는 대신 채널 클래스를 사용할 수 있습니다. 채널 클래스를 생성하려면 `make:channel` Artisan 명령을 사용하세요. 이 명령은 `AppBroadcasting` 디렉토리에 새 채널 클래스를 배치합니다.

    php artisan make:channel OrderChannel

Next, register your channel in your `routes/channels.php` file:

그 다음, `routes/channels.php` 파일에 생성된 채널을 등록할 수 있습니다.

    use App\Broadcasting\OrderChannel;

    Broadcast::channel('orders.{order}', OrderChannel::class);

Finally, you may place the authorization logic for your channel in the channel class' `join` method. This `join` method will house the same logic you would have typically placed in your channel authorization closure. You may also take advantage of channel model binding:

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

> {tip} Like many other classes in Laravel, channel classes will automatically be resolved by the [service container](/docs/{{version}}/container). So, you may type-hint any dependencies required by your channel in its constructor.

> {tip} 라라벨에 다른 많은 클래스와 같이, 채널 클래스도 자동으로 [서비스 컨테이너](/docs/{{version}}/container)를 통해 의존성이 해결됩니다. 따라서, 의존성을 가진 객체들을 채널 클래스 생성자에서 타입 힌트로 제공받을 수 있습니다.

<a name="broadcasting-events"></a>
## Broadcasting Events
## 이벤트 브로드캐스트하기

Once you have defined an event and marked it with the `ShouldBroadcast` interface, you only need to fire the event using the event's dispatch method. The event dispatcher will notice that the event is marked with the `ShouldBroadcast` interface and will queue the event for broadcasting:

이벤트를 정의하고 `ShouldBroadcast` 인터페이스로 표시한 후에는 이벤트의 디스패치 메서드를 사용하여 이벤트를 시작하기만 하면 됩니다. 이벤트 디스패처는 이벤트가 `ShouldBroadcast` 인터페이스로 표시되어 있음을 확인하고 브로드캐스팅을 위해 이벤트를 대기열에 넣습니다.

    use App\Events\OrderShipmentStatusUpdated;

    OrderShipmentStatusUpdated::dispatch($order);

<a name="only-to-others"></a>
### Only To Others
### 오직 다른이에게만

When building an application that utilizes event broadcasting, you may occasionally need to broadcast an event to all subscribers to a given channel except for the current user. You may accomplish this using the `broadcast` helper and the `toOthers` method:

이벤트 브로드캐스팅을 활용하는 애플리케이션을 구축할 때 현재 사용자를 제외한 특정 채널의 모든 구독자에게 이벤트를 브로드캐스트해야 하는 경우가 있습니다. `broadcast` 헬퍼와 `toOthers` 메서드를 사용하여 이 작업을 수행할 수 있습니다.

    use App\Events\OrderShipmentStatusUpdated;

    broadcast(new OrderShipmentStatusUpdated($update))->toOthers();

To better understand when you may want to use the `toOthers` method, let's imagine a task list application where a user may create a new task by entering a task name. To create a task, your application might make a request to a `/task` URL which broadcasts the task's creation and returns a JSON representation of the new task. When your JavaScript application receives the response from the end-point, it might directly insert the new task into its task list like so:

`toOthers` 메서드를 사용하려는 경우를 더 잘 이해하기 위해, 사용자가 작업 이름을 입력하여 새 작업을 생성할 수 있는 작업 목록 애플리케이션을 상상해 보겠습니다. 작업을 생성하기 위해 애플리케이션은 작업 생성을 브로드캐스트하고 새 작업의 JSON 표현을 반환하는 `/task` URL에 요청을 보낼 수 있습니다. JavaScript 애플리케이션이 엔드포인트에서 응답을 받으면 다음과 같이 새 작업을 작업 목록에 직접 삽입할 수 있습니다.

    axios.post('/task', task)
        .then((response) => {
            this.tasks.push(response.data);
        });

However, remember that we also broadcast the task's creation. If your JavaScript application is also listening for this event in order to add tasks to the task list, you will have duplicate tasks in your list: one from the end-point and one from the broadcast. You may solve this by using the `toOthers` method to instruct the broadcaster to not broadcast the event to the current user.

그러나 태스크 생성도 브로드캐스트한다는 것을 기억하십시오. JavaScript 애플리케이션이 작업 목록에 작업을 추가하기 위해 이 이벤트도 수신하는 경우 목록에 중복 작업이 있을 것입니다. 하나는 엔드포인트에서, 다른 하나는 브로드캐스트에서입니다. 브로드캐스터가 현재 사용자에게 이벤트를 브로드캐스트하지 않도록 지시하는 `toOthers` 메서드를 사용하여 이 문제를 해결할 수 있습니다.

> {note} Your event must use the `Illuminate\Broadcasting\InteractsWithSockets` trait in order to call the `toOthers` method.

> {note} `toOthers` 메소드를 호출할려면 반드시 이벤트에 `Illuminate\Broadcasting\InteractsWithSockets` 트레이트를 사용해야 합니다.

<a name="only-to-others-configuration"></a>
#### Configuration
#### 설정

When you initialize a Laravel Echo instance, a socket ID is assigned to the connection. If you are using a global [Axios](https://github.com/mzabriskie/axios) instance to make HTTP requests from your JavaScript application, the socket ID will automatically be attached to every outgoing request as a `X-Socket-ID` header. Then, when you call the `toOthers` method, Laravel will extract the socket ID from the header and instruct the broadcaster to not broadcast to any connections with that socket ID.

Laravel Echo 인스턴스를 초기화할 때 소켓 ID가 연결에 할당됩니다. 글로벌 [Axios](https://github.com/mzabriskie/axios) 인스턴스를 사용하여 JavaScript 애플리케이션에서 HTTP 요청을 만드는 경우 소켓 ID는 자동으로 모든 발신 요청에 `X-Socket-ID` 헤더로 첨부됩니다. 그런 다음 `toOthers` 메소드를 호출하면 라라벨은 헤더에서 소켓 ID를 추출하고 브로드캐스터에게 해당 소켓 ID를 가진 연결로 브로드캐스트하지 않도록 지시합니다.

If you are not using a global Axios instance, you will need to manually configure your JavaScript application to send the `X-Socket-ID` header with all outgoing requests. You may retrieve the socket ID using the `Echo.socketId` method:

전역 Axios 인스턴스를 사용하지 않는 경우 모든 발신 요청과 함께 'X-Socket-ID' 헤더를 보내도록 JavaScript 애플리케이션을 수동으로 설정해야 합니다. `Echo.socketId` 메소드를 사용하여 소켓 ID를 검색할 수 있습니다.

    var socketId = Echo.socketId();

<a name="customizing-the-connection"></a>
### Customizing The Connection
### 연결 사용자 정의

If your application interacts with multiple broadcast connections and you want to broadcast an event using a broadcaster other than your default, you may specify which connection to push an event to using the `via` method:

애플리케이션이 여러 브로드캐스트 연결과 상호 작용하고 기본값이 아닌 브로드캐스터를 사용하여 이벤트를 브로드캐스트하려는 경우 `via` 메서드를 사용하여 이벤트를 푸시할 연결을 지정할 수 있습니다.

    use App\Events\OrderShipmentStatusUpdated;

    broadcast(new OrderShipmentStatusUpdated($update))->via('pusher');

Alternatively, you may specify the event's broadcast connection by calling the `broadcastVia` method within the event's constructor. However, before doing so, you should ensure that the event class uses the `InteractsWithBroadcasting` trait:

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
## Receiving Broadcasts
## 브로드캐스트 받기

<a name="listening-for-events"></a>
### Listening For Events
### 이벤트 수신하기

Once you have [installed and instantiated Laravel Echo](#client-side-installation), you are ready to start listening for events that are broadcast from your Laravel application. First, use the `channel` method to retrieve an instance of a channel, then call the `listen` method to listen for a specified event:

[Laravel Echo를 설치 및 인스턴스화](#client-side-installation)하면 라라벨 애플리케이션에서 브로드캐스트되는 이벤트 수신을 시작할 준비가 된 것입니다. 먼저 `channel` 메서드를 사용하여 채널의 인스턴스를 검색한 다음 `listen` 메서드를 호출하여 지정된 이벤트를 수신합니다.

```js
Echo.channel(`orders.${this.order.id}`)
    .listen('OrderShipmentStatusUpdated', (e) => {
        console.log(e.order.name);
    });
```

If you would like to listen for events on a private channel, use the `private` method instead. You may continue to chain calls to the `listen` method to listen for multiple events on a single channel:

비공개 채널에서 이벤트를 수신하고 싶으면, `private` 메소드를 사용하세요. 하나의 채널에있는 여러 이벤트를 수신하고 싶으면 `listen` 메소드를 연결해서 사용할 수 있습니다.

```js
Echo.private(`orders.${this.order.id}`)
    .listen(...)
    .listen(...)
    .listen(...);
```

<a name="stop-listening-for-events"></a>
#### Stop Listening For Events
#### 이벤트 수신 중지

If you would like to stop listening to a given event without [leaving the channel](#leaving-a-channel), you may use the `stopListening` method:

[채널을 떠나지 않고](#leaving-a-channel) 주어진 이벤트에 대한 청취를 중단하려면 `stopListening` 메서드를 사용할 수 있습니다.

```js
Echo.private(`orders.${this.order.id}`)
    .stopListening('OrderShipmentStatusUpdated')
```

<a name="leaving-a-channel"></a>
### Leaving A Channel
### 채널 나가기

To leave a channel, you may call the `leaveChannel` method on your Echo instance:

채널을 나가기 위해서는, 에코 인스턴스에서 `leaveChannel` 메소드를 호출하면 됩니다.

```js
Echo.leaveChannel(`orders.${this.order.id}`);
```

If you would like to leave a channel and also its associated private and presence channels, you may call the `leave` method:

채널을 나가면서 연관된 비공개 현재 채널 또한 나가려면, 다음과 같이 `leave` 메소드를 호출하면 됩니다.

```js
Echo.leave(`orders.${this.order.id}`);
```
<a name="namespaces"></a>
### Namespaces
### 네임스페이스

You may have noticed in the examples above that we did not specify the full `App\Events` namespace for the event classes. This is because Echo will automatically assume the events are located in the `App\Events` namespace. However, you may configure the root namespace when you instantiate Echo by passing a `namespace` configuration option:

위의 예에서 이벤트 클래스에 대한 전체 `App\Events` 네임스페이스를 지정하지 않았음을 알 수 있습니다. 이는 Echo가 `App\Events` 네임스페이스에 이벤트가 있다고 자동으로 가정하기 때문입니다. 그러나 `namespace` 설정 옵션을 전달하여 Echo를 인스턴스화할 때 루트 네임스페이스를 설정할 수 있습니다.

```js
window.Echo = new Echo({
    broadcaster: 'pusher',
    // ...
    namespace: 'App.Other.Namespace'
});
```

Alternatively, you may prefix event classes with a `.` when subscribing to them using Echo. This will allow you to always specify the fully-qualified class name:

이 대신에, 에코를 이용하여 이벤트를 구독할 때 이벤트 클래스 앞에 `.`를 붙일 수 있습니다. 이렇게 하면 항상 정규화된 클래스명을 명시할 수 있습니다.

```js
Echo.channel('orders')
    .listen('.Namespace\\Event\\Class', (e) => {
        //
    });
```

<a name="presence-channels"></a>
## Presence Channels
## 프레젠스 채널

Presence channels build on the security of private channels while exposing the additional feature of awareness of who is subscribed to the channel. This makes it easy to build powerful, collaborative application features such as notifying users when another user is viewing the same page or listing the inhabitants of a chat room.

프레젠스 채널은 비공개 채널의 보안을 기반으로 구축되는 동시에 채널을 구독하는 사람을 인식하는 추가 기능을 노출합니다. 이를 통해 다른 사용자가 같은 페이지를 보고 있을 때 사용자에게 알리거나 대화방의 거주자를 나열하는 것과 같은 강력한 협업 애플리케이션 기능을 쉽게 구축할 수 있습니다.

<a name="authorizing-presence-channels"></a>
### Authorizing Presence Channels
### 프레젠스 채널 승인하기

All presence channels are also private channels; therefore, users must be [authorized to access them](#authorizing-channels). However, when defining authorization callbacks for presence channels, you will not return `true` if the user is authorized to join the channel. Instead, you should return an array of data about the user.

모든 프레젠스 채널은 비공개 채널이기도 합니다. 그러므로 사용자는 당연히 프레젠스에 접근하기 위해 [승인을 받아야 합니다](#authorizing-channels). 그렇지만, 프레젠스 채널을 위한 승인 콜백을 정의할 때, 채널에 들어오는 것을 승인하기 위해 `true`를 되돌려주도록 하는 대신 사용자의 데이터 배열을 되돌려 주어야 합니다.

The data returned by the authorization callback will be made available to the presence channel event listeners in your JavaScript application. If the user is not authorized to join the presence channel, you should return `false` or `null`:

승인 콜백에 의해 되돌려받는 데이터는 자바스크립트 애플리케이션의 프레젠스 채널 이벤트 수신자들이 사용할 수 있게 될 것입니다. 만약 사용자가 프레젠스 채널에 들어가는 것이 승인되지 않았으면, `false`나 `null`을 되돌려 주어야 합니다.

    Broadcast::channel('chat.{roomId}', function ($user, $roomId) {
        if ($user->canJoinRoom($roomId)) {
            return ['id' => $user->id, 'name' => $user->name];
        }
    });

<a name="joining-presence-channels"></a>
### Joining Presence Channels
### 프레젠스 채널에 들어가기

To join a presence channel, you may use Echo's `join` method. The `join` method will return a `PresenceChannel` implementation which, along with exposing the `listen` method, allows you to subscribe to the `here`, `joining`, and `leaving` events.

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

The `here` callback will be executed immediately once the channel is joined successfully, and will receive an array containing the user information for all of the other users currently subscribed to the channel. The `joining` method will be executed when a new user joins a channel, while the `leaving` method will be executed when a user leaves the channel. The `error` method will be executed when the authentication endpoint returns a HTTP status code other than 200 or if there is a problem parsing the returned JSON.

`here` 콜백은 채널이 성공적으로 연결되면 즉시 실행되며 현재 채널을 구독하고 있는 다른 모든 사용자에 대한 사용자 정보가 포함된 배열을 수신합니다. `joining` 메서드는 새로운 사용자가 채널에 합류할 때 실행되고, `leaving` 메서드는 사용자가 채널을 떠날 때 실행됩니다. `error` 메서드는 인증 엔드포인트가 200이 아닌 HTTP 상태 코드를 반환하거나 반환된 JSON을 구문 분석하는 데 문제가 있는 경우 실행됩니다.

<a name="broadcasting-to-presence-channels"></a>
### Broadcasting To Presence Channels
### 프레젠스 채널에 브로드캐스트하기

Presence channels may receive events just like public or private channels. Using the example of a chatroom, we may want to broadcast `NewMessage` events to the room's presence channel. To do so, we'll return an instance of `PresenceChannel` from the event's `broadcastOn` method:

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

As with other events, you may use the `broadcast` helper and the `toOthers` method to exclude the current user from receiving the broadcast:

다른 이벤트와 마찬가지로 `broadcast` 헬퍼와 `toOthers` 메서드를 사용하여 현재 사용자가 브로드캐스트를 수신하지 못하도록 제외할 수 있습니다.

    broadcast(new NewMessage($message));

    broadcast(new NewMessage($message))->toOthers();

As typical of other types of events, you may listen for events sent to presence channels using Echo's `listen` method:

다른 유형의 이벤트와 마찬가지로 Echo의 `listen` 메서드를 사용하여 현재 상태 채널로 전송된 이벤트를 수신할 수 있습니다.

    Echo.join(`chat.${roomId}`)
        .here(...)
        .joining(...)
        .leaving(...)
        .listen('NewMessage', (e) => {
            //
        });

<a name="model-broadcasting"></a>
## Model Broadcasting
## 모델 브로드캐스팅

> {note} Before reading the following documentation about model broadcasting, we recommend you become familiar with the general concepts of Laravel's model broadcasting services as well as how to manually create and listen to broadcast events.

> {note} 모델 브로드캐스팅에 대한 다음 문서를 읽기 전에 라라벨의 모델 브로드캐스팅 서비스에 대한 일반적인 개념과 브로드캐스트 이벤트를 수동으로 생성하고 수신하는 방법을 숙지하는 것이 좋습니다.

It is common to broadcast events when your application's [Eloquent models](/docs/{{version}}/eloquent) are created, updated, or deleted. Of course, this can easily be accomplished by manually [defining custom events for Eloquent model state changes](/docs/{{version}}/eloquent#events) and marking those events with the `ShouldBroadcast` interface.

애플리케이션의 [Eloquent 모델](/docs/{{version}}/eloquent)이 생성, 업데이트 또는 삭제될 때 이벤트를 브로드캐스트하는 것이 일반적입니다. 물론 이것은 수동으로 [Eloquent 모델 상태 변경에 대한 사용자 정의 이벤트 정의](/docs/{{version}}/eloquent#events) 및 `ShouldBroadcast` 인터페이스로 해당 이벤트를 표시하여 쉽게 수행할 수 있습니다.

However, if you are not using these events for any other purposes in your application, it can be cumbersome to create event classes for the sole purpose of broadcasting them. To remedy this, Laravel allows you to indicate that an Eloquent model should automatically broadcast its state changes.

그러나 이러한 이벤트를 애플리케이션에서 다른 목적으로 사용하지 않는 경우, 브로드캐스트할 목적으로만 이벤트 클래스를 만드는 것은 번거로울 수 있습니다. 이 문제를 해결하기 위해 라라벨은 Eloquent 모델이 자동으로 상태 변경을 브로드캐스트해야 함을 나타낼 수 있습니다.

To get started, your Eloquent model should use the `Illuminate\Database\Eloquent\BroadcastsEvents` trait. In addition, the model should define a `broadcastsOn` method, which will return an array of channels that the model's events should broadcast on:

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

Once your model includes this trait and defines its broadcast channels, it will begin automatically broadcasting events when a model instance is created, updated, deleted, trashed, or restored.

모델에 이 특성-trait이 포함되고 브로드캐스트 채널이 정의되면 모델 인스턴스가 생성, 업데이트, 삭제, 휴지통 또는 복원될 때 이벤트 브로드캐스트가 자동으로 시작됩니다.

In addition, you may have noticed that the `broadcastOn` method receives a string `$event` argument. This argument contains the type of event that has occurred on the model and will have a value of `created`, `updated`, `deleted`, `trashed`, or `restored`. By inspecting the value of this variable, you may determine which channels (if any) the model should broadcast to for a particular event:

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
#### Customizing Model Broadcasting Event Creation
#### 모델 브로드캐스팅 이벤트 생성 커스터마이징

Occasionally, you may wish to customize how Laravel creates the underlying model broadcasting event. You may accomplish this by defining a `newBroadcastableEvent` method on your Eloquent model. This method should return an `Illuminate\Database\Eloquent\BroadcastableModelEventOccurred` instance:

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
### Model Broadcasting Conventions
### 모델 브로드캐스팅 규약

<a name="model-broadcasting-channel-conventions"></a>
#### Channel Conventions
#### 채널 규칙

As you may have noticed, the `broadcastOn` method in the model example above did not return `Channel` instances. Instead, Eloquent models were returned directly. If an Eloquent model instance is returned by your model's `broadcastOn` method (or is contained in an array returned by the method), Laravel will automatically instantiate a private channel instance for the model using the model's class name and primary key identifier as the channel name.

눈치채셨겠지만 위의 모델 예시에서 `broadcastOn` 메소드는 `Channel` 인스턴스를 반환하지 않았습니다. 대신 Eloquent 모델이 직접 반환되었습니다. Eloquent 모델 인스턴스가 모델의 `broadcastOn` 메소드에 의해 반환되는 경우(또는 메소드에 의해 반환된 배열에 포함된 경우), 라라벨은 모델의 클래스 이름과 기본 키 식별자를 채널로 사용하여 모델에 대한 개인 채널 인스턴스를 자동으로 인스턴스화합니다.

So, an `App\Models\User` model with an `id` of `1` would be converted into a `Illuminate\Broadcasting\PrivateChannel` instance with a name of `App.Models.User.1`. Of course, in addition to returning Eloquent model instances from your model's `broadcastOn` method, you may return complete `Channel` instances in order to have full control over the model's channel names:

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

If you plan to explicitly return a channel instance from your model's `broadcastOn` method, you may pass an Eloquent model instance to the channel's constructor. When doing so, Laravel will use the model channel conventions discussed above to convert the Eloquent model into a channel name string:

모델의 `broadcastOn` 메서드에서 채널 인스턴스를 명시적으로 반환하려는 경우 Eloquent 모델 인스턴스를 채널 생성자에 전달할 수 있습니다. 그렇게 할 때 라라벨은 위에서 논의한 모델 채널 규칙을 사용하여 Eloquent 모델을 채널 이름 문자열로 변환합니다.

```php
return [new Channel($this->user)];
```

If you need to determine the channel name of a model, you may call the `broadcastChannel` method on any model instance. For example, this method returns the string `App.Models.User.1` for a `App\Models\User` model with an `id` of `1`:

모델의 채널 이름을 확인해야 하는 경우 모든 모델 인스턴스에서 `broadcastChannel` 메서드를 호출할 수 있습니다. 예를 들어 이 메소드는 `id`가 `1`인 `App\Models\User` 모델에 대해 `App.Models.User.1` 문자열을 반환합니다.

```php
$user->broadcastChannel()
```

<a name="model-broadcasting-event-conventions"></a>
#### Event Conventions
#### 이벤트 컨벤션

Since model broadcast events are not associated with an "actual" event within your application's `App\Events` directory, they are assigned a name and a payload based on conventions. Laravel's convention is to broadcast the event using the class name of the model (not including the namespace) and the name of the model event that triggered the broadcast.

모델 브로드캐스트 이벤트는 애플리케이션의 `App\Events` 디렉터리 내 "실제" 이벤트와 연결되지 않으므로 규칙에 따라 이름과 페이로드가 할당됩니다. 라라벨의 규칙은 모델의 클래스 이름(네임스페이스 제외)과 브로드캐스트를 트리거한 모델 이벤트의 이름을 사용하여 이벤트를 브로드캐스트하는 것입니다.

So, for example, an update to the `App\Models\Post` model would broadcast an event to your client-side application as `PostUpdated` with the following payload:

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

The deletion of the `App\Models\User` model would broadcast an event named `UserDeleted`.

`App\Models\User` 모델을 삭제하면 `UserDeleted`라는 이벤트가 브로드캐스트됩니다.

If you would like, you may define a custom broadcast name and payload by adding a `broadcastAs` and `broadcastWith` method to your model. These methods receive the name of the model event / operation that is occurring, allowing you to customize the event's name and payload for each model operation. If `null` is returned from the `broadcastAs` method, Laravel will use the model broadcasting event name conventions discussed above when broadcasting the event:

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
### Listening For Model Broadcasts
### 모델 브로드캐스팅 듣기

Once you have added the `BroadcastsEvents` trait to your model and defined your model's `broadcastOn` method, you are ready to start listening for broadcasted model events within your client-side application. Before getting started, you may wish to consult the complete documentation on [listening for events](#listening-for-events).

모델에 `BroadcastsEvents` 특성-trait을 추가하고 모델의 `broadcastOn` 메서드를 정의하면 클라이언트 측 애플리케이션 내에서 브로드캐스트된 모델 이벤트 수신을 시작할 준비가 된 것입니다. 시작하기 전에 [이벤트 수신](#listening-for-events)에 대한 전체 문서를 참조할 수 있습니다.

First, use the `private` method to retrieve an instance of a channel, then call the `listen` method to listen for a specified event. Typically, the channel name given to the `private` method should correspond to Laravel's [model broadcasting conventions](#model-broadcasting-conventions).

먼저 `private` 메서드를 사용하여 채널의 인스턴스를 검색한 다음 `listen` 메서드를 호출하여 지정된 이벤트를 수신 대기합니다. 일반적으로 `private` 메소드에 부여되는 채널 이름은 라라벨의 [모델 브로드캐스팅 규약](#model-broadcasting-conventions)과 일치해야 합니다.

Once you have obtained a channel instance, you may use the `listen` method to listen for a particular event. Since model broadcast events are not associated with an "actual" event within your application's `App\Events` directory, the [event name](#model-broadcasting-event-conventions) must be prefixed with a `.` to indicate it does not belong to a particular namespace. Each model broadcast event has a `model` property which contains all of the broadcastable properties of the model:

채널 인스턴스를 얻으면 `listen` 메소드를 사용하여 특정 이벤트를 수신할 수 있습니다. 모델 브로드캐스트 이벤트는 애플리케이션의 `App\Events` 디렉터리 내 "실제" 이벤트와 연결되어 있지 않기 때문에 [이벤트 이름](#model-broadcasting-event-conventions) 앞에 '.'가 붙어야 하고 이것은 특정 네임스페이스에 속하지 않습니다. 각 모델 브로드캐스트 이벤트에는 모델의 브로드캐스트 가능한 모든 속성이 포함된 `model` 속성이 있습니다.

```js
Echo.private(`App.Models.User.${this.user.id}`)
    .listen('.PostUpdated', (e) => {
        console.log(e.model);
    });
```

<a name="client-events"></a>
## Client Events
## 클라이언트 이벤트

> {tip} When using [Pusher Channels](https://pusher.com/channels), you must enable the "Client Events" option in the "App Settings" section of your [application dashboard](https://dashboard.pusher.com/) in order to send client events.

> {tip} [Pusher Channels](https://pusher.com/channels) 를 사용하는 경우, 클라이언트 이벤트를 전송하려면 [애플리케이션 대시 보드](https://dashboard.pusher.com/) 의 "App Settings"섹션에서 "Client Events" 옵션을 활성화해야합니다.

Sometimes you may wish to broadcast an event to other connected clients without hitting your Laravel application at all. This can be particularly useful for things like "typing" notifications, where you want to alert users of your application that another user is typing a message on a given screen.

때로는 라라벨 애플리케이션을 거치지 않고, 연결된 다른 클라이언트에게 이벤트를 브로드캐스트 해야할 수도 있습니다. 이는 특정한 경우 유용할 수 있는데, 어떤 사용자가 화면에 메세지를 "입력"하고 있다는 것을 다른 사용자에게 알리는 경우가 그렇습니다. 

To broadcast client events, you may use Echo's `whisper` method:

클라이언트 이벤트를 브로드 캐스트하려면, Echo의 `whisper` 메소드를 사용하면 됩니다.

    Echo.private(`chat.${roomId}`)
        .whisper('typing', {
            name: this.user.name
        });

To listen for client events, you may use the `listenForWhisper` method:

클라이언트 이벤트를 수신하려면, `listenForWhisper` 메소드를 사용하면 됩니다.

    Echo.private(`chat.${roomId}`)
        .listenForWhisper('typing', (e) => {
            console.log(e.name);
        });

<a name="notifications"></a>
## Notifications
## 알림

By pairing event broadcasting with [notifications](/docs/{{version}}/notifications), your JavaScript application may receive new notifications as they occur without needing to refresh the page. Before getting started, be sure to read over the documentation on using [the broadcast notification channel](/docs/{{version}}/notifications#broadcast-notifications).

이벤트 브로드캐스팅을 [notifications](/docs/{{version}}/notifications)와 페어링하면 JavaScript 애플리케이션이 페이지를 새로 고칠 필요 없이 새로운 알림을 수신할 수 있습니다. 시작하기 전에 [브로드캐스트 알림 채널](/docs/{{version}}/notifications#broadcast-notifications) 사용에 대한 설명서를 읽어보세요.

Once you have configured a notification to use the broadcast channel, you may listen for the broadcast events using Echo's `notification` method. Remember, the channel name should match the class name of the entity receiving the notifications:

브로드캐스트 채널을 사용하는 것으로 알림을 설정하고나면, 에코의 `notification`메소드를 이용해 브로드캐스트 이벤트를 수신할 수 있습니다. 채널명은 알림을 받는 엔티티의 클래스명과 동일해야 합니다.

    Echo.private(`App.Models.User.${userId}`)
        .notification((notification) => {
            console.log(notification.type);
        });

In this example, all notifications sent to `App\Models\User` instances via the `broadcast` channel would be received by the callback. A channel authorization callback for the `App.Models.User.{id}` channel is included in the default `BroadcastServiceProvider` that ships with the Laravel framework.

이 예제에서 `broadcast` 채널을 통해 `App\Models\User` 인스턴스로 전송된 모든 알림은 콜백에서 수신됩니다. `App.Models.User.{id}` 채널에 대한 채널 인증 콜백은 라라벨 프레임워크와 함께 제공되는 기본 `BroadcastServiceProvider`에 포함되어 있습니다.