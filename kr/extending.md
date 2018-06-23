# Extending The Framework
# 프레임워크의 확장

- [Managers & Factories 매니저 & 팩토리](#managers-and-factories)
- [Cache 캐시](#cache)
- [Session 세션](#session)
- [Authentication 인증](#authentication)
- [Service Container Based Extension 서비스 컨테이너에 의한 확장](#container-based-extension)

<a name="managers-and-factories"></a>
## Managers & Factories
## 매니저 & 팩토리

Laravel has several `Manager` classes that manage the creation of driver-based components. These include the cache, session, authentication, and queue components. The manager class is responsible for creating a particular driver implementation based on the application's configuration. For example, the `CacheManager` class can create APC, Memcached, File, and various other implementations of cache drivers.

라라벨은 드라이버 기반의 컴포넌트 생성을 관리하는 여러 `Manager` 클래스를 가지고 있습니다. 캐시, 세션, 인증과 큐 컴포넌트들이 바로 이러한 것들입니다. 매니저 클래스는 어플리케이션의 설정에 따른 특정 드라이버 구현체의 생성에 대해서 책임을 가집니다. 예를 들어 `CacheManager` 클래스는 APC, Memcached, File, 그리고 다른 다양한 캐시 드라이버의 구현체를 생성할 수 있습니다.

Each of these managers includes an `extend` method which may be used to easily inject new driver resolution functionality into the manager. We'll cover each of these managers below, with examples of how to inject custom driver support into each of them.

각각의 매니저들은 새로운 드라이버의 기능들을 쉽게 매니저에 등록 할 수 있도록 `extend` 메소드를 제공합니다. 이제 매니저에 대해서 예제와 함께 각각의 사용자 정의 드라이버 지원을 어떻게 주입할지 살펴보겠습니다.

> **Note:** Take a moment to explore the various `Manager` classes that ship with Laravel, such as the `CacheManager` and `SessionManager`. Reading through these classes will give you a more thorough understanding of how Laravel works under the hood. All manager classes extend the `Illuminate\Support\Manager` base class, which provides some helpful, common functionality for each manager.

> **참고:** 가급적 시간을 가지고 라라벨에서 제공하는 `CacheManager` 와`SessionManager` 같은 `Manager` 클래스를 살펴보시기 바랍니다.  이러한 클래스들을 살펴보는 것은 라라벨의 내부 동작에 대한 보다 확실한 이해를 줄 것입니다. 모든 매니저 클래스는 각각의 매니저에 편리하고 공통된 기능을 제공하도록 `Illuminate\Support\Manager` 기본 클래스를 상속 받습니다.

<a name="cache"></a>
## Cache
## 캐시

To extend the Laravel cache facility, we will use the `extend` method on the `CacheManager`, which is used to bind a custom driver resolver to the manager, and is common across all manager classes. For example, to register a new cache driver named "mongo", we would do the following:

라라벨의 캐시 기능을 확장하려면 `CacheManager`의 `extend` 메소드를 사용하면 됩니다. 이 메소드는 매니저에게 사용자 정의 드라이버 resolver 를 바인딩 하는데 사용되며 전체 매니저 클래스에서 공통적으로 사용할 수 있습니다. 예를 들어, "mongo"라는 새로운 캐시 드라이버를 등록하려면 다음과 같이하면 됩니다.

	Cache::extend('mongo', function($app)
	{
		return Cache::repository(new MongoStore);
	});

The first argument passed to the `extend` method is the name of the driver. This will correspond to your `driver` option in the `config/cache.php` configuration file. The second argument is a Closure that should return an `Illuminate\Cache\Repository` instance. The Closure will be passed an `$app` instance, which is an instance of `Illuminate\Foundation\Application` and a service container.

`extend` 메소드에 전달되는 첫번째 인자는 드라이버의 이름입니다. 이 이름은 `config/cache.php` 설정 파일의 `driver` 옵션에 해당합니다. 두번째 인자는 `Illuminate\Cache\Repository` 인스턴스를 반환하는 클로저가 됩니다. 클로저에는 `$app` 인스턴스가 전달되는데, 이 인자는 서비스 컨테이너 역활을 하는 `Illuminate\Foundation\Application`의 인스턴스입니다.

The call to `Cache::extend` could be done in the `boot` method of the default `App\Providers\AppServiceProvider` that ships with fresh Laravel applications, or you may create your own service provider to house the extension - just don't forget to register the provider in the `config/app.php` provider array.

확장된 기능을 사용하기 위해서는 설치된 라라벨 어플리케이션에서 제공하는 `App\Providers\AppServiceProvider`의 `boot` 메소드 안에서 `Cache::extend`가 호출되어야만 합니다. 또는 별도의 서비스 프로바이더를 생성해야 합니다. - 이경우 프로바이더를 `config/app.php` 의 provider 배열에 추가하는 것을 잊지 마십시오.

To create our custom cache driver, we first need to implement the `Illuminate\Contracts\Cache\Store` contract. So, our MongoDB cache implementation would look something like this:
여러분의 고유한 캐시 드라이버를 생성하기 위해서는 먼저 `Illuminate\Contracts\Cache\Store` contract 의 구현체가 필요합니다. 따라서 생성하려는 MongoDB 캐시는 다음과 같은 형태가 될 것입니다.

	class MongoStore implements Illuminate\Contracts\Cache\Store {

		public function get($key) {}
		public function put($key, $value, $minutes) {}
		public function increment($key, $value = 1) {}
		public function decrement($key, $value = 1) {}
		public function forever($key, $value) {}
		public function forget($key) {}
		public function flush() {}

	}

We just need to implement each of these methods using a MongoDB connection. Once our implementation is complete, we can finish our custom driver registration:

각각의 메소드를 MongoDB 연결을 사용하여 구현해야합니다. 구현을 완료하면, 이제 사용자 정의 드라이버를 등록하면 됩니다.

	Cache::extend('mongo', function($app)
	{
		return Cache::repository(new MongoStore);
	});

If you're wondering where to put your custom cache driver code, consider making it available on Packagist! Or, you could create an `Extensions` namespace within your `app` directory. However, keep in mind that Laravel does not have a rigid application structure and you are free to organize your application according to your preferences.

새롭게 지정한 캐시 드라이버 코드를 어디에 올려둘까 고민하고 있다면, Packagist에서 사용할 수 있도록 하는 것을 고려해 보십시오! 또는 `app` 디렉토리에 `Extensions` 네임스페이스를 생성할 수도 있습니다. 라라벨은 엄격한 어플리케이션 구조를 가지고 있지 않기 때문에, 원하는대로 편하게 구조를 결정하면 됩니다.

<a name="session"></a>
## Session
## 세션

Extending Laravel with a custom session driver is just as easy as extending the cache system. Again, we will use the `extend` method to register our custom code:

라라벨에서 세션 드라이버를 확장하는 것은 캐시 시스템을 확장하는 것과 마찬가지로 쉽습니다. 마찬가지로 사용자 지정 코드를 등록하기 위해서 `extend` 메소드를 사용합니다.

	Session::extend('mongo', function($app)
	{
		// Return implementation of SessionHandlerInterface
	});

### Where To Extend The Session
### 어디에서 세션을 확장하는지

You should place your session extension code in the `boot` method of your `AppServiceProvider`.  `AppServiceProvider`의 `boot` 메소드에서 세션 확장코드를 입력하면 됩니다.

### Writing The Session Extension
### 세션 확장 코드 작성하기

Note that our custom session driver should implement the `SessionHandlerInterface`. This interface contains just a few simple methods we need to implement. A stubbed MongoDB implementation would look something like this:

사용자 지정 세션 드라이버는 `SessionHandlerInterface` 를 구현한다는 것을 기억하십시오. 이 인터페이스에는 구현해야하는 간단한 몇 가지 메소드가 포함되어 있습니다. MongoDB를 사용하는 구현체라면 다음과 같이 될 것입니다.

	class MongoHandler implements SessionHandlerInterface {

		public function open($savePath, $sessionName) {}
		public function close() {}
		public function read($sessionId) {}
		public function write($sessionId, $data) {}
		public function destroy($sessionId) {}
		public function gc($lifetime) {}

	}

Since these methods are not as readily understandable as the cache `StoreInterface`, let's quickly cover what each of the methods do:

이 메소드들은 캐시의 `StoreInterface` 처럼 쉽게 이해 수 없기 때문에 각각의 메소드들에 대해서 간단하게 설명합니다.

- The `open` method would typically be used in file based session store systems. Since Laravel ships with a `file` session driver, you will almost never need to put anything in this method. You can leave it as an empty stub. It is simply a fact of poor interface design (which we'll discuss later) that PHP requires us to implement this method.
- `open` 메소드는 일반적으로 파일 기반의 세션 저장 시스템에서 사용됩니다. 라라벨은 `file` 세션 드라이버를 제공하고 있기 때문에, 여러분은 거의 해당 메소드에 추가할 것이 없습니다. 이 메소드는 비어 있는 형태로 구성해도 됩니다. 이것은 좋지않은 인터페이스 디자인의 경우가 됩니다만 (나중에 설명합니다), PHP가 이 메소드를 구현하게끔 요구하고 있습니다.
- The `close` method, like the `open` method, can also usually be disregarded. For most drivers, it is not needed.
-  `close` 메소드역시 `open` 메소드와 마찬가지로 무시할 수 있습니다. 대부분의 드라이버에서는 필요가 없습니다.
- The `read` method should return the string version of the session data associated with the given `$sessionId`. There is no need to do any serialization or other encoding when retrieving or storing session data in your driver, as Laravel will perform the serialization for you.
-  `read`  메소드는 주어진 `$sessionId` 에 해당하는 문자열의 세션 데이터의 문자열 버전을 반환해야합니다. 라라벨이 시리얼라이즈(직렬화)를 수행해주기 때문에 여러분이 작성한 드라이버에서 세션 데이터를 탐색하거나 저장하는데 시얼라이즈나 다른 인코딩을 처리할 필요는 없습니다.
- The `write` method should write the given `$data` string associated with the `$sessionId` to some persistent storage system, such as MongoDB, Dynamo, etc.
- `write` 메소드는 `$sessionId` 에 해당하는 `$data` 문자열을 MongoDB, Dynamo 등과 같은 시스템에 저장해야 합니다.
- The `destroy` method should remove the data associated with the `$sessionId` from persistent storage.
- `destory` 메소드는 저장소에서 주어진 `$sessionId` 에 해당하는 데이터를 삭제해야 합니다.
- The `gc` method should destroy all session data that is older than the given `$lifetime`, which is a UNIX timestamp. For self-expiring systems like Memcached and Redis, this method may be left empty.
- `gc` 메소드는 UNIX 타임스탬프로 주어진 `$lifetime` 보다 오래된 모든 세션 데이터들을 제거해야합니다. Memcached와 Redis처럼 스스로 오래된 데이터를 삭제하는 시스템에서는, 이 메소드는 비워 둡니다.

Once the `SessionHandlerInterface` has been implemented, we are ready to register it with the Session manager:
`SessionHandlerInterface`의 구현을 마치면 이제 다음처럼 세션 매니저에 등록할 수 있습니다.

	Session::extend('mongo', function($app)
	{
		return new MongoHandler;
	});

Once the session driver has been registered, we may use the `mongo` driver in our `config/session.php` configuration file.

세션 드라이버를 등록했으면 `mongo` 드라이버를 `config/session.php` 설정 파일에서 사용할 수 있습니다.

> **Note:** Remember, if you write a custom session handler, share it on Packagist!
> **참고:** 여러분의 고유한 세션 핸들러를 작성했다면 Packagist와 함께 공유해주세요!

<a name="authentication"></a>
## Authentication
## 인증

Authentication may be extended the same way as the cache and session facilities. Again, we will use the `extend` method we have become familiar with:
인증은 캐시와 세션 기능과 동일한 방법으로 확장이 가능합니다. 앞서 보신바와 같이 `extend` 메소드를 사용하면 됩니다.

	Auth::extend('riak', function($app)
	{
		// Return implementation of Illuminate\Contracts\Auth\UserProvider
	});

The `UserProvider` implementations are only responsible for fetching a `Illuminate\Contracts\Auth\Authenticatable` implementation out of a persistent storage system, such as MySQL, Riak, etc. These two interfaces allow the Laravel authentication mechanisms to continue functioning regardless of how the user data is stored or what type of class is used to represent it.

`UserProvider`의 구현체는 MySQL, Riak 등과 같은 저장소를 통해서 `Illuminate\Contracts\Auth\Authenticatable` 구현체를 확인하여 반환하는 역활을 수행합니다. 라라벨 인증 매카니즘은 이 두개의 인터페이스를 통해서 사용자 데이터가 어떻게 저장되어 있는가, 어떤 클래스가 사용자를 나타내고 있는지에 대해서는 관계없이 동작하게 됩니다.

Let's take a look at the `UserProvider` contract:
`UserProvider`contract 를 살펴봅시다.

	interface UserProvider {

		public function retrieveById($identifier);
		public function retrieveByToken($identifier, $token);
		public function updateRememberToken(Authenticatable $user, $token);
		public function retrieveByCredentials(array $credentials);
		public function validateCredentials(Authenticatable $user, array $credentials);

	}

The `retrieveById` function typically receives a numeric key representing the user, such as an auto-incrementing ID from a MySQL database. The `Authenticatable` implementation matching the ID should be retrieved and returned by the method.

`retrieveById`함수는 일반적으로 MySQL 데이터베이스의 auto-increment ID 와 같은 사용자를 나타내는 숫자키를 인자로 받습니다. ID 와 일치하는 `Authenticatable` 구현체를 검색 하여 메소드를 통해서 반환됩니다.

The `retrieveByToken` function retrieves a user by their unique `$identifier` and "remember me" `$token`, stored in a field `remember_token`. As with the previous method, the `Authenticatable` implementation should be returned.

`retrieveByToken` 함수는 고유한 `$identifier` 와 `remember_token` 필드에 저장된 “remember me” `$token` 을 통해서 사용자를 검색합니다. 앞의 메소드와 함께 `Authenticatable`의 구현체가 반환됩니다.

The `updateRememberToken` method updates the `$user` field `remember_token` with the new `$token`. The new token can be either a fresh token, assigned on successfull "remember me" login attempt, or a null when user is logged out.

`updateRememberToken` 메소드는 `$user` 의 `remember_token` 필드를 새로운 `$token` 으로 변경합니다. 새로운 토큰은 "Remember me"로 성공적으로 로그인했을 때 생성되는 새로운 토큰이거나, 또는 사용자가 로그아웃 할 때는 null일 수 있습니다.

The `retrieveByCredentials` method receives the array of credentials passed to the `Auth::attempt` method when attempting to sign into an application. The method should then "query" the underlying persistent storage for the user matching those credentials. Typically, this method will run a query with a "where" condition on `$credentials['username']`. The method should then return an implementation of `UserInterface`. **This method should not attempt to do any password validation or authentication.**

`retrieveByCredentials` 메소드는 어플리케이션에 로그인하기 위해 `Auth::attempt` 메소드에게 전달된 로그인 정보의 배열을 받습니다. 이 메소드는 전달받은 사용자 정보와 일치하는 사용자가  스토리지에 존재하는지 확인하기 위해서 “쿼리-질의”를 수행해야 합니다. 이 방법은 일반적으로 "where" 조건에 `$credentials['username’]` 쿼리를 실행하는 것입니다. 이 메소드는 UserInterface 의 구현을 반환해야합니다. 이 메소드 중에서 암호 검증과 인증을 시도해서는 안됩니다.

The `validateCredentials` method should compare the given `$user` with the `$credentials` to authenticate the user. For example, this method might compare the `$user->getAuthPassword()` string to a `Hash::make` of `$credentials['password']`. This method should only validate the user's credentials and return boolean.

`validateCredentials`메소드는 주어진 `$user` 와 인증하기 위한 `$credentials`를 비교해야 합니다. 예를 들어 이 메소드는 `$user->getAuthPassword()` 값과 `$credentials['password']`를 `Hash::make`한 값과 비교할 것입니다. 이 메소드는 사용자의 인증정보를 검증하여 boolean 값을 반환합니다.

Now that we have explored each of the methods on the `UserProvider`, let's take a look at the `Authenticatable`. Remember, the provider should return implementations of this interface from the `retrieveById` and `retrieveByCredentials` methods:

이제 `UserProvider` 의 메소드를 각각 살펴보았습니다, 이제 `Authenticatable`를 살펴봅시다. 유의 할것은 프로바이더는 `retrieveById` 와 `retrieveByCredentials`의 구현체를 반환해야 한다는 것입니다.

	interface Authenticatable {

		public function getAuthIdentifier();
		public function getAuthPassword();
		public function getRememberToken();
		public function setRememberToken($value);
		public function getRememberTokenName();

	}

This interface is simple. The `getAuthIdentifier` method should return the "primary key" of the user. In a MySQL back-end, again, this would be the auto-incrementing primary key. The `getAuthPassword` should return the user's hashed password. This interface allows the authentication system to work with any User class, regardless of what ORM or storage abstraction layer you are using. By default, Laravel includes a `User` class in the `app` directory which implements this interface, so you may consult this class for an implementation example.

인터페이스는 간단합니다. `getAuthIdentifier` 메소드는 사용자의 “primary key”를 반환 해야합니다. MySQL 를 벡엔드에서 사용하고 있다면, auto-incrementing primary key 가 될 것입니다. `getAuthPassword`메소드는 해쉬 처리된 사용자 패스워드를 반환해야합니다. 이 인터페이스는 어떤 ORM 및 스토리지 추상 클래스를 사용하고 있는지에 관계없이 인증 시스템이 사용자 클래스에 대해 작동하는 것을 가능하게 하고 있습니다. 기본적으로 라라벨은 이 인터페이스를 구현하고있는 `app` 디렉토리의 `User` 클래스를 포함하고 있습니다. 그래서 구현예제로 이 User 클래스를 살펴보십시오.

Finally, once we have implemented the `UserProvider`, we are ready to register our extension with the `Auth` facade:

`UserProvider`의 구현체를 완성하였다면, `Auth` 파사드를 통해서 등록하면 됩니다.

	Auth::extend('riak', function($app)
	{
		return new RiakUserProvider($app['riak.connection']);
	});

After you have registered the driver with the `extend` method, you switch to the new driver in your `config/auth.php` configuration file.

`extend` 메소드를 통해서 드라이버를 등록하고 난후에는, `config/auth.php` 설정 파일에서 새로운 드라이버로 설정을 변경하면 됩니다.

<a name="container-based-extension"></a>
## Service Container Based Extension
## 서비스 컨테이너에 의한 확장

Almost every service provider included with the Laravel framework binds objects into the service container. You can find a list of your application's service providers in the `config/app.php` configuration file. As you have time, you should skim through each of these provider's source code. By doing so, you will gain a much better understanding of what each provider adds to the framework, as well as what keys are used to bind various services into the service container.

라라벨 프레임워크에 포함된 대부분의 서비스 프로바이더는 서비스 컨테이너에 객체를 바인딩 하고 있습니다. `config/app.php` 설정 파일에서 서비스 프로바이더들의 목록을 확인할 수 있습니다. 가능하다면 여기에 나열된 서비스 프로바이더들의 소스 코드를 살펴보십시오. 그렇게 하면 각각의 서비스 프로바이더들이 프레임워크에 어떤 것을 더하고 있는지 또한 서비스 컨테이너에 여러 서비스를 바인딩하기 위해서 어떤 키를 사용하고 있는지 더 잘 알 수 있게 될 것입니다.

For example, the `HashServiceProvider` binds a `hash` key into the service container, which resolves into a `Illuminate\Hashing\BcryptHasher` instance. You can easily extend and override this class within your own application by overriding this binding. For example:

예를 들어 `HashServiceProvider` 는 `Illuminate\Hashing\BcryptHasher`인스턴스를 `hash` 키를 통해서 서비스 컨테이너에 등록합니다. 여러분은 어플리케이션에 바인딩을 재지정 함으로써 손쉽게 이 클래스를 확장하고 재정의 할 수 있습니다. 예를 들어 :

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

Note that this class extends the `HashServiceProvider`, not the default `ServiceProvider` base class. Once you have extended the service provider, swap out the `HashServiceProvider` in your `config/app.php` configuration file with the name of your extended provider.

이 클래스가 `ServiceProvider` 베이스 클래스가 아닌, `HashServiceProvider` 클래스를 상속하고 있다는 것에 주의 하십시오. 서비스 프로바이더를 상속하고 나면 `config/app.php` 설정 파일에서 확장한 `HashServiceProvider` 의 이름을 변경하십시오.

This is the general method of extending any core class that is bound in the container. Essentially every core class is bound in the container in this fashion, and can be overridden. Again, reading through the included framework service providers will familiarize you with where various classes are bound into the container, and what keys they are bound by. This is a great way to learn more about how Laravel is put together.

지금까지 컨테이너를에 바인딩된 코어 클래스를 확장하는 일반적인 방법을 살펴보았습니다. 기본적으로 컨테이너에 바인딩된 모든 코어 클래스는 이러한 방법을 통해서 바인딩을 재지정할 수 있습니다. 다시한번 프레임워크에 포함되어 있는 서비스 프로바이더들과 그 와 관련된 다양한 클래스들이 어떻게 서비스 컨테이너에 바인딩 되는지, 어떤 키를 사용하는지 살펴보시기 바랍니다. 이렇게 하는 것이 라라벨이 어떻게 동작하는지에 대해서 알 수 있는 좋은 방법이 될것입니다.
