# Queues
# 큐

- [환경설정](#configuration)
- [기본 사용법](#basic-usage)
- [클로져 큐](#queueing-closures)
- [큐 리스너(Listener) 실행하기](#running-the-queue-listener)
- [데몬 큐 작업자](#daemon-queue-worker)
- [큐 푸쉬하기](#push-queues)
- [실패된 작업](#failed-jobs)

<a name="configuration"></a>
## Configuration
## 환경 설정

The Laravel Queue component provides a unified API across a variety of different queue services. Queues allow you to defer the processing of a time consuming task, such as sending an e-mail, until a later time, thus drastically speeding up the web requests to your application.
라라벨 큐 컴포넌트는 다양한 큐 서비스로부터 통일된 API를 제공합니다. 큐를 사용하면 이메일을 보내는 작업과 같이 시간이 소요되는 작업을 나중에 처리할 수 있습니다. 이렇게 작업을 나중에 처리함으로서 여러분의 애플리케이션은 웹 요청을 더 빠르게 처리할 수 있습니다.

The queue configuration file is stored in `config/queue.php`. In this file you will find connection configurations for each of the queue drivers that are included with the framework, which includes a database, [Beanstalkd](http://kr.github.com/beanstalkd), [IronMQ](http://iron.io), [Amazon SQS](http://aws.amazon.com/sqs), [Redis](http://redis.io), null, and synchronous (for local use) driver. The `null` queue driver simply discards queued jobs so they are never run.

큐의 환경 설정 파일은 `config/queue.php`에 위치하고 있습니다. 이 파일에서 프레임워크에 포함된 database, [Beanstalkd](http://kr.github.com/beanstalkd), [IronMQ](http://iron.io), [Amazon SQS](http://aws.amazon.com/sqs), [Redis](http://redis.io), null, 그리고 synchronous (로컬 사용을 위한)의 큐 드라이버 설정을 확인할 수 있습니다.

### Queue Database Tables
### 큐 데이터베이스 테이블

In order to use the `database` queue driver, you will need a database table to hold the jobs. To generate a migration to create this table, run the `queue:table` Artisan command:
`database` 큐 드라이버를 사용하기 위해서는 작업들을 담아둘 데이터베이스 테이블이 필요합니다. 이 테이블을 추가하기 위한 마이그레이션을 생성하려면 `queue:table` 아티즌 명령을 실행하면 됩니다.

	php artisan queue:table

### Other Queue Dependencies
### 다른 큐 드라이버의 의존 패키지

The following dependencies are needed for the listed queue drivers:
아래의 의존 패키지들은 앞서 이야기 했던 각각의 큐 드라이버들을 사용하는데 필요합니다.

- Amazon SQS: `aws/aws-sdk-php`
- Beanstalkd: `pda/pheanstalk ~3.0`
- IronMQ: `iron-io/iron_mq`
- Redis: `predis/predis ~1.0`

<a name="basic-usage"></a>
## Basic Usage
## 기본 사용법

#### Pushing A Job Onto The Queue
#### 큐에 작업을 푸쉬(Push) 하는 방법

All of the queueable jobs for your application are stored in the `App\Commands` directory. You may generate a new queued command using the Artisan CLI:
애플리케이션에서 큐를 통해서 처리 할 수 있는 모든 작업들은 `App\Commands` 폴더 안에 저장됩니다. 큐를 통해서 처리할 새로운 작업을 생성하려면 아티즌 CLI를 사용하면 됩니다.

	php artisan make:command SendEmail --queued

To push a new job onto the queue, use the `Queue::push` method:
새로운 작업을 큐에 푸쉬하기 위해 `Queue::push` 메소드를 사용합니다:

	Queue::push(new SendEmail($message));

> **Note:** In this example, we are using the `Queue` facade directly; however, typically you would dispatch queued command via the [Command Bus](/docs/{{version}}/bus). We will continue to use the `Queue` facade throughout this page; however, familiarize with the command bus as well, since it is used to dispatch both queued and synchronous commands for your application.

> **주의:** 이 예제에서는 `Queue` 파사드를 직접적으로 사용하고 있습니다. 그러나 일반적으로는 [Command Bus](/docs/{{version}}/bus)를 통해 큐에 작업을 보내게 됩니다. 이 페이지는 이후에도 계속 `Queue` 파사드를 사용하겠지만 애플리케이션에서 큐와 동기화 명령을 모두 실행 할 수 있으므로 커맨드 버스를 사용하는 것에도 익숙해져야 합니다.

By default, the `make:command` Artisan command generates a "self-handling" command, meaning a `handle` method is added to the command itself. This method will be called when the job is executed by the queue. You may type-hint any dependencies you need on the `handle` method and the [service container](/docs/{{version}}/container) will automatically inject them:

기본적으로 `make:command` 아티즌 명령어는 `handle` 메소드가 커멘드에 직접 추가되는  “self-handling” 명령을 생성합니다. 이 메소드는 작업이 큐에 의해 실행될 때 호출될 것입니다. 여러분은 `handle` 메소드에서 필요로 하는 의존 객체들을 type-hint로 지정 할 수 있으며 [서비스 컨테이너](/docs/{{version}}/container) 는 자동으로 이 의존 객체들을 추가할 것입니다:

	public function handle(UserRepository $users)
	{
		//
	}

If you would like your command to have a separate handler class, you should add the `--handler` flag to the `make:command` command:

만약 여러분의 커멘드가 분리된 핸들러(handler) 클래스를 가지기를 원한다면 `--handler` flag를 `make:command` 커멘드에 추가하면 됩니다. 

	php artisan make:command SendEmail --queued --handler

The generated handler will be placed in `App\Handlers\Commands` and will be resolved out of the IoC container.

생성된 핸들러(handler)는 `App\Handlers\Commands`에 위치하고 IoC 컨테이너를 통해서 의존성이 해결(resolve)됩니다.

#### Specifying The Queue / Tube For A Job
작업에 대한 큐(대기열) / 튜브 지정하기

You may also specify the queue / tube a job should be sent to:

여러분들은 작업을 보낼 큐와 튜브를 직접 지정할 수 있습니다:

	Queue::pushOn('emails', new SendEmail($message));

#### Passing The Same Payload To Multiple Jobs
#### 여러 작업들에 동일한 페이로드(Payload) 전달하기

If you need to pass the same data to several queue jobs, you may use the `Queue::bulk` method:

만약 여러 큐 작업에 같은 데이터를 보내야 하는 경우 `Queue::bulk` 메소드를 사용할 수 있습니다:

	Queue::bulk([new SendEmail($message), new AnotherCommand]);

#### Delaying The Execution Of A Job
#### 작업을 지연시켜 실행하기

Sometimes you may wish to delay the execution of a queued job. For instance, you may wish to queue a job that sends a customer an e-mail 15 minutes after sign-up. You can accomplish this using the `Queue::later` method:

때때로 여러분들은 큐 작업을 지연시키기를 원할 수 있습니다. 예를들어 고객이 가입후 15분 후에 이메일을 보내도록 큐 작업을 지연시키길 원할 수 있습니다. `Queue::later` 메소드를 사용하면 이와 같은 작업을 수행할 수 있습니다:

	$date = Carbon::now()->addMinutes(15);

	Queue::later($date, new SendEmail($message));

In this example, we're using the [Carbon](https://github.com/briannesbitt/Carbon) date library to specify the delay we wish to assign to the job. Alternatively, you may pass the number of seconds you wish to delay as an integer.

이 예제에서는 우리가 할당하고자 하는 작업에 시간 지연을 설정하기 위해 [Carbon](https://github.com/briannesbitt/Carbon) 이라는 날짜 라이브러리를 사용했습니다. 또 라이브러리 없이 지연시키기를 원하는 만큼의 초에 대한 시간 값을 정수(integer) 형태로 전달할 수도 있습니다.

> **Note:** The Amazon SQS service has a delay limit of 900 seconds (15 minutes).
> **참고** 아마존 SQS 서비스는 900초의 제한 시간을 가지고 있습니다. (15분)
 
#### Queues And Eloquent Models
#### 큐와 Eloquent Models

If your queued job accepts an Eloquent model in its constructor, only the identifier for the model will be serialized onto the queue. 

만약 대기된 작업이 구조체 안에서 Eloquent 모델을 받아들인다면 모델에 대한 ID만 큐에 직렬화(serialized)를 통해 전달 됩니다. 

When the job is actually handled, the queue system will automatically re-retrieve the full model instance from the database. 

작업이 실제로 처리될때 큐 시스템은 전체 모델 인스턴스를 데이터베이스로부터 자동으로 다시 조회할 것입니다.  

It's all totally transparent to your application and prevents issues that can arise from serializing full Eloquent model instances.

이러한 방법은 여러분의 애플리케이션에 대해 완전히 알기쉽게 하고 전체 Eloquent 모델 인스턴스를 직렬화(serializing) 하는데서 발생할 수 있는 이슈들을 방지합니다.

#### Deleting A Processed Job
#### 처리된 작업 삭제하기

Once you have processed a job, it must be deleted from the queue. If no exception is thrown during the execution of your job, this will be done automatically.

작업이 처리되었을때 이 작업은 큐에서 삭제되어야 합니다. 만약 여러분의 작업이 진행되는동안 예외 처리가 없으면 작업은 자동으로 완료될 것입니다.

If you would like to `delete` or `release` the job manually, the `Illuminate\Queue\InteractsWithQueue` trait provides access to the queue job `release` and `delete` methods. The `release` method accepts a single value: the number of seconds you wish to wait until the job is made available again.

만약 작업을 수동으로 `delete` 혹은 `release` 하기 원한다면, `Illuminate\Queue\InteractsWithQueue` trait가 큐 작업에 대한 `release` 와 `delete` 메소드를 제공하여 원하는 것을 수행할 수 있습니다. `release` 메소드는 작업이 다시 할 수 있을때까지 기다리는 시간값을 전달 받습니다.

	public function handle(SendEmail $command)
	{
		if (true)
		{
			$this->release(30);
		}
	}

#### Releasing A Job Back Onto The Queue
#### 큐에 작업 다시 넣기

IF an exception is thrown while the job is being processed, it will automatically be released back onto the queue so it may be attempted again. 
만약 작업이 진행하는동안 예외(Exception)가 발생한다면 이 작업은 자동으로 다시 시도될 수 있도록 큐에 다시 보내집니다. 
The job will continue to be released until it has been attempted the maximum number of times allowed by your application. 다시 보내진 작업은 애플리케이션에서 설정된 최대 허용된 횟수까지 반복해서 재시도됩니다.  
The number of maximum attempts is defined by the `--tries` switch used on the `queue:listen` or `queue:work` Artisan commands.
 최대 시도 횟수는 `queue:listen` 혹은 `queue:work` 아티즌 명령에서 사용된 `--tries` 스위치에 의해 정의됩니다.

#### Checking The Number Of Run Attempts
#### 작업이 시도된 횟수 체크하기

If an exception occurs while the job is being processed, it will automatically be released back onto the queue. You may check the number of attempts that have been made to run the job using the `attempts` method:
만약 작업이 진행하는동안 예외가 발생한다면 이 작업은 다시 큐에 보내집니다. `attemps` 메소드를 사용하여 작업이 시도된 횟수를 체크할 수 있습니다.

	if ($this->attempts() > 3)
	{
		//
	}

> **Note:** Your command / handler must use the `Illuminate\Queue\InteractsWithQueue` trait in order to call this method.

> **주의** 이 메소드를 호출하기 위해 커맨드 / 핸들러는 `Illuminate\Queue\InteractsWithQueue` trait 를 반드시 사용해야만 합니다.

<a name="queueing-closures"></a>
## Queueing Closures
## 클로져 큐

You may also push a Closure onto the queue. This is very convenient for quick, simple tasks that need to be queued:
큐(Queue)에는 클로져도 등록할 수 있습니다. 클로저는 빠르고 간단한 작업을 큐를 통햇 처리해야할 경우에 매우 편리합니다.

#### Pushing A Closure Onto The Queue
#### 큐에 클로져 푸쉬하기

	Queue::push(function($job) use ($id)
	{
		Account::delete($id);

		$job->delete();
	});

> **Note:** Instead of making objects available to queued Closures via the `use` directive, consider passing primary keys and re-pulling the associated models from within your queue job. This often avoids unexpected serialization behavior.

> **주의** : 큐에 푸쉬하는 클로저에 객체를 사용하도록 전달하는 대신 `use`를 사용하여 기본 키를 전달하고 큐 작업 안에서 관련 모델을 다시 가져오도록 구성하십시오. 이렇게하면 직렬화에서 발생할 수 있는 예기치 않은 영향을 방지 할 수 있습니다.

When using Iron.io [push queues](#push-queues), you should take extra precaution queueing Closures. The end-point that receives your queue messages should check for a token to verify that the request is actually from Iron.io. For example, your push queue end-point should be something like: `https://yourapp.com/queue/receive?token=SecretToken`. You may then check the value of the secret token in your application before marshalling the queue request.

Iron.io [push queues](#push-queues)를 이용하는 경우,  큐로 푸쉬하는 클로저에 추가 예방 조치를 취해야 합니다. 큐 메세지를 받는 부분에서 요청이 실제로 Iron.io에서 왔는지 검증하는 토큰을 체크해야합니다. 예를들어 푸쉬된 큐를 받는 URL은 `https://yourapp.com/queue/receive?token=SecretToken`와 같이 되어야 합니다.  그런 다음에는 큐 요청을 진행하기 전에 애플리케이션에서 비밀 토큰 값을 체크하도록 합니다. 

<a name="running-the-queue-listener"></a>
## Running The Queue Listener
## 큐 리스너(Listener) 실행하기

Laravel includes an Artisan task that will run new jobs as they are pushed onto the queue. You may run this task using the `queue:listen` command:

라라벨은 큐에 푸쉬된 새로운 작업들을 실행시키는 아티즌 작업을 포함하고 있습니다. `queue:listen` 커멘드를 사용하여 이 작업을 실행할 수 있습니다.

#### Starting The Queue Listener
#### Queue Listener 시작하기

	php artisan queue:listen

You may also specify which queue connection the listener should utilize:
listener가 사용해야 하는 큐 연결을 지정할 수도 있습니다:

	php artisan queue:listen connection

Note that once this task has started, it will continue to run until it is manually stopped. You may use a process monitor such as [Supervisor](http://supervisord.org/) to ensure that the queue listener does not stop running.

참고로 작업이 시작되면, 수동으로 정지될때까지 동작될 것입니다. [Supervisor](http://supervisord.org/) 와 같은 프로세스 모니터를 사용하여 반드시 큐 리스너가 중단되지 않도록 해야합니다.

You may pass a comma-delimited list of queue connections to the `listen` command to set queue priorities:

큐 작업의 우선순위를 정하려면 `listen` 명령에 쉼표(,)로 결정된 큐 연결 목록들의 순서를 넘겨줄 수 있습니다.

	php artisan queue:listen --queue=high,low

In this example, jobs on the `high-connection` will always be processed before moving onto jobs from the `low` connection.

이 예제에서, `high-connection`의 작업은 항상 `low` 커넥션 작업보다 먼저 처리됩니다.

#### Specifying The Job Timeout Parameter
#### 작업 제한 시간에 대한 매개 변수 지정하기

You may also set the length of time (in seconds) each job should be allowed to run:
각 작업이 실행되는데 허용하는 시간을 초 단위로 설정할 수 있습니다.

	php artisan queue:listen --timeout=60

#### Specifying Queue Sleep Duration
#### 큐 대기(sleep) 시간 지정하기

In addition, you may specify the number of seconds to wait before polling for new jobs:
추가적으로 새로운 작업이 시작하기 전 기다리는 대기 시간을 초 단위로 지정할 수 도 있습니다.

	php artisan queue:listen --sleep=5

Note that the queue only "sleeps" if no jobs are on the queue. If more jobs are available, the queue will continue to work them without sleeping.

대기열에 작업이 없는 경우에만 큐가 일시적으로 “잠들어 있는” 것에 주의하십시오. 만약 작업이 존재한다면, 큐는 대기 않고 계속 작업을 처리합니다. 

#### Processing The First Job On The Queue
#### 큐의 첫 번째 작업을 처리하기

To process only the first job on the queue, you may use the `queue:work` command:
큐에 들어 있는 첫번째 작업만을 처리하기 위해서는 `queue:work` 명령을 사용할 수 있습니다.

	php artisan queue:work

<a name="daemon-queue-worker"></a>
## Daemon Queue Worker
## 데몬 큐 작업자

The `queue:work` also includes a `--daemon` option for forcing the queue worker to continue processing jobs without ever re-booting the framework. This results in a significant reduction of CPU usage when compared to the `queue:listen` command, but at the added complexity of needing to drain the queues of currently executing jobs during your deployments.

`queue:work` 명령에는 프레임워크의 재부팅 없이도 지속적으로 작업을 처리하도록 큐 실행을 강제하는 `--daemon` 옵션이 포함되어있습니다.  이를 통하면 `queue:listen` 명령과 비교하여 CPU 사용이 현저하게 줄일 수 있습니다.  하지만 현재 실행중인 실행중인 작업의 큐를 비우는 데에 복잡한 절차가 추가적으로 필요하게 됩니다. 

To start a queue worker in daemon mode, use the `--daemon` flag:

큐 작업자(worker) 를 데몬에서 시작하기 위해 `--daemon` flag를 사용할 수 있습니다.

	php artisan queue:work connection --daemon

	php artisan queue:work connection --daemon --sleep=3

	php artisan queue:work connection --daemon --sleep=3 --tries=3

As you can see, the `queue:work` command supports most of the same options available to `queue:listen`. You may use the `php artisan help queue:work` command to view all of the available options.

보시는 바와 같이 `queue:work` 명령어는 `queue:listen`에서 이용가능한 대부분의 같은 옵션들을 제공하고 있습니다.  `php artisan help queue:work` 명령어를 사용하면 사용 가능한 모든 옵션들을 볼 수 있습니다.

### Deploying With Daemon Queue Workers
### 데몬 큐 작업자에 의한 배포

The simplest way to deploy an application using daemon queue workers is to put the application in maintenance mode at the beginning of your deployment. This can be done using the `php artisan down` command. Once the application is in maintenance mode, Laravel will not accept any new jobs off of the queue, but will continue to process existing jobs.

데몬 queue worker 를 사용하여 애플리케이션을 deploy하기 위한 가장 쉬운 방법은 배포를 시작하는 시점에  `php artisan down` 명령을 사용하여 애플리케이션을 유지보수 모드로 변경하는 것입니다. 애플리케이션이 유지보수 모드에 들어가면 라라벨은 어떠한 새로운 작업들도 받아들이지 않을 것입니다. 다만 남아있던 작업들은 계속해서 진행됩니다.

The easiest way to restart your workers is to include the following command in your deployment script:
여러분의 workers를 재시작 하는 가장 쉬운 방법은 배포 스크립트(deployment script)에 아래의 명령을 포함하는 것입니다,

	php artisan queue:restart

This command will instruct all queue workers to restart after they finish processing their current job.
이 명령어는 모든 queue workers에게 현재 진행중인 작업들이 끝난 후 재시작하라고 지시할 것입니다.

> **Note:** This command relies on the cache system to schedule the restart. By default, APCu does not work for CLI commands. If you are using APCu, add `apc.enable_cli=1` to your APCu configuration.
>  **참고:** 이 명령은 재시작 일정을 잡기 위해 캐시 시스템에 의존합니다. 기본적으로 APCu는 CLI 명령에서 동작하지 않습니다. 만약 APCu를 사용하고 계신다면 APCu 환경 설정에 `apc.enable_cli=1`를 추가하시기 바랍니다.

### Coding For Daemon Queue Workers
### 데몬 큐 Workers 를 위한 코딩하기

Daemon queue workers do not restart the framework before processing each job. Therefore, you should be careful to free any heavy resources before your job finishes. For example, if you are doing image manipulation with the GD library, you should free the memory with `imagedestroy` when you are done.

데몬 큐  workers는 각각의 작업이 진행되기 전에 프레임워크를 재시작하지 않습니다. 따라서 많은 자원을 사용하는 작업을 사용하기 전에 주의해야합니다. 예를들어 GD 라이브러리로 이미지 처리를 하고 있다면 작업이 종료된 후 `imagedestroy`로 메모리를 비워주어야 합니다.

Similarly, your database connection may disconnect when being used by long-running daemon. You may use the `DB::reconnect` method to ensure you have a fresh connection.

비슷하게 데이터베이스 연결은 데몬이 장시간 동작할때 끊어질 수 있습니다. 여러분은 `DB::reconnect` 메소드를 사용하여 다시 커넥션을 가지도록 할 수 있습니다.

<a name="push-queues"></a>
## Push Queues
## 큐 푸쉬하기

Push queues allow you to utilize the powerful Laravel 5 queue facilities without running any daemons or background listeners. Currently, push queues are only supported by the [Iron.io](http://iron.io) driver. Before getting started, create an Iron.io account, and add your Iron credentials to the `config/queue.php` configuration file.
데몬이나 백그라운드 리스너(listener) 없이도 라라벨 5의 큐 기능을 마음껏 활용하실 수 있습니다. 현재 푸쉬 큐는  [Iron.io](http://iron.io) 드라이버에서만 지원되고 있습니다. 시작하기 전, Iron.io 계정을 만드시고 여러분의 Iron 인증 데이터를 `config/queue.php` 설정 파일에 추가하십시오.

#### Registering A Push Queue Subscriber
#### Push Queue Subscriber 등록하기

Next, you may use the `queue:subscribe` Artisan command to register a URL end-point that will receive newly pushed queue jobs:

다음으로 새로 푸쉬된 큐 작업들을 받을 엔드 포인트(end-point) URL를 `queue:subscribe` 아티즌 명령어로 등록합니다.

	php artisan queue:subscribe queue_name queue/receive

	php artisan queue:subscribe queue_name http://foo.com/queue/receive

Now, when you login to your Iron dashboard, you will see your new push queue, as well as the subscribed URL. You may subscribe as many URLs as you wish to a given queue. Next, create a route for your `queue/receive` end-point and return the response from the `Queue::marshal` method:

이제 Iron 대시보드에 로그인하면 구독중인 URL 뿐만 아니라 새롭게 푸쉬된 작업들을 볼 수 있습니다. 주어진 큐에 여러분들이 원하는 만큼의 URL들을 구독할 수 있습니다.  이제 `queue/receive` 엔드포인트(end-point)에 새로운 라우트를 생성하고 이 라우트에서 `Queue::marshal` 메소드로부터의 응답을 반환하도록 합니다:

	Route::post('queue/receive', function()
	{
		return Queue::marshal();
	});

The `marshal` method will take care of firing the correct job handler class. To fire jobs onto the push queue, just use the same `Queue::push` method used for conventional queues.

`marshal` 메소드는 올바른 작업 핸들러 클래스가 작동되도록 할 것입니다. 푸쉬된 큐에서 작업들을 처리하려면 기존에 큐에서 사용한것과 같은 `Queue::push` 메소드를 사용하십시오.

<a name="failed-jobs"></a>
## Failed Jobs
## 실패된 작업

Since things don't always go as planned, sometimes your queued jobs will fail. Don't worry, it happens to the best of us! Laravel includes a convenient way to specify the maximum number of times a job should be attempted. After a job has exceeded this amount of attempts, it will be inserted into a `failed_jobs` table. The failed jobs table name can be configured via the `config/queue.php` configuration file.

모든일들이 항상 계획한것처럼 진행되지 않기 때문에 종종 여러분들의 큐 작업은 실패하기도 합니다. 걱정하지 마십시오. 이것은 누구에게나 생길 수 있는 일입니다! 라라벨은 작업이 시도되는 최대 횟수를 지정하는 편리한 방법을 제공합니다. 작업이 제한된 횟수를 초과하는 경우 이 작업들은 `failed_jobs` 테이블에 추가됩니다. 이렇게 실패한 작업들이 담기는 테이블의 이름은 `config/queue.php` 환경설정 파일에서 설정하실 수 있습니다.

To create a migration for the `failed_jobs` table, you may use the `queue:failed-table` command:

`queue:failed-table` 명령을 사용하면 `failed_jobs` 테이블에 대한 마이그레이션을 만들 수 있습니다.

	php artisan queue:failed-table

You can specify the maximum number of times a job should be attempted using the `--tries` switch on the `queue:listen` command:

`queue:listen` 명령에서  `--tries` 스위치를 사용함으로써 재 작업하는 최대 횟수를 지정할 수 있습니다.

	php artisan queue:listen connection-name --tries=3

If you would like to register an event that will be called when a queue job fails, you may use the `Queue::failing` method. This event is a great opportunity to notify your team via e-mail or [HipChat](https://www.hipchat.com).

만약 작업이 실패되었을때 호출되는 이벤트를 등록하고 싶다면  `Queue::failing` 메소드를 사용할 수 있습니다. 이 이벤트는 여러분의 팀에게 이메일 혹은 [HipChat](https://www.hipchat.com)으로 알림을 보낼 수 있습니다.

	Queue::failing(function($connection, $job, $data)
	{
		//
	});

You may also define a `failed` method directly on a queue job class, allowing you to perform job specific actions when a failure occurs:

또한 작업이 실패하였을때 특정 작업을 수행하도록 큐 작업 클래스에 `failed` 메소드를 직접 정의할 수 있습니다.

	public function failed()
	{
		// Called when the job is failing...
	}

If your job is not self-handling and has a seperate handler class the `failed` method needs to be defined there instead.
작업을 직접 처리하지 않고, 별도의 분리된 클래스로 처리하고 있다면 해당 클래스에는 `failed` 메소드가 지정되어 있어야 합니다. 


### Retrying Failed Jobs
### 실패한 작업 다시 시도하기

To view all of your failed jobs, you may use the `queue:failed` Artisan command:
실패한 모든 작업들을 보기 위해 `queue:failed` 아티즌 명령을 사용할 수 있습니다.

	php artisan queue:failed

The `queue:failed` command will list the job ID, connection, queue, and failure time. The job ID may be used to retry the failed job. For instance, to retry a failed job that has an ID of 5, the following command should be issued:

 `queue:failed` 명령은 작업의 ID, 커넥션, 큐, 그리고 실패 시간을 목록으로 보여줍니다. 작업 ID는 실패한 작업을 다시 시도하기 위해 사용될 수 있습니다. 예를들어 5라는 ID를 가진 실패한 작업을 재시작하기 위해서는 아래의 명령을 실행해야 합니다.

	php artisan queue:retry 5

If you would like to delete a failed job, you may use the `queue:forget` command:
만약 실패한 작업을 삭제하기 위해서는 `queue:forget` 명령을 사용할 수 있습니다.

	php artisan queue:forget 5

To delete all of your failed jobs, you may use the `queue:flush` command:
실패한 모든 작업들을 삭제하기 위해서는 `queue:flush` 명령을 사용할 수 있습니다.

	php artisan queue:flush
