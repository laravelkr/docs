# 릴리즈 노트

- [버전 관리 체계](#versioning-scheme)
    - [예외사항](#exceptions)
- [지원 정책](#support-policy)
- [라라벨 8](#laravel-8)

<a name="versioning-scheme"></a>
## 버전 관리 체계

라라벨과 그외 자체적으로 제공하는 패키지들은 [시멘틱버저닝-유의적 버젼체계](https://semver.org/lang/ko/)을 따릅니다. 메이저 프레임워크 릴리즈는 1년마다 (~2월) 릴리즈되며, 마이너 릴리스는 매주 여러번 릴리즈 될 수 있습니다. 마이너 릴리즈에는 이전 버전의 호환성을 깨뜨리는 변경 사항이 **없어야** 합니다.

애플리케이션에서 라라벨 프레임워크, 라라벨의 컴포넌트 또는 패키지를 참조할 때, 메이저 릴리즈에서는 (ex 7.x -> 8.x) 이전 버전과 호환성이 깨지는 변경사항을 포함하고 있기 때문에 항상 `^8.0` 와 같이 참조하도록 해야 합니다. 변경사항에 대해서는 하루 안에 새로운 릴리즈를 업데이트 할 수 있도록 노력하고 있습니다.

<a name="exceptions"></a>
### 예외사항

<a name="named-arguments"></a>
#### 명명 된 인수-Named Arguments

현재 PHP의 [named arguments](https://www.php.net/manual/en/functions.arguments.php#functions.named-arguments) 기능은 라라벨의 하위 호환성 지침에 포함되지 않습니다. 라라벨 코드베이스를 개선하기 위해 필요한 경우 함수 매개 변수의 이름을 바꿀 수 있습니다. 따라서 라라벨 메서드를 호출 할 때 명명 된 인수를 사용하는 경우에는 매개 변수 이름이 향후 변경 될 수 있음을 이해하고 신중하게 수행해야합니다.

<a name="support-policy"></a>
## 지원 정책

모든 라라벨 릴리즈는 18개월의 버그 픽스와 2년동안의 보안 패치가 지원됩니다. Lumen 을 포함한 모든 추가 라이브러리의 경우 최신 릴리스에서만 버그 수정을 받습니다. 또한 [라라벨에서 지원되는 데이터베이스](/docs/{{version}}/database#introduction) 버전들도 참고하세요.

| 버전 | PHP (*)            | 릴리즈          | 버그픽스 지원기간   | 보안 패치 지원기간  |
| --- |--------------------|--------------|-------------|-------------|
| 6 (LTS) | 7.2 - 8.0          | 2019년 9월 3일  | 2022년 1월 25일 | 2022년 9월 6일 |
| 7 | 7.2 - 8.0          | 2020년 3월 3일  | 2020년 10월 6일 | 2021년 3월 3일 |
| 8 | 7.3 - 8.1          | 2020년 9월 8일  | 2022년 7월 26일 | 2023년 1월 24일 |
| 9 | 8.0 - 8.1          | 2022년 2월 8일  | 2023년 8월 8일 | 2024년 2월 8일 |
| 10 | 8.0 - 8.1 | 2023년 2월 7일 | 2024년 8월 7일 | 2025년 2월 7일 |

(*) Supported PHP versions

<a name="laravel-8"></a>
## 라라벨 8

라라벨 8은 라라벨 Jetstream, 모델 팩토리 클래스, 마이그레이션 스쿼싱, 배치 잡, 개선된 레이트 리미팅, 큐 개선사항들, 동적인 블레이드 컴포넌트, Tailwind 페이지네이션 뷰들, 시간 테스트 헬퍼들, `artisan serve` 의 개선사항, 이벤트 리스너의 개선사항 등 다양한 버그 수정과 사용성 개선을 통해 라라벨 7.x 를 개선합니다.

<a name="laravel-jetstream"></a>
### 라라벨 Jetstream

_라라벨 Jetstream은 [Taylor Otwell](https://github.com/taylorotwell)에 의해 작성되었습니다_.

[라라벨 Jetstream](https://jetstream.laravel.com)은 아름답게 디자인된 라라벨 어플리케이션 스캐폴딩입니다. Jetstream은 로그인, 가입, 이메일 인증, 2차 인증, 세션 관리, 라라벨 Sanctum을 통한 API 지원, 그리고 선택적인 팀 관리 기능을 통해 당신의 새로운 프로젝트를 위한 완벽한 시작지점을 제공합니다.

Jetsream은 [Tailwind CSS](https://tailwindcss.com)를 이용해서 디자인 되었으며 여러분은 [Livewire](https://laravel-livewire.com) 혹은 [Inertia](https://inertiajs.com) 기반의 스캐폴딩을 선택할 수 있습니다.

<a name="models-directory"></a>
### Models 디렉토리

커뮤니티의 요구에 라라벨의 기본 어플리케이션 뼈대에 `app/Models` 디렉토리가 추가되었습니다. 우리는 여러분이 엘로퀀트 모델을 위한 이 새로운 디렉토리를 좋아하길 바랍니다! 관련된 모든 생성 커맨드들은 이 디렉토리가 존재하면 이 디렉토리에 모델들이 있을것으로 가정하도록 업데이트 되었습니다. 만약 이 디렉토리가 없다면, 라라벨은 여러분의 모델이 `app` 디렉토리에 있을것이라고 가정합니다.

<a name="model-factory-classes"></a>
### 모델 팩토리 클래스들

_모델 팩토리 클래스는 [Taylor Otwell](https://github.com/taylorotwell)에 의해 기여되었습니다_.

엘로퀀트 [모델 팩토리](/docs/{{version}}/database-testing#defining-model-factories)는 클래스 기반의 팩토리로 일급 관계를 지원하도록 완전히 재작성 되었습니다. 예를 들면, 라라벨에 포함된 `UserFactory` 는 아래와 같이 작성되었습니다.

    <?php

    namespace Database\Factories;

    use App\Models\User;
    use Illuminate\Database\Eloquent\Factories\Factory;
    use Illuminate\Support\Str;

    class UserFactory extends Factory
    {
        /**
         * The name of the factory's corresponding model.
         *
         * @var string
         */
        protected $model = User::class;

        /**
         * Define the model's default state.
         *
         * @return array
         */
        public function definition()
        {
            return [
                'name' => $this->faker->name(),
                'email' => $this->faker->unique()->safeEmail(),
                'email_verified_at' => now(),
                'password' => '$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi', // password
                'remember_token' => Str::random(10),
            ];
        }
    }

`HasFactory` 라는 새로운 트레이트에 의해, 생성된 모델은 아래와 같이 사용될수 있습니다. 

    use App\Models\User;

    User::factory()->count(50)->create();

모델 팩토리들은 이제 간단한 PHP 클래스들이기 때문에, 상태 변경이 클래스 메소드로 작성될 수 있습니다. 또한, 여러분은 이제 엘로퀀트 모델 팩토리에 이제 다른 헬퍼클래스들을 필요한만큼 추가할 수 있습니다. 

예를 들어, `User` 모델이 기본 속성중 하나를 변경하는 `suspended` 상태를 가지고 있다고 가정해봅시다. 여러분은 베이스 팩토리의 `state` 메소드를 통해 상태 변경들을 정의할 수 있습니다. 여러분은 상태 메소들을 여러분이 원하는대로 네이밍 할 수 있습니다. 결국은 일반적인 PHP 메소드라는 이야기입니다.

    /**
     * Indicate that the user is suspended.
     *
     * @return \Illuminate\Database\Eloquent\Factories\Factory
     */
    public function suspended()
    {
        return $this->state([
            'account_status' => 'suspended',
        ]);
    }

상태 변경 메소드를 정의한 후, 아래처럼 사용할 수 있습니다.

    use App\Models\User;

    User::factory()->count(5)->suspended()->create();

언급된 것처럼, 라라벨 8의 모델 팩토리는 관계들에 대해 일급 객체 지원을 포함합니다. `User` 모델이 `posts` 관계 메소드를 가지고 있다고 가정해봅시다, 우리는 유저와 세 개의 포스트를 생성하기 위해 간단하게 아래의 코드를 실행하면 됩니다.

    $users = User::factory()
                ->hasPosts(3, [
                    'published' => false,
                ])
                ->create();

업그레이드를 좀 더 수월하게 만들기 위해, [laravel/legacy-factories](https://github.com/laravel/legacy-factories) 패키지가 라라벨 8.x 이전 버전 형태의 모델 팩토리를 지원하기 위해 제공됩니다.

라라벨의 재작성된 팩토리들은 여러분이 좋아할만한 다른 많은 기능들을 포함합니다. 모델 팩토리에 대해서 더 알아보시려면, [데이터베이스 테스팅](/docs/{{version}}/database-testing#defining-model-factories) 문서를 참고하세요.

<a name="migration-squashing"></a>
### 마이그레이션 스쿼싱

_마이그레이션 스쿼싱은 [Taylor Otwell](https://github.com/taylorotwell)에 의해 기여되었습니다_

어플리케이션 개발을 진행하면서, 여러분의 마이그레이션 파일들은 시간이 지날수록 계속 쌓여만 갈것입니다. 결국 마이그레이션 디렉토리는 수백개의 파일들로 꽉차버리게 됩니다. 여러분이 만약 MySQL이나 PostrgreSQL을 사용한다면, 여러분은 이제 마이그레이션 파일들을 "스쿼시" 하여 한개의 SQL 파일로 만들 수 있습니다. 시작하려면, `schema:dump` 커맨드를 실행하세요:

    php artisan schema:dump

    // Dump the current database schema and prune all existing migrations...
    php artisan schema:dump --prune

여러분이 이 커맨드를 실행하면 라라벨은 "schema" 파일을 여러분의 `database/schema` 디렉토리에 생성합니다. 이제, 여러분이 데이터베이스 마이그레이션을 시도할 때 아직 아무 마이그레이션도 실행되지 않은 초기상태 인 경우, 라라벨은 스키마 파일의 SQL을 먼저 실행할 것입니다. 스키마 파일의 명령어가 먼저 실행된 이후, 라라벨은 스키마 덤프에 포함되지 않은 마이그레이션의 나머지 부분들을 실행합니다.

<a name="job-batching"></a>
### 배치 잡

_배치 잡은 [Taylor Otwell](https://github.com/taylorotwell) & [Mohamed Said](https://github.com/themsaid)에 의해 기여되었습니다_.

라라벨의 배치잡 기능은 여러분이 쉽게 배치 잡을 실행하고 여러분의 배치잡이 완료되었을때 어떤 액션을 실행할수 있게 해줍니다.

새로운 `Bus` 파사드의 `batch` 메소드는 배치잡을 시작시키기 위해 사용될 수 있습니다. 물론, 배칭은 완료 콜백과 함께 실행 될때 더 유용합니다. 이를 위해서 여러분은 `then`, `catch`, 그리고 `finally` 메소드를 사용해 배치 작업의 완료 콜백을 정의할 수 있습니다. 각각의 콜백은 실행될 때 `Illuminate\Bus\Batch` 의 인스턴스를 인자로 넘겨받습니다.

    use App\Jobs\ProcessPodcast;
    use App\Podcast;
    use Illuminate\Bus\Batch;
    use Illuminate\Support\Facades\Bus;
    use Throwable;

    $batch = Bus::batch([
        new ProcessPodcast(Podcast::find(1)),
        new ProcessPodcast(Podcast::find(2)),
        new ProcessPodcast(Podcast::find(3)),
        new ProcessPodcast(Podcast::find(4)),
        new ProcessPodcast(Podcast::find(5)),
    ])->then(function (Batch $batch) {
        // All jobs completed successfully...
    })->catch(function (Batch $batch, Throwable $e) {
        // First batch job failure detected...
    })->finally(function (Batch $batch) {
        // The batch has finished executing...
    })->dispatch();

    return $batch->id;

배치잡에 대해 더 알아보고 싶다면, [큐 문서](/docs/{{version}}/queues#job-batching)를 참고하세요.

<a name="improved-rate-limiting"></a>
### 개선된 레이트 리미팅

_레이트 리미팅 개선은 [Taylor Otwell](https://github.com/taylorotwell)에 의해 기여되었습니다_.

라라벨의 리퀘스트 레이트 리미터 기능은 이전 버전의 `throttle` 미들웨어 API와의 호환성을 유지하면서도 더욱 유연하고 강력하게 확장되었습니다. 

레이트 리미터들은 `RateLimiter` 파사드의 `for` 메소드를 통해 정의될 수 있습니다. `for` 메소드는 레이트 리미터의 이름과 이 레이트 리미터에 할당된 라우트들에 적용될 제한 관련 설정을 리턴하는 클로져를 전달받습니다. 

    use Illuminate\Cache\RateLimiting\Limit;
    use Illuminate\Support\Facades\RateLimiter;

    RateLimiter::for('global', function (Request $request) {
        return Limit::perMinute(1000);
    });

레이트 리미터 콜백이 요청된 HTTP 리퀘스트 인스턴스를 전달받기 때문에, 여러분은 요청 혹은 요청하는 유저에 따라 유동적으로 적절한 레이트 리미트를 적용할 수 있을것입니다.

    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()->vipCustomer()
                    ? Limit::none()
                    : Limit::perMinute(100);
    });

때때로 여러분은 원하는 기준에 따라 레이트 리미트를 적용할 필요가 있습니다. 예를 들어 여러분이 단일 아이피에 대해 분당 100번의 요청만들 허용하고 싶다고 가정해봅시다. 이를 위해 여러분은 레이트 리미터 설정을 생성할 때 `by` 메소드를 사용할 수 있습니다.

    RateLimiter::for('uploads', function (Request $request) {
        return $request->user()->vipCustomer()
                    ? Limit::none()
                    : Limit::perMinute(100)->by($request->ip());
    });

레이트 리미터는 라우트 혹은 라우트 그룹에 `throttle` [미들웨어](/docs/{{version}}/middleware) 를 통해 연결됩니다. 쓰로틀 미들웨어는 여러분이 라우트에 적용하고자 하는 레이트 리미터의 이름을 전달받습니다.

    Route::middleware(['throttle:uploads'])->group(function () {
        Route::post('/audio', function () {
            //
        });

        Route::post('/video', function () {
            //
        });
    });

레이트 리미팅에 대해 더 알고 싶으시면, [라우팅 문서](/docs/{{version}}/routing#rate-limiting)를 참고하세요.

<a name="improved-maintenance-mode"></a>
### 개선된 점검 모드

_점검 모드 개선은 [Spatie](https://spatie.be)에게서 영감을 받아 [Taylor Otwell](https://github.com/taylorotwell)이 기여하였습니다_.

이전 버전의 라라벨에서는 `php artisan down` 점검 모드 기능이 "허용 목록"의 아이피 주소들에 한해 어플리케이션에 접근하는 것을 허용했습니다. 이 기능은 이제 제거되며 더 간단한 "secret" / 토큰 접근 방식으로 대체됩니다.

점검 모드 중에, 여러분은 이제 `secret` 옵션을 이용해 점검 모드를 우회하는 토큰을 설정할 수 있습니다.

    php artisan down --secret="1630542a-246b-4b66-afa1-dd72a4c43515"

어플리케이션이 점검 모드에 들어간 이후에, 토큰에 매칭되는 어플리케이션 URL에 접속하면 라라벨이 점검 모드 우회 쿠키를 여러분의 브라우저에 발급합니다.

    https://example.com/1630542a-246b-4b66-afa1-dd72a4c43515

이 숨겨진 라우트에 접근하면 여러분은 `/` 경로로 리다이렉트 될 것입니다. 쿠키가 한번 발급되면, 여러분은 어플리케이션에 점검 모드가 아닌것처럼 접근할 수 있을 것입니다. 

<a name="pre-rendering-the-maintenance-mode-view"></a>
#### 점검 모드 뷰 Pre-Rendering

만약 여러분이 배포 중 `php artisan down` 커맨드를 사용하려 한다면, 컴포저의 의존성 혹은 여러 인프라 구성요소가 업데이트 되는 중에 어플리케이션에 접근할 때 여전히 유저들 중 일부가 에러를 볼 가능성이 있습니다. 이것은 점검 모드 뷰를 템플릿 엔진을 사용해 렌더링 하기 위해 라라벨 프레임워크의 거대한 부분이 부팅되어야 하기 때문입니다.

위와 같은 이유로, 라라벨은 이제 점검 모드 뷰를 미리 렌더링(Pre-rendering) 하여 리퀘스트 사이클의 매우 앞부분에서 리턴할 수 있도록 지원합니다. 해당 뷰는 여러분의 어플리케이션의 의존성이 로딩되기 이전에 렌더링됩니다. 여러분은 `down` 커맨드의 `render` 옵션을 통해 미리 렌더링할 템플릿을 선택할 수 있습니다.

    php artisan down --render="errors::503"

<a name="closure-dispatch-chain-catch"></a>
### 클로져 Dispatch / 체인된 `catch`

_Catch 개선은 [Mohamed Said](https://github.com/themsaid)에 의해 기여되었습니다_.

새로운 `catch` 메소드를 사용하면, 큐잉된 클로져가 설정된 retry 수만큼 시도한 이후에도 실패했을 때에 실행할 클로져를 설정할 수 있습니다. 

    use Throwable;

    dispatch(function () use ($podcast) {
        $podcast->publish();
    })->catch(function (Throwable $e) {
        // This job has failed...
    });

<a name="dynamic-blade-components"></a>
### 동적 블레이드 컴포넌트

_동적 블레이드 컴포넌트는 [Taylor Otwell](https://github.com/taylorotwell)에 의해 기여되었습니다_.

때때로 런타임이 되기 전까지 어떤 컴포넌트를 렌더링 해야할지 모르는 상황에서 컴포넌트를 렌더 해야하는 경우가 있습니다. 이 상황에서, 여러분은 이제 라라벨에 내장된 `dynamic-component` 컴포넌트를 사용하여 런타임의 값 혹은 변수를 사용해 컴포넌트를 렌더할 수 있습니다.

    <x-dynamic-component :component="$componentName" class="mt-4" />

블레이드 컴포넌트에 대해 더 알고 싶다면, [블레이드 문서](/docs/{{version}}/blade#components)를 참고하세요.

<a name="event-listener-improvements"></a>
### 이벤트 리스너 개선

_이벤트 리스너 개선은 [Taylor Otwell](https://github.com/taylorotwell)에 의해 기여되었습니다_.

클로져 기반의 이벤트 리스너는 이제 `Event::listen` 메소드를 통해서만 등록될 수 있습니다. 라라벨은 이제 클로져를 검사하여 어떤 타입의 이벤트를 처리하는 리스너인지 판단합니다. 

    use App\Events\PodcastProcessed;
    use Illuminate\Support\Facades\Event;

    Event::listen(function (PodcastProcessed $event) {
        //
    });

또한, 클로져 기반의 이벤트 리스너는 `Illuminate\Events\queueable` 함수를 통해 큐로 처리가 가능한 리스너로 등록될 수 있습니다.

    use App\Events\PodcastProcessed;
    use function Illuminate\Events\queueable;
    use Illuminate\Support\Facades\Event;

    Event::listen(queueable(function (PodcastProcessed $event) {
        //
    }));

큐잉된 작업들처럼, 여러분은 `onConnection`, `onQueue`, 그리고 `delay` 메소드를 통해 큐잉된 리스너의 실행을 설정할 수 있습니다.

    Event::listen(queueable(function (PodcastProcessed $event) {
        //
    })->onConnection('redis')->onQueue('podcasts')->delay(now()->addSeconds(10)));

만약 익명의 큐잉된 리스너의 실패에 대해 처리하고 싶다면, `queueable` 리스너를 정의하면서 `catch` 메소드에 클로져를 제공하세요.

    use App\Events\PodcastProcessed;
    use function Illuminate\Events\queueable;
    use Illuminate\Support\Facades\Event;
    use Throwable;

    Event::listen(queueable(function (PodcastProcessed $event) {
        //
    })->catch(function (PodcastProcessed $event, Throwable $e) {
        // The queued listener failed...
    }));

<a name="time-testing-helpers"></a>
### 시간 테스팅 헬퍼

_시간 테스팅 헬퍼는 Ruby on Rails에서 영감을 받아 [Taylor Otwell](https://github.com/taylorotwell)이 기여하였습니다_.

테스트에서, 여러분은 `now` 혹은 `Illuminate\Support\Carbon::now()`와 같은 헬퍼에서 리턴되는 시간을 수정할 필요가 있을 수 있습니다. 라라벨의 기본 기능 테스트 클래스는 이제 여러분이 현재 시간을 조작할 수 있도록 하는 헬퍼 함수들을 포함합니다.

    public function testTimeCanBeManipulated()
    {
        // Travel into the future...
        $this->travel(5)->milliseconds();
        $this->travel(5)->seconds();
        $this->travel(5)->minutes();
        $this->travel(5)->hours();
        $this->travel(5)->days();
        $this->travel(5)->weeks();
        $this->travel(5)->years();

        // Travel into the past...
        $this->travel(-5)->hours();

        // Travel to an explicit time...
        $this->travelTo(now()->subHours(6));

        // Return back to the present time...
        $this->travelBack();
    }

<a name="artisan-serve-improvements"></a>
### Artisan `serve` 개선

_Artisan `serve` 개선은 [Taylor Otwell](https://github.com/taylorotwell)에 의해 기여되었습니다_.

Artisan `serve` 커맨드는 로컬의 `.env` 파일의 환경변수 변화를 감지해 자동으로 리로드 되도록 개선되었습니다. 과거에는 커맨드를 이용해 수동으로 정지하고 재시작해야 했습니다.

<a name="tailwind-pagination-views"></a>
### Tailwind 페이지네이션 뷰

라라벨 paginator는 이제 [Tailwind CSS](https://tailwindcss.com)를 기본적으로 사용하도록 업데이트 되었습니다. Tailwind CSS는 강력한 커스터마이징을 지원하며, 로우레벨의 CSS 프레임워크입니다. 강제적인 스타일이 없어 오버라이드하려고 애쓰지 않아도 되고 맞춤 디자인을 만드는데 사용할수 있는 컴포넌트들을 제공합니다. 당연히, 부트스트랩 3과 4의 뷰도 여전히 사용 가능합니다.

<a name="routing-namespace-updates"></a>
### 라우팅 네임스페이스 업데이트

이전 버전의 라라벨에서는, `RouteServiceProvider`가 `$namespace` 속성을 갖고 있었습니다. 이 속성의 값은 컨트롤러의 라우트를 정의 할때와 `action` 헬퍼 / `URL::action` 메서드를 호출할때 자동으로 접두 되었습니다. 라라벨 8.x 버전에서는, 이 속성은 기본적으로 `null` 입니다. 이것은 이제부터 라라벨이 자동으로 네임스페이스를 접두해주지 않는 다는 의미입니다. 그러므로, 라라벨 8.x 어플리케이션에서는 컨트롤러 라우트가 PHP 표준 callable 문법을 사용해 정의되어야합니다.

    use App\Http\Controllers\UserController;

    Route::get('/users', [UserController::class, 'index']);

`action` 관련된 메소드들을 호출할때도 동일한 callable 문법을 사용해야 합니다.

    action([UserController::class, 'index']);

    return Redirect::action([UserController::class, 'index']);

만약 여러분이 라라벨 7.x 스타일의 컨트롤러 라우트 prefix 기능을 사용하고 싶다면, 여러분의 어플리케이션의 `RouteServiceProvider` 에 `$namespace` 속성을 추가하세요.

> {note} 이 변경사항은 새로운 라라벨 8.x 어플리케이션들에만 적용됩니다. 라라벨 7.x로부터 업그레이드 된 어플리케이션들은 `RouteServiceProvider` 에 `$namespace` 속성을 갖고 있을 것이기 때문에 영향받지 않습니다.
