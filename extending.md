# 프레임워크의 확장(Extending The Framework)

- [Managers & Factories 매니저 & 팩토리](#managers-and-factories)
- [캐시](#cache)
- [세션](#session)
- [인증](#authentication)
- [서비스 컨테이너에 의한 확장](#container-based-extension)

<a name="managers-and-factories"></a>
## 매니저 & 팩토리

라라벨은 드라이버 기반의 컴포넌트 생성을 관리하는 여러 `Manager` 클래스를 가지고 있습니다. 캐시, 세션, 인증과 큐 컴포넌트들이 바로 이러한 것들입니다. 매니저 클래스는 어플리케이션의 설정에 따른 특정 드라이버 구현체의 생성에 대해서 책임을 가집니다. 예를 들어, `CacheManager` 클래스는 APC, Memcached, File, 그리고 다른 다양한 캐시 드라이버의 구현체를 생성할 수 있습니다.

각각의 매니저들은 새로운 드라이버의 기능들을 쉽게 매니저에 등록 할 수 있도록 `extend` 메소드를 제공합니다. 이제 매니저에 대해서 예제와 함께 각각의 사용자 정의 드라이버 지원을 어떻게 주입할지 살펴보겠습니다.

> **참고:** 가급적 시간을 가지고 라라벨에서 제공하는 `CacheManager` 와`SessionManager` 같은 `Manager` 클래스를 살펴보시기 바랍니다.  이러한 클래스들을 살펴보는 것은 라라벨의 내부 동작에 대한 보다 확실한 이해를 줄 것입니다. 모든 매니저 클래스는 각각의 매니저에 편리하고 공통된 기능을 제공하도록 `Illuminate\Support\Manager` 기본 클래스를 상속 받습니다.

<!--chak-comment-프레임워크의-확장(Extending-The-Framework)-매니저---팩토리-->

<a name="cache"></a>
## 캐시

라라벨의 캐시 기능을 확장하려면 `CacheManager`의 `extend` 메소드를 사용하면 됩니다. 이 메소드는 매니저에게 사용자 정의 드라이버 resolver를 바인딩 하는 데 사용되며 전체 매니저 클래스에서 공통적으로 사용할 수 있습니다. 예를 들어, "mongo"라는 새로운 캐시 드라이버를 등록하려면 다음과 같이하면 됩니다.

	Cache::extend('mongo', function($app)
	{
		return Cache::repository(new MongoStore);
	});

`extend` 메소드에 전달되는 첫 번째 인자는 드라이버의 이름입니다. 이 이름은 `config/cache.php` 설정 파일의 `driver` 옵션에 해당합니다. 두 번째 인자는 `Illuminate\Cache\Repository` 인스턴스를 반환하는 클로저가 됩니다. 클로저에는 `$app` 인스턴스가 전달되는데, 이 인자는 서비스 컨테이너 역할을 하는 `Illuminate\Foundation\Application`의 인스턴스입니다.

확장된 기능을 사용하기 위해서는 설치된 라라벨 어플리케이션에서 제공하는 `App\Providers\AppServiceProvider`의 `boot` 메소드 안에서 `Cache::extend`가 호출되어야만 합니다. 또는 별도의 서비스 프로바이더를 생성해야 합니다. - 이경우 프로바이더를 `config/app.php`의 provider 배열에 추가하는 것을 잊지 마십시오.

여러분의 고유한 캐시 드라이버를 생성하기 위해서는 먼저 `Illuminate\Contracts\Cache\Store` contract의 구현체가 필요합니다. 따라서 생성하려는 MongoDB 캐시는 다음과 같은 형태가 될 것입니다.

	class MongoStore implements Illuminate\Contracts\Cache\Store {

		public function get($key) {}
		public function put($key, $value, $minutes) {}
		public function increment($key, $value = 1) {}
		public function decrement($key, $value = 1) {}
		public function forever($key, $value) {}
		public function forget($key) {}
		public function flush() {}

	}

각각의 메소드를 MongoDB 연결을 사용하여 구현해야 합니다. 구현을 완료하면, 이제 사용자 정의 드라이버를 등록하면 됩니다.

	Cache::extend('mongo', function($app)
	{
		return Cache::repository(new MongoStore);
	});

새롭게 지정한 캐시 드라이버 코드를 어디에 올려둘까 고민하고 있다면, Packagist에서 사용할 수 있도록 하는 것을 고려해 보십시오! 또는 `app` 디렉토리에 `Extensions` 네임스페이스를 생성할 수도 있습니다. 라라벨은 엄격한 어플리케이션 구조를 가지고 있지 않기 때문에, 원하는대로 편하게 구조를 결정하면 됩니다.

<!--chak-comment-프레임워크의-확장(Extending-The-Framework)-캐시-->

<a name="session"></a>
## 세션

라라벨에서 세션 드라이버를 확장하는 것은 캐시 시스템을 확장하는 것과 마찬가지로 쉽습니다. 마찬가지로 사용자 지정 코드를 등록하기 위해서 `extend` 메소드를 사용합니다.

	Session::extend('mongo', function($app)
	{
		// Return implementation of SessionHandlerInterface
	});

### 어디에서 세션을 확장하는지

### 세션 확장 코드 작성하기

사용자 지정 세션 드라이버는 `SessionHandlerInterface`를 구현한다는 것을 기억하십시오. 이 인터페이스에는 구현해야하는 간단한 몇 가지 메소드가 포함되어 있습니다. MongoDB를 사용하는 구현체라면 다음과 같이 될 것입니다.

	class MongoHandler implements SessionHandlerInterface {

		public function open($savePath, $sessionName) {}
		public function close() {}
		public function read($sessionId) {}
		public function write($sessionId, $data) {}
		public function destroy($sessionId) {}
		public function gc($lifetime) {}

	}

이 메소드들은 캐시의 `StoreInterface`처럼 쉽게 이해 수 없기 때문에 각각의 메소드들에 대해서 간단하게 설명합니다.

- `open` 메소드는 일반적으로 파일 기반의 세션 저장 시스템에서 사용됩니다. 라라벨은 `file` 세션 드라이버를 제공하고 있으므로, 여러분은 거의 해당 메소드에 추가할 것이 없습니다. 이 메소드는 비어 있는 형태로 구성해도 됩니다. 이것은 좋지않은 인터페이스 디자인의 경우가 됩니다만 (나중에 설명합니다), PHP가 이 메소드를 구현하게끔 요구하고 있습니다.
-  `close` 메소드역시 `open` 메소드와 마찬가지로 무시할 수 있습니다. 대부분의 드라이버에서는 필요가 없습니다.
-  `read`  메소드는 주어진 `$sessionId`에 해당하는 문자열의 세션 데이터의 문자열 버전을 반환해야 합니다. 라라벨이 시리얼라이즈(직렬화)를 수행해주기 때문에 여러분이 작성한 드라이버에서 세션 데이터를 탐삭해거나 저장하는 데 시리얼라이즈나 다른 인코딩을 처리할 필요는 없습니다.
- `write` 메소드는 `$sessionId`에 해당하는 `$data` 문자열을 MongoDB, Dynamo 등과 같은 시스템에 저장해야 합니다.
- `destroy` 메소드는 저장소에서 주어진 `$sessionId`에 해당하는 데이터를 삭제해야 합니다.
- `gc` 메소드는 UNIX 타임스탬프로 주어진 `$lifetime` 보다 오래된 모든 세션 데이터들을 제거해야 합니다. Memcached와 Redis처럼 스스로 오래된 데이터를 삭제하는 시스템에서는, 이 메소드는 비워 둡니다.

`SessionHandlerInterface`의 구현을 마치면 이제 다음처럼 세션 매니저에 등록할 수 있습니다.

	Session::extend('mongo', function($app)
	{
		return new MongoHandler;
	});

세션 드라이버를 등록했으면 `mongo` 드라이버를 `config/session.php` 설정 파일에서 사용할 수 있습니다.

> **참고:** 여러분의 고유한 세션 핸들러를 작성했다면 Packagist와 함께 공유해주세요!

<!--chak-comment-프레임워크의-확장(Extending-The-Framework)-세션-->

<a name="authentication"></a>
## 인증

인증은 캐시와 세션 기능과 같은 방법으로 확장이 가능합니다. 앞서 보신바와 같이 `extend` 메소드를 사용하면 됩니다.

	Auth::extend('riak', function($app)
	{
		// Return implementation of Illuminate\Contracts\Auth\UserProvider
	});

`UserProvider`의 구현체는 MySQL, Riak 등과 같은 저장소를 통해서 `Illuminate\Contracts\Auth\Authenticatable` 구현체를 확인하여 반환하는 역할을 수행합니다. 라라벨 인증 매카니즘은 이 두 개의 인터페이스를 통해서 사용자 데이터가 어떻게 저장되어 있는가, 어떤 클래스가 사용자를 나타내고 있는지에 대해서는 관계없이 동작하게 됩니다.

`UserProvider`contract를 살펴봅시다.

	interface UserProvider {

		public function retrieveById($identifier);
		public function retrieveByToken($identifier, $token);
		public function updateRememberToken(Authenticatable $user, $token);
		public function retrieveByCredentials(array $credentials);
		public function validateCredentials(Authenticatable $user, array $credentials);

	}

`retrieveById`함수는 일반적으로 MySQL 데이터베이스의 auto-increment ID와 같은 사용자를 나타내는 숫자키를 인자로 받습니다. ID와 일치하는 `Authenticatable` 구현체를 검색 하여 메소드를 통해서 반환됩니다.

`retrieveByToken` 함수는 고유한 `$identifier`와 `remember_token` 필드에 저장된 “remember me” `$token`을 통해서 사용자를 검색합니다. 앞의 메소드와 함께 `Authenticatable`의 구현체가 반환됩니다.

`updateRememberToken` 메소드는 `$user`의 `remember_token` 필드를 새로운 `$token`으로 변경합니다. 새로운 토큰은 "Remember me"로 성공적으로 로그인했을 때 생성되는 새로운 토큰이거나, 또는 사용자가 로그아웃 할 때는 null일 수 있습니다.

`retrieveByCredentials` 메소드는 어플리케이션에 로그인하기 위해 `Auth::attempt` 메소드에게 전달된 로그인 정보의 배열을 받습니다. 이 메소드는 전달받은 사용자 정보와 일치하는 사용자가  스토리지에 존재하는지 확인하기 위해서 “쿼리-질의”를 수행해야 합니다. 이 방법은 일반적으로 "where" 조건에 `$credentials['username’]` 쿼리를 실행하는 것입니다. 이 메소드는 UserInterface의 구현을 반환해야 합니다. 이 메소드 중에서 암호 검증과 인증을 시도해서는 안됩니다.

`validateCredentials`메소드는 주어진 `$user`와 인증하기 위한 `$credentials`를 비교해야 합니다. 예를 들어, 이 메소드는 `$user->getAuthPassword()` 값과 `$credentials['password']`를 `Hash::make`한 값과 비교할 것입니다. 이 메소드는 사용자의 인증정보를 검증하여 boolean 값을 반환합니다.

이제 `UserProvider`의 메소드를 각각 살펴보았습니다, 이제 `Authenticatable`를 살펴봅시다. 유의 할것은 프로바이더는 `retrieveById`와 `retrieveByCredentials`의 구현체를 반환해야 한다는 것입니다.

	interface Authenticatable {

		public function getAuthIdentifier();
		public function getAuthPassword();
		public function getRememberToken();
		public function setRememberToken($value);
		public function getRememberTokenName();

	}

인터페이스는 간단합니다. `getAuthIdentifier` 메소드는 사용자의 “primary key”를 반환 해야 합니다. MySQL를 벡엔드에서 사용하고 있다면, auto-incrementing primary key가 될 것입니다. `getAuthPassword`메소드는 해쉬 처리된 사용자 패스워드를 반환해야 합니다. 이 인터페이스는 어떤 ORM 및 스토리지 추상 클래스를 사용하고 있는지에 관계없이 인증 시스템이 사용자 클래스에 대해 작동하는 것을 가능하게 하고 있습니다. 기본적으로 라라벨은 이 인터페이스를 구현하고있는 `app` 디렉토리의 `User` 클래스를 포함하고 있습니다. 그래서 구현예제로 이 User 클래스를 살펴보십시오.

`UserProvider`의 구현체를 완성하였다면, `Auth` 파사드를 통해서 등록하면 됩니다.

	Auth::extend('riak', function($app)
	{
		return new RiakUserProvider($app['riak.connection']);
	});

`extend` 메소드를 통해서 드라이버를 등록하고 난후에는, `config/auth.php` 설정 파일에서 새로운 드라이버로 설정을 변경하면 됩니다.

<!--chak-comment-프레임워크의-확장(Extending-The-Framework)-인증-->

<a name="container-based-extension"></a>
## 서비스 컨테이너에 의한 확장

라라벨 프레임워크에 포함된 대부분의 서비스 프로바이더는 서비스 컨테이너에 객체를 바인딩 하고 있습니다. `config/app.php` 설정 파일에서 서비스 프로바이더들의 목록을 확인할 수 있습니다. 가능하다면 여기에 나열된 서비스 프로바이더들의 소스 코드를 살펴보십시오. 그렇게 하면 각각의 서비스 프로바이더들이 프레임워크에 어떤 것을 더하고 있는지 또한, 서비스 컨테이너에 여러 서비스를 바인딩하기 위해서 어떤 키를 사용하고 있는지 더 잘 알 수 있게 될 것입니다.

예를 들어, `HashServiceProvider`는 `Illuminate\Hashing\BcryptHasher`인스턴스를 `hash` 키를 통해서 서비스 컨테이너에 등록합니다. 여러분은 어플리케이션에 바인딩을 재지정 함으로써 손쉽게 이 클래스를 확장하고 재정의 할 수 있습니다. 예를 들어 :

	<?php namespace App\Providers;

	class SnappyHashProvider extends \Illuminate\Hashing\HashServiceProvider {

		public function boot()
		{
			parent::boot();

			$this->app->bindShared('hash', function()
			{
				return new \Snappy\Hashing\ScryptHasher;
			});
		}

	}

이 클래스가 `ServiceProvider` 베이스 클래스가 아닌, `HashServiceProvider` 클래스를 상속하고 있다는 것에 주의 하십시오. 서비스 프로바이더를 상속하고 나면 `config/app.php` 설정 파일에서 확장한 `HashServiceProvider`의 이름을 변경하십시오.

지금까지 컨테이너를에 바인딩된 코어 클래스를 확장하는 일반적인 방법을 살펴보았습니다. 기본적으로 컨테이너에 바인딩된 모든 코어 클래스는 이러한 방법을 통해서 바인딩을 재지정할 수 있습니다. 다시한번 프레임워크에 포함되어 있는 서비스 프로바이더들과 그와 관련된 다양한 클래스들이 어떻게 서비스 컨테이너에 바인딩 되는지, 어떤 키를 사용하는지 살펴보시기 바랍니다. 이렇게 하는 것이 라라벨이 어떻게 동작하는지에 대해서 알 수 있는 좋은 방법이 될것입니다.

<!--chak-comment-프레임워크의-확장(Extending-The-Framework)-서비스-컨테이너에-의한-확장-->
