# Queues
# Queues-큐

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Connections Vs. Queues](#connections-vs-queues)
    - [커넥션 Vs. Queues-큐](#connections-vs-queues)
    - [Driver Notes & Prerequisites](#driver-prerequisites)
    - [드라이버 주의사항과 사전준비사항](#driver-prerequisites)
- [Creating Jobs](#creating-jobs)
- [Job 생성하기](#creating-jobs)
    - [Generating Job Classes](#generating-job-classes)
    - [Job 클래스 생성하기](#generating-job-classes)
    - [Class Structure](#class-structure)
    - [클래스 구조](#class-structure)
- [Dispatching Jobs](#dispatching-jobs)
- [Job 처리하기](#dispatching-jobs)
    - [Delayed Dispatching](#delayed-dispatching)
    - [지연시켜서 처리하기](#delayed-dispatching)
    - [Synchronous Dispatching](#synchronous-dispatching)
    - [동기식 반환](#synchronous-dispatching)
    - [Job Chaining](#job-chaining)
    - [Job 체이닝](#job-chaining)
    - [Customizing The Queue & Connection](#customizing-the-queue-and-connection)
    - [Queue-큐 & 커넥션 커스터마이징](#customizing-the-queue-and-connection)
    - [Specifying Max Job Attempts / Timeout Values](#max-job-attempts-and-timeout)
    - [최대 재시도 횟수 / 타임아웃 시간 지정하기](#max-job-attempts-and-timeout)
    - [Rate Limiting](#rate-limiting)
    - [실행 속도 제한](#rate-limiting)
    - [Error Handling](#error-handling)
    - [에러 핸들링](#error-handling)
- [Queueing Closures](#queueing-closures)
- [큐잉 클로저](#queueing-closures)
- [Running The Queue Worker](#running-the-queue-worker)
- [Queue Worker 구동하기](#running-the-queue-worker)
    - [Queue Priorities](#queue-priorities)
    - [Queue 우선순위 지정하기](#queue-priorities)
    - [Queue Workers & Deployment](#queue-workers-and-deployment)
    - [Queue Workers & 배포](#queue-workers-and-deployment)
    - [Job Expirations & Timeouts](#job-expirations-and-timeouts)
    - [Job 만료 & 타임아웃](#job-expirations-and-timeouts)
- [Supervisor Configuration](#supervisor-configuration)
- [Supervisor 설정](#supervisor-configuration)
- [Dealing With Failed Jobs](#dealing-with-failed-jobs)
- [실패한 Job 처리하기](#dealing-with-failed-jobs)
    - [Cleaning Up After Failed Jobs](#cleaning-up-after-failed-jobs)
    - [Cleaning Up After Failed Jobs](#cleaning-up-after-failed-jobs)
    - [Failed Job Events](#failed-job-events)
    - [실패한 Job 이벤트](#failed-job-events)
    - [Retrying Failed Jobs](#retrying-failed-jobs)
    - [실패한 Job 재시도하기](#retrying-failed-jobs)
    - [Ignoring Missing Models](#ignoring-missing-models)
    - [누락 된 모델 무시하기](#ignoring-missing-models)
- [Job Events](#job-events)
- [Job 이벤트](#job-events)

<a name="introduction"></a>
## Introduction
## 시작하기

> {tip} Laravel now offers Horizon, a beautiful dashboard and configuration system for your Redis powered queues. Check out the full [Horizon documentation](/docs/{{version}}/horizon) for more information.

> {tip} 라라벨은 이제 Redis 큐-queue를 위한 멋진 대시보드와 설정 시스템을 제공하는 Horizon를 지원합니다. 보다 자세한 사항은 [Horizon 문서](/docs/{{version}}/horizon)를 참고하십시오.

Laravel queues provide a unified API across a variety of different queue backends, such as Beanstalk, Amazon SQS, Redis, or even a relational database. Queues allow you to defer the processing of a time consuming task, such as sending an email, until a later time. Deferring these time consuming tasks drastically speeds up web requests to your application.

라라벨의 Queue-큐는 Beanstalk, Amazone SQS, Redis 그리고 관계형 데이터베이스과 같은 다양한 큐 시스템을 위한 통일된 API를 제공합니다. 큐를 사용하면 이메일을 보내는 일과 같이 시간이 소요되는 Job을 나중에 처리할 수 있습니다. 이렇게 소모되는 작업들을 나중에 처리함으로써 여러분의 애플리케이션은 웹 요청-request을 더 빠르게 처리할 수 있습니다.

The queue configuration file is stored in `config/queue.php`. In this file you will find connection configurations for each of the queue drivers that are included with the framework, which includes a database, [Beanstalkd](https://kr.github.io/beanstalkd/), [Amazon SQS](https://aws.amazon.com/sqs/), [Redis](https://redis.io),  and a synchronous driver that will execute jobs immediately (for local use). A `null` queue driver is also included which discards queued jobs.

큐의 환경 설정 파일은 `config/queue.php`에 위치하고 있습니다. 이 파일에서 프레임워크에 포함된 데이터베이스, [Beanstalkd](https://kr.github.com/beanstalkd), [Amazon SQS](https://aws.amazon.com/sqs/), [Redis](https://redis.io), 그리고 (로컬 사용을 위한) job을 즉시 실행하는 synchronous 드라이버 설정을 확인할 수 있습니다. `null` 큐 드라이버는 큐 동작을 비활성화합니다.

<a name="connections-vs-queues"></a>
### Connections Vs. Queues
### 커넥션 Vs. Queues-큐

Before getting started with Laravel queues, it is important to understand the distinction between "connections" and "queues". In your `config/queue.php` configuration file, there is a `connections` configuration option. This option defines a particular connection to a backend service such as Amazon SQS, Beanstalk, or Redis. However, any given queue connection may have multiple "queues" which may be thought of as different stacks or piles of queued jobs.

라라벨의 Queue-큐를 알아보기 전에, "커넥션"과 "queue-큐"의 차이점을 이해하고 있는 것이 중요합니다. `config/queue.php` 설정 파일에는 `connections` 설정 옵션이 존재합니다. 이 옵션은 Amazon SQS, Beanstalk 또는 Redis 와 같은 서비스에 대한 개별적인 커넥션을 정의합니다. 그렇지만 queue 커넥션은 각기 다른 job이 쌓여 있는 여러개의 "queue-큐"를 가질 수도 있습니다.

Note that each connection configuration example in the `queue` configuration file contains a `queue` attribute. This is the default queue that jobs will be dispatched to when they are sent to a given connection. In other words, if you dispatch a job without explicitly defining which queue it should be dispatched to, the job will be placed on the queue that is defined in the `queue` attribute of the connection configuration:

`queue` 설정 파일 안에 있는 각각의 커넥션 설정 예제가 `queue` 속성을 포함하고 있다는 것을 주의하십시오. 이것은 job이 처리되기 위해 주어진 커넥션에 보내졌을 때의 기본 큐입니다. 다시 말해, job을 어떤 큐를 통해서 처리할지 명시적으로 정의하지 않는다면, job은 커넥션 설정의 `queue` 속성에 정의되어 있는 큐에 보내집니다:

    // This job is sent to the default queue...
    Job::dispatch();

    // This job is sent to the "emails" queue...
    Job::dispatch()->onQueue('emails');

Some applications may not need to ever push jobs onto multiple queues, instead preferring to have one simple queue. However, pushing jobs to multiple queues can be especially useful for applications that wish to prioritize or segment how jobs are processed, since the Laravel queue worker allows you to specify which queues it should process by priority. For example, if you push jobs to a `high` queue, you may run a worker that gives them higher processing priority:

일부 애플리케이션은 job을 여러개의 queue에 푸시 할 필요가 없으며 하나의 queue에 보내는것이 더 나을 수도 있습니다. 하지만, 여러 queue에 job을 보내는 것은 우선 순위를 부여하고자 한다거나, 혹은 job 처리를 분할하고자하는 애플리케이션에서는 특히나 유용합니다. 라라벨의 queue worker는 우선 순위별로 처리해야하는 queue을 지정할 수 있기 때문입니다. 예를 들어, job을 `high` queue로 푸시한다면, 이 job들을 높은 우선 순위를 부여하여 처리할 수 있습니다:

    php artisan queue:work --queue=high,default

<a name="driver-prerequisites"></a>
### Driver Notes & Prerequisites
### 드라이버 주의사항과 사전준비사항

#### Database
#### 데이터베이스

In order to use the `database` queue driver, you will need a database table to hold the jobs. To generate a migration that creates this table, run the `queue:table` Artisan command. Once the migration has been created, you may migrate your database using the `migrate` command:

`database` 큐 드라이버를 사용하기 위해서는 Job들을 담아둘 데이터베이스 테이블이 필요합니다. 이 테이블을 추가하기 위한 마이그레이션을 생성하려면 `queue:table` 아티즌 명령을 실행하면 됩니다. 마이그레이션 파일이 생성되고 나면 `migrate` 명령어를 사용하여 데이터베이스 테이블을 생성할 수 있습니다:

    php artisan queue:table

    php artisan migrate

#### Redis
#### Redis

In order to use the `redis` queue driver, you should configure a Redis database connection in your `config/database.php` configuration file.

`redis` 큐 드라이버를 사용하기 위해서는 `config/database.php` 설정 파일에서 Redis 커넥션을 설정해야 합니다.

**Redis Cluster**
**Redis 클러스터**

If your Redis queue connection uses a Redis Cluster, your queue names must contain a [key hash tag](https://redis.io/topics/cluster-spec#keys-hash-tags). This is required in order to ensure all of the Redis keys for a given queue are placed into the same hash slot:

Redis 큐 커넥션이 Redis 클러스터를 사용한다면, 큐 이름은 [key hash tag](https://redis.io/topics/cluster-spec#keys-hash-tags)를 반드시 포함하고 있어야 합니다. 이것은 주어진 큐의 모든 Redis 키가 동일한 해시 슬롯에 배치되기 위해서 필요로합니다:

    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => '{default}',
        'retry_after' => 90,
    ],

**Blocking**
**대기하기**

When using the Redis queue, you may use the `block_for` configuration option to specify how long the driver should wait for a job to become available before iterating through the worker loop and re-polling the Redis database.

Redis 큐를 사용할 때,`block_for` 설정 옵션을 사용하여 드라이버가 작업 루프를 반복하고 Redis 데이터베이스를 다시 폴링하기 전에 job이 사용 가능하게 될 때까지 대기해야하는 시간을 지정할 수 있습니다.

Adjusting this value based on your queue load can be more efficient than continually polling the Redis database for new jobs. For instance, you may set the value to `5` to indicate that the driver should block for five seconds while waiting for a job to become available:

새로운 job을 가져오기 위해 Redis 데이터베이스를 계속 폴링하는 것보다는 큐의 부하에 따라 조절하는 게 더 효율적입니다. 예를 들면, 새로운 job이 사용 가능하게 될 때까지 드라이버가 5초를 기다려야 한다는 의미로 이 값을 5로 설정할 수 있습니다.

    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => 'default',
        'retry_after' => 90,
        'block_for' => 5,
    ],


#### Other Driver Prerequisites
#### 다른 큐 드라이버의 사전준비 사항들

The following dependencies are needed for the listed queue drivers:

아래의 의존 패키지들은 앞서 이야기 했던 각각의 큐 드라이버들을 사용하는데 필요합니다:


- Amazon SQS: `aws/aws-sdk-php ~3.0`
- Beanstalkd: `pda/pheanstalk ~4.0`
- Redis: `predis/predis ~1.0`


<a name="creating-jobs"></a>
## Creating Jobs
## Jobs 생성하기

<a name="generating-job-classes"></a>
### Generating Job Classes
### Job 클래스 생성하기

By default, all of the queueable jobs for your application are stored in the `app/Jobs` directory. If the `app/Jobs` directory doesn't exist, it will be created when you run the `make:job` Artisan command. You may generate a new queued job using the Artisan CLI:

기본적으로, 애플리케이션을 위한 모든 큐 Job들은 `app/Jobs` 디렉토리에 저장됩니다. `app/Jobs` 디렉토리가 존재하지 않는다면, `make:job` 아티즌 명령어를 실행할 때 생성됩니다. 새로운 큐 Job 클래스는 아티즌 CLI를 통해서 생성할 수 있습니다:

    php artisan make:job ProcessPodcast

The generated class will implement the `Illuminate\Contracts\Queue\ShouldQueue` interface, indicating to Laravel that the job should be pushed onto the queue to run asynchronously.

생성된 클래스는 Job이 queue를 통해서 비동기적으로 실행되어야 된다는 것을 나타내는, `Illuminate\Contracts\Queue\ShouldQueue` 인터페이스를 구현하고 있습니다.

<a name="class-structure"></a>
### Class Structure
### 클래스 구조

Job classes are very simple, normally containing only a `handle` method which is called when the job is processed by the queue. To get started, let's take a look at an example job class. In this example, we'll pretend we manage a podcast publishing service and need to process the uploaded podcast files before they are published:

Job 클래스는 매우 간단하며, 기본적으로 큐에 저장된 Job을 처리하기 위해서 호출되는 `handle` 메소드만을 가지고 있습니다. 시작에 앞서, 다음 Job 클래스의 예제를 살펴봅시다. 이 예제에서 우리는 팟캐스트 배포 서비스를 관리하고 업로드된 팟캐스트 파일을 배포하기 전 처리해야 된다고 가정하겠습니다:

    <?php

    namespace App\Jobs;

    use App\Podcast;
    use App\AudioProcessor;
    use Illuminate\Bus\Queueable;
    use Illuminate\Queue\SerializesModels;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Foundation\Bus\Dispatchable;

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

In this example, note that we were able to pass an [Eloquent model](/docs/{{version}}/eloquent) directly into the queued job's constructor. Because of the `SerializesModels` trait that the job is using, Eloquent models will be gracefully serialized and unserialized when the job is processing. If your queued job accepts an Eloquent model in its constructor, only the identifier for the model will be serialized onto the queue. When the job is actually handled, the queue system will automatically re-retrieve the full model instance from the database. It's all totally transparent to your application and prevents issues that can arise from serializing full Eloquent model instances.

이 예제에서 큐 Job 클래스의 생성자에 [Eloquent 모델](/docs/{{version}}/eloquent)이 직접적으로 전달된다는 것을 주목하십시오. Job 클래스에서 사용하는 SerializesModels 트레이트-trait에 의해 Eloquent 모델은 효과적으로 serialize 될것이며, Job이 처리 될 때 unserialize 됩니다. 큐에 저장된 Job이 생성자에서 Eloquent 모델을 전달 받는 경우, 모델의 식별자만 큐로 저장될 때 serialize 될 것입니다. Job이 실제로 처리될 때 큐 시스템은 자동으로 데이터베이스에서 해당 모델 인스턴스를 다시 가져옵니다. 이렇게 하는 것은 애플리케이션을 완전히 투명하게 하고, Eloquent 모델 인스턴스를 serialize 할 때 발생하는 문제를 방지 할 수 있습니다.

The `handle` method is called when the job is processed by the queue. Note that we are able to type-hint dependencies on the `handle` method of the job. The Laravel [service container](/docs/{{version}}/container) automatically injects these dependencies.

큐에 의해서 Job이 처리될 때에는 `handle` 메소드가 호출 됩니다. Job 클래스의 `handle` 메소드에 의존 객체들이 타입-힌트 될 수 있다는 것에 주의하십시오. 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)가 자동으로 의존 객체들을 주입해 줍니다.

If you would like to take total control over how the container injects dependencies into the `handle` method, you may use the container's `bindMethod` method. The `bindMethod` method accepts a callback which receives the job and the container. Within the callback, you are free to invoke the `handle` method however you wish. Typically, you should call this method from a [service provider](/docs/{{version}}/providers):

`handle` 메소드에서 컨테이너가 의존 객체들을 어떻게 주입하는지에 대한 완전한 제어를 원한다면, 컨테이너의 `bindMethod` 메소드를 사용할 수 있습니다. `bindMethod` 메소드는 job 과 컨테이너를 인자로 받는 콜백은 인자로 받습니다. 콜백 안에서, 자유롭게 `handle` 메소드를 호출할 수 있습니다. 일반적으로 이는 [서비스 프로바이더](/docs/{{version}}/providers) 안에서 호출합니다.

    use App\Jobs\ProcessPodcast;

    $this->app->bindMethod(ProcessPodcast::class.'@handle', function ($job, $app) {
        return $job->handle($app->make(AudioProcessor::class));
    });

> {note} Binary data, such as raw image contents, should be passed through the `base64_encode` function before being passed to a queued job. Otherwise, the job may not properly serialize to JSON when being placed on the queue.

> {note} Raw 이미지와 같은 바이너리 데이터의 경우, 큐를 통해서 처리되기 전에 `base64_encode` 함수가 적용된 상태로 전달되어야 합니다. 그렇지 않으면 Job이 큐에 입력 될 때 JSON으로 제대로 serialize 되지 않을 수 있습니다.

<a name="dispatching-jobs"></a>
## Dispatching Jobs
## Job 처리하기

Once you have written your job class, you may dispatch it using the `dispatch` method on the job itself. The arguments passed to the `dispatch` method will be given to the job's constructor:

Job 클래스를 작성한 뒤에 클래스의 `dispatch` 메소드를 사용하여 이를 처리할 수 있습니다. `dispatch` 메소드에 전달할 인자는 job의 생성자입니다:

    <?php

    namespace App\Http\Controllers;

    use App\Jobs\ProcessPodcast;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

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
### Delayed Dispatching
### 지연시켜서 처리하기

If you would like to delay the execution of a queued job, you may use the `delay` method when dispatching a job. For example, let's specify that a job should not be available for processing until 10 minutes after it has been dispatched:

대기열에 있는 job의 실행을 지연하려면, job 이 dispathcing 될 때 `delay` 메소드를 사용할 수 있습니다. 예를 들어, job이 10 분이 지난 뒤에 처리되도록 지정해보겠습니다:

    <?php

    namespace App\Http\Controllers;

    use App\Jobs\ProcessPodcast;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

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

> {note} The Amazon SQS queue service has a maximum delay time of 15 minutes.

> {note} 아마존 SQS 큐 서비스는 지연시간이 최대 15분을 넘을 수 없습니다.

<a name="synchronous-dispatching"></a>
### Synchronous Dispatching
### 동기식 반환

If you would like to dispatch a job immediately (synchronously), you may use the `dispatchNow` method. When using this method, the job will not be queued and will be run immediately within the current process:

작업을 즉시 (동기적으로) 반환하고 싶다면 `dispatchNow` 메소드를 사용할 수 있습니다. 이 방법을 사용하면 작업이 대기열에 저장되지 않고 현재 프로세스 내에서 즉시 실행됩니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use App\Jobs\ProcessPodcast;
    use App\Http\Controllers\Controller;

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
### Job Chaining
### Job 체이닝

Job chaining allows you to specify a list of queued jobs that should be run in sequence. If one job in the sequence fails, the rest of the jobs will not be run. To execute a queued job chain, you may use the `withChain` method on any of your dispatchable jobs:

Job 체이닝은 여러분이 queued 로 입력된 job이 순차적으로 실행되도록 목록을 지정할 수 있게 해줍니다. 이 순차적인 목록에서 하나의 job 이 실패하면, 나머지 job은 실행되지 않습니다. job 체이닝을 실행하려면, jobs 에 `withChain` 메소드를 사용하면 됩니다:

    ProcessPodcast::withChain([
        new OptimizePodcast,
        new ReleasePodcast
    ])->dispatch();

> {note} Deleting jobs using the `$this->delete()` method will not prevent chained jobs from being processed. The chain will only stop executing if a job in the chain fails.

> {note} `$this->delete()` 메소드를 사용하여 작업을 삭제한다고해서 연결된 작업이 처리되는 것을 막을 수는 없습니다. 체인의 작업이 실패하면 체인은 실행을 중지합니다.

#### Chain Connection & Queue
#### Connection과 Queue 체이닝

If you would like to specify the default connection and queue that should be used for the chained jobs, you may use the `allOnConnection` and `allOnQueue` methods. These methods specify the queue connection and queue name that should be used unless the queued job is explicitly assigned a different connection / queue:

체이닝된 job에 사용될 기본 커넥션과 큐를 지정하고 싶다면, `allOnConnection`과 `allOnQueue` 메소드를 사용하면 됩니다. 이 메소드는 대기열에 있는 job이 명시적으로 다른 커넥션 / 큐로 지정되지 않을 경우 사용될 큐 커넥션과 큐 이름을 지정합니다.

    ProcessPodcast::withChain([
        new OptimizePodcast,
        new ReleasePodcast
    ])->dispatch()->allOnConnection('redis')->allOnQueue('podcasts');


<a name="customizing-the-queue-and-connection"></a>
### Customizing The Queue & Connection
### Queue-큐 & 커넥션 커스터마이징

#### Dispatching To A Particular Queue
#### 특정 queue-큐로 처리하기

By pushing jobs to different queues, you may "categorize" your queued jobs and even prioritize how many workers you assign to various queues. Keep in mind, this does not push jobs to different queue "connections" as defined by your queue configuration file, but only to specific queues within a single connection. To specify the queue, use the `onQueue` method when dispatching the job:

다른 queue-큐에 job을 푸시하여, queue job을 "분류"할 수 있으며, 다양한 queue에 다수의 worker를 지정하여 개별 우선순위를 지정할 수도 있습니다. 유념할 점은, 이것은 queue 설정 파일에 정의된 다른 "커넥션"에 job을 푸시하는 것이 아니라, 하나의 커넥션 안에서 queue-큐를 지정한다는 것입니다. queue-큐를 지정하려면 job 이 dispatching 될 때 `onQueue` 메소드를 사용하면 됩니다:

    <?php

    namespace App\Http\Controllers;

    use App\Jobs\ProcessPodcast;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

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

#### Dispatching To A Particular Connection
#### 특정 커넥션으로 처리하기

If you are working with multiple queue connections, you may specify which connection to push a job to. To specify the connection, use the `onConnection` method when dispatching the job:

여러개의 queue-큐 커넥션을 사용하고자 한다면, 푸시하는 job에 커넥션을 지정할 수 있습니다. 커넥션을 지정하기 위해서는, job이 dispatching 될 때 `onConnection` 메소드를 사용하면 됩니다:

    <?php

    namespace App\Http\Controllers;

    use App\Jobs\ProcessPodcast;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

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

job을 처리하는 queue에 대해서 `onConnection` 과 `onQueue` 메소드를 체이닝하여 지정할 수도 있습니다:

    ProcessPodcast::dispatch($podcast)
                  ->onConnection('sqs')
                  ->onQueue('processing');
                  
Alternatively, you may specify the `connection` as a property on the job class:

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
### Specifying Max Job Attempts / Timeout Values
### 최대 재시도 횟수 / 타임아웃 시간 지정하기

#### Max Attempts
#### 최대 재시도 횟수

One approach to specifying the maximum number of times a job may be attempted is via the `--tries` switch on the Artisan command line:

작업이 수행될 때 쵀대 재시도 횟수를 지정하려면, 아티즌 명령어에 `--tries` 스위치를 지정하면 됩니다:

    php artisan queue:work --tries=3

However, you may take a more granular approach by defining the maximum number of attempts on the job class itself. If the maximum number of attempts is specified on the job, it will take precedence over the value provided on the command line:

그렇지만, job 클래스 자체에 최대 재시도 횟수를 정의하여 보다 세부적으로 조절하는 방법도 있습니다. job 클래스에 최대 재시도 횟수가 지정되면 커맨드 라인에서 지정된 값보다 우선합니다:

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
#### Time Based Attempts
#### 시간 기반의 재시도

As an alternative to defining how many times a job may be attempted before it fails, you may define a time at which the job should timeout. This allows a job to be attempted any number of times within a given time frame. To define the time at which a job should timeout, add a `retryUntil` method to your job class:

job 이 최종적으로 실패처리 될 때까지, 얼마나 많이 재시도 할지 횟수를 정의하는 대신에, job이 시간초과 처리되는 기준 시간을 정의할 수 있습니다. 이를 이용하면 주어진 시간안에 job을 여러번 재시도하게 할 수 있습니다. job 의 시간초과를 정의하려면, job 클래스에 `retryUntil` 메소드를 추가하면 됩니다:

    /**
     * Determine the time at which the job should timeout.
     *
     * @return \DateTime
     */
    public function retryUntil()
    {
        return now()->addSeconds(5);
    }

> {tip} You may also define a `retryUntil` method on your queued event listeners.

> {tip} 또한 queued 이벤트 리스너에도 `retryUntil` 메소드를 정의할 수 있습니다.

#### Timeout
#### 타임아웃

> {note} The `timeout` feature is optimized for PHP 7.1+ and the `pcntl` PHP extension.

> {note} `timeout` 기능은 PHP 7.1이상 그리고 `pcntl` PHP 확장 기능에 최적화 되어 있습니다.

Likewise, the maximum number of seconds that jobs can run may be specified using the `--timeout` switch on the Artisan command line:

마찬가지로, 작업이 수행될 때 최대 수행시간을 초단위로 지정할 수 있습니다. 이는 아티즌 명령어에 `--timeout` 스위치를 지정하면 됩니다:

    php artisan queue:work --timeout=30

However, you may also define the maximum number of seconds a job should be allowed to run on the job class itself. If the timeout is specified on the job, it will take precedence over any timeout specified on the command line:

또한, job 클래스 자체에 최대 작업 수행 시간을 초단위로 정의할 수도 있습니다. 타임아웃 시간이 job에 정의되면, 커맨드 라인에서 제공된 값보다 우선합니다:

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
### Rate Limiting
### 실행 속도 제한

> {note} This feature requires that your application can interact with a [Redis server](/docs/{{version}}/redis).

> {note} 이 기능을 사용하려면, 애플리케이션이 [Redis server](/docs/{{version}}/redis)에 연결되어 있어야 합니다.

If your application interacts with Redis, you may throttle your queued jobs by time or concurrency. This feature can be of assistance when your queued jobs are interacting with APIs that are also rate limited.

애플리케이션이 Redis 에 연결되어 있는 경우, queue job을 시간 또는 동시에 처리할 수 있는 수를 제한할 수 있습니다. 이 기능은 사용량 제한이 있는 외부 API 작업을 수행하는 queue job을 실행할 때 도움이 될 수 있습니다.

For example, using the `throttle` method, you may throttle a given type of job to only run 10 times every 60 seconds. If a lock can not be obtained, you should typically release the job back onto the queue so it can be retried later:
 
예를들어 `throttle` 메소드를 사용하여 주어진 job이 60초마다 10번만 실행되도록 조절할 수 있습니다. lock을 획득할 수 없는 경우에는 일반적으로 job을 릴리즈 하여 나중에 다시 시도하도록 할 수 있습니다:

    Redis::throttle('key')->allow(10)->every(60)->then(function () {
        // Job logic...
    }, function () {
        // Could not obtain lock...

        return $this->release(10);
    });

> {tip} In the example above, the `key` may be any string that uniquely identifies the type of job you would like to rate limit. For example, you may wish to construct the key based on the class name of the job and the IDs of the Eloquent models it operates on.

> {tip} 이 예제에서, `key` 는 속도제한을 시키고자 하는 job을 식별할 수 있는 고유한 문자열입니다. 예를 들면, 이 키는 job의 클래스 이름 그리고 Eloquent 모델의 id 값을 기반으로 구성할 수 있습니다.

> {note}  Releasing a throttled job back onto the queue will still increment the job's total number of `attempts`.

> {note} 스로틀 작업을 대기열로 다시 보낸다면 job의 총 시도 횟수가 증가합니다.

Alternatively, you may specify the maximum number of workers that may simultaneously process a given job. This can be helpful when a queued job is modifying a resource that should only be modified by one job at a time. For example, using the `funnel` method, you may limit jobs of a given type to only be processed by one worker at a time:

이 대신에, 여러분은 주어진 job을 동시에 처리할 수 있는 worker의 최대 갯수를 지정할 수도 있습니다. 이는 queue에 들어 있는 job이 리소스를 수정하는 것과 같이, 한번에 하나씩 실행되어야 하는 경우 유용합니다. 예를 들자면, `funnel` 메소드를 사용하여, 한번에 하나의 worker를 통해서 처리되도록 하는 유형의 job에서 제한을 걸 수도 있습니다:

    Redis::funnel('key')->limit(1)->then(function () {
        // Job logic...
    }, function () {
        // Could not obtain lock...

        return $this->release(10);
    });

> {tip} When using rate limiting, the number of attempts your job will need to run successfully can be hard to determine. Therefore, it is useful to combine rate limiting with [time based attempts](#time-based-attempts).

> {tip} 사용량 제한을 사용하는 경우, job이 재시도 되는 횟수를 결정하는 것은 매우 어려운 일이 됩니다. 그래서 이런경우 사용량 제한과 함께 [시간 기반 재시도 횟수 지정하기](#time-based-attempts)를 사용하는 것이 유용합니다.

<a name="error-handling"></a>
### Error Handling
### 에러 핸들

If an exception is thrown while the job is being processed, the job will automatically be released back onto the queue so it may be attempted again. The job will continue to be released until it has been attempted the maximum number of times allowed by your application. The maximum number of attempts is defined by the `--tries` switch used on the `queue:work` Artisan command. Alternatively, the maximum number of attempts may be defined on the job class itself. More information on running the queue worker [can be found below](#running-the-queue-worker).

job이 처리되는 동안에 exception이 발생하면, job은 자동으로 다시 실행되기 위해서 queue로 반환됩니다. job은 애플리케이션에서 정의된 최대 재시도 횟수만큼 계속해서 실행됩니다. 재시도 횟수는 `queue:work` 아티즌 명령어를 사용할 때 `--tries` 스위치를 사용하여 정의됩니다. 재시도 횟수를 job클래스 자체에 정의할 수도 있습니다. queue worker에 대한 보다 자세한 사항은 [다음에서 찾을 수 있습니다](#running-the-queue-worker)

<a name="queueing-closures"></a>
## Queueing Closures
## 큐잉 클로저

Instead of dispatching a job class to the queue, you may also dispatch a Closure. This is great for quick, simple tasks that need to be executed outside of the current request cycle:

작업 클래스를 대기열로 보내지 않고 Closure를 보낼 수도 있습니다. 이는 현재 요청주기를 벗어나 실행해야하는 빠르고 간단한 작업에 유용합니다.

    $podcast = App\Podcast::find(1);

    dispatch(function () use ($podcast) {
        $podcast->publish();
    });

When dispatching Closures to the queue, the Closure's code contents is cryptographically signed so it can not be modified in transit.

클로저를 큐에 디스패치 할 때 Closure의 코드 내용은 암호화되어 서명되어 전송 중에 수정할 수 없습니다.

<a name="running-the-queue-worker"></a>
## Running The Queue Worker
## Queue-큐 worker 실행하기

Laravel includes a queue worker that will process new jobs as they are pushed onto the queue. You may run the worker using the `queue:work` Artisan command. Note that once the `queue:work` command has started, it will continue to run until it is manually stopped or you close your terminal:

라라벨은 queue에 푸시된 새로운 job을 처리하는 queue worker를 포함하고 있습니다. `queue:work` 아티즌 명령어를 사용하여 worker를 실행할 수 있습니다. `queue:work` 명령어가 시작하고 나면, 여러분이 수동으로 중지하거나, 터미널을 닫을 때 까지 계속 실행되어 있습니다:

    php artisan queue:work

> {tip} To keep the `queue:work` process running permanently in the background, you should use a process monitor such as [Supervisor](#supervisor-configuration) to ensure that the queue worker does not stop running.

> {tip} `queue:work` 프로세스를 백그라운드에서 계속 지속되게 하려면, queue worker가 중단되지 않고 계속되는 것을 보장하기 위해 [Supervisor](#supervisor-configuration)와 같은 프로세스 모니터를 사용해야 합니다.

Remember, queue workers are long-lived processes and store the booted application state in memory. As a result, they will not notice changes in your code base after they have been started. So, during your deployment process, be sure to [restart your queue workers](#queue-workers-and-deployment).

주의할 점은 queue worker는 장시간 동안 살아 있는 프로세스로, 애플리케이션의 상태를 메모리에 저장한다는 것입니다. 그 결과, 일단 구동되고 나면 코드 기반의 변경사항은 반영되지 않습니다. 따라서 개발 중에는 직접 [queue worker를 재시작](#queue-workers-and-deployment)해야 합니다.

Alternatively, you may run the `queue:listen` command. When using the `queue:listen` command, you don't have to manually restart the worker after your code is changed; however, this command is not as efficient as `queue:work`:

또는 `queue:listen` 명령을 실행할 수도 있습니다. `queue:listen` 명령을 사용할 때 코드가 변경된 후에는 worker를 수동으로 다시 시작할 필요가 없습니다. 그러나 이 명령은 `queue:work`만큼 효율적이지 않습니다.

    php artisan queue:listen

#### Specifying The Connection & Queue
#### 커넥션 & queue-큐 지정하기

You may also specify which queue connection the worker should utilize. The connection name passed to the `work` command should correspond to one of the connections defined in your `config/queue.php` configuration file:

어떤 queue-큐 커넥션을 worker 가 사용할지 지정할 수도 있습니다. `work` 명령어에 전달된 커넥션 이름은 `config/queue.php` 설정 파일에 정의되어 있는 커넥션 이름과 일치해야 합니다:

    php artisan queue:work redis

You may customize your queue worker even further by only processing particular queues for a given connection. For example, if all of your emails are processed in an `emails` queue on your `redis` queue connection, you may issue the following command to start a worker that only processes only that queue:

주어진 커넥션의 특정 queue만 처리되도록 queue-큐 worker를 커스터마이즈 할 수 있습니다. 예를 들어, 모든 이메일이 `redis` queue-큐 커넥션의 `emails` queue-큐에서 처리되도록 하고자 할경우, 다음의 명령어를 사용하여 하나의 queue-큐를 처리하는 worker를 시작할 수 있습니다:

    php artisan queue:work redis --queue=emails

#### Processing A Single Job
#### 하나의 단일 Job 처리하기

The `--once` option may be used to instruct the worker to only process a single job from the queue:

`--once` 옵션은 worker 가 queue로 부터 하나의 단일 job을 처리하도록 합니다:

    php artisan queue:work --once

#### Processing All Queued Jobs & Then Exiting
#### 대기중인 모든 작업 처리 및 종료

The `--stop-when-empty` option may be used to instruct the worker to process all jobs and then exit gracefully. This option can be useful when working Laravel queues within a Docker container if you wish to shutdown the container after the queue is empty:

`--stop-when-empty` 옵션은 워커에게 모든 작업을 처리 한 다음 정상적으로 종료하도록 지시하는 데 사용할 수 있습니다. 이 옵션은 Docker 컨테이너에서 Laravel 큐가 동작 할 때 큐가 빈 후 컨테이너를 종료하려면 유용 할 수 있습니다.

    php artisan queue:work --stop-when-empty

#### Resource Considerations
#### 리소스 고려사항

Daemon queue workers do not "reboot" the framework before processing each job. Therefore, you should free any heavy resources after each job completes. For example, if you are doing image manipulation with the GD library, you should free the memory with `imagedestroy` when you are done.

데몬 큐 worker는 각 job을 처리하기 전에 프레임워크를 "reboot"하지 않습니다. 따라서 각 job이 완료되면 사용된 리소스를 해제(free)해야 합니다. 예를 들어 GD 라이브러리로 이미지를 조작하는 경우, 작업이 완료되면 `imagedestroy`를 사용하여 메모리를 해제해야 합니다.

<a name="queue-priorities"></a>
### Queue Priorities
### 큐의 우선순위

Sometimes you may wish to prioritize how your queues are processed. For example, in your `config/queue.php` you may set the default `queue` for your `redis` connection to `low`. However, occasionally you may wish to push a job to a `high` priority queue like so:

때로는 queue-큐를 어떻게 처리할 것인지 우선순위를 지정하고자 할 수도 있습니다. 예를 들어, `config/queue.php`에서 `redis` 커넥션의 기본 `queue`를 `low`로 지정하였지만, 어떤 job을 `high` 우선순위로 queue에 푸시하려는 경우입니다:

    dispatch((new Job)->onQueue('high'));

To start a worker that verifies that all of the `high` queue jobs are processed before continuing to any jobs on the `low` queue, pass a comma-delimited list of queue names to the `work` command:

`job`이 `low` queue로 처리되기 전에, `high` queue job으로 처리되는 것을 명확히 하여 worker가 시작되게 하려면, 콤마로 구분된 queue 이름의 목록을 `work` 명령어에 전달하면 됩니다:

    php artisan queue:work --queue=high,low

<a name="queue-workers-and-deployment"></a>
### Queue Workers & Deployment
### Queue worker & 배포

Since queue workers are long-lived processes, they will not pick up changes to your code without being restarted. So, the simplest way to deploy an application using queue workers is to restart the workers during your deployment process. You may gracefully restart all of the workers by issuing the `queue:restart` command:

queue worker는 장시간 동안 살아 있는 프로세스이기 때문에, 재시작하지 않으면, 코드 변경사항이 적용되지 않습니다. 따라서, queue worker를 사용하는 애플리케이션을 배포하는 가장 간단한 방법은 배포 프로세스 안에서 worker를 재시작하는 것입니다. `queue:restart` 명령어를 사용하여 모든 worker를 급작스런 중단없이 무난하게 재시작할 수 있습니다:

    php artisan queue:restart

This command will instruct all queue workers to gracefully "die" after they finish processing their current job so that no existing jobs are lost. Since the queue workers will die when the `queue:restart` command is executed, you should be running a process manager such as [Supervisor](#supervisor-configuration) to automatically restart the queue workers.

이 명령어는 현재 job이 손실되지 않도록 현재 job의 처리가 종료 된 후 전체 queue worker에 부드럽게 "종료(die)" 되도록 지시합니다. `queue:restart` 명령이 실행되면 queue worker는 종료되기 때문에, 자동으로 queue worker를 다시 시작하는 [Supervisor](#supervisor-configuration)와 같은 프로세스 매니저를 실행해야 합니다.

> {tip} The queue uses the [cache](/docs/{{version}}/cache) to store restart signals, so you should verify a cache driver is properly configured for your application before using this feature.

> {tip} 큐-queue는 [cache](/docs/{{version}}/cache)에 재시작 시그널을 저장합니다. 따라서 이 기능을 사용하기 전에 애플리케이션에 캐시 드라이버가 적절히 설정되었는지 확인해야합니다.

<a name="job-expirations-and-timeouts"></a>
### Job Expirations & Timeouts
### Job 만료 & 타임아웃

#### Job Expiration
#### Job 만료

In your `config/queue.php` configuration file, each queue connection defines a `retry_after` option. This option specifies how many seconds the queue connection should wait before retrying a job that is being processed. For example, if the value of `retry_after` is set to `90`, the job will be released back onto the queue if it has been processing for 90 seconds without being deleted. Typically, you should set the `retry_after` value to the maximum number of seconds your jobs should reasonably take to complete processing.

`config/queue.php` 설정 파일에서 각각의 queue 커넥션에는 `retry_after` 옵션을 정의합니다. 이 옵션은 job 처리를 다시 시도하기 전에 queue 커넥션에서 얼마나 대기해야 되는지를 지정합니다. 예를 들어 `retry_after` 값이 `90` 이라면, 해당 job은 처리가 완료된 후에, 90초동안 제거되지 않으면 queue에 다시 투입됩니다. 일반적으로 `retry_after` 값은 job 이 합리적으로 완료될 때까지의 최대 시간 값(초)를 지정해야합니다.

> {note} The only queue connection which does not contain a `retry_after` value is Amazon SQS. SQS will retry the job based on the [Default Visibility Timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/AboutVT.html) which is managed within the AWS console.

> {note} 아마존 SQS에서는 `retry_after` 값은 포함되어 있지 않습니다. SQS는 AWS 콘솔 안에서 관리되는 [Default Visibility Timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/AboutVT.html)에 의해서 job이 재시작됩니다.

#### Worker Timeouts
#### worker 타임아웃

The `queue:work` Artisan command exposes a `--timeout` option. The `--timeout` option specifies how long the Laravel queue master process will wait before killing off a child queue worker that is processing a job. Sometimes a child queue process can become "frozen" for various reasons, such as an external HTTP call that is not responding. The `--timeout` option removes frozen processes that have exceeded that specified time limit:

`queue:work` 아티즌 명령어는 `--timeout` 옵션도 지원합니다. `--timeout` 옵션은 라라벨의 queue 마스터 프로세스가 작업을 처리하는 child queue worker가 job을 얼마나 오래 처리하는지 지정합니다. 때로는 child queue 프로세스는 외부 HTTP 호출이 응답이 없는 것과 같은 다양한 이유로, "먹통"이 될 수 있습니다. `--timeout` 옵션은 지정된 실행 시간이 지난 뒤에, 먹통이된 프로세스를 제거합니다:

    php artisan queue:work --timeout=60

The `retry_after` configuration option and the `--timeout` CLI option are different, but work together to ensure that jobs are not lost and that jobs are only successfully processed once.

`retry_after` 설정 옵션과 `--timeout` CLI 옵션은 서로 다릅니다. 하지만 job이 한번에 성공적으로 처리될 수 있도록 하는데 함께 작동합니다.

> {note} The `--timeout` value should always be at least several seconds shorter than your `retry_after` configuration value. This will ensure that a worker processing a given job is always killed before the job is retried. If your `--timeout` option is longer than your `retry_after` configuration value, your jobs may be processed twice.

> {note} `--timeout` 값은 `retry_after` 설정 값보다 몇초 짧게 설정해야 합니다. 이렇게하면 주어진 job을 처리하는 worker가 작업 재시도 하기 전에 확실하게 중지됩니다. `--timeout` 옵션을 `retry_after` 설정 값보다 길게하면 작업이 두 번 실행되는 것입니다.

#### Worker Sleep Duration
#### Worker 잠자기 시간

When jobs are available on the queue, the worker will keep processing jobs with no delay in between them. However, the `sleep` option determines how long (in seconds) the worker will "sleep" if there are no new jobs available. While sleeping, the worker will not process any new jobs - the jobs will be processed after the worker wakes up again.

job 이 큐에서 사용가능해지면, worker는 딜레이 없이 바로 job을 처리합니다. 그렇지만 `sleep` 옵션이 설정되면 worker는 새로운 job을 처리할 때 일정 시간(초단위)동안 "잠자기 (sleep)" 하게 됩니다. 잠자기 동안에는 새로운 job을 처리하지 않습니다 - worker가 다시 깨어나면 job을 처리합니다.

    php artisan queue:work --sleep=3

<a name="supervisor-configuration"></a>
## Supervisor Configuration
## Supervisor 설정하기

#### Installing Supervisor
#### Supervisor 설치하기

Supervisor is a process monitor for the Linux operating system, and will automatically restart your `queue:work` process if it fails. To install Supervisor on Ubuntu, you may use the following command:

Supervisor는 리눅스 OS를 위한 프로세스 모니터로, `queue:work` 프로세스가 종료된 경우 자동으로 이를 재시작 할 것입니다. 우분투에서 Supervisor를 설치하기 위해서는 다음의 명령어를 사용하면 됩니다:

    sudo apt-get install supervisor

> {tip} If configuring Supervisor yourself sounds overwhelming, consider using [Laravel Forge](https://forge.laravel.com), which will automatically install and configure Supervisor for your Laravel projects.

> {tip} Supervisor 설정이 너무 어렵게 느껴진다면, 자동으로 이를 설치하고 설정할 수 있게 해주는 라라벨 프로젝트인 [라라벨 Forge](https://forge.laravel.com)를 사용하는 것을 고려해보십시오.

#### Configuring Supervisor
#### Supervisor 설정하기

Supervisor configuration files are typically stored in the `/etc/supervisor/conf.d` directory. Within this directory, you may create any number of configuration files that instruct supervisor how your processes should be monitored. For example, let's create a `laravel-worker.conf` file that starts and monitors a `queue:work` process:

Supervisor 설정 파일은 일반적으로 `/etc/supervisor/conf.d` 디렉토리에 저장되어 있습니다. 이 디렉토리 안에서 Supervisor가 어떻게 프로세스를 모니터링 할 것인지 지시하는 설정 파일을 원하는 수 만큼 생성할 수 있습니다. 예를 들어, `laravel-worker.conf` 파일을 만들고 `queue:work` 프로세스를 시작하고, 모니터링 해 보겠습니다:

    [program:laravel-worker]
    process_name=%(program_name)s_%(process_num)02d
    command=php /home/forge/app.com/artisan queue:work sqs --sleep=3 --tries=3
    autostart=true
    autorestart=true
    user=forge
    numprocs=8
    redirect_stderr=true
    stdout_logfile=/home/forge/app.com/worker.log

In this example, the `numprocs` directive will instruct Supervisor to run 8 `queue:work` processes and monitor all of them, automatically restarting them if they fail. You should change the `queue:work sqs` portion of the `command` directive to reflect your desired queue connection.

이 예제에서, `numprocs` 지시어는 Supervisor에 총 8 개의 `queue:work` 프로세스를 실행하고 이들을 모니터링하여, 이 프로세스가 죽어 있으면, 자동으로 재시작하도록 지시하고 있습니다. `command` 지시어의 `queue:work sqs` 부분을 변경하고 선택한 커넥션에 맞추도록 해야합니다.

#### Starting Supervisor
#### Supervisor 시작하기

Once the configuration file has been created, you may update the Supervisor configuration and start the processes using the following commands:

설정 파일을 생성하고 나면, 다음 명령어를 통해서 Supervisor 설정을 변경하고, 시작할 수 있습니다:

    sudo supervisorctl reread

    sudo supervisorctl update

    sudo supervisorctl start laravel-worker:*

For more information on Supervisor, consult the [Supervisor documentation](http://supervisord.org/index.html).

Supervisor에 대한 보다 자세한 정보는 [Supervisor 문서](http://supervisord.org/index.html)를 참고하십시오.

<a name="dealing-with-failed-jobs"></a>
## Dealing With Failed Jobs
## 실패한 Job 처리하기

Sometimes your queued jobs will fail. Don't worry, things don't always go as planned! Laravel includes a convenient way to specify the maximum number of times a job should be attempted. After a job has exceeded this amount of attempts, it will be inserted into the `failed_jobs` database table. To create a migration for the `failed_jobs` table, you may use the `queue:failed-table` command:

종종 여러분들의 큐 Job은 실패하기도 합니다. 걱정하지 마세요, 모든 일들이 항상 계획한 것처럼 진행되지 않습니다. 라라벨은 Job이 시도되는 최대 횟수를 지정하는 편리한 방법을 제공합니다. Job이 제한된 횟수를 초과하는 경우 이 Job들은 `failed_jobs` 데이터베이스 테이블에 추가됩니다. `queue:failed-table` 명령을 사용하면 `failed_jobs` 테이블에 대한 마이그레이션을 만들 수 있습니다:

    php artisan queue:failed-table

    php artisan migrate

Then, when running your [queue worker](#running-the-queue-worker), you should specify the maximum number of times a job should be attempted using the `--tries` switch on the `queue:work` command. If you do not specify a value for the `--tries` option, jobs will be attempted indefinitely:

그런 뒤에, [queue worker](#running-the-queue-worker)가 실행될 때, `queue:listen` 명령어의 `--tries` 스위치를 사용하여 Job이 재시도 되어야 할 최대 횟수를 지정해야합니다. `--tries` 옵션값을 지정하지 않는다면, job은 무한정 계속 시도됩니다:

    php artisan queue:work redis --tries=3

In addition, you may specify how many seconds Laravel should wait before retrying a job that has failed using the `--delay` option. By default, a job is retried immediately:

또한 `--delay` 옵션을 사용하여 실패한 작업을 다시 시도하기 전에 Laravel이 기다려야하는 시간을 지정할 수 있습니다. 기본적으로 Job은 즉시 재시도됩니다.

    php artisan queue:work redis --tries=3 --delay=3

If you would like to configure the failed job retry delay on a per-job basis, you may do so by defining a `retryAfter` property on your queued job class:

Job 마다 실패한 Job의 재시도 지연을 설정하려면 대기중인 Job 클래스에 `retryAfter` 속성을 정의하면됩니다.

    /**
     * The number of seconds to wait before retrying the job.
     *
     * @var int
     */
    public $retryAfter = 3;

<a name="cleaning-up-after-failed-jobs"></a>
### Cleaning Up After Failed Jobs
### 실패한 Job 정리하기

You may define a `failed` method directly on your job class, allowing you to perform job specific clean-up when a failure occurs. This is the perfect location to send an alert to your users or revert any actions performed by the job. The `Exception` that caused the job to fail will be passed to the `failed` method:

job 클래스에 `failed` 메소드를 정의할 수 있습니다. 이는 실패가 발생했을 때 job을 정리하는 액션을 수행할 수 있게 합니다. 이 메소드는 사용자에게 알림을 보내거나, job에서 실행된 액션을 되돌리는 역할을 하기 좋습니다. `failed`메소드에는 job에서 발생한 `Exception`이 전달됩니다:

    <?php

    namespace App\Jobs;

    use Exception;
    use App\Podcast;
    use App\AudioProcessor;
    use Illuminate\Bus\Queueable;
    use Illuminate\Queue\SerializesModels;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Contracts\Queue\ShouldQueue;

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
### Failed Job Events
### 실패한 Job에 대한 이벤트

If you would like to register an event that will be called when a job fails, you may use the `Queue::failing` method. This event is a great opportunity to notify your team via email or [Slack](https://www.slack.com). For example, we may attach a callback to this event from the `AppServiceProvider` that is included with Laravel:

Job이 실패한 경우에 호출될 이벤트를 등록하려면, `Queue::failing` 메소드를 사용하면 됩니다. 이 이벤트는 여러분의 팀에게 이메일 또는 [Slack](https://www.slack.com)과 같이 알림을 보낼 수 있습니다. 예를 들어 라라벨에 포함되어 있는 `AppServiceProvider` 에 이 이벤트 콜백을 추가해 보겠습니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Queue;
    use Illuminate\Queue\Events\JobFailed;
    use Illuminate\Support\ServiceProvider;

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
### Retrying Failed Jobs
### 실패한 Job 다시 시도하기

To view all of your failed jobs that have been inserted into your `failed_jobs` database table, you may use the `queue:failed` Artisan command:

`failed_jobs` 데이터베이스 테이블에 추가된 실패한 모든 Job들을 보기 위해서 `queue:failed` 아티즌 명령을 사용할 수 있습니다:

    php artisan queue:failed

The `queue:failed` command will list the job ID, connection, queue, and failure time. The job ID may be used to retry the failed job. For instance, to retry a failed job that has an ID of `5`, issue the following command:

`queue:failed` 명령은 Job의 ID, 커넥션, 큐, 그리고 실패 시간을 목록으로 보여줍니다. Job ID는 실패한 Job을 다시 시도하기 위해 사용될 수 있습니다. 예를 들어 `5`라는 ID를 가진 실패한 Job을 재시작할려면, 다음 명령어를 실행합니다:

    php artisan queue:retry 5

To retry all of your failed jobs, execute the `queue:retry` command and pass `all` as the ID:

실패한 모든 Job들을 다시 시도하게 하려면 `queue:retry` 명령어에 ID 대신 `all`을 전달하여 실행하면 됩니다:

    php artisan queue:retry all

If you would like to delete a failed job, you may use the `queue:forget` command:

만약 실패한 Job을 삭제하기 위해서는 `queue:forget` 명령을 사용하면 됩니다:

    php artisan queue:forget 5

To delete all of your failed jobs, you may use the `queue:flush` command:

실패한 모든 Job들을 삭제하기 위해서는 `queue:flush` 명령을 사용할 수 있습니다:

    php artisan queue:flush

<a name="ignoring-missing-models"></a>
### Ignoring Missing Models
### 누락 된 모델 무시하기

When injecting an Eloquent model into a job, it is automatically serialized before being placed on the queue and restored when the job is processed. However, if the model has been deleted while the job was waiting to be processed by a worker, your job may fail with a `ModelNotFoundException`.

Eloquent 모델을 작업에 주입 할 때 대기열에 배치되기 전에 자동으로 직렬화되고 작업이 처리 될 때 복원됩니다. 그러나 작업자가 작업을 처리하는 동안 모델이 삭제 된 경우 작업이 `ModelNotFoundException`으로 실패 할 수 있습니다.

For convenience, you may choose to automatically delete jobs with missing models by setting your job's `deleteWhenMissingModels` property to `true`:

편의상 `deleteWhenMissingModels` 속성을 `true`로 설정하여 누락 된 모델이있는 작업을 자동으로 삭제하도록 선택할 수 있습니다 :

    /**
     * Delete the job if its models no longer exist.
     *
     * @var bool
     */
    public $deleteWhenMissingModels = true;

<a name="job-events"></a>
## Job Events
## Job 이벤트

Using the `before` and `after` methods on the `Queue` [facade](/docs/{{version}}/facades), you may specify callbacks to be executed before or after a queued job is processed. These callbacks are a great opportunity to perform additional logging or increment statistics for a dashboard. Typically, you should call these methods from a [service provider](/docs/{{version}}/providers). For example, we may use the `AppServiceProvider` that is included with Laravel:

`Queue` [파사드](/docs/{{version}}/facades)의 `before` 와 `after` 메소드를 사용하여, 큐 Job이 실행되기 전 과 이후에 실행될 콜백을 지정할 수 있습니다. 이 콜백은 로깅이나 대시보드의 통계를 증가시키는것과 같은 추가적인 작업을 수행하기에 좋습니다. 일반적으로 이 메소드는 [서비스 프로바이더](/docs/{{version}}/providers)에서 호출해야합니다. 예를 들어 라라벨에 포함되어 있는 `AppServiceProvider` 에서 실행할 수 있습니다:

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

Using the `looping` method on the `Queue` [facade](/docs/{{version}}/facades), you may specify callbacks that execute before the worker attempts to fetch a job from a queue. For example, you might register a Closure to rollback any transactions that were left open by a previously failed job:

`Queue` [파사드](/docs/{{version}}/facades)의 `looping` 메소드를 사용할 때, worker가 큐에서 job을 처리하기 전에 실행할 콜백을 지정할 수 있습니다. 예를 들어, 이전에 실패한 작업이 남겨놓은 트랜젝션을 롤백하는 클로저를 등록할 수 있습니다:

    Queue::looping(function () {
        while (DB::transactionLevel() > 0) {
            DB::rollBack();
        }
    });
