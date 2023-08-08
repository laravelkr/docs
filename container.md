# 서비스 컨테이너

- [시작하기](#introduction)
  - [설정이 필요없는 의존성 해결](#zero-configuration-resolution)
  - [컨테이너를 사용할 때](#when-to-use-the-container)
- [바인딩](#binding)
    - [기본적인 바인딩](#binding-basics)
    - [인터페이스에 구현객체 바인딩하기](#binding-interfaces-to-implementations)
    - [문맥에 따른 조건적 바인딩](#contextual-binding)
    - [기본 타입 바인딩](#binding-primitives)
    - [Typed Variadic 바인딩](#binding-typed-variadics)
    - [태깅](#tagging)
    - [바인딩 확장](#extending-bindings)
- [의존성 해결](#resolving)
    - [Make 메소드](#the-make-method)
    - [자동 주입](#automatic-injection)
- [메소드 호출 & 주입](#method-invocation-and-injection)
- [컨테이너 이벤트](#container-events)
- [PSR-11](#psr-11)

<a name="introduction"></a>
## 시작하기

라라벨의 서비스 컨테이너는 클래스의 의존성을 관리하고 의존성을 주입하는 강력한 도구 입니다. 의존성 주입이라는 멋진 말의 의미는 다음과 같습니다. 클래스간의 의존성은 클래스 생성될 때 또는 경우에 따라 "setter" 메소드에 의해서 "주입" 된다는 의미입니다.  

간단한 예제를 들어 봅시다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Repositories\UserRepository;
    use App\Models\User;

    class UserController extends Controller
    {
        /**
         * The user repository implementation.
         *
         * @var UserRepository
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
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return Response
         */
        public function show($id)
        {
            $user = $this->users->find($id);

            return view('user.profile', ['user' => $user]);
        }
    }

이 예제에서 `UserController`는 데이터 소스로부터 사용자를 조회할 필요가 있습니다. 따라서 우리는 사용자를 조회할 수 있는 서비스를 **주입** 할 것입니다. 여기에서는, `UserRepository` 가  [Eloquent](/docs/{{version}}/eloquent)를 사용하여 데이터베이스로부터 사용자 정보를 조회합니다. repository 가 주입되었기 때문에, 원하는 경우 손쉽게 다른 구현 객체로 변경할 수 있습니다. 또한 애플리케이션을 테스트할 때 손쉽게 "목킹" 하거나, 더미 `UserRepository` 구현체를 생성할 수도 있습니다. 

라라벨 서비스 컨테이너를 깊이 이해하는 것은 강력하고 큰 애플리케이션을 구축할 때나 라라벨 코어에 공헌하기 위해서 아주 중요한 부분입니다.

<a name="zero-configuration-resolution"></a>
### 설정이 필요없는 의존성 해결

클래스에 의존성이 없거나 인터페이스가 아닌 다른 구현 된 클래스에만 의존하는 경우, 컨테이너는 해당 클래스에 대한 의존성 해결을 지시받을 필요가 없습니다. 예를 들어 'routes/web.php' 파일에 다음 코드를 넣을 수 있습니다.

    <?php

    class Service
    {
        //
    }

    Route::get('/', function (Service $service) {
        die(get_class($service));
    });

이 예시에서 애플리케이션의 `/` 경로로 접속하면 자동으로 라우트 핸들러를 통해 `Service` 클래스에 대한 의존성을 해결하고 주입합니다. 이것은 여러분들이 비대한 설정 파일들에 대한 걱정 없이 의존성 주입의 이점을 누리며 애플리케이션을 개발할 수 있다는 것을 의미합니다.

고맙게도 라라벨 애플리케이션을 개발할 때 작성할 [컨트롤러](/docs/{version}/controllers), [이벤트 리스너](/docs/{version}/events), [미들웨어](/docs/{vers}/{version}/middware) 등을 포함한 클래스는 컨테이너를 통해 종속성을 자동으로 수신합니다.

<a name="when-to-use-the-container"></a>
### 컨테이너를 사용할 때

설정이 따로 필요없는 의존성 해결 덕분에, 라우트, 컨트롤러, 이벤트 리스너 그리고 그 외에도 컨테이너와 직접 상호 작용하지 않고 타입힌팅으로 의존성을 해결할 수 있습니다. 예를 들어, 라우트 명시에 `Illuminate\Http\Request` 객체를 타입힌팅 하면 현재 요청 에 쉽게 액세스 할 수 있습니다. 컨테이너와 직접 상호작용하는 코드를 작성하지 않더라도, 이면에선 의존성 주입들을 관리해줍니다.

    use Illuminate\Http\Request;

    Route::get('/', function (Request $request) {
        // ...
    });

많은 상황에서, 파사드와 자동 의존성 주입 덕분에 라라벨 애플리케이션을 컨테이너에 무엇이든 직접 바인딩하거나 의존성 해결할 필요 없이 만들 수 있습니다. 그럼 언제 컨테이너와 직접 상호작용할까요? 두 상황을 한번 예시로 봅시다.

먼저, 인터페이스를 구현한 클래스를 작성하고 라우트나 클래스 생성자에 타입힌팅 하고 싶으면, [인터페이스에 구현객체 바인딩하기](#binding-interfaces-to-implementations) 를 해야 합니다. 두번째로 다른 라라벨 개발자들에게 공유하려는 [라라벨 패키지 작성하기](/docs/{{version}}/packages) 를 진행 중이라면, 컨테이너에 패키지의 서비스들을 바인딩해야 할 수 있습니다.

<a name="binding"></a>
## 바인딩

<a name="binding-basics"></a>
### 기본적인 바인딩 

대부분의 서비스 컨테이너 바인딩들은 [서비스 프로바이더](/docs/{{version}}/providers) 내에서 등록됩니다. 따라서 이러한 모든 예제들은 해당 컨텍스트에서 컨테이너를 사용하는 데모가 될 것입니다.

> **Note**
> 특정 인터페이스에 대한 의존성이 없을 때에는 컨테이너에 클래스를 바인딩 할 필요는 없습니다. 이러한 객체들은 리플랙션에 의해서 자동으로 의존성이 해결되기 때문에, 컨테이너가 각각의 객체들이 어떻게 생성될지 알 필요는 없습니다. 

#### 간단한 바인딩 

서비스 프로바이더 안에서는 항상 `$this->app` 속성을 통해서 컨테이너 인스턴스에 접근 할 수 있습니다. 또한 `bind` 메소드를 사용하여 클래스나 인터페이스 이름에 대한 의존성을 우리가 원하는 클래스의 인스턴스를 반환하는 `Closure`를 등록하여 바인딩 할 수 있습니다.

    $this->app->bind('HelpSpot\API', function ($app) {
        return new \HelpSpot\API($app->make('HttpClient'));
    });

클로저에서 컨테이너 자신을 인자로 전달 받고 있다는 것에 유의하십시오. 이를 통해서 연결된 객체의 의존성 문제를 위해서 컨테이너 자신을 사용할 수 있습니다. 

#### 싱글톤으로 바인딩하기

`singleton` 메소드로 클래스나 인터페이스를 바인딩 하면 컨테이너는 한 번만 해당 의존성을 해결합니다. 싱글톤 바인딩으로 의존성이 해결되면, 컨테이너의 다른 부분에서 호출될 때 동일한 객체 인스턴스가 반환될 것입니다.

    $this->app->singleton('HelpSpot\API', function ($app) {
        return new \HelpSpot\API($app->make('HttpClient'));
    });

<a name="binding-scoped"></a>
#### Scoped 싱글턴 바인딩

`scoped` 메소드는 주어진 Laravel 요청/작업 라이프사이클 내에서 한 번만 해결되어야 하는 클래스 또는 인터페이스를 컨테이너에 바인딩합니다. `singleton` 메서드와 유사하지만, `scoped` 메서드를 통해 등록된 인스턴스는 [Laravel Octane](/docs/{{version}}/octane) 워커가 새 요청을 처리하거나 [큐 워커](/docs/{{version}}/queues)가 새 "라이프사이클"을 시작할 때마다 컨테이너에서 비워집니다.

    use App\Services\Transistor;
    use App\Services\PodcastParser;

    $this->app->scoped(Transistor::class, function ($app) {
        return new Transistor($app->make(PodcastParser::class));
    });

<a name="binding-instances"></a>
#### 인스턴스를 바인딩하기

`instance` 메소드를 사용하여 이미 존재하는 객체의 인스턴스를 컨테이너에 바인딩 할 수 있습니다. 이후 컨테이너에서 호출이 될 때는 매번 주어진 인스턴스가 반환됩니다. 

    $api = new \HelpSpot\API(new HttpClient);

    $this->app->instance('HelpSpot\API', $api);

<a name="binding-interfaces-to-implementations"></a>
### 인터페이스에 구현객체 바인딩하기

서비스 컨테이너의 강력한 기능 중 하나는 주어진 구현 객체에 인터페이스를 바인딩 할 수 있다는 것입니다. 예를 들어 `EventPusher` 인터페이스와 `RedisEventPusher` 구현이 있다고 가정해 보겠습니다. 이 인터페이스를 구현한 `RedisEventPusher` 객체를 구성한 뒤에 이 객체를 다음과 같이 서비스 컨테이너에 등록할 수 있습니다.

    $this->app->bind(
        'App\Contracts\EventPusher',
        'App\Services\RedisEventPusher'
    );

이것은 구문은 `EventPusher` 인터페이스의 구현 객체가 필요할 때 컨테이너가 `RedisEventPusher` 을 주입해준다는 것을 말합니다. 이제 우리는 `EventPusher` 인터페이스에 대한 타입을 생성자에 지정하면 어디에서라도 서비스 컨테이너가 의존성을 주입해줍니다.

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

때때로 동일한 인터페이스에 대한 2가지 구현 객체가 있고, 각각의 클래스마다 다른 구현 객체를 전달하고자 할 수도 있습니다. 예를 들어 각각의 컨트롤러가 다른 `Illuminate\Contracts\Filesystem\Filesystem` [contract](/docs/{{version}}/contracts) 구현체에 의존한다면, 라라벨은 간단하고 유연한 인터페이스를 통해서 다음 행동을 정의합니다. 

    use App\Http\Controllers\PhotoController;
    use App\Http\Controllers\UploadController;
    use App\Http\Controllers\VideoController;
    use Illuminate\Contracts\Filesystem\Filesystem;
    use Illuminate\Support\Facades\Storage;

    $this->app->when(PhotoController::class)
              ->needs(Filesystem::class)
              ->give(function () {
                  return Storage::disk('local');
              });

    $this->app->when([VideoController::class, UploadController::class])
              ->needs(Filesystem::class)
              ->give(function () {
                  return Storage::disk('s3');
              });

<a name="binding-primitives"></a>
#### 기본 타입 바인딩

때로는, 클래스가 주입되는 클래스들을 받아들일 수도 있지만, 정수형과 같은 기본 타입의 값들을 주입 할 필요가 있을 수도 있습니다. 여러분은 손쉽게 문맥에 따라 조건적 바인딩을 통해서 클래스가 필요한 값을 주입할 수 있습니다.

    use App\Http\Controllers\UserController;

    $this->app->when(UserController::class)
              ->needs('$variableName')
              ->give($value);

때때로 클래스는 태그 된 인스턴스의 배열에 의존 할 수 있습니다. `giveTagged` 메소드를 사용하면 해당 태그로 모든 컨테이너 바인딩을 쉽게 삽입 할 수 있습니다.

    $this->app->when(ReportAggregator::class)
        ->needs('$reports')
        ->giveTagged('reports');

<a name="binding-typed-variadics"></a>
### Typed Variadic 바인딩

때때로 가변 생성자 인수를 사용하여 유형이 지정된 객체의 배열을 받는 클래스가 있을 수 있습니다.

    class Firewall
    {
        protected $logger;
        protected $filters;

        public function __construct(Logger $logger, Filter ...$filters)
        {
            $this->logger = $logger;
            $this->filters = $filters;
        }
    }

컨텍스트 바인딩을 사용하면 해결 된 `Filter` 인스턴스의 배열을 반환하는 Closure와 함께 `give` 메서드를 제공하여 이 종속성을 해결할 수 있습니다.

    $this->app->when(Firewall::class)
              ->needs(Filter::class)
              ->give(function ($app) {
                    return [
                        $app->make(NullFilter::class),
                        $app->make(ProfanityFilter::class),
                        $app->make(TooLongFilter::class),
                    ];
              });

편의를 위해 `Firewall`에 `Filter` 인스턴스가 필요 할 때마다 컨테이너에서 확인 할 클래스 이름 배열을 제공 할 수도 있습니다.

    $this->app->when(Firewall::class)
              ->needs(Filter::class)
              ->give([
                  NullFilter::class,
                  ProfanityFilter::class,
                  TooLongFilter::class,
              ]);

<a name="variadic-tag-dependencies"></a>
#### 가변 태그 종속성

때때로 클래스는 주어진 클래스 (`Report ... $reports`)로 타입 힌트 된 가변 종속성을 가질 수 있습니다. `needs` 및 `giveTagged` 메소드를 사용하면 주어진 종속성에 대해 해당 태그로 모든 컨테이너 바인딩을 쉽게 삽입 할 수 있습니다.

    $this->app->when(ReportAggregator::class)
        ->needs(Report::class)
        ->giveTagged('reports');

<a name="tagging"></a>
### 태깅

가끔은, 바인딩의 특정 "카테고리" 전체에 대한 의존성 해결을 해야 할 때도 있습니다. 예를 들어, 서로 다른 `Report` 인터페이스의 구현 객체를 포함하는 배열을 전달받는 보고서 수집기를 개발하고 있다고 해봅시다. `Report` 구현 객체를 등록한 뒤에, `tag` 메소드를 사용하여 태그를 달 수 있습니다.

    $this->app->bind('SpeedReport', function () {
        //
    });

    $this->app->bind('MemoryReport', function () {
        //
    });

    $this->app->tag(['SpeedReport', 'MemoryReport'], 'reports');

서비스에 태그가 붙으면 `tagged` 메소드를 사용하여 손쉽게 의존성을 해결할 수 있습니다. 

    $this->app->bind('ReportAggregator', function ($app) {
        return new ReportAggregator($app->tagged('reports'));
    });

<a name="extending-bindings"></a>
### 바인딩 확장

`extend` 메소드로 서비스의 의존성을 수정할 수 있습니다. 예를 들어, 서비스의 의존성이 해결되었을 때, 서비스를 꾸미거나(decorate) 혹은 설정하는 위한 추가 코드를 실행할 수 있습니다. `extend` 메서드는 두 인자를 받습니다. 하나는 확장하려는 서비스 클래스이고 다른 하나는 수정된 서비스를 반환할 클로저입니다. 클로저는 해결중인 서비스와 컨테이너 인스턴스를 입력 받습니다.  

    $this->app->extend(Service::class, function ($service, $app) {
        return new DecoratedService($service);
    });

<a name="resolving"></a>
## 의존성 해결

<a name="the-make-method"></a>
#### `make` 메소드

컨테이너로 부터 클래스 인스턴스에 대한 의존성을 해결하기 위해서 `make` 메소드를 사용할 수 있습니다. `make` 메소드는 의존성 해결을 위해 여러분이 원하는 클래스나 인터페이스에 대한 이름을 전달받습니다. 

    use App\Services\Transistor;

    $transistor = $this->app->make(Transistor::class);

클래스의 의존성 중 일부가 컨테이너를 통해 해결될 수 없는 경우 `makeWith` 메서드에 연관 배열로 전달하여 의존성을 주입할 수 있습니다. 예를 들어 `Transistor` 서비스에 필요한 생성자 인수 `$id`를 수동으로 전달할 수 있습니다.

    use App\Services\Transistor;

    $transistor = $this->app->makeWith(Transistor::class, ['id' => 1]);

`$app` 변수에 접근할 수 없는 서비스 프로바이더 밖에서 컨테이너로 부터 클래스 인스턴스를 리졸브하려면 `App` [파사드](/docs/{{version}}/facades)나 `app` [헬퍼](/docs/{{version}}/helpers#method-app)를 사용할 수 있습니다.

    use App\Services\Transistor;
    use Illuminate\Support\Facades\App;

    $transistor = App::make(Transistor::class);

    $transistor = app(Transistor::class);

컨테이너에 의해 리졸브되는 클래스에 컨테이너 자체를 의존성 주입하려면 클래스 생성자에 `Illuminate\Container\Container` 클래스를 타입힌트 하면 됩니다.

    use Illuminate\Container\Container;

    /**
     * Create a new class instance.
     *
     * @param  \Illuminate\Container\Container  $container
     * @return void
     */
    public function __construct(Container $container)
    {
        $this->container = $container;
    }

<a name="automatic-injection"></a>
#### 자동 주입

앞서 이야기한 방법과 다르게, 그리고 가장 중요한 것은 [컨트롤러](/docs/{{version}}/controllers), [이벤트 리스너](/docs/{{version}}/events), [미들웨어](/docs/{{version}}/middleware) 등 을 포함하여 클래스의 생성자에 "타입-힌트" 를 선언함으로써 컨테이너가 의존성을 해결할 수 있도록 하는 것입니다. 또한 [queued jobs](/docs/{{version}}/queues)의 `handle` 메소드에도 종속성을 입력할 수 있습니다. 실제로 이 방법이 개발에서 컨테이너에 의해서 객체의 의존성을 해결할 때 가장 많이 사용되는 방법입니다.

예를 들어 컨트롤러의 생성자에서 타입 힌트로 지정된 Repository를 정의했다고 가정해 보겠습니다. 해당 Repository는 자동으로 의존성이 해결되어 클래스에 주입될 것입니다. 

    <?php

    namespace App\Http\Controllers;

    use App\Models\Users\Repository as UserRepository;

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

<a name="method-invocation-and-injection"></a>
## 메소드 호출 & 주입

때때로 메소드의 의존성들을 자동으로 주입할 수 있는 상태에서 객체 인스턴스에서 메소드를 호출하고 싶을 수 있습니다. 예를 들어, 아래와 같은 클래스가 있습니다.

    <?php

    namespace App;

    use App\Repositories\UserRepository;

    class UserReport
    {
        /**
         * Generate a new user report.
         *
         * @param  \App\Repositories\UserRepository  $repository
         * @return array
         */
        public function generate(UserRepository $repository)
        {
            // ...
        }
    }

`generate` 메소드를 컨테이너를 통해 호출하고 싶을 수 있습니다. 아래와 같이 사용할 수 있습니다.

    use App\UserReport;
    use Illuminate\Support\Facades\App;

    $report = App::call([new UserReport, 'generate']);

`call` 메소드는 모든 PHP callable 을 허용합니다. 컨테이너의 `call` 메소드는 의존성들을 해결하는 동안 클로저를 호출하는데 사용될 수도 있다는 얘기입니다.

    use App\Repositories\UserRepository;
    use Illuminate\Support\Facades\App;

    $result = App::call(function (UserRepository $repository) {
        // ...
    });

<a name="container-events"></a>
## 컨테이너 이벤트

서비스 컨테이너는 객체의 의존성 해결을 수행할 때마다 이벤트를 발생시킵니다. `resolving` 메소드를 사용하여 이 이벤트들에 대응할 수 있습니다. 

    $this->app->resolving(function ($object, $app) {
        // Called when container resolves object of any type...
    });

    $this->app->resolving(\HelpSpot\API::class, function ($api, $app) {
        // Called when container resolves objects of type "HelpSpot\API"...
    });

보시다시피, 의존성이 해결된 객체가 콜백에 전달되어, 최종적으로 객체를 필요로 하는 대상에 전달하기 전에 추가적으로 객체의 속성을 설정할 수 있습니다.

<a name="psr-11"></a>
## PSR-11

라라벨의 서비스 컨테이너는 [PSR-11](https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-11-container.md) 인터페이스를 구현합니다. 따라서, PSR-11 인터페이스를 타입힌트 하여 라라벨의 컨테이너 인스턴스에 접근이 가능합니다. 

    use Psr\Container\ContainerInterface;

    Route::get('/', function (ContainerInterface $container) {
        $service = $container->get('Service');

        //
    });

지정된 식별자를 해결할 수 없는 경우는 예외가 던져집니다. 식별자가 바인드 되지 않은 경우 예외는 `Psr\Container\NotFoundExceptionInterface` 인스턴스입니다. 식별자가 바인드 되었지만 해결할 수 없는 경우에는 `Psr\Container\ContainerExceptionInterface` 인스턴스가 던져집니다.
