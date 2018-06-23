# 릴리즈 노트

- [버전 관리 체계](#versioning-scheme)
- [지원 정책](#support-policy)
- [라라벨 5.6](#laravel-5.6)

<a name="versioning-scheme"></a>
## 버전 관리 체계

라라벨의 버전 관리 체계는 다음의 컨벤션을 유지합니다: `paradigm.major.minor`. 메이저 프레임워크 릴리즈는 6 개월마다 (2월, 8월) 릴리즈되며, 마이너 릴리스는 매주 여러번 릴리즈 될 수 있습니다. 마이너 릴리즈에는 이전 버전의 호환성을 깨뜨리는 변경 사항이 **없어야** 합니다.

어플리케이션에서 라라벨 프레임워크, 라라벨의 컴포넌트 또는 패키지를 참조할 때에, 라라벨의 메이저 릴리즈가 이전 버전과 호환성을 유지하지 못하는 변경사항을 포함하고 있기 때문에 항상 `5.5.*` 와 같이 참조하도록 해야 합니다. 변경사항에 대해서는 하루 안에 새로운 릴리즈를 업데이트 할 수 있도록 노력하고 있습니다.

패러다임 변경 릴리즈는 몇년간 구분되어 있으며, 프레임워크의 아키텍처와 컨벤션에 대한 근본적인 변경을 나타냅니다. 현재까지는 개발중인 버전에 대한 변경 발표는 없습니다.

<a name="support-policy"></a>
## 지원 정책

라라벨 5.5과 같은 LTS 릴리즈 동안에는, 2년간의 버그 픽스와 3년동안의 보안 패치가 지원됩니다. 이러한 릴리즈는 장기간에 걸친 지원과 유지보수를 제공합니다. 일반적인 릴리즈에서는 버그 픽스는 6개월, 보안 패치는 1년동안 제공됩니다.

| 버전 | 릴리즈 | 버그픽스 지원기간| 보안 패치 지원기간|
| --- | --- | --- | --- |
| 5.0 | 2015 년 2 월 4 일 | 2015 년 8 월 4 일 | 2016 년 2 월 4 일 |
| 5.1 (LTS) | 2015 년 6 월 9 일 | 2017 년 6 월 9 일 | 2018 년 6 월 9 일 |
| 5.2 | 2015 년 12 월 21 일 | 2016 년 6 월 21 일 | 2016 년 12 월 21 일 |
| 5.3 | 2016 년 8 월 23 일 | 2017 년 2 월 23 일 | 2017 년 8 월 23 일 |
| 5.4 | 2017 년 1 월 24 일 | 2017 년 7 월 24 일 | 2018 년 1 월 24 일 |
| 5.5 (LTS) | 2017 년 8 월 30 일 | 2019 년 8 월 30 일  | 2020 년 8 월 30 일 |
| 5.6 | 2018 년 2 월 7 일 | 2018 년 8 월 7 일 | 2019 년 2 월 7 일 |

<a name="laravel-5.6"></a>
## 라라벨 5.6

라라벨 5.6은 로깅 시스템의 향상, 단일-서버 작업 스케줄링, 모델의 serialization 기능 향상, 동적 속도 제한 기능, 브로드캐스트 채널 클래스, API 리소스 컨트롤러 생성, Eloquent 날짜 포맷의 기능 향상, 블레이드 컴포넌트의 별칭 기능, Argon2 암호화 지원, Collision 패키지 포함등 라라벨 5.5에 비해서 기능이 향상되었습니다. 또한 모든 프론트엔드 스캐폴딩은 부트스트랩4 기반으로 업그레이드 되었습니다.

라라벨에서 사용하는 Symfony 컴포넌트가 Symfony `~4.0` 릴리즈 시리즈로 업그레이드 되었습니다.

라라벨 5.6 버전의 릴리즈는 [Spark 6.0](https://spark.laravel.com)(릴리즈 이후 첫번째 메이저 업그레이드인)과 동시에 진행되었습니다. Spark 6.0 은 Stripe 와 Braintree 에 대한 가격 정책, 다국어, 부트스트랩 4 와 더 향상된 UI 그리고 Stripe의 개별 요소를 더 잘 지원합니다.

> {tip} 이 문서는 프레임워크에서 주요한 개선사항들에 대한 요약입니다. 보다 자세한 변경사항은 [GitHub](https://github.com/laravel/framework/blob/5.6/CHANGELOG-5.6.md)에서 확인할 수 있습니다.

### 로깅 시스템의 향상

라라벨 5.6 에서 로깅 시스템에 많은 기능향상이 있었습니다. 모든 로깅 설정은 새로운 `config/logging.php` 파일로 지정됩니다. 로그 메세지를 보내는 여러개의 핸들러를 구성하고자 할때 "stacks" 을 사용하면 됩니다. 예를 들어, 모든 `debug` 레벨의 메세지를 시스템 로그로 보내면서, `error` 레벨 메세지를 슬랙으로 보낼 수 있습니다. 이렇게 하면 여러분의 팀에서 에러를 보다 빠르고 즉각적으로 대응할 수 있습니다:

    'channels' => [
        'stack' => [
            'driver' => 'stack',
            'channels' => ['syslog', 'slack'],
        ],
    ],

또한 기존의 로그 채널을 새로운 "tap" 기능을 사용하여 손쉽게 커스터마이징 할 수 있습니다. 보다 자세한 내용은 [로깅 문서](/docs/{{version}}/logging)를 참고하시기 바랍니다.

### 단일 서버에서의 작업 스케줄링

> {note} 이 기능을 사용하기 위해서는, 어플리케이션이 `memcached` 또는 `redis` 캐시 서버를 기본 캐시 서버로 사용하고 있어야 합니다. 또한, 모든 서버는 동일한 중앙 캐시 서버와 통신하고 있어야 합니다.

어플리케이션이 여러개의 서버에서 동작중이라면, 특정 job을 하나의 단일 서버에서만 실행할 필요가 있을 수 있습니다. 예를 들어 매주 금요일 저녁 새로운 보고서를 생성하는 작업스케줄을 가지고 있다고 가정해 보겠습니다. 만약 세개의 서버가 있고, 스케줄 작업이 서버 모두에서 동작한다면, job은 세번 실행되고, 보고서는 세번 생성되버립니다. 이런건 좋지 않죠!

작업이 하나의 서버에서만 실행되도록 하려면, 예약된 작업을 정의할 때 `onOneServer` 메소드를 사용하면 됩니다. 작업을 수행하는 첫번째 서버는 다른 서버가 동일한 크론 사이클 동안 지정된 작업을 동시에 실행하지 못하도록 lock을 통해서 보호하게 됩니다:

    $schedule->command('report:generate')
             ->fridays()
             ->at('17:00')
             ->onOneServer();

### 동적 Rate 제한

이전 버전의 라라벨에서 라우트 그룹에 [접속 속도 제한](/docs/{{version}}/routing#rate-limiting)을 지정할 때에는, 직접 최대 request-요청의 갯수를 지정했어야합니다:

    Route::middleware('auth:api', 'throttle:60,1')->group(function () {
        Route::get('/user', function () {
            //
        });
    });

라라벨 5.6에서는 인증된 `User` 모델의 속성을 사용하여, 동적으로 request-요청의 최대값을 지정할 수 있습니다. 예를 들어, `User` 모델이 `rate_limit` 속성을 가지고 있다면, 이 속성의 이름을 `throttle` 미들웨어에 전달해서, 이 값을 가지고 최대 request-요청 횟수를 제한하는데 사용하도록 지정할 수 있습니다:

    Route::middleware('auth:api', 'throttle:rate_limit,1')->group(function () {
        Route::get('/user', function () {
            //
        });
    });

### 브로드 캐스트 채널 클래스

어플리케이션에서 여러개의 다른 채널을 사용하고 있다면, `routes/channels.php` 파일이 매우 커져버릴 수 있습니다. 이때, 채널의 권한을 확인하기 위해서 클로저를 사용하는 대신, 채널 클래스를 사용할 수 있습니다. 채널 클래스를 생성하려면 `make:channel` 아티즌 명령어를 사용하면 됩니다. 이 명령어는 `App/Broadcasting` 디렉토리에 새로운 채널 클래스를 생성합니다.

    php artisan make:channel OrderChannel

다음으로, `routes/channels.php` 파일에 아래와 같이 새로운 채널 클래스를 등록합니다:

    use App\Broadcasting\OrderChannel;

    Broadcast::channel('order.{order}', OrderChannel::class);

그리고나서, 생성한 채널 클래스의 `join` 메소드안에 권한을 확인하는 로직을 구성하면 됩니다. 이 `join` 메서드는 일반적으로 채널 승인 클로저에 작성하던 로직과 동일한 로직이 작성됩니다. 물론, 채널 모델 바인딩을 활용할 수도 있습니다:

    <?php

    namespace App\Broadcasting;

    use App\User;
    use App\Order;

    class OrderChannel
    {
        /**
         * Create a new channel instance.
         *
         * @return void
         */
        public function __construct()
        {
            //
        }

        /**
         * Authenticate the user's access to the channel.
         *
         * @param  \App\User  $user
         * @param  \App\Order  $order
         * @return array|bool
         */
        public function join(User $user, Order $order)
        {
            return $user->id === $order->user_id;
        }
    }

### API Controller Generation

리소스 라우트를 선언할 때 API로 구성하길 원한다면, 일반적으로 HTML 템플릿을 구성하는 `create` 와 `edit` 를 제외하고 생성하게됩니다. 리소스 컨트롤러를 생성할 때, 이 메소드를 포함시키지 않길 원한다면, `make:controller` 커맨드 명령에 `--api` 옵션을 사용하시면 됩니다:

    php artisan make:controller API/PhotoController --api

### 모델 Serialization 기능 향상

이전 버전의 라라벨에서는 큐-queue에 모델이 전달될 때, 연결된 관계가 온전하게 복원되지는 않았습니다. 라라벨 5.6에서는 모델에서 로딩된 관계들까지 큐-queue로 전달되어 처리될 때 자동으로 다시 로딩됩니다.

### Eloquent 날짜 변환

여러분은 이제 Eloquent의 날짜 컬럼을 각각 다른 포맷으로 커스터마이징 할 수 있습니다. 이를 위해서는 $case 속성에 각각의 날짜 포맷을 지정해야 합니다. 그러고 나면, 배열이나 JSON으로 시리얼라이즈 될때 지정한 포맷으로 변환됩니다:

    protected $casts = [
        'birthday' => 'date:Y-m-d',
        'joined_at' => 'datetime:Y-m-d H:00',
    ];

### 블레이드 컴포넌트 별칭 지정

블레이드 컴포넌트를 하위 디렉토리에 저장했다면, 이제 이들에 별칭을 지정하여 엑세스 할 수 있습니다. 예를 들어, `resources/views/components/alert.blade.php`에 저장된 블레이드 컴포넌트를 생각해 보겠습니다. `component` 메소드를 사용하여 `components.alert`를 `alert`으로 별칭을 지정할 수 있습니다:

    Blade::component('components.alert', 'alert');

컴포넌트에 별칭을 부여하고 나면, 지시어를 사용하여 렌더링 할 수 있습니다:

    @alert('alert', ['type' => 'danger'])
        You are not allowed to access this resource!
    @endalert

추가 슬롯이 없다면 컴포넌트의 파라미터를 생략할 수 있습니다:

    @alert
        You are not allowed to access this resource!
    @endalert

### Argon2 패스워드 해싱 지원

어플리케이션을 PHP 7.2.0 이상 환경에서 구성중이라면, 라라벨은 이제 Argon2 알고리즘을 사용한 패스워드 해싱을 지원합니다. 기존 해시 드라이버는 새로운 `config/hashing.php` 설정 파일에서 제어할 수 있습니다.

### UUID Methods

라라벨 5.6에서는 UUID를 생성하는 두개의 새로운 메소드를 제공합니다: `Str::uuid`와 `Str::orderedUuid`입니다. `orderedUuid` 메소드는 Mysql과 같은 인덱싱된 데이터베이스 컬럼에 효과적으로 저장될 수 있도록 "타임스탬프와 같은 정렬이 가능한" UUID를 생성합니다. (역자주 : 이 메소드의 결과로 생성되는 UUID의 첫번째 부분은 시간에 따라서 증감하는 형태를 보입니다). 각각의 이 메소드들은 `Ramsey\Uuid\Uuid` 객체를 반환합니다:

    use Illuminate\Support\Str;

    return (string) Str::uuid();

    return (string) Str::orderedUuid();

### Collision 패키지

`laravel/laravel` 어플리케이션에서는 이제 `dev` 컴포저 의존성으로 Nuno Maduro가 관리하는 [Collision](https://github.com/nunomaduro/collision) 패키지가 추가되었습니다. 이 패키지는 커맨드라인에서 라라벨 어플리케이션을 작동할 때 유용한 에러 리포팅 기능을 제공합니다:

<img src="https://raw.githubusercontent.com/nunomaduro/collision/stable/docs/example.png" width="600" height="388">

### 부트스트랩 4

인증 보일러플레이크 코드 와 뷰 컴포넌트를 구성하는 예제와 같이 모든 프론트엔드 스캐폴딩은 [부트스트랩 4](https://blog.getbootstrap.com/2018/01/18/bootstrap-4/)로 업그레이드 되었습니다. 이에 따라 기본적으로, 페이지네이션의 링크는 부트스트랩4 형식으로 생성됩니다.
