# Redis
# 레디스

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Configuration](#configuration)
    - [설정하기](#configuration)
    - [Predis](#predis)
    - [Predis](#predis)
    - [PhpRedis](#phpredis)
    - [PhpRedis](#phpredis)
- [Interacting With Redis](#interacting-with-redis)
- [Redis 와 상호작용](#interacting-with-redis)
    - [Pipelining Commands](#pipelining-commands)
    - [파이프라이닝 명령어](#pipelining-commands)
- [Pub / Sub](#pubsub)
- [Pub / Sub](#pubsub)

<a name="introduction"></a>
## Introduction
## 시작하기

[Redis](https://redis.io) is an open source, advanced key-value store. It is often referred to as a data structure server since keys can contain [strings](https://redis.io/topics/data-types#strings), [hashes](https://redis.io/topics/data-types#hashes), [lists](https://redis.io/topics/data-types#lists), [sets](https://redis.io/topics/data-types#sets), and [sorted sets](https://redis.io/topics/data-types#sorted-sets).

[레디스](https://redis.io) 는 키-밸류 기반의 오픈소스 저장소 입니다. 레디스는 키에 [문자열](https://redis.io/topics/data-types#strings), [해쉬](https://redis.io/topics/data-types#hashes), [리스트](https://redis.io/topics/data-types#lists), [세트](https://redis.io/topics/data-types#sets), 그리고 [정렬 세트](https://redis.io/topics/data-types#sorted-sets)를 사용할 수 있기 때문에 데이터 구조 서버로 자주 거론되고 있습니다.

Before using Redis with Laravel, you will need to install the `predis/predis` package via Composer:

라라벨에서 레디스를 사용하기 전에 여러분은 Composer 를 통해서 `predis/predis` 패키지를 설치할 필요가 있습니다:

    composer require predis/predis

Alternatively, you may install the [PhpRedis](https://github.com/phpredis/phpredis) PHP extension via PECL. The extension is more complex to install but may yield better performance for applications that make heavy use of Redis.

이렇게 하는 대신에, PECL을 통해서 [PhpRedis](https://github.com/phpredis/phpredis) PHP extension을 설치할 수도 있습니다. 이 extension 은 좀 더 설치가 복잡하지만, Redis를 많이 사용하는 애플리케이션에서 보다 나은 성능을 보여줍니다.

<a name="configuration"></a>
### Configuration
### 설정하기

The Redis configuration for your application is located in the `config/database.php` configuration file. Within this file, you will see a `redis` array containing the Redis servers utilized by your application:

애플리케이션의 Redis 설정은 `config/database.php` 설정 파일에 담겨져 있습니다. 이 파일 내부의
`redis` 배열이 애플리케이션에서 사용할 레디스 서버의 설정 정보를 담고 있습니다:

    'redis' => [

        'client' => 'predis',

        'default' => [
            'host' => env('REDIS_HOST', '127.0.0.1'),
            'password' => env('REDIS_PASSWORD', null),
            'port' => env('REDIS_PORT', 6379),
            'database' => env('REDIS_DB', 0),
        ],

        'cache' => [
            'host' => env('REDIS_HOST', '127.0.0.1'),
            'password' => env('REDIS_PASSWORD', null),
            'port' => env('REDIS_PORT', 6379),
            'database' => env('REDIS_CACHE_DB', 1),
        ],

    ],

The default server configuration should suffice for development. However, you are free to modify this array based on your environment. Each Redis server defined in your configuration file is required to have a name, host, and port.

기본적으로 설정된 서버는 개발시에는 충분할 수 있습니다. 하지만 환경에 맞게 이 배열을 변경할 수도 있습니다. 설정 파일 안에서 정의된 각각의 레디스 서버는 이름과 호스트 그리고 포트를 필요로 합니다.

#### Configuring Clusters
#### 클러스터 설정하기

If your application is utilizing a cluster of Redis servers, you should define these clusters within a `clusters` key of your Redis configuration:

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

By default, clusters will perform client-side sharding across your nodes, allowing you to pool nodes and create a large amount of available RAM. However, note that client-side sharding does not handle failover; therefore, is primarily suited for cached data that is available from another primary data store. If you would like to use native Redis clustering, you should specify this in the `options` key of your Redis configuration:

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

The `cluster` option will instruct the Laravel Redis client to perform client-side sharding across your Redis nodes, allowing you to pool nodes and create a large amount of available RAM. However, note that client-side sharding does not handle failover; therefore, is primarily suited for cached data that is available from another primary data store.

`cluster` 옵션은 라라벨 레디스 클라이언트에게 레디스 노드에 클라이언트 측 샤딩을 수행 할 수 있도록 지시하여 노드를 풀링하고 사용가능한 RAM 을 가능한 많이 생성할 수 있도록 합니다. 하지만 클라이언트 샤딩은 페일오버를 처리하지는 않기 때문에 다른 기본 저장소를 위해 사용할 캐시 데이터를 취급하는데 주로 적합합니다.

<a name="predis"></a>
### Predis
### Predis

In addition to the default `host`, `port`, `database`, and `password` server configuration options, Predis supports additional [connection parameters](https://github.com/nrk/predis/wiki/Connection-Parameters) that may be defined for each of your Redis servers. To utilize these additional configuration options, add them to your Redis server configuration in the `config/database.php` configuration file:

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
### PhpRedis

To utilize the PhpRedis extension, you should change the `client` option of your Redis configuration to `phpredis`. This option is found in your `config/database.php` configuration file:

PhpRedis extension을 구성하려면, `phpredis` Redis 설정의 `client` 옵션을 변경해야합니다. 이 옵션은 `config/database.php` 설정 파일에서 찾을 수 있습니다:

    'redis' => [

        'client' => 'phpredis',

        // Rest of Redis configuration...
    ],

In addition to the default `host`, `port`, `database`, and `password` server configuration options, PhpRedis supports the following additional connection parameters: `persistent`, `prefix`, `read_timeout` and `timeout`. You may add any of these options to your Redis server configuration in the `config/database.php` configuration file:

`host`, `port`, `database`, 그리고 `password` 서버 설정에 더하여, PhpRedis 는 다음의 추가적인 커넥션 파라미터를 지원합니다: `persistent`, `prefix`, `read_timeout` 그리고 `timeout`. `config/database.php` 설정 파일의 Redis 서버 설정에 이 옵션들을 추가할 수 있습니다:

    'default' => [
        'host' => env('REDIS_HOST', 'localhost'),
        'password' => env('REDIS_PASSWORD', null),
        'port' => env('REDIS_PORT', 6379),
        'database' => 0,
        'read_timeout' => 60,
    ],

<a name="interacting-with-redis"></a>
## Interacting With Redis
## Redis와 상호작용하기

You may interact with Redis by calling various methods on the `Redis` [facade](/docs/{{version}}/facades). The `Redis` facade supports dynamic methods, meaning you may call any [Redis command](https://redis.io/commands) on the facade and the command will be passed directly to Redis. In this example, we will call the Redis `GET` command by calling the `get` method on the `Redis` facade:

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

As mentioned above, you may call any of the Redis commands on the `Redis` facade. Laravel uses magic methods to pass the commands to the Redis server, so pass the arguments the Redis command expects:

앞서 말한바와 같이 `Redis` 파사드에서 어떤 Redis 명령어라도 호출 할 수 있습니다. 라라벨은 매직 매소드를 사용하여 명령어를 Redis 서버에 전달하기 때문에, Redis 명령어가 요구하는 인자도 전달 할 수 있습니다:

    Redis::set('name', 'Taylor');

    $values = Redis::lrange('names', 5, 10);

Alternatively, you may also pass commands to the server using the `command` method, which accepts the name of the command as its first argument, and an array of values as its second argument:

이렇게 하는 대신에, `command` 메소드의 첫번째 인자를 명령어의 이름으로 하고, 두번째 인자를 전달하는 값의 배열로 하여 명령어를 서버에 전달 할 수도 있습니다:

    $values = Redis::command('lrange', ['name', 5, 10]);

#### Using Multiple Redis Connections
#### 여러개의 Redis 커넥션 사용하기

You may get a Redis instance by calling the `Redis::connection` method:

`Redis::connection` 메소드를 호출하여 레디스 인스턴스를 가져올 수 있습니다:

    $redis = Redis::connection();

This will give you an instance of the default Redis server. You may also pass the connection or cluster name to the `connection` method to get a specific server or cluster as defined in your Redis configuration:

이 구문은 기본으로 설정된 레디스 서버의 인스턴스를 반환 합니다. 만약 클러스터링을 사용하지 않고 있으면서, 레디스 설정에서 정의되어 있는 특정 서버 인스턴스 혹은 클러스터를 가져오려면 미리 지정된 서버 또는 클러스터의 이름을 `connection` 메소드에 넘겨주면 됩니다:

    $redis = Redis::connection('my-connection');

<a name="pipelining-commands"></a>
### Pipelining Commands
### 파이프라이닝 명령어

Pipelining should be used when you need to send many commands to the server in one operation. The `pipeline` method accepts one argument: a `Closure` that receives a Redis instance. You may issue all of your commands to this Redis instance and they will all be executed within a single operation:

파이프라이닝은 다수의 명령어들을 한번에 서버로 보내야 할 때 사용되어집니다. `pipeline` 메소드는 Redis 인스턴스를 전달받는 하나의 `Closure`를 하나의 인자로 전달 받습니다. 이 Redis 인스턴스에 모든 명령을 내리 수 있으며 모든 명령은 단일 작업 내에서 실행됩니다:

    Redis::pipeline(function ($pipe) {
        for ($i = 0; $i < 1000; $i++) {
            $pipe->set("key:$i", $i);
        }
    });

<a name="pubsub"></a>
## Pub / Sub
## Pub / Sub

Laravel provides a convenient interface to the Redis `publish` and `subscribe` commands. These Redis commands allow you to listen for messages on a given "channel". You may publish messages to the channel from another application, or even using another programming language, allowing easy communication between applications and processes.

라라벨은 Redis 의 `publish` 와 `subscribe` 명령에 대한 편리한 인터페이스를 제공합니니다. 이러한 Redis 명령들은 주어진 "채널"에서 메시지를 수신 할 수 있도록 해줍니다. 다른 애플리케이션에서 채널에 메세지를 전달하거나, 설령 다른 프로그래밍 언어를 사용하더라도 애플리케이션과 프로세스간의 통신을 쉽게 할 수 있습니다.

First, let's setup a channel listener using the `subscribe` method. We'll place this method call within an [Artisan command](/docs/{{version}}/artisan) since calling the `subscribe` method begins a long-running process:

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

Now we may publish messages to the channel using the `publish` method:

이제 `publish` 메소드를 사용하여 채널에 메세지를 표시할 수 있습니다:

    Route::get('publish', function () {
        // Route logic...

        Redis::publish('test-channel', json_encode(['foo' => 'bar']));
    });

#### Wildcard Subscriptions
#### 와일드 카드 Subscriptions

Using the `psubscribe` method, you may subscribe to a wildcard channel, which may be useful for catching all messages on all channels. The `$channel` name will be passed as the second argument to the provided callback `Closure`:

`psubscribe` 메소드를 사용하여, 전체 채널에서 모든 메세지를 수신하는데 유용한 와일드 카드 채널을 subscribe 할 수 있습니다. `$channel`의 이름은 콜백 `Closure` 의 두번째 인자로 전달 될 것입니다:

    Redis::psubscribe(['*'], function ($message, $channel) {
        echo $message;
    });

    Redis::psubscribe(['users.*'], function ($message, $channel) {
        echo $message;
    });
