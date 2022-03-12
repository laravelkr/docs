# Queues-큐

- [시작하기](#introduction)
    - [커넥션 Vs. Queues-큐](#connections-vs-queues)
    - [드라이버 주의사항과 사전준비사항](#driver-prerequisites)
- [Job 생성하기](#creating-jobs)
    - [Job 클래스 생성하기](#generating-job-classes)
    - [클래스 구조](#class-structure)
    - [고유한 작업-Job](#unique-jobs)
- [Job 미들웨어](#job-middleware)
    - [속도 제한](#rate-limiting)
    - [작업 중복 방지](#preventing-job-overlaps)
    - [작업량 초과 예외-Exception](#throttling-exceptions)
- [Job 처리하기](#dispatching-jobs)
    - [지연시켜서 처리하기](#delayed-dispatching)
    - [동기식 반환](#synchronous-dispatching)
    - [작업 및 데이터베이스 트랜잭션](#jobs-and-database-transactions)
    - [Job 체이닝](#job-chaining)
    - [Queue-큐 & 커넥션 커스터마이징](#customizing-the-queue-and-connection)
    - [최대 재시도 횟수 / 타임아웃 시간 지정하기](#max-job-attempts-and-timeout)
    - [에러 핸들링](#error-handling)
- [작업 배치](#job-batching)
    - [일괄 처리 가능한 작업 정의](#defining-batchable-jobs)
    - [일괄 작업 실행하기](#dispatching-batches)
    - [배치에 작업 추가](#adding-jobs-to-batches)
    - [배치 검사](#inspecting-batches)
    - [배치 취소](#cancelling-batches)
    - [배치 실패](#batch-failures)
    - [배치 정리하기](#pruning-batches)
- [큐잉 클로저](#queueing-closures)
- [Queue Worker 구동하기](#running-the-queue-worker)
    - [`queue:work` 명령](#the-queue-work-command)
    - [Queue 우선순위 지정하기](#queue-priorities)
    - [Queue Workers & 배포](#queue-workers-and-deployment)
    - [Job 만료 & 타임아웃](#job-expirations-and-timeouts)
- [Supervisor 설정](#supervisor-configuration)
- [실패한 Job 처리하기](#dealing-with-failed-jobs)
    - [Job 실패 후 처리](#cleaning-up-after-failed-jobs)
    - [실패한 작업 재시도](#retrying-failed-jobs)
    - [누락된 모델 무시하기](#ignoring-missing-models)
    - [DynamoDB에 실패한 작업 저장](#storing-failed-jobs-in-dynamodb)
    - [실패한 작업 정리하기](#pruning-failed-jobs)
    - [실패한 작업 이벤트](#failed-job-events)
- [큐-queue에서 작업 지우기](#clearing-jobs-from-queues)
- [큐-queue 모니터링](#monitoring-your-queues)
- [Job 이벤트](#job-events)

<a name="introduction"></a>
## 시작하기

웹 애플리케이션을 개발하는 동안, 업로드 된 CSV 파일의 구문 분석 및 저장과 같은 작업을 개발 할 수 있으며, 이는 일반적인 웹 요청 중에 처리하기에는 너무 오래 걸립니다. 고맙게도 라라벨을 사용하면 백그라운드에서 처리될 수 있는 대기 작업을 쉽게 생성할 수 있습니다. 시간이 오래걸리는 작업을 큐-queue로 이동함으로써, 애플리케이션은 웹 요청을 빠른 속도로 응답하고 고객에게 더 나은 사용자 경험을 제공할 수 있습니다.

라라벨 큐-queue은 [Amazon SQS](https://aws.amazon.com/sqs/), [Redis](https://redis.io) 또는 관계형 데이터베이스와 같은, 다양한 큐-queue를 사용 할 수 있는 통합 큐-queue API를 제공합니다.

라라벨의 큐-queue 설정 옵션은 애플리케이션의 `config/queue.php` 설정 파일에 저장됩니다. 이 파일에는 [Amazon SQS](https://aws.amazon.com/sqs/), [Redis](https://redis.io), 데이터베이스를 포함하여, 프레임워크에 포함된 각 큐-queue 드라이버에 대한 연결 설정이 있습니다. [Beanstalkd](https://beanstalkd.github.io/) 드라이버, 작업을 즉시 실행하는 동기 드라이버(로컬 개발 중에 사용). 대기 중인 작업을 삭제하는 `null` 큐-queue 드라이버도 포함되어 있습니다.

> {tip} 라라벨은 이제 Redis 큐-queue를 위한 멋진 대시보드와 설정 시스템을 제공하는 Horizon을 지원합니다. 보다 자세한 사항은 [Horizon 문서](/docs/{{version}}/horizon)를 참고하십시오.

<a name="connections-vs-queues"></a>
### 커넥션 Vs. Queues-큐

라라벨 큐를 시작하기 전에 "연결-connection"과 "큐-queue"의 차이점을 이해하는 것이 중요합니다. `config/queue.php` 설정 파일에 `connections` 설정 배열이 있습니다. 이 옵션은 Amazon SQS, Beanstalk 또는 Redis와 같은 백엔드 큐-queue 서비스에 대한 연결을 정의합니다. 그러나 주어진 큐-queue 연결에는 큐-queue에 있는 작업의 다른 스택이나 더미로 간주될 수 있는 여러 "큐-queue"가 있을 수 있습니다.

`queue` 설정 파일 안에 있는 각각의 커넥션 설정 예제가 `queue` 속성을 포함하고 있다는 것을 주의하십시오. 이것은 job이 처리되기 위해 주어진 커넥션에 보내졌을 때의 기본 queue 입니다. 다시 말해, job을 어떤 queue를 통해서 처리할지 명시적으로 정의하지 않는다면, job은 커넥션 설정의 `queue` 속성에 정의되어 있는 큐에 보내집니다.

    use App\Jobs\ProcessPodcast;

    // This job is sent to the default connection's default queue...
    ProcessPodcast::dispatch();

    // This job is sent to the default connection's "emails" queue...
    ProcessPodcast::dispatch()->onQueue('emails');

일부 애플리케이션은 job을 여러개의 queue에 푸시 할 필요가 없으며 하나의 queue에 보내는 것이 더 나을 수도 있습니다. 하지만, 여러 queue에 job을 보내는 것은 우선 순위를 부여하고자 한다거나, 혹은 job 처리를 분할하고자하는 애플리케이션에서는 특히나 유용합니다. 라라벨의 queue worker는 우선 순위별로 처리해야하는 queue을 지정할 수 있기 때문입니다. 예를 들어, job을 `high` queue로 푸시한다면, 이 job들을 높은 우선 순위를 부여하여 처리할 수 있습니다.

    php artisan queue:work --queue=high,default

<a name="driver-prerequisites"></a>
### 드라이버 주의사항과 사전준비사항

<a name="database"></a>
#### 데이터베이스

`database` queue 드라이버를 사용하기 위해서는 Job들을 담아둘 데이터베이스 테이블이 필요합니다. 이 테이블을 추가하기 위한 마이그레이션을 생성하려면 `queue:table` 아티즌 명령을 실행하면 됩니다. 마이그레이션 파일이 생성되고 나면 `migrate` 명령어를 사용하여 데이터베이스 테이블을 생성할 수 있습니다.

    php artisan queue:table

    php artisan migrate

마지막으로 애플리케이션의 `.env` 파일에서 `QUEUE_CONNECTION` 변수를 업데이트하여 `database` 드라이버를 사용하도록 애플리케이션에 지시하는 것을 잊지 마십시오.

    QUEUE_CONNECTION=database

<a name="redis"></a>
#### Redis

`redis` 큐 드라이버를 사용하기 위해서는 `config/database.php` 설정 파일에서 Redis 커넥션을 설정해야 합니다.

**Redis 클러스터**

Redis queue 커넥션이 Redis 클러스터를 사용한다면, queue 이름은 [key hash tag](https://redis.io/topics/cluster-spec#keys-hash-tags) 를 반드시 포함하고 있어야 합니다. 이것은 큐-queue 가 Redis 동일한 해시 슬롯에 부여됨을 보호 하고자 필요로 합니다.

    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => '{default}',
        'retry_after' => 90,
    ],

**대기하기**

Redis queue를 사용할 때, `block_for` 설정 옵션을 사용하여 드라이버가 작업 루프를 반복하고 Redis 데이터베이스를 다시 폴링하기 전에 job이 사용 가능하게 될 때까지 대기해야하는 시간을 지정할 수 있습니다.

새로운 job을 가져오기 위해 Redis 데이터베이스를 계속 폴링하는 것보다는 큐의 부하에 따라 조절하는 게 더 효율적입니다. 예를 들면, 새로운 job이 사용 가능하게 될 때까지 드라이버가 5초를 기다려야 한다는 의미로 이 값을 5로 설정할 수 있습니다.

    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => 'default',
        'retry_after' => 90,
        'block_for' => 5,
    ],

> {note} `block_for`를 `0`으로 설정하면 작업을 사용할 수있을 때까지 큐 작업자가 무기한으로 대기됩니다. 이렇게하면 다음 작업이 처리 될 때까지 `SIGTERM`과 같은 신호가 처리되지 않습니다.

<a name="other-driver-prerequisites"></a>
#### 다른 큐 드라이버의 사전준비 사항들

나열된 큐-queue 드라이버에는 다음 의존성이 필요합니다. 이러한 의존성은 Composer 패키지 관리자를 통해 설치할 수 있습니다.

- Amazon SQS: `aws/aws-sdk-php ~3.0`
- Beanstalkd: `pda/pheanstalk ~4.0`
- Redis: `predis/predis ~1.0` 또는 phpredis PHP 확장 모듈

<a name="creating-jobs"></a>
## Job 생성하기

<a name="generating-job-classes"></a>
### Job 클래스 생성하기

기본적으로 애플리케이션의 큐-queue에 있는 모든 작업은 `app/Jobs` 디렉토리에 저장됩니다. `app/Jobs` 디렉토리가 존재하지 않는 경우 `make:job` Artisan 명령을 실행할 때 생성됩니다.

    php artisan make:job ProcessPodcast

생성된 클래스는 Job이 queue를 통해서 비동기적으로 실행되어야 된다는 것을 나타내는, `Illuminate\Contracts\Queue\ShouldQueue` 인터페이스를 구현하고 있습니다.

> {tip} 작업 스텁은 [스텁 게시하기](/docs/{{version}}/artisan#stub-customization)를 사용하여 사용자 정의할 수 있습니다.

<a name="class-structure"></a>
### 클래스 구조

작업 클래스는 매우 간단하며 일반적으로 작업이 큐-queue에서 처리될 때 호출되는 `handle` 메서드만 포함합니다. 시작하기 위해 예제 작업 클래스를 살펴보겠습니다. 이 예에서는 팟캐스트 게시 서비스를 관리하고 업로드된 팟캐스트 파일을 게시하기 전에 처리해야 한다고 가정합니다.

    <?php

    namespace App\Jobs;

    use App\Models\Podcast;
    use App\Services\AudioProcessor;
    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Foundation\Bus\Dispatchable;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Queue\SerializesModels;

    class ProcessPodcast implements ShouldQueue
    {
        use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

        /**
         * The podcast instance.
         *
         * @var \App\Models\Podcast
         */
        protected $podcast;

        /**
         * Create a new job instance.
         *
         * @param  App\Models\Podcast  $podcast
         * @return void
         */
        public function __construct(Podcast $podcast)
        {
            $this->podcast = $podcast;
        }

        /**
         * Execute the job.
         *
         * @param  App\Services\AudioProcessor  $processor
         * @return void
         */
        public function handle(AudioProcessor $processor)
        {
            // Process uploaded podcast...
        }
    }

이 예제에서 [Eloquent 모델](/docs/{{version}}/eloquent)을 대기 중인 작업의 생성자에 직접 전달할 수 있었습니다. 작업이 사용하는 `SerializesModels` 특성-trait 때문에 Eloquent 모델과 로드된 관계는 작업이 처리될 때 정상적으로 직렬화되고 직렬화 해제됩니다.

큐-queue에 있는 작업이 생성자에서 Eloquent 모델을 수락하면 모델의 식별자만 큐-queue에 직렬화됩니다. 작업이 실제로 처리되면 큐-queue 시스템은 데이터베이스에서 전체 모델 인스턴스와 로드된 관계를 자동으로 다시 검색합니다. 모델 직렬화에 대한 이 접근 방식을 사용하면 훨씬 더 작은 작업 페이로드를 큐-queue 드라이버로 보낼 수 있습니다.

<a name="handle-method-dependency-injection"></a>
#### `handle` 메서드 의존성 주입

`handle` 메서드는 작업이 큐-queue에서 처리될 때 호출됩니다. 작업의 `handle` 메서드에 대한 타입 힌트 의존성을 확인할 수 있습니다. 라라벨 [서비스 컨테이너](/docs/{{version}}/container)는 이러한 의존성을 자동으로 주입합니다.

컨테이너가 `handle` 메서드에 의존성을 주입하는 방법을 완전히 제어하려면 컨테이너의 `bindMethod` 메서드를 사용할 수 있습니다. `bindMethod` 메소드는 작업과 컨테이너를 수신하는 콜백을 입력받습니다. 콜백 내에서 원하는 대로 `handle` 메서드를 호출할 수 있습니다. 일반적으로 `App\Providers\AppServiceProvider` [서비스 컨테이너](/docs/{{version}}/providers)의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

    use App\Jobs\ProcessPodcast;
    use App\Services\AudioProcessor;

    $this->app->bindMethod([ProcessPodcast::class, 'handle'], function ($job, $app) {
        return $job->handle($app->make(AudioProcessor::class));
    });

> {note} Raw 이미지와 같은 바이너리 데이터의 경우, 큐를 통해서 처리되기 전에 `base64_encode` 함수가 적용된 상태로 전달되어야 합니다. 그렇지 않으면 Job이 큐에 입력 될 때 JSON으로 제대로 serialize 되지 않을 수 있습니다.

<a name="handling-relationships"></a>
#### 관계 처리

로드된 관계도 직렬화되기 때문에 직렬화된 작업 문자열이 때때로 상당히 커질 수 있습니다. 관계가 직렬화되는 것을 방지하기 위해 속성 값을 설정할 때 모델에서 `withoutRelations` 메서드를 호출할 수 있습니다. 이 메서드는 로드된 관계 없이 모델의 인스턴스를 반환합니다.

    /**
     * Create a new job instance.
     *
     * @param  \App\Models\Podcast  $podcast
     * @return void
     */
    public function __construct(Podcast $podcast)
    {
        $this->podcast = $podcast->withoutRelations();
    }

<a name="unique-jobs"></a>
### 고유한 작업-Job

> {note} 고유한 작업에는 [locks](/docs/{{version}}/cache#atomic-locks)를 지원하는 캐시 드라이버가 필요합니다. 현재 `memcached`, `redis`, `dynamodb`, `database`, `file` 및 `array` 캐시 드라이버는 원자 잠금을 지원합니다. 또한 일괄-Batch 작업 내 작업에는 고유 작업 제약 조건이 적용되지 않습니다.

때로 특정 작업의 인스턴스 하나만 큐-queue에 존재해야하는 경우가 있습니다. 작업 클래스에 `ShouldBeUnique` 인터페이스를 구현하면 됩니다. 이 인터페이스에서는 클래스에 추가 메서드를 정의할 필요가 없습니다.

    <?php

    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Contracts\Queue\ShouldBeUnique;

    class UpdateSearchIndex implements ShouldQueue, ShouldBeUnique
    {
        ...
    }

위의 예제에서 `UpdateSearchIndex` 작업은 고유합니다. 따라서 작업의 다른 인스턴스가 이미 큐-queue에 있고 처리가 완료되지 않은 경우 작업이 디스패치되지 않습니다.

특정 경우 작업을 고유하게 만드는 특정 "키"를 정의하거나 작업이 더 이상 고유하지 않은 시간 초과를 지정하고자 할 수 있습니다. 이를 수행하기 위해 작업 클래스에서 `uniqueId` 및 `uniqueFor` 속성 또는 메서드를 정의할 수 있습니다.

    <?php

    use App\Product;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Contracts\Queue\ShouldBeUnique;

    class UpdateSearchIndex implements ShouldQueue, ShouldBeUnique
    {
        /**
         * The product instance.
         *
         * @var \App\Product
         */
        public $product;

        /**
         * The number of seconds after which the job's unique lock will be released.
         *
         * @var int
         */
        public $uniqueFor = 3600;

        /**
         * The unique ID of the job.
         *
         * @return string
         */
        public function uniqueId()
        {
            return $this->product->id;
        }
    }

위의 예제에서 `UpdateSearchIndex` 작업은 제품 ID별로 고유합니다. 따라서 기존 작업이 처리를 완료할 때까지 동일한 제품 ID를 가진 작업의 새 디스패치가 무시됩니다. 또한 기존 작업이 1시간 이내에 처리되지 않으면 고유 잠금이 해제되고 동일한 고유 키를 가진 다른 작업이 큐-queue에 디스패치될 수 있습니다.

<a name="keeping-jobs-unique-until-processing-begins"></a>
#### 처리가 시작될 때까지 고유한 작업 유지

기본적으로 고유한 작업은 작업이 처리를 완료하거나 모든 재시도에 실패한 후 "잠금 해제"됩니다. 그러나 작업이 처리되기 직전에 잠금을 해제하려는 상황이 있을 수 있습니다. 이를 수행하려면 작업에서 `ShouldBeUnique` 계약 대신 `ShouldBeUniqueUntilProcessing` 계약을 구현해야 합니다.

    <?php

    use App\Product;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Contracts\Queue\ShouldBeUniqueUntilProcessing;

    class UpdateSearchIndex implements ShouldQueue, ShouldBeUniqueUntilProcessing
    {
        // ...
    }

<a name="unique-job-locks"></a>
#### 고유 작업 잠금

배후에서 `ShouldBeUnique` 작업이 전달되면 라라벨은 `uniqueId` 키를 사용하여 [lock](/docs/{{version}}/cache#atomic-locks)을 획득하려고 시도합니다. 잠금이 획득되지 않으면 작업이 디스패치되지 않습니다. 작업이 처리를 완료하거나 모든 재시도에 실패하면 이 잠금이 해제됩니다. 기본적으로 라라벨은 이 잠금을 얻기 위해 기본 캐시 드라이버를 사용합니다. 그러나 잠금을 획득하기 위해 다른 드라이버를 사용하려면 사용해야 하는 캐시 드라이버를 반환하는 `uniqueVia` 메서드를 정의할 수 있습니다.

    use Illuminate\Support\Facades\Cache;

    class UpdateSearchIndex implements ShouldQueue, ShouldBeUnique
    {
        ...

        /**
         * Get the cache driver for the unique job lock.
         *
         * @return \Illuminate\Contracts\Cache\Repository
         */
        public function uniqueVia()
        {
            return Cache::driver('redis');
        }
    }

> {tip} 작업의 동시 처리만 제한해야 하는 경우 대신 [`WithoutOverlapping`](/docs/{{version}}/queues#preventing-job-overlaps) 작업 미들웨어를 사용하세요.

<a name="job-middleware">< /a>-
## 작업 미들웨어

Job 미들웨어를 사용하면 대기중인 Job 실행을 중심으로 커스텀 로직을 래핑하여 Job 자체의 중복코드를 줄일 수 있습니다. 예를 들어, 5 초마다 한 Job 만 처리 하기위해 라라벨의 Redis 속도 제한 기능을 활용하는 다음 `handle` 메소드를 생각해보십시오.

    use Illuminate\Support\Facades\Redis;

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

이 코드는 동작은 하지만, `handle` 메소드의 구현은 Redis 속도 제한 로직으로 지저분해집니다. 또한 이 속도 제한 로직은 속도를 제한하려는 다른 Job에 복제해야합니다.

핸들 메서드에서 속도 제한 대신 속도 제한을 처리하는 job 미들웨어를 정의할 수 있습니다. 라라벨에는 job 미들웨어의 기본 위치가 없으므로, 애플리케이션의 아무 곳에나 job 미들웨어를 배치할 수 있습니다. 이 예제에서는 미들웨어를 `app/Jobs/Middleware` 디렉토리에 배치합니다.

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

작업 미들웨어를 생성한 후 작업의 `middleware` 메서드에서 반환하여 job에 연결할 수 있습니다. 이 메소드는 `make:job` Artisan 명령으로 스캐폴딩된 작업에는 존재하지 않으므로, 수동으로 job 클래스에 추가해야 합니다.

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

> {tip} job 미들웨어는 큐에 넣을 수 있는 이벤트 리스너, 메일링, 알림에도 할당할 수 있습니다.

<a name="rate-limiting"></a>
### 속도 제한

속도 제한 작업 미들웨어를 작성하는 방법을 방금 시연했지만, 라라벨에는 실제로 속도 제한 작업에 사용할 수 있는 속도 제한 미들웨어가 포함되어 있습니다. [route rate limiters](/docs/{{version}}/routing#defining-rate-limiters)와 마찬가지로 작업 속도 제한기는 `RateLimiter` 파사드의 `for` 메서드를 사용하여 정의됩니다.

예를 들어 프리미엄 고객에게 이러한 제한을 걸지 않으면서, 일반 사용자는 시간당 한 번 데이터를 백업하도록 처리할 수 있습니다. 이를 위해 `AppServiceProvider`의 `boot` 메소드에서 `RateLimiter`를 정의할 수 있습니다.

    use Illuminate\Cache\RateLimiting\Limit;
    use Illuminate\Support\Facades\RateLimiter;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        RateLimiter::for('backups', function ($job) {
            return $job->user->vipCustomer()
                        ? Limit::none()
                        : Limit::perHour(1)->by($job->user->id);
        });
    }

위의 예에서는 시간당 비율 제한을 정의했습니다. 그러나 `perMinute` 메서드를 사용해서, 분을 기준으로 속도 제한을 쉽게 정의할 수 있습니다. 또한 속도 제한의 `by` 메서드에 원하는 값을 전달할 수 있습니다. 그러나 이 값은 고객별로 비율 제한을 분할하는 데 가장 자주 사용됩니다.

    return Limit::perMinute(50)->by($job->user->id);

속도 제한을 정의하면 `Illuminate\Queue\Middleware\RateLimited` 미들웨어를 사용하여 백업 작업에 속도 제한기를 연결할 수 있습니다. 작업이 속도 제한을 초과할 때마다 이 미들웨어는 속도 제한 기간에 따라 적절한 지연을 위해 작업을 다시 큐-queue로 보냅니다.

    use Illuminate\Queue\Middleware\RateLimited;

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [new RateLimited('backups')];
    }

비율이 제한된 작업을 다시 큐-queue로 보내도, 작업의 총 `attempts` 횟수는 계속 증가합니다. 그에 따라 작업 클래스에서 `tries` 및 `maxExceptions` 속성을 조정할 수 있습니다. 또는 [`retryUntil` 메서드](#time-based-attempts)를 사용하여 작업이 더 이상 시도되지 않아야 할 때까지의 시간을 정의할 수 있습니다.

작업이 제한된 비율로 재시도되는 것을 원하지 않는다면 `dontRelease` 메소드를 사용할 수 있습니다.

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [(new RateLimited('backups'))->dontRelease()];
    }

> {tip} Redis를 사용하신다면 `Illuminate\Queue\Middleware\RateLimitedWithRedis` 미들웨어를 사용하시면 됩니다. 이 미들웨어는 Redis에 맞게 미세 조정되어 있으며 기본 속도 제한 미들웨어보다 더 효율적입니다.

<a name="preventing-job-overlaps"></a>
### 작업 중복 방지

라라벨에는 임의의 키를 기반으로 작업이 겹치는 것을 방지할 수 있는 `Illuminate\Queue\Middleware\WithoutOverlapping` 미들웨어가 포함되어 있습니다. 이는 큐-queue에 있는 작업이 한 번에 하나의 작업에 의해서만 변경되어야 하는 리소스를 관리할 때 유용할 수 있습니다.

예를 들어, 사용자의 신용 점수를 업데이트하는 대기 job이 있고, 동일한 사용자 ID에 대해 신용 점수 업데이트 job이 겹치지 않도록 하려고 한다고 가정해 보겠습니다. 이를 수행하려면 작업의 `middleware` 메서드에서 `WithoutOverlapping` 미들웨어를 반환할 수 있습니다.

    use Illuminate\Queue\Middleware\WithoutOverlapping;

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [new WithoutOverlapping($this->user->id)];
    }

겹치는 작업은 다시 큐-queue로 보내집니다. 보내진 작업을 다시 시도하기 전에 경과해야 하는 시간(초)을 지정할 수도 있습니다.

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [(new WithoutOverlapping($this->order->id))->releaseAfter(60)];
    }

겹치는 작업을 즉시 삭제하여 재시도하지 않으려면 `dontRelease` 메서드를 사용할 수 있습니다.

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [(new WithoutOverlapping($this->order->id))->dontRelease()];
    }

`WithoutOverlapping` 미들웨어는 라라벨의 원자 잠금 기능을 이용해 구동됩니다. 때때로 작업이 예기치 않게 실패하거나 잠금이 해제되지 않는 방식으로 시간 초과될 수 있습니다. 따라서 `expireAfter` 메서드를 사용하여 잠금 만료 시간을 명시적으로 정의할 수 있습니다. 예를 들어, 아래 예제는 작업이 처리를 시작한 지 3분 후에 `WithoutOverlapping` 잠금을 해제하도록 라라벨에 지시합니다.

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [(new WithoutOverlapping($this->order->id))->expireAfter(180)];
    }

> {note} `WithoutOverlapping` 미들웨어는 [locks](/docs/{{version}}/cache#atomic-locks)를 지원하는 캐시 드라이버가 필요합니다. 현재 `memcached`, `redis`, `dynamodb`, `database`, `file` 및 `array` 캐시 드라이버는 원자 잠금을 지원합니다.

<a name="throttling-exceptions"></a>
### 작업량 초과 예외-Exception

라라벨에는 예외를 조절할 수 있는 `Illuminate\Queue\Middleware\ThrottlesExceptions` 미들웨어가 포함되어 있습니다. 작업에서 지정된 수의 예외가 발생하면 작업을 실행하려는 모든 추가 시도는 지정된 시간 간격이 경과할 때까지 지연됩니다. 이 미들웨어는 불안정한 타사 서비스와 상호 작용하는 작업에 특히 유용합니다.

예를 들어 예외를 발생시키기 시작하는 타사 API와 상호 작용하는 대기 중인 작업을 상상해 보겠습니다. 예외를 조절하려면 작업의 `middleware` 메서드에서 `ThrottlesExceptions` 미들웨어를 반환할 수 있습니다. 일반적으로 이 미들웨어는 [시간 기반 시도](#time-based-attempts)를 구현하는 작업과 쌍을 이루어야 합니다.

    use Illuminate\Queue\Middleware\ThrottlesExceptions;

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [new ThrottlesExceptions(10, 5)];
    }

    /**
     * Determine the time at which the job should timeout.
     *
     * @return \DateTime
     */
    public function retryUntil()
    {
        return now()->addMinutes(5);
    }

미들웨어의 생성자에서 입력받는 첫 번째 인수는 작업이 조절되기 전에 던질 수 있는 예외 수이고, 두 번째 생성자 인수는 작업이 조절된 후 다시 시도되기 전에 경과해야 하는 시간(분)입니다. 위의 코드 예제에서 작업에서 5분 이내에 10개의 예외가 발생하면 작업을 다시 시도하기 전에 5분을 기다립니다.

작업에서 예외가 발생했지만 예외 임계값에 아직 도달하지 않은 경우 작업은 일반적으로 즉시 재시도됩니다. 그러나 미들웨어를 작업에 연결할 때 `backoff` 메서드를 호출하여 이러한 작업이 지연되어야 하는 시간(분)을 지정할 수 있습니다.

    use Illuminate\Queue\Middleware\ThrottlesExceptions;

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [(new ThrottlesExceptions(10, 5))->backoff(5)];
    }

내부적으로 이 미들웨어는 비율 제한을 구현하기 위해 라라벨의 캐시 시스템을 사용하며 작업의 클래스 이름은 캐시 "키"로 활용됩니다. 미들웨어를 작업에 연결할 때 `by` 메서드를 호출하여 이 키를 재정의할 수 있습니다. 이는 동일한 타사 서비스와 상호 작용하는 여러 작업이 있고 공통 조절 "버킷"을 공유하려는 경우에 유용할 수 있습니다.

    use Illuminate\Queue\Middleware\ThrottlesExceptions;

    /**
     * Get the middleware the job should pass through.
     *
     * @return array
     */
    public function middleware()
    {
        return [(new ThrottlesExceptions(10, 10))->by('key')];
    }

> {tip} Redis를 사용하신다면 `Illuminate\Queue\Middleware\ThrottlesExceptionsWithRedis` 미들웨어를 사용하시면 됩니다. 이 미들웨어는 Redis에 맞게 미세 조정되고 기본 예외 조절 미들웨어보다 더 효율적입니다.

<a name="dispatching-jobs"></a>
## Job 처리하기

Job 클래스를 작성한 뒤에 클래스의 `dispatch` 메소드를 사용하여 이를 처리할 수 있습니다. `dispatch` 메소드에 전달할 인자는 job의 생성자입니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Jobs\ProcessPodcast;
    use App\Models\Podcast;
    use Illuminate\Http\Request;

    class PodcastController extends Controller
    {
        /**
         * Store a new podcast.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $podcast = Podcast::create(...);

            // ...

            ProcessPodcast::dispatch($podcast);
        }
    }

조건부 Job 처리를 원한다면, `dispatchIf`와 `dispatchUnless` 메소드를 사용할 수 있습니다.

    ProcessPodcast::dispatchIf($accountActive, $podcast);

    ProcessPodcast::dispatchUnless($accountSuspended, $podcast);

<a name="delayed-dispatching"></a>
### 지연시켜서 처리하기

큐-queue 작업자가 작업을 즉시 처리할 수 없도록 지정하려면 작업을 발송할 때 `delay` 메서드를 사용할 수 있습니다. 예를 들어 작업이 발송된 후 10분이 지나야 처리할 수 있다고 지정해 보겠습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Jobs\ProcessPodcast;
    use App\Models\Podcast;
    use Illuminate\Http\Request;

    class PodcastController extends Controller
    {
        /**
         * Store a new podcast.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $podcast = Podcast::create(...);

            // ...

            ProcessPodcast::dispatch($podcast)
                        ->delay(now()->addMinutes(10));
        }
    }

> {note} 아마존 SQS 큐 서비스는 지연시간이 최대 15분을 넘을 수 없습니다.

<a name="dispatching-after-the-response-is-sent-to-browser"></a>
#### 브라우저로 응답을 보낸 후 처리

또는 `dispatchAfterResponse` 메서드는 HTTP 응답이 사용자의 브라우저로 전송될 때까지 작업 처리를 지연시킵니다. 이렇게 하면 큐-queue에 있는 작업이 계속 실행 중이더라도 사용자가 애플리케이션을 사용할 수 있습니다. 이것은 일반적으로 이메일 보내기와 같이 1초 정도 걸리는 작업에만 사용해야 합니다. 현재 HTTP 요청 내에서 처리되기 때문에 이러한 방식으로 디스패치된 작업은 처리를 위해 큐-queue 작업자를 실행할 필요가 없습니다.

    use App\Jobs\SendNotification;

    SendNotification::dispatchAfterResponse();

또한 클로저를 `dispatch`하고 HTTP 응답이 브라우저로 전송된 후 클로저를 실행하기 위해 `afterResponse` 메소드를 `dispatch` 헬퍼에 연결할 수 있습니다.

    use App\Mail\WelcomeMessage;
    use Illuminate\Support\Facades\Mail;

    dispatch(function () {
        Mail::to('taylor@example.com')->send(new WelcomeMessage);
    })->afterResponse();

<a name="synchronous-dispatching"></a>
### 동기식 반환

작업을 즉시(동기식으로) 전달하려면 `dispatchSync` 메서드를 사용할 수 있습니다. 이 방법을 사용하면 작업이 큐-queue에 추가되지 않고 현재 프로세스 내에서 즉시 실행됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Jobs\ProcessPodcast;
    use App\Models\Podcast;
    use Illuminate\Http\Request;

    class PodcastController extends Controller
    {
        /**
         * Store a new podcast.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $podcast = Podcast::create(...);

            // Create podcast...

            ProcessPodcast::dispatchSync($podcast);
        }
    }

<a name="jobs-and-database-transactions"></a>
### 작업 및 데이터베이스 트랜잭션

데이터베이스 트랜잭션 내에서 작업을 디스패치하는 것은 좋은 방식이지만, 작업이 실제로 성공적으로 실행될 수 있도록 특별히 주의해야 합니다. 트랜잭션 내에서 작업을 디스패치할 때, 트랜잭션이 커밋되기 전에 작업자-wotker가 작업을 처리할 수 있습니다. 이 경우 데이터베이스 트랜잭션 중 모델 또는 데이터베이스 레코드에 대한 업데이트가 아직 데이터베이스에 반영되지 않았을 수 있습니다. 또한 트랜잭션 내에서 생성된 모델 또는 데이터베이스 레코드는 데이터베이스에 존재하지 않을 수 있습니다.

고맙게도 라라벨은 이 문제를 해결할 수 있는 몇 가지 방법을 제공합니다. 먼저 큐-queue 연결의 설정 배열에서 `after_commit` 연결 옵션을 설정할 수 있습니다.

    'redis' => [
        'driver' => 'redis',
        // ...
        'after_commit' => true,
    ],

`after_commit` 옵션이 `true`이면 데이터베이스 트랜잭션 내에서 작업을 디스패치할 수 있습니다. 그러나 라라벨은 실제로 작업을 디스패치하기 전에 열려 있는 모든 데이터베이스 트랜잭션이 커밋될 때까지 기다립니다. 물론 현재 열려 있는 데이터베이스 트랜잭션이 없으면 작업이 즉시 발송됩니다.

트랜잭션 중에 발생한 예외로 인해 트랜잭션이 롤백되면 해당 트랜잭션 중에 디스패치된 디스패치 작업은 버려집니다.

> {tip} `after_commit` 설정 옵션을 `true`로 설정하면 열려 있는 모든 데이터베이스 트랜잭션이 커밋된 후 대기 중인 이벤트 리스너, 메일 가능 항목, 알림 및 브로드캐스트 이벤트도 전달됩니다.

<a name="specifying-commit-dispatch-behavior-inline"></a>
#### 커밋 디스패치 동작 인라인 지정

`after_commit` 큐-queue 연결 설정 옵션을 `true`로 설정하지 않으면, 열려 있는 모든 데이터베이스 트랜잭션이 커밋된 후 특정 작업이 발송되어야 한다고 표시할 수 있습니다. 이를 수행하기 위해 `afterCommit` 메서드를 디스패치 작업에 연결할 수 있습니다.

    use App\Jobs\ProcessPodcast;

    ProcessPodcast::dispatch($podcast)->afterCommit();

마찬가지로, `after_commit` 설정 옵션이 `true`로 설정되어 있으면, 열려 있는 데이터베이스 트랜잭션이 커밋될 때까지 기다리지 않고 특정 작업이 즉시 발송되어야 함을 나타낼 수 있습니다.

    ProcessPodcast::dispatch($podcast)->beforeCommit();

<a name="job-chaining"></a>
### Job 체이닝

작업 체인을 사용하면 기본 작업이 성공적으로 실행된 후 순서대로 실행되어야 하는 대기 중인 작업 목록을 지정할 수 있습니다. 순서내의 한 작업이 실패하면 나머지 작업은 실행되지 않습니다. 큐-queue에 있는 작업 체인을 실행하려면 `bus` 파사드에서 제공하는 `chain` 메서드를 사용할 수 있습니다. 라라벨의 명령 버스는 큐 작업 디스패칭이 다음을 기반으로 구축되는 하위 수준 컴포넌트입니다.

    use App\Jobs\OptimizePodcast;
    use App\Jobs\ProcessPodcast;
    use App\Jobs\ReleasePodcast;
    use Illuminate\Support\Facades\Bus;

    Bus::chain([
        new ProcessPodcast,
        new OptimizePodcast,
        new ReleasePodcast,
    ])->dispatch();

작업 클래스 인스턴스를 연결하는 것 외에도 클로저를 연결할 수도 있습니다.

    Bus::chain([
        new ProcessPodcast,
        new OptimizePodcast,
        function () {
            Podcast::update(...);
        },
    ])->dispatch();

> {참고} 작업 내에서 `$this->delete()` 메서드를 사용하여 작업을 삭제해도 연결된 작업이 처리되지 않습니다. 체인은 체인의 작업이 실패하는 경우에만 실행을 중지합니다.

<a name="chain-connection-queue"></a>
#### Connection과 Queue 체이닝

연결된 작업에 사용해야 하는 연결 및 큐-queue을 지정하려면 `onConnection` 및 `onQueue` 메서드를 사용할 수 있습니다. 다음 메서드는 큐-queue에 있는 작업에 다른 연결 큐-queue이 명시적으로 할당되지 않는 한 사용해야 하는 큐-queue 연결 및 큐-queue 이름을 지정합니다.

    Bus::chain([
        new ProcessPodcast,
        new OptimizePodcast,
        new ReleasePodcast,
    ])->onConnection('redis')->onQueue('podcasts')->dispatch();

<a name="chain-failures"></a>
#### 체인 실패

작업을 연결할 때 `catch` 메서드를 사용하여 체인 내의 작업이 실패할 경우 호출되어야 하는 클로저를 지정할 수 있습니다. 주어진 콜백은 작업 실패를 일으킨 `Throwable` 인스턴스를 입력받습니다.

    use Illuminate\Support\Facades\Bus;
    use Throwable;

    Bus::chain([
        new ProcessPodcast,
        new OptimizePodcast,
        new ReleasePodcast,
    ])->catch(function (Throwable $e) {
        // A job within the chain has failed...
    })->dispatch();

<a name="customizing-the-queue-and-connection"></a>
### Queue-큐 & 커넥션 커스터마이징

<a name="dispatching-to-a-particular-queue"></a>
#### 특정 queue-큐로 처리하기

다른 queue-큐에 job 푸시를 통하여, queue job을 "분류"할 수 있으며, 다양한 queue에 다수의 worker를 지정하여 개별 우선순위를 지정할 수도 있습니다. 유념할 점은, 이것은 queue 설정 파일에 정의된 다른 "커넥션"에 job을 푸시하는 것이 아니라, 하나의 커넥션 안에서 queue-큐를 지정한다는 것입니다. queue-큐를 지정하려면 job 이 dispatching 될 때 `onQueue` 메소드를 사용하면 됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Jobs\ProcessPodcast;
    use App\Models\Podcast;
    use Illuminate\Http\Request;

    class PodcastController extends Controller
    {
        /**
         * Store a new podcast.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $podcast = Podcast::create(...);

            // Create podcast...

            ProcessPodcast::dispatch($podcast)->onQueue('processing');
        }
    }

또는 작업 생성자 내에서 `onQueue` 메서드를 호출하여 작업의 큐-queue을 지정할 수 있습니다.

    <?php

    namespace App\Jobs;

     use Illuminate\Bus\Queueable;
     use Illuminate\Contracts\Queue\ShouldQueue;
     use Illuminate\Foundation\Bus\Dispatchable;
     use Illuminate\Queue\InteractsWithQueue;
     use Illuminate\Queue\SerializesModels;

    class ProcessPodcast implements ShouldQueue
    {
        use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

        /**
         * Create a new job instance.
         *
         * @return void
         */
        public function __construct()
        {
            $this->onQueue('processing');
        }
    }

<a name="dispatching-to-a-particular-connection"></a>
#### 특정 커넥션으로 처리하기

애플리케이션이 여러 큐-queue 연결과 상호 작용하는 경우 `onConnection` 메서드를 사용하여 작업을 푸시할 연결을 지정할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Jobs\ProcessPodcast;
    use App\Models\Podcast;
    use Illuminate\Http\Request;

    class PodcastController extends Controller
    {
        /**
         * Store a new podcast.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $podcast = Podcast::create(...);

            // Create podcast...

            ProcessPodcast::dispatch($podcast)->onConnection('sqs');
        }
    }

`onConnection` 및 `onQueue` 메서드를 함께 연결하여 작업에 대한 연결 및 큐-queue을 지정할 수 있습니다.

    ProcessPodcast::dispatch($podcast)
                  ->onConnection('sqs')
                  ->onQueue('processing');

또는 작업의 생성자 내에서 `onConnection` 메서드를 호출하여 작업의 연결을 지정할 수 있습니다.

    <?php

    namespace App\Jobs;

     use Illuminate\Bus\Queueable;
     use Illuminate\Contracts\Queue\ShouldQueue;
     use Illuminate\Foundation\Bus\Dispatchable;
     use Illuminate\Queue\InteractsWithQueue;
     use Illuminate\Queue\SerializesModels;

    class ProcessPodcast implements ShouldQueue
    {
        use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

        /**
         * Create a new job instance.
         *
         * @return void
         */
        public function __construct()
        {
            $this->onConnection('sqs');
        }
    }

<a name="max-job-attempts-and-timeout"></a>
### 최대 재시도 횟수 / 타임아웃 시간 지정하기

<a name="max-attempts"></a>
#### 최대 재시도 횟수

대기 중인 작업 중 하나에 오류가 발생하는 경우 계속해서 재시도하는 것을 무기한으로 원하지 않을 수 있습니다. 따라서 라라벨은 작업을 시도할 수 있는 횟수 또는 기간을 지정하는 다양한 방법을 제공합니다.

작업을 시도할 수 있는 최대 횟수를 지정하는 한 가지 방법은 Artisan 명령줄의 `--tries` 스위치를 사용하는 것입니다. 이 옵션은 처리 중인 작업이 더 구체적인 시도 횟수를 지정하지 않는 한 worker가 처리하는 모든 작업에 적용됩니다.

    php artisan queue:work --tries=3

작업이 최대 시도 횟수를 초과하면 "실패한" 작업으로 간주됩니다. 실패한 작업 처리에 대한 자세한 내용은 [실패한 작업 문서](#dealing-with-failed-jobs)를 참조하십시오.

작업 클래스 내에서 작업을 시도할 수 있는 최대 횟수를 지정하면, 보다 세분화해서 관리할 수 있습니다. 작업에 최대 시도 횟수를 지정하면, 명령줄에 틍해 지정한 `--tries` 값보다 우선 적용됩니다.

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

작업이 실패하기 전에 시도할 수 있는 횟수를 정의하는 대신, 작업을 더 이상 시도하지 않아야 하는 시간을 정의할 수 있습니다. 이를 통해 주어진 시간 내에서 작업을 여러 번 시도할 수 있습니다. 작업을 더 이상 시도하지 않아야 하는 시간을 정의하려면, 작업 클래스에 `retryUntil` 메서드를 추가하세요. 이 메서드는 `DateTime` 인스턴스를 반환해야 합니다.

    /**
     * Determine the time at which the job should timeout.
     *
     * @return \DateTime
     */
    public function retryUntil()
    {
        return now()->addMinutes(10);
    }

> {tip} [대기 중인 이벤트 리스너](/docs/{{version}}/events#queued-event-listeners)에서 `tries` 속성이나 `retryUntil` 메서드를 정의할 수도 있습니다.

<a name="max-exceptions"></a>
#### 최대 예외

때때로 작업을 여러 번 시도할 수 있다고 지정하고 싶을 수 있지만, 지정한 수 보다 많은 (처리되지않은) 예외가 발생한다면 실패해야 할 경우가 있습니다(`release` 메서드에 의해 직접 해제되는 것과 반대). 이를 수행하기 위해 작업 클래스에 `maxExceptions` 속성을 정의할 수 있습니다.

    <?php

    namespace App\Jobs;

    use Illuminate\Support\Facades\Redis;

    class ProcessPodcast implements ShouldQueue
    {
        /**
         * The number of times the job may be attempted.
         *
         * @var int
         */
        public $tries = 25;

        /**
         * The maximum number of unhandled exceptions to allow before failing.
         *
         * @var int
         */
        public $maxExceptions = 3;

        /**
         * Execute the job.
         *
         * @return void
         */
        public function handle()
        {
            Redis::throttle('key')->allow(10)->every(60)->then(function () {
                // Lock obtained, process the podcast...
            }, function () {
                // Unable to obtain lock...
                return $this->release(10);
            });
        }
    }

이 예제에서, 애플리케이션이 Redis lock을 확보 할 수없는 경우 job은 10초 동안 허용되며 최대 25회 재시도됩니다. 그러나 처리되지 않은 예외가 3회 발생하면 작업이 실패합니다.

<a name="timeout"></a>
#### 타임아웃

> {note} 작업 시간 제한을 지정하려면 `pcntl` PHP 확장기능을 설치해야합니다

대기 중인 작업에 소요되는 시간을 대략적으로 알고 있는 경우가 많습니다. 이러한 이유로 라라벨에서는 "timeout" 값을 지정할 수 있습니다. 작업이 시간 초과 값으로 지정된 시간(초)보다 오래 처리되는 경우, 작업을 처리하는 작업자는 오류와 함께 종료됩니다. 일반적으로 작업자는 [서버에 설정된 프로세스 관리자](#supervisor-configuration)에 의해 자동으로 다시 시작됩니다.

작업을 실행할 수 있는 최대 시간(초)은 Artisan 명령줄의 `--timeout` 스위치를 사용하여 지정할 수 있습니다.

    php artisan queue:work --timeout=30

작업이 계속 시간 초과되어 최대 시도 횟수를 초과하면, 실패한 것으로 표시됩니다.

작업 클래스 자체에서 작업을 실행할 수 있는 최대 시간(초)을 정의할 수도 있습니다. 작업에 시간 초과가 지정되면 명령줄에 지정된 시간 초과보다 우선 적용됩니다.

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

때로는 소켓이나 나가는 HTTP 연결과 같은 IO 차단 프로세스가 지정된 시간 제한을 따르지 않을 수 있습니다. 따라서 이러한 기능을 사용할 때 항상 해당 API를 사용하여 시간 제한을 지정해야합니다. 예를 들어 Guzzle을 사용할 때는 항상 연결 및 요청 시간 제한 값을 지정해야합니다.

<a name="failing-on-timeout"></a>
#### 시간 초과 시 실패

작업이 시간 초과 시 [실패](#dealing-with-failed-jobs)로 표시하고싶다면, 작업 클래스에서 `failOnTimeout` 속성을 정의할 수 있습니다.

```php
/**
 * Indicate if the job should be marked as failed on timeout.
 *
 * @var bool
 */
public $failOnTimeout = true;
```

<a name="error-handling"></a>
### 에러 핸들링

job이 처리되는 동안에 exception이 발생하면, job을 다시 시도하기 위하여 자동으로 queue로 반환됩니다. job은 애플리케이션에서 정의된 최대 재시도 횟수만큼 계속해서 실행됩니다. 재시도 횟수는 `queue:work` 아티즌 명령어를 사용할 때 `--tries` 옵션을 사용하여 정의됩니다. 재시도 횟수를 job클래스 자체에 정의할 수도 있습니다. queue worker에 대한 보다 자세한 사항은 [다음에서 찾을 수 있습니다](#running-the-queue-worker).

<a name="manually-releasing-a-job"></a>
#### 수동으로 작업 해제

때로는 나중에 다시 시도할 수 있도록 수동으로 작업을 큐-queue로 다시 해제하고자 할 수 있습니다. `release` 메서드를 호출하여 이를 처리할 수 있습니다.

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        // ...

        $this->release();
    }

기본적으로 `release` 메서드는 즉각적인 처리를 위해 작업을 다시 큐-queue로 돌려보냅니다. 그러나 `release` 메소드에 정수를 전달하면 주어진 시간(초)이 경과할 때까지 작업을 처리할 수 없도록 큐-queue에 지시할 수 있습니다.

    $this->release(10);

<a name="manually-failing-a-job"></a>
#### 수동으로 작업 실패

때로는 수동으로 작업을 "실패"로 표시해야 할 수도 있습니다. 그럴려면 `fail` 메소드를 호출하면 됩니다.

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        // ...

        $this->fail();
    }

예외 때문에 작업을 실패한 것으로 표시하려면, catch를 통해 잡은 예외를 `fail` 메서드에 전달하면 됩니다.

    $this->fail($exception);

> {tip} 실패한 작업에 대한 자세한 내용은 [작업 실패 처리 문서](#dealing-with-failed-jobs)를 확인하세요.

<a name="job-batching"></a>
## Job 배치(동일 프로그램에서 일괄 처리되는 작업 단위)

라라벨의 배치-batch 기능을 사용하면, 작업 일괄 처리를 쉽게 실행한 다음, 일괄 작업 실행이 완료되면 일부 작업을 처리할 수 있습니다. 시작하기 전에 완료율과 같은 작업 배치에 대한 메타 정보를 포함하는 테이블을 작성하기 위해 데이터베이스 마이그레이션을 생성해야 합니다. 이 마이그레이션은 `queue:batches-table` Artisan 명령을 사용하여 생성할 수 있습니다.

    php artisan queue:batches-table

    php artisan migrate

<a name="defining-batchable-jobs"></a>
### 일괄 처리 가능한 작업 정의

일괄 처리 가능한 작업을 정의하려면 평소와 같이 [큐-queue 가능한 작업을 생성](#creating-jobs)해야 합니다. 그러나 작업 클래스에 `Illuminate\Bus\Batchable` 특성-trait을 추가해야 합니다. 이 특성은 작업이 실행 중인 현재 배치를 검색하는 데 사용할 수 있는 `batch` 메서드에 대한 접근을 제공합니다.

    <?php

    namespace App\Jobs;

    use Illuminate\Bus\Batchable;
    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Foundation\Bus\Dispatchable;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Queue\SerializesModels;

    class ImportCsv implements ShouldQueue
    {
        use Batchable, Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

        /**
         * Execute the job.
         *
         * @return void
         */
        public function handle()
        {
            if ($this->batch()->cancelled()) {
                // Determine if the batch has been cancelled...

                return;
            }

            // Import a portion of the CSV file...
        }
    }

<a name="dispatching-batches"></a>
### 일괄 작업 실행하기

일괄 작업을 실행하려면 `Bus` 파사드의 `batch` 메소드를 사용해야 합니다. 물론 일괄 처리는 완료 콜백과 결합될 때 주로 유용합니다. 따라서 `then`, `catch` 및 `finally` 메서드를 사용하여 배치에 대한 완료 콜백을 정의할 수 있습니다. 이러한 각 콜백은 호출될 때 `Illuminate\Bus\Batch` 인스턴스를 받습니다. 이 예제에서 우리는 CSV 파일에서 주어진 행 수를 각각 처리하는 작업 배치를 큐-queue에 넣고 있다고 상상할 것입니다.

    use App\Jobs\ImportCsv;
    use Illuminate\Bus\Batch;
    use Illuminate\Support\Facades\Bus;
    use Throwable;

    $batch = Bus::batch([
        new ImportCsv(1, 100),
        new ImportCsv(101, 200),
        new ImportCsv(201, 300),
        new ImportCsv(301, 400),
        new ImportCsv(401, 500),
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->catch(function (Batch $batch, Throwable $e) {
        // First batch job failure detected...
    })->finally(function (Batch $batch) {
        // The batch has finished executing...
    })->dispatch();

    return $batch->id;

`$batch->id` 속성을 통해 액세스할 수 있는 배치의 ID는 배치가 발송된 후 배치에 대한 정보를 얻기 위해 [라라벨 명령 버스 쿼리](#inspecting-batches)에 사용될 수 있습니다.

> {note} 일괄 콜백은 나중에 라라벨 큐에 의해 직렬화되어 실행되기 때문에 콜백 내에서 `$this` 변수를 사용해서는 안됩니다.

<a name="naming-batches"></a>
#### 배치 이름 지정

라라벨 Horizon 및 라라벨 Telescope와 같은 일부 도구들은 배치 이름이 지정된 경우 배치에 대해 보다 사용자 친화적인 debug 정보를 제공합니다. 배치에 임의의 이름을 지정하려면, 배치를 정의하는 동안 `name` 메소드를 호출해야 합니다.

    $batch = Bus::batch([
        // ...
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->name('Import CSV')->dispatch();

<a name="batch-connection-queue"></a>
#### Connection 및 Queue 배치

일괄 작업에 사용해야 하는 연결 및 큐-queue을 지정하려면 `onConnection` 및 `onQueue` 메서드를 사용할 수 있습니다. 모든 일괄 작업은 동일한 연결 및 큐-queue 내에서 실행되어야 합니다.

    $batch = Bus::batch([
        // ...
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->onConnection('redis')->onQueue('imports')->dispatch();

<a name="chains-within-batches"></a>
#### 배치 내 체인

배열 내에 연결된 작업을 배치하여 배치 내에서 일련의 [연결된 작업](#job-chaining)을 정의할 수 있습니다. 예를 들어, 두 개의 작업 체인을 병렬로 실행하고 두 작업 체인이 모두 처리를 완료하면 콜백을 실행할 수 있습니다.

    use App\Jobs\ReleasePodcast;
    use App\Jobs\SendPodcastReleaseNotification;
    use Illuminate\Bus\Batch;
    use Illuminate\Support\Facades\Bus;

    Bus::batch([
        [
            new ReleasePodcast(1),
            new SendPodcastReleaseNotification(1),
        ],
        [
            new ReleasePodcast(2),
            new SendPodcastReleaseNotification(2),
        ],
    ])->then(function (Batch $batch) {
        // ...
    })->dispatch();

<a name="adding-jobs-to-batches"></a>
### 배치에 Job 추가

경우에 따라 일괄 작업 내에서 일괄 작업을 추가하는 것이 유용할 수 있습니다. 이 패턴은 웹 요청 중에 발송하는 데 너무 오래 걸릴 수 있는 수천 개의 작업을 일괄 처리해야 할 때 유용할 수 있습니다. 따라서 대신에 더 많은 작업으로 배치를 분할해서 실행하는 "로더" 작업의 초기 배치를 실행할 수 있습니다.

    $batch = Bus::batch([
        new LoadImportBatch,
        new LoadImportBatch,
        new LoadImportBatch,
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->name('Import Contacts')->dispatch();

이 예제에서는 `LoadImportBatch` 작업을 사용하여 추가 작업으로 배치를 만들어냅니다. 이를 수행하기 위해 작업의 `batch` 메서드를 통해 액세스할 수 있는 배치 인스턴스에서 `add` 메서드를 사용할 수 있습니다.

    use App\Jobs\ImportContacts;
    use Illuminate\Support\Collection;

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        if ($this->batch()->cancelled()) {
            return;
        }

        $this->batch()->add(Collection::times(1000, function () {
            return new ImportContacts;
        }));
    }

> {note} 동일한 배치에 속하는 job 내에서만 배치에 job들을 추가할 수 있습니다.

<a name="inspecting-batches"></a>
### 배치 검사

일괄 완료 콜백에 제공되는 `Illuminate\Bus\Batch` 인스턴스에는 지정된 작업 배치와 상호 작용하고 검사하는 데 도움이 되는 다양한 속성과 메서드가 있습니다.

    // The UUID of the batch...
    $batch->id;

    // The name of the batch (if applicable)...
    $batch->name;

    // The number of jobs assigned to the batch...
    $batch->totalJobs;

    // The number of jobs that have not been processed by the queue...
    $batch->pendingJobs;

    // The number of jobs that have failed...
    $batch->failedJobs;

    // The number of jobs that have been processed thus far...
    $batch->processedJobs();

    // The completion percentage of the batch (0-100)...
    $batch->progress();

    // Indicates if the batch has finished executing...
    $batch->finished();

    // Cancel the execution of the batch...
    $batch->cancel();

    // Indicates if the batch has been cancelled...
    $batch->cancelled();

<a name="returning-batches-from-routes"></a>
#### 라우트에서 배치 반환

모든 `Illuminate\Bus\Batch` 인스턴스는 JSON으로 직렬화할 수 있습니다. 즉, 완료 진행 상황을 포함하여 배치에 대한 정보가 포함된 JSON 페이로드를 검색하기 위해, 애플리케이션 경로 중 하나에서 직접 반환할 수 있습니다. 이렇게 하면 애플리케이션 UI에서 일괄 처리 완료 진행률에 대한 정보를 편리하게 표시할 수 있습니다.

ID로 배치를 검색하려면 `Bus` 파사드의 `findBatch` 메소드를 사용할 수 있습니다.

    use Illuminate\Support\Facades\Bus;
    use Illuminate\Support\Facades\Route;

    Route::get('/batch/{batchId}', function (string $batchId) {
        return Bus::findBatch($batchId);
    });

<a name="cancelling-batches"></a>
### 배치 취소

때로 지정된 배치를 취소해야 할 수도 있습니다. 이는 `Illuminate\Bus\Batch` instance에서 `cancel` 메소드를 호출하여 처리할 수 있습니다.

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        if ($this->user->exceedsImportLimit()) {
            return $this->batch()->cancel();
        }

        if ($this->batch()->cancelled()) {
            return;
        }
    }

이전 예제에서 알 수 있듯이, 일괄 처리 작업은 일반적으로 `handle` 메서드의 시작 부분에서 일괄 처리가 취소되었는지 확인해야 합니다.

    /**
     * Execute the job.
     *
     * @return void
     */
    public function handle()
    {
        if ($this->batch()->cancelled()) {
            return;
        }

        // Continue processing...
    }

<a name="batch-failures"></a>
### 배치 실패

일괄 작업이 실패하면 `catch` 콜백(할당된 경우)이 호출됩니다. 이 콜백은 배치 내에서 실패한 첫 번째 작업에 대해서만 호출됩니다.

<a name="allowing-failures"></a>
#### 실패 허용

배치 내의 job이 실패하면 라라벨은 자동으로 배치를 "취소됨"으로 표시합니다. 당신이 원할 경우, job 실패로 인해 배치가 취소 되게 자동으로 표시되지 않도록 이 동작을 비활성화할 수 있습니다. 이는 배치를 dispatch하는 동안 `allowFailures` 메소드를 호출하여 수행합니다.

    $batch = Bus::batch([
        // ...
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->allowFailures()->dispatch();

<a name="retrying-failed-batch-jobs"></a>
#### 실패한 job 배치를 재시도

편의상, 라라벨은 주어진 배치에 대하여 실패한 모든 job들을 쉽게 재시도 할 수 있는 `queue:retry-batch` 아티즌 명령어를 제공합니다. `queue:retry-batch` 명령어는 재시도 해야 하는 실패한 job들의 배치의 UUID를 허용합니다.

```bash
php artisan queue:retry-batch 32dbc76c-4f82-4749-b610-a639fe0099b5
```

<a name="pruning-batches"></a>
### 배치 정리하기

정리를 해주지 않는다면, `job_batches` 테이블은 레코드가 매우 빠르게 누적될 수 있습니다. 이를 완화하려면 `queue:prune-batches` Artisan 명령이 매일 실행되도록 [일정](/docs/{{version}}/scheduling)해야 합니다.

    $schedule->command('queue:prune-batches')->daily();

기본적으로 24시간이 지난 완료된 모든 배치는 정리됩니다. 명령을 호출할 때 `hour` 옵션을 사용하여 배치 데이터를 유지할 기간을 결정할 수 있습니다. 예를 들어 다음 명령은 48시간 전에 완료된 모든 배치를 삭제합니다.

    $schedule->command('queue:prune-batches --hours=48')->daily();

때때로 작업이 실패하고 해당 작업이 성공적으로 재시도되지 않은 배치와 같이, 성공적으로 완료되지 않은 배치에 대해 `jobs_batches` 테이블에 해당 배치 레코드를 계속해서 저장할 수 있습니다. `unfinished` 옵션을 사용하여 이러한 완료되지 않은 배치 레코드를 정리하도록 `queue:prune-batches` 명령에 지시할 수 있습니다.

    $schedule->command('queue:prune-batches --hours=48 --unfinished=72')->daily();

<a name="queueing-closures"></a>
## 큐잉 클로저

작업 클래스를 큐-queue에 전달하는 대신 클로저를 전달할 수도 있습니다. 이는 현재 요청 주기 외부에서 실행해야 하는 빠르고 간단한 작업에 적합합니다. 큐에 클로저를 디스패치할 때 클로저의 코드 내용은 암호화로 서명되어 전송 중에 수정할 수 없습니다.

    $podcast = App\Podcast::find(1);

    dispatch(function () use ($podcast) {
        $podcast->publish();
    });

`catch` 메소드를 사용하여 큐의 [설정된 재시도 시도횟수](#max-job-attempts-and-timeout)를 모두 사용한 후에도 큐에 있는 클로저가 성공적으로 완료되지 않으면 실행되어야 하는 클로저를 제공할 수 있습니다.

    use Throwable;

    dispatch(function () use ($podcast) {
        $podcast->publish();
    })->catch(function (Throwable $e) {
        // This job has failed...
    });

<a name="running-the-queue-worker"></a>
## Queue-큐 worker 실행하기

<a name="the-queue-work-command"></a>
### `queue:work` 커맨드

라라벨에는 큐-queue 작업자를 시작하고 큐-queue에 푸시될 때 새 작업을 처리하는 Artisan 명령이 포함되어 있습니다. `queue:work` Artisan 명령을 사용하여 작업자를 실행할 수 있습니다. `queue:work` 명령이 시작되면 수동으로 중지하거나 터미널을 닫을 때까지 계속 실행됩니다.

    php artisan queue:work

> {tip} `queue:work` 프로세스를 백그라운드에서 계속 지속되게 하려면, queue worker가 중단되지 않는 것을 보장하기 위해 [Supervisor](#supervisor-configuration)와 같은 프로세스 모니터를 사용해야 합니다.

큐-queue 작업자는 수명이 긴 프로세스이며, 부팅된 애플리케이션 상태를 메모리에 저장합니다. 결과적으로 그들은 시작된 후에 코드 기반의 변경 사항을 알아차리지 못할 것입니다. 따라서 배포 프로세스 중에 [큐-queue 작업자를 다시 시작](#queue-workers-and-deployment)해야 합니다. 또한 애플리케이션에서 생성하거나 수정한 정적 상태는 작업 간에 자동으로 재설정되지 않습니다.

또는 `queue:listen` 명령을 실행할 수 있습니다. `queue:listen` 명령을 사용할 때 업데이트된 코드를 다시 로드하거나 애플리케이션 상태를 재설정하려는 경우 작업자를 수동으로 다시 시작할 필요가 없습니다. 그러나 이 명령은 `queue:work` 명령보다 훨씬 비효율적입니다.

    php artisan queue:listen

<a name="running-multiple-queue-workers"></a>
#### 여러 큐-queue 작업자 실행

여러 작업자를 큐-queue에 할당하고 동시에 작업을 처리하려면 여러 `queue:work` 프로세스를 시작하기만 하면 됩니다. 이것은 터미널의 여러 탭을 통해 로컬로 수행하거나 프로세스 관리자의 설정을 사용하여 프로덕션에서 처리할 수 있습니다. [Supervisor 사용 시](#supervisor-configuration) `numprocs` 설정 값을 사용할 수 있습니다.

<a name="specifying-the-connection-queue"></a>
#### 커넥션 & queue-큐 지정하기

어떤 queue-큐 커넥션을 worker가 사용할지 지정할 수도 있습니다. `work` 명령어에 전달된 커넥션 이름은 `config/queue.php` 설정 파일에 정의되어 있는 커넥션 이름과 일치해야 합니다.

    php artisan queue:work redis

기본적으로 `queue:work` 명령은 지정된 연결의 기본 큐-queue에 대한 작업만 처리합니다. 그러나 주어진 연결에 대한 특정 큐-queue만 처리하여 큐-queue 작업자를 추가로 사용자 지정할 수 있습니다. 예를 들어 모든 이메일이 `redis` 큐-queue 연결의 `emails` 큐-queue에서 처리되는 경우 다음 명령을 실행하여 해당 큐-queue만 처리하는 작업자를 시작할 수 있습니다.

    php artisan queue:work redis --queue=emails

<a name="processing-a-specified-number-of-jobs"></a>
#### 지정된 수의 Job 처리하기

`--once` 옵션은 worker가 queue로 부터 하나의 단일 job을 처리하도록 합니다.

    php artisan queue:work --once

`--max-jobs` 옵션은 작업자에게 주어진 수의 작업을 처리한 다음 종료하도록 지시하는 데 사용할 수 있습니다. 이 옵션은 [Supervisor](#supervisor-configuration)와 결합하여 작업자가 주어진 수의 작업을 처리한 후 자동으로 다시 시작하여 누적되었을 수 있는 메모리를 해제할 때 유용할 수 있습니다.

    php artisan queue:work --max-jobs=1000

<a name="processing-all-queued-jobs-then-exiting"></a>
#### 대기중인 모든 Job 처리 및 종료

`--stop-when-empty` 옵션은 작업자에게 모든 작업을 처리한 다음 정상적으로 종료하도록 지시하는 데 사용할 수 있습니다. 이 옵션은 큐가 비어 있는 후 컨테이너를 종료하려는 경우 Docker 컨테이너 내에서 라라벨 큐를 처리할 때 유용할 수 있습니다.

    php artisan queue:work --stop-when-empty

<a name="processing-jobs-for-a-given-number-of-seconds"></a>
#### 주어진 시간 동안 Job 처리

`--max-time` 옵션은 작업자에게 주어진 시간(초) 동안 작업을 처리한 다음 종료하도록 지시하는 데 사용할 수 있습니다. 이 옵션은 [Supervisor](#supervisor-configuration)와 함께 사용하면 작업자가 주어진 시간 동안 작업을 처리한 후 자동으로 다시 시작하여 축적된 메모리를 해제하는 데 유용할 수 있습니다.

    // Process jobs for one hour and then exit...
    php artisan queue:work --max-time=3600

<a name="worker-sleep-duration"></a>
#### 작업자 수면 시간

큐-queue에서 작업을 사용할 수 있는 경우 작업자는 작업 사이에 지연 없이 작업을 계속 처리합니다. 그러나 `sleep` 옵션은 사용 가능한 새 작업이 없는 경우 작업자가 "잠자기" 시간(초)을 결정합니다. 잠자는 동안 작업자는 새 작업을 처리하지 않습니다. 작업자가 다시 깨어난 후에 작업이 처리됩니다.

    php artisan queue:work --sleep=3

<a name="resource-considerations"></a>
#### 리소스 고려사항

데몬 큐 작업자는 각 작업을 처리하기 전에 프레임워크를 "재부팅"하지 않습니다. 따라서 각 작업이 완료된 후 무거운 리소스를 해제해야 합니다. 예를 들어, GD 라이브러리로 이미지 조작을 하는 경우 이미지 처리가 완료되면 `imagedestroy`로 메모리를 해제해야 합니다.

<a name="queue-priorities"></a>
### queue의 우선순위

때로는 큐-queue 처리 방법의 우선 순위를 정하고 싶을 수 있습니다. 예를 들어 `config/queue.php` 설정 파일에서 `redis` 연결에 대한 기본 `queue`를 `low`로 설정할 수 있습니다. 그러나 때때로 다음과 같이 작업을 `high` 우선 순위 큐-queue로 푸시하고 싶을 수 있습니다.

    dispatch((new Job)->onQueue('high'));

job 이 `low` queue로 처리되기 전에, `high` queue job으로 처리되는 것을 명확히 하여 worker가 시작되게 하려면, 콤마로 구분된 queue 이름의 목록을 `work` 명령어에 전달하면 됩니다.

    php artisan queue:work --queue=high,low

<a name="queue-workers-and-deployment"></a>
### Queue worker & 배포

큐-queue 작업자는 수명이 긴 프로세스이므로 다시 시작하지 않고는 코드 변경 사항을 알아차리지 못합니다. 따라서 큐-queue 작업자를 사용하여 애플리케이션을 배포하는 가장 간단한 방법은 배포 프로세스 중에 작업자를 다시 시작하는 것입니다. `queue:restart` 명령을 실행하여 모든 작업자를 정상적으로 다시 시작할 수 있습니다.

    php artisan queue:restart

이 명령은 기존 작업이 손실되지 않도록 모든 큐-queue 작업자가 현재 작업 처리를 마친 후 정상적으로 종료하도록 지시합니다. 큐 워커는 `queue:restart` 명령이 실행되면 종료되므로 [Supervisor](#supervisor-configuration)와 같은 프로세스 관리자를 실행하여 큐 워커를 자동으로 재시작해야 합니다.

> {tip} 큐-queue은 [cache](/docs/{{version}}/cache)를 사용하여 재시작 신호를 저장하므로 이 기능을 사용하기 전에 캐시 드라이버가 애플리케이션에 대해 올바르게 설정되었는지 확인해야 합니다.

<a name="job-expirations-and-timeouts"></a>
### Job 만료 & 타임아웃

<a name="job-expiration"></a>
#### Job 만료

`config/queue.php` 설정 파일에서 각 큐-queue 연결은 `retry_after` 옵션을 정의합니다. 이 옵션은 처리 중인 작업을 재시도하기 전에 큐-queue 연결이 대기해야 하는 시간(초)을 지정합니다. 예를 들어 `retry_after` 값이 `90`으로 설정된 경우 작업이 해제되거나 삭제되지 않고 90초 동안 처리된 경우 작업이 큐-queue로 다시 해제됩니다. 일반적으로 `retry_after` 값을 작업이 처리를 완료하는 데 합리적으로 걸리는 최대 시간(초)으로 설정해야 합니다.

> {note} 아마존 SQS에서는 `retry_after` 값은 포함되어 있지 않습니다. SQS는 AWS 콘솔 안에서 관리되는 [Default Visibility Timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/AboutVT.html) 에 의해서 job이 재시작됩니다.

<a name="worker-timeouts"></a>
#### worker 타임아웃

`queue:work` Artisan 명령은 `--timeout` 옵션을 표시합니다. 작업이 시간 초과 값으로 지정된 시간(초)보다 오래 처리되는 경우 작업을 처리하는 작업자는 오류와 함께 종료됩니다. 일반적으로 작업자는 [서버에 설정된 프로세스 관리자](#supervisor-configuration)에 의해 자동으로 다시 시작됩니다.

```bash
php artisan queue:work --timeout=60
```

`retry_after` 설정 옵션과 `--timeout` CLI 옵션은 서로 다릅니다. 하지만 job이 한번에 성공적으로 처리될 수 있도록 하는데 함께 작동합니다.

> {note} `--timeout` 값은 항상 `retry_after` 설정 값보다 몇 초 이상 짧아야 합니다. 이렇게 하면 고정된 작업을 처리하는 작업자가 작업을 재시도하기 전에 항상 종료됩니다. `--timeout` 옵션이 `retry_after` 설정 값보다 길면 작업이 두 번 처리될 수 있습니다.

<a name="supervisor-configuration"></a>
## Supervisor 설정하기

프로덕션에서는 `queue:work` 프로세스를 계속 실행하는 방법이 필요합니다. `queue:work` 프로세스는 작업자 시간 초과 초과 또는 `queue:restart` 명령 실행과 같은 다양한 이유로 실행을 중지될 수 있습니다.

이러한 이유로 `queue:work` 프로세스가 종료되고 자동으로 다시 시작될 때를 감지할 수 있는 프로세스 모니터를 설정해야 합니다. 또한 프로세스 모니터를 사용하면 동시에 실행할 `queue:work` 프로세스 수를 지정할 수 있습니다. Supervisor는 Linux 환경에서 일반적으로 사용되는 프로세스 모니터이며 다음 문서에서 이를 설정하는 방법에 대해 설명합니다.

<a name="installing-supervisor"></a>
#### Supervisor 설치하기

Supervisor는 Linux 운영 체제용 프로세스 모니터이며, `queue:work` 프로세스가 실패하면 자동으로 다시 시작합니다. Ubuntu에 Supervisor를 설치하려면 다음 명령을 사용할 수 있습니다.

    sudo apt-get install supervisor

> {tip} Supervisor를 직접 설정하고 관리하는 것이 너무 어렵다면 [Laravel Forge](https://forge.laravel.com)를 사용해 보세요. 그러면 프로덕션 라라벨 프로젝트에 Supervisor가 자동으로 설치되고 설정됩니다.

<a name="configuring-supervisor"></a>
#### Supervisor 설정하기

Supervisor 설정 파일은 일반적으로 `/etc/supervisor/conf.d` 디렉토리에 저장됩니다. 이 디렉토리 내에서 감독자에게 프로세스 모니터링 방법을 정의하는 설정 파일을 원하는 만큼 생성할 수 있습니다. 예를 들어 `queue:work` 프로세스를 시작하고 모니터링하는 `laravel-worker.conf` 파일을 생성해 보겠습니다.

```ini
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /home/forge/app.com/artisan queue:work sqs --sleep=3 --tries=3 --max-time=3600
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=forge
numprocs=8
redirect_stderr=true
stdout_logfile=/home/forge/app.com/worker.log
stopwaitsecs=3600
```

이 예에서 `numprocs` 지시문은 Supervisor에게 8개의 `queue:work` 프로세스를 실행하고 모든 프로세스를 모니터링하도록 지시하며, 실패하면 자동으로 다시 시작합니다. 원하는 큐-queue 연결 및 작업자 옵션을 반영하도록 설정의 command 지시문을 변경해야 합니다.

> {note} `stopwaitsecs`의 값이 가장 긴 실행 작업에서 소비하는 시간(초)보다 큰지 확인해야합니다. 그렇지 않으면 supervisor가 작업을 처리를 완료하기 전에 종료 할 수 있습니다.

<a name="starting-supervisor"></a>
#### Supervisor 시작하기

설정 파일을 생성하고 나면, 다음 명령어를 통해서 Supervisor 설정을 업데이트 하고, 시작할 수 있습니다.

```bash
sudo supervisorctl reread

sudo supervisorctl update

sudo supervisorctl start laravel-worker:*
```

Supervisor에 대한 보다 자세한 정보는 [Supervisor 문서](http://supervisord.org/index.html) 를 참고하십시오.

<a name="dealing-with-failed-jobs"></a>
## 실패한 Job 처리하기

대기 중인 작업이 실패하는 경우가 있습니다. 걱정하지 마십시오. 원래 일이 항상 계획대로 진행되지는 않습니다! 라라벨에는 [작업을 시도해야 하는 최대 횟수를 지정](#max-job-attempts-and-timeout)하는 편리한 방법이 포함되어 있습니다. 작업이 이 시도 횟수를 초과하면 `failed_jobs` 데이터베이스 테이블에 삽입됩니다. 물론 해당 테이블이 아직 없는 경우 생성해야 합니다. `failed_jobs` 테이블에 대한 마이그레이션을 생성하려면 `queue:failed-table` 명령을 사용할 수 있습니다.

    php artisan queue:failed-table

    php artisan migrate

[큐-queue 작업자](#running-the-queue-worker) 프로세스를 실행할 때 `queue:work` 명령의 `--tries` 스위치를 사용하여 작업을 시도해야 하는 최대 횟수를 지정할 수 있습니다. `--tries` 옵션에 대한 값을 지정하지 않으면 작업은 한 번만 또는 작업 클래스의 `$tries` 속성에 지정된 횟수만큼 시도됩니다.

    php artisan queue:work redis --tries=3

`--backoff` 옵션을 사용하여 예외가 발생한 작업을 재시도하기 전에 라라벨이 몇 초 동안 기다려야 하는지 지정할 수 있습니다. 기본적으로 작업은 즉시 다시 큐-queue로 보내진 후 재시도하게 됩니다.

    php artisan queue:work redis --tries=3 --backoff=3

작업별로 예외가 발생한 작업을 재시도하기 전에 라라벨이 대기해야 하는 시간(초)을 설정하려면 작업 클래스에 `backoff` 속성을 정의하면 됩니다.

    /**
     * The number of seconds to wait before retrying the job.
     *
     * @var int
     */
    public $backoff = 3;

작업의 백오프 시간을 결정하기 위해 더 복잡한 논리가 필요한 경우 작업 클래스에서 `backoff` 메서드를 정의할 수 있습니다.

    /**
    * Calculate the number of seconds to wait before retrying the job.
    *
    * @return int
    */
    public function backoff()
    {
        return 3;
    }

`backoff` 메소드에서 백오프 값의 배열을 반환하여 "지수-exponential" 백오프를 쉽게 설정할 수 있습니다. 이 예에서 재시도 지연은 첫 번째 재시도의 경우 1초, 두 번째 재시도의 경우 5초, 세 번째 재시도의 경우 10초입니다.

    /**
    * Calculate the number of seconds to wait before retrying the job.
    *
    * @return array
    */
    public function backoff()
    {
        return [1, 5, 10];
    }

<a name="cleaning-up-after-failed-jobs"></a>
### 실패한 Job 정리하기

특정 작업이 실패하면 사용자에게 경고를 보내거나 작업에 의해 부분적으로 완료된 작업을 되돌릴 수 있습니다. 이를 수행하기 위해 작업 클래스에 `failed` 메서드를 정의할 수 있습니다. 작업 실패의 원인이 된 `Throwable` 인스턴스는 `failed` 메서드로 전달됩니다.

    <?php

    namespace App\Jobs;

    use App\Models\Podcast;
    use App\Services\AudioProcessor;
    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Queue\SerializesModels;
    use Throwable;

    class ProcessPodcast implements ShouldQueue
    {
        use InteractsWithQueue, Queueable, SerializesModels;

        /**
         * The podcast instance.
         *
         * @var \App\Podcast
         */
        protected $podcast;

        /**
         * Create a new job instance.
         *
         * @param  \App\Models\Podcast  $podcast
         * @return void
         */
        public function __construct(Podcast $podcast)
        {
            $this->podcast = $podcast;
        }

        /**
         * Execute the job.
         *
         * @param  \App\Services\AudioProcessor  $processor
         * @return void
         */
        public function handle(AudioProcessor $processor)
        {
            // Process uploaded podcast...
        }

        /**
         * Handle a job failure.
         *
         * @param  \Throwable  $exception
         * @return void
         */
        public function failed(Throwable $exception)
        {
            // Send user notification of failure, etc...
        }
    }

> {note} `failed` 메서드를 호출하기 전에 작업의 새 인스턴스가 인스턴스화됩니다. 따라서 `handle` 메서드 내에서 발생했을 수 있는 클래스 속성의 변경사항은 손실됩니다.

<a name="retrying-failed-jobs"></a>
### 실패한 작업 재시도

`failed_jobs` 데이터베이스 테이블에 삽입된 실패한 모든 작업을 보려면 `queue:failed` Artisan 명령을 사용할 수 있습니다.

    php artisan queue:failed

`queue:failed` 명령은 작업 ID, 연결, 큐-queue, 실패 시간 및 기타 작업 정보를 나열합니다. 작업 ID를 사용하여 실패한 작업을 재시도할 수 있습니다. 예를 들어 ID가 `ce7bb17c-cdd8-41f0-a8ec-7b4fef4e5ece`인 실패한 작업을 재시도하려면 다음 명령을 실행하십시오.

    php artisan queue:retry ce7bb17c-cdd8-41f0-a8ec-7b4fef4e5ece

필요한 경우 명령에 여러 ID를 전달할 수 있습니다.

    php artisan queue:retry ce7bb17c-cdd8-41f0-a8ec-7b4fef4e5ece 91401d2c-0784-4f43-824c-34f94a33c24d

특정 큐-queue에 대해 실패한 모든 작업을 다시 시도할 수도 있습니다.

    php artisan queue:retry --queue=name

실패한 모든 작업을 다시 시도하려면 `queue:retry` 명령을 실행하고 `all`을 ID로 전달합니다.

    php artisan queue:retry all

실패한 작업을 삭제하려면 `queue:forget` 명령을 사용할 수 있습니다.

    php artisan queue:forget 91401d2c-0784-4f43-824c-34f94a33c24d

> {tip} [Horizon](/docs/{{version}}/horizon)을 사용할 때 실패한 작업을 삭제하려면 `queue:forget` 명령 대신 `horizon:forget` 명령을 사용해야 합니다.

`failed_jobs` 테이블에서 실패한 모든 작업을 삭제하려면 `queue:flush` 명령을 사용할 수 있습니다.

    php artisan queue:flush

<a name="ignoring-missing-models"></a>
### 누락된 모델 무시

Eloquent 모델을 작업에 주입할 때 모델은 큐-queue에 배치되기 전에 자동으로 직렬화되고 작업이 처리될 때 데이터베이스에서 다시 검색됩니다. 하지만 작업이 작업자의 처리를 기다리는 동안 모델이 삭제된 경우 `ModelNotFoundException`과 함께 작업이 실패할 수 있습니다.

편의를 위해 작업의 `deleteWhenMissingModels` 속성을 `true`로 설정하여 누락된 모델이 있는 작업을 자동으로 삭제하도록 선택할 수 있습니다. 이 속성이 `true`로 설정되면 라라벨은 예외를 발생시키지 않고 조용히 작업을 버립니다.

    /**
     * Delete the job if its models no longer exist.
     *
     * @var bool
     */
    public $deleteWhenMissingModels = true;

<a name="storing-failed-jobs-in-dynamodb"></a>
### DynamoDB에 실패한 작업 저장

라라벨은 또한 실패한 작업 레코드를 관계형 데이터베이스 테이블 대신 [DynamoDB](https://aws.amazon.com/dynamodb) 에 저장하는 기능을 지원합니다. 그러나 실패한 모든 작업 레코드를 저장하려면 DynamoDB 테이블을 생성해야 합니다. 일반적으로 이 테이블의 이름은 `failed_jobs`로 지정해야 하지만 애플리케이션의 `queue` 설정 파일에 있는 `queue.failed.table` 설정 값을 기반으로 테이블 이름을 지정해야 합니다.

`failed_jobs` 테이블에는 `application`이라는 문자열 기본 파티션 키와 `uuid`라는 문자열 기본 정렬 키가 있어야 합니다. 키의 `application` 부분에는 애플리케이션의 `app` 설정 파일 내 `name` 설정 값으로 정의된 애플리케이션의 이름이 포함됩니다. 애플리케이션 이름은 DynamoDB 테이블 키의 일부이므로 동일한 테이블을 사용하여 여러 라라벨 애플리케이션에 대해 실패한 작업을 저장할 수 있습니다.

또한 라라벨 애플리케이션이 Amazon DynamoDB와 통신할 수 있도록 AWS SDK를 설치해야 합니다.

```nothing
composer require aws/aws-sdk-php
```

다음으로 `queue.failed.driver` 설정 옵션의 값을 `dynamodb`로 설정합니다. 또한 실패한 작업 설정 배열 내에서 `key`, `secret` 및 `region` 설정 옵션을 정의해야 합니다. 이러한 옵션은 AWS에서 인증하는 데 사용됩니다. `dynamodb` 드라이버를 사용할 때 `queue.failed.database` 설정 옵션이 필요하지 않습니다.

```php
'failed' => [
    'driver' => env('QUEUE_FAILED_DRIVER', 'dynamodb'),
    'key' => env('AWS_ACCESS_KEY_ID'),
    'secret' => env('AWS_SECRET_ACCESS_KEY'),
    'region' => env('AWS_DEFAULT_REGION', 'us-east-1'),
    'table' => 'failed_jobs',
],
```

<a name="pruning-failed-jobs"></a>
### 실패한 작업 정리하기

`queue:prune-failed` Artisan 명령을 호출하여 애플리케이션의 `failed_jobs` 테이블에 있는 모든 레코드를 삭제할 수 있습니다.

    php artisan queue:prune-failed

명령에 `--hours` 옵션을 제공하면 마지막 N시간 내에 삽입된 실패한 작업 레코드만 유지됩니다. 예를 들어 다음 명령은 48시간 전에 삽입된 실패한 작업 레코드를 모두 삭제합니다.

    php artisan queue:prune-failed --hours=48

<a name="failed-job-events"></a>
### 실패한 Job에 대한 이벤트

작업이 실패할 때 호출될 이벤트 리스너를 등록하려면 `Queue` 파사드의 `failing` 메서드를 사용할 수 있습니다. 예를 들어, 라라벨에 포함된 `AppServiceProvider`의 `boot` 메소드에서 이 이벤트에 클로저를 첨부할 수 있습니다.

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

<a name="clearing-jobs-from-queues"></a>
## 큐-queue에서 작업 지우기

> {tip} [Horizon](/docs/{{version}}/horizon)을 사용할 때 큐-queue에서 작업을 지우려면 `queue:clear` 명령 대신 `horizon:clear` 명령을 사용해야 합니다.

기본 연결의 기본 큐-queue에서 모든 작업을 삭제하려면 `queue:clear` Artisan 명령을 사용하여 삭제할 수 있습니다.

    php artisan queue:clear

특정 연결 및 큐-queue에서 작업을 삭제하기 위해 `connection` 인수와 `queue` 옵션을 제공할 수도 있습니다.

    php artisan queue:clear redis --queue=emails

> {note} 큐-queue에서 작업 지우기는 SQS, Redis 및 데이터베이스 큐-queue 드라이버에서만 사용할 수 있습니다. 또한 SQS 메시지 삭제 프로세스는 최대 60초가 걸리므로 큐-queue을 지운 후 최대 60초 동안 SQS 큐-queue로 전송된 작업도 삭제될 수 있습니다.

<a name="monitoring-your-queues"></a>
## 큐-queue 모니터링

큐-queue에 작업이 갑자기 유입되면 과부하가 걸려 작업이 완료될 때까지 오랜 대기 시간이 발생할 수 있습니다. 원하는 경우 큐-queue 작업 수가 지정된 임계값을 초과할 때 라라벨이 경고할 수 있습니다.

시작하려면 `queue:monitor` 명령을 [매분 실행](/docs/{{version}}/scheduling)으로 예약해야 합니다. 이 명령은 모니터링하려는 큐-queue의 이름과 원하는 작업 수 임계값을 허용합니다.

```bash
php artisan queue:monitor redis:default,redis:deployments --max=100
```

이 명령을 단독으로 예약하는 것만으로는 큐-queue의 초과 상태를 경고하는 알림을 트리거하기에 충분하지 않습니다. 명령이 임계값을 초과하는 작업 수가 있는 큐-queue을 발견하면 `Illuminate\Queue\Events\QueueBusy` 이벤트가 전달됩니다. 귀하 또는 귀하의 개발 팀에 알림을 보내기 위해 애플리케이션의 `EventServiceProvider` 내를 이용해서, 이 이벤트를 수신할 수 있습니다.

```php
use App\Notifications\QueueHasLongWaitTime;
use Illuminate\Queue\Events\QueueBusy;
use Illuminate\Support\Facades\Event;
use Illuminate\Support\Facades\Notification;

/**
 * Register any other events for your application.
 *
 * @return void
 */
public function boot()
{
    Event::listen(function (QueueBusy $event) {
        Notification::route('mail', 'dev@example.com')
                ->notify(new QueueHasLongWaitTime(
                    $event->connection,
                    $event->queue,
                    $event->size
                ));
    });
}
```

<a name="job-events"></a>
## Job 이벤트

`Queue` [facade](/docs/{{version}}/facades)에서 `before` 및 `after` 메서드를 사용하여 대기 중인 작업이 처리되기 전이나 후에 실행할 콜백을 지정할 수 있습니다. 이러한 콜백은 대시보드에 대한 추가 로깅 또는 증분 통계를 처리할 수 있는 좋은 기회입니다. 일반적으로 [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드에서 이러한 메소드를 호출해야 합니다. 예를 들어 라라벨에 포함된 `AppServiceProvider`를 사용할 수 있습니다.

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

`Queue` [facade](/docs/{{version}}/facades)에서 `looping` 메서드를 사용하여 작업자가 큐-queue에서 작업을 가져오기 전에 실행되는 콜백을 지정할 수 있습니다. 예를 들어, 이전에 실패한 작업에 의해 열려 있던 모든 트랜잭션을 롤백하기 위ㅂ해 클로저를 등록할 수 있습니다.

    use Illuminate\Support\Facades\DB;
    use Illuminate\Support\Facades\Queue;

    Queue::looping(function () {
        while (DB::transactionLevel() > 0) {
            DB::rollBack();
        }
    });
