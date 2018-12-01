# 레디스

- [시작하기](#introduction)
- [기본적인 사용법](#basic-usage)
    - [명령어 파이프라이닝](#pipelining-commands)
- [Pub / Sub](#pubsub)

<a name="introduction"></a>
## 시작하기

[레디스](http://redis.io) 는 키-밸류 기반의 오픈소서 저장소 입니다. 레디스는 키에 [문자열](http://redis.io/topics/data-types#strings), [해쉬](http://redis.io/topics/data-types#hashes), [리스트](http://redis.io/topics/data-types#lists), [세트](http://redis.io/topics/data-types#sets), 그리고 [정렬 세트](http://redis.io/topics/data-types#sorted-sets)를 사용할 수 있기 때문에 데이터 구조 서버로 자주 거론되고 있습니다. 라라벨에서 레디스를 사용하기 전에 여러분은 Composer 를 통해서 `predis/predis` 패키지 (~1.0)를 설치할 필요가 있습니다.

<a name="configuration"></a>
### 설정하기

애플리케이션에서 사용할 레디스의 설정은 `config/database.php` 설정 파일에서 위치해 있습니다. 파일 내부의 `redis` 배열이 애플리케이션에서 사용할 레디스 서버의 설정 정보를 담고 있습니다. 

    'redis' => [

        'cluster' => false,

        'default' => [
            'host'     => '127.0.0.1',
            'port'     => 6379,
            'database' => 0,
        ],

    ],

기본적으로 설정된 서버는 개발시에는 충분할 수 있습니다. 하지만 환경에 맞게 설정을 변경할 수도 있습니다. 간단하게 레디스 서버의 이름과 그에 맞는 호스트 및 포트번호를 설정하면 됩니다. 

`cluster` 옵션은 라라벨 레디스 클라이언트에게 레디스 노드에 클라이언트 측 샤딩을 수행 할 수 있도록 하여 노드를 풀링하고 사용가능한 RAM 을 가능한 많이 생성할 수 있도록 합니다. 하지만 클라이언트 샤딩은 페일오버를 처리하지는 않기 때문에 다른 기본 저장소를 위해 사용할 캐시 데이터를 취급하는데 주로 적합합니다.  

추가적으로, 레디스 커넥션 정의 부분안에서 [클라이언트 옵션](https://github.com/nrk/predis/wiki/Client-Options)을 지정할 수 있도록 `options` 배열값을 정의할 수 있습니다. 

만약 레디스 서버에 인증이 필요하다면 레디스 서버 설정 배열에 `password` 설정값을 추가하면 됩니다. 

> **주의:** 만약 레디스 PHP extension 이 PECL을 통해서 설치되었다면, `config/app.php` 파일 안에서 Redis 별칭을 변경해야할 것입니다. 

<a name="basic-usage"></a>
## 기본적인 사용법 

`Redis` [파사드](/docs/{{version}}/facades)를 통해서 다양한 메소드를 호출하여 Redis 를 제어할 수 있습니다. `Redis` 파사드는 동적인 메소드를 지원하며, 이는 파사드에 [Redis 명령어]를 호출하면 Redis 에 직접 명령어가 전달된다는 것을 의미합니다. 예를 들어 `Redis` 파사드에 `get` 메소드를 호출하여 레디스 서버에 `GET` 명령어를 호출할 수 있습니다. 

    <?php

    namespace App\Http\Controllers;

    use Redis;
    use App\Http\Controllers\Controller;

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

물론, 앞서 말한바와 같이 `Redis` 파사드에서 어떤 Redis 명령어라도 호출 할 수 있습니다. 라라벨은 매직 매소드를 사용하여 명령어를 Redis 서버에 전달하기 때문에, Redis 명령어가 요구하는 인자도 전달 할 수 있습니다. 

    Redis::set('name', 'Taylor');

    $values = Redis::lrange('names', 5, 10);

이렇게 하는 대신에, 첫번째 인자를 명령어의 이름으로 하고, 두번째 인자를 전달하는 값의 배열의 형태로 하여 `command` 메소드를 직접 호출하여 명령어를 서버에 전달 할 수도 있습니다. 

    $values = Redis::command('lrange', ['name', 5, 10]);

#### 여러개의 Redis 커넥션 사용하기

`Redis::connection` 메소드를 호출하여 레디스 인스턴스를 가져올 수 있습니다:

    $redis = Redis::connection();

이 구문은 기본 레디스 서버의 인스턴스를 반환 합니다. 만약 클러스터링을 사용하지 않고 있으면서, 레디스 설정에서 정의되어 있는 특정 서버 인스턴스를 가져오려면 미리 지정된 서버의 이름을 `connection` 메소드에 파라미터로 넘겨주면 됩니다:

    $redis = Redis::connection('other');

<a name="pipelining-commands"></a>
### 명령어 파이프라이닝

파이프라이닝은 다수의 명령어들을 한번에 서버로 보내야 할 때 사용되어집니다. `pipeline` 메소드는 Redis 인스턴스를 전해 받는 `Closure` 형태의 한개의 인자를 전달 받습니다. 여러분은 이슈 할 수 있습니다. 이 Redis 인스턴스에서 모든 명령들이 한번의 동작 안에서 실행되도록 할 수 있습니다. 

    Redis::pipeline(function ($pipe) {
        for ($i = 0; $i < 1000; $i++) {
            $pipe->set("key:$i", $i);
        }
    });

<a name="pubsub"></a>
## Pub / Sub

라라벨은 Redis 의 `publish` 와 `subscribe` 명령에 대한 편리한 인터페이스를 제공합니니다. 이러한 Redis 명령들은 주어진 "채널"에서  메시지를 수신 할 수 있도록 해줍니다. 다른 애플리케이션에서 또는 다른 프로그래밍 언어를 통해서 이 채널에 메시지를 게시할 수 있으며, 쉽게 통신 할 수 있습니다.

먼저 Reis 에서 `subscribe` 메소드를 사용하여 채널에 리스너를 설정해 보겠습니다. `subscribe` 메소드는 긴 시간동안 실행되는 프로세스로 동작하기 때문에 이 메소드는 [아티즌 명령어](/docs/{{version}}/artisan)에서 호출하겠습니다.

    <?php

    namespace App\Console\Commands;

    use Redis;
    use Illuminate\Console\Command;

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
            Redis::subscribe(['test-channel'], function($message) {
                echo $message;
            });
        }
    }

이제 `publish` 메소드를 사용하여 채널에 메세지를 표시할 수 있습니다. 

    Route::get('publish', function () {
        // Route logic...

        Redis::publish('test-channel', json_encode(['foo' => 'bar']));
    });

#### Wildcard Subscriptions

`psubscribe` 메소드를 사용하여, 모든 채널에서 메세지를 확인할 수 있는 와일드 카드 채널에 대해서 subscribe 할 수 있습니다. `$channel`의 이름은 콜백 `Closure` 의 두번째 인자로 전달 될 것입니다. 

    Redis::psubscribe(['*'], function($message, $channel) {
        echo $message;
    });

    Redis::psubscribe(['users.*'], function($message, $channel) {
        echo $message;
    });