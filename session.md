# HTTP 세션

- [시작하기](#introduction)
    - [설정하기](#configuration)
    - [드라이버 사전준비사항](#driver-prerequisites)
- [세션 사용하기](#using-the-session)
    - [데이터 조회하기](#retrieving-data)
    - [데이터 저장하기](#storing-data)
    - [데이터 임시저장하기](#flash-data)
    - [데이터 삭제하기](#deleting-data)
    - [세션 ID 다시 생성하기](#regenerating-the-session-id)
- [사용자 정의 세션 드라이버 추가하기](#adding-custom-session-drivers)
    - [드라이버 구현하기](#implementing-the-driver)
    - [드라이버 등록하기](#registering-the-driver)

<a name="introduction"></a>
## 시작하기

HTTP 기반의 애플리케이션은 상태를 저장할수 없기 때문에, HTTP 여러 요청들에 관계없이 사용자의 정보를 저장하기위해서 세션이 사용됩니다. 라라벨은 풍부한 표현이 가능하며 일관된 API를 통해서 엑세스 되는 다양한 세션 백엔드를 제공합니다. 별다른 설정 없이도, 많이 알려진 [Memcached](https://memcached.org), [Redis](https://redis.io) 그리고 데이터베이스를 지원합니다.

<a name="configuration"></a>
### 설정하기

세션의 설정파일은 `config/session.php`로 저장되어 있습니다. 이 파일에서 사용 가능한 옵션을 잘 살펴보십시오. 기본적으로 라라벨은 대부분의 애플리케이션에서 잘 작동 할 수 있도록 `file` 세션 드라이버를 사용하도록 설정되어 있습니다. 실서비스용 애플리케이션에서는 보다 나은 세션 처리 성능을 위해서 `memcached` 또는 `redis` 드라이버를 사용하는 것을 고려하십시오.

세션 `driver` 설정 옵션은 각각의 요청-request에 따른 세션을 어디에 저장할 것인지 정의합니다. 라라벨은 별다른 설정 없이도 다양한 드라이버를 제공합니다.

- `file` - `storage/framework/sessions` 디렉토리에 세션을 저장합니다.
- `cookie` - 암호화된 쿠키를 사용하여 안전하게 세션을 저장할 것입니다.
- `database` - 세션이 관계형 데이터베이스에 저장된다.
- `memcached` / `redis` - 빠르고, 캐시를 기반으로한 memcached, redis 에 저장합니다.
- `array` - 세션은 PHP 배열에 저장되며 지속되지 않습니다.

> {tip} array 드라이버는 [테스트](/docs/{{version}}/testing)가 진행되는 동안 사용되고, 세션이 지속적으로 유지되지 않습니다.

<a name="driver-prerequisites"></a>
### 드라이버의 사전준비사항

#### 데이터베이스

`database` 세션 드라이버를 사용하는 경우, 세션을 저장할 수 있는 테이블을 생성할 필요가 있습니다. 다음의 `Schema` 예제를 통해서 테이블을 생성할 수 있습니다:

    Schema::create('sessions', function ($table) {
        $table->string('id')->unique();
        $table->unsignedInteger('user_id')->nullable();
        $table->string('ip_address', 45)->nullable();
        $table->text('user_agent')->nullable();
        $table->text('payload');
        $table->integer('last_activity');
    });

`session:table` 아티즌 명령어를 통해서 이 마이그레이션을 생성할 수 있습니다:

    php artisan session:table

    php artisan migrate

#### Redis

라라벨에서 Redis 세션을 사용하기 전에, Composer 를 통해서 `predis/predis` 패키지 (~1.0)을 설치할 필요가 있습니다. `database` 설정 파일 안에서 Redis 커넥션을 설정할 수 있습니다. `session` 설정 파일에서 `connection` 옵션은 세션이 어떤 Redis 커넥션을 사용할지 지정하는데 사용될 수 있습니다.

<a name="using-the-session"></a>
## 세션 사용하기

<a name="retrieving-data"></a>
### 데이터 조회하기

라라벨 안에서 세션 데이터를 조작하려면 두 가지 주요한 방법이 있습니다: 글로벌 `session` 헬퍼를 사용하는 것과 `Request` 인스턴스를 통한 방법입니다. 먼저 컨트롤러 메소드에서 타입-힌트 할 수 있는 `Request` 인스턴스를 통해서 세션에 엑세스 하는 것을 살펴보겠습니다. 유의할 것은, 컨트롤러 메소드는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)를 통해서 자동으로 의존성 주입된다는 것입니다:

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function show(Request $request, $id)
        {
            $value = $request->session()->get('key');

            //
        }
    }

세션에서 특정 아이템을 찾을 때, `get` 메소드의 두번째 인자로 기본 값을 전달 할 수 있습니다. 이 기본값은 지정된 키가 세션 안에서 존재하지 않을 경우 반환될 것입니다. `get` 메소드의 기본값으로 `클로저`를 전달하는 경우 요청받은 키가 존재하지 않는다면, `클로저`가 실행결과를 반환할 것입니다:

    $value = $request->session()->get('key', 'default');

    $value = $request->session()->get('key', function () {
        return 'default';
    });

#### 글로벌 세션 헬퍼

또한 세션에서 데이터를 찾거나, 저장하기 위해서 글로벌 `session` PHP 함수를 사용할 수도 있습니다. `session` 헬퍼를 하나의 문자열을 인자로 호출하게 되면, 해당하는 세션 키에 대한 값을 반환합니다. 헬퍼가 키 / 값 쌍으로 구성된 배열과 함께 호출되면 해당 세션 값이 세션에 저장됩니다:

    Route::get('home', function () {
        // Retrieve a piece of data from the session...
        $value = session('key');

        // Specifying a default value...
        $value = session('key', 'default');

        // Store a piece of data in the session...
        session(['key' => 'value']);
    });

> {tip} 글로벌 `session` 헬퍼를 사용하는 것에 비해서 HTTP 요청-request 인스턴스에서 세션을 사용하는 것에는 약간의 실질적인 차이가 있습니다. 두가지 메소드는 테스트 케이스 안에서 사용가능한 `assertSessionHas` 메소드를 통해서 [테스트가 가능합니다](/docs/{{version}}/testing).

#### 모든 세션 데이터 조회하기

세션에서 모든 데이터를 찾고자 한다면 `all` 메소드를 사용하면 됩니다:

    $data = $request->session()->all();

#### 세션에 아이템이 존재하는지 확인하기

세션안에 아이템이 존재하는지 확인하려면 `has` 메소드를 사용하면 됩니다. `has` 메소드는 값이 현재 존재하고 `null`이 아니라면 `true`를 반환합니다:

    if ($request->session()->has('users')) {
        //
    }

값이 `null`이더라도 세션안에 아이템이 들어 있는지 확인하려면 `exists` 메소드를 사용할 수 있습니다. `exists` 메소드는 값이 존재한다면 `true`를 반환합니다:

    if ($request->session()->exists('users')) {
        //
    }

<a name="storing-data"></a>
### 데이터 저장하기

세션에 데이터를 저장하기 위해서는, 일반적으로 `put` 메소드나 `session` 헬퍼를 사용합니다:

    // Via a request instance...
    $request->session()->put('key', 'value');

    // Via the global helper...
    session(['key' => 'value']);

#### 세션에 들어 있는 배열에 값 추가하기

`push` 메소드는 세션에 들어 있는 배열에 새로운 값을 추가할 때 사용할 수 있습니다. 예를 들어 어떤 팀의 이름들에 대한 배열을 나타내는 `user.teams` 키가 있을 때, 다음과 같이 값을 추가할 수 있습니다:

    $request->session()->push('user.teams', 'developers');

#### 아이템을 조회 & 삭제하기

하나의 구문안에서 `pull` 메소드는 세션에서 아이템을 가져오면서 삭제합니다:

    $value = $request->session()->pull('key', 'default');

<a name="flash-data"></a>
### 데이터 임시저장하기

때로는 바로 다음번의 요청에서만 사용하기 위해서 세션에 아이템을 저장할 수 있습니다. 바로 `flash` 메소드를 사용하는 것입니다. 이 메소드를 사용하여 세션에 저장된 데이터는 바로 이어지는 HTTP 요청에 대해서만 사용이 가능하고, 이후에는 삭제됩니다. 임시 데이터는 주로 상태 메세지등 잠깐동안 사용하데 유용합니다:

    $request->session()->flash('status', 'Task was successful!');

여러 요청-request에 대해서 임시 데이터를 보다 오랫동안 유지할 필요가 있을 때, `reflash` 메소드를 사용하면 임시 데이터가 추가 요청-request에 대해서도 계속 유지됩니다. 지정한 임시 데이터만을 계속 유지하고자 한다면, `keep` 메소드를 사용하면 됩니다:

    $request->session()->reflash();

    $request->session()->keep(['username', 'email']);

<a name="deleting-data"></a>
### 데이터 삭제하기

`forget` 메소드는 세션에서 데이터를 삭제합니다. 세션에서 모든 데이터를 삭제하기를 원한다면 `flush` 메소드를 사용하면 됩니다:

    $request->session()->forget('key');

    $request->session()->flush();

<a name="regenerating-the-session-id"></a>
### 세션 ID 다시 생성하기

세션 ID를 다시 생성하는 것은 종종 악의적인 사용자의 애플리케이션에 대한 [세션 fixation](https://en.wikipedia.org/wiki/Session_fixation) 공격을 방지하기 위해서 수행합니다.

라라벨에 포함된 `LoginController` 사용하는 경우 인증 과정에서 세션 ID가 자동으로 재생성됩니다; 그렇지만 세션 ID를 수동으로 다시 생성할 필요가 있다면 `regenerate` 메소드를 사용할 수 있습니다.

    $request->session()->regenerate();

<a name="adding-custom-session-drivers"></a>
## 사용자 정의 세션 드라이버 추가하기

<a name="implementing-the-driver"></a>
#### 드라이버 구현하기

여러분의 사용자 정의 세션 드라이버는 `SessionHandlerInterface`를 구현해야 합니다. 이 인터페이스는 구현해야할 필요가 있는 몇가지 간단한 메소드를 포함하고 있습니다. MongoDB를 사용하는 구현체라면 다음과 같이 될 것입니다:

    <?php

    namespace App\Extensions;

    class MongoSessionHandler implements \SessionHandlerInterface
    {
        public function open($savePath, $sessionName) {}
        public function close() {}
        public function read($sessionId) {}
        public function write($sessionId, $data) {}
        public function destroy($sessionId) {}
        public function gc($lifetime) {}
    }

> {tip} 라라벨은 이러한 확장 기능을 담아둘 디렉토리를 제공하지는 않습니다. 원하는 곳 어디에든 자유롭게 구성할 수 있습니다. 이 예제에서는, `MongoSessionHandler`를 저장하기 위해서 `Extensions` 디렉토리를 만들었습니다.

이 메소드들의 목적을 쉽게 이해하기 어렵기 때문에, 각각의 메소드를 빠르게 살펴보겠습니다:

- `open` 메소드는 일반적으로 파일 기반의 세션 저장 시스템에서 사용됩니다. 라라벨은 `file` 세션 드라이버를 제공하고 있기 때문에, 여러분은 거의 해당 메소드에 추가할 것이 없습니다. 이 메소드는 비어 있는 형태로 구성해도 됩니다. 이것은 좋지않은 인터페이스 디자인의 경우가 됩니다만 (나중에 설명합니다), PHP가 이 메소드를 구현하게끔 요구하고 있습니다.
- `close` 메소드역시 `open` 메소드와 마찬가지로 무시할 수 있습니다. 대부분의 드라이버에서는 필요가 없습니다.
- `read`  메소드는 주어진 `$sessionId` 에 해당하는 문자열의 세션 데이터의 문자열 버전을 반환해야합니다. 라라벨이 시리얼라이즈(직렬화)를 수행해주기 때문에 여러분이 작성한 드라이버에서 세션 데이터를 탐색하거나 저장하는데 시리얼라이즈나 다른 인코딩을 처리할 필요는 없습니다.
- `write` 메소드는 `$sessionId` 에 해당하는 `$data` 문자열을 MongoDB, Dynamo 등과 같은 시스템에 저장해야 합니다. 다시 말하지만, 라라벨이 이미 처리하기 때문에, 여러분은 어떠한 시리얼라이제이션-직렬화도 수행하지 말아야 합니다.
- `destory` 메소드는 저장소에서 주어진 `$sessionId` 에 해당하는 데이터를 삭제해야 합니다.
- `gc` 메소드는 UNIX 타임스탬프로 주어진 `$lifetime` 보다 오래된 모든 세션 데이터들을 제거해야합니다. Memcached와 Redis처럼 스스로 오래된 데이터를 삭제하는 시스템에서는, 이 메소드는 비워 둡니다.

<a name="registering-the-driver"></a>
#### 드라이버 등록하기

드라이버를 구현하였다면, 프레임워크에 등록할 준비가 되었습니다. 라라벨의 세션 벡엔드에 추가적인 드라이버를 추가하려면 `Session` [파사드](/docs/{{version}}/facades)의 `extens` 메소드를 사용할 수 있습니다. [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드에서 `extend` 메소드를 호출해야 합니다. 이미 존재하는 `AppServiceProvider` 또는 완전히 새로운 프로바이더를 생성하여 수행할 수 있습니다:

    <?php

    namespace App\Providers;

    use App\Extensions\MongoSessionHandler;
    use Illuminate\Support\Facades\Session;
    use Illuminate\Support\ServiceProvider;

    class SessionServiceProvider extends ServiceProvider
    {
        /**
         * Perform post-registration booting of services.
         *
         * @return void
         */
        public function boot()
        {
            Session::extend('mongo', function ($app) {
                // Return implementation of SessionHandlerInterface...
                return new MongoSessionHandler;
            });
        }

        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

세션 드라이버를 등록했으면 `mongo` 드라이버를 `config/session.php` 설정 파일에서 사용할 수 있습니다.
