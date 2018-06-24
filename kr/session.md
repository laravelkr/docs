# Session 세션

- [Configuration 설정](#configuration)
- [Session Usage 세션 사용법](#session-usage)
- [Flash Data 임시 데이터](#flash-data)
- [Database Sessions 데이터베이스 세션](#database-sessions)
- [Session Drivers 세션 드라이버](#session-drivers)

<a name="configuration"></a>
## Configuration 설정

Since HTTP driven applications are stateless, sessions provide a way to store information about the user across requests. HTTP 기반의 애플리케이션은 상태를 저장할수 없기 때문에, HTTP 요청들에 관계없이 사용자의 정보를 저장하기위해서 세션이 사용됩니다. Laravel ships with a variety of session back-ends available for use through a clean, unified API. 라라벨은 다양한 벡엔드 세션들에 관계없이 간결하고 통일된 API를 제공합니다. Support for popular back-ends such as [Memcached](http://memcached.org), [Redis](http://redis.io), and databases is included out of the box. 많이 알려진  [Memcached](http://memcached.org), [Redis](http://redis.io) 그리고 데이터베이스를 별다른 설정 없이 세션 시스템으로 사용할 수 있습니다. 

The session configuration is stored in `config/session.php`. 세션의 설정은 `config/session.php` 파일에 있습니다. Be sure to review the well documented options available to you in this file. 이 파일에는 각각의 옵션에 대한 정리된 문서가 포함되어 있으므로 잘 확인하시기 바랍니다. By default, Laravel is configured to use the `file` session driver, which will work well for the majority of applications. 대부분의 애플리케이션에서 작동이 가능하도록 기본적으로 라라벨은 `file` 세션 드라이버를 사용합니다. 

Before using Redis sessions with Laravel, you will need to install the `predis/predis` package (~1.0) via Composer. 라라벨에서 Redis 세션을 사용하기 위해서는 컴포저를 통해서 `predis/predis` 패키지를 설치해야만 합니다. 

> **Note참고:** If you need all stored session data to be encrypted, set the `encrypt` configuration option to `true`. 세션을 암호화하여 저장하고자 한다면 `encrypt` 설정 옵션을 `true`로 지정하십시오. 

> **Note:** When using the `cookie` session driver, you should **never** remove the `EncryptCookie` middleware from your HTTP kernel. If you remove this middleware, your application will be vulnerable to remote code injection.

> **주의** `cookie` 세션 드라이버를 사용할 때에는 HTTP 커널에서 **절대로** `EncryptCookie` 미들웨어를 제거해서는 안됩니다. 이 미들웨어를 제거하게 되면, 애플리케이션이 원격으로 부터 코드를 실행하는 취약점이 존재합니다.

#### Reserved Keys 
#### 예약어 

The Laravel framework uses the `flash` session key internally, so you should not add an item to the session by that name. 라라벨 프레임워크는 내부적으로 `flash`라는 세션키를 사용하고 있기 때문에 이 이름으로 세션을 추가하지 말아야 합니다. 

<a name="session-usage"></a>
## Session Usage
## 세션 사용법

The session may be accessed in several ways, via the HTTP request's `session` method, the `Session` facade, or the `session` helper function. 세션에 엑세스하기 위해서는 여러가지 방법이 있습니다. HTTP 요청-request의 `session` 메소드를 사용하는 방법, `Session` 파사드를 사용하는 방법, 그리고 `session` 헬퍼 함수를 사용할 수 있습니다. When the `session` helper is called without arguments, it will return the entire session object. For example: 아무런 전달 인자 없이 `session` 헬퍼함수를 호출할 때에는 전체 세션 객체가 반환됩니다. 

	session()->regenerate();

#### Storing An Item In The Session
#### 아이템 세션에 저장하기

	Session::put('key', 'value');

	session(['key' => 'value']);

#### Push A Value Onto An Array Session Value
#### 배열 세션값으로 저장하기 

	Session::push('user.teams', 'developers');

#### Retrieving An Item From The Session
#### 세션에서 특정 아이템 가져오기

	$value = Session::get('key');

	$value = session('key');

#### Retrieving An Item Or Returning A Default Value
#### 특정 아이템을 찾거나 기본값 반환받기

	$value = Session::get('key', 'default');

	$value = Session::get('key', function() { return 'default'; });

#### Retrieving An Item And Forgetting It
#### 아이템 값 가져온 후 삭제하기

	$value = Session::pull('key', 'default');

#### Retrieving All Data From The Session
#### 세션의 모든 데이터 가져오기

	$data = Session::all();

#### Determining If An Item Exists In The Session
#### 세션에 아이템이 존재하는지 확인하기

	if (Session::has('users'))
	{
		//
	}

#### Removing An Item From The Session
#### 세션에서 특정 아이템 삭제하기

	Session::forget('key');

#### Removing All Items From The Session
#### 세션의 모든 아이템 삭제하기

	Session::flush();

#### Regenerating The Session ID
#### 세션 ID 재생성하기 

	Session::regenerate();

<a name="flash-data"></a>
## Flash Data
## 임시 데이터

Sometimes you may wish to store items in the session only for the next request. 때로는 바로 다음번의 요청에서만 사용하기 위해서 세션에 아이템을 저장할 수 있습니다. You may do so using the `Session::flash` method: 바로 `Session::flash` 메소드를 사용하는 것입니다. 

	Session::flash('key', 'value');

#### Reflashing The Current Flash Data For Another Request
#### 현재의 임시 데이터를 다른 요청에서 사용하기 위해서 다시 임시 저장하기

	Session::reflash();

#### Reflashing Only A Subset Of Flash Data
#### 임시 데이터의 일부 값만 다시 임시 저장하기

	Session::keep(['username', 'email']);

<a name="database-sessions"></a>
## Database Sessions
## 데이터 베이스 세션

When using the `database` session driver, you will need to setup a table to contain the session items. `database` 세션 드라이버를 사용하는 경우 세션 아이템들이 저장될 테이블을 생성해야 합니다. Below is an example `Schema` declaration for the table: 다음의 `Schema` 예제를 통해서 테이블을 생성할 수 있습니다. 

	Schema::create('sessions', function($table)
	{
		$table->string('id')->unique();
		$table->text('payload');
		$table->integer('last_activity');
	});

Of course, you may use the `session:table` Artisan command to generate this migration for you! 물론, `session:table` 아티즌 명령어를 통해서 이 마이그레이션을 생성할 수 있습니다. 

	php artisan session:table

	composer dump-autoload

	php artisan migrate

<a name="session-drivers"></a>
## Session Drivers
## 세션 드라이버

The session "driver" defines where session data will be stored for each request. 세션 “드라이버” 는 각각의 요청에 따라 세션이 어디에 저장될지를 결정합니다. Laravel ships with several great drivers out of the box: 라라벨은 별다른 설정 없이 다양한 드라이버를 사용할 수 있습니다. 

- `file` - sessions will be stored in `storage/framework/sessions`.
- `file` - 세션이 `storage/framework/sessions` 폴더에 저장됩니다. 
- `cookie` - sessions will be stored in secure, encrypted cookies.
- `cookie` - 암호화된 쿠키를 사용하여 안전하게 세션을 저장할 것입니다. 
- `database` - sessions will be stored in a database used by your application.
- `database` - 세션은 애플리케이션에 의해서 데이터베이스에 저장됩니다. 
- `memcached` / `redis` - sessions will be stored in one of these fast, cached based stores.
- `memcached` / `redis` - 세션은 캐시 기반의 드라이버들에 의해 빠르게 저장됩니다. 
- `array` - sessions will be stored in a simple PHP array and will not be persisted across requests.
- `array` - 세션은 간단한 PHP 배열에 저장되지만 요청들간에 지속되지 않습니다. 

> **Note주의:** The array driver is typically used for running [unit tests](/docs/{{version}}/testing), so no session data will be persisted. 배열 드라이버의 경우에는 실제로 세션은 유지되지 않기 때문에 [unit tests](/docs/{{version}}/testing)를 수행하는데에만 사용하시기 바랍니다. 
