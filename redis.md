# 레디스

- [시작하기](#introduction)
    - [설정하기](#configuration)
    - [Predis](#predis)
    - [PhpRedis](#phpredis)
- [Redis 와 상호작용](#interacting-with-redis)
    - [파이프라이닝 명령어](#pipelining-commands)
- [Pub / Sub](#pubsub)

<a name="introduction"></a>
## 시작하기

[레디스](https://redis.io) 는 키-밸류 기반의 오픈소스 저장소 입니다. 레디스는 키에 [문자열](https://redis.io/topics/data-types#strings), [해쉬](https://redis.io/topics/data-types#hashes), [리스트](https://redis.io/topics/data-types#lists), [세트](https://redis.io/topics/data-types#sets), 그리고 [정렬 세트](https://redis.io/topics/data-types#sorted-sets)를 사용할 수 있기 때문에 데이터 구조 서버로 자주 거론되고 있습니다.

라라벨에서 레디스를 사용하기 전에 여러분은 Composer 를 통해서 `predis/predis` 패키지를 설치할 필요가 있습니다:

    composer require predis/predis

이렇게 하는 대신에, PECL을 통해서 [PhpRedis](https://github.com/phpredis/phpredis) PHP extension을 설치할 수도 있습니다. 이 extension 은 좀 더 설치가 복잡하지만, Redis를 많이 사용하는 애플리케이션에서 보다 나은 성능을 보여줍니다.

<a name="configuration"></a>
### 설정하기

애플리케이션의 Redis 설정은 `config/database.php` 설정 파일에 담겨져 있습니다. 이 파일 내부의 `redis` 배열이 애플리케이션에서 사용할 레디스 서버의 설정 정보를 담고 있습니다:

    'redis' => [

        'client' => 'predis',

        'default' => [
            'host' => env('REDIS_HOST', 'localhost'),
            'password' => env('REDIS_PASSWORD', null),
            'port' => env('REDIS_PORT', 6379),
            'database' => 0,
        ],

    ],

기본적으로 설정된 서버는 개발시에는 충분할 수 있습니다. 하지만 환경에 맞게 이 배열을 변경할 수도 있습니다. 설정 파일 안에서 정의된 각각의 레디스 서버는 이름과 호스트 그리고 포트를 필요로 합니다.

#### 클러스터 설정하기

애플리케이션이 Redis 서버 클러스터를 사용한다면, Redis 설정의 `clusters` 키 안에 클러스터를 정의해야 합니다:

    'redis' => [

        'client' => 'predis',

        'clusters' => [
            'default' => [
                [
                    'host' => env('REDIS_HOST', 'localhost'),
                    'password' => env('REDIS_PASSWORD', null),
                    'port' => env('REDIS_PORT', 6379),
                    'database' => 0,
                ],
            ],
        ],

    ],

기본적으로, 클러스터는 클라이언트-사이드 샤딩을 수행하므로, 노드를 풀링하고 RAM을 가능한 많이 사용할 수 있도록 만듭니다. 하지만 클라이언트-사이드 샤딩은 페일오버-failover를 처리하지 않으므로, 다른 기본 데이터 저장소로 부터 데이터를 캐시하는데 적합합니다. 네이티브 Redis 클러스터링을 사용하려면, Redis 설정의 `options` 키에 다음의 설정을 지정해야합니다:

    'redis' => [

        'client' => 'predis',

        'options' => [
            'cluster' => 'redis',
        ],

        'clusters' => [
            // ...
        ],

    ],

`cluster` 옵션은 라라벨 레디스 클라이언트에게 레디스 노드에 클라이언트 측 샤딩을 수행 할 수 있도록 지시하여 노드를 풀링하고 사용가능한 RAM 을 가능한 많이 생성할 수 있도록 합니다. 하지만 클라이언트 샤딩은 페일오버를 처리하지는 않기 때문에 다른 기본 저장소를 위해 사용할 캐시 데이터를 취급하는데 주로 적합합니다.

<a name="predis"></a>
### Predis

`host`, `port`, `database`, 그리고 `password` 서버 설정에 더하여, Predis 는 각각의 Redis 서버에 대해 정의할 수 있는 추가적인 [커넥션 파라미터](https://github.com/nrk/predis/wiki/Connection-Parameters)를 지원합니다. 이 추가적인 설정 옵션을 구성하려면, `config/database.php` 설정 파일에 해당 설정 옵션들을 Redis 서버 설정 부분에 추가하기만 하면 됩니다:

    'default' => [
        'host' => env('REDIS_HOST', 'localhost'),
        'password' => env('REDIS_PASSWORD', null),
        'port' => env('REDIS_PORT', 6379),
        'database' => 0,
        'read_write_timeout' => 60,
    ],

<a name="phpredis"></a>
### PhpRedis

> {note} 만약 PhpRedis PHP extension 이 PECL을 통해서 설치되었다면, `config/app.php` 파일 안에서 `Redis` 별칭을 다른 이름으로 변경해야합니다.

PhpRedis extension을 구성하려면, `phpredis` Redis 설정의 `client` 옵션을 변경해야합니다. 이 옵션은 `config/database.php` 설정 파일에서 찾을 수 있습니다:

    'redis' => [

        'client' => 'phpredis',

        // Rest of Redis configuration...
    ],

`host`, `port`, `database`, 그리고 `password` 서버 설정에 더하여, PhpRedis 는 다음의 추가적인 커넥션 파라미터를 지원합니다: `persistent`, `prefix`, `read_timeout` 그리고 `timeout`. `config/database.php` 설정 파일의 Redis 서버 설정에 이 옵션들을 추가할 수 있습니다:

    'default' => [
        'host' => env('REDIS_HOST', 'localhost'),
        'password' => env('REDIS_PASSWORD', null),
        'port' => env('REDIS_PORT', 6379),
        'database' => 0,
        'read_timeout' => 60,
    ],

<a name="interacting-with-redis"></a>
## Redis와 상호작용하기

`Redis` [파사드](/docs/{{version}}/facades)를 통해서 다양한 메소드를 호출하여 Redis와 상호작용할 수 있습니다. `Redis` 파사드는 동적인 메소드를 지원하며, 이는 파사드에 [Redis 명령어](https://redis.io/commands)를 호출하면 Redis 에 직접 명령어가 전달된다는 것을 의미합니다. 예를 들어 `Redis` 파사드에 `get` 메소드를 호출하여 레디스 `GET` 명령어를 호출할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Support\Facades\Redis;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return Response
         */
        public function showProfile($id)
        {
            $user = Redis::get('user:profile:'.$id);

            return view('user.profile', ['user' => $user]);
        }
    }

물론, 앞서 말한바와 같이 `Redis` 파사드에서 어떤 Redis 명령어라도 호출 할 수 있습니다. 라라벨은 매직 매소드를 사용하여 명령어를 Redis 서버에 전달하기 때문에, Redis 명령어가 요구하는 인자도 전달 할 수 있습니다:

    Redis::set('name', 'Taylor');

    $values = Redis::lrange('names', 5, 10);

이렇게 하는 대신에, `command` 메소드의 첫번째 인자를 명령어의 이름으로 하고, 두번째 인자를 전달하는 값의 배열로 하여 명령어를 서버에 전달 할 수도 있습니다:

    $values = Redis::command('lrange', ['name', 5, 10]);

#### 여러개의 Redis 커넥션 사용하기

`Redis::connection` 메소드를 호출하여 레디스 인스턴스를 가져올 수 있습니다:

    $redis = Redis::connection();

이 구문은 기본으로 설정된 레디스 서버의 인스턴스를 반환 합니다. 만약 클러스터링을 사용하지 않고 있으면서, 레디스 설정에서 정의되어 있는 특정 서버 인스턴스 혹은 클러스터를 가져오려면 미리 지정된 서버 또는 클러스터의 이름을 `connection` 메소드에 넘겨주면 됩니다:

    $redis = Redis::connection('my-connection');

<a name="pipelining-commands"></a>
### 파이프라이닝 명령어

파이프라이닝은 다수의 명령어들을 한번에 서버로 보내야 할 때 사용되어집니다. `pipeline` 메소드는 Redis 인스턴스를 전달받는 하나의 `Closure`를 하나의 인자로 전달 받습니다. 이 Redis 인스턴스에 모든 명령을 내리 수 있으며 모든 명령은 단일 작업 내에서 실행됩니다:

    Redis::pipeline(function ($pipe) {
        for ($i = 0; $i < 1000; $i++) {
            $pipe->set("key:$i", $i);
        }
    });

<a name="pubsub"></a>
## Pub / Sub

라라벨은 Redis 의 `publish` 와 `subscribe` 명령에 대한 편리한 인터페이스를 제공합니니다. 이러한 Redis 명령들은 주어진 "채널"에서 메시지를 수신 할 수 있도록 해줍니다. 다른 애플리케이션에서 채널에 메세지를 전달하거나, 설령 다른 프로그래밍 언어를 사용하더라도 애플리케이션과 프로세스간의 통신을 쉽게 할 수 있습니다.

먼저 `subscribe` 메소드를 사용하여 채널에 리스너를 설정해 보겠습니다. `subscribe` 메소드는 긴 시간동안 실행되는 프로세스로 동작하기 때문에 이 메소드는 [아티즌 명령어](/docs/{{version}}/artisan)에서 호출하겠습니다:

    <?php

    namespace App\Console\Commands;

    use Illuminate\Console\Command;
    use Illuminate\Support\Facades\Redis;

    class RedisSubscribe extends Command
    {
        /**
         * The name and signature of the console command.
         *
         * @var string
         */
        protected $signature = 'redis:subscribe';

        /**
         * The console command description.
         *
         * @var string
         */
        protected $description = 'Subscribe to a Redis channel';

        /**
         * Execute the console command.
         *
         * @return mixed
         */
        public function handle()
        {
            Redis::subscribe(['test-channel'], function ($message) {
                echo $message;
            });
        }
    }

이제 `publish` 메소드를 사용하여 채널에 메세지를 표시할 수 있습니다:

    Route::get('publish', function () {
        // Route logic...

        Redis::publish('test-channel', json_encode(['foo' => 'bar']));
    });

#### 와일드 카드 Subscriptions

`psubscribe` 메소드를 사용하여, 전체 채널에서 모든 메세지를 수신하는데 유용한 와일드 카드 채널을 subscribe 할 수 있습니다. `$channel`의 이름은 콜백 `Closure` 의 두번째 인자로 전달 될 것입니다:

    Redis::psubscribe(['*'], function ($message, $channel) {
        echo $message;
    });

    Redis::psubscribe(['users.*'], function ($message, $channel) {
        echo $message;
    });
