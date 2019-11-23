# Queues-큐

- [시작하기](#introduction)
    - [커넥션 Vs. Queues-큐](#connections-vs-queues)
    - [드라이버 주의사항과 사전준비사항](#driver-prerequisites)
- [Job 생성하기](#creating-jobs)
    - [Job 클래스 생성하기](#generating-job-classes)
    - [클래스 구조](#class-structure)
    - [Job 미들웨어](#job-middleware)
- [Job 처리하기](#dispatching-jobs)
    - [지연시켜서 처리하기](#delayed-dispatching)
    - [동기식 반환](#synchronous-dispatching)
    - [Job 체이닝](#job-chaining)
    - [Queue-큐 & 커넥션 커스터마이징](#customizing-the-queue-and-connection)
    - [최대 재시도 횟수 / 타임아웃 시간 지정하기](#max-job-attempts-and-timeout)
    - [실행 속도 제한](#rate-limiting)
    - [에러 핸들링](#error-handling)
- [큐잉 클로저](#queueing-closures)
- [Queue Worker 구동하기](#running-the-queue-worker)
    - [Queue 우선순위 지정하기](#queue-priorities)
    - [Queue Workers & 배포](#queue-workers-and-deployment)
    - [Job 만료 & 타임아웃](#job-expirations-and-timeouts)
- [Supervisor 설정](#supervisor-configuration)
- [실패한 Job 처리하기](#dealing-with-failed-jobs)
    - [Job 실패 후 처리](#cleaning-up-after-failed-jobs)
    - [실패한 Job 이벤트](#failed-job-events)
    - [실패한 Job 재시도하기](#retrying-failed-jobs)
    - [누락 된 모델 무시하기](#ignoring-missing-models)
- [Job 이벤트](#job-events)

<a name="introduction"></a>
## 시작하기

> {tip} 라라벨은 이제 Redis 큐-queue를 위한 멋진 대시보드와 설정 시스템을 제공하는 Horizon을 지원합니다. 보다 자세한 사항은 [Horizon 문서](/docs/{{version}}/horizon)를 참고하십시오.

라라벨의 Queue-큐는 Beanstalk, Amazone SQS, Redis 그리고 관계형 데이터베이스과 같은 다양한 queue 시스템을 위한 통일된 API를 제공합니다. queue를 사용하면 이메일을 보내는 일과 같이 시간이 소요되는 Job을 나중에 처리할 수 있습니다. 이렇게 소모되는 작업들을 나중에 처리함으로써 여러분의 애플리케이션은 웹 요청-request을 더 빠르게 처리할 수 있습니다.

큐의 환경 설정 파일은 `config/queue.php`에 있습니다. 이 파일에서 프레임워크에 포함된 데이터베이스, [Beanstalkd](https://kr.github.com/beanstalkd), [Amazon SQS](https://aws.amazon.com/sqs/), [Redis](https://redis.io), 그리고 (로컬 사용을 위한) job을 즉시 실행하는 synchronous 드라이버 설정을 확인할 수 있습니다. `null`로 설정 시 큐 동작을 비활성화 합니다.

<a name="connections-vs-queues"></a>
### 커넥션 Vs. Queues-큐

라라벨의 Queue-큐를 알아보기 전에, "커넥션"과 "queue-큐"의 차이점을 이해하고 있는 것이 중요합니다. `config/queue.php` 설정 파일에는 `connections` 설정 옵션이 존재합니다. 이 옵션은 Amazon SQS, Beanstalk 또는 Redis 와 같은 서비스에 대한 개별적인 커넥션을 정의합니다. 그렇지만 queue 커넥션은 각기 다른 job이 쌓여 있는 여러개의 "queue-큐"를 가질 수도 있습니다.

`queue` 설정 파일 안에 있는 각각의 커넥션 설정 예제가 `queue` 속성을 포함하고 있다는 것을 주의하십시오. 이것은 job이 처리되기 위해 주어진 커넥션에 보내졌을 때의 기본 queue 입니다. 다시 말해, job을 어떤 queue를 통해서 처리할지 명시적으로 정의하지 않는다면, job은 커넥션 설정의 `queue` 속성에 정의되어 있는 큐에 보내집니다.

    // This job is sent to the default queue...
    Job::dispatch();

    // This job is sent to the "emails" queue...
    Job::dispatch()->onQueue('emails');

일부 애플리케이션은 job을 여러개의 queue에 푸시 할 필요가 없으며 하나의 queue에 보내는것이 더 나을 수도 있습니다. 하지만, 여러 queue에 job을 보내는 것은 우선 순위를 부여하고자 한다거나, 혹은 job 처리를 분할하고자하는 애플리케이션에서는 특히나 유용합니다. 라라벨의 queue worker는 우선 순위별로 처리해야하는 queue을 지정할 수 있기 때문입니다. 예를 들어, job을 `high` queue로 푸시한다면, 이 job들을 높은 우선 순위를 부여하여 처리할 수 있습니다.

    php artisan queue:work --queue=high,default

<a name="driver-prerequisites"></a>
### 드라이버 주의사항과 사전준비사항

#### 데이터베이스

`database` queue 드라이버를 사용하기 위해서는 Job들을 담아둘 데이터베이스 테이블이 필요합니다. 이 테이블을 추가하기 위한 마이그레이션을 생성하려면 `queue:table` 아티즌 명령을 실행하면 됩니다. 마이그레이션 파일이 생성되고 나면 `migrate` 명령어를 사용하여 데이터베이스 테이블을 생성할 수 있습니다.

    php artisan queue:table

    php artisan migrate

#### Redis

`redis` 큐 드라이버를 사용하기 위해서는 `config/database.php` 설정 파일에서 Redis 커넥션을 설정해야 합니다.

**Redis 클러스터**

Redis queue 커넥션이 Redis 클러스터를 사용한다면, queue 이름은 [key hash tag](https://redis.io/topics/cluster-spec#keys-hash-tags)를 반드시 포함하고 있어야 합니다. 이것은 큐-queue 가 Redis 동일한 해시 슬롯에 부여됨을 보호 하고자 필요로 합니다.

    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => '{default}',
        'retry_after' => 90,
    ],

**대기하기**

Redis queue를 사용할 때,`block_for` 설정 옵션을 사용하여 드라이버가 작업 루프를 반복하고 Redis 데이터베이스를 다시 폴링하기 전에 job이 사용 가능하게 될 때까지 대기해야하는 시간을 지정할 수 있습니다.

새로운 job을 가져오기 위해 Redis 데이터베이스를 계속 폴링하는 것보다는 큐의 부하에 따라 조절하는 게 더 효율적입니다. 예를 들면, 새로운 job이 사용 가능하게 될 때까지 드라이버가 5초를 기다려야 한다는 의미로 이 값을 5로 설정할 수 있습니다.

    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => 'default',
        'retry_after' => 90,
        'block_for' => 5,
    ],

> {note} `block_for`를 `0`으로 설정하면 작업을 사용할 수있을 때까지 큐 작업자가 무기한으로 대기됩니다. 이렇게하면 다음 작업이 처리 될 때까지 `SIGTERM`과 같은 신호가 처리되지 않습니다.

#### 다른 큐 드라이버의 사전준비 사항들

아래의 의존 패키지들은 앞서 이야기 했던 각각의 queue 드라이버들을 사용하는데 필요합니다.


- Amazon SQS: `aws/aws-sdk-php ~3.0`
- Beanstalkd: `pda/pheanstalk ~4.0`
- Redis: `predis/predis ~1.0` 또는 phpredis PHP 확장 모듈


<a name="creating-jobs"></a>
## Jobs 생성하기

<a name="generating-job-classes"></a>
### Job 클래스 생성하기

기본적으로, 애플리케이션을 위한 모든 queue Job들은 `app/Jobs` 디렉토리에 저장됩니다. `app/Jobs` 디렉토리가 존재하지 않는다면, `make:job` 아티즌 명령어를 실행할 때 생성됩니다. 새로운 queue Job 클래스는 아티즌 CLI를 통해서 생성할 수 있습니다.

    php artisan make:job ProcessPodcast

생성된 클래스는 Job이 queue를 통해서 비동기적으로 실행되어야 된다는 것을 나타내는, `Illuminate\Contracts\Queue\ShouldQueue` 인터페이스를 구현하고 있습니다.

<a name="class-structure"></a>
### 클래스 구조

Job 클래스는 매우 간단하며, 기본적으로 큐에 저장된 Job을 처리하기 위해서 호출되는 `handle` 메소드만을 가지고 있습니다. 시작에 앞서, 다음 Job 클래스의 예제를 살펴봅시다. 이 예제에서 우리는 팟캐스트 배포 서비스를 관리하고 업로드된 팟캐스트 파일을 배포하기 전 처리해야 된다고 가정하겠습니다.

    <?php

    namespace App\Jobs;

    use App\AudioProcessor;
    use App\Podcast;
    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Foundation\Bus\Dispatchable;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Queue\SerializesModels;

    class ProcessPodcast implements ShouldQueue
    {
        use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

        protected $podcast;

        /**
         * Create a new job instance.
         *
         * @param  Podcast  $podcast
         * @return void
         */
        public function __construct(Podcast $podcast)
        {
            $this->podcast = $podcast;
        }

        /**
         * Execute the job.
         *
         * @param  AudioProcessor  $processor
         * @return void
         */
        public function handle(AudioProcessor $processor)
        {
            // Process uploaded podcast...
        }
    }

이 예제에서 queue Job 클래스의 생성자에 [Eloquent 모델](/docs/{{version}}/eloquent)이 직접적으로 전달된다는 것을 주목하십시오. Job 클래스에서 사용하는 SerializesModels 트레이트-trait에 의해 Eloquent 모델은 효과적으로 serialize 될것이며, Job이 처리 될 때 unserialize 됩니다. queue에 저장된 Job이 생성자에서 Eloquent 모델을 전달 받는 경우, 모델의 식별자만 큐로 저장될 때 serialize 될 것입니다. Job이 실제로 처리될 때 queue 시스템은 자동으로 데이터베이스에서 해당 모델 인스턴스를 다시 가져옵니다. 이렇게 하는 것은 애플리케이션을 완전히 투명하게 하고, Eloquent 모델 인스턴스를 serialize 할 때 발생하는 문제를 방지 할 수 있습니다.

queue에 의해서 Job이 처리될 때에는 `handle` 메소드가 호출 됩니다. Job 클래스의 `handle` 메소드에 의존 객체들이 타입-힌트 될 수 있다는 것에 주의하십시오. 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)가 자동으로 의존 객체들을 주입해 줍니다.

`handle` 메소드에서 컨테이너가 의존 객체들을 어떻게 주입하는지에 대한 완전한 제어를 원한다면, 컨테이너의 `bindMethod` 메소드를 사용할 수 있습니다. `bindMethod` 메소드는 job 과 컨테이너를 인자로 받는 콜백을 인자로 받습니다. 콜백 안에서, 자유롭게 `handle` 메소드를 호출할 수 있습니다. 일반적으로 이는 [서비스 프로바이더](/docs/{{version}}/providers) 안에서 호출합니다.

    use App\Jobs\ProcessPodcast;

    $this->app->bindMethod(ProcessPodcast::class.'@handle', function ($job, $app) {
        return $job->handle($app->make(AudioProcessor::class));
    });

> {note} Raw 이미지와 같은 바이너리 데이터의 경우, 큐를 통해서 처리되기 전에 `base64_encode` 함수가 적용된 상태로 전달되어야 합니다. 그렇지 않으면 Job이 큐에 입력 될 때 JSON으로 제대로 serialize 되지 않을 수 있습니다.

<a name="job-middleware"></a>
### Job 미들웨어

Job 미들웨어를 사용하면 대기중인 Job 실행을 중심으로 커스텀 로직을 래핑하여 Job 자체의 중복코드를 줄일 수 있습니다. 예를 들어, 5 초마다 한 Job 만 처리 하기위해 Laravel의 Redis 속도 제한 기능을 활용하는 다음 `handle` 메소드를 생각해보십시오.

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        Redis::throttle('key')->block(0)->allow(1)->every(5)->then(function () {
            info('Lock obtained...');

            // Handle job...
        }, function () {
            // Could not obtain lock...

            return $this->release(5);
        });
    }

이 코드는 동작은 하지만, `handle` 메소드의 구조는 Redis 속도 제한 로직으로 지저분해집니다. 또한 이 속도 제한 로직은 속도를 제한하려는 다른 Job에 복제해야합니다.

handle 메소드에서 속도 제한을 직접 처리하는 대신 속도 제한을 하는 Job 미들웨어를 정의 할 수 있습니다. Laravel에는 Job 미들웨어의 기본 위치가 없으므로 Job 미들웨어를 애플리케이션의 어디든 배치 할 수 있습니다. 이 예제에서는 미들웨어를 `app/Jobs/Middleware` 디렉토리에 넣습니다.

    <?php

    namespace App\Jobs\Middleware;

    use Illuminate\Support\Facades\Redis;

    class RateLimited
    {
        /**
         * Process the queued job.
         *
         * @param  mixed  $job
         * @param  callable  $next
         * @return mixed
         */
        public function handle($job, $next)
        {
            Redis::throttle('key')
                    ->block(0)->allow(1)->every(5)
                    ->then(function () use ($job, $next) {
                        // Lock obtained...

                        $next($job);
                    }, function () use ($job) {
                        // Could not obtain lock...

                        $job->release(5);
                    });
        }
    }

보시다시피 [route 미들웨어](/docs/{{version}}/middleware)와 같이 Job 미들웨어는 처리중인 Job과 Job 처리를 계속하기 위해 호출해야하는 콜백을 받습니다.

Job 미들웨어를 작성한 후에는 Job의 `middleware`메소드에서 리턴받아 Job에 추가 할 수 있습니다. 이 메소드는 `make:job` 아티즌 명령으로 생성된 된 Job에는 존재하지 않으므로 직접 Job 클래스에 추가해야합니다.

    use App\Jobs\Middleware\RateLimited;

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [new RateLimited];
    }

<a name="dispatching-jobs"></a>
## Job 처리하기

Job 클래스를 작성한 뒤에 클래스의 `dispatch` 메소드를 사용하여 이를 처리할 수 있습니다. `dispatch` 메소드에 전달할 인자는 job의 생성자입니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Jobs\ProcessPodcast;
    use Illuminate\Http\Request;

    class PodcastController extends Controller
    {
        /**
         * Store a new podcast.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            // Create podcast...

            ProcessPodcast::dispatch($podcast);
        }
    }

<a name="delayed-dispatching"></a>
### 지연시켜서 처리하기

대기열에 있는 job의 실행을 지연하려면, job 이 dispathcing 될 때 `delay` 메소드를 사용할 수 있습니다. 예를 들어, job이 10 분이 지난 뒤에 처리되도록 지정해보겠습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Jobs\ProcessPodcast;
    use Illuminate\Http\Request;

    class PodcastController extends Controller
    {
        /**
         * Store a new podcast.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            // Create podcast...

            ProcessPodcast::dispatch($podcast)
                    ->delay(now()->addMinutes(10));
        }
    }

> {note} 아마존 SQS 큐 서비스는 지연시간이 최대 15분을 넘을 수 없습니다.

<a name="synchronous-dispatching"></a>
### 동기식 반환

작업을 즉시 (동기적으로) 반환하고 싶다면 `dispatchNow` 메소드를 사용할 수 있습니다. 이 방법을 사용하면 작업이 대기열에 저장되지 않고 현재 프로세스 내에서 즉시 실행됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Jobs\ProcessPodcast;
    use Illuminate\Http\Request;

    class PodcastController extends Controller
    {
        /**
         * Store a new podcast.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            // Create podcast...

            ProcessPodcast::dispatchNow($podcast);
        }
    }

<a name="job-chaining"></a>
### Job 체이닝

Job 체이닝은 여러분이 대기열에 입력된 job이 순차적으로 실행되도록 목록을 지정할 수 있게 해줍니다. 이 순차적인 목록에서 하나의 job 이 실패하면, 나머지 job은 실행되지 않습니다. job 체이닝을 실행하려면, 실행 가능한 모든 jobs 에 `withChain` 메소드를 사용하면 됩니다.

    ProcessPodcast::withChain([
        new OptimizePodcast,
        new ReleasePodcast
    ])->dispatch();

> {note} `$this->delete()` 메소드를 사용하여 작업을 삭제한다고해서 연결된 작업이 처리되는 것을 막을 수는 없습니다. 체인 안에서 진행되는 작업이 실패하면 체인은 실행을 중지합니다.

#### Connection과 Queue 체이닝

체이닝된 job에 사용될 기본 커넥션과 queue를 지정하고 싶다면, `allOnConnection`과 `allOnQueue` 메소드를 사용하면 됩니다. 이 메소드는 대기열에 있는 job이 명시적으로 다른 커넥션 / queue로 지정되지 않을 경우 사용될 queue 커넥션과 queue 이름을 지정합니다.

    ProcessPodcast::withChain([
        new OptimizePodcast,
        new ReleasePodcast
    ])->dispatch()->allOnConnection('redis')->allOnQueue('podcasts');


<a name="customizing-the-queue-and-connection"></a>
### Queue-큐 & 커넥션 커스터마이징

#### 특정 queue-큐로 처리하기

다른 queue-큐에 job 푸시를 통하여, queue job을 "분류"할 수 있으며, 다양한 queue에 다수의 worker를 지정하여 개별 우선순위를 지정할 수도 있습니다. 유념할 점은, 이것은 queue 설정 파일에 정의된 다른 "커넥션"에 job을 푸시하는 것이 아니라, 하나의 커넥션 안에서 queue-큐를 지정한다는 것입니다. queue-큐를 지정하려면 job 이 dispatching 될 때 `onQueue` 메소드를 사용하면 됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Jobs\ProcessPodcast;
    use Illuminate\Http\Request;

    class PodcastController extends Controller
    {
        /**
         * Store a new podcast.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            // Create podcast...

            ProcessPodcast::dispatch($podcast)->onQueue('processing');
        }
    }

#### 특정 커넥션으로 처리하기

여러개의 queue-큐 커넥션을 사용하고자 한다면, 푸시하는 job에 커넥션을 지정할 수 있습니다. job이 dispatching 될 때 커넥션을 지정하기 위해서는 `onConnection` 메소드를 사용하면 됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Jobs\ProcessPodcast;
    use Illuminate\Http\Request;

    class PodcastController extends Controller
    {
        /**
         * Store a new podcast.
         *
         * @param  Request  $request
         * @return Response
         */
        public function store(Request $request)
        {
            // Create podcast...

            ProcessPodcast::dispatch($podcast)->onConnection('sqs');
        }
    }

You may chain the `onConnection` and `onQueue` methods to specify the connection and the queue for a job:
job을 처리하는 queue에 특정 커넥션에서 실행하려면 `onConnection` 과 `onQueue` 메소드를 체이닝하여 사용할 수도 있습니다.

    ProcessPodcast::dispatch($podcast)
                  ->onConnection('sqs')
                  ->onQueue('processing');

또는 작업 클래스에서 `connection`을 속성으로 지정할 수 있습니다.

    <?php

    namespace App\Jobs;

    class ProcessPodcast implements ShouldQueue
    {
        /**
         * The queue connection that should handle the job.
         *
         * @var string
         */
        public $connection = 'sqs';
    }

<a name="max-job-attempts-and-timeout"></a>
### 최대 재시도 횟수 / 타임아웃 시간 지정하기

#### 최대 재시도 횟수

작업이 수행될 때 쵀대 재시도 횟수를 지정하려면, 아티즌 명령어에 `--tries` 옵션을 지정하면 됩니다.

    php artisan queue:work --tries=3

그렇지만, job 클래스 자체에 최대 재시도 횟수를 정의하여 보다 세부적으로 조절하는 방법도 있습니다. job 클래스에 최대 재시도 횟수가 지정되면 커맨드 라인에서 지정된 값보다 우선합니다.

    <?php

    namespace App\Jobs;

    class ProcessPodcast implements ShouldQueue
    {
        /**
         * The number of times the job may be attempted.
         *
         * @var int
         */
        public $tries = 5;
    }

<a name="time-based-attempts"></a>
#### 시간 기반의 재시도

job 이 최종적으로 실패처리 될 때까지, 얼마나 많이 재시도 할지 횟수를 정의하는 대신에, job이 시간초과 처리되는 기준 시간을 정의할 수 있습니다. 이를 이용하면 주어진 시간안에 job을 여러번 재시도하게 할 수 있습니다. job 의 시간초과를 정의하려면, job 클래스에 `retryUntil` 메소드를 추가하면 됩니다.

    /**
     * Determine the time at which the job should timeout.
     *
     * @return \DateTime
     */
    public function retryUntil()
    {
        return now()->addSeconds(5);
    }

> {tip} queued 이벤트 리스너에도 `retryUntil` 메소드를 정의할 수 있습니다.

#### 타임아웃

> {note} `timeout` 기능은 PHP 7.1 이상이며 `pcntl` PHP 확장 기능에 최적화 되어 있습니다.

마찬가지로, 작업이 수행될 때 최대 수행시간을 초단위로 지정할 수 있습니다. 이는 아티즌 명령어에 `--timeout` 옵션을 지정하면 됩니다.

    php artisan queue:work --timeout=30

또한, job 클래스 자체에 최대 작업 수행 시간을 초단위로 정의할 수도 있습니다. 타임아웃 시간이 job에 정의되면, 커맨드 라인에서 지정된 값보다 우선시 됩니다.

    <?php

    namespace App\Jobs;

    class ProcessPodcast implements ShouldQueue
    {
        /**
         * The number of seconds the job can run before timing out.
         *
         * @var int
         */
        public $timeout = 120;
    }

<a name="rate-limiting"></a>
### 실행 속도 제한

> {note} 이 기능을 사용하려면, 애플리케이션이 [Redis server](/docs/{{version}}/redis)에 연결되어 있어야 합니다.

애플리케이션이 Redis 에 연결되어 있는 경우, queue job을 시간 또는 동시에 처리할 수 있는 수를 제한할 수 있습니다. 이 기능은 사용량 제한이 있는 외부 API 작업을 수행하는 queue job을 실행할 때 도움이 될 수 있습니다.

예를 들어 `throttle` 메소드를 사용하여 주어진 job이 60초마다 10번만 실행되도록 조절할 수 있습니다. lock을 획득할 수 없는 경우에는 일반적으로 job을 queue에 릴리즈 한 뒤 나중에 다시 시도하도록 할 수 있습니다.

    Redis::throttle('key')->allow(10)->every(60)->then(function () {
        // Job logic...
    }, function () {
        // Could not obtain lock...

        return $this->release(10);
    });

> {tip} 이 예제에서, `key` 는 속도제한을 시키고자 하는 job을 식별할 수 있는 고유한 문자열입니다. 예를 들면, 이 키는 job의 클래스 이름 그리고 Eloquent 모델의 id 값을 기반으로 구성할 수 있습니다.

> {note} 스로틀 작업을 대기열로 다시 보낸다면 job의 총 시도 횟수가 증가합니다.

이 대신에, 여러분은 주어진 job을 동시에 처리할 수 있는 worker의 최대 개수를 지정할 수도 있습니다. 이는 queue에 들어 있는 job이 리소스를 수정하는 것과 같이, 한번에 하나씩 실행되어야 하는 경우 유용합니다. 예를 들어, `funnel` 메소드를 사용하여, 한번에 하나의 worker를 통해서 처리되도록 하는 유형의 job에서 제한을 걸 수도 있습니다.

    Redis::funnel('key')->limit(1)->then(function () {
        // Job logic...
    }, function () {
        // Could not obtain lock...

        return $this->release(10);
    });

> {tip} 사용량 제한을 사용하는 경우, job의 성공적 실행을 위해 필요한 재시도 되는 횟수를 결정하는 것은 매우 어렵습니다. 그래서 이런경우 사용량 제한과 함께 [시간 기반 재시도 횟수 지정하기](#time-based-attempts)를 사용하는 것이 유용합니다.

<a name="error-handling"></a>
### 에러 핸들

job이 처리되는 동안에 exception이 발생하면, job을 다시 시도하기 위하여 자동으로 queue로 반환됩니다. job은 애플리케이션에서 정의된 최대 재시도 횟수만큼 계속해서 실행됩니다. 재시도 횟수는 `queue:work` 아티즌 명령어를 사용할 때 `--tries` 옵션을 사용하여 정의됩니다. 재시도 횟수를 job클래스 자체에 정의할 수도 있습니다. queue worker에 대한 보다 자세한 사항은 [다음에서 찾을 수 있습니다](#running-the-queue-worker)

<a name="queueing-closures"></a>
## 큐잉 클로저

작업 클래스를 대기열로 보내지 않고 Closure를 보낼 수도 있습니다. 이는 현재 요청주기를 벗어나 실행 해야하는 빠르고 간단한 작업에 유용합니다.

    $podcast = App\Podcast::find(1);

    dispatch(function () use ($podcast) {
        $podcast->publish();
    });

클로저를 queue에 dispatch 할 때 Closure의 코드 내용은 암호화 되어 서명되어 전송 중에 수정할 수 없습니다.

<a name="running-the-queue-worker"></a>
## Queue-큐 worker 실행하기

라라벨은 queue에 푸시된 새로운 job을 처리하는 queue worker를 포함하고 있습니다. `queue:work` 아티즌 명령어를 사용하여 worker를 실행할 수 있습니다. `queue:work` 명령어가 시작하고 나면, 여러분이 수동으로 중지하거나, 터미널을 닫을 때 까지 계속 실행되어 있습니다.

    php artisan queue:work

> {tip} `queue:work` 프로세스를 백그라운드에서 계속 지속되게 하려면, queue worker가 중단되지 않는 것을 보장하기 위해 [Supervisor](#supervisor-configuration)와 같은 프로세스 모니터를 사용해야 합니다.

주의할 점은 queue worker는 장시간 동안 살아 있는 프로세스 이며 애플리케이션의 상태를 메모리에 저장한다는 것입니다. 그 결과, 일단 구동되고 나면 코드 기반의 변경사항은 반영되지 않습니다. 따라서 개발 중에는 직접 [queue worker를 재시작](#queue-workers-and-deployment)해야 합니다.

또는 `queue:listen` 명령을 실행할 수도 있습니다. `queue:listen` 명령을 사용할 때 코드가 변경된 후에는 worker를 수동으로 다시 시작할 필요가 없습니다. 그러나 이 명령은 `queue:work`만큼 효율적이지 않습니다.

    php artisan queue:listen

#### 커넥션 & queue-큐 지정하기

어떤 queue-큐 커넥션을 worker 가 사용할지 지정할 수도 있습니다. `work` 명령어에 전달된 커넥션 이름은 `config/queue.php` 설정 파일에 정의되어 있는 커넥션 이름과 일치해야 합니다.

    php artisan queue:work redis

주어진 커넥션의 특정 queue만 처리되도록 queue-큐 worker를 커스터마이즈 할 수 있습니다. 예를 들어, 모든 이메일이 `redis` queue-큐 커넥션의 `emails` queue-큐에서 처리되도록 하고자 할경우, 다음의 명령어를 사용하여 하나의 queue-큐를 처리하는 worker를 시작할 수 있습니다.

    php artisan queue:work redis --queue=emails

#### 하나의 단일 Job 처리하기

`--once` 옵션은 worker 가 queue로 부터 하나의 단일 job을 처리하도록 합니다.

    php artisan queue:work --once

#### 대기중인 모든 작업 처리 및 종료

`--stop-when-empty` 옵션은 워커에게 모든 작업을 처리 한 다음 정상적으로 종료하도록 지시하는 데 사용할 수 있습니다. 이 옵션은 Docker 컨테이너에서 Laravel queue가 동작 할 때 queue가 빈 후 컨테이너를 종료하려면 유용 할 수 있습니다.

    php artisan queue:work --stop-when-empty

#### 리소스 고려사항

데몬 queue worker는 각 job을 처리하기 전에 프레임워크를 "reboot"하지 않습니다. 따라서 각 job이 완료되면 사용된 리소스를 해제(free)해야 합니다. 예를 들어 GD 라이브러리로 이미지를 조작하는 경우, 작업이 완료되면 `imagedestroy`를 사용하여 메모리를 해제해야 합니다.

<a name="queue-priorities"></a>
### queue의 우선순위

때로는 queue-큐를 어떻게 처리할 것인지 우선순위를 지정하고자 할 수도 있습니다. 예를 들어, `config/queue.php`에서 `redis` 커넥션의 기본 `queue`를 `low`로 지정하였지만, 어떤 job을 `high` 우선순위로 queue에 푸시하려는 경우입니다.

    dispatch((new Job)->onQueue('high'));

job 이 `low` queue로 처리되기 전에, `high` queue job으로 처리되는 것을 명확히 하여 worker가 시작되게 하려면, 콤마로 구분된 queue 이름의 목록을 `work` 명령어에 전달하면 됩니다.

    php artisan queue:work --queue=high,low

<a name="queue-workers-and-deployment"></a>
### Queue worker & 배포

queue worker는 장시간 동안 살아 있는 프로세스이기 때문에, 재시작하지 않으면, 코드 변경사항이 적용되지 않습니다. 따라서, queue worker를 사용하는 애플리케이션을 배포하는 가장 간단한 방법은 배포 프로세스 안에서 worker를 재시작하는 것입니다. `queue:restart` 명령어를 사용하여 모든 worker를 정상적으로 재시작할 수 있습니다.

    php artisan queue:restart

이 명령어는 현재 job이 손실되지 않도록 현재 job의 처리가 종료 된 후 전체 queue worker에 "종료(die)" 되도록 지시합니다. `queue:restart` 명령이 실행되면 queue worker는 종료되기 때문에, 자동으로 queue worker를 다시 시작하는 [Supervisor](#supervisor-configuration)와 같은 프로세스 매니저를 실행해야 합니다.

> {tip} 큐-queue는 [cache](/docs/{{version}}/cache)에 재시작 시그널을 저장합니다. 따라서 이 기능을 사용하기 전에 애플리케이션에 캐시 드라이버가 적절히 설정되었는지 확인해야합니다.

<a name="job-expirations-and-timeouts"></a>
### Job 만료 & 타임아웃

#### Job 만료

`config/queue.php` 설정 파일에서 각각의 queue 커넥션에는 `retry_after` 옵션을 정의합니다. 이 옵션은 job 처리를 다시 시도하기 전에 queue 커넥션에서 얼마나 대기해야 되는지를 지정합니다. 예를 들어 `retry_after` 값이 `90` 이라면, 해당 job은 처리가 완료된 후에, 90초동안 제거되지 않으면 queue에 다시 투입됩니다. 일반적으로 `retry_after` 값은 job 이 합리적으로 완료될 때까지의 최대 시간 값(초)를 지정해야합니다.

> {note} 아마존 SQS에서는 `retry_after` 값은 포함되어 있지 않습니다. SQS는 AWS 콘솔 안에서 관리되는 [Default Visibility Timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/AboutVT.html)에 의해서 job이 재시작됩니다.

#### worker 타임아웃

`queue:work` 아티즌 명령어는 `--timeout` 옵션도 지원합니다. `--timeout` 옵션은 라라벨의 queue 마스터 프로세스가 작업을 처리하는 child queue worker가 job을 얼마나 오래 처리하는지 지정합니다. 때로는 child queue 프로세스는 외부 HTTP 호출이 응답이 없는 것과 같은 다양한 이유로, "먹통"이 될 수 있습니다. `--timeout` 옵션은 지정된 실행 시간이 지난 뒤에, "먹통"이 된 프로세스를 제거합니다.

    php artisan queue:work --timeout=60

`retry_after` 설정 옵션과 `--timeout` CLI 옵션은 서로 다릅니다. 하지만 job이 한번에 성공적으로 처리될 수 있도록 하는데 함께 작동합니다.

> {note} `--timeout` 값은 `retry_after` 설정 값보다 몇초 짧게 설정해야 합니다. 이렇게하면 주어진 job을 처리하는 worker가 작업 재시도 하기 전에 중지됩니다. `--timeout` 옵션을 `retry_after` 설정 값보다 길게하면 작업이 두 번 실행 될 것입니다.

#### Worker 잠자기 시간

job 이 큐에서 사용가능해지면, worker는 딜레이 없이 바로 job을 처리합니다. 그렇지만 `sleep` 옵션이 설정되면 worker는 새로운 job을 처리할 때 일정 시간(초단위)동안 "잠자기 (sleep)" 하게 됩니다. 잠자기 동안에는 새로운 job을 처리하지 않습니다 - worker가 다시 깨어나면 job을 처리합니다.

    php artisan queue:work --sleep=3

<a name="supervisor-configuration"></a>
## Supervisor 설정하기

#### Supervisor 설치하기

Supervisor는 리눅스 OS를 위한 프로세스 모니터로, `queue:work` 프로세스가 종료된 경우 자동으로 이를 재시작 할 것입니다. 우분투에서 Supervisor를 설치하기 위해서는 다음의 명령어를 사용하면 됩니다.

    sudo apt-get install supervisor

> {tip} Supervisor 설정이 너무 어렵게 느껴진다면, 자동으로 이를 설치하고 설정할 수 있게 해주는 라라벨 프로젝트인 [라라벨 Forge](https://forge.laravel.com)를 사용하는 것을 고려해보십시오.

#### Supervisor 설정하기

Supervisor 설정 파일은 일반적으로 `/etc/supervisor/conf.d` 디렉토리에 저장되어 있습니다. 이 디렉토리 안에서 Supervisor가 어떻게 프로세스를 모니터링 할 것인지 지시하는 설정 파일을 원하는 수 만큼 생성할 수 있습니다. 예를 들어, `laravel-worker.conf` 파일을 만들고 `queue:work` 프로세스를 시작하고, 모니터링 해 보겠습니다.

    [program:laravel-worker]
    process_name=%(program_name)s_%(process_num)02d
    command=php /home/forge/app.com/artisan queue:work sqs --sleep=3 --tries=3
    autostart=true
    autorestart=true
    user=forge
    numprocs=8
    redirect_stderr=true
    stdout_logfile=/home/forge/app.com/worker.log

이 예제에서, `numprocs` 지시어는 Supervisor에 총 8 개의 `queue:work` 프로세스를 실행하고 이들을 모니터링하여, 이 프로세스가 죽어 있으면, 자동으로 재시작하도록 지시하고 있습니다. `command` 지시어의 `queue:work sqs` 부분을 변경하고 선택한 커넥션에 맞추도록 해야합니다.

#### Supervisor 시작하기

설정 파일을 생성하고 나면, 다음 명령어를 통해서 Supervisor 설정을 업데이트 하고, 시작할 수 있습니다.

    sudo supervisorctl reread

    sudo supervisorctl update

    sudo supervisorctl start laravel-worker:*

Supervisor에 대한 보다 자세한 정보는 [Supervisor 문서](http://supervisord.org/index.html)를 참고하십시오.

<a name="dealing-with-failed-jobs"></a>
## 실패한 Job 처리하기

종종 여러분들의 queue Job은 실패하기도 합니다. 걱정하지 마세요, 모든 일들이 항상 계획한 것처럼 진행되지 않습니다. 라라벨은 Job이 시도되는 최대 횟수를 지정하는 편리한 방법을 제공합니다. Job이 제한된 횟수를 초과하는 경우 이 Job들은 `failed_jobs` 데이터베이스 테이블에 추가됩니다. `queue:failed-table` 명령을 사용하면 `failed_jobs` 테이블에 대한 마이그레이션을 만들 수 있습니다.

    php artisan queue:failed-table

    php artisan migrate

그런 뒤에, [queue worker](#running-the-queue-worker)가 실행될 때, `queue:listen` 명령어의 `--tries` 옵션을 사용하여 Job이 재시도 되어야 할 최대 횟수를 지정해야합니다. `--tries` 옵션값을 지정하지 않는다면, job은 무한정 계속 시도됩니다.

    php artisan queue:work redis --tries=3

또한 `--delay` 옵션을 사용하여 실패한 작업을 다시 시도하기 전에 Laravel이 기다려야하는 시간을 지정할 수 있습니다. 기본적으로 Job은 즉시 재시도됩니다.

    php artisan queue:work redis --tries=3 --delay=3

Job 마다 실패한 Job의 재시도 지연을 설정하려면 대기중인 Job 클래스에 `retryAfter` 속성을 정의하면됩니다.

    /**
     * The number of seconds to wait before retrying the job.
     *
     * @var int
     */
    public $retryAfter = 3;

<a name="cleaning-up-after-failed-jobs"></a>
### 실패한 Job 정리하기

job 클래스에 `failed` 메소드를 정의할 수 있습니다. 이는 실패가 발생했을 때 job을 정리하는 액션을 수행할 수 있게 합니다. 이 메소드는 사용자에게 알림을 보내거나, job에서 실행된 액션을 되돌리는 역할을 하기 좋습니다. `failed`메소드에는 job에서 발생한 `Exception`이 전달됩니다.

    <?php

    namespace App\Jobs;

    use App\AudioProcessor;
    use App\Podcast;
    use Exception;
    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Queue\SerializesModels;

    class ProcessPodcast implements ShouldQueue
    {
        use InteractsWithQueue, Queueable, SerializesModels;

        protected $podcast;

        /**
         * Create a new job instance.
         *
         * @param  Podcast  $podcast
         * @return void
         */
        public function __construct(Podcast $podcast)
        {
            $this->podcast = $podcast;
        }

        /**
         * Execute the job.
         *
         * @param  AudioProcessor  $processor
         * @return void
         */
        public function handle(AudioProcessor $processor)
        {
            // Process uploaded podcast...
        }

        /**
         * The job failed to process.
         *
         * @param  Exception  $exception
         * @return void
         */
        public function failed(Exception $exception)
        {
            // Send user notification of failure, etc...
        }
    }

<a name="failed-job-events"></a>
### 실패한 Job에 대한 이벤트

Job이 실패한 경우에 호출될 이벤트를 등록하려면, `Queue::failing` 메소드를 사용하면 됩니다. 이 이벤트는 여러분의 팀에게 이메일 또는 [Slack](https://www.slack.com)과 같이 알림을 보낼 수 있습니다. 예를 들어 라라벨에 포함되어 있는 `AppServiceProvider` 에 이 이벤트 콜백을 추가해 보겠습니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Queue;
    use Illuminate\Support\ServiceProvider;
    use Illuminate\Queue\Events\JobFailed;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }
        
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Queue::failing(function (JobFailed $event) {
                // $event->connectionName
                // $event->job
                // $event->exception
            });
        }
    }

<a name="retrying-failed-jobs"></a>
### 실패한 Job 다시 시도하기

`failed_jobs` 데이터베이스 테이블에 추가된 실패한 모든 Job들을 보기 위해서 `queue:failed` 아티즌 명령을 사용할 수 있습니다.

    php artisan queue:failed

`queue:failed` 명령은 Job의 ID, 커넥션, queue, 그리고 실패 시간을 목록으로 보여줍니다. Job ID는 실패한 Job을 다시 시도하기 위해 사용될 수 있습니다. 예를 들어 `5`라는 ID를 가진 실패한 Job을 재시작할려면, 다음 명령어를 실행합니다.

    php artisan queue:retry 5

실패한 모든 Job들을 다시 시도하게 하려면 `queue:retry` 명령어에 ID 대신 `all`을 전달하여 실행하면 됩니다.

    php artisan queue:retry all

만약 실패한 Job을 삭제하기 위해서는 `queue:forget` 명령을 사용하면 됩니다.

    php artisan queue:forget 5

실패한 모든 Job들을 삭제하기 위해서는 `queue:flush` 명령을 사용할 수 있습니다.

    php artisan queue:flush

<a name="ignoring-missing-models"></a>
### 누락 된 모델 무시하기

Eloquent 모델을 작업에 주입 할 때 queue에 배치되기 전에 자동으로 직렬화되고 작업이 처리 될 때 복원됩니다. 그러나 작업자가 작업을 처리하는 동안 모델이 삭제 된 경우 작업이 `ModelNotFoundException`으로 실패 할 수 있습니다.

편의상 `deleteWhenMissingModels` 속성을 `true`로 설정하여 누락 된 모델이있는 작업을 자동으로 삭제하도록 선택할 수 있습니다.

    /**
     * Delete the job if its models no longer exist.
     *
     * @var bool
     */
    public $deleteWhenMissingModels = true;

<a name="job-events"></a>
## Job 이벤트

`Queue` [파사드](/docs/{{version}}/facades)의 `before` 와 `after` 메소드를 사용하여, queue Job이 실행되기 전 과 이후에 실행될 콜백을 지정할 수 있습니다. 이 콜백은 로깅이나 대시보드의 통계를 증가시키는것과 같은 추가적인 작업을 수행하기에 좋습니다. 일반적으로 이 메소드는 [서비스 프로바이더](/docs/{{version}}/providers)에서 호출해야합니다. 예를 들어 라라벨에 포함되어 있는 `AppServiceProvider` 에서 실행할 수 있습니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Queue;
    use Illuminate\Support\ServiceProvider;
    use Illuminate\Queue\Events\JobProcessed;
    use Illuminate\Queue\Events\JobProcessing;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Queue::before(function (JobProcessing $event) {
                // $event->connectionName
                // $event->job
                // $event->job->payload()
            });

            Queue::after(function (JobProcessed $event) {
                // $event->connectionName
                // $event->job
                // $event->job->payload()
            });
        }
    }

`Queue` [파사드](/docs/{{version}}/facades)의 `looping` 메소드를 사용할 때, worker가 큐에서 job을 처리하기 전에 실행할 콜백을 지정할 수 있습니다. 예를 들어, 이전에 실패한 작업이 남겨놓은 트랜젝션을 롤백하는 클로저를 등록할 수 있습니다.

    Queue::looping(function () {
        while (DB::transactionLevel() > 0) {
            DB::rollBack();
        }
    });
