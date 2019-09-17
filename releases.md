# Release Notes

- [Versioning Scheme](#versioning-scheme)
- [버전 관리 체계](#versioning-scheme)
- [Support Policy](#support-policy)
- [지원 정책](#support-policy)
- [Laravel 6.0](#laravel-6.0)
- [라라벨 6.0](#laravel-6.0)

<a name="versioning-scheme"></a>
## Versioning Scheme
## 버전 관리 체계

Laravel and its other first-party packages follow [Semantic Versioning](https://semver.org). Major framework releases are released every six months (February and August), while minor and patch releases may be released as often as every week. Minor and patch releases should **never** contain breaking changes.

라라벨과 다른 자체 제작 패키지들은 [유의적 버젼](https://semver.org/lang/ko/)을 따릅니다. 메이저 프레임워크 릴리즈는 6 개월마다 (2월, 8월) 릴리즈되며, 마이너 릴리스는 매주 여러번 릴리즈 될 수 있습니다. 마이너 릴리즈에는 이전 버전의 호환성을 깨뜨리는 변경 사항이 **없어야** 합니다.

When referencing the Laravel framework or its components from your application or package, you should always use a version constraint such as `^6.0`, since major releases of Laravel do include breaking changes. However, we strive to always ensure you may update to a new major release in one day or less.

애플리케이션에서 라라벨 프레임워크, 라라벨의 컴포넌트 또는 패키지를 참조할 때에, 라라벨의 메이저 릴리즈가 이전 버전과 호환성을 유지하지 못하는 변경사항을 포함하고 있기 때문에 항상 `^6.0` 와 같이 참조하도록 해야 합니다. 변경사항에 대해서는 하루 안에 새로운 릴리즈를 업데이트 할 수 있도록 노력하고 있습니다.

<a name="support-policy"></a>
## Support Policy
## 지원 정책

For LTS releases, such as Laravel 6.0, bug fixes are provided for 2 years and security fixes are provided for 3 years. These releases provide the longest window of support and maintenance. For general releases, bug fixes are provided for 6 months and security fixes are provided for 1 year. For all additional libraries, including Lumen, only the latest release receives bug fixes.

라라벨 6.0과 같은 LTS 릴리즈 동안에는, 2년간의 버그 픽스와 3년동안의 보안 패치가 지원됩니다. 이러한 릴리즈는 장기간에 걸친 지원과 유지보수를 제공합니다. 일반적인 릴리즈에서는 버그 픽스는 6개월, 보안 패치는 1년동안 제공됩니다. Lumen 을 포함한 모든 추가 라이브러리의 경우 최신 릴리스에서만 버그 수정을받습니다.

| Version | Release | Bug Fixes Until | Security Fixes Until |
| --- | --- | --- | --- |
| 5.5 (LTS) | August 30th, 2017 | August 30th, 2019 | August 30th, 2020 |
| 5.6 | February 7th, 2018 | August 7th, 2018 | February 7th, 2019 |
| 5.7 | September 4th, 2018 | March 4th, 2019 | September 4th, 2019 |
| 5.8 | February 26th, 2019 | August 26th, 2019 | February 26th, 2020 |
| 6.0 (LTS) | September 3rd, 2019 | September 3rd, 2021 | September 3rd, 2022 |

| 버전 | 릴리즈 | 버그픽스 지원기간| 보안 패치 지원기간 |
| --- | --- | --- | --- |
| 5.5 (LTS) | 2017년 8월 30일 | 2019년 8월 30일  | 2020년 8월 30일 |
| 5.6 | 2018년 2월 7일 | 2018년 8월 7일 | 2019년 2월 7일 |
| 5.7 | 2018년 9월 4일 | 2019년 3월 4일 | 2019년 9월 4일 |
| 5.8 | 2019년 2월 26일 | 2019년 8월 26일 | 2020년 2월 26일 |
| 6.0 (LTS) | 2019년 9월 3일 | 2021년 9월 3일 | 2022년 9월 3일 |


<a name="laravel-6.0"></a>
## Laravel 6.0
## 라라벨 6.0

Laravel 6.0 (LTS) continues the improvements made in Laravel 5.8 by introducing semantic versioning, compatibility with [Laravel Vapor](https://vapor.laravel.com), improved authorization responses, job middleware, lazy collections, sub-query improvements, the extraction of frontend scaffolding to the `laravel/ui` Composer package, and a variety of other bug fixes and usability improvements.

라라벨 6.0 (LTS) 은 [Laravel Vapor](https://vapor.laravel.com)과의 호환성, 권한 응답, job 미들웨어, lazy 콜렉션, 서브쿼리 개선, `laravel/ui` 패키지를 통한 프론트엔드 스캐폴딩 및 다양한 버그 수정 및 유용성 개선 등을 통해 라라벨 5.8 의 만들어진 지속적으로 개선해 나갈 것 입니다.

### Semantic Versioning
### 유의적 버젼

The Laravel framework (`laravel/framework`) package now follows the [semantic versioning](https://semver.org/) standard. This makes the framework consistent with the other first-party Laravel packages which already followed this versioning standard. The Laravel release cycle will remain unchanged.

라라벨 프레임워크 (`laravel/framework`) 패키지는 [유의적 버젼](https://semver.org/lang/ko/) 표준을 따릅니다. 이것은 이 버젼 표준을 따른 다른 자체 제작 패키지들와 함께 프레임워크 일관성을 만듭니다. 라라벨 릴리즈 사이클은 변경되지 않을 것입니다.

### Laravel Vapor Compatibility
### 라라벨 Vapor 호환성

_Laravel Vapor was built by [Taylor Otwell](https://github.com/taylorotwell)_.
_라라벨 Vapor 는 [Taylor Otwell](https://github.com/taylorotwell)에 의해 만들어졌습니다_.

Laravel 6.0 provides compatibility with [Laravel Vapor](https://vapor.laravel.com), an auto-scaling serverless deployment platform for Laravel. Vapor abstracts the complexity of managing Laravel applications on AWS Lambda, as well as interfacing those applications with SQS queues, databases, Redis clusters, networks, CloudFront CDN, and more.

라라벨 6.0 은 라라벨를 위한 '오토스케일링 서버리스 개발 플랫폼'인 [라라벨 Vapor](https://vapor.laravel.com)과 호환성을 제공합니다. Vapor 는 SQS 큐들, 데이터베이스들, Redis 클러스터들, 네트워크들, CloudFront CDN 등과 이외의 것들을 인터페이스화해서 AWS Lambda 위에서의 라라벨 어플리케이션 관리의 복잡함을 추상화하였습니다.

### Improved Exceptions Via Ignition
### Ignition 을 통한 개선된 예외상황들

Laravel 6.0 ships with [Ignition](https://github.com/facade/ignition), a new open source exception detail page created by Freek Van der Herten and Marcel Pociot. Ignition offers many benefits over previous releases, such as improved Blade error file and line number handling, runnable solutions for common problems, code editing, exception sharing, and an improved UX.

라라벨 6.0은 Freek Van der Herten 과 Marcel Pociot 가 만든 새로운 오픈소스 예외상황 상세 페이지인 [Ignition](https://github.com/facade/ignition)을 장착하였습니다. Ignition 은 이전 버전에 비해 개선된, 블레이드 에러 파일과 줄번호 관리, 일반적 문제에 대한 실행 가능한 해결책, 코드 수정, 예외상황 공유, 개선된 UX 의 이점을 제공합니다.

### Improved Authorization Responses
### 개선된 권한 응답

_Improved authorization responses were implemented by [Gary Green](https://github.com/garygreen)_.
_개선된 권한 응답은 [Gary Green](https://github.com/garygreen)에 의해 구현되었습니다_.

In previous releases of Laravel, it was difficult to retrieve and expose custom authorization messages to end users. This made it difficult to explain to end-users exactly why a particular request was denied. In Laravel 6.0, this is now much easier using authorization response messages and the new `Gate::inspect` method. For example, given the following policy method:

라라벨의 이전 릴리즈에서는 엔드유저에게 커스텀 권한 메시지 노출하거나 검색하는 것에 어려움이 있었습니다. 이것은 엔드유저에게 특정 리퀘스트가 어떠한 이유로 거부되었는지 설명하기 어렵습니다. 라라벨 6.0 에서는 권한 응답 메세지와 `Gate::inspect` 메소드를 이용해서 쉽게 가능하게 합니다. 예와 같이 다음의 정책 메소드를 봅시다.

    /**
     * Determine if the user can view the given flight.
     *
     * @param  \App\User  $user
     * @param  \App\Flight  $flight
     * @return mixed
     */
    public function view(User $user, Flight $flight)
    {
        return $this->deny('Explanation of denial.');
    }

The authorization policy's response and message may be easily retrieved using the `Gate::inspect` method:

승인 정책 응답과 메세지는 `Gate::inspcet` 메소드를 이용해서 쉽게 검색됩니다.

    $response = Gate::inspect('view', $flight);

    if ($response->allowed()) {
        // User is authorized to view the flight...
    }

    if ($response->denied()) {
        echo $response->message();
    }

In addition, these custom messages will automatically be returned to your frontend when using helper methods such as `$this->authorize` or `Gate::authorize` from your routes or controllers.

더해서 당신의 라우트와 컨트롤러로부터 `$this->authorize`, `Gate:authorize` 같은 헬퍼 메소드을 이용할 때, 이러한 커스텀 메세지들은 당신의 프론트엔드에게 자동으로 반환될 것입니다.


### Job Middleware
### Job 미들웨어

_Job middleware were implemented by [Taylor Otwell](https://github.com/taylorotwell)_.
_Job 미들웨어는 [Taylor Otwell](https://github.com/taylorotwell)에 의해 구현되었습니다_.

Job middleware allow you wrap custom logic around the execution of queued jobs, reducing boilerplate in the jobs themselves. For example, in previous releases of Laravel, you may have wrapped the logic of a job's `handle` method within a rate-limited callback:

Job 미들웨어는 큐에 들어간 job 이 실행될때, 그 전후를 감싸는 커스텀 로직을 추가할 수 있게 도와줍니다. 예를 들자면, 당신은 라라벨 이전 버젼에서 실행속도 제한(rated-limited) 콜백에서 그 job 의 `handle` 메소드의 로직 전후를 감쌌어야 했었습니다:

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

In Laravel 6.0, this logic may be extracted into a job middleware, allowing you to keep your job's `handle` method free of any rate limiting responsibilities:

라라벨 6.0 에서는 이 로직이 하나의 job 미들웨어로 추출될거고, 그것은 당신의 job 의 `handle` 메소드를 어떠한 실행속도 제한(rated-limiting) 책임들 없이 유지할 수 있게 합니다.

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

After creating middleware, they may be attached to a job by returning them from the job's `middleware` method:

job 의 `middleware` 메소드로부터 만든 미들웨어를 반환받고, 그것들은 그 job 에 추가될 것입니다.  

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

### Lazy Collections
### Lazy 콜렉션

_Lazy collections were implemented by [Joseph Silber](https://github.com/JosephSilber)_.
_Lazy 콜렉션은 [Joseph Silber](https://github.com/JosephSilber)에 의해 구현되었습니다_.

Many developers already enjoy Laravel's powerful [Collection methods](https://laravel.com/docs/collections). To supplement the already powerful `Collection` class, Laravel 6.0 introduces a `LazyCollection`, which leverages PHP's [generators](https://www.php.net/manual/en/language.generators.overview.php) to allow you to work with very large datasets while keeping memory usage low.

많은 개발자들은 이미 라라벨의 강력한 [콜렉션 메소드들](/docs/collections) 잘 사용하고 있습니다. 이 강력한 `Collection` 클래스를 좀더 지원하기 위해 라라벨 6.0 에서는 PHP 의 [generators](https://www.php.net/manual/en/language.generators.overview.php)를 이용한, 적은 메모리 사용을 유지하먼서 아주 큰 데이타셋과 함께 일하기 맞춤인 Lazy 콜렉션을 도입했습니다. 

For example, imagine your application needs to process a multi-gigabyte log file while taking advantage of Laravel's collection methods to parse the logs. Instead of reading the entire file into memory at once, lazy collections may be used to keep only a small part of the file in memory at a given time:

예를 들자면, 당신의 애플리케이션이 로그를 파싱하기위해 라라벨의 콜렉션 메소드를 이용함으로써 얻는 이익도 있겠지만, 몇 기가바이트 단위의 로그 파일을 처리해야할 때를 생각한다면 비효율 적입니다. Lazy 콜렉션은 파일 전체를 메모리 안으로 한번에 불러오는 대신, 요청한 시간에 파일의 일부분만 메모리로 읽어 올 것입니다.:

    use App\LogEntry;
    use Illuminate\Support\LazyCollection;

    LazyCollection::make(function () {
        $handle = fopen('log.txt', 'r');

        while (($line = fgets($handle)) !== false) {
            yield $line;
        }
    })
    ->chunk(4)
    ->map(function ($lines) {
        return LogEntry::fromLines($lines);
    })
    ->each(function (LogEntry $logEntry) {
        // Process the log entry...
    });

Or, imagine you need to iterate through 10,000 Eloquent models. When using traditional Laravel collections, all 10,000 Eloquent models must be loaded into memory at the same time:

혹은 당신이 만개의 엘로퀜트 모델을 순회할 때를 가정해봅시다. 기존의 라라벨 콜렉션을 이용했다면 전체 만개의 엘로퀜트 모델을 매번 메모리에 읽어와야 할 것입니다.:

    $users = App\User::all()->filter(function ($user) {
        return $user->id > 500;
    });

However, beginning in Laravel 6.0, the query builder's `cursor` method has been updated to return a `LazyCollection` instance. This allows you to still only run a single query against the database but also only keep one Eloquent model loaded in memory at a time. In this example, the `filter` callback is not executed until we actually iterate over each user individually, allowing for a drastic reduction in memory usage:

그러나 라라벨 6.0 의 시작과 함게, 쿼리빌더의 `cursor` 메소드는 `LazyCollection` 인스턴스로 반환하도록 변경되었습니다. 데이터베이스에 하나의 쿼리에 실행해 한번에 메모리에 하나의 엘로퀜트 모델을 유지하도록 합니다. 예를 들자면 `filter` 콜백은 각 사용자 개별로 순회 반복되기전까지 실행되지 않아서 메모리 사용량이 크게 줄어줍니다.

    $users = App\User::cursor()->filter(function ($user) {
        return $user->id > 500;
    });

    foreach ($users as $user) {
        echo $user->id;
    }

### Eloquent Subquery Enhancements
### 엘로퀜트 서브쿼리 확장

_Eloquent subquery enhancements were implemented by [Jonathan Reinink](https://github.com/reinink)_.
_엘로퀜트 서브쿼리 확장은 [Jonathan Reinink](https://github.com/reinink)에 의해 구현되었습니다_.

Laravel 6.0 introduces several new enhancements and improvements to database subquery support. For example, let's imagine that we have a table of flight `destinations` and a table of `flights` to destinations. The `flights` table contains an `arrived_at` column which indicates when the flight arrived at the destination.

라라벨 6.0 은 데이터베이스 서브쿼리 지원에 대한 새로운 확장과 향상을 소개했습니다. 예를 들자면, 비행 `destinations`(목적지) 테이블과 목적지로의 `flights`(비행) 테이블이 있다고 상상해봅시다.
`flights` 테이블에는 목적지에 도착한 때를 가리키는 `arrived_at` 컬럼이 포함되어있습니다.

Using the new subquery select functionality in Laravel 6.0, we can select all of the `destinations` and the name of the flight that most recently arrived at that destination using a single query:

라라벨 6.0 의 새로운 서브쿼리 조회(select) 기능을 사용하면, 하나의 쿼리를 사용하여 비행이름과 목적지의 최근 도착시간을 한번에 조회할 수 있습니다. 

    return Destination::addSelect(['last_flight' => Flight::select('name')
        ->whereColumn('destination_id', 'destinations.id')
        ->orderBy('arrived_at', 'desc')
        ->limit(1)
    ])->get();

In addition, we can use new subquery features added to the query builder's `orderBy` function to sort all destinations based on when the last flight arrived at that destination. Again, this may be done while executing a single query against the database:

더해서, 해당 목적지에 도착한 마지막 비행 순서에 기초해서, 모든 도착지를 정렬할수 있는 기능을 쿼리빌더의 `orderBy`에 추가하는 새로운 서브쿼리 기능을 사용할수도 있습니다.  

    return Destination::orderByDesc(
        Flight::select('arrived_at')
            ->whereColumn('destination_id', 'destinations.id')
            ->orderBy('arrived_at', 'desc')
            ->limit(1)
    )->get();

### Laravel UI
### 라라벨 UI

The frontend scaffolding typically provided with previous releases of Laravel has been extracted into a `laravel/ui` Composer package. This allows the first-party UI scaffolding to be developed and versioned separately from the primary framework. As a result of this change, no Bootstrap or Vue code is present in default framework scaffolding, and the `make:auth` command has been extracted from the framework as well.

라라벨 전 릴리즈에 제공되었던 전통적인 프론트엔드 스캐폴딩이 `laravel\ui` 컴포저 패키지로 추출되었습니다. 이 것은 주 프레임웍으로부터 개발 분리 될 자체 제작 UI 스캐폴딩이 될 것입니다. 이 같은 변화의 결과로 bootstrap, vue 코드가 기본 프레임웍 스캐폴딩에서 제공되지 않고, `make:auth` 명령어도 프레임웍에서 추출되었습니다.

In order to restore the traditional Vue / Bootstrap scaffolding present in previous releases of Laravel, you may install the `laravel/ui` package and use the `ui` Artisan command to install the frontend scaffolding:

이전 릴리즈의 라라벨에서 제공된 전통적인 vue / bootstrap 스캐폴딩을 복원하는 순서로, 아마 당신은 `laravel\ui` 패키지를 설치하고, `ui` 아티산 커멘드로 프론트엔드 스캐폴딩을 설치해야 할 것입니다.

    composer require laravel/ui

    php artisan ui vue --auth
