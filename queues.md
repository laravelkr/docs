# 큐(Queues)

- [환경설정](#configuration)
- [기본 사용법](#basic-usage)
- [클로져 큐](#queueing-closures)
- [큐 리스너(Listener) 실행하기](#running-the-queue-listener)
- [데몬 큐 작업자](#daemon-queue-worker)
- [큐 푸쉬하기](#push-queues)
- [실패된 작업](#failed-jobs)

<a name="configuration"></a>
## 환경 설정

라라벨 큐 컴포넌트는 다양한 큐 서비스로부터 통일된 API를 제공합니다. 큐를 사용하면 이메일을 보내는 작업과 같이 시간이 소요되는 작업을 나중에 처리할 수 있습니다. 이렇게 작업을 나중에 처리함으로서 여러분의 어플리케이션은 웹 요청을 더 빠르게 처리할 수 있습니다.

큐의 환경 설정 파일은 `config/queue.php`에 있습니다. 이 파일에서 프레임워크에 포함된 database, [Beanstalkd](http://kr.github.com/beanstalkd), [IronMQ](http://iron.io), [Amazon SQS](http://aws.amazon.com/sqs), [Redis](http://redis.io), null, 그리고 synchronous (로컬 사용을 위한)의 큐 드라이버 설정을 확인할 수 있습니다.

### 큐 데이터베이스 테이블

`database` 큐 드라이버를 사용하기 위해서는 작업들을 담아둘 데이터베이스 테이블이 필요합니다. 이 테이블을 추가하기 위한 마이그레이션을 생성하려면 `queue:table` 아티즌 명령을 실행하면 됩니다:

	php artisan queue:table

### 다른 큐 드라이버를 사용하는 데 필요한 의존 패키지들

아래의 의존 패키지들은 앞서 이야기 했던 각각의 큐 드라이버들을 사용하는 데 필요합니다.

- Amazon SQS: `aws/aws-sdk-php`
- Beanstalkd: `pda/pheanstalk ~3.0`
- IronMQ: `iron-io/iron_mq`
- Redis: `predis/predis ~1.0`

<!--chak-comment-큐(Queues)-환경-설정-->

<a name="basic-usage"></a>
## 기본 사용법

#### 큐에 작업을 푸쉬(Push) 하는 방법

어플리케이션에서 큐를 통해서 처리 할 수 있는 모든 작업들은 `App\Commands` 폴더 안에 저장됩니다. 큐를 통해서 처리할 새로운 작업을 생성하려면 아티즌 CLI를 사용하면 됩니다:

	php artisan make:command SendEmail --queued

새로운 작업을 큐에 푸쉬하기 위해 `Queue::push` 메소드를 사용합니다:

	Queue::push(new SendEmail($message));

> **주의:** 이 예제에서는 `Queue` 파사드를 직접적으로 사용하고 있습니다. 그러나 일반적으로는 [Command Bus](/docs/5.0/bus)를 통해 큐에 작업을 보내게 됩니다. 이 페이지는 이후에도 계속 `Queue` 파사드를 사용하겠지만 어플리케이션에서 큐와 동기화 명령을 모두 실행 할 수 있으므로 커맨드 버스를 사용하는 것에도 익숙해져야 합니다.

기본적으로 `make:command` 아티즌 명령어는 `handle` 메소드가 커멘드에 직접 추가되는  “self-handling” 명령을 생성합니다. 이 메소드는 작업이 큐에 의해 실행될 때 호출될 것입니다. 여러분은 `handle` 메소드에서 필요로 하는 의존 객체들을 type-hint로 지정 할 수 있으며 [service container](/docs/5.0/container)는 자동으로 이 의존 객체들을 추가할 것입니다:

	public function handle(UserRepository $users)
	{
		//
	}

만약 여러분의 커멘드가 분리된 핸들러(handler) 클래스를 가지기를 원한다면 `--handler` flag를 `make:command` 커멘드에 추가하면 됩니다.

	php artisan make:command SendEmail --queued --handler

생성된 핸들러(handler)는 `App\Handlers\Commands`에 위치하고 IoC 컨테이너를 통해서 의존성이 해결(resolve)됩니다.

#### 작업에 대한 큐(대기열) / 튜브 지정하기

여러분은 작업을 보낼 큐와 튜브를 직접 지정할 수 있습니다:

	Queue::pushOn('emails', new SendEmail($message));

#### 여러 작업들에 같은 페이로드(Payload) 전달하기

만약 여러 큐 작업에 같은 데이터를 보내야 하는 경우 `Queue::bulk` 메소드를 사용할 수 있습니다:

	Queue::bulk([new SendEmail($message), new AnotherCommand]);

#### 작업을 지연시켜 실행하기

때때로 여러분은 큐 작업을 지연시키기를 원할 수 있습니다. 예를 들어, 고객이 가입후 15분 후에 이메일을 보내도록 큐 작업을 지연시키길 원할 수 있습니다. `Queue::later` 메소드를 사용하면 이와 같은 작업을 수행할 수 있습니다:

	$date = Carbon::now()->addMinutes(15);

	Queue::later($date, new SendEmail($message));

이 예제에서는 우리가 할당하고자 하는 작업에 시간 지연을 설정하기 위해 [Carbon](https://github.com/briannesbitt/Carbon) 이라는 날짜 라이브러리를 사용했습니다. 또 라이브러리 없이 지연시키기를 원하는 만큼의 초에 대한 시간 값을 정수(integer) 형태로 전달할 수도 있습니다.

> **참고** 아마존 SQS 서비스는 900초의 제한 시간을 가지고 있습니다. (15분)

#### 큐와 Eloquent Models

만약 대기된 작업이 구조체 안에서 Eloquent 모델을 받아들인다면 모델에 대한 ID만 큐에 직렬화(serialized)를 통해 전달 됩니다.

작업이 실제로 처리될때 큐 시스템은 전체 모델 인스턴스를 데이터베이스로부터 자동으로 다시 조회할 것입니다.

이러한 방법은 여러분의 어플리케이션에 대해 완전히 알기쉽게 하고 전체 Eloquent 모델 인스턴스를 직렬화(serializing) 하는 데서 발생할 수 있는 이슈들을 방지합니다.

#### 처리된 작업 삭제하기

작업이 처리되었을때 이 작업은 큐에서 삭제되어야 합니다. 만약 여러분의 작업이 진행되는동안 예외 처리가 없으면 작업은 자동으로 완료될 것입니다.

만약 작업을 수동으로 `delete` 혹은 `release` 하기 원한다면, `Illuminate\Queue\InteractsWithQueue` trait가 큐 작업에 대한 `release`와 `delete` 메소드를 제공하여 원하는 것을 수행할 수 있습니다. `release` 메소드는 작업이 다시 할 수 있을때까지 기다리는 시간값을 전달받습니다.

	public function handle(SendEmail $command)
	{
		if (true)
		{
			$this->release(30);
		}
	}

#### 큐에 작업 다시 넣기

만약 작업이 진행하는동안 예외(Exception)가 발생한다면 이 작업은 자동으로 다시 시도될 수 있도록 큐에 다시 보내집니다. 다시 보내진 작업은 어플리케이션에서 설정된 최대 허용된 횟수까지 반복해서 재시도됩니다. 최대 시도 횟수는 `queue:listen` 혹은 `queue:work` 아티즌 명령에서 사용된 `--tries` 스위치에 의해 정의됩니다.

#### 작업이 시도된 횟수 체크하기

만약 작업이 진행하는동안 예외가 발생한다면 이 작업은 다시 큐에 보내집니다. `attemps` 메소드를 사용하여 작업이 시도된 횟수를 체크할 수 있습니다:

	if ($this->attempts() > 3)
	{
		//
	}

> **주의** 이 메소드를 호출하기 위해 커맨드 / 핸들러는 `Illuminate\Queue\InteractsWithQueue` trait를 반드시 사용해야만 합니다.

<!--chak-comment-큐(Queues)-기본-사용법-->

<a name="queueing-closures"></a>
## 클로져 큐

큐(Queue)에는 클로져도 등록할 수 있습니다. 클로저는 빠르고 간단한 작업을 큐를 통햇 처리해야 할 경우에 매우 편리합니다:

#### 큐에 클로져 푸쉬하기

	Queue::push(function($job) use ($id)
	{
		Account::delete($id);

		$job->delete();
	});

> **주의** : 큐에 푸쉬하는 클로저에 객체를 사용하도록 전달하는 대신 `use`를 사용하여 기본 키를 전달하고 큐 작업 안에서 관련 모델을 다시 가져오도록 구성하십시오. 이렇게하면 직렬화에서 발생할 수 있는 예기치 않은 영향을 방지 할 수 있습니다.

Iron.io [push queues](#push-queues)를 이용하는 경우,  큐로 푸쉬하는 클로저에 추가 예방 조치를 취해야 합니다. 큐 메세지를받는 부분에서 요청이 실제로 Iron.io에서 왔는지 검증하는 토큰을 체크해야 합니다. 예를 들어, 푸쉬된 큐를받는 URL은 `https://yourapp.com/queue/receive?token=SecretToken`와 같이 되어야 합니다.  그런 다음에는 큐 요청을 진행하기 전에 어플리케이션에서 비밀 토큰 값을 체크하도록 합니다.

<!--chak-comment-큐(Queues)-클로져-큐-->

<a name="running-the-queue-listener"></a>
## 큐 리스너(Listener) 실행하기

라라벨은 큐에 푸쉬된 새로운 작업들을 실행 시키는 아티즌 작업을 포함하고 있습니다. `queue:listen` 커멘드를 사용하여 이 작업을 실행할 수 있습니다.

#### Queue Listener 시작하기

	php artisan queue:listen

listener가 사용해야 하는 큐 연결을 지정할 수도 있습니다:

	php artisan queue:listen connection

참고로 작업이 시작되면, 큐 리스너는 수동으로 정지 시킬 때까지 계속 작동할 것입니다. [Supervisor](http://supervisord.org/)와 같은 프로세스 모니터를 사용하여 반드시 큐 리스너가 중단되지 않도록 해야 합니다.

큐 작업의 우선순위를 정하려면 `listen` 명령에 쉼표(,)로 결정된 큐 연결 목록들의 순서를 넘겨줄 수 있습니다.

	php artisan queue:listen --queue=high,low

이 예제에서,  `high-connection`의 작업은  항상  `low-connection` 작업보다 먼저 처리됩니다.

#### 작업 제한 시간에 대한 매개 변수 지정하기

각 작업이 실행되는데 허용하는 시간을 초 단위로 설정할 수 있습니다:

	php artisan queue:listen --timeout=60

#### 큐 대기(sleep) 시간 지정하기

추가로 새로운 작업이 시작하기 전 기다리는 대기 시간을 초 단위로 지정할 수 도 있습니다:

	php artisan queue:listen --sleep=5

대기열에 작업이 없는 경우에만 큐가 일시적으로 “잠들어 있는” 것에 주의하십시오. 만약 작업이 존재한다면, 큐는 대기 않고 계속 작업을 처리합니다.

#### 큐의 첫 번째 작업을 처리하기

큐에 들어 있는 첫 번째 작업만을 처리하기 위해서는 `queue:work` 명령을 사용할 수 있습니다:

	php artisan queue:work

<!--chak-comment-큐(Queues)-큐-리스너(Listener)-실행하기-->

<a name="daemon-queue-worker"></a>
## 데몬 큐 작업자

`queue:work` 명령에는 프레임워크의 재부팅 없이도 지속적으로 작업을 처리하도록 큐 실행을 강제하는 `--daemon` 옵션이 포함되어있습니다.  이를 통하면 `queue:listen` 명령과 비교하여 CPU 사용이 현저하게 줄일 수 있습니다.  하지만 현재 실행중인 실행중인 작업의 큐를 비우는 데에 복잡한 절차가 추가로 필요하게 됩니다.

큐 작업자(worker)를 데몬에서 시작하기 위해 `--daemon` flag를 사용할 수 있습니다:

	php artisan queue:work connection --daemon

	php artisan queue:work connection --daemon --sleep=3

	php artisan queue:work connection --daemon --sleep=3 --tries=3

보시는 바와 같이 `queue:work` 명령어는 `queue:listen`에서 이용가능한 대부분의 같은 옵션들을 제공하고 있습니다. `php artisan help queue:work` 명령어를 사용하면 사용 가능한 모든 옵션들을 볼 수 있습니다.

### 데몬 큐 작업자에 의한 배포

데몬 queue worker를 사용하여 어플리케이션을 deploy하기 위한 가장 쉬운 방법은 배포를 시작하는 시점에  `php artisan down` 명령을 사용하여 어플리케이션을 유지보수 모드로 변경하는 것입니다. 어플리케이션이 유지보수 모드에 들어가면 라라벨은 어떠한 새로운 작업들도 받아들이지 않을 것입니다. 다만 남아있던 작업들은 계속해서 진행됩니다.

여러분의 workers를 재시작 하는 가장 쉬운 방법은 배포 스크립트(deployment script)에 아래의 명령을 포함하는 것입니다:

	php artisan queue:restart

이 명령어는 모든 queue workers에게 현재 진행중인 작업들이 끝난 후 재시작하라고 지시할 것입니다.

>  **참고:** 이 명령은 재시작 일정을 잡기 위해 캐시 시스템에 의존합니다. 기본적으로 APCu는 CLI 명령에서 동작하지 않습니다. 만약 APCu를 사용하고 계신다면 APCu 환경 설정에 `apc.enable_cli=1`를 추가하시기 바랍니다.

### 데몬 큐 Workers를 위한 코딩하기

데몬 큐  workers는 각각의 작업이 진행되기 전에 프레임워크를 재시작하지 않습니다. 따라서 많은 자원을 사용하는 작업을 사용하기 전에 주의해야 합니다. 예를 들어, GD 라이브러리로 이미지 처리를 하고 있다면 작업이 종료된 후 `imagedestroy`로 메모리를 비워주어야 합니다.

비슷하게 데이터베이스 연결은 데몬이 장시간 동작할때 끊어질 수 있습니다. 여러분은 `DB::reconnect` 메소드를 사용하여 다시 커넥션을 가지도록 할 수 있습니다.

<!--chak-comment-큐(Queues)-데몬-큐-작업자-->

<a name="push-queues"></a>
## 큐 푸쉬하기

데몬이나 백그라운드 리스너(listener) 없이도 라라벨 5의 큐 기능을 마음껏 활용하실 수 있습니다. 현재 푸쉬 큐는  [Iron.io](http://iron.io) 드라이버에서만 지원되고 있습니다. 시작하기 전, Iron.io 계정을 만드시고 여러분의 Iron 인증 데이터를 `config/queue.php` 설정 파일에 추가하십시오.

#### Push Queue Subscriber 등록하기

다음으로 새로 푸쉬된 큐 작업들을 받을 엔드 포인트(end-point) URL를 `queue:subscribe` 아티즌 명령어로 등록합니다.

	php artisan queue:subscribe queue_name queue/receive

	php artisan queue:subscribe queue_name http://foo.com/queue/receive

이제 Iron 대시보드에 로그인하면 구독중인 URL 뿐만 아니라 새롭게 푸쉬된 작업들을 볼 수 있습니다. 주어진 큐에 여러분이 원하는 만큼의 URL들을 구독할 수 있습니다.  이제 `queue/receive` 엔드포인트(end-point)에 새로운 라우트를 생성하고 이 라우트에서 `Queue::marshal` 메소드로부터의 응답을 반환하도록 합니다:

	Route::post('queue/receive', function()
	{
		return Queue::marshal();
	});

`marshal` 메소드는 올바른 작업 핸들러 클래스가 작동되도록 할 것입니다. 푸쉬된 큐에서 작업들을 처리하려면 기존에 큐에서 사용한것과 같은 `Queue::push` 메소드를 사용하십시오.

<!--chak-comment-큐(Queues)-큐-푸쉬하기-->

<a name="failed-jobs"></a>
## 실패된 작업

모든일들이 항상 계획한것처럼 진행되지 않기 때문에 종종 여러분의 큐 작업은 실패하기도 합니다. 걱정하지 마십시오. 이것은 누구에게나 생길 수 있는 일입니다! 라라벨은 작업이 시도되는 최대 횟수를 지정하는 편리한 방법을 제공합니다. 작업이 제한된 횟수를 초과하는 경우 이 작업들은 `failed_jobs` 테이블에 추가됩니다. 이렇게 실패한 작업들이 담기는 테이블의 이름은 `config/queue.php` 환경설정 파일에서 설정하실 수 있습니다.

`queue:failed-table` 명령을 사용하면 `failed_jobs` 테이블에 대한 마이그레이션을 만들 수 있습니다.

	php artisan queue:failed-table

`queue:listen` 명령에서  `--tries` 스위치를 사용함으로써 재 작업하는 최대 횟수를 지정할 수 있습니다.

	php artisan queue:listen connection-name --tries=3

만약 작업이 실패되었을때 호출되는 이벤트를 등록하고 싶다면  `Queue::failing` 메소드를 사용할 수 있습니다. 이 이벤트는 여러분의 팀에게 이메일 혹은 [HipChat](https://www.hipchat.com)으로 알림을 보낼 수 있습니다.

	Queue::failing(function($connection, $job, $data)
	{
		//
	});

또한, 작업이 실패하였을때 특정 작업을 수행하도록 큐 작업 클래스에 `failed` 메소드를 직접 정의할 수 있습니다.

	public function failed()
	{
		// Called when the job is failing...
	}

작업을 직접 처리하지 않고, 별도의 분리된 클래스로 처리하고 있다면 해당 클래스에는 `failed` 메소드가 선언되어 있어야 합니다. 

### 실패한 작업 다시 시도하기

실패한 모든 작업들을 보기 위해 `queue:failed` 아티즌 명령을 사용할 수 있습니다:

	php artisan queue:failed

 `queue:failed` 명령은 작업의 ID, 커넥션, 큐, 그리고 실패 시간을 목록으로 보여줍니다. 작업 ID는 실패한 작업을 다시 시도하기 위해 사용될 수 있습니다. 예를 들어, 5라는 ID를 가진 실패한 작업을 재시작하기 위해서는 아래의 명령을 실행해야 합니다.

	php artisan queue:retry 5

만약 실패한 작업을 삭제하기 위해서는 `queue:forget` 명령을 사용할 수 있습니다:

	php artisan queue:forget 5

실패한 모든 작업들을 삭제하기 위해서는 `queue:flush` 명령을 사용할 수 있습니다:

	php artisan queue:flush

<!--chak-comment-큐(Queues)-실패된-작업-->
