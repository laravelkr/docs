# 서비스 컨테이너(Service Container)

- [소개](#introduction)
- [기본 사용법](#basic-usage)
- [구현 객체에 인터페이스를 바인딩 하는 법](#binding-interfaces-to-implementations)
- [상황에 맞는 바인딩](#contextual-binding)
- [태깅](#tagging)
- [실제 어플리케이션](#practical-applications)
- [컨테이너 이벤트](#container-events)

<a name="introduction"></a>
## 소개

라라벨의 서비스 컨테이너는 클래스의 의존성을 관리하는 강력한 도구입니다. 의존성 주입이라는 멋진 말의 의미는 다음과 같습니다: 클래스간의 의존성은 클래스 생성될 때 또는 경우에 따라 "setter" 메소드에 의해서 "주입" 된다는 의미입니다:

간단한 예제를 들어 봅시다:

    <?php namespace App\Handlers\Commands;

    use App\User;
    use App\Commands\PurchasePodcastCommand;
    use Illuminate\Contracts\Mail\Mailer;

    class PurchasePodcastHandler {

        /**
         * The mailer implementation.
         */
        protected $mailer;

        /**
         * Create a new instance.
         *
         * @param  Mailer  $mailer
         * @return void
         */
        public function __construct(Mailer $mailer)
        {
            $this->mailer = $mailer;
        }

        /**
         * Purchase a podcast.
         *
         * @param  PurchasePodcastCommand  $command
         * @return void
         */
        public function handle(PurchasePodcastCommand $command)
        {
            //
        }

    }

위의 예제에서 `PurchasePodcast` 명령어는 podcast가 주문이 되면 이메일을 보내야 할 필요가 있습니다. 그래서 우리는 이메일을 보내기 위한 서비스를 **주입** 시킬것입니다. 서비스가 주입되었기 때문에 원하는 경우 쉽게 다른 구현 객체로 바꿀 수 있습니다. 또한, 어플리케이션을 테스팅 할 때 손쉽게 "목킹" 할수 있고 메일러의 더미 구현클래스를 만들수 있습니다.

라라벨 서비스 컨테이너를 깊이 이해하는 것은 강력하고 큰 애플리케이션을 구축 할 때나 라라벨 코어에 공헌하기 위해서  중요한 부분입니다.

<!--chak-comment-서비스-컨테이너(Service-Container)-소개-->

<a name="basic-usage"></a>
## 기본 사용법

### 바인딩

대부분의 서비스 컨테이너 바인딩들은 [서비스 프로바이더](/docs/5.0/providers) 내에서 등록됩니다. 따라서 이러한 모든 예제들은 해당 컨텍스트에서 컨테이너를 사용하는 데모가 될것입니다. 하지만 만약 팩토리와 같이 어플리케이션의 다른 곳에서 컨테이너의 인스턴스가 필요하다면 컨테이너의 인스턴스가 주입되도록 `Illuminate\Contracts\Container\Container` contract를 타입힌트로 지정하면 됩니다. 다른 방법으로는 컨테이너에 액세스하기 위해`App` 파사드를 사용할 수 있습니다.

#### 기본적인 의존성 해결 등록하기

서비스 프로바이더 내에서는 항상 `$this->app`을 통해서 컨테이너 인스턴스에 엑세스 할 수 있습니다.

서비스 컨테이너에 의존성을 등록하는 방법은 클로저 콜백과 구현클래스에 인터페이스를 바인딩 하는것을 포함하여 몇 가지 방법이 있습니다. 클로저를 통해서 의존성을 등록할 때에는 컨테이너에 어떤 특정 키(주로 클래스의 이름)와 함께 특정 값을 반환하는 클로저가 등록됩니다:

    $this->app->bind('FooBar', function($app)
    {
        return new FooBar($app['SomethingElse']);
    });

#### 싱글톤으로 등록하기

때로는 컨테이너에 바인딩할 대상이 한번만 수행되고 컨테이너가 여러번 호출해도 같은 인스턴스가 반환되기를 원할 수도 있습니다.

    $this->app->singleton('FooBar', function($app)
    {
        return new FooBar($app['SomethingElse']);
    });

#### 존재하는 인스턴스를 컨테이너에 바인딩하기

`instance` 메소드를 사용하여 이미 존재하는 객체의 인스턴스를 컨테이너에 바인딩 할 수 있습니다. 이후 컨테이너에서 호출이 될때는 매번 주어진 인스턴스가 반환됩니다.

    $fooBar = new FooBar(new SomethingElse);

    $this->app->instance('FooBar', $fooBar);

### 의존성 해결

컨테이너 밖에서 의존성 해결(바인딩한 대상을 호출하여 값을 반환받는것)을 하는 데는 몇 가지 방법이 있습니다. 먼저 `make` 메소드를 사용하는 것입니다:

    $fooBar = $this->app->make('FooBar');

두 번째로는 컨테이너가 PHP의 `ArrayAccess`를 구현하고 있으므로  “배열 엑세스”를 통해서 사용하는 방법입니다:

    $fooBar = $this->app['FooBar'];

마지막으로 가장 중요한 방법은 컨트롤러, 이벤트 리스너, 큐 잡, 필터 그리고 다른 곳에서도 클래스의 생성자에 "타입-힌트”를 함으로써 컨테이너가 의존성을 해결할 수 있도록 하는 것입니다. 컨테이너는 자동으로 의존 객체를 주입해 줄것입니다:

    <?php namespace App\Http\Controllers;

    use Illuminate\Routing\Controller;
    use App\Users\Repository as UserRepository;

    class UserController extends Controller {

        /**
         * The user repository instance.
         */
        protected $users;

        /**
         * Create a new controller instance.
         *
         * @param  UserRepository  $users
         * @return void
         */
        public function __construct(UserRepository $users)
        {
            $this->users = $users;
        }

        /**
         * Show the user with the given ID.
         *
         * @param  int  $id
         * @return Response
         */
        public function show($id)
        {
            //
        }

    }

<!--chak-comment-서비스-컨테이너(Service-Container)-기본-사용법-->

<a name="binding-interfaces-to-implementations"></a>
## 구현 객체에 인터페이스 바인딩하기

### 구현된 의존객체를 주입하기

서비스 컨테이너의 강력한 기능중 하나는 주어진 구현 객체에 인터페이스를 바인딩 할 수 있다는 것입니다. 예를 들어, 어떤 어플리케이션이 실시간 이벤트를 주고받는 [Pusher](https://pusher.com) 웹서비스와 연결되어 있다고 해봅시다. 만약 Pusher의 PHP SDK를 사용하고 있다면, Pusher 클라이언트의 인스턴스를 클래스로 주입 할 수 있습니다:

    <?php namespace App\Handlers\Commands;

    use App\Commands\CreateOrder;
    use Pusher\Client as PusherClient;

    class CreateOrderHandler {

        /**
         * The Pusher SDK client instance.
         */
        protected $pusher;

        /**
         * Create a new order handler instance.
         *
         * @param  PusherClient  $pusher
         * @return void
         */
        public function __construct(PusherClient $pusher)
        {
            $this->pusher = $pusher;
        }

        /**
         * Execute the given command.
         *
         * @param  CreateOrder  $command
         * @return void
         */
        public function execute(CreateOrder $command)
        {
            //
        }

    }

이 예제에서, 클래스 의존성을 주입하는 것은 좋습니만, Pusher SDK와 완전히 커플링이 되었습니다. 만약 Pusher SDK 메소드가 변경되거나, 새로운 이벤트 서비스로 교체하기로 한다면 `CreateOrderHandler` 코드를 변경해야만 합니다.

### 인터페이스에 대한 프로그래밍

이벤트 푸시의 변경에 대해 `CreateOrderHandler`를 "분리" 시키기 위해서는, `EventPusher` 인터페이스와 `PusherEventPusher` 구현 객체를 따로 정의 하면 됩니다:

    <?php namespace App\Contracts;

    interface EventPusher {

        /**
         * Push a new event to all clients.
         *
         * @param  string  $event
         * @param  array  $data
         * @return void
         */
        public function push($event, array $data);

    }

위의 인터페이스에 대한 `PusherEventPusher` 구현 객체를 생성한 뒤에는 다음과 같이 서비스 컨테이너에 등록하면 됩니다:

    $this->app->bind('App\Contracts\EventPusher', 'App\Services\PusherEventPusher');

이것은 컨테이너에게 `EventPusher`의 구현 객체가 필요하게되면, `PusherEventPusher`를 주입하도록 알려주는 것입니다:

        /**
         * Create a new order handler instance.
         *
         * @param  EventPusher  $pusher
         * @return void
         */
        public function __construct(EventPusher $pusher)
        {
            $this->pusher = $pusher;
        }

<!--chak-comment-서비스-컨테이너(Service-Container)-구현-객체에-인터페이스-바인딩하기-->

<a name="contextual-binding"></a>
## 문맥에 알맞은 바인딩

때때로 같은 인터페이스에 대한 2가지 구현 객체가 있고, 각각의 클래스마다 다른 구현 객체를 전달하고자 할 수도 있습니다. 예를 들어, 시스템이 새로운 주문을 받을 때에는 Pusher 대신에 [PubNub](http://www.pubnub.com/)를 통해서 이벤트를 전송하고자 할 수도 있습니다. 라라벨에서는 이 같은 행동을 정의 할 수 있는 간단하고 유연한 인터페이스를 제공합니다:

    $this->app->when('App\Handlers\Commands\CreateOrderHandler')
              ->needs('App\Contracts\EventPusher')
              ->give('App\Services\PubNubEventPusher');

<!--chak-comment-서비스-컨테이너(Service-Container)-문맥에-알맞은-바인딩-->

<a name="tagging"></a>
## 태깅

가끔은, 바인딩의 특정 “카테고리” 전체에 대한 의존성 해결을 해야 할 때도 있습니다. 예를 들어, 서로 다른 `Report` 인터페이스의 구현 객체를 포함하는 배열을 전달받는 보고서 수집기를 개발하고 있다고 해봅시다. `Report` 구현 객체를 등록한 뒤에, `tag` 메소드를 사용하여 태그를 달 수 있습니다:

    $this->app->bind('SpeedReport', function()
    {
        //
    });

    $this->app->bind('MemoryReport', function()
    {
        //
    });

    $this->app->tag(['SpeedReport', 'MemoryReport'], 'reports');

서비스가 태그가 붙으면 `tagged` 메소드를 사용하여 손쉽게 의존성을 해결할 수 있습니다:

    $this->app->bind('ReportAggregator', function($app)
    {
        return new ReportAggregator($app->tagged('reports'));
    });

<!--chak-comment-서비스-컨테이너(Service-Container)-태깅-->

<a name="practical-applications"></a>
## 실용적 어플리케이션

라라벨은 어플리케이션의 유연함과 테스트의 용이함을 높이기 위해서 서비스 컨테이너를 사용할 수 있는 여러 가지 기회를 제공합니다. 주요한 한 가지 예제는 컨트롤러에서의 의존성 해결입니다. 모든 컨트롤러는 서비스 컨테이너에 의해서 의존성 해결이 가능합니다. 이것은 여러분이 컨트롤러의 생성자 안에서 타입힌트를 작성했다면 그에 해당하는 의존 객체들이 자동으로 주입된다는 것을 의미합니다.

    <?php namespace App\Http\Controllers;

    use Illuminate\Routing\Controller;
    use App\Repositories\OrderRepository;

    class OrdersController extends Controller {

        /**
         * The order repository instance.
         */
        protected $orders;

        /**
         * Create a controller instance.
         *
         * @param  OrderRepository  $orders
         * @return void
         */
        public function __construct(OrderRepository $orders)
        {
            $this->orders = $orders;
        }

        /**
         * Show all of the orders.
         *
         * @return Response
         */
        public function index()
        {
            $orders = $this->orders->all();

            return view('orders', ['orders' => $orders]);
        }

    }

위의 예제에서 `OrderRepository` 클래스는 자동으로 컨트롤러에 주입되었습니다. 이것은  [단위 테스트](/docs/5.0/testing)를 할때, 데이터베이스 레이어에 관계없이 “목업” `OrderRepository`가 컨테이너에 바인딩 될 수 있음을 의미합니다.

#### 컨테이너를 사용하는 다른 예제

물론, 위에서 말한바와 같이, 서비스 컨테이너를 통해서 라라벨이 의존성을 해결하는 곳이 컨트롤러 뿐만은 아닙니다. 여러분은 라우트 클로저, 필터, 큐 잡, 이벤트 리스너등에서 타입 힌트를 통한 의존성 주입을 할 수 있습니다. 서비스 컨테이너를 사용하는 이러한 경우의 예제는 각각의 문서를 참고하십시오.

<!--chak-comment-서비스-컨테이너(Service-Container)-실용적-어플리케이션-->

<a name="container-events"></a>
## 컨테이너 이벤트

#### 의존성 해결 리스너 등록하기

컨테이너는 객체의 의존성 해결을 수행할 때 마다 이벤트를 발생시킵니다. `resolving` 메소드를 사용하여 이 이벤트들에 대응할 수 있습니다:

    $this->app->resolving(function($object, $app)
    {
        // Called when container resolves object of any type...
    });

    $this->app->resolving(function(FooBar $fooBar, $app)
    {
        // Called when container resolves objects of type "FooBar"...
    });

의존성이 해결된 객체가 콜백에 전달됩니다.

<!--chak-comment-서비스-컨테이너(Service-Container)-컨테이너-이벤트-->
