# 업그레이드 가이드

- [5.3에서 5.4.0 으로 업그레이드 하기](#upgrade-5.4.0)

<a name="upgrade-5.4.0"></a>
## 5.3에서 5.4.0으로 업그레이드 하기

#### 업그레이드 예상 시간 : 약 1-2시간

> {note} 가능한 모든 변경 내용을 기록하려고 했습니다. 그렇지만 변경 사항 중 일부는 프레임워크의 명확하지 않은 부분에서 이루어 지기 때문에 이중 일부가 실제 어플리케이션에 영향을 끼칠 수도 있습니다.

### 의존성 업데이트

`composer.json` 파일에 있는 `laravel/framework` 의존성을 `5.4.*` 로 변경합니다. 그리고 `phpunit/phpunit`을 `~5.7` 으로 업데이트 해야 합니다.

#### 컴파일된 서비스 파일 제거

`bootstrap/cache/compiled.php`파일이 존재한다면 이 파일을 제거해야 합니다. 프레임워크는 더이상 이 파일을 사용하지 않습니다.

#### 캐시 플러시

모든 패키지를 업그레이드 한 뒤에, `php artisan view:clear` 를 실행해서`Illuminate\View\Factory::getFirstLoop()` 제거와 관련된 블레이드 오류가 나지 않도록 하십시오. 또한 `php artisan route:clear`를 실행하여 라우트 캐시를 비워야 할 수 있습니다.

#### 라라벨 캐셔

라라벨 캐셔는 라라벨 5.4와 이미 잘 호환됩니다.

#### 라라벨 Passport

라라벨 5.4 와 [Axios](https://github.com/mzabriskie/axios) 자바스크립트 라이브러리와의 호환성을 위해서 라라벨 Passport `2.0.0`가 릴리즈 되었습니다. 라라벨 5.3에서 업그레이드를 하고, 사전에 준비된 Passport Vue 컴포넌트를 사용하는 경우에는 Axios 라이브러리가 어플리케이션의 어디에서라도 사용가능한지 확인해야 합니다.

#### 라라벨 Scout

라라벨 5.4와의 호환성을 위해서 라라벨 Scout `3.0.0` 이 릴리즈되었습니다.

#### 라라벨 Socialite

라라벨 5.4와의 호환성을 위해서 라라벨 Socialite `3.0.0` 이 릴리즈되었습니다.

#### 라라벨 Tinker

`laravel/tinker` 아티즌 명령어를 계속 사용하려면 `laravel/tinker` 패키지를 설치해야 합니다:

    composer require laravel/tinker

패키지를 설치하고 나서 `config/app.php` 설정 파일의 `providers` 배열에 `Laravel\Tinker\TinkerServiceProvider::class`를 추가 하십시오.

#### Guzzle

라라벨 5.4는 Guzzle 6.0 이상을 필요로합니다.

### 권한 승인-authorization

#### `getPolicyFor` 메소드

이전까지, `Gate::getPolicyFor($class)` 메소드가 호출 될 때 policy를 찾을 수 없으면 exception이 던져졌습니다. 이제는, 주어진 클래스에 대한 policy를 찾을 수 없으면 `null` 이 반환됩니다. 이 메소드를 직접 호출한다면 `null`을 체크하도록 리팩토링 하십시오:

```php
$policy = Gate::getPolicyFor($class);

if ($policy) {
    // code that was previously in the try block
} else {
    // code that was previously in the catch block
}
```

### 블레이드

#### `@section` Escaping

라라벨 5.4에서, 섹션에 전달된 인라인 컨텐츠는 자동으로 escape 됩니다:

    @section('title', $content)

섹션 안에서 컨텐츠를 escape 하지 않고 렌더링 하려면, 섹션을 "긴 폼" 스타일로 정의해야 합니다:

    @section('title')
        {!! $content !!}
    @stop

### Bootstrappers

HTTP 나 Console 커널에서 `$bootstrappers` 배열을 직접 오버라이딩 했다면, `DetectEnvironment` 엔트리를 `LoadEnvironmentVariables` 으로 이름을 변경해야 합니다.

### 브로드캐스팅

#### 채널 모델 바인딩

라라벨 5.3 에서 채널 이름 플레이스홀더를 정의할 때 `*` 문자가 사용되었습니다. 라라벨 5.4에서는 라우트 처럼 `{foo}` 스타일의 플레이스홀더를 정의해야 합니다:

    Broadcast::channel('App.User.{userId}', function ($user, $userId) {
        return (int) $user->id === (int) $userId;
    });

### 컬렉션

#### `every` 메소드

`every` 메소드의 동작은 `nth` 메소드로 변경되어 Lodash에 정의된 메소드의 이름과 일치하게 되었습니다.

#### `random` 메소드

`$collection->random(1)`을 호출하면 이제 하나의 아이템을 가지는 새로운 컬렉션 인스턴스를 반환합니다. 이전까지는 하나의 객체가 반환되었습니다. 이 메소드는 인자가 전달되지 않는 경우에만 하나의 객체를 반환합니다.

### 컨테이너

#### `bind` / `instance` 을 통한 별칭-aliasing

이전버전의 라라벨 릴리즈에서는, 별칭을 부여하기 위해서 `bind` 또는 `instance` 메소드에 첫번째 인자로 배열을 전달할 수 있었습니다:

    $container->bind(['foo' => FooContract::class], function () {
        return 'foo';
    });

그렇지만 이 동작은 라라벨 5.4에서는 제거되었기 때문에, 별칭을 등록하려면 이제 `alias` 메소드를 사용해야합니다:

    $container->alias(FooContract::class, 'foo');

#### 슬래시 문자를 사용한 클래스 바인딩

컨테이너 안에서 클래스를 바인딩 할 때 문자열의 맨 앞에 슬래시를 붙이는 형태는 더이상 지원하지 않습니다. 이 기능은 많은 양의 문자열을 작성해야만 했었습니다. 앞에 슬래시 문자를 붙이는 대신에 간단하게 바인딩 할 수 있습니다:

    $container->bind('Class\Name', function () {
        //
    });

    $container->bind(ClassName::class, function () {
        //
    });

#### `make` 메소드 파라미터

컨테이너의 `make` 메소드는 더이상 파라미터 배열을 두번째 인자로 받지 않습니다. 이 기능은 일반적으로 좋지 않은 코드를 나타냅니다. 일반적으로 더 직관적인 다른 방법으로 객체를 생성할 수 있습니다.

만약 꼭 배열로된 파라미터를 전달해야한다면, `makeWith` 메소드를 사용하십시오.

#### 콜백을 통한 의존성 해결

컨테이너의 `resolving` 과 `afterResolving` 메소드는 첫번째 인자로 클래스 이름 또는 바인딩된 키를 받아야 합니다:

    $container->resolving('Class\Name', function ($instance) {
        //
    });

    $container->afterResolving('Class\Name', function ($instance) {
        //
    });

#### `share` 메소드는 제거되었습니다.

`share` 메소드는 컨테이너에서 제거되었습니다. 이 메소드는 지난 몇년동안 문서에도 기록되지 않은 레거시 메소드였습니다. 이 메소드를 사용하는 경우 대신에 `singleton` 메소드를 사용해야합니다:

    $container->singleton('foo', function () {
        return 'foo';
    });

### 콘솔

#### `Illuminate\Console\AppNamespaceDetectorTrait` 트레이트

`Illuminate\Console\AppNamespaceDetectorTrait` 트레이트를 직접 참조하는 경우 `Illuminate\Console\DetectsApplicationNamespace`를 대신 참조하도록 수정하십시오.

### 데이터베이스

#### `orWhere` 에 배열 인자 전달하기

`orWhere` 메소드에 첫번째 인자를 배열로 전달할때 내부의 조건은 각각의 요소를 기준으로 `OR` 처리됩니다:

    $query->orWhere(['a' => 1, 'b' => 2])

    OR (a = 1 AND b = 2) // 이전버전까지...

    OR (a = 1 OR b = 2) // 신규버전...

#### 커스텀 커넥션

이전 버전에서 커스텀 데이터베이스 커넥션 인스턴스의 의존성을 해결하기 위해서, `db.connection.{driver-name}` 형태의 키를 서비스 컨테이너에 바인딩 했었다면, 이제 `AppServiceProvider` 파일의 `register` 메소드안에서 `Illuminate\Database\Connection::resolverFor` 메소드를 사용해야 합니다:

    use Illuminate\Database\Connection;

    Connection::resolverFor('driver-name', function ($connection, $database, $prefix, $config) {
        //
    });

#### Fetch 모드

라라벨은 더 이상 설정 파일에서 PDO의 "Fetch 모드"를 커스터마이징 할 수 있는 기능을 제공하지 않습니다. 대신에 `PDO::FETCH_OBJ`가 항상 사용됩니다. 계속 어플리케이션에서 Fetch 모드를 커스터마이징 하고자 하는 경우 `Illuminate\Database\Events\StatementPrepared` 이벤트를 리스닝 하면 됩니다:

    Event::listen(StatementPrepared::class, function ($event) {
        $event->statement->setFetchMode(...);
    });

### Eloquent

#### 데이트 캐스팅

`date` 캐스팅은 이제 컬럼을 `Carbon` 객체로 변환하고 객체에서 `startOfDay` 메소드를 호출합니다. 날짜의 시간 부분을 유지하려면 `dateTime` 캐스트를 사용해야 합니다.

#### 외래키 컨벤션

관계를 정의 할 때 외래 키가 명시적으로 지정되지 않은 경우에, Eloquent는 관련 모델의 테이블 이름과 기본 키 이름을 사용하여 외래 키를 추정합니다. 대부분의 어플리케이션에서 이는 별다른 변경사항이 아닙니다 예를 들면:

    public function user()
    {
        return $this->belongsTo(User::class);
    }

이전 버전의 라라벨과 마찬가지로 일반적으로 모델의 관계는 외래키로 `user_id` 를 사용했습니다. 만약 `User` 모델의 `$primaryKey` 속성값 또는 `getKeyName` 메소드를 오버라이딩 했다면 그 결과가 달라질 수 있습니다 예를 들면:

    public function getKeyName()
    {
        return 'key';
    }

이 경우 라라벨은 커스텀 정의사항을 우선하여 외래키 컬럼이름을 `user_id` 대신 `user_key` 로 결정합니다.

#### BelongsToMany `setJoin`

`setJoin` 메소드는 `performJoin`으로 이름이 변경되었습니다.

#### BelongsToMany `getRelatedIds`

`getRelatedIds` 메소드는 `allRelatedIds`으로 이름이 변경되었습니다.

#### 하나 또는 여러 개의 `createMany`

`hasOne` 또는 `hasMany` 관계의 `createMany` 메소드는 이제 배열 대신에 컬렉션 객체를 반환합니다.

#### 연관된 모델 커넥션

연관된 모델은 이제 상위 모델과 동일한 커넥션을 사용합니다. 예를 들어, 다음과 같은 쿼리를 실행한다면:

    User::on('example')->with('posts');

Eloquent는 기본 데이터베이스 커넥션 대신에 `example` 커넥션에 posts 테이블을 질의합니다. 기본 커넥션에서 `posts` 관계를 읽으려면 모델의 커넥션을 어플리케이션의 디폴터 커넥션으로 명시적으로 설정해야 합니다.

#### `chunk` 메소드

이제 쿼리 빌더의 `chunk` 메소드에서는 `each` 메소드와의 일관성을 제공하기 위해서 `orderBy` 구문을 필요로 합니다. `orderBy` 구문이 제공되지 않으면 exception이 발생합니다. 예를 들자면:

    DB::table('users')->orderBy('id')->chunk(100, function ($users) {
        foreach ($users as $user) {
            //
        }
    });

Eloquent 쿼리 빌더의 `chunk` 메소드는 특별히 `orderBy` 구문을 제공하지 않으면, 자동으로 primary key를 사용합니다.

#### `create` & `forceCreate` 메소드

`Model::create` 및 `Model::forceCreate` 메소드는 다수의 커넥션에서 모델을 생성하는 데 더 나은 지원을 제공하기 위해 `Illuminate\Database\Eloquent\Builder` 클래스로 옮겨졌습니다. 그렇지만, 만약 여러분의 고유한 모델에서 이 메소드를 확장하고자 한다면, 빌더의 `create` 메소드를 호출하도록 구현 메소드를 수정해야 합니다. 예들 들면:

    public static function create(array $attributes = [])
    {
        $model = static::query()->create($attributes);

        // ...

        return $model;
    }

#### `hydrate` 메소드

이 메소드에 커넥션 이름을 전달하고 있다면, 이제 `on` 메소드를 사용해야 합니다:

    User::on('connection')->hydrate($records);

#### `hydrateRaw` 메소드

`Model::hydrateRaw` 메소드는 이제 `fromQuery` 로 이름이 변경되었습니다. 이 메소드에 커스텀 커넥션 이름을 전달했었다면, 이제 `on` 메소드를 사용해야 합니다:

    User::on('connection')->fromQuery('...');

#### `whereKey` 메소드

`whereKey($id)`메소드는 이제 주어진 프라이머리 키 값에 "where" 절을 추가합니다 이전까지는 동적 "where" 절 빌더에서 "key" 컬럼에 "where" 절을 추가했었습니다. `whenKey` 메소드를 사용하여 `key` 컬럼에 대한 조건을 동적으로 추가한 경우 이제 `where('key', ...)`을 사용해야 합니다.

#### `factory` 헬퍼

`factory(User::class, 1)->make()` 또는 `factory(User::class, 1)->create()` 호출은 이제 하나의 아이템을 가지는 컬렉션을 반환합니다. 이전에는 단일 모델을 반환했었습니다. 이 메소드는 인자라 전달되지 않으면 단일 모델을 반환합니다.

### 이벤트

#### Contract 변경사항

어플리케이션이나 패키지에서 `Illuminate\Contracts\Events\Dispatcher` 인터페이스를 직접 구현했었다면, `fire` 메소드를 `dispatch` 메소드로 이름을 변경해야 합니다.

#### 이벤트 우선순위

이벤트 핸들러의 "우선 순위"에 대한 지원이 제거되었습니다. 이 문서화되지 않은 기능은 일반적으로 이벤트 기능의 남용을 나타냅니다. 대신에, synchronous 메소드 호출을 사용 고려하십시오. 또는, 관련되지 않은 핸들러 다음에 주어진 이벤트 핸들러가 실행되도록 이벤트 핸들러 내에서 새로운 이벤트를 전달 할 수도 있습니다.

#### 와일드카드 이벤트 핸들러 시그니처

와일드카드 이벤트 핸들러는 이제 첫번째 인자로 이벤트 이름을, 두번째 인자로 이벤트 데이터 배열을 전달 받습니다. `Event::fireing` 메소드는 제거되었습니다:

    Event::listen('*', function ($eventName, array $data) {
        //
    });

#### `kernel.handled` 이벤트

`kernel.handled` 이벤트는 이제 `Illuminate\Foundation\Http\Events\RequestHandled` 클래스를 사용하는 객체 기반의 이벤트입니다.

#### `locale.changed` 이벤트

`locale.changed` 이벤트는 이제 `Illuminate\Foundation\Events\LocaleUpdated` 클래스를 사용하는 객체 기반의 이벤트입니다.

#### `illuminate.log` 이벤트

`illuminate.log` 이벤트는 이제 `Illuminate\Log\Events\MessageLogged` 클래스를 사용하는 객체 기반 이벤트입니다.

### Exceptions

`Illuminate\Http\Exception\HttpResponseException`의 이름이 `Illuminate\Http\Exceptions\HttpResponseException`으로 변경되었습니다. 이제 `Exceptions` 는 복수형입니다. 마찬가지로 `Illuminate\Http\Exception\PostTooLargeException` 의 이름이 `Illuminate\Http\Exceptions\PostTooLargeException`으로 변경되었습니다.

### 메일

#### `Class@method` 문법

`Class@method` 문법을 사용하여 메일을 송신하는 기능은 더이상 지원되지 않습니다. 예를 들면:

    Mail::send('view.name', $data, 'Class@send');

이 방법으로 이메일을 송신했었다면 [mailables](/docs/{{version}}/mail)를 사용하도록 변경해야합니다.

#### 새로운 설정 옵션

라라벨 5.4의 새로운 마크다운 메일 컴포넌트 기능을 지원하기 위해서, 다음의 설정 부분을 `mail` 설정 파일의 가장 아래쪽에 추가해야 합니다:

    'markdown' => [
        'theme' => 'default',

        'paths' => [
            resource_path('views/vendor/mail'),
        ],
    ],


#### 클로저를 이용하여 메일을 큐에 지정하기

큐를 통해서 메일을 보내라면, 이제 [mailable](/docs/{{version}}/mail)을 사용해야만 합니다. `Mail::queue` 와 `Mail::later` 메소드는 더이상 클로저를 통해서 메일을 큐로 보내는 것을 지원하지 않습니다. 이 기능은 PHP가 기본적으로 지원하지 않는 클로저의 serialize를 가능하게 하기 위해서 별도의 라이브러리를 사용해야만 했기 때문입니다.

### 큐-Queue

#### 실패한 Job 저장 테이블

어플리케이션에서 `failed_jobs` 테이블을 구성해놓고 있었다면, 테이블에 `exception` 컬럼을 추가해야 합니다:

    $table->longText('exception')->after('payload');

### Redis

#### 클러스터링 지원기능 향상

라라벨 5.4에서는 Redis 클러스터링을 지원하는 기능향상이 추가되었습니다. Redis 클러스터를 사용하고자 하는 경우 `config/database.php` 설정 파일의 Redis 부분에 `clusters` 설정 옵션을 구성해야 합니다:

    'redis' => [

        'client' => 'predis',

        'options' => [
            'cluster' => 'redis',
        ],

        'clusters' => [
            'default' => [
                [
                    'host' => env('REDIS_HOST', '127.0.0.1'),
                    'password' => env('REDIS_PASSWORD', null),
                    'port' => env('REDIS_PORT', 6379),
                    'database' => 0,
                ],
            ],
        ],

    ],

### 라우팅

#### Post 사이즈 미들웨어

`Illuminate\Foundation\Http\Middleware\VerifyPostSize` 클래스는 `Illuminate\Foundation\Http\Middleware\ValidatePostSize`으로 이름이 변경되었습니다.

#### `middleware` 메소드

`Illuminate\Routing\Router` 클래스의 `middleware` 메소드는 `aliasMiddleware()` 으로 이름이 변경되었습니다. 대부분의 어플리케이션에서는 이 메소드를 직접 호출하지 않습니다. 일반적으로 HTTP 커널에서 `$routeMiddleware` 배열에 정의된 라우트 레벨의 미들웨어를 등록하기 위해서 이 메소드를 호출합니다.

#### `Route` 메소드

`Illuminate\Routing\Route` 클래스의 `getUri` 메소드는 제거되었습니다. 대신에 `uri` 메소드를 사용해야 합니다.

`Illuminate\Routing\Route` 클래스의 `getMethods` 메소드는 제거되었습니다. 대신에 `methods` 메소드를 사용해야 합니다.

`Illuminate\Routing\Route` 클래스의 `getParameter` 메소드는 제거되었습니다. 대신에 `parameter` 메소드를 사용해야합니다.

### 세션

#### Symfony 호환성

라라벨의 세션 핸들러가 더이상 Symfony의 `SessionInterface`를 구현하지 않습니다. 이 인터페이스를 구현하는 것은 프레임워크에서 필요로 하지 않는 기능들까지 작성하게 만들었습니다. 대신에 새로운 `Illuminate\Contracts\Session\Session` 인터페이스를 사용할 수 있습니다. 다음의 코드 변경 사항도 적용해야합니다:

모든 `->set()` 메소드 호출은 `->put()` 으로 변경해야 합니다. 일반적으로 라라벨 어플리케이션은 매뉴얼에 없는 메소드는 호출하지 않지만, 이는 예외적으로 주의해야될 부분입니다.

모든 `->getToken()` 메소드 호출은 `->token()` 으로 변경해야합니다.

모든 `$request->setSession()` 메소드 호출은 `setLaravelSession()` 으로 변경해야합니다.

### 테스팅

라라벨 5.4의 테스팅 레이어는 새롭게 작성되어 별다른 설정없이도 보다 간단하고 더 가벼워졌습니다. 라라벨 5.3의 테스팅 레이어를 계속 사용하고자 한다면 어플리케이션에 `laravel/browser-kit-testing` [package](https://github.com/laravel/browser-kit-testing)를 설치해야 합니다. 이 패키지는 라라벨 5.3 테스팅과 관련된 전체적인 호환성을 지원합니다. 사실, 라라벨 5.3의 테스트 레이어를 사용하면서 라라벨 5.4의 테스트 레이어를 같이 실행할 수 있습니다.

라라벨이 라라벨 5.4 테스트 제너레이터를 통해서 생성한 새로운 테스트를 오토로딩할 수 있도록 `Tests` 네임스페이스를 `composer.json` 파일의 `autoload-dev` 부분에 추가해야 합니다:

    "psr-4": {
        "Tests\\": "tests/"
    }

#### 하나의 어플리케이션에서 라라벨 5.3 & 5.4 테스트 실행하기

먼저, `laravel/browser-kit-testing` 패키지를 설치하십시오:

    composer require --dev laravel/browser-kit-testing "1.*"

그런뒤에, `tests/TestCase.php` 파일을 `tests` 디렉토리에 `BrowserKitTest.php`라는 이름으로 저장하십시오. 이제 파일을 수정하여 `Laravel\BrowserKitTesting\TestCase` 클래스를 상속하도록 합니다. 이 작업을 마쳤다면, `test` 디렉토리에 `TestCase.php` 와 `BrowserKitTest.php`의 두가지 베이스 테스트 클래스를 가지게 됩니다. `BrowserKitTest` 클래스르의 로딩을 위해서 `compose.json` 파일에 다음과 같이 추가하십시오:

패키지를 설치하고 나서는, `tests/TestCase.php` 파일을 복사하여 `tests` 디렉토리에 `BrowserKitTestCase.php` 파일로 저장하십시오. 그런 뒤에, 파일이 `Laravel\BrowserKitTesting\TestCase` 클래스를 상속하도록 수정하십시오. 이 작업을 마치면 `tests` 디렉토리에는 `TestCase.php`와 `BrowserKitTestCase.php` 두개의 베이스 테스트 클래스를 가지게 됩니다. `BrowserKitTestCase` 클래스를 로드하기 위해서, `composer.json` 파일에 추가해야할 수도 있습니다:

    "autoload-dev": {
        "psr-4": {
            "Tests\\": "tests/"
       }
    }

라라벨 5.3에서 작성된 테스트는 `BrowserKitTestCase` 클래스를 상속 받고, 라라벨 5.3의 테스트 레이어를 사용하는 새로운 테스트는 `TestCase` 클래스를 상속받습니다. `BrowserKitTestCase` 클래스는 다음과 같습니다:

    <?php

    use Illuminate\Contracts\Console\Kernel;
    use Laravel\BrowserKitTesting\TestCase as BaseTestCase;

    abstract class BrowserKitTestCase extends BaseTestCase
    {
        /**
         * The base URL of the application.
         *
         * @var string
         */
        public $baseUrl = 'http://localhost';

        /**
         * Creates the application.
         *
         * @return \Illuminate\Foundation\Application
         */
        public function createApplication()
        {
            $app = require __DIR__.'/../bootstrap/app.php';

            $app->make(Kernel::class)->bootstrap();

            return $app;
        }
    }

이 클래스를 생성한뒤에, 여러분의 테스트가 새로운 `BrowserKitTestCase` 클래스를 상속받도록 확인하십시오. 이렇게 하면 라라벨 5.3에서 작성한 모든 테스트를 라라벨 5.4에서 계속 실행할 수 있습니다. 원하는 경우 차근차근 [새로운 라라벨 5.4의 테스트 문법](/docs/5.4/http-tests) 또는 [라라벨 Dusk](/docs/5.4/dusk) 로 포팅 할 수도 있습니다.

> {note} 새로운 테스트를 작성하고 라라벨 5.4 테스트 레이어를 사용하려면 `TestCase` 클래스를 상속받아야합니다.

#### 업그레이드된 어플리케이션에서 Dusk 설치하기

라라벨 5.3에서 업그레이드된 어플리케이션에서 라라벨 Dusk 를 설치하려면, 먼저 컴포저를 통해서 인스톨 하십시오

    composer require --dev laravel/dusk "1.*"

그 다음 `tests` 디렉토리에 `CreatesApplication` 트레이트를 생성해야합니다. 이 트레이트는 테스트 케이스를 위한 새로운 어플리케이션 인스턴스를 생성하는 역할을 합니다. 이 트레이트는 다음과 같은 형태를 가지고 있습니다:

    <?php

    use Illuminate\Contracts\Console\Kernel;

    trait CreatesApplication
    {
        /**
         * Creates the application.
         *
         * @return \Illuminate\Foundation\Application
         */
        public function createApplication()
        {
            $app = require __DIR__.'/../bootstrap/app.php';

            $app->make(Kernel::class)->bootstrap();

            return $app;
        }
    }

> {note} 만약 테스트에 네임스페이스를 지정하고 PSR-4 오토로잉에 따라서 `tests` 디렉토리를 사용하고 있다면, `CreatesApplication` 트레이트를 지정한 네임스페이스에 구성하십시오.

준비단계가 완료되면 일반적인 [DUsk 설치 구성](/docs/{{version}}/dusk#installation)절차를 따르면 됩니다.

#### 구동환경

라라벨 5.4는 더이상 각각의 테스트 클래스에서 수동으로 `putenv('APP_ENV=testing')`를 지정하지 않습니다. 이 대신에 프레임워크는 `.env`파일에서 로딩된 `APP_ENV` 변수를 사용합니다.

#### 이벤트 Fake

`Event` fake의 `assertFired` 메소드는 `assertFired`로, `assertNotFired` 메소드는 `assertNotDispatched` 으로 업데이트해야 합니다. 메소드의 사용방법이 변경되지는 않았습니다.

#### 메일 Fake

라라벨 5.4에서 `Mail` fake는 아주 간단하게 변경되었습니다. `assertSentTo`메소드를 사용하는 대신에, 이제 `assertSent` 메소드를 사용하고, 콜백 안에서 `hasTo`, `hasCc` 등의 헬퍼 메소드를 활용하면 됩니다:

    Mail::assertSent(MailableName::class, function ($mailable) {
        return $mailable->hasTo('email@example.com');
    });

### 다국어

#### `{Inf}` 플레이스홀더

다국어 문자열의 복수 표현을 위해서 `{Inf}` 플레이스홀더를 사용중이었다면, `*` 문자열을 사용하도록 수정해야 합니다:

    {0} First Message|{1,*} Second Message

### URL 생성

#### The `forceSchema` Method

`Illuminate\Routing\UrlGenerator` 클래스의 `forceSchema` 메소드는 `forceScheme`으로 이름이 변경되었습니다.

### 유효성 검사

#### Date 포맷의 유효성 검사

Date 포맷의 유효성 검사가 보다 엄격해지고 PHP [날짜 함수](http://php.net/manual/en/function.date.php) 문서에 있는 플레이스홀더 표현을 지원합니다. 이전 버전의 라라벨에서는 타임존 플레스홀더 `P` 는 모든 타임존 포맷을 받을 수 있었습니다만; 라라벨 5.4에서는 각각의 타임존 포맷은 PHP 문서에 따르는 고유한 플레스홀더를 가지게 됩니다.

#### 메소드 이름

`addError` 메소드는 `addFailure`으로 이름이 변경되었습니다. 또한 `doReplacements` 메소드는 `makeReplacements` 으로 이름이 변경되었습니다. 일반적으로 이 변경사항들은 `Validator` 클래스를 상속받아서 사용하는 경우에만 관련이 있습니다.

### 기타

`laravel/laravel` [GitHub 저장소](https://github.com/laravel/laravel)에서 변경사항을 확인하는 것이 좋습니다. 많은 변경사항들이 모두 필요하지는 않지만, 어플리케이션을 이러한 변경파일들을 통해서 최신으로 유지할 수 있습니다. 변경사항 중 일부는 이 업그레이드 가이드에서 다뤄지지만 설정 파일이나 코멘트와 같은 사항은 다뤄지지 않습니다. [GitHub 비교 도구](https://github.com/laravel/laravel/compare/5.3...5.4)를 통해서 변경사항을 쉽게 확인하고 중요한 업데이트를 수행할 수 있습니다.
