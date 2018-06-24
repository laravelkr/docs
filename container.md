# 서비스 컨테이너

- [소개](#introduction)
- [바인딩](#binding)
    - [구현 객체에 인터페이스 바인딩하기](#binding-interfaces-to-implementations)
    - [문맥에 따른 조건적 바인딩](#contextual-binding)
    - [태깅](#tagging)
- [의존성 해결](#resolving)
- [컨테이너 이벤트](#container-events)

<a name="introduction"></a>
## 소개

라라벨의 서비스 컨테이너는 클래스의 의존성을 관리하는 강력한 도구 입니다. 의존성 주입이라는 멋진 말의 의미는 다음과 같습니다: 클래스간의 의존성은 클래스 생성될 때 또는 경우에 따라 "setter" 메소드에 의해서 "주입" 된다는 의미입니다.  

간단한 예제를 들어 봅시다:

    <?php

    namespace App\Jobs;

    use App\User;
    use Illuminate\Contracts\Mail\Mailer;
    use Illuminate\Contracts\Bus\SelfHandling;

    class PurchasePodcast implements SelfHandling
    {
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
         * @return void
         */
        public function handle()
        {
            //
        }
    }

이 예제에서 `PurchasePodcast` 작업은 podcast 가 주문이되면 이메일을 보내려 합니다. 따라서 우리는 이메일을 보내기 위한 서비스를 **주입** 할 것입니다. 서비스는 주입되었기 때문에, 원하는 경우 쉽게 다른 구현 객체로 변경할 수 있습니다. 또한 애플리케이션을 테스트 할 때 손쉽게 "목킹" 하여 메일 송신서비스의 더미 클래스를 만들 수 있습니다. 

라라벨 서비스 컨테이너를 깊이 이해하는 것은 강력하고 큰 애플리케이션을 구축 할 때나 라라벨 코어에 공헌하기 위해서 아주 중요한 부분입니다.

<a name="binding"></a>
## 바인딩

대부분의 서비스 컨테이너 바인딩들은 [서비스 프로바이더](/docs/5.1/providers) 내에서 등록됩니다. 따라서 이러한 모든 예제들은 해당 컨텍스트에서 컨테이너를 사용하는 데모가 될것입니다. 특정 인터페이스에 대한 의존성이 없을 때에는 컨테이너에 클래스를 바인딩 할 필요는 없습니다. 의존성과 관련된 객체들은 PHP의 리플랙션에 의해서 자동으로 생성되기 때문에 컨테이너가 각각의 객체들이 어떻게 생성될지 알 필요는 없습니다. 

서비스 프로바이더 안에서는 항상 `$this->app` 을 통해서 컨테이너 인스턴스에 접근 할 수 있습니다. 또한 `bind` 메소드를 사용하여 클래스나 인터페이스 이름에 대한 의존성을 우리가 원하는 클래스의 인스턴스를 반환하는 `Closure`를 등록하여 바인딩 할 수 있습니다.

    $this->app->bind('HelpSpot\API', function ($app) {
        return new HelpSpot\API($app['HttpClient']);
    });

클로저에서 컨테이너 자신을 인자로 전달 받고 있다는 것에 유의하십시오. 이를 통해서 연결된 객체의 의존성 문제를 위해서 컨테이너 자신을 사용할 수 있습니다. 

#### 싱글톤으로 바인딩하기

`singleton` 메소드로 클래스나 인터페이스를 바인딩 하면 한번만 해당 의존성이 해결되고, 그 다음부터는 동일한 인스턴스가 반환됩니다. 

    $this->app->singleton('FooBar', function ($app) {
        return new FooBar($app['SomethingElse']);
    });

#### 인스턴스를 바인딩하기

`instance` 메소드를 사용하여 이미 존재하는 객체의 인스턴스를 컨테이너에 바인딩 할 수 있습니다. 이후 컨테이너에서 호출이 될때는 매번 주어진 인스턴스가 반환됩니다. 

    $fooBar = new FooBar(new SomethingElse);

    $this->app->instance('FooBar', $fooBar);

<a name="binding-interfaces-to-implementations"></a>
### 구현 객체에 인터페이스 바인딩하기

서비스 컨테이너의 강력한 기능중 하나는 주어진 구현 객체에 인터페이스를 바인딩 할 수 있다는 것입니다. 예를 들어 `EventPusher` 인터페이스와 `RedisEventPusher` 구현이 있다고 가정해 보겠습니다. 이 인터페이스를 구현한 `RedisEventPusher` 객채를 구성한 뒤에 이 객체를 다음과 같이 서비스 컨테이너에 등록할 수 있습니다:

    $this->app->bind('App\Contracts\EventPusher', 'App\Services\RedisEventPusher');

이것은 EventPusher 인터페이스의 구현 객체가 필요할 때 컨테이너가 `RedisEventPusher` 을 주입해준다는 것을 말합니다. 이제 우리는 `EventPusher` 인터페이스에 대한 타입을 생성자에 지정하면 어디에서라도 서비스 컨테이너가 의존성을 주입해줍니다.

    use App\Contracts\EventPusher;

    /**
     * Create a new class instance.
     *
     * @param  EventPusher  $pusher
     * @return void
     */
    public function __construct(EventPusher $pusher)
    {
        $this->pusher = $pusher;
    }

<a name="contextual-binding"></a>
### 문맥에 따른 조건적 바인딩

때때로 동일한 인터페이스에 대한 2가지 구현 객체가 있고, 각각의 클래스마다 다른 구현 객체를 전달하고자 할 수도 있습니다. 예를 들어 시스템이 새로운 주문을 받을 때에는 Pusher 대신에 [PubNub](http://www.pubnub.com/)를 통해서 이벤트를 전송하고자 할 수도 있습니다. 라라벨에서는 이 같은 행동을 정의 할 수 있는 간단하고 유연한 인터페이스를 제공합니다:

    $this->app->when('App\Handlers\Commands\CreateOrderHandler')
              ->needs('App\Contracts\EventPusher')
              ->give('App\Services\PubNubEventPusher');

`give` 메소드에는 클로저를 전달 할 수도 있습니다.

    $this->app->when('App\Handlers\Commands\CreateOrderHandler')
              ->needs('App\Contracts\EventPusher')
              ->give(function () {
                      // Resolve dependency...
                  });

<a name="tagging"></a>
### 태깅

가끔은, 바인딩의 특정 "카테고리" 전체에 대한 의존성 해결을 해야 할 때도 있습니다. 예를 들어, 서로 다른 `Report` 인터페이스의 구현 객체를 포함하는 배열을 전달 받는 보고서 수집기를 개발하고 있다고 해봅시다. `Report` 구현 객체를 등록한 뒤에, `tag` 메소드를 사용하여 태그를 달 수 있습니다.:


    $this->app->bind('SpeedReport', function () {
        //
    });

    $this->app->bind('MemoryReport', function () {
        //
    });

    $this->app->tag(['SpeedReport', 'MemoryReport'], 'reports');

서비스에 태그가 붙으면 `tagged` 메소드를 사용하여 손쉽게 의존성을 해결할 수 있습니다:

    $this->app->bind('ReportAggregator', function ($app) {
        return new ReportAggregator($app->tagged('reports'));
    });

<a name="resolving"></a>
## 의존성 해결

컨테이너 밖에서 의존성 해결(바인딩한 대상을 호출하여 값을 반환받는것)을 하는데는 몇가지 방법이 있습니다. 먼저 여러분이 원하는 클래스나 인터페이스에 대한 이름을 전달 받는 `make` 메소드를 사용할 수 있습니다. 

    $fooBar = $this->app->make('FooBar');

두번째로 컨테이너에 배열과 같이 접근할 수 있는데, 이는 컨테이너가 `ArrayAccess` 인터페이스의 구현체 이기 때문입니다.

    $fooBar = $this->app['FooBar'];

마지막으로 가장 중요한 방법은 [컨트롤러](/docs/{{version}}/controllers), [이벤트 리스너](/docs/{{version}}/events), [queue jobs](/docs/{{version}}/queues), [미들웨어](/docs/{{version}}/middleware) 그리고 다른 곳에서도 클래스의 생성자에 "타입-힌트" 를 선언함으로써 컨테이너가 의존성을 해결할 수 있도록 하는 것입니다. 실제로는 이 방법이 개발에서 컨테이너가 객체의 의존성 문제를 해결하는 가장 많이 사용되는 방법입니다.

컨테이너는 의존성을 해결하고자 하는 클래스의 의존 객체를 자동으로 주입해 줍니다. 예를 들어 컨트롤러의 생성자에서 타입 힌트로 지정된 Repository를 정의했다고 가정해 보겠습니다. 해당 Repository는 자동으로 의존성이 해결되어 클래스에 주입될 것입니다. 

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Routing\Controller;
    use App\Users\Repository as UserRepository;

    class UserController extends Controller
    {
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

<a name="container-events"></a>
## 컨테이너 이벤트

서비스 컨테이너는 객체의 의존성 해결을 수행할 때 마다 이벤트를 발생시킵니다. `resolving` 메소드를 사용하여 이 이벤트들에 대응할 수 있습니다. 

    $this->app->resolving(function ($object, $app) {
        // Called when container resolves object of any type...
    });

    $this->app->resolving(FooBar::class, function (FooBar $fooBar, $app) {
        // Called when container resolves objects of type "FooBar"...
    });

보시다시피, 의존성이 해결된 객체가 콜백에 전달되어, 최종적으로 객체를 필요로 하는 대상에 전달하기 전에 추가적으로 객체의 속성을 설정 할 수 있습니다.
