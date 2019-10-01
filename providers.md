# 서비스 프로바이더

- [시작하기](#introduction)
- [서비스 프로바이더 작성하기](#writing-service-providers)
    - [Register 메소드](#the-register-method)
    - [Boot 메소드](#the-boot-method)
- [프로바이더 등록하기](#registering-providers)
- [지연된 프로바이더](#deferred-providers)

<a name="introduction"></a>
## 시작하기

서비스 프로바이더는 라라벨 애플리케이션의 부팅(부트스트래핑)의 가장 핵심이라고 할 수 있습니다. 여러분의 애플리케이션과 마찬가지로 라라벨의 모든 코어 서비스는 서비스 프로바이더를 통해서 부트스트래핑 됩니다.

그런데 "부트스트래핑" 이란 과연 무엇을 의미하는 것일까요? 일반적으로는 서비스 컨테이너에 바인딩을 등록하는 것을 포함해서 이벤트 리스너, 미들웨어 그리고 라우트등을 **등록** 하는 것을 의미합니다. 서비스 프로바이더는 애플리케이션 구성의 핵심입니다.

라라벨에 포함되어 있는 `config/app.php` 파일을 열어 본다면 `providers` 배열을 볼 수 있을 것입니다. 배열 안에 있는 모든 서비스 프로바이더 클래스가 애플리케이션에 로드됩니다. 대부분의 프로바이더는 "지연된" 프로바이더입니다. 이 말은 모든 요청에 대해서 반드시 로드되지 않고 실제로 필요할 때에 로드 된다는 것을 의미합니다.

여기에서는 서비스 프로바이더를 작성하는 방법과 라라벨 애플리케이션에 등록하는 방법을 배워봅시다.

<a name="writing-service-providers"></a>
## 서비스 프로바이더 작성하기

모든 서비스 프로바이더는 `Illuminate\Support\ServiceProvider` 클래스를 상속받습니다. 대부분의 서비스 프로바이더는 `register` 와 `boot` 메소드를 가지고 있습니다. `register` 메소드 안에서는 **[서비스 컨테이너](/docs/{{version}}/container)에 등록만을 하도록 합니다**. `register` 메소드안에서는 다른 이벤트 리스너나 라우트 또는 기타 기능의 일부등을 등록하지 말아야 합니다.

아티즌 CLI에서는 `make:provider` 명령어를 통해서 새로운 프로바이더를 생성할 수 있습니다.

    php artisan make:provider RiakServiceProvider

<a name="the-register-method"></a>
### Register 메소드

이미 설명한 바와 같이, `register` 메소드 안에서는, [서비스 컨테이너](/docs/{{version}}/container)에 무언가를 바인딩 하는 작업만을 수행 해야 합니다. 여러분은 `register` 메소드 안에서 어떠한 이벤트 리스너, 라우트, 또는 다른 기능들에 대한 코드를 넣어서는 절대 안됩니다. 그렇지 않으면, 서비스 프로바이더가 아직 로드 하지 않은 서비스를 의도치 않게 사용해 버리고 말것입니다.

기본적인 서비스 프로바이더를 살펴보겠습니다. 서비스 프로바이더의 메소드 안에서 언제든지 `$app` 속성을 사용할 수 있으며, 이를 통해 서비스 컨테이너에 접근할 수 있습니다.

    <?php

    namespace App\Providers;

    use Riak\Connection;
    use Illuminate\Support\ServiceProvider;

    class RiakServiceProvider extends ServiceProvider
    {
        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            $this->app->singleton(Connection::class, function ($app) {
                return new Connection(config('riak'));
            });
        }
    }

이 서비스 프로바이더는 `register` 메소드만 정의되어 있습니다. 그리고 이 메소드를 통해서 서비스 컨테이너에 `Riak\Connection` 구현 객체를 정의하고 있습니다. 서비스 컨테이너가 어떻게 작동하는지 이해하지 못하겠다면, [컨테이너 문서](/docs/{{version}}/container)를 확인하십시오.

#### `bindings` 과 `singletons` 속성

서비스 프로바이더가 동일한 (simple) 바인딩 여러개를 등록한다면, 각각의 컨테이너 바인딩을 일일이 등록하는 대신에 `bindings` 와 `singletons` 속성을 사용할 수 있습니다. 프레임워크에서 서비스 프로바이더가 로드 될 때, 이 속성들을 자동으로 체크하고 바인딩을 등록합니다.

    <?php

    namespace App\Providers;

    use App\Contracts\ServerProvider;
    use App\Contracts\DowntimeNotifier;
    use App\Services\ServerToolsProvider;
    use Illuminate\Support\ServiceProvider;
    use App\Services\PingdomDowntimeNotifier;
    use App\Services\DigitalOceanServerProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * All of the container bindings that should be registered.
         *
         * @var array
         */
        public $bindings = [
            ServerProvider::class => DigitalOceanServerProvider::class,
        ];

        /**
         * All of the container singletons that should be registered.
         *
         * @var array
         */
        public $singletons = [
            DowntimeNotifier::class => PingdomDowntimeNotifier::class,
            ServerToolsProvider::class => ServerToolsProvider::class,
        ];
    }

<a name="the-boot-method"></a>
### Boot 메소드

그럼 이제 서비스 프로바이더 안에서 [뷰 컴포저](/docs/{{version}}/views#view-composers)를 등록할 필요가 있다면 어떻게 해야 할까요? 그런 작업은 `boot` 메소드 안에서 해야합니다. **이 메소드는 모든 다른 서비스 프로바이더들이 등록된 이후에 호출됩니다** 즉, 프레임 워크에 의해 등록된 다른 모든 서비스들에 액세스 할 수 있다는 것을 의미합니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\ServiceProvider;

    class ComposerServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            view()->composer('view', function () {
                //
            });
        }
    }

#### Boot 메소드의 의존성 주입

여러분은 서비스 프로바이더의 `boot` 메소드에서 의존성 주입을 위해서 타입 힌트를 사용할 수 있습니다. [서비스 컨테이너](/docs/{{version}}/container)는 자동으로 필요한 의존 객체를 주입할 것입니다.

    use Illuminate\Contracts\Routing\ResponseFactory;

    public function boot(ResponseFactory $response)
    {
        $response->macro('caps', function ($value) {
            //
        });
    }

<a name="registering-providers"></a>
## 프로바이더 등록하기

모든 서비스 프로바이더들은 `config/app.php` 설정 파일에 등록되어 있습니다. 이 파일에는 서비스 프로바이더들의 클래스 이름을 나열하고 등록할 수 있는 `providers` 배열이 포함되어 있습니다. 기본적으로는 라라벨의 코어 서비스 프로바이더들이 배열에 나열되어 있습니다. 이 프로바이더들이 라라벨의 메일러, 큐, 캐시등과 같은 핵심적인 컴포넌트들을 부트스트랩핑 하게 됩니다.

여러분의 프로바이더들을 등록하려면 이 배열에 추가 하면 됩니다.

    'providers' => [
        // Other Service Providers

        App\Providers\ComposerServiceProvider::class,
    ],

<a name="deferred-providers"></a>
## 지연(deferred) 프로바이더

만약 여러분의 프로바이더가 **단지** [서비스 컨테이너](/docs/{{version}}/container)에 바인딩을 등록하기만 한다면, 등록된 바인딩이 실제로 필요할때까지 등록 자체를 지연(deferred) 시킬 수 있습니다. 이러한 프로바이더 로딩의 지연(deferred)은 모든 요청에 프로바이더를 파일 시스템에서 로드하지 않으므로 애플리케이션의 성능을 향상시킬 것입니다.

라라벨은 지연된 서비스 프로바이더가 제공하는 모든 서비스 목록과 해당 서비스 프로바이더 클래스 이름을 컴파일하고 저장합니다. 그런 다음 이러한 서비스들 중 하나를 의존성 해결하려고 할 때에만, 라라벨이 서비스 프로바이더를 로드합니다.

프로바이더를 지연(defer) 로딩 하려면, `\Illuminate\Contracts\Support\DeferrableProvider` 인터페이스를 구현하고 `provides` 메소드를 정의하면 됩니다. `provides` 메소드는 프로바이더에 의해서 바인딩이 등록된 서비스 컨테이너를 리턴해야 합니다.

    <?php

    namespace App\Providers;

    use Riak\Connection;
    use Illuminate\Support\ServiceProvider;
    use Illuminate\Contracts\Support\DeferrableProvider;

    class RiakServiceProvider extends ServiceProvider implements DeferrableProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            $this->app->singleton(Connection::class, function ($app) {
                return new Connection($app['config']['riak']);
            });
        }

        /**
         * Get the services provided by the provider.
         *
         * @return array
         */
        public function provides()
        {
            return [Connection::class];
        }
    }
