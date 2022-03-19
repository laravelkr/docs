# Upgrade Guide
# 업그레이드 가이드

- [Upgrading To 9.0 From 8.x](#upgrade-9.0)
- [8.x에서 9.0으로 업그레이드](#upgrade-9.0)

<a name="high-impact-changes"></a>
## High Impact Changes
## 영향도 높은 변경사항들

<div class="content-list" markdown="1">

- [Updating Dependencies](#updating-dependencies)
- [의존성 업데이트](#updating-dependencies)
- [Flysystem 3.x](#flysystem-3)
- [Flysystem 3.x](#flysystem-3)
- [Symfony Mailer](#symfony-mailer)
- [Symfony 메일러](#symfony-mailer)

</div>

<a name="medium-impact-changes"></a>
## Medium Impact Changes
## 영향도 중간의 변경사항들

<div class="content-list" markdown="1">

- [Belongs To Many `firstOrNew`, `firstOrCreate`, and `updateOrCreate` methods](#belongs-to-many-first-or-new)
- [Belongs To Many `firstOrNew`, `firstOrCreate`, and `updateOrCreate` methods](#belongs-to-many-first-or-new)
- [Custom Casts & `null`](#custom-casts-and-null)
- [Custom Casts & `null`](#custom-casts-and-null)
- [Default HTTP Client Timeout](#http-client-default-timeout)
- [Default HTTP Client Timeout](#http-client-default-timeout)
- [PHP Return Types](#php-return-types)
- [PHP Return Types](#php-return-types)
- [Postgres "Schema" Configuration](#postgres-schema-configuration)
- [Postgres "Schema" Configuration](#postgres-schema-configuration)
- [The `assertDeleted` Method](#the-assert-deleted-method)
- [`assertDeleted` 메소드](#the-assert-deleted-method)
- [The `lang` Directory](#the-lang-directory)
- [`lang` 디렉토리](#the-lang-directory)
- [The `password` Rule](#the-password-rule)
- [`password` 규칙](#the-password-rule)
- [The `when` / `unless` Methods](#when-and-unless-methods)
- [`when` / `unless` 메소드](#when-and-unless-methods)
- [Unvalidated Array Keys](#unvalidated-array-keys)
- [검증되지 않은 배열 키](#unvalidated-array-keys)

</div>

<a name="upgrade-9.0"></a>
## Upgrading To 9.0 From 8.x
## 8.x에서 9.0으로 업그레이드

<a name="estimated-upgrade-time-10-minutes"></a>
#### Estimated Upgrade Time: 30 Minutes
#### 예상되는 업그레이드 시간: 30분

> {tip} We attempt to document every possible breaking change. Since some of these breaking changes are in obscure parts of the framework only a portion of these changes may actually affect your application. Want to save time? You can use [Laravel Shift](https://laravelshift.com/) to help automate your application upgrades.

> {tip} 이전 버전과 호환되지 않는 모든 변경사항을 기록했습니다만 변경사항들 중 일부는 프레임워크의 모호한 부분에 있기 때문에 실제로는 여러분의 어플리케이션에 영향을 끼치지 않을 수도 있습니다. 시간을 절약하고 싶다면 [Laravel Shift](https://laravelshift.com/)와 같은 서비스를 사용할수도 있습니다.  

<a name="updating-dependencies"></a>
### Updating Dependencies
### 의존성 업데이트

**Likelihood Of Impact: High**
**영향 가능성: 높음**

#### PHP 8.0.2 Required
#### PHP 8.0.2 필요

라라벨은 이제 PHP 8.0.2 이상을 필요로 합니다.

#### Composer Dependencies
#### 컴포저 의존성 업데이트

You should update the following dependencies in your application's `composer.json` file:

애플리케이션의 `composer.json` 에서 다음과 같이 의존성을 업데이트 해야합니다.

<div class="content-list" markdown="1">

- `laravel/framework` to `^9.0`
- `nunomaduro/collision` to `^6.1`

</div>

- `laravel/framework` 을 `^9.0` 으로 지정
- `nunomaduro/collision` 을 `^6.1` 으로 지정

In addition, please replace `facade/ignition` with `"spatie/laravel-ignition": "^1.0"` in your application's `composer.json` file.

추가적으로 `composer.json` 에서 `facade/ignition` 을 `"spatie/laravel-ignition": "^1.0"` 으로 교체하십시오.

Furthermore, the following first-party packages have received new major releases to support Laravel 9.x. If applicable, you should read their individual upgrade guides before upgrading:

그리고 다음의 패키지를 사용한다면 라라벨 9.x 를 지원하는 새로운 버전으로 받으십시오. 개별 패키지를 업데이트하기 전에 패키지별로 업그레이드 가이드를 확인하십시오.  

<div class="content-list" markdown="1">

- [Vonage Notification Channel (v3.0)](https://github.com/laravel/vonage-notification-channel/blob/3.x/UPGRADE.md) (Replaces Nexmo)
- [Vonage 알림 채널 (v3.0)](https://github.com/laravel/vonage-notification-channel/blob/3.x/UPGRADE.md) (Nexmo 대체)

</div>

Finally, examine any other third-party packages consumed by your application and verify you are using the proper version for Laravel 9 support.

마지막으로 애플리케이션에서 사용하는 써드파티 패키지가 라라벨 9 를 지원하는지 확인하십시오.

<a name="php-return-types"></a>
#### PHP Return Types
#### PHP 리턴 타입

PHP is beginning to transition to requiring return type definitions on PHP methods such as `offsetGet`, `offsetSet`, etc. In light of this, Laravel 9 has implemented these return types in its code base. Typically, this should not affect user written code; however, if you are overriding one of these methods by extending Laravel's core classes, you will need to add these return types to your own application or package code:

`offsetGet`, `offsetSet`와 같은 PHP 메소드는 리턴타입을 정의하는 형태로 변환되기 시작했습니다. 이에 따라, 라라벨 9에서는 이러한 반환타입을 코드에 구현했습니다. 일반적인 프레임워크 사용자들에게는 영향을 미치지 않습니다. 하지만 라라벨의 핵심 클래스를 확장하여 다음의 메소드 중 하나를 오버라이딩 하였다면, 이러한 반환타입을 애플리케이션이나 패키지에 추가해야합니다.   

<div class="content-list" markdown="1">

- `count(): int`
- `getIterator(): Traversable`
- `getSize(): int`
- `jsonSerialize(): array`
- `offsetExists($key): bool`
- `offsetGet($key): mixed`
- `offsetSet($key, $value): void`
- `offsetUnset($key): void`

</div>

In addition, return types were added to methods implementing PHP's `SessionHandlerInterface`. Again, it is unlikely that this change affects your own application or package code:

추가로 PHP의 `SessionHandlerInterface` 에도 반환타입이 추가되었습니다. 다시말하지만, 이러한 변경사항이 여러분의 애플리케이션이나 패키지 코드에 영향을 끼칠 가능성은 거의 없습니다.

<div class="content-list" markdown="1">

- `open($savePath, $sessionName): bool`
- `close(): bool`
- `read($sessionId): string|false`
- `write($sessionId, $data): bool`
- `destroy($sessionId): bool`
- `gc($lifetime): int`

</div>

<a name="application"></a>
### Application
### 애플리케이션

<a name="the-application-contract"></a>
#### The `Application` Contract
#### `Application` 인터페이스

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `storagePath` method of the `Illuminate\Contracts\Foundation\Application` interface has been updated to accept a `$path` argument. If you are implementing this interface you should update your implementation accordingly:

`Illuminate\Contracts\Foundation\Application` 인터페이스의 `storagePath` 메소드가 `$path` 인자를 전달받을 수 있도록 변경되었습니다. 이 인터페이스를 구현하고 있다면 다음과 같은 구현을 업데이트해야합니다. 

    public function storagePath($path = '');

Similarly, the `langPath` method of the `Illuminate\Foundation\Application` class has been updated to accept a `$path` argument:

마찬가지로 `Illuminate\Foundation\Application` 클래스의 `langPath` 메소드가 `$path` 인자를 전달받을 수 있도록 변경되었습니다.

    public function langPath($path = '');

#### Exception Handler `ignore` Method
#### Exception Handler `ignore` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The exception handler's `ignore` method is now `public` instead of `protected`. This method is not included in the default application skeleton; however, if you have manually defined this method you should update its visibility to `public`:

예외-exception 핸들러의 `ignore` 메소드는 이제 `protected` 대신 `public`이 되었습니다. 이 메소드는 애플리케이션의 기본적인 코드에는 포함되어 있지 않습니다만, 수동으로 이 메소드를 정의하였다면 다음과 같이 `public` 으로 변경해야합니다. 

```php
public function ignore(string $class);
```

### Blade
### 블레이드 템플릿

#### Lazy Collections & The `$loop` Variable
#### 지연 컬렉션 & `$loop` 변수

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

When iterating over a `LazyCollection` instance within a Blade template, the `$loop` variable is no longer available, as accessing this variable causes the entire `LazyCollection` to be loaded into memory, thus rendering the usage of lazy collections pointless in this scenario.

블레이드 템플릿 안에서 `LazyCollection` 인스턴스의 반복문을 처리할 때에는 `$loop` 변수는 더 이상 사용할 수 없습니다. 그 이유는 `$loop` 변수에 접근하게 되면 `LazyCollection` 전체가 메모리에 로딩되기 때문에 지연 컬렉션의 사용 이유가 무색해지기 대문입니다. 

### Collections
### 컬렉션

#### The `Enumerable` Contract
#### `Enumerable` 인터페이스

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `Illuminate\Support\Enumerable` contract now defines a `sole` method. If you are manually implementing this interface, you should update your implementation to reflect this new method:

`Illuminate\Support\Enumerable` 인터페이스가 `sole` 메소드를 정의합니다. 수동으로 이 인터페이스를 구현하였다면 다음의 시그니처를 구현하도록 업데이트 해야합니다.

```php
public function sole($key = null, $operator = null, $value = null);
```

#### The `reduceWithKeys` Method
#### `reduceWithKeys` 메소드

The `reduceWithKeys` method has been removed as the `reduce` method provides the same functionality. You may simply update your code to call `reduce` instead of `reduceWithKeys`.

`reduceWithKeys` 메소드는 제거되고 `reduce` 메소드가 동일한 역할을 수행합니다. `reduceWithKeys` 메소드 대신 `reduce` 메소드를 사용하도록 업데이트 하십시오.

#### The `reduceMany` Method
#### `reduceMany` 메소드

The `reduceMany` method has been renamed to `reduceSpread` for naming consistency with other similar methods.

`reduceSpread` 메소드의 이름이 `reduceMany`으로 변경되었습니다. 

### Container
### 컨테이너

#### The `Container` Contract
#### `Container` 인터페이스

**Likelihood Of Impact: Very Low**
**영향 가능성: 매우 낮음**

The `Illuminate\Contracts\Container\Container` contract has received two method definitions: `scoped` and `scopedIf`. If you are manually implementing this contract, you should update your implementation to reflect these new methods.

`Illuminate\Contracts\Container\Container` 인터페이스에 두개의 메소드 `scoped` 및 `scopedIf`가 추가되었습니다. 수동으로 이 인터페이스를 구현하였다면, 새로운 메소드를 구현하도록 업데이트 하십시오.

#### The `ContextualBindingBuilder` Contract
#### `ContextualBindingBuilder` 인터페이스

**Likelihood Of Impact: Very Low**
**영향 가능성: 매우 낮음**

The `Illuminate\Contracts\Container\ContextualBindingBuilder` contract now defines a `giveConfig` method. If you are manually implementing this interface, you should update your implementation to reflect this new method:

`Illuminate\Contracts\Container\ContextualBindingBuilder` 인터페이스에 `giveConfig` 메소드가 추가되었습니다. 수동으로 이 인터페이스를 구현하였다면 다음의 메소드를 구현하도록 업데이트 하십시오.

```php
public function giveConfig($key, $default = null);
```

### Database
### 데이터베이스

<a name="postgres-schema-configuration"></a>
#### Postgres "Schema" Configuration
#### Postgres "스키마" 설정

**Likelihood Of Impact: Medium**
** 영향 가능성: 중간**

The `schema` configuration option used to configure Postgres connection search paths in your application's `config/database.php` configuration file should be renamed to `search_path`.

애플리케이션의 `config/database.php` 설정파일에서 Postgres 커넥션 검색 경로를 설정하는데 사용하는 `schema` 옵션값은 `search_path` 으로 이름이 변경되었습니다. 

<a name="schema-builder-doctrine-method"></a>
#### Schema Builder `registerCustomDoctrineType` Method
#### 스키마 빌더의 `registerCustomDoctrineType` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `registerCustomDoctrineType` method has been removed from the `Illuminate\Database\Schema\Builder` class. You may use the `registerDoctrineType` method on the `DB` facade instead, or register custom Doctrine types in the `config/database.php` configuration file.

`Illuminate\Database\Schema\Builder` 클래스의 `registerCustomDoctrineType` 메소드가 제거되었습니다. 대신 `DB` 파사드의 `registerDoctrineType` 메소드를 사용하거나 `config/database.php` 설정 파일에서 커스텀 Doctrine 타입을 등록할 수 있습니다. 

### Eloquent
### 엘로퀀트

<a name="custom-casts-and-null"></a>
#### Custom Casts & `null`
#### Custom Casts & `null`

**Likelihood Of Impact: Medium**
** 영향 가능성: 중간**

In previous releases of Laravel, the `set` method of custom cast classes was not invoked if the cast attribute was being set to `null`. However, this behavior was inconsistent with the Laravel documentation. In Laravel 9.x, the `set` method of the cast class will be invoked with `null` as the provided `$value` argument. Therefore, you should ensure your custom casts are able to sufficiently handle this scenario:

라라벨의 이전 버전에서는 커스텀 캐스트 클래스의 `set` 메소드는 캐스트 속성이 `null` 로 지정되는 경우에는 호출되지 않았습니다. 그러나 이 동작은 라라벨 매뉴얼과 일치하지 않는 문제가 있어서 라라벨 9.X 부터는 캐스트 클래스의 메소드에 `$value` 인자가 `null` 로 지정되어 호출됩니다. 따라서 커스텀 캐스트가 의도대로 동작하는지 확인하시길 바랍니다.

```php
/**
 * Prepare the given value for storage.
 *
 * @param  \Illuminate\Database\Eloquent\Model  $model
 * @param  string  $key
 * @param  AddressModel  $value
 * @param  array  $attributes
 * @return array
 */
public function set($model, $key, $value, $attributes)
{
    if (! $value instanceof AddressModel) {
        throw new InvalidArgumentException('The given value is not an Address instance.');
    }

    return [
        'address_line_one' => $value->lineOne,
        'address_line_two' => $value->lineTwo,
    ];
}
```

<a name="belongs-to-many-first-or-new"></a>
#### Belongs To Many `firstOrNew`, `firstOrCreate`, and `updateOrCreate` Methods
#### 다대다 연관관계의 `firstOrNew`, `firstOrCreate`, `updateOrCreate` 메소드

**Likelihood Of Impact: Medium**
** 영향 가능성: 중간**

The `belongsToMany` relationship's `firstOrNew`, `firstOrCreate`, and `updateOrCreate` methods all accept an array of attributes as their first argument. In previous releases of Laravel, this array of attributes was compared against the "pivot" / intermediate table for existing records.

`belongsToMany` 연관관계의 `firstOrNew`, `firstOrCreate`, `updateOrCreate` 메소드는 첫 번째 인자로 속성값을 담은 배열을 인자로 전달받습니다. 이전버전까지는 이 속성값 배열을 기존 레코드의 "피벗" / 중간 테이블과 비교하였습니다.

However, this behavior was unexpected and typically unwanted. Instead, these methods now compare the array of attributes against the table of the related model:

그렇지만, 이 동작이 의도하지 않은 것이었고 일반적으로 원하지 않는 동작이라고 판단하여 연관된 모델 테이블과 속성 배열을 비교하도록 변경되었습니다.

```php
$user->roles()->updateOrCreate([
    'name' => 'Administrator',
]);
```

In addition, the `firstOrCreate` method now accepts a `$values` array as its second argument. This array will be merged with the first argument to the method (`$attributes`) when creating the related model if one does not already exist. This change makes this method consistent with the `firstOrCreate` methods offered by other relationship types:

추가적으로 `firstOrCreate` 메소드는 `$values` 배열을 두 번째 인자로 전달 받을 수 있습니다. 이 배열은 존재하지 않는 연관 모델을 생성할 때 메소드의 첫 번째 인자(`$attributes`)와 머지됩니다. 이러한 변경사항을 통해서 `firstOrCreate` 메소드가 다른 연관관계에서 제공하는 메소드와 동일한 형식을 갖추게 되었습니다. 

```php
$user->roles()->firstOrCreate([
    'name' => 'Administrator',
], [
    'created_by' => $user->id,
]);
```

#### The `touch` Method
#### `touch` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `touch` method now accepts an attribute to touch. If you were previously overwriting this method, you should update your method signature to reflect this new argument:

`touch` 메소드는 이제 동작을 위한 속성값을 인자로 전달 받을 수 있습니다. 이 메소드를 오버라이딩 하는 코드를 작성하였다면 다음의 메소드 시그니처를 구현하도록 업데이트 해야합니다.

```php
public function touch($attribute = null);
```

### Encryption
### 암호화

#### The Encrypter Contract
#### Encrypter 인터페이스

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `Illuminate\Contracts\Encryption\Encrypter` contract now defines a `getKey` method. If you are manually implementing this interface, you should update your implementation accordingly:

`Illuminate\Contracts\Encryption\Encrypter` 인터페이스가 `getKey` 메소드 정의가 추가되었습니다. 이 인터페이스를 수동으로 구현하였다면, 다음의 메소드를 구현하도록 업데이트 해야합니다. 

```php
public function getKey();
```

### Facades
### 파사드

#### `getFacadeAccessor` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `getFacadeAccessor` method must always return a container binding key. In previous releases of Laravel, this method could return an object instance; however, this behavior is no longer supported. If you have written your own facades, you should ensure that this method returns a container binding string:

`getFacadeAccessor` 메소드는 항상 컨테이너 바인딩 키를 반환해야합니다. 이전 버전까지는 이 메소드는 객체 인스턴스를 반환할 수 있었습니다. 하지만 이런 동작은 더 이상 지원하지 않습니다. 만약 파사드를 구현하였고 객체를 반환하는 방식을 사용하였다면 컨테이너 바인딩 문자열을 반환하도록 업데이트 해야합니다. 

```php
/**
 * Get the registered name of the component.
 *
 * @return string
 */
protected static function getFacadeAccessor()
{
    return Example::class;
}
```

### Filesystem
### 파일시스템

#### The `FILESYSTEM_DRIVER` Environment Variable
#### `FILESYSTEM_DRIVER` 환경변수

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `FILESYSTEM_DRIVER` environment variable has been renamed to `FILESYSTEM_DISK` to more accurately reflect its usage. This change only affects the application skeleton; however, you are welcome to update your own application's environment variables to reflect this change if you wish.

보다 정확한 의미전달을 위해서 `FILESYSTEM_DRIVER` 환경 변수가 `FILESYSTEM_DISK` 으로 이름이 변경되었습니다. 이 변경사항은 애플리케이션 스켈레톤에만 영향을 줍니다. 원한다면 애플리케이션에 이 변경사항을 적용하여 업데이트 하십시오. 

#### The "Cloud" Disk
#### "Cloud" 디스크

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `cloud` disk configuration option was removed from the default application skeleton in November of 2020. This change only affects the application skeleton. If you are using the `cloud` disk within your application, you should leave this configuration value in your own application's skeleton.

2020년 11월 기본 애플리케이션의 스켈레톤에서 `cloud` 디스크 설정 옵션이 제거되었습니다. 이 변경사항은 애플리케이션 스켈레톤에만 영향을 미칩니다. 애플리케이션에서 `cloud` 디스크를 사용하는 경우에는 이 설정 값을 그대로 두시기 바랍니다. 

<a name="flysystem-3"></a>
### Flysystem 3.x
### Flysystem 3.x

**Likelihood Of Impact: High**
**영향 가능성: 높음**

Laravel 9.x has migrated from [Flysystem](https://flysystem.thephpleague.com/v2/docs/) 1.x to 3.x. Under the hood, Flysystem powers all of the file manipulation methods provided by the `Storage` facade. In light of this, some changes may be required within your application; however, we have tried to make this transition as seamless as possible.

라라벨 9.x 에서는 [Flysystem](https://flysystem.thephpleague.com/v2/docs/)을 1.x 에서 3.x으로 마이그레이션하였습니다. 내부적으로 Flysystem은 `Storage` 파사드에서 제공하는 파일을 다루는 모든 메소드를 제공합니다. Flysystem의 버전 변경으로 인해서 애플리케이션의 일부 코드가 영향을 받을 수도 있습니다. 라라벨 9 에서는 이러한 영향이 없게끔 원활한 작업을 지원하도록 노력하였습니다.

#### Driver Prerequisites
#### 드라이버 필요사항

Before using the S3, FTP, or SFTP drivers, you will need to install the appropriate package via the Composer package manager:

S3, FTP, SFTP 드라이버를 사용하기 전에 다음의 컴포저 패키지를 설치해야합니다.

- Amazon S3: `composer require -W league/flysystem-aws-s3-v3 "^3.0"`
- FTP: `composer require league/flysystem-ftp "^3.0"`
- SFTP: `composer require league/flysystem-sftp-v3 "^3.0"`

#### Overwriting Existing Files
#### 기존에 존재하는 파일 덮어쓰기

Write operations such as `put`, `write`, and `writeStream` now overwrite existing files by default. If you do not want to overwrite existing files, you should manually check for the file's existence before performing the write operation.

`put`, `write`, `writeStream`와 같은 쓰기 작업은 이제 기본적으로 기존 파일을 덮어쓰는 형태로 동작합니다. 기존 파일을 덮어쓰지 않으려면 쓰기 작업을 수행하기 전에 파일의 존재를 수동으로 확인해야 합니다.

#### Write Exceptions
#### 쓰기에서 예외 발생

Write operations such as `put`, `write`, and `writeStream` no longer throw an exception when a write operation fails. Instead, `false` is returned. If you would like to preserve the previous behavior which threw exceptions, you may define the `throw` option within a filesystem disk's configuration array:

`put`, `write`, `writeStream`와 같은 쓰기 작업은 더 이상 작업 실패시에 예외를 발생시키지 않습니다. 대신 `false` 가 반환됩니다. 이전처럼 작업에 실패하였을 때 예외가 발생하길 원한다면 다음과 같이 파일시스템 디스크 설정 배열의 `throw` 옵션을 정의하면 됩니다.

```php
'public' => [
    'driver' => 'local',
    // ...
    'throw' => true,
],
```

#### Reading Missing Files
#### 찾을 수 없는 파일 읽기

Attempting to read from a file that does not exist now returns `null`. In previous releases of Laravel, an `Illuminate\Contracts\Filesystem\FileNotFoundException` would have been thrown.

찾을 수 없는 파일을 읽으려는 시도가 있는 경우 `null` 이 반환됩니다. 라라벨의 이전 버전까지는 `Illuminate\Contracts\Filesystem\FileNotFoundException`가 발생했습니다.

#### Deleting Missing Files
#### 찾을 수 없는 파일 삭제

Attempting to `delete` a file that does not exist now returns `true`.

찾을 수 없는 파일에 대해서 `delete` 메소드를 실행하면 `true`가 반환됩니다.

#### Cached Adapters
#### 캐싱된 어댑터

Flysystem no longer supports "cached adapters". Thus, they have been removed from Laravel and any relevant configuration (such as the `cache` key within disk configurations) can be removed.

Flysystem은 "캐싱된 어댑터"를 더이상 지원하지 않습니다. 라라벨에서 관련 기능이 제거되었기 때문에 연관된 설정 내용 (디스크 설정 파일에서 `cache` 키)도 제거되었습니다.

#### Custom Filesystems
#### 커스텀 파일시스템

Slight changes have been made to the steps required to register custom filesystem drivers. Therefore, if you were defining your own custom filesystem drivers, or using packages that define custom drivers, you should update your code and dependencies.

커스텀 파일시스템을 등록하는데 필요한 단계가 약간 변경되었습니다. 따라서 커스텀 파일시스템 드라이버를 정의하거나 커스텀 드라이버를 정의하는 패키지를 사용한 경우 관련 코드와 의존성을 업데이트 해야합니다.

For example, in Laravel 8.x, a custom filesystem driver might be registered like so:

예를 들어, 라라벨 8.x 에서는 커스텀 파일시스템 드라이버는 다음과 같이 등록하였습니다.

```php
use Illuminate\Support\Facades\Storage;
use League\Flysystem\Filesystem;
use Spatie\Dropbox\Client as DropboxClient;
use Spatie\FlysystemDropbox\DropboxAdapter;

Storage::extend('dropbox', function ($app, $config) {
    $client = new DropboxClient(
        $config['authorization_token']
    );

    return new Filesystem(new DropboxAdapter($client));
});
```

However, in Laravel 9.x, the callback given to the `Storage::extend` method should return an instance of `Illuminate\Filesystem\FilesystemAdapter` directly:

그렇지만 라라벨 9.x 부터는 `Storage::extend` 메소드에 전달되는 콜백은 `Illuminate\Filesystem\FilesystemAdapter` 타입의 인스턴스를 반환해야합니다.

```php
use Illuminate\Filesystem\FilesystemAdapter;
use Illuminate\Support\Facades\Storage;
use League\Flysystem\Filesystem;
use Spatie\Dropbox\Client as DropboxClient;
use Spatie\FlysystemDropbox\DropboxAdapter;

Storage::extend('dropbox', function ($app, $config) {
    $adapter = new DropboxAdapter(
        new DropboxClient($config['authorization_token'])
    );

    return new FilesystemAdapter(
        new Filesystem($adapter, $config),
        $adapter,
        $config
    );
});
```

### Helpers
### 헬퍼함수

<a name="data-get-function"></a>
#### The `data_get` Helper & Iterable Objects
#### `data_get` 헬퍼함수 & 반복 가능한 객체

**Likelihood Of Impact: Very Low**
**영향 가능성: 매우 낮음**

Previously, the `data_get` helper could be used to retrieve nested data on arrays and `Collection` instances; however, this helper can now retrieve nested data on all iterable objects.

이전까지는 `data_get` 헬퍼함수를 사용하여 배열이나 `컬렉션` 인스턴스에서만 중첩된 데이터를 조회하는데 사용이 가능했습니다. 하지만 이 헬퍼는 모든 반복가능한 객체에서 중첩된 데이터의 조회가 가능합니다.  

<a name="str-function"></a>
#### The `str` Helper
#### `str` 헬퍼함수

**Likelihood Of Impact: Very Low**
**영향 가능성: 매우 낮음**

Laravel 9.x now includes a global `str` [helper function](/docs/{{version}}/helpers#method-str). If you are defining a global `str` helper in your application, you should rename or remove it so that it does not conflict with Laravel's own `str` helper.

라라벨 9.x 에서는 글로벌 `str` [헬퍼함수](/docs/{{version}}/helpers#method-str)가 포함되어 있습니다. 애플리케이션에서 별도로 `str` 헬퍼를 정의하였다면 라라벨의 `str` 헬퍼함수와 충돌하지 않도록 이름을 변경하거나 제거해야합니다. 

<a name="when-and-unless-methods"></a>
#### The `when` / `unless` Methods
#### `when` / `unless` 메소드

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

As you may know, `when` and `unless` methods are offered by various classes throughout the framework. These methods can be used to conditionally perform an action if the boolean value of the first argument to the method evaluates to `true` or `false`:

`when` 과 `unless` 메소드는 프레임워크 전반에 걸쳐서 다양한 클레스에서 사용됩니다. 이 메소드는 첫 번째 인자로 전달되는 불리언값(true / false)에 따라서 선택적으로 액션을 수행하는 용도로 사용됩니다.

```php
$collection->when(true, function ($collection) {
    $collection->merge([1, 2, 3]);
});
```

Therefore, in previous releases of Laravel, passing a closure to the `when` or `unless` methods meant that the conditional operation would always execute, since a loose comparison against a closure object (or any other object) always evaluates to `true`. This often led to unexpected outcomes because developers expect the **result** of the closure to be used as the boolean value that determines if the conditional action executes.

그래서, 이전버전까지의 라라벨에서는 첫 번째 인자로 클로저를 전달할 때 이 클로저가(그리고 다른 객체들이) PHP 언어의 느슨한 비교 연산을 통해서 `true`로 간주되어서 액션이 항상 수행되었습니다. 개발자는 클로저의 실행 **결과**에 따라서 액션이 실행되기를 기대하기 때문에 종종 의도하지 않은 결과를 발생시켰습니다.   

So, in Laravel 9.x, any closures passed to the `when` or `unless` methods will be executed and the value returned by the closure will be considered the boolean value used by the `when` and `unless` methods:

따라서 라라벨 9.X 부터는 `when` 이나 `unless` 메소드에 전달되는 클로저는 클로저의 실행결과 값에 따라서 이후의 액션이 실행되도록 변경되었습니다.

```php
$collection->when(function ($collection) {
    // This closure is executed...
    return false;
}, function ($collection) {
    // Not executed since first closure returned "false"...
    $collection->merge([1, 2, 3]);
});
```

### HTTP Client
### HTTP Client

<a name="http-client-default-timeout"></a>
#### Default Timeout
#### Timeout 기본값

**Likelihood Of Impact: Medium**
**영향 가능성: 중간**

The [HTTP client](/docs/{{version}}/http-client) now has a default timeout of 30 seconds. In other words, if the server does not respond within 30 seconds, an exception will be thrown. Previously, no default timeout length was configured on the HTTP client, causing requests to sometimes "hang" indefinitely.

[HTTP client](/docs/{{version}}/http-client)는 이제 타임아웃 기본값으로 30초를 지정합니다. 다시말해 서버가 30초 이내에 응답하지 않으면 예외가 발생합니다. 이전까지는 타임아웃 기본값이 설정되어 있지 않아서 때대로 요청이 계속 유지되는 문제가 있었습니다.

If you wish to specify a longer timeout for a given request, you may do so using the `timeout` method:

다른 서버로 요청을 보낼 때 기본값 보다 더 긴 타임아웃을 지정하려면 `timeout` 메소드를 사용하면 됩니다.

    $response = Http::timeout(120)->get(...);

#### HTTP Fake & Middleware
#### HTTP Fake & 미들웨어

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

Previously, Laravel would not execute any provided Guzzle HTTP middleware when the [HTTP client](/docs/{{version}}/http-client) was "faked". However, in Laravel 9.x, Guzzle HTTP middleware will be executed even when the HTTP client is faked.

이전버전에서는 라라벨이 [HTTP client](/docs/{{version}}/http-client)가 "fake"로 취급될 때 제공된 Guzzle HTTP 미들웨어를 실행하지 않았습니다. 하지만 라라벨 9.x 부터는 Guzzle HTTP 미들웨어가 실행됩니다. 

#### HTTP Fake & Dependency Injection
#### HTTP Fake & 의존성 주입

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

In previous releases of Laravel, invoking the `Http::fake()` method would not affect instances of the `Illuminate\Http\Client\Factory` that were injected into class constructors. However, in Laravel 9.x, `Http::fake()` will ensure fake responses are returned by HTTP clients injected into other services via dependency injection. This behavior is more consistent with the behavior of other facades and fakes.

라라벨의 이전 릴리즈에서는 `Http::fake()` 메소드를 호출해도 메소드를 호출해도 클래스 생성자에 주입된 `Illuminate\Http\Client\Factory` 인스턴스에 영향을 미치지 않았습니다. 하지만 라라벨 9.x 에서 `Http::fake()`는 의존성 주입을 통해 다른 서비스에 주입된 HTTP 클라이언트에 의해서 Fake 응답이 반환되도록 동작합니다. 이 동작은 다른 파사드와 Fake 에서도 동일합니다. 

<a name="symfony-mailer"></a>
### Symfony Mailer
### Symfony 메일러

**Likelihood Of Impact: High**
**영향 가능성: 높음**

One of the largest changes in Laravel 9.x is the transition from SwiftMailer, which is no longer maintained as of December 2021, to Symfony Mailer. However, we have tried to make this transition as seamless as possible for your applications. That being said, please thoroughly review the list of changes below to ensure your application is fully compatible.

라라벨 9.x의 가장 큰 변경사항 중 하나는 2021년 12월 현재 더 이상 관리되고 있지 않은 SwiftMailer를 대체하여 Symfony 메일러를 도입한 것입니다. 애플리케이션에서 가능한 문제없이 활용될 수 있도록 노력하였습니다만 아래의 변경사항 목록을 확인하여 애플리케이션에 호환되는지 확인하시기 바랍니다.

#### Driver Prerequisites
#### 드라이버 사전 준비사항

To continue using the Mailgun transport, your application should require the `symfony/mailgun-mailer` and `symfony/http-client` Composer packages:

Mailgun을 통해서 이메일을 전송하려면 애플리케이션에 `symfony/mailgun-mailer` 및 `symfony/http-client` 컴포저 패키지를 설치해야합니다.

```shell
composer require symfony/mailgun-mailer symfony/http-client
```

The `wildbit/swiftmailer-postmark` Composer package should be removed from your application. Instead, your application should require the `symfony/postmark-mailer` and `symfony/http-client` Composer packages:

애플리케이션에서 `wildbit/swiftmailer-postmark` 컴포저 패키지는 삭제해야합니다. 대신 `symfony/postmark-mailer` 및 `symfony/http-client` 컴포저 패키지가 필요합니다.

```shell
composer require symfony/postmark-mailer symfony/http-client
```

#### Updated Return Types
#### 반환 타입 업데이트

The `send`, `html`, `text`, and `plain` methods no longer return the number of recipients that received the message. Instead, an instance of `Illuminate\Mail\SentMessage` is returned. This object contains an instance of `Symfony\Component\Mailer\SentMessage` that is accessible via the `getSymfonySentMessage` method or by dynamically invoking methods on the object.

`send`, `html`, `text`, `plain` 메소드는 더 이상 메세지를 수신한 수신자의 수를 반환하지 않습니다. 그대신 `Illuminate\Mail\SentMessage` 인스턴스가 반환됩니다. 이 객체는 `getSymfonySentMessage` 메소드 또는 객체에 동적 메소드 호출을 사용해서 접근할 수 있는 `Symfony\Component\Mailer\SentMessage` 인스턴스를 포함하고 있습니다.  

#### Renamed "Swift" Methods
#### "Swift" 메소드의 이름 변경

Various SwiftMailer related methods, some of which were undocumented, have been renamed to their Symfony Mailer counterparts. For example, the `withSwiftMessage` method has been renamed to `withSymfonyMessage`:

매뉴얼에 기록되지 않은 다양한 SwiftMailer 관련 메소드는 Symfony Mailer 를 사용한 방식으로 이름이 변경되었습니다. 예를 들어 `withSwiftMessage` 메소드는 `withSymfonyMessage`로 이름이 변경되었습니다.

    // Laravel 8.x...
    $this->withSwiftMessage(function ($message) {
        $message->getHeaders()->addTextHeader(
            'Custom-Header', 'Header Value'
        );
    });

    // Laravel 9.x...
    use Symfony\Component\Mime\Email;

    $this->withSymfonyMessage(function (Email $message) {
        $message->getHeaders()->addTextHeader(
            'Custom-Header', 'Header Value'
        );
    });

> {note} Please thoroughly review the [Symfony Mailer documentation](https://symfony.com/doc/6.0/mailer.html#creating-sending-messages) for all possible interactions with the `Symfony\Component\Mime\Email` object.

> {note} `Symfony\Component\Mime\Email`에서 사용가능한 작업들은 [Symfony 메일러 매뉴얼](https://symfony.com/doc/6.0/mailer.html#creating-sending-messages)을 참고하십시오.

The list below contains a more thorough overview of renamed methods. Many of these methods are low-level methods used to interact with SwiftMailer / Symfony Mailer directly, so may not be commonly used within most Laravel applications:

다음의 목록에서 이름이 변경된 메소드를 확인할 수 있습니다. 이 메소드 중 다수는 SwiftMailer / Symfony Mailer와 직접 인터렉션하는 메소드입니다. 대부분은 저수준의 메소드이므로 라라벨 애플리케이션에서는 일반적으로 사용되지 않을 수 있습니다.

    Message::getSwiftMessage();
    Message::getSymfonyMessage();

    Mailable::withSwiftMessage($callback);
    Mailable::withSymfonyMessage($callback);

    MailMessage::withSwiftMessage($callback);
    MailMessage::withSymfonyMessage($callback);

    Mailer::getSwiftMailer();
    Mailer::getSymfonyTransport();

    Mailer::setSwiftMailer($swift);
    Mailer::setSymfonyTransport(TransportInterface $transport);

    MailManager::createTransport($config);
    MailManager::createSymfonyTransport($config);

#### Proxied `Illuminate\Mail\Message` Methods
#### 프록시 처리된 `Illuminate\Mail\Message` 메소드

The `Illuminate\Mail\Message` typically proxied missing methods to the underlying `Swift_Message` instance. However, missing methods are now proxied to an instance of `Symfony\Component\Mime\Email` instead. So, any code that was previously relying on missing methods to be proxied to SwiftMailer should be updated to their corresponding Symfony Mailer counterparts.

`Illuminate\Mail\Message`는 일반적으로 기본 `Swift_Message` 인스턴에 누락된 메소드를 프록시처리해서 제공했습니다. 하지만 누락된 메소드는 새로운 `Symfony\Component\Mime\Email` 인스턴스에 연결됩니다. 따라서 이전에 누락된 메소드를 사용하여 SwiftMailer로 처리하던 코드는 Symfony Mailer를 사용하도록 업데이트 해야합니다.

Again, many applications may not be interacting with these methods, as they are not documented within the Laravel documentation:

다시말하지만, 라라벨 매뉴얼에 별도로 기재되어 있지 않기 때문에 애플리케이션에서 기존에 이런 메소드를 사용하지 않았을 수도 있습니다. 

    // Laravel 8.x...
    $message
        ->setFrom('taylor@laravel.com')
        ->setTo('example@example.org')
        ->setSubject('Order Shipped')
        ->setBody('<h1>HTML</h1>', 'text/html')
        ->addPart('Plain Text', 'text/plain');

    // Laravel 9.x...
    $message
        ->from('taylor@laravel.com')
        ->to('example@example.org')
        ->subject('Order Shipped')
        ->html('<h1>HTML</h1>')
        ->text('Plain Text');

#### Generated Messages IDs
#### 생성된 메세지 ID

SwiftMailer offered the ability to define a custom domain to include in generated Message IDs via the `mime.idgenerator.idright` configuration option. This is not supported by Symfony Mailer. Instead, Symfony Mailer will automatically generate a Message ID based on the sender.

SwiftMailer는 `mime.idgenerator.idright` 설정 옵션값을 사용하여 생성된 메시지 ID에 포함할 커스텀 도메인을 정의하는 기능을 제공했습니다. 이 기능은 Symfony Mailer에서는 지원되지 않습니다. 대신 Symfony Mailer는 발신자를 기반으로 메시지 ID를 자동으로 생성합니다.

#### Forced Reconnections
#### 강제 재연결

It is no longer possible to force a transport reconnection (for example when the mailer is running via a daemon process). Instead, Symfony Mailer will attempt to reconnect to the transport automatically and throw an exception if the reconnection fails.

더 이상 강제로 전송 재연결을 수행할 수 없습니다(예: 메일러가 데몬 프로세스를 통해 실행 중인 경우). 대신 Symfony Mailer는 전송 과정에서 자동으로 재연결을 시도하고 재연결이 실패하면 예외를 발생시킵니다.

#### SMTP Stream Options
#### SMTP 스트림 옵션

Defining stream options for the SMTP transport is no longer supported. Instead, you must define the relevant options directly within the configuration if they are supported. For example, to disable TLS peer verification:

SMTP 전송에 대한 스트림 옵션을 정의하는 것은 더 이상 지원되지 않습니다. 대신에 연관된 옵션을 설정 파일안에서 직접 정의해야합니다. 예를 들어 TLS 피어 확인을 비활성화하려면 다음과 같이 설정하면 됩니다.

    'smtp' => [
        // Laravel 8.x...
        'stream' => [
            'ssl' => [
                'verify_peer' => false,
            ],
        ],

        // Laravel 9.x...
        'verify_peer' => false,
    ],

To learn more about the available configuration options, please review the [Symfony Mailer documentation](https://symfony.com/doc/6.0/mailer.html#transport-setup).

사용할 수 있는 설정 옵션에 대해서는 [Symfony Mailer 매뉴얼](https://symfony.com/doc/6.0/mailer.html#transport-setup)을 참고하십시오.

> {note} In spite of the example above, you are not generally advised to disable SSL verification since it introduces the possibility of "man-in-the-middle" attacks.

> {note} 위의 예시와 같이 SSL 확인을 비활성화하는 것은 "중간자" 공격의 가능성이 있으므로 일반적으로 사용하지 않는 것이 좋습니다.

#### SMTP `auth_mode`
#### SMTP `auth_mode`

Defining the SMTP `auth_mode` in the `mail` configuration file is no longer required. The authentication mode will be automatically negotiated between Symfony Mailer and the SMTP server.

`mail` 설정파일안에서 SMTP `auth_mode`를 정의할 필요가 없습니다. 인증 모드는 Symfony Mailer와 SMTP 서버 간에 자동으로 수행됩니다.

#### Failed Recipients
#### 전송에 실패한 수신자

It is no longer possible to retrieve a list of failed recipients after sending a message. Instead, a `Symfony\Component\Mailer\Exception\TransportExceptionInterface` exception will be thrown if a message fails to send. Instead of relying on retrieving invalid email addresses after sending a message, we recommend that you validate email addresses before sending the message instead.

메세지를 발송한 뒤에 전송에 실패한 수신자 목록을 조회할 수 없습니다. 대신 메세지 발송이 실패하면 `Symfony\Component\Mailer\Exception\TransportExceptionInterface` 예외가 발생합니다. 메시지를 보낸 후 실패한 수신자 이메일 주소를 조회하는 대신 메시지를 보내기 전에 이메일 주소를 확인하는 것이 좋습니다.

### Packages
### 패키지

<a name="the-lang-directory"></a>
#### The `lang` Directory
#### `lang` 디렉토리

**Likelihood Of Impact: Medium**
** 영향 가능성: 중간**

In new Laravel applications, the `resources/lang` directory is now located in the root project directory (`lang`). If your package is publishing language files to this directory, you should ensure that your package is publishing to `app()->langPath()` instead of a hard-coded path.

새로운 라라벨 애플리케이션에서 `resources/lang` 디렉토리는 이제 프로젝트의 루트 디렉토리(`lang`)로 옮겨졌습니다. 패키지가 이 디렉토리에 언어 파일을 작성하는 경우 패키지가 하드 코딩된 경로 대신 `app()->langPath()`를 통해서 파일을 작성하는지 확인해야 합니다.

<a name="queue"></a>
### Queue
### Queue-큐

<a name="the-opis-closure-library"></a>
#### The `opis/closure` Library
#### `opis/closure` 라이브러리

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

Laravel's dependency on `opis/closure` has been replaced by `laravel/serializable-closure`. This should not cause any breaking change in your application unless you are interacting with the `opis/closure` library directly. In addition, the previously deprecated `Illuminate\Queue\SerializableClosureFactory` and `Illuminate\Queue\SerializableClosure` classes have been removed. If you are interacting with `opis/closure` library directly or using any of the removed classes, you may use [Laravel Serializable Closure](https://github.com/laravel/serializable-closure) instead.

라라벨의 `opis/closure` 의존성은 `laravel/serializable-closure`으로 대체되었습니다. `opis/closure` 라이브러리를 직접사용한 경우가 아니라면 애플리케이션에는 별다른 영향이 없을 것입니다. 추가적으로 이전에 지원중단된 `Illuminate\Queue\SerializableClosureFactory` 및 `Illuminate\Queue\SerializableClosure` 클래스가 제거되었습니다. `opis/closure` 라이브러리를 직접 사용하거나 제거된 클래스를 사용하였다면 [Laravel Serializable Closure](https://github.com/laravel/serializable-closure)를 대신 사용할 수 있습니다.

#### The Failed Job Provider `flush` Method
#### 실패한 작업 제공자의 `flush` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `flush` method defined by the `Illuminate\Queue\Failed\FailedJobProviderInterface` interface now accepts an `$hours` argument which determines how old a failed job must be (in hours) before it is flushed by the `queue:flush` command. If you are manually implementing the `FailedJobProviderInterface` you should ensure that your implementation is updated to reflect this new argument:

`Illuminate\Queue\Failed\FailedJobProviderInterface` 인터페이스의 `flush` 메소드는 이제 실패한 작업이 `queue:flush`에 의해서 삭제되기 전에 얼마나 오래 유지되어야 하는지(시간) `$hours` 인자로 결정합니다. 만약 수동으로 `FailedJobProviderInterface` 인터페이스를 구현하였다면 변경된 인자를 전달받도록 업데이트 해야합니다. 

```php
public function flush($hours = null);
```

### Session
### 세션

#### The `getSession` Method
#### `getSession` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `Symfony\Component\HttpFoundaton\Request` class that is extended by Laravel's own `Illuminate\Http\Request` class offers a `getSession` method to get the current session storage handler. This method is not documented by Laravel as most Laravel applications interact with the session through Laravel's own `session` method.

라라벨은 `Symfony\Component\HttpFoundaton\Request` 클래스를 확장한 `Illuminate\Http\Request`을 사용합니다. 이 클래스는 현재 세션 스토리지 핸들러를 가져오기 위한 `getSession` 메소드를 제공합니다. 대부분의 라라벨 애플리케이션은 `session` 메소드를 통해 세션과 상호 작용하기 때문에 이 메소드는 별도로 문서에 기록되지 않았습니다.

The `getSession` method previously returned an instance of `Illuminate\Session\Store` or `null`; however, due to the Symfony 6.x release enforcing a return type of `Symfony\Component\HttpFoundation\Session\SessionInterface`, the `getSession` now correctly returns a `SessionInterface` implementation or throws an `\Symfony\Component\HttpFoundation\Exception\SessionNotFoundException` exception when no session is available.

`getSession` 메소드는 이전까지 `Illuminate\Session\Store` 인스턴스 또는 `null`을 반환했습니다. 하지만 Symfony 6.x 릴리즈 부터 `Symfony\Component\HttpFoundation\Session\SessionInterface` 인스턴스를 반환하기 때문에 `getSession` 메소드도 일치하는 `SessionInterface` 구현체를 반환하거나 세션을 사용할 수 없는 경우 `\Symfony\Component\HttpFoundation\Exception\SessionNotFoundException` 예외를 발생시킵니다.

### Testing
### 테스팅

<a name="the-assert-deleted-method"></a>
#### The `assertDeleted` Method
#### `assertDeleted` 메소드

**Likelihood Of Impact: Medium**
** 영향 가능성: 중간**

All calls to the `assertDeleted` method should be updated to `assertModelMissing`.

`assertDeleted` 메소드를 `assertModelMissing` 으로 변경하십시오.

### Trusted Proxies
### 신뢰할 수 있는 프록시

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

If you are upgrading your Laravel 8 project to Laravel 9 by importing your existing application code into a totally new Laravel 9 application skeleton, you may need to update your application's "trusted proxy" middleware.

기존의 라라벨 8 애플리케이션 코드를 완전히 새로운 라라벨 9 애플리케이션 스켈레톤으로 가져와 업그레이드하는 경우라면, 애플리케이션의 "신뢰할 수 있는 프록시" 미들웨어 설정을 변경해야합니다.

Within your `app/Http/Middleware/TrustProxies.php` file, update `use Fideloper\Proxy\TrustProxies as Middleware` to `use Illuminate\Http\Middleware\TrustProxies as Middleware`.

`app/Http/Middleware/TrustProxies.php` 파일 안에서, `use Fideloper\Proxy\TrustProxies as Middleware` 코드를 `use Illuminate\Http\Middleware\TrustProxies as Middleware` 으로 변경하십시오.

Next, within `app/Http/Middleware/TrustProxies.php`, you should update the `$headers` property definition:

다음으로 `app/Http/Middleware/TrustProxies.php` 파일 안에서, `$headers` 속성의 정의를 다음과 같이 변경해야합니다.

```php
// Before...
protected $headers = Request::HEADER_X_FORWARDED_ALL;

// After...
protected $headers =
    Request::HEADER_X_FORWARDED_FOR |
    Request::HEADER_X_FORWARDED_HOST |
    Request::HEADER_X_FORWARDED_PORT |
    Request::HEADER_X_FORWARDED_PROTO |
    Request::HEADER_X_FORWARDED_AWS_ELB;
```

Finally, you can remove the `fideloper/proxy` Composer dependency from your application:

마지막으로 `fideloper/proxy` 컴포저 의존성을 제거하십시오.

```shell
composer remove fideloper/proxy
```

### Validation
### 유효성 검사

#### Form Request `validated` Method
#### Form Request 객체의 `validated` 메소드

**Likelihood Of Impact: Low**
**영향 가능성: 낮음**

The `validated` method offered by form requests now accepts `$key` and `$default` arguments. If you are manually overwriting the definition of this method, you should update your method's signature to reflect these new arguments:

form request 객체의 `validated` 메소드는 이제 `$key` 와 `$default` 인자를 전달받습니다. 이 메소드를 수동으로 오버라이딩해서 사용중이라면, 다음의 시그니처를 반영하도록 업데이트해야합니다.

```php
public function validated($key = null, $default = null)
```

<a name="the-password-rule"></a>
#### The `password` Rule
#### `password` 규칙

**Likelihood Of Impact: Medium**
** 영향 가능성: 중간**

The `password` rule, which validates that the given input value matches the authenticated user's current password, has been renamed to `current_password`.

주어진 입력값이 사용자의 현재 비밀번호와 일치하는지 확인할 수 있는 `password` 규칙이 `current_password`으로 이름이 변경되었습니다.

<a name="unvalidated-array-keys"></a>
#### Unvalidated Array Keys
#### 검증되지 않은 배열 키

**Likelihood Of Impact: Medium**
** 영향 가능성: 중간**

In previous releases of Laravel, you were required to manually instruct Laravel's validator to exclude unvalidated array keys from the "validated" data it returns, especially in combination with an `array` rule that does not specify a list of allowed keys.

이전 버전의 라라벨에서는 입력된 필드의 값이 배열인지 확인하는 `array` 유효성 검사 규칙을 지정할 때 유효성 검사가 필요하지 않은 "검증되지 않는 배열키"를 제외하려면 직접 라라벨의 Validator 를 조작해야만 했습니다.   

However, in Laravel 9.x, unvalidated array keys are always excluded from the "validated" data even when no allowed keys have been specified via the `array` rule. Typically, this behavior is the most expected behavior and the previous `excludeUnvalidatedArrayKeys` method was only added to Laravel 8.x as a temporary measure in order to preserve backwards compatibility.

라라벨 9.x 에서는 `array` 유효성 검사 규칙을 확인할 때 반드시 필요한 키가 지정되지 않은 경우에도 항상 "요휴성 검사를 확인하는" 데이터에서 제외됩니다. 일반적으로 이 동작은 이전버전과의 호환성을 위해서 라라벨 8.x 에서 임시로 추가된 `excludeUnvalidatedArrayKeys` 메서드와 관련있습니다.  

Although it is not recommended, you may opt-in to the previous Laravel 8.x behavior by invoking a new `includeUnvalidatedArrayKeys` method within the `boot` method of one of your application's service providers:

권장하지는 않지만, 애플리케이션 서비스 프로바이더의 `boot` 메소드에서 `includeUnvalidatedArrayKeys` 메소드를 호출하여 이전버전인 라라벨 8.x 와 동일한 동작을 하도록 설정할 수 있습니다.

```php
use Illuminate\Support\Facades\Validator;

/**
 * Register any application services.
 *
 * @return void
 */
public function boot()
{
    Validator::includeUnvalidatedArrayKeys();
}
```

<a name="miscellaneous"></a>
### Miscellaneous
### 기타사항

We also encourage you to view the changes in the `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel). While many of these changes are not required, you may wish to keep these files in sync with your application. Some of these changes will be covered in this upgrade guide, but others, such as changes to configuration files or comments, will not be. You can easily view the changes with the [GitHub comparison tool](https://github.com/laravel/laravel/compare/8.x...9.x) and choose which updates are important to you.

우리는 또한 `laravel/laravel` [GitHub 저장소](https://github.com/laravel/laravel)에서 변경사항들을 확인해보시길 권합니다. 변경사항들 중 많은 부분들은 필수는 아니지만, 업데이트 된 파일들을 여러분의 어플리케이션에 동기화 해두는게 좋습니다. 변경사항들 중 일부는 이 업그레이드 가이드에서 설명되었지만 설정 파일이나 코멘트같은 다른 변경사항들은 그렇지 않았습니다. [GitHub comparison tool](https://github.com/laravel/laravel/compare/8.x...9.x)을 이용해 변경사항들을 쉽게 살펴보고 어떤 변경사항이 여러분에게 중요한지 살펴보세요.
