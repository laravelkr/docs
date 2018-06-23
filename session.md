# 세션

- [소개](#introduction)
- [기본적인 사용법](#basic-usage)
    - [임시 데이터](#flash-data)
- [사용자 정의 세션 드라이버 추가하기](#adding-custom-session-drivers)

<a name="introduction"></a>
## 소개

HTTP 기반의 어플리케이션은 상태를 저장할수 없기 때문에, HTTP 요청들에 관계없이 사용자의 정보를 저장하기위해서 세션이 사용됩니다. 라라벨은 다양한 벡엔드 세션들에 관계없이 간결하고 통일된 API를 제공합니다. 많이 알려진  [Memcached](http://memcached.org), [Redis](http://redis.io) 그리고 데이터베이스를 별다른 설정 없이 세션 시스템으로 사용할 수 있습니다.

### 설정하기

세션의 설정파일은 `config/session.php`로 저장되어 있습니다. 이 파일에는 각각의 옵션에 대한 정리된 문서가 포함되어 있으므로 잘 확인하시기 바랍니다. 많은 어플리케이션에서 작동이 가능하도록 라라벨에서는 기본적으로 `file` 세션 드라이버를 사용합니다. 실서비스용 어플리케이션에서는 보다 나은 세션 처리 성능을 위해서 `memcached` 나 `redis` 드라이버를 사용하는 것을 고려하십시오.

세션 `driver` 정의는 각각의 요청에 따른 세션을 어디에 저장할 것인지 정의합니다. 라라벨은 별다른 설정 없이도 다양한 드라이버를 제공합니다.

- `file` - `storage/framework/sessions` 디렉토리에 세션을 저장합니다.
- `cookie` - 암호화된 쿠키를 사용하여 안전하게 세션을 저장할 것입니다.
- `database` - 어플리케이션이 사용하는 데이터베이스에 저장합니다. .
- `memcached` / `redis` - 빠르고, 캐시를 기반으로한 memcached, redis 에 저장합니다.
- `array` - PHP 배열에 세션을 저장합니다. 이경우 세션이 요청에 대해서 지속적으로 유지 되지 않습니다.

> **주의:** array 드라이버는 일반적으로 [테스팅](/docs/{{version}}/testing)용으로 사용되고, 세션이 유지되지 않습니다.

### 드라이버별 요구사항

#### 데이터베이스

`database` 세션 드라이버를 사용하는 경우, 세션을 저장할 수 있는 테이블을 구성할 필요가 있을 것입니다. 다음의 `Schema` 예제를 통해서 테이블을 생성할 수 있습니다.

    Schema::create('sessions', function ($table) {
        $table->string('id')->unique();
        $table->text('payload');
        $table->integer('last_activity');
    });

You may use the `session:table` Artisan command to generate this migration for you!

`session:table` 아티즌 명령어를 통해서 이 마이그레이션을 생성할 수 있습니다.

    php artisan session:table

    composer dump-autoload

    php artisan migrate

#### Redis

라라벨에서 Redis 세션을 사용하기 전에, Composer 를 통해서 `predis/predis` 패키지 (~1.0)을 설치할 필요가 있습니다.

### 다른 세션에서의 고려사항

라라벨 프레임워크는 내부적으로 `flash`라는 세션키를 사용하고 있어서, 이 이름을 아이템 이름으로 하여 세션에 추가해서는 안됩니다.

만약 세션에 저장하는 모든 데이터를 암호화 할 필요가 있다면, `encrypt` 옵션을 `true` 로 설정하면 됩니다.

<a name="basic-usage"></a>
## 기본적인 사용법

#### 세션에 엑세스 하기

먼저, 세션에 엑세스해 보겠습니다. 컨트롤러 메소드에서 타입-힌트를 사용하여 HTTP request 를 통해서 세션 인스턴스에 엑세스 할 수 있습니다. 유의할 것은, 컨트롤러 메소드는 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)를 통한 의존성 주입된다는 것입니다.

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
        public function showProfile(Request $request, $id)
        {
            $value = $request->session()->get('key');

            //
        }
    }

세션에서 특정 값을 찾을 때, `get` 메소드의 두번째 인자로 기본 값을 전달 할 수 있습니다. 이 기본값은 지정된 키가 세션 안에서 존재하지 않을 경우 반환될 것입니다. `get` 메소드의 기본값으로 `Closure`를 전달한다면, `Closure` 는 실행 결과를 반환할 것입니다.

    $value = $request->session()->get('key', 'default');

    $value = $request->session()->get('key', function() {
        return 'default';
    });

세션에서 모든 데이터를 찾고자 한다면 `all` 메소드를 사용하면 됩니다.

    $data = $request->session()->all();

또한 세션에서 데이터를 찾거나, 저장하기 위해서 글로벌 `session` PHP 함수를 사용할 수도 있습니다.

    Route::get('home', function () {
        // Retrieve a piece of data from the session...
        $value = session('key');

        // Store a piece of data in the session...
        session(['key' => 'value']);
    });

#### 세션에 아이템이 존재하는지 확인하기

`has` 메소드는 세션안에 아이템이 존재하는지 확인하는데 사용됩니다. 이 메소드는 아이템이 존재하는 경우 `true` 를 반환합니다.

    if ($request->session()->has('users')) {
        //
    }

#### 세션에 데이터 저장하기

세션 인스턴스에 엑세스 한 뒤에, 데이터를 처리하기 위한 다양한 함수들을 호출할 수 있습니다. 예를 들어 `put` 메소드는 세션에 새로운 데이터를 저장합니다.

    $request->session()->put('key', 'value');

#### 세션에 들어 있는 배열에 값 추가하기

`push` 메소드는 세션에 들어 있는 배열에 새로운 값을 추가할 때 사용할 수 있습니다. 예를 들어 어떤 팀의 이름들에 대한 배열을 나타내는 `user.teams` 키가 있을 때, 다음과 같이 값을 추가할 수 있습니다.

    $request->session()->push('user.teams', 'developers');

#### 아이템을 가져오면서 삭제하기

`pull` 메소드는 세션에서 아이템을 가져오면서 삭제합니다.

    $value = $request->session()->pull('key', 'default');

#### 세션에서 아이템 삭제하기 =

`forget` 메소드는 세션에서 데이터를 삭제합니다. 세션에서 모든 데이터를 삭제하기를 원한다면 `flush` 메소드를 사용하면 됩니다:

    $request->session()->forget('key');

    $request->session()->flush();

#### 세션 ID 다시 생성하기

세션 ID를 다시 생성할 필요가 있다면, `regenerate` 메소드를 사용하면 됩니다:

    $request->session()->regenerate();

<a name="flash-data"></a>
### 임시 데이터

때로는 바로 다음번의 요청에서만 사용하기 위해서 세션에 아이템을 저장할 수 있습니다. 바로 `flash` 메소드를 사용하는 것입니다. 이 메소드를 사용하여 세션에 저장된 데이터는 바로 이어지는 HTTP 요청에 대해서만 사용이 가능하고, 이후에는 삭제됩니다. 임시 데이터는 주로 상태 메세지등 잠깐동안 사용하데 유용합니다:

    $request->session()->flash('status', 'Task was successful!');

임시 데이터를 보다 오랫동안 유지할 필요가 있을 때, `reflash` 메소드를 사용하면 임시 디이터가 추가적인 요청에 대해서도 계속 유지됩니다. 지정한 임시 데이터를 계속 유지하고자 한다면, `keep` 메소드를 사용하면 됩니다:

    $request->session()->reflash();

    $request->session()->keep(['username', 'email']);

<a name="adding-custom-session-drivers"></a>
## 사용자 정의 세션 드라이버 추가하기

라라벨의 세션기능에 추가적인 드라이버를 더하고자 하려면, `Session` [파사드](/docs/{{version}}/facades)에 `extend` 메소드를 사용하면 됩니다. [서비스 프로바이더](/docs/{{version}}/providers)의 `boot` 메소드에서 `extend` 메소드를 호출할 수 있습니다:

    <?php

    namespace App\Providers;

    use Session;
    use App\Extensions\MongoSessionStore;
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
            Session::extend('mongo', function($app) {
                // Return implementation of SessionHandlerInterface...
                return new MongoSessionStore;
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

사용자 지정 세션 드라이버는 `SessionHandlerInterface` 를 구현한다는 것을 기억하십시오. 이 인터페이스에는 구현해야하는 간단한 몇 가지 메소드가 포함되어 있습니다. MongoDB를 사용하는 구현체라면 다음과 같이 될 것입니다.

    <?php

    namespace App\Extensions;

    class MongoHandler implements SessionHandlerInterface
    {
        public function open($savePath, $sessionName) {}
        public function close() {}
        public function read($sessionId) {}
        public function write($sessionId, $data) {}
        public function destroy($sessionId) {}
        public function gc($lifetime) {}
    }

이 메소드들은 캐시의 `StoreInterface` 처럼 쉽게 이해 수 없기 때문에 각각의 메소드들에 대해서 간단하게 설명합니다.

- `open` 메소드는 일반적으로 파일 기반의 세션 저장 시스템에서 사용됩니다. 라라벨은 `file` 세션 드라이버를 제공하고 있기 때문에, 여러분은 거의 해당 메소드에 추가할 것이 없습니다. 이 메소드는 비어 있는 형태로 구성해도 됩니다. 이것은 좋지않은 인터페이스 디자인의 경우가 됩니다만 (나중에 설명합니다), PHP가 이 메소드를 구현하게끔 요구하고 있습니다.
- The `close` method, like the `open` method, can also usually be disregarded. For most drivers, it is not needed.
- `close` 메소드역시 `open` 메소드와 마찬가지로 무시할 수 있습니다. 대부분의 드라이버에서는 필요가 없습니다.
- `read`  메소드는 주어진 `$sessionId` 에 해당하는 문자열의 세션 데이터의 문자열 버전을 반환해야합니다. 라라벨이 시리얼라이즈(직렬화)를 수행해주기 때문에 여러분이 작성한 드라이버에서 세션 데이터를 탐삭해거나 저장하는데 시리얼라이즈나 다른 인코딩을 처리할 필요는 없습니다.
- `write` 메소드는 `$sessionId` 에 해당하는 `$data` 문자열을 MongoDB, Dynamo 등과 같은 시스템에 저장해야 합니다.
- The `destroy` method should remove the data associated with the `$sessionId` from persistent storage.
- `destory` 메소드는 저장소에서 주어진 `$sessionId` 에 해당하는 데이터를 삭제해야 합니다.
- `gc` 메소드는 UNIX 타임스탬프로 주어진 `$lifetime` 보다 오래된 모든 세션 데이터들을 제거해야합니다. Memcached와 Redis처럼 스스로 오래된 데이터를 삭제하는 시스템에서는, 이 메소드는 비워 둡니다.

세션 드라이버를 등록했으면 `mongo` 드라이버를 `config/session.php` 설정 파일에서 사용할 수 있습니다.
