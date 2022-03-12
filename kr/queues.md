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
    - [Unique Jobs](#unique-jobs)
    - [고유한 작업-Job](#unique-jobs)
- [Job Middleware](#job-middleware)
- [Job 미들웨어](#job-middleware)
    - [Rate Limiting](#rate-limiting)
    - [속도 제한](#rate-limiting)
    - [Preventing Job Overlaps](#preventing-job-overlaps)
    - [작업 중복 방지](#preventing-job-overlaps)
    - [Throttling Exceptions](#throttling-exceptions)
    - [작업량 초과 예외-Exception](#throttling-exceptions)
- [Dispatching Jobs](#dispatching-jobs)
- [Job 처리하기](#dispatching-jobs)
    - [Delayed Dispatching](#delayed-dispatching)
    - [지연시켜서 처리하기](#delayed-dispatching)
    - [Synchronous Dispatching](#synchronous-dispatching)
    - [동기식 반환](#synchronous-dispatching)
    - [Jobs & Database Transactions](#jobs-and-database-transactions)
    - [작업 및 데이터베이스 트랜잭션](#jobs-and-database-transactions)
    - [Job Chaining](#job-chaining)
    - [Job 체이닝](#job-chaining)
    - [Customizing The Queue & Connection](#customizing-the-queue-and-connection)
    - [Queue-큐 & 커넥션 커스터마이징](#customizing-the-queue-and-connection)
    - [Specifying Max Job Attempts / Timeout Values](#max-job-attempts-and-timeout)
    - [최대 재시도 횟수 / 타임아웃 시간 지정하기](#max-job-attempts-and-timeout)
    - [Error Handling](#error-handling)
    - [에러 핸들링](#error-handling)
- [Job Batching](#job-batching)
- [작업 배치](#job-batching)
    - [Defining Batchable Jobs](#defining-batchable-jobs)
    - [일괄 처리 가능한 작업 정의](#defining-batchable-jobs)
    - [Dispatching Batches](#dispatching-batches)
    - [일괄 작업 실행하기](#dispatching-batches)
    - [Adding Jobs To Batches](#adding-jobs-to-batches)
    - [배치에 작업 추가](#adding-jobs-to-batches)
    - [Inspecting Batches](#inspecting-batches)
    - [배치 검사](#inspecting-batches)
    - [Cancelling Batches](#cancelling-batches)
    - [배치 취소](#cancelling-batches)
    - [Batch Failures](#batch-failures)
    - [배치 실패](#batch-failures)
    - [Pruning Batches](#pruning-batches)
    - [배치 정리하기](#pruning-batches)
- [Queueing Closures](#queueing-closures)
- [큐잉 클로저](#queueing-closures)
- [Running The Queue Worker](#running-the-queue-worker)
- [Queue Worker 구동하기](#running-the-queue-worker)
    - [The `queue:work` Command](#the-queue-work-command)
    - [`queue:work` 명령](#the-queue-work-command)
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
    - [Job 실패 후 처리](#cleaning-up-after-failed-jobs)
    - [Retrying Failed Jobs](#retrying-failed-jobs)
    - [실패한 작업 재시도](#retrying-failed-jobs)
    - [Ignoring Missing Models](#ignoring-missing-models)
    - [누락된 모델 무시하기](#ignoring-missing-models)
    - [Storing Failed Jobs In DynamoDB](#storing-failed-jobs-in-dynamodb)
    - [DynamoDB에 실패한 작업 저장](#storing-failed-jobs-in-dynamodb)
    - [Pruning Failed Jobs](#pruning-failed-jobs)
    - [실패한 작업 정리하기](#pruning-failed-jobs)
    - [Failed Job Events](#failed-job-events)
    - [실패한 작업 이벤트](#failed-job-events)
- [Clearing Jobs From Queues](#clearing-jobs-from-queues)
- [큐-queue에서 작업 지우기](#clearing-jobs-from-queues)
- [Monitoring Your Queues](#monitoring-your-queues)
- [큐-queue 모니터링](#monitoring-your-queues)
- [Job Events](#job-events)
- [Job 이벤트](#job-events)

<a name="introduction"></a>
## Introduction
## 시작하기

While building your web application, you may have some tasks, such as parsing and storing an uploaded CSV file, that take too long to perform during a typical web request. Thankfully, Laravel allows you to easily create queued jobs that may be processed in the background. By moving time intensive tasks to a queue, your application can respond to web requests with blazing speed and provide a better user experience to your customers.

웹 애플리케이션을 개발하는 동안, 업로드 된 CSV 파일의 구문 분석 및 저장과 같은 작업을 개발 할 수 있으며, 이는 일반적인 웹 요청 중에 처리하기에는 너무 오래 걸립니다. 고맙게도 라라벨을 사용하면 백그라운드에서 처리될 수 있는 대기 작업을 쉽게 생성할 수 있습니다. 시간이 오래걸리는 작업을 큐-queue로 이동함으로써, 애플리케이션은 웹 요청을 빠른 속도로 응답하고 고객에게 더 나은 사용자 경험을 제공할 수 있습니다.

Laravel queues provide a unified queueing API across a variety of different queue backends, such as [Amazon SQS](https://aws.amazon.com/sqs/), [Redis](https://redis.io), or even a relational database.

라라벨 큐-queue은 [Amazon SQS](https://aws.amazon.com/sqs/), [Redis](https://redis.io) 또는 관계형 데이터베이스와 같은, 다양한 큐-queue를 사용 할 수 있는 통합 큐-queue API를 제공합니다.

Laravel's queue configuration options are stored in your application's `config/queue.php` configuration file. In this file, you will find connection configurations for each of the queue drivers that are included with the framework, including the database, [Amazon SQS](https://aws.amazon.com/sqs/), [Redis](https://redis.io), and [Beanstalkd](https://beanstalkd.github.io/) drivers, as well as a synchronous driver that will execute jobs immediately (for use during local development). A `null` queue driver is also included which discards queued jobs.

라라벨의 큐-queue 설정 옵션은 애플리케이션의 `config/queue.php` 설정 파일에 저장됩니다. 이 파일에는 [Amazon SQS](https://aws.amazon.com/sqs/), [Redis](https://redis.io), 데이터베이스를 포함하여, 프레임워크에 포함된 각 큐-queue 드라이버에 대한 연결 설정이 있습니다. [Beanstalkd](https://beanstalkd.github.io/) 드라이버, 작업을 즉시 실행하는 동기 드라이버(로컬 개발 중에 사용). 대기 중인 작업을 삭제하는 `null` 큐-queue 드라이버도 포함되어 있습니다.

> {tip} Laravel now offers Horizon, a beautiful dashboard and configuration system for your Redis powered queues. Check out the full [Horizon documentation](/docs/{{version}}/horizon) for more information.

> {tip} 라라벨은 이제 Redis 큐-queue를 위한 멋진 대시보드와 설정 시스템을 제공하는 Horizon을 지원합니다. 보다 자세한 사항은 [Horizon 문서](/docs/{{version}}/horizon)를 참고하십시오.

<a name="connections-vs-queues"></a>
### Connections Vs. Queues
### 커넥션 Vs. Queues-큐

Before getting started with Laravel queues, it is important to understand the distinction between "connections" and "queues". In your `config/queue.php` configuration file, there is a `connections` configuration array. This option defines the connections to backend queue services such as Amazon SQS, Beanstalk, or Redis. However, any given queue connection may have multiple "queues" which may be thought of as different stacks or piles of queued jobs.

라라벨 큐를 시작하기 전에 "연결-connection"과 "큐-queue"의 차이점을 이해하는 것이 중요합니다. `config/queue.php` 설정 파일에 `connections` 설정 배열이 있습니다. 이 옵션은 Amazon SQS, Beanstalk 또는 Redis와 같은 백엔드 큐-queue 서비스에 대한 연결을 정의합니다. 그러나 주어진 큐-queue 연결에는 큐-queue에 있는 작업의 다른 스택이나 더미로 간주될 수 있는 여러 "큐-queue"가 있을 수 있습니다.

Note that each connection configuration example in the `queue` configuration file contains a `queue` attribute. This is the default queue that jobs will be dispatched to when they are sent to a given connection. In other words, if you dispatch a job without explicitly defining which queue it should be dispatched to, the job will be placed on the queue that is defined in the `queue` attribute of the connection configuration:

`queue` 설정 파일 안에 있는 각각의 커넥션 설정 예제가 `queue` 속성을 포함하고 있다는 것을 주의하십시오. 이것은 job이 처리되기 위해 주어진 커넥션에 보내졌을 때의 기본 queue 입니다. 다시 말해, job을 어떤 queue를 통해서 처리할지 명시적으로 정의하지 않는다면, job은 커넥션 설정의 `queue` 속성에 정의되어 있는 큐에 보내집니다.

    use App\Jobs\ProcessPodcast;

    // This job is sent to the default connection's default queue...
    ProcessPodcast::dispatch();

    // This job is sent to the default connection's "emails" queue...
    ProcessPodcast::dispatch()->onQueue('emails');

Some applications may not need to ever push jobs onto multiple queues, instead preferring to have one simple queue. However, pushing jobs to multiple queues can be especially useful for applications that wish to prioritize or segment how jobs are processed, since the Laravel queue worker allows you to specify which queues it should process by priority. For example, if you push jobs to a `high` queue, you may run a worker that gives them higher processing priority:

일부 애플리케이션은 job을 여러개의 queue에 푸시 할 필요가 없으며 하나의 queue에 보내는 것이 더 나을 수도 있습니다. 하지만, 여러 queue에 job을 보내는 것은 우선 순위를 부여하고자 한다거나, 혹은 job 처리를 분할하고자하는 애플리케이션에서는 특히나 유용합니다. 라라벨의 queue worker는 우선 순위별로 처리해야하는 queue을 지정할 수 있기 때문입니다. 예를 들어, job을 `high` queue로 푸시한다면, 이 job들을 높은 우선 순위를 부여하여 처리할 수 있습니다.

    php artisan queue:work --queue=high,default

<a name="driver-prerequisites"></a>
### Driver Notes & Prerequisites
### 드라이버 주의사항과 사전준비사항

<a name="database"></a>
#### Database
#### 데이터베이스

In order to use the `database` queue driver, you will need a database table to hold the jobs. To generate a migration that creates this table, run the `queue:table` Artisan command. Once the migration has been created, you may migrate your database using the `migrate` command:

`database` queue 드라이버를 사용하기 위해서는 Job들을 담아둘 데이터베이스 테이블이 필요합니다. 이 테이블을 추가하기 위한 마이그레이션을 생성하려면 `queue:table` 아티즌 명령을 실행하면 됩니다. 마이그레이션 파일이 생성되고 나면 `migrate` 명령어를 사용하여 데이터베이스 테이블을 생성할 수 있습니다.

    php artisan queue:table

    php artisan migrate

Finally, don't forget to instruct your application to use the `database` driver by updating the `QUEUE_CONNECTION` variable in your application's `.env` file:

마지막으로 애플리케이션의 `.env` 파일에서 `QUEUE_CONNECTION` 변수를 업데이트하여 `database` 드라이버를 사용하도록 애플리케이션에 지시하는 것을 잊지 마십시오.

    QUEUE_CONNECTION=database

<a name="redis"></a>
#### Redis
#### Redis

In order to use the `redis` queue driver, you should configure a Redis database connection in your `config/database.php` configuration file.

`redis` 큐 드라이버를 사용하기 위해서는 `config/database.php` 설정 파일에서 Redis 커넥션을 설정해야 합니다.

**Redis Cluster**
**Redis 클러스터**

If your Redis queue connection uses a Redis Cluster, your queue names must contain a [key hash tag](https://redis.io/topics/cluster-spec#keys-hash-tags). This is required in order to ensure all of the Redis keys for a given queue are placed into the same hash slot:

Redis queue 커넥션이 Redis 클러스터를 사용한다면, queue 이름은 [key hash tag](https://redis.io/topics/cluster-spec#keys-hash-tags) 를 반드시 포함하고 있어야 합니다. 이것은 큐-queue 가 Redis 동일한 해시 슬롯에 부여됨을 보호 하고자 필요로 합니다.

    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => '{default}',
        'retry_after' => 90,
    ],

**Blocking**
**대기하기**

When using the Redis queue, you may use the `block_for` configuration option to specify how long the driver should wait for a job to become available before iterating through the worker loop and re-polling the Redis database.

Redis queue를 사용할 때, `block_for` 설정 옵션을 사용하여 드라이버가 작업 루프를 반복하고 Redis 데이터베이스를 다시 폴링하기 전에 job이 사용 가능하게 될 때까지 대기해야하는 시간을 지정할 수 있습니다.

Adjusting this value based on your queue load can be more efficient than continually polling the Redis database for new jobs. For instance, you may set the value to `5` to indicate that the driver should block for five seconds while waiting for a job to become available:

새로운 job을 가져오기 위해 Redis 데이터베이스를 계속 폴링하는 것보다는 큐의 부하에 따라 조절하는 게 더 효율적입니다. 예를 들면, 새로운 job이 사용 가능하게 될 때까지 드라이버가 5초를 기다려야 한다는 의미로 이 값을 5로 설정할 수 있습니다.

    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => 'default',
        'retry_after' => 90,
        'block_for' => 5,
    ],

> {note} Setting `block_for` to `0` will cause queue workers to block indefinitely until a job is available. This will also prevent signals such as `SIGTERM` from being handled until the next job has been processed.

> {note} `block_for`를 `0`으로 설정하면 작업을 사용할 수있을 때까지 큐 작업자가 무기한으로 대기됩니다. 이렇게하면 다음 작업이 처리 될 때까지 `SIGTERM`과 같은 신호가 처리되지 않습니다.

<a name="other-driver-prerequisites"></a>
#### Other Driver Prerequisites
#### 다른 큐 드라이버의 사전준비 사항들

The following dependencies are needed for the listed queue drivers. These dependencies may be installed via the Composer package manager:

나열된 큐-queue 드라이버에는 다음 의존성이 필요합니다. 이러한 의존성은 Composer 패키지 관리자를 통해 설치할 수 있습니다.

- Amazon SQS: `aws/aws-sdk-php ~3.0`
- Beanstalkd: `pda/pheanstalk ~4.0`
- Redis: `predis/predis ~1.0` 또는 phpredis PHP 확장 모듈

<a name="creating-jobs"></a>
## Creating Jobs
## Job 생성하기

<a name="generating-job-classes"></a>
### Generating Job Classes
### Job 클래스 생성하기

By default, all of the queueable jobs for your application are stored in the `app/Jobs` directory. If the `app/Jobs` directory doesn't exist, it will be created when you run the `make:job` Artisan command:

기본적으로 애플리케이션의 큐-queue에 있는 모든 작업은 `app/Jobs` 디렉토리에 저장됩니다. `app/Jobs` 디렉토리가 존재하지 않는 경우 `make:job` Artisan 명령을 실행할 때 생성됩니다.

    php artisan make:job ProcessPodcast

The generated class will implement the `Illuminate\Contracts\Queue\ShouldQueue` interface, indicating to Laravel that the job should be pushed onto the queue to run asynchronously.

생성된 클래스는 Job이 queue를 통해서 비동기적으로 실행되어야 된다는 것을 나타내는, `Illuminate\Contracts\Queue\ShouldQueue` 인터페이스를 구현하고 있습니다.

> {tip} Job stubs may be customized using [stub publishing](/docs/{{version}}/artisan#stub-customization).

> {tip} 작업 스텁은 [스텁 게시하기](/docs/{{version}}/artisan#stub-customization)를 사용하여 사용자 정의할 수 있습니다.

<a name="class-structure"></a>
### Class Structure
### 클래스 구조

Job classes are very simple, normally containing only a `handle` method that is invoked when the job is processed by the queue. To get started, let's take a look at an example job class. In this example, we'll pretend we manage a podcast publishing service and need to process the uploaded podcast files before they are published:

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

In this example, note that we were able to pass an [Eloquent model](/docs/{{version}}/eloquent) directly into the queued job's constructor. Because of the `SerializesModels` trait that the job is using, Eloquent models and their loaded relationships will be gracefully serialized and unserialized when the job is processing.

이 예제에서 [Eloquent 모델](/docs/{{version}}/eloquent)을 대기 중인 작업의 생성자에 직접 전달할 수 있었습니다. 작업이 사용하는 `SerializesModels` 특성-trait 때문에 Eloquent 모델과 로드된 관계는 작업이 처리될 때 정상적으로 직렬화되고 직렬화 해제됩니다.

If your queued job accepts an Eloquent model in its constructor, only the identifier for the model will be serialized onto the queue. When the job is actually handled, the queue system will automatically re-retrieve the full model instance and its loaded relationships from the database. This approach to model serialization allows for much smaller job payloads to be sent to your queue driver.

큐-queue에 있는 작업이 생성자에서 Eloquent 모델을 수락하면 모델의 식별자만 큐-queue에 직렬화됩니다. 작업이 실제로 처리되면 큐-queue 시스템은 데이터베이스에서 전체 모델 인스턴스와 로드된 관계를 자동으로 다시 검색합니다. 모델 직렬화에 대한 이 접근 방식을 사용하면 훨씬 더 작은 작업 페이로드를 큐-queue 드라이버로 보낼 수 있습니다.

<a name="handle-method-dependency-injection"></a>
#### `handle` Method Dependency Injection
#### `handle` 메서드 의존성 주입

The `handle` method is invoked when the job is processed by the queue. Note that we are able to type-hint dependencies on the `handle` method of the job. The Laravel [service container](/docs/{{version}}/container) automatically injects these dependencies.

`handle` 메서드는 작업이 큐-queue에서 처리될 때 호출됩니다. 작업의 `handle` 메서드에 대한 타입 힌트 의존성을 확인할 수 있습니다. 라라벨 [서비스 컨테이너](/docs/{{version}}/container)는 이러한 의존성을 자동으로 주입합니다.

If you would like to take total control over how the container injects dependencies into the `handle` method, you may use the container's `bindMethod` method. The `bindMethod` method accepts a callback which receives the job and the container. Within the callback, you are free to invoke the `handle` method however you wish. Typically, you should call this method from the `boot` method of your `App\Providers\AppServiceProvider` [service provider](/docs/{{version}}/providers):

컨테이너가 `handle` 메서드에 의존성을 주입하는 방법을 완전히 제어하려면 컨테이너의 `bindMethod` 메서드를 사용할 수 있습니다. `bindMethod` 메소드는 작업과 컨테이너를 수신하는 콜백을 입력받습니다. 콜백 내에서 원하는 대로 `handle` 메서드를 호출할 수 있습니다. 일반적으로 `App\Providers\AppServiceProvider` [서비스 컨테이너](/docs/{{version}}/providers)의 `boot` 메소드에서 이 메소드를 호출해야 합니다.

    use App\Jobs\ProcessPodcast;
    use App\Services\AudioProcessor;

    $this->app->bindMethod([ProcessPodcast::class, 'handle'], function ($job, $app) {
        return $job->handle($app->make(AudioProcessor::class));
    });

> {note} Binary data, such as raw image contents, should be passed through the `base64_encode` function before being passed to a queued job. Otherwise, the job may not properly serialize to JSON when being placed on the queue.

> {note} Raw 이미지와 같은 바이너리 데이터의 경우, 큐를 통해서 처리되기 전에 `base64_encode` 함수가 적용된 상태로 전달되어야 합니다. 그렇지 않으면 Job이 큐에 입력 될 때 JSON으로 제대로 serialize 되지 않을 수 있습니다.

<a name="handling-relationships"></a>
#### Handling Relationships
#### 관계 처리

Because loaded relationships also get serialized, the serialized job string can sometimes become quite large. To prevent relations from being serialized, you can call the `withoutRelations` method on the model when setting a property value. This method will return an instance of the model without its loaded relationships:

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
### Unique Jobs
### 고유한 작업-Job

> {note} Unique jobs require a cache driver that supports [locks](/docs/{{version}}/cache#atomic-locks). Currently, the `memcached`, `redis`, `dynamodb`, `database`, `file`, and `array` cache drivers support atomic locks. In addition, unique job constraints do not apply to jobs within batches.

> {note} 고유한 작업에는 [locks](/docs/{{version}}/cache#atomic-locks)를 지원하는 캐시 드라이버가 필요합니다. 현재 `memcached`, `redis`, `dynamodb`, `database`, `file` 및 `array` 캐시 드라이버는 원자 잠금을 지원합니다. 또한 일괄-Batch 작업 내 작업에는 고유 작업 제약 조건이 적용되지 않습니다.

Sometimes, you may want to ensure that only one instance of a specific job is on the queue at any point in time. You may do so by implementing the `ShouldBeUnique` interface on your job class. This interface does not require you to define any additional methods on your class:

때로 특정 작업의 인스턴스 하나만 큐-queue에 존재해야하는 경우가 있습니다. 작업 클래스에 `ShouldBeUnique` 인터페이스를 구현하면 됩니다. 이 인터페이스에서는 클래스에 추가 메서드를 정의할 필요가 없습니다.

    <?php

    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Contracts\Queue\ShouldBeUnique;

    class UpdateSearchIndex implements ShouldQueue, ShouldBeUnique
    {
        ...
    }

In the example above, the `UpdateSearchIndex` job is unique. So, the job will not be dispatched if another instance of the job is already on the queue and has not finished processing.

위의 예제에서 `UpdateSearchIndex` 작업은 고유합니다. 따라서 작업의 다른 인스턴스가 이미 큐-queue에 있고 처리가 완료되지 않은 경우 작업이 디스패치되지 않습니다.

In certain cases, you may want to define a specific "key" that makes the job unique or you may want to specify a timeout beyond which the job no longer stays unique. To accomplish this, you may define `uniqueId` and `uniqueFor` properties or methods on your job class:

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

In the example above, the `UpdateSearchIndex` job is unique by a product ID. So, any new dispatches of the job with the same product ID will be ignored until the existing job has completed processing. In addition, if the existing job is not processed within one hour, the unique lock will be released and another job with the same unique key can be dispatched to the queue.

위의 예제에서 `UpdateSearchIndex` 작업은 제품 ID별로 고유합니다. 따라서 기존 작업이 처리를 완료할 때까지 동일한 제품 ID를 가진 작업의 새 디스패치가 무시됩니다. 또한 기존 작업이 1시간 이내에 처리되지 않으면 고유 잠금이 해제되고 동일한 고유 키를 가진 다른 작업이 큐-queue에 디스패치될 수 있습니다.

<a name="keeping-jobs-unique-until-processing-begins"></a>
#### Keeping Jobs Unique Until Processing Begins
#### 처리가 시작될 때까지 고유한 작업 유지

By default, unique jobs are "unlocked" after a job completes processing or fails all of its retry attempts. However, there may be situations where you would like your job to unlock immediately before it is processed. To accomplish this, your job should implement the `ShouldBeUniqueUntilProcessing` contract instead of the `ShouldBeUnique` contract:

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
#### Unique Job Locks
#### 고유 작업 잠금

Behind the scenes, when a `ShouldBeUnique` job is dispatched, Laravel attempts to acquire a [lock](/docs/{{version}}/cache#atomic-locks) with the `uniqueId` key. If the lock is not acquired, the job is not dispatched. This lock is released when the job completes processing or fails all of its retry attempts. By default, Laravel will use the default cache driver to obtain this lock. However, if you wish to use another driver for acquiring the lock, you may define a `uniqueVia` method that returns the cache driver that should be used:

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

> {tip} If you only need to limit the concurrent processing of a job, use the [`WithoutOverlapping`](/docs/{{version}}/queues#preventing-job-overlaps) job middleware instead.

> {tip} 작업의 동시 처리만 제한해야 하는 경우 대신 [`WithoutOverlapping`](/docs/{{version}}/queues#preventing-job-overlaps) 작업 미들웨어를 사용하세요.

<a name="job-middleware">< /a>-
## Job Middleware
## 작업 미들웨어

Job middleware allow you to wrap custom logic around the execution of queued jobs, reducing boilerplate in the jobs themselves. For example, consider the following `handle` method which leverages Laravel's Redis rate limiting features to allow only one job to process every five seconds:

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

While this code is valid, the implementation of the `handle` method becomes noisy since it is cluttered with Redis rate limiting logic. In addition, this rate limiting logic must be duplicated for any other jobs that we want to rate limit.

이 코드는 동작은 하지만, `handle` 메소드의 구현은 Redis 속도 제한 로직으로 지저분해집니다. 또한 이 속도 제한 로직은 속도를 제한하려는 다른 Job에 복제해야합니다.

Instead of rate limiting in the handle method, we could define a job middleware that handles rate limiting. Laravel does not have a default location for job middleware, so you are welcome to place job middleware anywhere in your application. In this example, we will place the middleware in an `app/Jobs/Middleware` directory:

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

As you can see, like [route middleware](/docs/{{version}}/middleware), job middleware receive the job being processed and a callback that should be invoked to continue processing the job.

보시다시피 [route 미들웨어](/docs/{{version}}/middleware)와 같이 Job 미들웨어는 처리중인 Job과 Job 처리를 계속하기 위해 호출해야하는 콜백을 받습니다.

After creating job middleware, they may be attached to a job by returning them from the job's `middleware` method. This method does not exist on jobs scaffolded by the `make:job` Artisan command, so you will need to manually add it to your job class:

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

> {tip} Job middleware can also be assigned to queueable event listeners, mailables, and notifications.

> {tip} job 미들웨어는 큐에 넣을 수 있는 이벤트 리스너, 메일링, 알림에도 할당할 수 있습니다.

<a name="rate-limiting"></a>
### Rate Limiting
### 속도 제한

Although we just demonstrated how to write your own rate limiting job middleware, Laravel actually includes a rate limiting middleware that you may utilize to rate limit jobs. Like [route rate limiters](/docs/{{version}}/routing#defining-rate-limiters), job rate limiters are defined using the `RateLimiter` facade's `for` method.

속도 제한 작업 미들웨어를 작성하는 방법을 방금 시연했지만, 라라벨에는 실제로 속도 제한 작업에 사용할 수 있는 속도 제한 미들웨어가 포함되어 있습니다. [route rate limiters](/docs/{{version}}/routing#defining-rate-limiters)와 마찬가지로 작업 속도 제한기는 `RateLimiter` 파사드의 `for` 메서드를 사용하여 정의됩니다.

For example, you may wish to allow users to backup their data once per hour while imposing no such limit on premium customers. To accomplish this, you may define a `RateLimiter` in the `boot` method of your `AppServiceProvider`:

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

In the example above, we defined an hourly rate limit; however, you may easily define a rate limit based on minutes using the `perMinute` method. In addition, you may pass any value you wish to the `by` method of the rate limit; however, this value is most often used to segment rate limits by customer:

위의 예에서는 시간당 비율 제한을 정의했습니다. 그러나 `perMinute` 메서드를 사용해서, 분을 기준으로 속도 제한을 쉽게 정의할 수 있습니다. 또한 속도 제한의 `by` 메서드에 원하는 값을 전달할 수 있습니다. 그러나 이 값은 고객별로 비율 제한을 분할하는 데 가장 자주 사용됩니다.

    return Limit::perMinute(50)->by($job->user->id);

Once you have defined your rate limit, you may attach the rate limiter to your backup job using the `Illuminate\Queue\Middleware\RateLimited` middleware. Each time the job exceeds the rate limit, this middleware will release the job back to the queue with an appropriate delay based on the rate limit duration.

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

Releasing a rate limited job back onto the queue will still increment the job's total number of `attempts`. You may wish to tune your `tries` and `maxExceptions` properties on your job class accordingly. Or, you may wish to use the [`retryUntil` method](#time-based-attempts) to define the amount of time until the job should no longer be attempted.

비율이 제한된 작업을 다시 큐-queue로 보내도, 작업의 총 `attempts` 횟수는 계속 증가합니다. 그에 따라 작업 클래스에서 `tries` 및 `maxExceptions` 속성을 조정할 수 있습니다. 또는 [`retryUntil` 메서드](#time-based-attempts)를 사용하여 작업이 더 이상 시도되지 않아야 할 때까지의 시간을 정의할 수 있습니다.

If you do not want a job to be retried when it is rate limited, you may use the `dontRelease` method:

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

> {tip} If you are using Redis, you may use the `Illuminate\Queue\Middleware\RateLimitedWithRedis` middleware, which is fine-tuned for Redis and more efficient than the basic rate limiting middleware.

> {tip} Redis를 사용하신다면 `Illuminate\Queue\Middleware\RateLimitedWithRedis` 미들웨어를 사용하시면 됩니다. 이 미들웨어는 Redis에 맞게 미세 조정되어 있으며 기본 속도 제한 미들웨어보다 더 효율적입니다.

<a name="preventing-job-overlaps"></a>
### Preventing Job Overlaps
### 작업 중복 방지

Laravel includes an `Illuminate\Queue\Middleware\WithoutOverlapping` middleware that allows you to prevent job overlaps based on an arbitrary key. This can be helpful when a queued job is modifying a resource that should only be modified by one job at a time.

라라벨에는 임의의 키를 기반으로 작업이 겹치는 것을 방지할 수 있는 `Illuminate\Queue\Middleware\WithoutOverlapping` 미들웨어가 포함되어 있습니다. 이는 큐-queue에 있는 작업이 한 번에 하나의 작업에 의해서만 변경되어야 하는 리소스를 관리할 때 유용할 수 있습니다.

For example, let's imagine you have a queued job that updates a user's credit score and you want to prevent credit score update job overlaps for the same user ID. To accomplish this, you can return the `WithoutOverlapping` middleware from your job's `middleware` method:

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

Any overlapping jobs will be released back to the queue. You may also specify the number of seconds that must elapse before the released job will be attempted again:

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

If you wish to immediately delete any overlapping jobs so that they will not be retried, you may use the `dontRelease` method:

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

The `WithoutOverlapping` middleware is powered by Laravel's atomic lock feature. Sometimes, your job may unexpectedly fail or timeout in such a way that the lock is not released. Therefore, you may explicitly define a lock expiration time using the `expireAfter` method. For example, the example below will instruct Laravel to release the `WithoutOverlapping` lock three minutes after the job has started processing:

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

> {note} The `WithoutOverlapping` middleware requires a cache driver that supports [locks](/docs/{{version}}/cache#atomic-locks). Currently, the `memcached`, `redis`, `dynamodb`, `database`, `file`, and `array` cache drivers support atomic locks.

> {note} `WithoutOverlapping` 미들웨어는 [locks](/docs/{{version}}/cache#atomic-locks)를 지원하는 캐시 드라이버가 필요합니다. 현재 `memcached`, `redis`, `dynamodb`, `database`, `file` 및 `array` 캐시 드라이버는 원자 잠금을 지원합니다.

<a name="throttling-exceptions"></a>
### Throttling Exceptions
### 작업량 초과 예외-Exception

Laravel includes a `Illuminate\Queue\Middleware\ThrottlesExceptions` middleware that allows you to throttle exceptions. Once the job throws a given number of exceptions, all further attempts to execute the job are delayed until a specified time interval lapses. This middleware is particularly useful for jobs that interact with third-party services that are unstable.

라라벨에는 예외를 조절할 수 있는 `Illuminate\Queue\Middleware\ThrottlesExceptions` 미들웨어가 포함되어 있습니다. 작업에서 지정된 수의 예외가 발생하면 작업을 실행하려는 모든 추가 시도는 지정된 시간 간격이 경과할 때까지 지연됩니다. 이 미들웨어는 불안정한 타사 서비스와 상호 작용하는 작업에 특히 유용합니다.

For example, let's imagine a queued job that interacts with a third-party API that begins throwing exceptions. To throttle exceptions, you can return the `ThrottlesExceptions` middleware from your job's `middleware` method. Typically, this middleware should be paired with a job that implements [time based attempts](#time-based-attempts):

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

The first constructor argument accepted by the middleware is the number of exceptions the job can throw before being throttled, while the second constructor argument is the number of minutes that should elapse before the job is attempted again once it has been throttled. In the code example above, if the job throws 10 exceptions within 5 minutes, we will wait 5 minutes before attempting the job again.

미들웨어의 생성자에서 입력받는 첫 번째 인수는 작업이 조절되기 전에 던질 수 있는 예외 수이고, 두 번째 생성자 인수는 작업이 조절된 후 다시 시도되기 전에 경과해야 하는 시간(분)입니다. 위의 코드 예제에서 작업에서 5분 이내에 10개의 예외가 발생하면 작업을 다시 시도하기 전에 5분을 기다립니다.

When a job throws an exception but the exception threshold has not yet been reached, the job will typically be retried immediately. However, you may specify the number of minutes such a job should be delayed by calling the `backoff` method when attaching the middleware to the job:

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

Internally, this middleware uses Laravel's cache system to implement rate limiting, and the job's class name is utilized as the cache "key". You may override this key by calling the `by` method when attaching the middleware to your job. This may be useful if you have multiple jobs interacting with the same third-party service and you would like them to share a common throttling "bucket":

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

> {tip} If you are using Redis, you may use the `Illuminate\Queue\Middleware\ThrottlesExceptionsWithRedis` middleware, which is fine-tuned for Redis and more efficient than the basic exception throttling middleware.

> {tip} Redis를 사용하신다면 `Illuminate\Queue\Middleware\ThrottlesExceptionsWithRedis` 미들웨어를 사용하시면 됩니다. 이 미들웨어는 Redis에 맞게 미세 조정되고 기본 예외 조절 미들웨어보다 더 효율적입니다.

<a name="dispatching-jobs"></a>
## Dispatching Jobs
## Job 처리하기

Once you have written your job class, you may dispatch it using the `dispatch` method on the job itself. The arguments passed to the `dispatch` method will be given to the job's constructor:

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

If you would like to conditionally dispatch a job, you may use the `dispatchIf` and `dispatchUnless` methods:

조건부 Job 처리를 원한다면, `dispatchIf`와 `dispatchUnless` 메소드를 사용할 수 있습니다.

    ProcessPodcast::dispatchIf($accountActive, $podcast);

    ProcessPodcast::dispatchUnless($accountSuspended, $podcast);

<a name="delayed-dispatching"></a>
### Delayed Dispatching
### 지연시켜서 처리하기

If you would like to specify that a job should not be immediately available for processing by a queue worker, you may use the `delay` method when dispatching the job. For example, let's specify that a job should not be available for processing until 10 minutes after it has been dispatched:

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

> {note} The Amazon SQS queue service has a maximum delay time of 15 minutes.

> {note} 아마존 SQS 큐 서비스는 지연시간이 최대 15분을 넘을 수 없습니다.

<a name="dispatching-after-the-response-is-sent-to-browser"></a>
#### Dispatching After The Response Is Sent To Browser
#### 브라우저로 응답을 보낸 후 처리

Alternatively, the `dispatchAfterResponse` method delays dispatching a job until after the HTTP response is sent to the user's browser. This will still allow the user to begin using the application even though a queued job is still executing. This should typically only be used for jobs that take about a second, such as sending an email. Since they are processed within the current HTTP request, jobs dispatched in this fashion do not require a queue worker to be running in order for them to be processed:

또는 `dispatchAfterResponse` 메서드는 HTTP 응답이 사용자의 브라우저로 전송될 때까지 작업 처리를 지연시킵니다. 이렇게 하면 큐-queue에 있는 작업이 계속 실행 중이더라도 사용자가 애플리케이션을 사용할 수 있습니다. 이것은 일반적으로 이메일 보내기와 같이 1초 정도 걸리는 작업에만 사용해야 합니다. 현재 HTTP 요청 내에서 처리되기 때문에 이러한 방식으로 디스패치된 작업은 처리를 위해 큐-queue 작업자를 실행할 필요가 없습니다.

    use App\Jobs\SendNotification;

    SendNotification::dispatchAfterResponse();

You may also `dispatch` a closure and chain the `afterResponse` method onto the `dispatch` helper to execute a closure after the HTTP response has been sent to the browser:

또한 클로저를 `dispatch`하고 HTTP 응답이 브라우저로 전송된 후 클로저를 실행하기 위해 `afterResponse` 메소드를 `dispatch` 헬퍼에 연결할 수 있습니다.

    use App\Mail\WelcomeMessage;
    use Illuminate\Support\Facades\Mail;

    dispatch(function () {
        Mail::to('taylor@example.com')->send(new WelcomeMessage);
    })->afterResponse();

<a name="synchronous-dispatching"></a>
### Synchronous Dispatching
### 동기식 반환

If you would like to dispatch a job immediately (synchronously), you may use the `dispatchSync` method. When using this method, the job will not be queued and will be executed immediately within the current process:

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
### Jobs & Database Transactions
### 작업 및 데이터베이스 트랜잭션

While it is perfectly fine to dispatch jobs within database transactions, you should take special care to ensure that your job will actually be able to execute successfully. When dispatching a job within a transaction, it is possible that the job will be processed by a worker before the transaction has committed. When this happens, any updates you have made to models or database records during the database transaction may not yet be reflected in the database. In addition, any models or database records created within the transaction may not exist in the database.

데이터베이스 트랜잭션 내에서 작업을 디스패치하는 것은 좋은 방식이지만, 작업이 실제로 성공적으로 실행될 수 있도록 특별히 주의해야 합니다. 트랜잭션 내에서 작업을 디스패치할 때, 트랜잭션이 커밋되기 전에 작업자-wotker가 작업을 처리할 수 있습니다. 이 경우 데이터베이스 트랜잭션 중 모델 또는 데이터베이스 레코드에 대한 업데이트가 아직 데이터베이스에 반영되지 않았을 수 있습니다. 또한 트랜잭션 내에서 생성된 모델 또는 데이터베이스 레코드는 데이터베이스에 존재하지 않을 수 있습니다.

Thankfully, Laravel provides several methods of working around this problem. First, you may set the `after_commit` connection option in your queue connection's configuration array:

고맙게도 라라벨은 이 문제를 해결할 수 있는 몇 가지 방법을 제공합니다. 먼저 큐-queue 연결의 설정 배열에서 `after_commit` 연결 옵션을 설정할 수 있습니다.

    'redis' => [
        'driver' => 'redis',
        // ...
        'after_commit' => true,
    ],

When the `after_commit` option is `true`, you may dispatch jobs within database transactions; however, Laravel will wait until all open database transactions have been committed before actually dispatching the job. Of course, if no database transactions are currently open, the job will be dispatched immediately.

`after_commit` 옵션이 `true`이면 데이터베이스 트랜잭션 내에서 작업을 디스패치할 수 있습니다. 그러나 라라벨은 실제로 작업을 디스패치하기 전에 열려 있는 모든 데이터베이스 트랜잭션이 커밋될 때까지 기다립니다. 물론 현재 열려 있는 데이터베이스 트랜잭션이 없으면 작업이 즉시 발송됩니다.

If a transaction is rolled back due to an exception that occurs during the transaction, the dispatched jobs that were dispatched during that transaction will be discarded.

트랜잭션 중에 발생한 예외로 인해 트랜잭션이 롤백되면 해당 트랜잭션 중에 디스패치된 디스패치 작업은 버려집니다.

> {tip} Setting the `after_commit` configuration option to `true` will also cause any queued event listeners, mailables, notifications, and broadcast events to be dispatched after all open database transactions have been committed.

> {tip} `after_commit` 설정 옵션을 `true`로 설정하면 열려 있는 모든 데이터베이스 트랜잭션이 커밋된 후 대기 중인 이벤트 리스너, 메일 가능 항목, 알림 및 브로드캐스트 이벤트도 전달됩니다.

<a name="specifying-commit-dispatch-behavior-inline"></a>
#### Specifying Commit Dispatch Behavior Inline
#### 커밋 디스패치 동작 인라인 지정

If you do not set the `after_commit` queue connection configuration option to `true`, you may still indicate that a specific job should be dispatched after all open database transactions have been committed. To accomplish this, you may chain the `afterCommit` method onto your dispatch operation:

`after_commit` 큐-queue 연결 설정 옵션을 `true`로 설정하지 않으면, 열려 있는 모든 데이터베이스 트랜잭션이 커밋된 후 특정 작업이 발송되어야 한다고 표시할 수 있습니다. 이를 수행하기 위해 `afterCommit` 메서드를 디스패치 작업에 연결할 수 있습니다.

    use App\Jobs\ProcessPodcast;

    ProcessPodcast::dispatch($podcast)->afterCommit();

Likewise, if the `after_commit` configuration option is set to `true`, you may indicate that a specific job should be dispatched immediately without waiting for any open database transactions to commit:

마찬가지로, `after_commit` 설정 옵션이 `true`로 설정되어 있으면, 열려 있는 데이터베이스 트랜잭션이 커밋될 때까지 기다리지 않고 특정 작업이 즉시 발송되어야 함을 나타낼 수 있습니다.

    ProcessPodcast::dispatch($podcast)->beforeCommit();

<a name="job-chaining"></a>
### Job Chaining
### Job 체이닝

Job chaining allows you to specify a list of queued jobs that should be run in sequence after the primary job has executed successfully. If one job in the sequence fails, the rest of the jobs will not be run. To execute a queued job chain, you may use the `chain` method provided by the `Bus` facade. Laravel's command bus is a lower level component that queued job dispatching is built on top of:

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

In addition to chaining job class instances, you may also chain closures:

작업 클래스 인스턴스를 연결하는 것 외에도 클로저를 연결할 수도 있습니다.

    Bus::chain([
        new ProcessPodcast,
        new OptimizePodcast,
        function () {
            Podcast::update(...);
        },
    ])->dispatch();

> {note} Deleting jobs using the `$this->delete()` method within the job will not prevent chained jobs from being processed. The chain will only stop executing if a job in the chain fails.

> {참고} 작업 내에서 `$this->delete()` 메서드를 사용하여 작업을 삭제해도 연결된 작업이 처리되지 않습니다. 체인은 체인의 작업이 실패하는 경우에만 실행을 중지합니다.

<a name="chain-connection-queue"></a>
#### Chain Connection & Queue
#### Connection과 Queue 체이닝

If you would like to specify the connection and queue that should be used for the chained jobs, you may use the `onConnection` and `onQueue` methods. These methods specify the queue connection and queue name that should be used unless the queued job is explicitly assigned a different connection / queue:

연결된 작업에 사용해야 하는 연결 및 큐-queue을 지정하려면 `onConnection` 및 `onQueue` 메서드를 사용할 수 있습니다. 다음 메서드는 큐-queue에 있는 작업에 다른 연결 큐-queue이 명시적으로 할당되지 않는 한 사용해야 하는 큐-queue 연결 및 큐-queue 이름을 지정합니다.

    Bus::chain([
        new ProcessPodcast,
        new OptimizePodcast,
        new ReleasePodcast,
    ])->onConnection('redis')->onQueue('podcasts')->dispatch();

<a name="chain-failures"></a>
#### Chain Failures
#### 체인 실패

When chaining jobs, you may use the `catch` method to specify a closure that should be invoked if a job within the chain fails. The given callback will receive the `Throwable` instance that caused the job failure:

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
### Customizing The Queue & Connection
### Queue-큐 & 커넥션 커스터마이징

<a name="dispatching-to-a-particular-queue"></a>
#### Dispatching To A Particular Queue
#### 특정 queue-큐로 처리하기

By pushing jobs to different queues, you may "categorize" your queued jobs and even prioritize how many workers you assign to various queues. Keep in mind, this does not push jobs to different queue "connections" as defined by your queue configuration file, but only to specific queues within a single connection. To specify the queue, use the `onQueue` method when dispatching the job:

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

Alternatively, you may specify the job's queue by calling the `onQueue` method within the job's constructor:

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
#### Dispatching To A Particular Connection
#### 특정 커넥션으로 처리하기

If your application interacts with multiple queue connections, you may specify which connection to push a job to using the `onConnection` method:

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

You may chain the `onConnection` and `onQueue` methods together to specify the connection and the queue for a job:

`onConnection` 및 `onQueue` 메서드를 함께 연결하여 작업에 대한 연결 및 큐-queue을 지정할 수 있습니다.

    ProcessPodcast::dispatch($podcast)
                  ->onConnection('sqs')
                  ->onQueue('processing');

Alternatively, you may specify the job's connection by calling the `onConnection` method within the job's constructor:

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
### Specifying Max Job Attempts / Timeout Values
### 최대 재시도 횟수 / 타임아웃 시간 지정하기

<a name="max-attempts"></a>
#### Max Attempts
#### 최대 재시도 횟수

If one of your queued jobs is encountering an error, you likely do not want it to keep retrying indefinitely. Therefore, Laravel provides various ways to specify how many times or for how long a job may be attempted.

대기 중인 작업 중 하나에 오류가 발생하는 경우 계속해서 재시도하는 것을 무기한으로 원하지 않을 수 있습니다. 따라서 라라벨은 작업을 시도할 수 있는 횟수 또는 기간을 지정하는 다양한 방법을 제공합니다.

One approach to specifying the maximum number of times a job may be attempted is via the `--tries` switch on the Artisan command line. This will apply to all jobs processed by the worker unless the job being processed specifies a more specific number of times it may be attempted:

작업을 시도할 수 있는 최대 횟수를 지정하는 한 가지 방법은 Artisan 명령줄의 `--tries` 스위치를 사용하는 것입니다. 이 옵션은 처리 중인 작업이 더 구체적인 시도 횟수를 지정하지 않는 한 worker가 처리하는 모든 작업에 적용됩니다.

    php artisan queue:work --tries=3

If a job exceeds its maximum number of attempts, it will be considered a "failed" job. For more information on handling failed jobs, consult the [failed job documentation](#dealing-with-failed-jobs).

작업이 최대 시도 횟수를 초과하면 "실패한" 작업으로 간주됩니다. 실패한 작업 처리에 대한 자세한 내용은 [실패한 작업 문서](#dealing-with-failed-jobs)를 참조하십시오.

You may take a more granular approach by defining the maximum number of times a job may be attempted on the job class itself. If the maximum number of attempts is specified on the job, it will take precedence over the `--tries` value provided on the command line:

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
#### Time Based Attempts
#### 시간 기반의 재시도

As an alternative to defining how many times a job may be attempted before it fails, you may define a time at which the job should no longer be attempted. This allows a job to be attempted any number of times within a given time frame. To define the time at which a job should no longer be attempted, add a `retryUntil` method to your job class. This method should return a `DateTime` instance:

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

> {tip} You may also define a `tries` property or `retryUntil` method on your [queued event listeners](/docs/{{version}}/events#queued-event-listeners).

> {tip} [대기 중인 이벤트 리스너](/docs/{{version}}/events#queued-event-listeners)에서 `tries` 속성이나 `retryUntil` 메서드를 정의할 수도 있습니다.

<a name="max-exceptions"></a>
#### Max Exceptions
#### 최대 예외

Sometimes you may wish to specify that a job may be attempted many times, but should fail if the retries are triggered by a given number of unhandled exceptions (as opposed to being released by the `release` method directly). To accomplish this, you may define a `maxExceptions` property on your job class:

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

In this example, the job is released for ten seconds if the application is unable to obtain a Redis lock and will continue to be retried up to 25 times. However, the job will fail if three unhandled exceptions are thrown by the job.

이 예제에서, 애플리케이션이 Redis lock을 확보 할 수없는 경우 job은 10초 동안 허용되며 최대 25회 재시도됩니다. 그러나 처리되지 않은 예외가 3회 발생하면 작업이 실패합니다.

<a name="timeout"></a>
#### Timeout
#### 타임아웃

> {note} The `pcntl` PHP extension must be installed in order to specify job timeouts.

> {note} 작업 시간 제한을 지정하려면 `pcntl` PHP 확장기능을 설치해야합니다

Often, you know roughly how long you expect your queued jobs to take. For this reason, Laravel allows you to specify a "timeout" value. If a job is processing for longer than the number of seconds specified by the timeout value, the worker processing the job will exit with an error. Typically, the worker will be restarted automatically by a [process manager configured on your server](#supervisor-configuration).

대기 중인 작업에 소요되는 시간을 대략적으로 알고 있는 경우가 많습니다. 이러한 이유로 라라벨에서는 "timeout" 값을 지정할 수 있습니다. 작업이 시간 초과 값으로 지정된 시간(초)보다 오래 처리되는 경우, 작업을 처리하는 작업자는 오류와 함께 종료됩니다. 일반적으로 작업자는 [서버에 설정된 프로세스 관리자](#supervisor-configuration)에 의해 자동으로 다시 시작됩니다.

The maximum number of seconds that jobs can run may be specified using the `--timeout` switch on the Artisan command line:

작업을 실행할 수 있는 최대 시간(초)은 Artisan 명령줄의 `--timeout` 스위치를 사용하여 지정할 수 있습니다.

    php artisan queue:work --timeout=30

If the job exceeds its maximum attempts by continually timing out, it will be marked as failed.

작업이 계속 시간 초과되어 최대 시도 횟수를 초과하면, 실패한 것으로 표시됩니다.

You may also define the maximum number of seconds a job should be allowed to run on the job class itself. If the timeout is specified on the job, it will take precedence over any timeout specified on the command line:

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

Sometimes, IO blocking processes such as sockets or outgoing HTTP connections may not respect your specified timeout. Therefore, when using these features, you should always attempt to specify a timeout using their APIs as well. For example, when using Guzzle, you should always specify a connection and request timeout value.

때로는 소켓이나 나가는 HTTP 연결과 같은 IO 차단 프로세스가 지정된 시간 제한을 따르지 않을 수 있습니다. 따라서 이러한 기능을 사용할 때 항상 해당 API를 사용하여 시간 제한을 지정해야합니다. 예를 들어 Guzzle을 사용할 때는 항상 연결 및 요청 시간 제한 값을 지정해야합니다.

<a name="failing-on-timeout"></a>
#### Failing On Timeout
#### 시간 초과 시 실패

If you would like to indicate that a job should be marked as [failed](#dealing-with-failed-jobs) on timeout, you may define the `$failOnTimeout` property on the job class:

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
### Error Handling
### 에러 핸들링

If an exception is thrown while the job is being processed, the job will automatically be released back onto the queue so it may be attempted again. The job will continue to be released until it has been attempted the maximum number of times allowed by your application. The maximum number of attempts is defined by the `--tries` switch used on the `queue:work` Artisan command. Alternatively, the maximum number of attempts may be defined on the job class itself. More information on running the queue worker [can be found below](#running-the-queue-worker).

job이 처리되는 동안에 exception이 발생하면, job을 다시 시도하기 위하여 자동으로 queue로 반환됩니다. job은 애플리케이션에서 정의된 최대 재시도 횟수만큼 계속해서 실행됩니다. 재시도 횟수는 `queue:work` 아티즌 명령어를 사용할 때 `--tries` 옵션을 사용하여 정의됩니다. 재시도 횟수를 job클래스 자체에 정의할 수도 있습니다. queue worker에 대한 보다 자세한 사항은 [다음에서 찾을 수 있습니다](#running-the-queue-worker).

<a name="manually-releasing-a-job"></a>
#### Manually Releasing A Job
#### 수동으로 작업 해제

Sometimes you may wish to manually release a job back onto the queue so that it can be attempted again at a later time. You may accomplish this by calling the `release` method:

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

By default, the `release` method will release the job back onto the queue for immediate processing. However, by passing an integer to the `release` method you may instruct the queue to not make the job available for processing until a given number of seconds has elapsed:

기본적으로 `release` 메서드는 즉각적인 처리를 위해 작업을 다시 큐-queue로 돌려보냅니다. 그러나 `release` 메소드에 정수를 전달하면 주어진 시간(초)이 경과할 때까지 작업을 처리할 수 없도록 큐-queue에 지시할 수 있습니다.

    $this->release(10);

<a name="manually-failing-a-job"></a>
#### Manually Failing A Job
#### 수동으로 작업 실패

Occasionally you may need to manually mark a job as "failed". To do so, you may call the `fail` method:

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

If you would like to mark your job as failed because of an exception that you have caught, you may pass the exception to the `fail` method:

예외 때문에 작업을 실패한 것으로 표시하려면, catch를 통해 잡은 예외를 `fail` 메서드에 전달하면 됩니다.

    $this->fail($exception);

> {tip} For more information on failed jobs, check out the [documentation on dealing with job failures](#dealing-with-failed-jobs).

> {tip} 실패한 작업에 대한 자세한 내용은 [작업 실패 처리 문서](#dealing-with-failed-jobs)를 확인하세요.

<a name="job-batching"></a>
## Job Batching
## Job 배치(동일 프로그램에서 일괄 처리되는 작업 단위)

Laravel's job batching feature allows you to easily execute a batch of jobs and then perform some action when the batch of jobs has completed executing. Before getting started, you should create a database migration to build a table to contain meta information about your job batches, such as their completion percentage. This migration may be generated using the `queue:batches-table` Artisan command:

라라벨의 배치-batch 기능을 사용하면, 작업 일괄 처리를 쉽게 실행한 다음, 일괄 작업 실행이 완료되면 일부 작업을 처리할 수 있습니다. 시작하기 전에 완료율과 같은 작업 배치에 대한 메타 정보를 포함하는 테이블을 작성하기 위해 데이터베이스 마이그레이션을 생성해야 합니다. 이 마이그레이션은 `queue:batches-table` Artisan 명령을 사용하여 생성할 수 있습니다.

    php artisan queue:batches-table

    php artisan migrate

<a name="defining-batchable-jobs"></a>
### Defining Batchable Jobs
### 일괄 처리 가능한 작업 정의

To define a batchable job, you should [create a queueable job](#creating-jobs) as normal; however, you should add the `Illuminate\Bus\Batchable` trait to the job class. This trait provides access to a `batch` method which may be used to retrieve the current batch that the job is executing within:

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
### Dispatching Batches
### 일괄 작업 실행하기

To dispatch a batch of jobs, you should use the `batch` method of the `Bus` facade. Of course, batching is primarily useful when combined with completion callbacks. So, you may use the `then`, `catch`, and `finally` methods to define completion callbacks for the batch. Each of these callbacks will receive an `Illuminate\Bus\Batch` instance when they are invoked. In this example, we will imagine we are queueing a batch of jobs that each process a given number of rows from a CSV file:

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

The batch's ID, which may be accessed via the `$batch->id` property, may be used to [query the Laravel command bus](#inspecting-batches) for information about the batch after it has been dispatched.

`$batch->id` 속성을 통해 액세스할 수 있는 배치의 ID는 배치가 발송된 후 배치에 대한 정보를 얻기 위해 [라라벨 명령 버스 쿼리](#inspecting-batches)에 사용될 수 있습니다.

> {note} Since batch callbacks are serialized and executed at a later time by the Laravel queue, you should not use the `$this` variable within the callbacks.

> {note} 일괄 콜백은 나중에 라라벨 큐에 의해 직렬화되어 실행되기 때문에 콜백 내에서 `$this` 변수를 사용해서는 안됩니다.

<a name="naming-batches"></a>
#### Naming Batches
#### 배치 이름 지정

Some tools such as Laravel Horizon and Laravel Telescope may provide more user-friendly debug information for batches if batches are named. To assign an arbitrary name to a batch, you may call the `name` method while defining the batch:

라라벨 Horizon 및 라라벨 Telescope와 같은 일부 도구들은 배치 이름이 지정된 경우 배치에 대해 보다 사용자 친화적인 debug 정보를 제공합니다. 배치에 임의의 이름을 지정하려면, 배치를 정의하는 동안 `name` 메소드를 호출해야 합니다.

    $batch = Bus::batch([
        // ...
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->name('Import CSV')->dispatch();

<a name="batch-connection-queue"></a>
#### Batch Connection & Queue
#### Connection 및 Queue 배치

If you would like to specify the connection and queue that should be used for the batched jobs, you may use the `onConnection` and `onQueue` methods. All batched jobs must execute within the same connection and queue:

일괄 작업에 사용해야 하는 연결 및 큐-queue을 지정하려면 `onConnection` 및 `onQueue` 메서드를 사용할 수 있습니다. 모든 일괄 작업은 동일한 연결 및 큐-queue 내에서 실행되어야 합니다.

    $batch = Bus::batch([
        // ...
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->onConnection('redis')->onQueue('imports')->dispatch();

<a name="chains-within-batches"></a>
#### Chains Within Batches
#### 배치 내 체인

You may define a set of [chained jobs](#job-chaining) within a batch by placing the chained jobs within an array. For example, we may execute two job chains in parallel and execute a callback when both job chains have finished processing:

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
### Adding Jobs To Batches
### 배치에 Job 추가

Sometimes it may be useful to add additional jobs to a batch from within a batched job. This pattern can be useful when you need to batch thousands of jobs which may take too long to dispatch during a web request. So, instead, you may wish to dispatch an initial batch of "loader" jobs that hydrate the batch with even more jobs:

경우에 따라 일괄 작업 내에서 일괄 작업을 추가하는 것이 유용할 수 있습니다. 이 패턴은 웹 요청 중에 발송하는 데 너무 오래 걸릴 수 있는 수천 개의 작업을 일괄 처리해야 할 때 유용할 수 있습니다. 따라서 대신에 더 많은 작업으로 배치를 분할해서 실행하는 "로더" 작업의 초기 배치를 실행할 수 있습니다.

    $batch = Bus::batch([
        new LoadImportBatch,
        new LoadImportBatch,
        new LoadImportBatch,
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->name('Import Contacts')->dispatch();

In this example, we will use the `LoadImportBatch` job to hydrate the batch with additional jobs. To accomplish this, we may use the `add` method on the batch instance that may be accessed via the job's `batch` method:

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

> {note} You may only add jobs to a batch from within a job that belongs to the same batch.

> {note} 동일한 배치에 속하는 job 내에서만 배치에 job들을 추가할 수 있습니다.

<a name="inspecting-batches"></a>
### Inspecting Batches
### 배치 검사

The `Illuminate\Bus\Batch` instance that is provided to batch completion callbacks has a variety of properties and methods to assist you in interacting with and inspecting a given batch of jobs:

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
#### Returning Batches From Routes
#### 라우트에서 배치 반환

All `Illuminate\Bus\Batch` instances are JSON serializable, meaning you can return them directly from one of your application's routes to retrieve a JSON payload containing information about the batch, including its completion progress. This makes it convenient to display information about the batch's completion progress in your application's UI.

모든 `Illuminate\Bus\Batch` 인스턴스는 JSON으로 직렬화할 수 있습니다. 즉, 완료 진행 상황을 포함하여 배치에 대한 정보가 포함된 JSON 페이로드를 검색하기 위해, 애플리케이션 경로 중 하나에서 직접 반환할 수 있습니다. 이렇게 하면 애플리케이션 UI에서 일괄 처리 완료 진행률에 대한 정보를 편리하게 표시할 수 있습니다.

To retrieve a batch by its ID, you may use the `Bus` facade's `findBatch` method:

ID로 배치를 검색하려면 `Bus` 파사드의 `findBatch` 메소드를 사용할 수 있습니다.

    use Illuminate\Support\Facades\Bus;
    use Illuminate\Support\Facades\Route;

    Route::get('/batch/{batchId}', function (string $batchId) {
        return Bus::findBatch($batchId);
    });

<a name="cancelling-batches"></a>
### Cancelling Batches
### 배치 취소

Sometimes you may need to cancel a given batch's execution. This can be accomplished by calling the `cancel` method on the `Illuminate\Bus\Batch` instance:

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

As you may have noticed in previous examples, batched jobs should typically check to see if the batch has been cancelled at the beginning of their `handle` method:

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
### Batch Failures
### 배치 실패

When a batched job fails, the `catch` callback (if assigned) will be invoked. This callback is only invoked for the first job that fails within the batch.

일괄 작업이 실패하면 `catch` 콜백(할당된 경우)이 호출됩니다. 이 콜백은 배치 내에서 실패한 첫 번째 작업에 대해서만 호출됩니다.

<a name="allowing-failures"></a>
#### Allowing Failures
#### 실패 허용

When a job within a batch fails, Laravel will automatically mark the batch as "cancelled". If you wish, you may disable this behavior so that a job failure does not automatically mark the batch as cancelled. This may be accomplished by calling the `allowFailures` method while dispatching the batch:

배치 내의 job이 실패하면 라라벨은 자동으로 배치를 "취소됨"으로 표시합니다. 당신이 원할 경우, job 실패로 인해 배치가 취소 되게 자동으로 표시되지 않도록 이 동작을 비활성화할 수 있습니다. 이는 배치를 dispatch하는 동안 `allowFailures` 메소드를 호출하여 수행합니다.

    $batch = Bus::batch([
        // ...
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->allowFailures()->dispatch();

<a name="retrying-failed-batch-jobs"></a>
#### Retrying Failed Batch Jobs
#### 실패한 job 배치를 재시도

For convenience, Laravel provides a `queue:retry-batch` Artisan command that allows you to easily retry all of the failed jobs for a given batch. The `queue:retry-batch` command accepts the UUID of the batch whose failed jobs should be retried:

편의상, 라라벨은 주어진 배치에 대하여 실패한 모든 job들을 쉽게 재시도 할 수 있는 `queue:retry-batch` 아티즌 명령어를 제공합니다. `queue:retry-batch` 명령어는 재시도 해야 하는 실패한 job들의 배치의 UUID를 허용합니다.

```bash
php artisan queue:retry-batch 32dbc76c-4f82-4749-b610-a639fe0099b5
```

<a name="pruning-batches"></a>
### Pruning Batches
### 배치 정리하기

Without pruning, the `job_batches` table can accumulate records very quickly. To mitigate this, you should [schedule](/docs/{{version}}/scheduling) the `queue:prune-batches` Artisan command to run daily:

정리를 해주지 않는다면, `job_batches` 테이블은 레코드가 매우 빠르게 누적될 수 있습니다. 이를 완화하려면 `queue:prune-batches` Artisan 명령이 매일 실행되도록 [일정](/docs/{{version}}/scheduling)해야 합니다.

    $schedule->command('queue:prune-batches')->daily();

By default, all finished batches that are more than 24 hours old will be pruned. You may use the `hours` option when calling the command to determine how long to retain batch data. For example, the following command will delete all batches that finished over 48 hours ago:

기본적으로 24시간이 지난 완료된 모든 배치는 정리됩니다. 명령을 호출할 때 `hour` 옵션을 사용하여 배치 데이터를 유지할 기간을 결정할 수 있습니다. 예를 들어 다음 명령은 48시간 전에 완료된 모든 배치를 삭제합니다.

    $schedule->command('queue:prune-batches --hours=48')->daily();

Sometimes, your `jobs_batches` table may accumulate batch records for batches that never completed successfully, such as batches where a job failed and that job was never retried successfully. You may instruct the `queue:prune-batches` command to prune these unfinished batch records using the `unfinished` option:

때때로 작업이 실패하고 해당 작업이 성공적으로 재시도되지 않은 배치와 같이, 성공적으로 완료되지 않은 배치에 대해 `jobs_batches` 테이블에 해당 배치 레코드를 계속해서 저장할 수 있습니다. `unfinished` 옵션을 사용하여 이러한 완료되지 않은 배치 레코드를 정리하도록 `queue:prune-batches` 명령에 지시할 수 있습니다.

    $schedule->command('queue:prune-batches --hours=48 --unfinished=72')->daily();

<a name="queueing-closures"></a>
## Queueing Closures
## 큐잉 클로저

Instead of dispatching a job class to the queue, you may also dispatch a closure. This is great for quick, simple tasks that need to be executed outside of the current request cycle. When dispatching closures to the queue, the closure's code content is cryptographically signed so that it can not be modified in transit:

작업 클래스를 큐-queue에 전달하는 대신 클로저를 전달할 수도 있습니다. 이는 현재 요청 주기 외부에서 실행해야 하는 빠르고 간단한 작업에 적합합니다. 큐에 클로저를 디스패치할 때 클로저의 코드 내용은 암호화로 서명되어 전송 중에 수정할 수 없습니다.

    $podcast = App\Podcast::find(1);

    dispatch(function () use ($podcast) {
        $podcast->publish();
    });

Using the `catch` method, you may provide a closure that should be executed if the queued closure fails to complete successfully after exhausting all of your queue's [configured retry attempts](#max-job-attempts-and-timeout):

`catch` 메소드를 사용하여 큐의 [설정된 재시도 시도횟수](#max-job-attempts-and-timeout)를 모두 사용한 후에도 큐에 있는 클로저가 성공적으로 완료되지 않으면 실행되어야 하는 클로저를 제공할 수 있습니다.

    use Throwable;

    dispatch(function () use ($podcast) {
        $podcast->publish();
    })->catch(function (Throwable $e) {
        // This job has failed...
    });

<a name="running-the-queue-worker"></a>
## Running The Queue Worker
## Queue-큐 worker 실행하기

<a name="the-queue-work-command"></a>
### The `queue:work` Command
### `queue:work` 커맨드

Laravel includes an Artisan command that will start a queue worker and process new jobs as they are pushed onto the queue. You may run the worker using the `queue:work` Artisan command. Note that once the `queue:work` command has started, it will continue to run until it is manually stopped or you close your terminal:

라라벨에는 큐-queue 작업자를 시작하고 큐-queue에 푸시될 때 새 작업을 처리하는 Artisan 명령이 포함되어 있습니다. `queue:work` Artisan 명령을 사용하여 작업자를 실행할 수 있습니다. `queue:work` 명령이 시작되면 수동으로 중지하거나 터미널을 닫을 때까지 계속 실행됩니다.

    php artisan queue:work

> {tip} To keep the `queue:work` process running permanently in the background, you should use a process monitor such as [Supervisor](#supervisor-configuration) to ensure that the queue worker does not stop running.

> {tip} `queue:work` 프로세스를 백그라운드에서 계속 지속되게 하려면, queue worker가 중단되지 않는 것을 보장하기 위해 [Supervisor](#supervisor-configuration)와 같은 프로세스 모니터를 사용해야 합니다.

Remember, queue workers, are long-lived processes and store the booted application state in memory. As a result, they will not notice changes in your code base after they have been started. So, during your deployment process, be sure to [restart your queue workers](#queue-workers-and-deployment). In addition, remember that any static state created or modified by your application will not be automatically reset between jobs.

큐-queue 작업자는 수명이 긴 프로세스이며, 부팅된 애플리케이션 상태를 메모리에 저장합니다. 결과적으로 그들은 시작된 후에 코드 기반의 변경 사항을 알아차리지 못할 것입니다. 따라서 배포 프로세스 중에 [큐-queue 작업자를 다시 시작](#queue-workers-and-deployment)해야 합니다. 또한 애플리케이션에서 생성하거나 수정한 정적 상태는 작업 간에 자동으로 재설정되지 않습니다.

Alternatively, you may run the `queue:listen` command. When using the `queue:listen` command, you don't have to manually restart the worker when you want to reload your updated code or reset the application state; however, this command is significantly less efficient than the `queue:work` command:

또는 `queue:listen` 명령을 실행할 수 있습니다. `queue:listen` 명령을 사용할 때 업데이트된 코드를 다시 로드하거나 애플리케이션 상태를 재설정하려는 경우 작업자를 수동으로 다시 시작할 필요가 없습니다. 그러나 이 명령은 `queue:work` 명령보다 훨씬 비효율적입니다.

    php artisan queue:listen

<a name="running-multiple-queue-workers"></a>
#### Running Multiple Queue Workers
#### 여러 큐-queue 작업자 실행

To assign multiple workers to a queue and process jobs concurrently, you should simply start multiple `queue:work` processes. This can either be done locally via multiple tabs in your terminal or in production using your process manager's configuration settings. [When using Supervisor](#supervisor-configuration), you may use the `numprocs` configuration value.

여러 작업자를 큐-queue에 할당하고 동시에 작업을 처리하려면 여러 `queue:work` 프로세스를 시작하기만 하면 됩니다. 이것은 터미널의 여러 탭을 통해 로컬로 수행하거나 프로세스 관리자의 설정을 사용하여 프로덕션에서 처리할 수 있습니다. [Supervisor 사용 시](#supervisor-configuration) `numprocs` 설정 값을 사용할 수 있습니다.

<a name="specifying-the-connection-queue"></a>
#### Specifying The Connection & Queue
#### 커넥션 & queue-큐 지정하기

You may also specify which queue connection the worker should utilize. The connection name passed to the `work` command should correspond to one of the connections defined in your `config/queue.php` configuration file:

어떤 queue-큐 커넥션을 worker가 사용할지 지정할 수도 있습니다. `work` 명령어에 전달된 커넥션 이름은 `config/queue.php` 설정 파일에 정의되어 있는 커넥션 이름과 일치해야 합니다.

    php artisan queue:work redis

By default, the `queue:work` command only processes jobs for the default queue on a given connection. However, you may customize your queue worker even further by only processing particular queues for a given connection. For example, if all of your emails are processed in an `emails` queue on your `redis` queue connection, you may issue the following command to start a worker that only processes that queue:

기본적으로 `queue:work` 명령은 지정된 연결의 기본 큐-queue에 대한 작업만 처리합니다. 그러나 주어진 연결에 대한 특정 큐-queue만 처리하여 큐-queue 작업자를 추가로 사용자 지정할 수 있습니다. 예를 들어 모든 이메일이 `redis` 큐-queue 연결의 `emails` 큐-queue에서 처리되는 경우 다음 명령을 실행하여 해당 큐-queue만 처리하는 작업자를 시작할 수 있습니다.

    php artisan queue:work redis --queue=emails

<a name="processing-a-specified-number-of-jobs"></a>
#### Processing A Specified Number Of Jobs
#### 지정된 수의 Job 처리하기

The `--once` option may be used to instruct the worker to only process a single job from the queue:

`--once` 옵션은 worker가 queue로 부터 하나의 단일 job을 처리하도록 합니다.

    php artisan queue:work --once

The `--max-jobs` option may be used to instruct the worker to process the given number of jobs and then exit. This option may be useful when combined with [Supervisor](#supervisor-configuration) so that your workers are automatically restarted after processing a given number of jobs, releasing any memory they may have accumulated:

`--max-jobs` 옵션은 작업자에게 주어진 수의 작업을 처리한 다음 종료하도록 지시하는 데 사용할 수 있습니다. 이 옵션은 [Supervisor](#supervisor-configuration)와 결합하여 작업자가 주어진 수의 작업을 처리한 후 자동으로 다시 시작하여 누적되었을 수 있는 메모리를 해제할 때 유용할 수 있습니다.

    php artisan queue:work --max-jobs=1000

<a name="processing-all-queued-jobs-then-exiting"></a>
#### Processing All Queued Jobs & Then Exiting
#### 대기중인 모든 Job 처리 및 종료

The `--stop-when-empty` option may be used to instruct the worker to process all jobs and then exit gracefully. This option can be useful when processing Laravel queues within a Docker container if you wish to shutdown the container after the queue is empty:

`--stop-when-empty` 옵션은 작업자에게 모든 작업을 처리한 다음 정상적으로 종료하도록 지시하는 데 사용할 수 있습니다. 이 옵션은 큐가 비어 있는 후 컨테이너를 종료하려는 경우 Docker 컨테이너 내에서 라라벨 큐를 처리할 때 유용할 수 있습니다.

    php artisan queue:work --stop-when-empty

<a name="processing-jobs-for-a-given-number-of-seconds"></a>
#### Processing Jobs For A Given Number Of Seconds
#### 주어진 시간 동안 Job 처리

The `--max-time` option may be used to instruct the worker to process jobs for the given number of seconds and then exit. This option may be useful when combined with [Supervisor](#supervisor-configuration) so that your workers are automatically restarted after processing jobs for a given amount of time, releasing any memory they may have accumulated:

`--max-time` 옵션은 작업자에게 주어진 시간(초) 동안 작업을 처리한 다음 종료하도록 지시하는 데 사용할 수 있습니다. 이 옵션은 [Supervisor](#supervisor-configuration)와 함께 사용하면 작업자가 주어진 시간 동안 작업을 처리한 후 자동으로 다시 시작하여 축적된 메모리를 해제하는 데 유용할 수 있습니다.

    // Process jobs for one hour and then exit...
    php artisan queue:work --max-time=3600

<a name="worker-sleep-duration"></a>
#### Worker Sleep Duration
#### 작업자 수면 시간

When jobs are available on the queue, the worker will keep processing jobs with no delay in between them. However, the `sleep` option determines how many seconds the worker will "sleep" if there are no new jobs available. While sleeping, the worker will not process any new jobs - the jobs will be processed after the worker wakes up again.

큐-queue에서 작업을 사용할 수 있는 경우 작업자는 작업 사이에 지연 없이 작업을 계속 처리합니다. 그러나 `sleep` 옵션은 사용 가능한 새 작업이 없는 경우 작업자가 "잠자기" 시간(초)을 결정합니다. 잠자는 동안 작업자는 새 작업을 처리하지 않습니다. 작업자가 다시 깨어난 후에 작업이 처리됩니다.

    php artisan queue:work --sleep=3

<a name="resource-considerations"></a>
#### Resource Considerations
#### 리소스 고려사항

Daemon queue workers do not "reboot" the framework before processing each job. Therefore, you should release any heavy resources after each job completes. For example, if you are doing image manipulation with the GD library, you should free the memory with `imagedestroy` when you are done processing the image.

데몬 큐 작업자는 각 작업을 처리하기 전에 프레임워크를 "재부팅"하지 않습니다. 따라서 각 작업이 완료된 후 무거운 리소스를 해제해야 합니다. 예를 들어, GD 라이브러리로 이미지 조작을 하는 경우 이미지 처리가 완료되면 `imagedestroy`로 메모리를 해제해야 합니다.

<a name="queue-priorities"></a>
### Queue Priorities
### queue의 우선순위

Sometimes you may wish to prioritize how your queues are processed. For example, in your `config/queue.php` configuration file, you may set the default `queue` for your `redis` connection to `low`. However, occasionally you may wish to push a job to a `high` priority queue like so:

때로는 큐-queue 처리 방법의 우선 순위를 정하고 싶을 수 있습니다. 예를 들어 `config/queue.php` 설정 파일에서 `redis` 연결에 대한 기본 `queue`를 `low`로 설정할 수 있습니다. 그러나 때때로 다음과 같이 작업을 `high` 우선 순위 큐-queue로 푸시하고 싶을 수 있습니다.

    dispatch((new Job)->onQueue('high'));

To start a worker that verifies that all of the `high` queue jobs are processed before continuing to any jobs on the `low` queue, pass a comma-delimited list of queue names to the `work` command:

job 이 `low` queue로 처리되기 전에, `high` queue job으로 처리되는 것을 명확히 하여 worker가 시작되게 하려면, 콤마로 구분된 queue 이름의 목록을 `work` 명령어에 전달하면 됩니다.

    php artisan queue:work --queue=high,low

<a name="queue-workers-and-deployment"></a>
### Queue Workers & Deployment
### Queue worker & 배포

Since queue workers are long-lived processes, they will not notice changes to your code without being restarted. So, the simplest way to deploy an application using queue workers is to restart the workers during your deployment process. You may gracefully restart all of the workers by issuing the `queue:restart` command:

큐-queue 작업자는 수명이 긴 프로세스이므로 다시 시작하지 않고는 코드 변경 사항을 알아차리지 못합니다. 따라서 큐-queue 작업자를 사용하여 애플리케이션을 배포하는 가장 간단한 방법은 배포 프로세스 중에 작업자를 다시 시작하는 것입니다. `queue:restart` 명령을 실행하여 모든 작업자를 정상적으로 다시 시작할 수 있습니다.

    php artisan queue:restart

This command will instruct all queue workers to gracefully exit after they finish processing their current job so that no existing jobs are lost. Since the queue workers will exit when the `queue:restart` command is executed, you should be running a process manager such as [Supervisor](#supervisor-configuration) to automatically restart the queue workers.

이 명령은 기존 작업이 손실되지 않도록 모든 큐-queue 작업자가 현재 작업 처리를 마친 후 정상적으로 종료하도록 지시합니다. 큐 워커는 `queue:restart` 명령이 실행되면 종료되므로 [Supervisor](#supervisor-configuration)와 같은 프로세스 관리자를 실행하여 큐 워커를 자동으로 재시작해야 합니다.

> {tip} The queue uses the [cache](/docs/{{version}}/cache) to store restart signals, so you should verify that a cache driver is properly configured for your application before using this feature.

> {tip} 큐-queue은 [cache](/docs/{{version}}/cache)를 사용하여 재시작 신호를 저장하므로 이 기능을 사용하기 전에 캐시 드라이버가 애플리케이션에 대해 올바르게 설정되었는지 확인해야 합니다.

<a name="job-expirations-and-timeouts"></a>
### Job Expirations & Timeouts
### Job 만료 & 타임아웃

<a name="job-expiration"></a>
#### Job Expiration
#### Job 만료

In your `config/queue.php` configuration file, each queue connection defines a `retry_after` option. This option specifies how many seconds the queue connection should wait before retrying a job that is being processed. For example, if the value of `retry_after` is set to `90`, the job will be released back onto the queue if it has been processing for 90 seconds without being released or deleted. Typically, you should set the `retry_after` value to the maximum number of seconds your jobs should reasonably take to complete processing.

`config/queue.php` 설정 파일에서 각 큐-queue 연결은 `retry_after` 옵션을 정의합니다. 이 옵션은 처리 중인 작업을 재시도하기 전에 큐-queue 연결이 대기해야 하는 시간(초)을 지정합니다. 예를 들어 `retry_after` 값이 `90`으로 설정된 경우 작업이 해제되거나 삭제되지 않고 90초 동안 처리된 경우 작업이 큐-queue로 다시 해제됩니다. 일반적으로 `retry_after` 값을 작업이 처리를 완료하는 데 합리적으로 걸리는 최대 시간(초)으로 설정해야 합니다.

> {note} The only queue connection which does not contain a `retry_after` value is Amazon SQS. SQS will retry the job based on the [Default Visibility Timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/AboutVT.html) which is managed within the AWS console.

> {note} 아마존 SQS에서는 `retry_after` 값은 포함되어 있지 않습니다. SQS는 AWS 콘솔 안에서 관리되는 [Default Visibility Timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/AboutVT.html) 에 의해서 job이 재시작됩니다.

<a name="worker-timeouts"></a>
#### Worker Timeouts
#### worker 타임아웃

The `queue:work` Artisan command exposes a `--timeout` option. If a job is processing for longer than the number of seconds specified by the timeout value, the worker processing the job will exit with an error. Typically, the worker will be restarted automatically by a [process manager configured on your server](#supervisor-configuration):

`queue:work` Artisan 명령은 `--timeout` 옵션을 표시합니다. 작업이 시간 초과 값으로 지정된 시간(초)보다 오래 처리되는 경우 작업을 처리하는 작업자는 오류와 함께 종료됩니다. 일반적으로 작업자는 [서버에 설정된 프로세스 관리자](#supervisor-configuration)에 의해 자동으로 다시 시작됩니다.

```bash
php artisan queue:work --timeout=60
```

The `retry_after` configuration option and the `--timeout` CLI option are different, but work together to ensure that jobs are not lost and that jobs are only successfully processed once.

`retry_after` 설정 옵션과 `--timeout` CLI 옵션은 서로 다릅니다. 하지만 job이 한번에 성공적으로 처리될 수 있도록 하는데 함께 작동합니다.

> {note} The `--timeout` value should always be at least several seconds shorter than your `retry_after` configuration value. This will ensure that a worker processing a frozen job is always terminated before the job is retried. If your `--timeout` option is longer than your `retry_after` configuration value, your jobs may be processed twice.

> {note} `--timeout` 값은 항상 `retry_after` 설정 값보다 몇 초 이상 짧아야 합니다. 이렇게 하면 고정된 작업을 처리하는 작업자가 작업을 재시도하기 전에 항상 종료됩니다. `--timeout` 옵션이 `retry_after` 설정 값보다 길면 작업이 두 번 처리될 수 있습니다.

<a name="supervisor-configuration"></a>
## Supervisor Configuration
## Supervisor 설정하기

In production, you need a way to keep your `queue:work` processes running. A `queue:work` process may stop running for a variety of reasons, such as an exceeded worker timeout or the execution of the `queue:restart` command.

프로덕션에서는 `queue:work` 프로세스를 계속 실행하는 방법이 필요합니다. `queue:work` 프로세스는 작업자 시간 초과 초과 또는 `queue:restart` 명령 실행과 같은 다양한 이유로 실행을 중지될 수 있습니다.

For this reason, you need to configure a process monitor that can detect when your `queue:work` processes exit and automatically restart them. In addition, process monitors can allow you to specify how many `queue:work` processes you would like to run concurrently. Supervisor is a process monitor commonly used in Linux environments and we will discuss how to configure it in the following documentation.

이러한 이유로 `queue:work` 프로세스가 종료되고 자동으로 다시 시작될 때를 감지할 수 있는 프로세스 모니터를 설정해야 합니다. 또한 프로세스 모니터를 사용하면 동시에 실행할 `queue:work` 프로세스 수를 지정할 수 있습니다. Supervisor는 Linux 환경에서 일반적으로 사용되는 프로세스 모니터이며 다음 문서에서 이를 설정하는 방법에 대해 설명합니다.

<a name="installing-supervisor"></a>
#### Installing Supervisor
#### Supervisor 설치하기

Supervisor is a process monitor for the Linux operating system, and will automatically restart your `queue:work` processes if they fail. To install Supervisor on Ubuntu, you may use the following command:

Supervisor는 Linux 운영 체제용 프로세스 모니터이며, `queue:work` 프로세스가 실패하면 자동으로 다시 시작합니다. Ubuntu에 Supervisor를 설치하려면 다음 명령을 사용할 수 있습니다.

    sudo apt-get install supervisor

> {tip} If configuring and managing Supervisor yourself sounds overwhelming, consider using [Laravel Forge](https://forge.laravel.com), which will automatically install and configure Supervisor for your production Laravel projects.

> {tip} Supervisor를 직접 설정하고 관리하는 것이 너무 어렵다면 [Laravel Forge](https://forge.laravel.com)를 사용해 보세요. 그러면 프로덕션 라라벨 프로젝트에 Supervisor가 자동으로 설치되고 설정됩니다.

<a name="configuring-supervisor"></a>
#### Configuring Supervisor
#### Supervisor 설정하기

Supervisor configuration files are typically stored in the `/etc/supervisor/conf.d` directory. Within this directory, you may create any number of configuration files that instruct supervisor how your processes should be monitored. For example, let's create a `laravel-worker.conf` file that starts and monitors `queue:work` processes:

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

In this example, the `numprocs` directive will instruct Supervisor to run eight `queue:work` processes and monitor all of them, automatically restarting them if they fail. You should change the `command` directive of the configuration to reflect your desired queue connection and worker options.

이 예에서 `numprocs` 지시문은 Supervisor에게 8개의 `queue:work` 프로세스를 실행하고 모든 프로세스를 모니터링하도록 지시하며, 실패하면 자동으로 다시 시작합니다. 원하는 큐-queue 연결 및 작업자 옵션을 반영하도록 설정의 command 지시문을 변경해야 합니다.

> {note} You should ensure that the value of `stopwaitsecs` is greater than the number of seconds consumed by your longest running job. Otherwise, Supervisor may kill the job before it is finished processing.

> {note} `stopwaitsecs`의 값이 가장 긴 실행 작업에서 소비하는 시간(초)보다 큰지 확인해야합니다. 그렇지 않으면 supervisor가 작업을 처리를 완료하기 전에 종료 할 수 있습니다.

<a name="starting-supervisor"></a>
#### Starting Supervisor
#### Supervisor 시작하기

Once the configuration file has been created, you may update the Supervisor configuration and start the processes using the following commands:

설정 파일을 생성하고 나면, 다음 명령어를 통해서 Supervisor 설정을 업데이트 하고, 시작할 수 있습니다.

```bash
sudo supervisorctl reread

sudo supervisorctl update

sudo supervisorctl start laravel-worker:*
```

For more information on Supervisor, consult the [Supervisor documentation](http://supervisord.org/index.html).

Supervisor에 대한 보다 자세한 정보는 [Supervisor 문서](http://supervisord.org/index.html) 를 참고하십시오.

<a name="dealing-with-failed-jobs"></a>
## Dealing With Failed Jobs
## 실패한 Job 처리하기

Sometimes your queued jobs will fail. Don't worry, things don't always go as planned! Laravel includes a convenient way to [specify the maximum number of times a job should be attempted](#max-job-attempts-and-timeout). After a job has exceeded this number of attempts, it will be inserted into the `failed_jobs` database table. Of course, we will need to create that table if it does not already exist. To create a migration for the `failed_jobs` table, you may use the `queue:failed-table` command:

대기 중인 작업이 실패하는 경우가 있습니다. 걱정하지 마십시오. 원래 일이 항상 계획대로 진행되지는 않습니다! 라라벨에는 [작업을 시도해야 하는 최대 횟수를 지정](#max-job-attempts-and-timeout)하는 편리한 방법이 포함되어 있습니다. 작업이 이 시도 횟수를 초과하면 `failed_jobs` 데이터베이스 테이블에 삽입됩니다. 물론 해당 테이블이 아직 없는 경우 생성해야 합니다. `failed_jobs` 테이블에 대한 마이그레이션을 생성하려면 `queue:failed-table` 명령을 사용할 수 있습니다.

    php artisan queue:failed-table

    php artisan migrate

When running a [queue worker](#running-the-queue-worker) process, you may specify the maximum number of times a job should be attempted using the `--tries` switch on the `queue:work` command. If you do not specify a value for the `--tries` option, jobs will only be attempted once or as many times as specified by the job class' `$tries` property:

[큐-queue 작업자](#running-the-queue-worker) 프로세스를 실행할 때 `queue:work` 명령의 `--tries` 스위치를 사용하여 작업을 시도해야 하는 최대 횟수를 지정할 수 있습니다. `--tries` 옵션에 대한 값을 지정하지 않으면 작업은 한 번만 또는 작업 클래스의 `$tries` 속성에 지정된 횟수만큼 시도됩니다.

    php artisan queue:work redis --tries=3

Using the `--backoff` option, you may specify how many seconds Laravel should wait before retrying a job that has encountered an exception. By default, a job is immediately released back onto the queue so that it may be attempted again:

`--backoff` 옵션을 사용하여 예외가 발생한 작업을 재시도하기 전에 라라벨이 몇 초 동안 기다려야 하는지 지정할 수 있습니다. 기본적으로 작업은 즉시 다시 큐-queue로 보내진 후 재시도하게 됩니다.

    php artisan queue:work redis --tries=3 --backoff=3

If you would like to configure how many seconds Laravel should wait before retrying a job that has encountered an exception on a per-job basis, you may do so by defining a `backoff` property on your job class:

작업별로 예외가 발생한 작업을 재시도하기 전에 라라벨이 대기해야 하는 시간(초)을 설정하려면 작업 클래스에 `backoff` 속성을 정의하면 됩니다.

    /**
     * The number of seconds to wait before retrying the job.
     *
     * @var int
     */
    public $backoff = 3;

If you require more complex logic for determining the job's backoff time, you may define a `backoff` method on your job class:

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

You may easily configure "exponential" backoffs by returning an array of backoff values from the `backoff` method. In this example, the retry delay will be 1 second for the first retry, 5 seconds for the second retry, and 10 seconds for the third retry:

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
### Cleaning Up After Failed Jobs
### 실패한 Job 정리하기

When a particular job fails, you may want to send an alert to your users or revert any actions that were partially completed by the job. To accomplish this, you may define a `failed` method on your job class. The `Throwable` instance that caused the job to fail will be passed to the `failed` method:

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

> {note} A new instance of the job is instantiated before invoking the `failed` method; therefore, any class property modifications that may have occurred within the `handle` method will be lost.

> {note} `failed` 메서드를 호출하기 전에 작업의 새 인스턴스가 인스턴스화됩니다. 따라서 `handle` 메서드 내에서 발생했을 수 있는 클래스 속성의 변경사항은 손실됩니다.

<a name="retrying-failed-jobs"></a>
### Retrying Failed Jobs
### 실패한 작업 재시도

To view all of the failed jobs that have been inserted into your `failed_jobs` database table, you may use the `queue:failed` Artisan command:

`failed_jobs` 데이터베이스 테이블에 삽입된 실패한 모든 작업을 보려면 `queue:failed` Artisan 명령을 사용할 수 있습니다.

    php artisan queue:failed

The `queue:failed` command will list the job ID, connection, queue, failure time, and other information about the job. The job ID may be used to retry the failed job. For instance, to retry a failed job that has an ID of `ce7bb17c-cdd8-41f0-a8ec-7b4fef4e5ece`, issue the following command:

`queue:failed` 명령은 작업 ID, 연결, 큐-queue, 실패 시간 및 기타 작업 정보를 나열합니다. 작업 ID를 사용하여 실패한 작업을 재시도할 수 있습니다. 예를 들어 ID가 `ce7bb17c-cdd8-41f0-a8ec-7b4fef4e5ece`인 실패한 작업을 재시도하려면 다음 명령을 실행하십시오.

    php artisan queue:retry ce7bb17c-cdd8-41f0-a8ec-7b4fef4e5ece

If necessary, you may pass multiple IDs to the command:

필요한 경우 명령에 여러 ID를 전달할 수 있습니다.

    php artisan queue:retry ce7bb17c-cdd8-41f0-a8ec-7b4fef4e5ece 91401d2c-0784-4f43-824c-34f94a33c24d

You may also retry all of the failed jobs for a particular queue:

특정 큐-queue에 대해 실패한 모든 작업을 다시 시도할 수도 있습니다.

    php artisan queue:retry --queue=name

To retry all of your failed jobs, execute the `queue:retry` command and pass `all` as the ID:

실패한 모든 작업을 다시 시도하려면 `queue:retry` 명령을 실행하고 `all`을 ID로 전달합니다.

    php artisan queue:retry all

If you would like to delete a failed job, you may use the `queue:forget` command:

실패한 작업을 삭제하려면 `queue:forget` 명령을 사용할 수 있습니다.

    php artisan queue:forget 91401d2c-0784-4f43-824c-34f94a33c24d

> {tip} When using [Horizon](/docs/{{version}}/horizon), you should use the `horizon:forget` command to delete a failed job instead of the `queue:forget` command.

> {tip} [Horizon](/docs/{{version}}/horizon)을 사용할 때 실패한 작업을 삭제하려면 `queue:forget` 명령 대신 `horizon:forget` 명령을 사용해야 합니다.

To delete all of your failed jobs from the `failed_jobs` table, you may use the `queue:flush` command:

`failed_jobs` 테이블에서 실패한 모든 작업을 삭제하려면 `queue:flush` 명령을 사용할 수 있습니다.

    php artisan queue:flush

<a name="ignoring-missing-models"></a>
### Ignoring Missing Models
### 누락된 모델 무시

When injecting an Eloquent model into a job, the model is automatically serialized before being placed on the queue and re-retrieved from the database when the job is processed. However, if the model has been deleted while the job was waiting to be processed by a worker, your job may fail with a `ModelNotFoundException`.

Eloquent 모델을 작업에 주입할 때 모델은 큐-queue에 배치되기 전에 자동으로 직렬화되고 작업이 처리될 때 데이터베이스에서 다시 검색됩니다. 하지만 작업이 작업자의 처리를 기다리는 동안 모델이 삭제된 경우 `ModelNotFoundException`과 함께 작업이 실패할 수 있습니다.

For convenience, you may choose to automatically delete jobs with missing models by setting your job's `deleteWhenMissingModels` property to `true`. When this property is set to `true`, Laravel will quietly discard the job without raising an exception:

편의를 위해 작업의 `deleteWhenMissingModels` 속성을 `true`로 설정하여 누락된 모델이 있는 작업을 자동으로 삭제하도록 선택할 수 있습니다. 이 속성이 `true`로 설정되면 라라벨은 예외를 발생시키지 않고 조용히 작업을 버립니다.

    /**
     * Delete the job if its models no longer exist.
     *
     * @var bool
     */
    public $deleteWhenMissingModels = true;

<a name="storing-failed-jobs-in-dynamodb"></a>
### Storing Failed Jobs In DynamoDB
### DynamoDB에 실패한 작업 저장

Laravel also provides support for storing your failed job records in [DynamoDB](https://aws.amazon.com/dynamodb) instead of a relational database table. However, you must create a DynamoDB table to store all of the failed job records. Typically, this table should be named `failed_jobs`, but you should name the table based on the value of the `queue.failed.table` configuration value within your application's `queue` configuration file.

라라벨은 또한 실패한 작업 레코드를 관계형 데이터베이스 테이블 대신 [DynamoDB](https://aws.amazon.com/dynamodb) 에 저장하는 기능을 지원합니다. 그러나 실패한 모든 작업 레코드를 저장하려면 DynamoDB 테이블을 생성해야 합니다. 일반적으로 이 테이블의 이름은 `failed_jobs`로 지정해야 하지만 애플리케이션의 `queue` 설정 파일에 있는 `queue.failed.table` 설정 값을 기반으로 테이블 이름을 지정해야 합니다.

The `failed_jobs` table should have a string primary partition key named `application` and a string primary sort key named `uuid`. The `application` portion of the key will contain your application's name as defined by the `name` configuration value within your application's `app` configuration file. Since the application name is part of the DynamoDB table's key, you can use the same table to store failed jobs for multiple Laravel applications.

`failed_jobs` 테이블에는 `application`이라는 문자열 기본 파티션 키와 `uuid`라는 문자열 기본 정렬 키가 있어야 합니다. 키의 `application` 부분에는 애플리케이션의 `app` 설정 파일 내 `name` 설정 값으로 정의된 애플리케이션의 이름이 포함됩니다. 애플리케이션 이름은 DynamoDB 테이블 키의 일부이므로 동일한 테이블을 사용하여 여러 라라벨 애플리케이션에 대해 실패한 작업을 저장할 수 있습니다.

In addition, ensure that you install the AWS SDK so that your Laravel application can communicate with Amazon DynamoDB:

또한 라라벨 애플리케이션이 Amazon DynamoDB와 통신할 수 있도록 AWS SDK를 설치해야 합니다.

```nothing
composer require aws/aws-sdk-php
```

Next, set the `queue.failed.driver` configuration option's value to `dynamodb`. In addition, you should define `key`, `secret`, and `region` configuration options within the failed job configuration array. These options will be used to authenticate with AWS. When using the `dynamodb` driver, the `queue.failed.database` configuration option is unnecessary:

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
### Pruning Failed Jobs
### 실패한 작업 정리하기

You may delete all of the records in your application's `failed_jobs` table by invoking the `queue:prune-failed` Artisan command:

`queue:prune-failed` Artisan 명령을 호출하여 애플리케이션의 `failed_jobs` 테이블에 있는 모든 레코드를 삭제할 수 있습니다.

    php artisan queue:prune-failed

If you provide the `--hours` option to the command, only the failed job records that were inserted within the last N number of hours will be retained. For example, the following command will delete all of the failed job records that were inserted more than 48 hours ago:

명령에 `--hours` 옵션을 제공하면 마지막 N시간 내에 삽입된 실패한 작업 레코드만 유지됩니다. 예를 들어 다음 명령은 48시간 전에 삽입된 실패한 작업 레코드를 모두 삭제합니다.

    php artisan queue:prune-failed --hours=48

<a name="failed-job-events"></a>
### Failed Job Events
### 실패한 Job에 대한 이벤트

If you would like to register an event listener that will be invoked when a job fails, you may use the `Queue` facade's `failing` method. For example, we may attach a closure to this event from the `boot` method of the `AppServiceProvider` that is included with Laravel:

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
## Clearing Jobs From Queues
## 큐-queue에서 작업 지우기

> {tip} When using [Horizon](/docs/{{version}}/horizon), you should use the `horizon:clear` command to clear jobs from the queue instead of the `queue:clear` command.

> {tip} [Horizon](/docs/{{version}}/horizon)을 사용할 때 큐-queue에서 작업을 지우려면 `queue:clear` 명령 대신 `horizon:clear` 명령을 사용해야 합니다.

If you would like to delete all jobs from the default queue of the default connection, you may do so using the `queue:clear` Artisan command:

기본 연결의 기본 큐-queue에서 모든 작업을 삭제하려면 `queue:clear` Artisan 명령을 사용하여 삭제할 수 있습니다.

    php artisan queue:clear

You may also provide the `connection` argument and `queue` option to delete jobs from a specific connection and queue:

특정 연결 및 큐-queue에서 작업을 삭제하기 위해 `connection` 인수와 `queue` 옵션을 제공할 수도 있습니다.

    php artisan queue:clear redis --queue=emails

> {note} Clearing jobs from queues is only available for the SQS, Redis, and database queue drivers. In addition, the SQS message deletion process takes up to 60 seconds, so jobs sent to the SQS queue up to 60 seconds after you clear the queue might also be deleted.

> {note} 큐-queue에서 작업 지우기는 SQS, Redis 및 데이터베이스 큐-queue 드라이버에서만 사용할 수 있습니다. 또한 SQS 메시지 삭제 프로세스는 최대 60초가 걸리므로 큐-queue을 지운 후 최대 60초 동안 SQS 큐-queue로 전송된 작업도 삭제될 수 있습니다.

<a name="monitoring-your-queues"></a>
## Monitoring Your Queues
## 큐-queue 모니터링

If your queue receives a sudden influx of jobs, it could become overwhelmed, leading to a long wait time for jobs to complete. If you wish, Laravel can alert you when your queue job count exceeds a specified threshold.

큐-queue에 작업이 갑자기 유입되면 과부하가 걸려 작업이 완료될 때까지 오랜 대기 시간이 발생할 수 있습니다. 원하는 경우 큐-queue 작업 수가 지정된 임계값을 초과할 때 라라벨이 경고할 수 있습니다.

To get started, you should schedule the `queue:monitor` command to [run every minute](/docs/{{version}}/scheduling). The command accepts the names of the queues you wish to monitor as well as your desired job count threshold:

시작하려면 `queue:monitor` 명령을 [매분 실행](/docs/{{version}}/scheduling)으로 예약해야 합니다. 이 명령은 모니터링하려는 큐-queue의 이름과 원하는 작업 수 임계값을 허용합니다.

```bash
php artisan queue:monitor redis:default,redis:deployments --max=100
```

Scheduling this command alone is not enough to trigger a notification alerting you of the queue's overwhelmed status. When the command encounters a queue that has a job count exceeding your threshold, an `Illuminate\Queue\Events\QueueBusy` event will be dispatched. You may listen for this event within your application's `EventServiceProvider` in order to send a notification to you or your development team:

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
## Job Events
## Job 이벤트

Using the `before` and `after` methods on the `Queue` [facade](/docs/{{version}}/facades), you may specify callbacks to be executed before or after a queued job is processed. These callbacks are a great opportunity to perform additional logging or increment statistics for a dashboard. Typically, you should call these methods from the `boot` method of a [service provider](/docs/{{version}}/providers). For example, we may use the `AppServiceProvider` that is included with Laravel:

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

Using the `looping` method on the `Queue` [facade](/docs/{{version}}/facades), you may specify callbacks that execute before the worker attempts to fetch a job from a queue. For example, you might register a closure to rollback any transactions that were left open by a previously failed job:

`Queue` [facade](/docs/{{version}}/facades)에서 `looping` 메서드를 사용하여 작업자가 큐-queue에서 작업을 가져오기 전에 실행되는 콜백을 지정할 수 있습니다. 예를 들어, 이전에 실패한 작업에 의해 열려 있던 모든 트랜잭션을 롤백하기 위ㅂ해 클로저를 등록할 수 있습니다.

    use Illuminate\Support\Facades\DB;
    use Illuminate\Support\Facades\Queue;

    Queue::looping(function () {
        while (DB::transactionLevel() > 0) {
            DB::rollBack();
        }
    });
