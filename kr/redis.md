# Redis
# 레디스

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
    - [Clusters](#clusters)
    - [설정하기](#configuration)
    - [Predis](#predis)
    - [Predis](#predis)
    - [phpredis](#phpredis)
    - [phpredis](#phpredis)
- [Interacting With Redis](#interacting-with-redis)
- [Redis 와 상호작용](#interacting-with-redis)
    - [Transactions](#transactions)
    - [트랜잭션](#transactions)
    - [Pipelining Commands](#pipelining-commands)
    - [파이프라이닝 명령어](#pipelining-commands)
- [Pub / Sub](#pubsub)
- [Pub / Sub](#pubsub)

<a name="introduction"></a>
## Introduction
## 시작하기

[Redis](https://redis.io) is an open source, advanced key-value store. It is often referred to as a data structure server since keys can contain [strings](https://redis.io/topics/data-types#strings), [hashes](https://redis.io/topics/data-types#hashes), [lists](https://redis.io/topics/data-types#lists), [sets](https://redis.io/topics/data-types#sets), and [sorted sets](https://redis.io/topics/data-types#sorted-sets).

[레디스](https://redis.io) 는 오픈소스 기반의 키-밸류 기반의 저장소 입니다. 레디스는 키에 [문자열](https://redis.io/topics/data-types#strings), [해쉬](https://redis.io/topics/data-types#hashes), [리스트](https://redis.io/topics/data-types#lists), [세트](https://redis.io/topics/data-types#sets), 그리고 [정렬 세트](https://redis.io/topics/data-types#sorted-sets)를 사용할 수 있기 때문에 데이터 구조 서버라고도 불립니다.

Before using Redis with Laravel, we encourage you to install and use the [phpredis](https://github.com/phpredis/phpredis) PHP extension via PECL. The extension is more complex to install compared to "user-land" PHP packages but may yield better performance for applications that make heavy use of Redis. If you are using [Laravel Sail](/docs/{{version}}/sail), this extension is already installed in your application's Docker container.

라라벨에서 Redis를 사용하기 전에, PECL를 통하여 [PhpRedis](https://github.com/phpredis/phpredis) PHP Extension를 설치하고 사용하는 것을 권장합니다. 이 PHP 확장(PHP Extension)은 설치가 좀 더 복잡하지만 레디스를 많이 사용하는 어플리케이션에 보다 나은 성능을 제공합니다. [라라벨 세일](/docs/{{version}}/sail)을 이용하여 환경을 설정하였다면 도커 컨테이너 내에 이미 PECL 방식의 PHP Redis Extension이 설치되어 있습니다.

If you are unable to install the phpredis extension, you may install the `predis/predis` package via Composer. Predis is a Redis client written entirely in PHP and does not require any additional extensions:

phpredis 확장을 설치할 수 없는 경우 Composer를 통해 `predis/predis` 패키지를 설치할 수 있습니다. predis는 PHP 로만 작성된 레디스 클라이언트이므로 추가적으로 다른 확장 라이브러리나 패키지를 요구하지 않습니다.

```shell
composer require predis/predis
```

<a name="configuration"></a>
## Configuration
## 설정하기

You may configure your application's Redis settings via the `config/database.php` configuration file. Within this file, you will see a `redis` array containing the Redis servers utilized by your application:

'config/database.php' 파일을 통해 애플리케이션의 레디스 설정을 설정할 수 있습니다. 이 파일 내부의 `redis` 배열은 애플리케이션에서 사용할 레디스 서버의 설정 정보를 담고 있습니다.

    'redis' => [

        'client' => env('REDIS_CLIENT', 'phpredis'),

        'default' => [
            'host' => env('REDIS_HOST', '127.0.0.1'),
            'password' => env('REDIS_PASSWORD'),
            'port' => env('REDIS_PORT', 6379),
            'database' => env('REDIS_DB', 0),
        ],

        'cache' => [
            'host' => env('REDIS_HOST', '127.0.0.1'),
            'password' => env('REDIS_PASSWORD'),
            'port' => env('REDIS_PORT', 6379),
            'database' => env('REDIS_CACHE_DB', 1),
        ],

    ],

Each Redis server defined in your configuration file is required to have a name, host, and a port unless you define a single URL to represent the Redis connection:

설정 파일에 정의된 레디스 서버는 각각 name, host 및 port를 가져야 합니다. 만약 하나의 url로 레디스 연결을 설정하려 할 때는 아래와 같이 설정합니다.

    'redis' => [

        'client' => env('REDIS_CLIENT', 'phpredis'),

        'default' => [
            'url' => 'tcp://127.0.0.1:6379?database=0',
        ],

        'cache' => [
            'url' => 'tls://user:password@127.0.0.1:6380?database=1',
        ],

    ],

<a name="configuring-the-connection-scheme"></a>
#### Configuring The Connection Scheme
#### 연결 스키마 설정

By default, Redis clients will use the `tcp` scheme when connecting to your Redis servers; however, you may use TLS / SSL encryption by specifying a `scheme` configuration option in your Redis server's configuration array:

기본적으로 Redis 클라이언트는 Reids 서버에 연결할 때 `tcp` 스키마를 사용합니다. 그러나 Redis 서버 설정 배열에서 `scheme` 설정 옵션을 지정하여 TLS / SSL 암호화를 사용할 수 있습니다.

    'redis' => [

        'client' => env('REDIS_CLIENT', 'phpredis'),

        'default' => [
            'scheme' => 'tls',
            'host' => env('REDIS_HOST', '127.0.0.1'),
            'password' => env('REDIS_PASSWORD'),
            'port' => env('REDIS_PORT', 6379),
            'database' => env('REDIS_DB', 0),
        ],

    ],


<a name="clusters"></a>
### Clusters
### 클러스터

If your application is utilizing a cluster of Redis servers, you should define these clusters within a `clusters` key of your Redis configuration. This configuration key does not exist by default so you will need to create it within your application's `config/database.php` configuration file:

애플리케이션이 레디스 서버 클러스터를 사용한다면, 레디스 설정의 `clusters` 키 안에 설정값을 정의해야 합니다. 이 설정 키는 디폴트로 제공되지 않으므로 설정 파일 내에 존재하지 않습니다. 애플리케이션의 `config/database.php` 설정 파일 내에 다음과 같이 따로 값을 넣어 주도록 합니다.

    'redis' => [

        'client' => env('REDIS_CLIENT', 'phpredis'),

        'clusters' => [
            'default' => [
                [
                    'host' => env('REDIS_HOST', 'localhost'),
                    'password' => env('REDIS_PASSWORD'),
                    'port' => env('REDIS_PORT', 6379),
                    'database' => 0,
                ],
            ],
        ],

    ],

By default, clusters will perform client-side sharding across your nodes, allowing you to pool nodes and create a large amount of available RAM. However, client-side sharding does not handle failover; therefore, it is primarily suited for transient cached data that is available from another primary data store.

기본적으로 클러스트는 노드 전체에서 클라이언트 사이드 샤딩을 수행합니다. 클라이언트 사이드 샤딩은 레디스 저장소 자체의 동작이 아닌 애플리케이션에서 레디스 서버를 조작할 때 사용되는 프로그램을 쪽의 동작을 의미하며 레디스 저장소의 여러 노드를 논리적으로 하나의 대상으로 보도록 하는 pooling-풀링 작업을 수행합니다. 클러스터링을 통해 여러 레디스 노드를 사용할 수 있기 때문에 가능한 많은 RAM(Random Access Memory)을 사용할 수 있습니다. 하지만 클라이언트 사이드 샤딩은 레디스의 노드가 다운되었을 때 노드를 재연결하여 복구하는 failover를 처리하지 않기 때문에 데이터 손실이 일어날 수 있습니다. 따라서 다른 primary-기본 데이터 저장소에 저장되어 있는 값의 캐시 데이터를 일시적으로 저장하는데 적합합니다.

If you would like to use native Redis clustering instead of client-side sharding, you may specify this by setting the `options.cluster` configuration value to `redis` within your application's `config/database.php` configuration file:

클라이언트 사이드 샤딩 대신 레디스 서버 자체에서 분산 저장하는 방식의 설정을 하는 네이티브 레디스 클러스터링을 사용하려면, 애플리케이션의 `config/database.php` 설정 파일 내에서 `options.cluster` 설정 값을 `redis`로 설정하여 네이티브 클러스터링을 지정할 수 있습니다.

    'redis' => [

        'client' => env('REDIS_CLIENT', 'phpredis'),

        'options' => [
            'cluster' => env('REDIS_CLUSTER', 'redis'),
        ],

        'clusters' => [
            // ...
        ],

    ],

<a name="predis"></a>
### Predis
### Predis

If you would like your application to interact with Redis via the Predis package, you should ensure the `REDIS_CLIENT` environment variable's value is `predis`:

애플리케이션이 `Predis` 패키지를 통해 레디스 서버와 상호 작용하도록 하려면, `REDIS_CLIENT` 환경 변수의 값이 `predis`인지 확인해야 합니다:

    'redis' => [

        'client' => env('REDIS_CLIENT', 'predis'),

        // ...
    ],

In addition to the default `host`, `port`, `database`, and `password` server configuration options, Predis supports additional [connection parameters](https://github.com/nrk/predis/wiki/Connection-Parameters) that may be defined for each of your Redis servers. To utilize these additional configuration options, add them to your Redis server configuration in your application's `config/database.php` configuration file:

Predis는 `host`, `port`, `database`, `password` 기본 서버 설정 옵션 외에도 각 Redis 서버에 대해 정의할 수 있는 추가 [커넥션 파라미터](https://github.com/nrk/predis/wiki/Connection-Parameters)를 지원합니다. 이 추가적인 설정 옵션을 활용하려면, `config/database.php` 설정 파일의 레디스 서버 설정에 해당 옵션을 추가하세요.

    'default' => [
        'host' => env('REDIS_HOST', 'localhost'),
        'password' => env('REDIS_PASSWORD'),
        'port' => env('REDIS_PORT', 6379),
        'database' => 0,
        'read_write_timeout' => 60,
    ],

<a name="the-redis-facade-alias"></a>
#### The Redis Facade Alias
#### Redis Facade 별칭

Laravel's `config/app.php` configuration file contains an `aliases` array which defines all of the class aliases that will be registered by the framework. By default, no `Redis` alias is included because it would conflict with the `Redis` class name provided by the phpredis extension. If you are using the Predis client and would like to add a `Redis` alias, you may add it to the `aliases` array in your application's `config/app.php` configuration file:

라라벨의 `config/app.php` 설정 파일에는 프레임워크에 등록되는 모든 클래스 별칭을 정의하는 `aliases` 배열이 포함되어 있습니다. 편의를 위해 라라벨에서 제공하는 [facade](/docs/{version}/facades)마다 별칭 항목이 포함되어 있지만,  기본적으로 `Redis` 별칭은 포함되지 않는데, 이는 phpredis 확장자가 제공하는 `Redis` 클래스 이름과 충돌할 수 있기 때문입니다. Predis 클라이언트를 사용하는 경우 이 별칭을 활성화하려면 응용 프로그램의 `config/app.php` 설정 파일의 `aliases` 배열에 별칭을 추가해주면 됩니다.

    'aliases' => Facade::defaultAliases()->merge([
        'Redis' => Illuminate\Support\Facades\Redis::class,
    ])->toArray(),

<a name="phpredis"></a>
### phpredis
### phpredis

By default, Laravel will use the phpredis extension to communicate with Redis. The client that Laravel will use to communicate with Redis is dictated by the value of the `redis.client` configuration option, which typically reflects the value of the `REDIS_CLIENT` environment variable:

기본적으로 라라벨은 phpredis 확장을 사용하여 레디스와 통신합니다. 라라벨이 레디스와 통신하는 데 사용할 클라이언트는 `redis.client` 옵션의 값에 따라 결정되며, 일반적으로 `REDIS_CLIENT` 환경 변수의 값을 반영합니다:

    'redis' => [

        'client' => env('REDIS_CLIENT', 'phpredis'),

        // Rest of Redis configuration...
    ],

In addition to the default `scheme`, `host`, `port`, `database`, and `password` server configuration options, phpredis supports the following additional connection parameters: `name`, `persistent`, `persistent_id`, `prefix`, `read_timeout`, `retry_interval`, `timeout`, and `context`. You may add any of these options to your Redis server configuration in the `config/database.php` configuration file:

기본 `scheme`, `host`, `port`, `database`, `password` 설정 옵션에 더하여, PhpRedis 는 다음의 추가적인 커넥션 파라미터를 지원합니다. `name`, `persistent`, `prefix`, `read_timeout`, `timeout`, `context`. `config/database.php` 파일의 레디스 서버 설정에 이 옵션들을 추가할 수 있습니다.

    'default' => [
        'host' => env('REDIS_HOST', 'localhost'),
        'password' => env('REDIS_PASSWORD'),
        'port' => env('REDIS_PORT', 6379),
        'database' => 0,
        'read_timeout' => 60,
        'context' => [
            // 'auth' => ['username', 'secret'],
            // 'stream' => ['verify_peer' => false],
        ],
    ],

<a name="phpredis-serialization"></a>
#### phpredis Serialization & Compression
#### phpredis 직렬화 & 압축

The phpredis extension may also be configured to use a variety of serialization and compression algorithms. These algorithms can be configured via the `options` array of your Redis configuration:

phpredis extension 은 다양한 직렬화-serialization 및 압축 알고리즘을 사용하도록 설정할 수 있습니다. 이러한 알고리즘은 레디스 설정의 `options` 배열을 통해 설정할 수 있습니다:

    'redis' => [

        'client' => env('REDIS_CLIENT', 'phpredis'),

        'options' => [
            'serializer' => Redis::SERIALIZER_MSGPACK,
            'compression' => Redis::COMPRESSION_LZ4,
        ],

        // Rest of Redis configuration...
    ],

Currently supported serialization algorithms include: `Redis::SERIALIZER_NONE` (default), `Redis::SERIALIZER_PHP`, `Redis::SERIALIZER_JSON`, `Redis::SERIALIZER_IGBINARY`, and `Redis::SERIALIZER_MSGPACK`.

현재 지원되는 직렬화 알고리즘은 다음과 같습니다 : `Redis::SERIALIZER_NONE`(기본값), `Redis::SERIALIZER_PHP`, `Redis::SERIALIZER_JSON`, `Redis::SERIALIZER_IGBINARY`, `Redis::SERIALIZER_MSGPACK`

Supported compression algorithms include: `Redis::COMPRESSION_NONE` (default), `Redis::COMPRESSION_LZF`, `Redis::COMPRESSION_ZSTD`, and `Redis::COMPRESSION_LZ4`.

지원되는 압축 알고리즘은 다음과 같습니다 : `Redis::COMPRESSION_NONE`(기본값), `Redis::COMPRESSION_LZF`, `Redis::COMPRESSION_ZSTD`, `Redis::COMPRESSION_LZ4`

<a name="interacting-with-redis"></a>
## Interacting With Redis
## Redis와 상호작용하기

You may interact with Redis by calling various methods on the `Redis` [facade](/docs/{{version}}/facades). The `Redis` facade supports dynamic methods, meaning you may call any [Redis command](https://redis.io/commands) on the facade and the command will be passed directly to Redis. In this example, we will call the Redis `GET` command by calling the `get` method on the `Redis` facade:

`Redis` [파사드](/docs/{{version}}/facades)를 통해서 다양한 메소드를 호출하여 Redis와 상호작용할 수 있습니다. `Redis` 파사드는 동적인 메소드를 지원하며, 이는 파사드에 [Redis 명령어](https://redis.io/commands)를 호출하면 레디스에 직접 명령어가 전달된다는 것을 의미합니다. 예를 들어 `Redis` 파사드에 `get` 메소드를 호출하여 레디스 `GET` 명령어를 호출할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Support\Facades\Redis;
    use Illuminate\View\View;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         */
        public function show(string $id): View
        {
            return view('user.profile', [
                'user' => Redis::get('user:profile:'.$id)
            ]);
        }
    }

As mentioned above, you may call any of Redis' commands on the `Redis` facade. Laravel uses magic methods to pass the commands to the Redis server. If a Redis command expects arguments, you should pass those to the facade's corresponding method:

앞서 말한바와 같이 `Redis` 파사드에서 어떤 레디스 명령어라도 호출 할 수 있습니다. 라라벨은 매직 메소드를 사용하여 명령어를 레디스 서버에 전달합니다. 레디스 명령어가 요구하는 인자를 각 명령어의 방법으로 전달해야 합니다.

    use Illuminate\Support\Facades\Redis;

    Redis::set('name', 'Taylor');

    $values = Redis::lrange('names', 5, 10);

Alternatively, you may pass commands to the server using the `Redis` facade's `command` method, which accepts the name of the command as its first argument and an array of values as its second argument:

다른 방법으로는, `Redis` 파사드의 `command` 메소드의 첫번째 인자를 명령어의 이름으로 하고, 두번째 인자를 전달하는 값의 배열로 하여 명령어를 서버에 전달 할 수도 있습니다.

    $values = Redis::command('lrange', ['name', 5, 10]);

<a name="using-multiple-redis-connections"></a>
#### Using Multiple Redis Connections
#### 여러개의 Redis 커넥션 사용하기

Your application's `config/database.php` configuration file allows you to define multiple Redis connections / servers. You may obtain a connection to a specific Redis connection using the `Redis` facade's `connection` method:

`config/database.php` 설정 파일을 사용하면 여러개의 Redis 연결(서버)를 설정 할 수 있습니다. `Redis` 파사드의 `connection` 메소드로 설정한 특정 레디스 인스턴스를 가져올 수 있습니다.

    $redis = Redis::connection('connection-name');

To obtain an instance of the default Redis connection, you may call the `connection` method without any additional arguments:

기본 레디스 연결 인스턴스를 가져오려면 `connection` 메소드에 추가 인자 없이 전달하면 됩니다.

    $redis = Redis::connection();

<a name="transactions"></a>
### Transactions
### 트랜잭션

The `Redis` facade's `transaction` method provides a convenient wrapper around Redis' native `MULTI` and `EXEC` commands. The `transaction` method accepts a closure as its only argument. This closure will receive a Redis connection instance and may issue any commands it would like to this instance. All of the Redis commands issued within the closure will be executed in a single, atomic transaction:

Redis` 파사드의 `transaction` 메서드는 Redis의 기본 `MULTI` 및 `EXEC` 명령에 대한 편리한 래퍼를 제공합니다. `transaction` 메서드는 하나의 인자를 받는데 클로저를 인자로 받습니다. 이 클로저는 레디스 연결 인스턴스를 수신하고 이 인스턴스에 원하는 모든 명령을 전달할 수 있습니다. 클로저 내의 모든 레디스 명령은 한 단위의 원자적 트렌젝션으로 실행됩니다.

    use Redis;
    use Illuminate\Support\Facades;

    Facades\Redis::transaction(function (Redis $redis) {
        $redis->incr('user_visits', 1);
        $redis->incr('total_visits', 1);
    });

> **Warning**  
> When defining a Redis transaction, you may not retrieve any values from the Redis connection. Remember, your transaction is executed as a single, atomic operation and that operation is not executed until your entire closure has finished executing its commands.

> **Warning**  
> 레디스 트랜젝션이 정의 되는 동안 Redis 에서 해당 값을 찾을수 없습니다. 모든 트랜잭션은 원자성이 보장되며 모든 클로저가 실행 될때까지 모든 명령은 실행되지 않음을 기억하세요.

#### Lua Scripts
#### Lua Scripts

The `eval` method provides another method of executing multiple Redis commands in a single, atomic operation. However, the `eval` method has the benefit of being able to interact with and inspect Redis key values during that operation. Redis scripts are written in the [Lua programming language](https://www.lua.org).

`eval` 메소드는 여러 Redis 명령을 하나의 원자 연산으로 실행할 수 있는 또 다른 방법입니다. `eval` 메소드는 Redis 키 값과 상호 작용하고 검사할 수 있다는 장점이 있습니다. 레디스 스크립트는 [Lua programming language](https://www.lua.org)로 작성됩니다.

The `eval` method can be a bit scary at first, but we'll explore a basic example to break the ice. The `eval` method expects several arguments. First, you should pass the Lua script (as a string) to the method. Secondly, you should pass the number of keys (as an integer) that the script interacts with. Thirdly, you should pass the names of those keys. Finally, you may pass any other additional arguments that you need to access within your script.

처음에는 `eval` 메소드가 무서울 수 있으나, 해당 메소드가 조금 익숙해지기 위해 기본적인 예를 살펴보도록 하겠습니다. `eval` 메서드에는 여러 개의 인수가 필요합니다. 먼저 Lua 스크립트(문자열 처럼)를 메소드에 전달해야 합니다. 둘째, 스크립트가 상호 작용하는 키(정수형 인자 처럼)를 전달해야 합니다. 세 번째, 키의 이름을 넘겨야 합니다. 마지막으로 스크립트 내에서 액세스해야 하는 다른 추가 인수를 전달할 수 있습니다.

In this example, we will increment a counter, inspect its new value, and increment a second counter if the first counter's value is greater than five. Finally, we will return the value of the first counter:

아래의 예제에서는 카운터를 증가시키고, 첫 번째 카운터의 값이 5보다 크면 두 번째 카운터를 증가시킵니다. 마지막으로 첫 번째 카운터의 값을 반환합니다.

    $value = Redis::eval(<<<'LUA'
        local counter = redis.call("incr", KEYS[1])

        if counter > 5 then
            redis.call("incr", KEYS[2])
        end

        return counter
    LUA, 2, 'first-counter', 'second-counter');

> **Warning**  
> Please consult the [Redis documentation](https://redis.io/commands/eval) for more information on Redis scripting.

> **Warning**  
> 레디스 스크립팅에 자세한 내용은 [Redis documentation](https://redis.io/commands/eval) 에서 확인하세요.

<a name="pipelining-commands"></a>
### Pipelining Commands
### 파이프라이닝 명령어

Sometimes you may need to execute dozens of Redis commands. Instead of making a network trip to your Redis server for each command, you may use the `pipeline` method. The `pipeline` method accepts one argument: a closure that receives a Redis instance. You may issue all of your commands to this Redis instance and they will all be sent to the Redis server at the same time to reduce network trips to the server. The commands will still be executed in the order they were issued:

때로는 수십 개의 Redis 명령을 실행해야 할 수도 있습니다. 각 명령에 대해 레디스 한 단위의 서버간 네트워크 전송(a network trip)을 만드는 것 대신 `pipeline` 메서드를 사용할 수 있습니다. `pipeline` 레디스 인스턴스를 수신하는 클로저를 하나를 인자로 받습니다. 이 레디스 인스턴스에 모든 명령을 실행하면 모든 명령이 한꺼번에 레디스 서버로 전송되기 때문에 서버간의 네트워크 전송으로 지연되는 시간을 줄일 수 있습니다. 이 명령은 작성한 순서대로 실행됩니다.

    use Redis;
    use Illuminate\Support\Facades;

    Facades\Redis::pipeline(function (Redis $pipe) {
        for ($i = 0; $i < 1000; $i++) {
            $pipe->set("key:$i", $i);
        }
    });

<a name="pubsub"></a>
## Pub / Sub
## Pub / Sub

Laravel provides a convenient interface to the Redis `publish` and `subscribe` commands. These Redis commands allow you to listen for messages on a given "channel". You may publish messages to the channel from another application, or even using another programming language, allowing easy communication between applications and processes.

라라벨은 레디스의 `publish` 와 `subscribe` 명령에 대한 편리한 인터페이스를 제공합니니다. 이러한 Redis 명령들은 주어진 "채널"에서 메시지를 수신 할 수 있도록 해줍니다. 다른 애플리케이션 또는 다른 프로그래밍 언어에서 채널에 메시지를 전달할 수 있으므로 애플리케이션과 프로세스간의 통신을 쉽게 할 수 있습니다.

First, let's setup a channel listener using the `subscribe` method. We'll place this method call within an [Artisan command](/docs/{{version}}/artisan) since calling the `subscribe` method begins a long-running process:

먼저 `subscribe` 메소드를 사용하여 채널에 리스너를 설정해 보겠습니다. `subscribe` 메소드는 장기간 실행되는 프로세스로 동작하기 때문에 이 메소드의 호출은 [아티즌 명령어](/docs/{{version}}/artisan)내에 배치하겠습니다:

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
         */
        public function handle(): void
        {
            Redis::subscribe(['test-channel'], function (string $message) {
                echo $message;
            });
        }
    }

Now we may publish messages to the channel using the `publish` method:

이제 `publish` 메소드를 사용하여 채널에 메세지를 전달할 수 있습니다.

    use Illuminate\Support\Facades\Redis;

    Route::get('/publish', function () {
        // ...

        Redis::publish('test-channel', json_encode([
            'name' => 'Adam Wathan'
        ]));
    });

<a name="wildcard-subscriptions"></a>
#### Wildcard Subscriptions
#### 와일드 카드 Subscriptions

Using the `psubscribe` method, you may subscribe to a wildcard channel, which may be useful for catching all messages on all channels. The channel name will be passed as the second argument to the provided closure:

`psubscribe` 메소드를 사용하여, 전체 채널에서 모든 메세지를 수신하는데 유용한 와일드 카드 채널을 subscribe 할 수 있습니다. 채널이름은 제공된 클로저의 두 번째 인자로 전달됩니다.

    Redis::psubscribe(['*'], function (string $message, string $channel) {
        echo $message;
    });

    Redis::psubscribe(['users.*'], function (string $message, string $channel) {
        echo $message;
    });
