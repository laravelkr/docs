# Queues-큐

- [시작하기](#introduction)
- [작업 클래스를 작성하기](#writing-job-classes)
    - [작업 클래스 생성하기](#generating-job-classes)
    - [작업 클래스 구조](#job-class-structure)
- [큐에 작업을 푸쉬(Push) 하는 방법](#pushing-jobs-onto-the-queue)
    - [작업을 지연시켜 실행하기](#delayed-jobs)
    - [HTTP Request 에서 작업 Dispatching 하기](#dispatching-jobs-from-requests)
    - [작업 이벤트](#job-events)
- [큐 Listener 구동하기](#running-the-queue-listener)
    - [Supervisor 설정하기](#supervisor-configuration)
    - [데몬 큐 Listener](#daemon-queue-listener)
    - [큐 Listener 데몬 배포하기](#deploying-with-daemon-queue-listeners)
- [실패한 작업 처리하기](#dealing-with-failed-jobs)
    - [실패한 작업에 대한 이벤트](#failed-job-events)
    - [실패한 작업 다시 시도하기](#retrying-failed-jobs)

<a name="introduction"></a>
## 시작하기

라라벨 큐 서비스는 다양한 큐 시스템으로부터 통일된 API를 제공합니다. 큐를 사용하면 이메일을 보내는 작업과 같이 시간이 소요되는 작업을 나중에 처리할 수 있습니다. 이렇게 작업을 나중에 처리함으로서 여러분의 애플리케이션은 웹 요청을 더 빠르게 처리할 수 있습니다.

<a name="configuration"></a>
### 설정하기

큐의 환경 설정 파일은 `config/queue.php`에 위치하고 있습니다. 이 파일에서 프레임워크에 포함된 database, [Beanstalkd](http://kr.github.com/beanstalkd), [IronMQ](http://iron.io), [Amazon SQS](http://aws.amazon.com/sqs), [Redis](http://redis.io), 그리고 synchronous (로컬 사용을 위한)의 큐 드라이버 설정을 확인할 수 있습니다.

`null` 큐 드라이버는 큐 작업을 비활성화합니다. 

### 드라이버 요구사항

#### 데이터베이스

`database` 큐 드라이버를 사용하기 위해서는 작업들을 담아둘 데이터베이스 테이블이 필요합니다. 이 테이블을 추가하기 위한 마이그레이션을 생성하려면 `queue:table` 아티즌 명령을 실행하면 됩니다. 마이그레이션 파일이 생성되고 나면 `migrate` 명령어를 사용하여 데이터베이스 테이블을 생성할 수 있습니다:

    php artisan queue:table

    php artisan migrate

#### 다른 큐 드라이버의 의존 패키지

아래의 의존 패키지들은 앞서 이야기 했던 각각의 큐 드라이버들을 사용하는데 필요합니다.

- Amazon SQS: `aws/aws-sdk-php ~3.0`
- Beanstalkd: `pda/pheanstalk ~3.0`
- IronMQ: `iron-io/iron_mq ~2.0|~4.0`
- Redis: `predis/predis ~1.0`

<a name="writing-job-classes"></a>
## 작업 클래스를 작성하기

<a name="generating-job-classes"></a>
### 작업 클래스 생성하기

기본적으로, 애플리케이션을 위한 모든 큐 작업들은 `app/Jobs` 디렉토리에 저장됩니다. 새로운 큐 작업 클래스를 아티즌 CLI를 통해서 생성할 수 있습니다: 

    php artisan make:job SendReminderEmail --queued

이 명령어는 `app/Jobs` 디렉토리에 새로운 클래스를 생성할 것입니다, 그리고 이 클래스는 라라벨에 작업이 동기적으로 실행되는 대신에 큐를 통해서 처리된다는 것을 알려주며, `Illuminate\Contracts\Queue\ShouldQueue`를 구현하게 될 것입니다. 

<a name="job-class-structure"></a>
### 작업 클래스 구조

작업 클래스는 매우 간단하며, 기본적으로 큐에 저장된 작업을 처리하기 위해서 불려지는 `handle` 메소드만을 가지고 있습니다. 시작에 앞서, 다음 작업 클래스의 예제를 살펴봅시다: 

    <?php

    namespace App\Jobs;

    use App\User;
    use App\Jobs\Job;
    use Illuminate\Contracts\Mail\Mailer;
    use Illuminate\Queue\SerializesModels;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Contracts\Bus\SelfHandling;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class SendReminderEmail extends Job implements SelfHandling, ShouldQueue
    {
        use InteractsWithQueue, SerializesModels;

        protected $user;

        /**
         * Create a new job instance.
         *
         * @param  User  $user
         * @return void
         */
        public function __construct(User $user)
        {
            $this->user = $user;
        }

        /**
         * Execute the job.
         *
         * @param  Mailer  $mailer
         * @return void
         */
        public function handle(Mailer $mailer)
        {
            $mailer->send('emails.reminder', ['user' => $this->user], function ($m) {
                //
            });

            $this->user->reminders()->create(...);
        }
    }

이 예제에서 큐 작업 클래스의 생성자에 [Eloquent 모델](/docs/{{version}}/eloquent)이 직접적으로 전달된다는 것을 주목하십시오. 작업 클래스에서 사용하는 SerializesModels 트레이트-trait에 의해 Eloquent 모델은 효과적으로 serialize 될것이며, 작업이 처리 될 때 unserialize 됩니다. 큐에 저장된 작업이 생성자에서 Eloquent 모델을 전달 받는 경우, 모델의 식별자만 큐로 저장될 때 serialize 될 것입니다. 작업이 실제로 처리될 때 큐 시스템은 자동으로 데이터베이스에서 해당 모델 인스턴스를 다시 가져옵니다. 이렇게 하는 것은 애플리케이션을 완전히 투명하게 하고, Eloquent 모델 인스턴스를 serialize 할 때 발생하는 문제를 방지 할 수 있습니다.

큐에 의해서 작업이 처리될 때에는 `handle` 메소드가 호출 됩니다. 작업 클래스의 `handle` 메소드에 의존 객체들이 타입-힌트 될 수 있다는 것에 주의하십시오. 라라벨의  [서비스 컨테이너](/docs/{{version}}/container)가 자동으로 의존 객체들을 주입해 줍니다. 

#### 작업이 잘못된 경우

작업이 처리되는 동안 예외가 발생하면, 작업은 큐에 의해서 해제되어 자동으로 다시 처리 시도를 하게 됩니다. 작업은 애플리케이션에서 허용하는 최대 횟수만큼 다시 실행됩니다. 최대 실행 횟수는 `queue:listen` 또는 `queue:work` 아티즌 작업에 정의된 횟수입니다. 큐 listener 를 실행하는 보다 자세한 사항은 [이후](#running-the-queue-listener)에 살펴보겠습니다. 

#### 수동으로 작업 해제하기

작업을 수동으로 `release-해제` 하고자 한다면, 생성된 작업 클래스에 포함되어 있는 `InteractsWithQueue` 트레이트-trait가 제공하는 `release` 메소드를 사용하면 됩니다. `release` 메소드는 하나의 인자를 전달 받습니다: 인자는 작업을 다시 수행하기 위한 대기시간을 초단위로 나타냅니다:

    public function handle(Mailer $mailer)
    {
        if (condition) {
            $this->release(10);
        }
    }

#### 실행 시도 횟수 체크

앞서 확인한 바와 같이, 작업이 처리되는동안 예외가 발생하게 되면, 자동으로 작업을 큐로 해제하게 됩니다. 여러분은 `attempts` 메소드를 사용하여 작업이 재시도 되는 횟수를 확인할 수 있습니다: 

    public function handle(Mailer $mailer)
    {
        if ($this->attempts() > 3) {
            //
        }
    }

<a name="pushing-jobs-onto-the-queue"></a>
## 큐에 작업을 푸쉬(Push) 하는 방법

`app/Http/Controllers/Controller.php` 에 있는 라라벨의 기본 컨트롤러는 `DispatchesJobs` 트레이트-trait 를 사용하도록(use) 구성되어 있습니다. 이 트레이트는 `dispatch` 메소드와 같이 작업을 큐로 넣는 편리한 몇가지 메소드를 제공합니다: 

    <?php

    namespace App\Http\Controllers;

    use App\User;
    use Illuminate\Http\Request;
    use App\Jobs\SendReminderEmail;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Send a reminder e-mail to a given user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function sendReminderEmail(Request $request, $id)
        {
            $user = User::findOrFail($id);

            $this->dispatch(new SendReminderEmail($user));
        }
    }

물론, 때로는 라우트나 컨트롤러가 아닌 애플리케이션의 어디선가에서 작업을 큐로 처리하고자 할 수도 있습니다. 이러한 이유로, 애플리케이션의 어떤 클래스에서도 `DispatchesJobs` 트레이트-trait를 포함시킬 수 있으며, 트레이트의 다양한 dispatch 메소드를 사용할 수 있습니다. 다음은 이 트레이트-trait를 사용하는 간단한 클래스 예제 입니다. 

    <?php

    namespace App;

    use Illuminate\Foundation\Bus\DispatchesJobs;

    class ExampleClass
    {
        use DispatchesJobs;
    }

#### 작업을 처리하기 위한 큐를 지정하기

작업이 보내져야할 큐를 지정할 수도 있습니다. 

작업을 각기 다른 큐로 전달하는 것은, 큐로 처리할 작업들을 분류하고, 얼마나 많은 작업 처리자를 각각의 큐에 할당하는지 우선순위를 정하는 것입니다. 이 의미는 작업을 큐 설정 파일에 정의된 별도의 큐 "커넥션-connections"으로 보낸다는 것이 아니라, 하나의 커넥션 안에서 큐를 지정한다는 것입니다. 큐를 지정하기 위해서는 작업 인스턴스에서 `onQueue` 메소드를 사용하면 됩니다. `onQueue` 메소드는 `Illuminate\Bus\Queueable` 트레이트-trait에 의해서 제공되며 `App\Jobs\Job` 기본 클래스에 이미 포함되어 있습니다. 

    <?php

    namespace App\Http\Controllers;

    use App\User;
    use Illuminate\Http\Request;
    use App\Jobs\SendReminderEmail;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Send a reminder e-mail to a given user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function sendReminderEmail(Request $request, $id)
        {
            $user = User::findOrFail($id);

            $job = (new SendReminderEmail($user))->onQueue('emails');

            $this->dispatch($job);
        }
    }

<a name="delayed-jobs"></a>
### 작업을 지연시켜 실행하기

때때로 여러분들은 큐 작업을 지연시키기를 원할 수 있습니다. 예를들어 고객이 가입후 15분 후에 알림 이메일을 보내도록 큐 작업을 지연시키길 원할 수 있습니다. `Illuminate\Bus\Queueable` 트레이트-trait에 의해서 제공되는 작업 클래스의 `delay` 메소드를 사용하면 이와 같은 작업을 수행할 수 있습니다:

    <?php

    namespace App\Http\Controllers;

    use App\User;
    use Illuminate\Http\Request;
    use App\Jobs\SendReminderEmail;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Send a reminder e-mail to a given user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function sendReminderEmail(Request $request, $id)
        {
            $user = User::findOrFail($id);

            $job = (new SendReminderEmail($user))->delay(60);

            $this->dispatch($job);
        }
    }

이 예제에서는, 작업이 worker 에 의해서 수행 가능해질 때까지 60초 동안 지연시킨다고 지정하고 있습니다. 

> **주의:** 아마존 SQS 서비스는 최대 15분까지 지연시킬 수 있습니다. 

<a name="dispatching-jobs-from-requests"></a>
### HTTP Request 에서 작업 Dispatching 하기

HTTP request 를 큐 작업을 통해서 처리하고자 하는 것은 매우 일반적입니다. 따라서 매번 각각의 request 에 대해서 수동으로 이를 지시하는 대신 라라벨은 몇가지 이를 쉽게 처리할 수 있는 헬퍼 메소드를 제공합니다. `DispatchesJobs` 트레이트-trait의 `dispatchFrom` 메소드를 살펴보도록 하겠습니다. 기본적으로 이 트레이트-trait는 라라벨의 기본 컨트롤러 클래스에 포함되어 있습니다: 

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

    class CommerceController extends Controller
    {
        /**
         * Process the given order.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function processOrder(Request $request, $id)
        {
            // Process the request...

            $this->dispatchFrom('App\Jobs\ProcessOrder', $request);
        }
    }

이 메소드는 주어진 작업 클래스의 생성자를 조사하고, 작업의 생성자에서 필요한 파라미터를 채우기 위해서, HTTP request(또는 다른 `ArrayAccess` 객체)로 부터 변수를 추출할 것입니다. 따라서 작업 클래스의 생성자에서 `productId` 변수를 받도록 되어 있으면, 작업은 HTTP request 로 부터 `productId` 파라미터를 꺼내도록 시도할 것입니다. 

여러분은 또한 `dispatchFrom` 메소드의 세번째 인자로 배열을 전달할 수 있습니다. 이 배열은 request 로 부터 사용할 수 없는 생성자 파라미터를 채우는데 사용될 것입니다. 

    $this->dispatchFrom('App\Jobs\ProcessOrder', $request, [
        'taxPercentage' => 20,
    ]);

<a name="job-events"></a>
### 작업 이벤트

#### 작업 완료 이벤트

`Queue::aftet` 메소드를 통해서 큐를 통해서 작업이 성공적으로 실행되고나면 호출되는 콜백을 등록할 수 있습니다. 이 콜백은 추가적인 로깅, 후속작업을 큐로 처리하기, 또는 대시보드를 위한 정보를 갱신등의 작업을 수행하기에 좋습니다. 다음의 예와 같이, 라라벨에 포함 된 `AppServiceProvider` 에 이벤트 콜백을 추가할 수 있습니다: 

    <?php

    namespace App\Providers;

    use Queue;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Queue::after(function ($connection, $job, $data) {
                //
            });
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

<a name="running-the-queue-listener"></a>
## 큐 Listener 구동하기

#### 큐 Listener 시작하기

라라벨은 큐로 추가될 새로운 작업을 구동시킬 아티즌 명령어를 가지고 있습니다. `queue:listen` 명령어를 사용하여 listener 를 구동할 수 있습니다: 

    php artisan queue:listen

listener가 사용해야하는 큐 connection-커넥션을 지정할 수도 있습니다: 

    php artisan queue:listen connection

이 명령어가 시작되면, 수동으로 중지하지 않는 한 계속 실해된다는 것에 주의하십시오. [Supervisor](http://supervisord.org/) 같은 프로세스 모니터를 이용하면 큐 listener가 확실히 중단없이 계속 작동하도록 보장됩니다.

#### 큐의 우선순위

작업의 큐 우선순위를 설정하기 위해서 `listen` 명령어의 큐 connection-커넥션의 목록에 콤마(,)를 전달할 수 있습니다:

    php artisan queue:listen --queue=high,low

이 예제에서는 `high` 우선순위의 작업은 항상 `low` 작업을 처리하기 전에 처리됩니다.

#### 작업의 시간 제한 파라미터 지정하기

여러분은 또한 각작업이 실행되어야할 시간의 제한의 길이(초단위)를 설정할 수도 있습니다:

    php artisan queue:listen --timeout=60

#### 큐 일시 정지 시간 지정하기

추가적으로, 새로운 작업을 polling하기 전에 대기 시간을 초단위의 숫자로 지정할 수도 있습니다:

    php artisan queue:listen --sleep=5

큐동작은 큐에 아무런 작업이 없는 경우에만 "잠자기"를 한다는 것에 주의하십시오. 사용가능한 작업이 있는경우, 큐는 대기 없이 계속 작업을 처리 할것입니다. 

<a name="supervisor-configuration"></a>
### Supervisor 설정하기

Supervisor는 리눅스 OS를 위한 프로세스 모니터로, 명령이 실패한 경우 자동으로 `queue:listen` 또는 `queue:work`를 재시작 할 것입니다. 우분투에서 Supervisor를 설치하기 위해서는 다음의 명령어를 사용하면 됩니다: 

    sudo apt-get install supervisor

Supervisor 설정 파일은 일반적으로 `/etc/supervisor/conf.d` 디렉토리에 저장되어 있습니다. 이 디렉토리 안에서 Supervisor가 어떻게 프로세스를 모니터링 할 것인지 지시하는 설정 파일을 원하는 수 만큼 생성할 수 있습니다. 예를 들어, `laravel-worker.conf` 파일을 만들고 `queue:work` 프로세스를 시작하고, 모니터링 해 보겠습니다. 

    [program:laravel-worker]
    process_name=%(program_name)s_%(process_num)02d
    command=php /home/forge/app.com/artisan queue:work sqs --sleep=3 --tries=3 --daemon
    autostart=true
    autorestart=true
    user=forge
    numprocs=8
    redirect_stderr=true
    stdout_logfile=/home/forge/app.com/worker.log

이 예제에서, `numprocs` 지시어는 Supervisor에 총 8 개의 `queue:work` 프로세스를 실행하고 이들을 모니터링하여, 이 프로세스가 죽어 있으면, 자동으로 재시작하도록 지시하고 있습니다. 당연히, `command` 지시어의 `queue:work sqs` 부분을 변경하고 선택한 큐 커넥션에 맞추도록 해야합니다.

설정 파일이 생성되고 나면, 다음 명령어를 통해서 Supervisor 설정을 변경하고, 시작할 수 있습니다: 

    sudo supervisorctl reread

    sudo supervisorctl update

    sudo supervisorctl start laravel-worker:*

Supervisor의 설정 및 사용에 대한 보다 자세한 내용은 [Supervisor 문서](http://supervisord.org/index.html)를 참고하십시오. 또는 [라라벨 Forge](https://forge.laravel.com)를 사용하여 자동으로 Supervisor를 설정하고 관리할 수 있는 편리한 웹 인터페이스를 사용할 수 있습니다. 

<a name="daemon-queue-listener"></a>
### 데몬 큐 Listener

`queue:work` 아티즌 명령어는 프레임 워크를 재시작하지 않고 계속하여 작업을 처리하도록 큐 worker를 강제하는 `--daemon` 옵션을 포함하고 있습니다. 이를 통해서 `queue:listen` 과 비교하여 CPU 사용률을 크게 낮출 수 있습니다.

큐 worker를 데몬 모드로 시작하기 위해서는, --daemon 플래그를 사용합니다.

    php artisan queue:work connection --daemon

    php artisan queue:work connection --daemon --sleep=3

    php artisan queue:work connection --daemon --sleep=3 --tries=3

보시는 바와 같이, `queue:work` 명령은 `queue:listen` 에서 사용할 수 있는 것과 거의 동일한 옵션을 지원합니다. `php artisan help queue:work` 명령어를 사용하여 가능한 모든 옵션을 확인할 수 있습니다.

#### 데몬 큐 Listener 를 고려하여 코딩하기

데몬 큐 worker는 각각의 작업을 처리하기 전에 프레임워크를 다시 시작하지 않습니다. 따라서 많은 자원을 사용하는 작업을 완료하기 전에 자원을 해제하는 것에 주의를 기울여야만 합니다. 예들 들어, GD 라이브러리를 사용하여 이미지를 처리하는 경우, 작업이 완료되면 `imagedestory` 를 사용하여 메모리를 해제해야만 합니다. 

뿐만 아니라, 장시간 실행되는 데몬이 사용되는 동안 여러분의 데이터베이스 커넥션이 끊어 질 수도 있습니다. 새로운 연결을 위해서 `DB::reconnect` 메소드를 사용할 필요가 있을 것입니다. 

<a name="deploying-with-daemon-queue-listeners"></a>
### 큐 Listener 데몬 배포하기

데몬 큐 worker는 장시간 유지되는 프로세스이기 때문에, 재시작하기 전에는 코드의 변경사항이 반영되지 않을 것입니다. 따라서, 데몬 큐 workder를 사용하는 애플리케이션을 배포하는 가장 간단한 방법은 스크립트를 배호하는 동안 workder를 재시작하는 것입니다. 여러분의 배포 스크립트에서 다음의 명령어를 통해서 손쉽게 모든 worker를 재시작 할 수 있습니다. 

    php artisan queue:restart

이 명령어는 모든 큐 worker 들이 그들이 수행중인 현재의 작업들이 완료되고, 현재 존재하는 작업들이 손실되지 않도록 부드럽게 재시작 되도록 할 것입니다.

> **주의:** 이 명령어는 재시작을 위한 스케줄 작업에서 캐시 시스템에 의존하고 있습니다. 기본적으로 APCu는 CLI 작업을 위해서 작동하지 않습니다. 여러분이 APCu를 사용중이라면, APCu 설정에 `apc.enable_cli=1` 을 추가하십시오. 

<a name="dealing-with-failed-jobs"></a>
## 실패한 작업 처리하기

모든일들이 항상 계획한것처럼 진행되지 않기 때문에 종종 여러분들의 큐 작업은 실패하기도 합니다. 걱정하지 마십시오. 이것은 누구에게나 생길 수 있는 일입니다! 라라벨은 작업이 시도되는 최대 횟수를 지정하는 편리한 방법을 제공합니다. 작업이 제한된 횟수를 초과하는 경우 이 작업들은 `failed_jobs` 테이블에 추가됩니다. 테이블의 이름은 `config/queue.php` 환경설정 파일에서 설정하실 수 있습니다.

`queue:failed-table` 명령을 사용하면 `failed_jobs` 테이블에 대한 마이그레이션을 만들 수 있습니다.

    php artisan queue:failed-table

[queue listener](#running-the-queue-listener)가 실행될 때 작업이 재시도 되어야 할 최대 횟수를 `queue:listen` 명령어의 `--tries` 스위치를 사용하여 지정할 수 있습니다. 

    php artisan queue:listen connection-name --tries=3

<a name="failed-job-events"></a>
### 실패한 작업에 대한 이벤트

큐 작업이 실패한 경우에 호출 될 이벤트를 등록하고자 한다면, `Queue::failing` 메소드를 사용하면 됩니다. 이 이벤트는 여러분의 팀에게 이메일 또는 [HipChat](https://www.hipchat.com)과 같이 알림을 보낼 수 있습니다. 예를 들어 라라벨에 포함되어 있는 `AppServiceProvider` 에 이 이벤트 콜백을 추가해 보겠습니다. 

    <?php

    namespace App\Providers;

    use Queue;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Queue::failing(function ($connection, $job, $data) {
                // Notify team of failing job...
            });
        }

        /**
         * Register the service provider.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

#### 작업 클래스에서 failed 메소드 정의하기

보다 정교한 제어를 위해서, 작업이 실패한 경우에 지정된 작업이 수행되도록, `failed` 메소드를 직접 큐 작업 클래스에 정의 할 수 있습니다. 

    <?php

    namespace App\Jobs;

    use App\Jobs\Job;
    use Illuminate\Contracts\Mail\Mailer;
    use Illuminate\Queue\SerializesModels;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Contracts\Bus\SelfHandling;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class SendReminderEmail extends Job implements SelfHandling, ShouldQueue
    {
        use InteractsWithQueue, SerializesModels;

        /**
         * Execute the job.
         *
         * @param  Mailer  $mailer
         * @return void
         */
        public function handle(Mailer $mailer)
        {
            //
        }

        /**
         * Handle a job failure.
         *
         * @return void
         */
        public function failed()
        {
            // Called when the job is failing...
        }
    }

<a name="retrying-failed-jobs"></a>
### 실패한 작업 다시 시도하기

`failed_jobs` 데이터베이스 테이블에 추가된 실패한 모든 작업들을 보기 위해서 `queue:failed` 아티즌 명령을 사용할 수 있습니다.

    php artisan queue:failed

`queue:failed` 명령은 작업의 ID, 커넥션, 큐, 그리고 실패 시간을 목록으로 보여줍니다. 작업 ID는 실패한 작업을 다시 시도하기 위해 사용될 수 있습니다. 예를들어 5라는 ID를 가진 실패한 작업을 재시작하기 위해서는 아래의 명령을 실행해야 합니다.

    php artisan queue:retry 5

실패한 모든 작업들을 다시 시도하게 하려면 `queue:retry` 와 함께 ID 대신 `all`을 사용할 수 있습니다:

    php artisan queue:retry all

만약 실패한 작업을 삭제하기 위해서는 `queue:forget` 명령을 사용할 수 있습니다:

    php artisan queue:forget 5

실패한 모든 작업들을 삭제하기 위해서는 `queue:flush` 명령을 사용할 수 있습니다:

    php artisan queue:flush
