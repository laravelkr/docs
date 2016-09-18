# 커맨드 버스(Command Bus)

- [소개](#introduction)
- [커맨드 생성하기](#creating-commands)
- [커맨드 디스패치](#dispatching-commands)
- [큐 커맨드](#queued-commands)
- [커맨드 파이프라인](#command-pipeline)

<a name="introduction"></a>
## 소개

라라벨의 커맨드 버스는 어플리케이션이 수행할 필요가 있는 작업들을 캡슐화하는 편리한 방법을 제공합니다. 커맨드의 목적에 대한 이해를 돕기 위해서, 사용자가 팟캐스트를 구매할 수 있는 어플리케이션을 만들고 있다고 가정해봅시다.

사용자가 팟캐스트를 구매할 때 필요한 다양한 작업들이 있습니다. 예를 들어, 사용자의 신용 카드로 요금 청구를 하고, 데이터베이스에 구매를 나타내는  레코드를 추가하고, 구매 확인 메일을 보내야 할 것입니다. 어쩌면 우리는 사용자가 팟캐스트를 구매 할 수 있는지에 대해 어떠한 검증을 해야 할 수도 있습니다. 

이러한 로직들을 하나의 컨트롤러 메소드에 모두 넣을 수 있지만, 그럴 경우 몇 가지 단점이 있습니다. 첫째로, 컨트롤러는 아마도 여러 개의 각기 다른 HTTP 액션들을 처리하게 되는데, 각각의 컨트롤러 메소드가 복잡한 로직을 포함하고 있으면 컨트롤러를 비대하게 하고 읽기 어렵게 만들 것입니다. 두 번째로, 컨트롤러 외부에서 팟캐스트를 구매하는 로직을 재사용하는 것이 어렵습니다. 세 번째로, 팟캐스트 구매 로직을 테스트하기 위해 해당하는 HTTP 요청을 생성하고 응용 프로그램에 보내는 요청을 직접 수행해야 하기 때문에, 커맨드의 유닛 테스트가 훨씬 어려워질 것입니다.

컨트롤러에 이러한 로직을 넣는 것 대신, `PurchasePodcast` 커맨드와 같은 “명령어” 객체 안에 캡슐화하는 방법을 선택할 수 있습니다. 

<!--chak-comment-커맨드-버스(Command-Bus)-소개-->

<a name="creating-commands"></a>
## 커맨드 생성하기

아티즌 CLI에서는 `make:command` 명령어를 사용하여 새로운 커맨드 클래스를 생성할 수 있습니다:

	php artisan make:command PurchasePodcast

새롭게 생성되는 클래스는 `app/Commands` 디렉토리에 들어갑니다. 기본적으로 커맨드는 생성자와 `handle` 메소드의 두 가지 메소드를 가지고 있습니다. 물론 생성자에 `handle` 메소드에 의해서 실행되는 커맨드와 관련된 객체를 전달 할 수 있습니다. 예를 들어보겠습니다:

	class PurchasePodcast extends Command implements SelfHandling {

		protected $user, $podcast;

		/**
		 * Create a new command instance.
		 *
		 * @return void
		 */
		public function __construct(User $user, Podcast $podcast)
		{
			$this->user = $user;
			$this->podcast = $podcast;
		}

		/**
		 * Execute the command.
		 *
		 * @return void
		 */
		public function handle()
		{
			// Handle the logic to purchase the podcast...

			event(new PodcastWasPurchased($this->user, $this->podcast));
		}

	}

또한, `handle` 메소드에서도 타입힌트를 사용하여 [서비스 컨테이너](/docs/5.0/container)가 의존 객체를 자동으로 주입할 수 있도록 할 수 있습니다. 예를 들어:

		/**
		 * Execute the command.
		 *
		 * @return void
		 */
		public function handle(BillingGateway $billing)
		{
			// Handle the logic to purchase the podcast...
		}

<!--chak-comment-커맨드-버스(Command-Bus)-커맨드-생성하기-->

<a name="dispatching-commands"></a>
## 커맨드 디스패치

이제 커맨드를 생성하였으면 어떻게 처리할까요? 물론 직접 `handle` 메소드를 호출할 수도 있지만, 라라벨의 “커맨드 버스”를 통해서 처리하면 앞으로 이야기할 몇몇 장점들이 있습니다. 

만약 여러분이 어플리케이션의 베이스 컨트롤러를 살펴본다면 `DispatchesCommands` trait를 볼 수 있습니다. 이 trait는 어느 컨트롤러에서라도 `dispatch` 메소드를 호출할 수 있게 해줍니다. 다음 예를 살펴보겠습니다:

	public function purchasePodcast($podcastId)
	{
		$this->dispatch(
			new PurchasePodcast(Auth::user(), Podcast::findOrFail($podcastId))
		);
	}

커맨드 버스는 커맨드를 실행하고 `handle` 메소드에서 필요한 의존 객체들을 주입해주기 위해서 IoC 컨테이너를 호출합니다. 

원한다면 아무 클래스에 `Illuminate\Foundation\Bus\DispatchesCommands` trait를 추가할 수도 있습니다. 만약 어떤 클래스에서든 생성자에 커맨드 버스 인스턴스를 받고자 한다면 `Illuminate\Contracts\Bus\Dispatcher` 인터페이스를 타입힌트 하면 됩니다. 끝으로 빠르게 커맨드를 처리하기 위해서 `Bus` 파사드를 사용할 수도 있습니다: 

		Bus::dispatch(
			new PurchasePodcast(Auth::user(), Podcast::findOrFail($podcastId))
		);

### 요청으로부터 커맨드 프로퍼티 맵핑

명령어로 HTTP 요청 변수를 매핑하는 것은 매우 일반적입니다. 그래서 힘들게 여러분이 각각의 요청에 일일이 작업하게 하는 대신 라라벨에서는 몇 개의 헬퍼 메소드를 제공하여 처리할 수 있도록 해줍니다. `DispatchesCommands` trait에서 사용 가능한 `dispatchFrom` 메소드를 살펴보도록 합시다:

	$this->dispatchFrom('Command\Class\Name', $request);

이 메소드는 주어진 커맨드 클래스의 생성자를 검사하여 HTTP 요청(또는, 어떠한 다른 `ArrayAccess` 객체)으로부터 변수를 추출할 것입니다. 만약 커맨드 클래스의 생성자에서 `firstName` 변수가 있어야 한다면 커맨드 버스는 HTTP 요청으로부터 `firstName` 파라미터를 가져올 것입니다. 

`dispatchFrom` 메소드의 세 번째 인자로 배열을 전달할 수 있습니다. 이 배열은 HTTP 요청에서 얻을 수 없는 파라미터를 채우는 데 사용됩니다:

	$this->dispatchFrom('Command\Class\Name', $request, [
		'firstName' => 'Taylor',
	]);

<!--chak-comment-커맨드-버스(Command-Bus)-커맨드-디스패치-->

<a name="queued-commands"></a>
## 큐 커맨드

커맨드 버스는 현재의 요청 라이프사이클 동안 수행되는 동기 작업들뿐만 아니라 라라벨의 큐작업들을 생성하기 위한 주요한 방법도 제공합니다. 그러면, 어떻게 하면 커맨드 버스가 동기적 처리 대신에 백그라운드 처리를 위한 큐 작업을 실행하도록 할수 있을까요? 매우 쉽습니다. 먼저 새로운 커맨드를 생성할 때 아티즌 명령어에 `--queued` 옵션을 덧붙이면 됩니다:

	php artisan make:command PurchasePodcast --queued

여러분이 확인할 수 있듯이 이 옵션은 커맨드에 `Illuminate\Contracts\Queue\ShouldBeQueued` 인터페이스와 `SerializesModels` trait 기능을 추가합니다. 이 기능들은 커맨드 버스가 커맨드를 큐에 추가하도록 지시할 뿐만 아니라, 직렬화(serialize) 합니다. 그리고 커맨드의 프로퍼티로 저장된 Eloquent 모델들은 역직렬화(deserializa) 합니다. 

만약 이미 생성한 커맨드를 큐 커맨드로 변환하고 싶다면 `Illuminate\Contracts\Queue\ShouldBeQueued` 인터페이스를 구현하도록 클래스에 수동으로 추가해주면 됩니다. 인터페이스에 어떤 메소드는 없고, 단지 디스패처에게 알리기 위한 “표시용 인터페이스” 역할을 합니다. 

그다음에는, 정상적으로 커맨드를 작성하면 됩니다. 커맨드가 처리될 때 버스가 자동으로 해당 커맨드를 백그라운드 처리를 위해 큐에 추가합니다. 이보다 더 쉬울 순 없습니다.

큐 커맨드의 상호 작용에 대한 보다 자세한 내용은 [큐 문서](/docs/5.0/queues)를 참고하십시오. 

<!--chak-comment-커맨드-버스(Command-Bus)-큐-커맨드-->

<a name="command-pipeline"></a>
## 커맨드 파이프라인

커맨드가 핸들러에서 처리되기 전에 “파이프라인” 안에서 다른 클래스로 전달할 수 있습니다. 커맨드 파이프는 여러분의 커맨드를 제외하고, HTTP 미들웨어처럼 동작합니다. 예를 들어, 커맨드 파이프는 하나의 데이터베이스 트랜잭션 안에서 전체 커맨드 연산을 랩핑(wrap)할 수도 있고, 간단하게 실행 로그로 남길 수도 있습니다. 

버스에 파이프를 추가하려면 `App\Providers\BusServiceProvider::boot`메소드에서 디스패처의 `pipeThrough` 메소드를 호출하면 됩니다:

	$dispatcher->pipeThrough(['UseDatabaseTransactions', 'LogCommand']);

커맨드 파이프는 미들웨어와 마찬가지로 `handle` 메소드를 정의합니다:

	class UseDatabaseTransactions {

		public function handle($command, $next)
		{
			return DB::transaction(function() use ($command, $next)
			{
				return $next($command);
			});
		}

	}

커맨드 파이프 클래스는 [IoC 컨테이너](/docs/5.0/container)를 통해서 의존성이 해결되므로 생성자에서 타입힌트를 통한 의존성을 지정할 수 있습니다. 

커맨드 파이프에는 `Closure` 도 지정할 수 있습니다:

	$dispatcher->pipeThrough([function($command, $next)
	{
		return DB::transaction(function() use ($command, $next)
		{
			return $next($command);
		});
	}]);

<!--chak-comment-커맨드-버스(Command-Bus)-커맨드-파이프라인-->
