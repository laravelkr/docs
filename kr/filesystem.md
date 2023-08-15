# File Storage
# 파일 스토리지

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [The Local Driver](#the-local-driver)
    - [로컬 드라이버](#the-local-driver)
    - [The Public Disk](#the-public-disk)
    - [Public 디스크](#the-public-disk)
    - [Driver Prerequisites](#driver-prerequisites)
    - [드라이버 사용시 준비사항](#driver-prerequisites)
    - [Scoped & Read-Only Filesystems](#scoped-and-read-only-filesystems)
    - [범위 제한 & 읽기 전용 파일시스템](#scoped-and-read-only-filesystems)
    - [Amazon S3 Compatible Filesystems](#amazon-s3-compatible-filesystems)
    - [Amazon S3 호환 파일 시스템](#amazon-s3-compatible-filesystems)
- [Obtaining Disk Instances](#obtaining-disk-instances)
- [Disk 인스턴스 획득하기](#obtaining-disk-instances)
    - [On-Demand Disks](#on-demand-disks)
    - [온디멘드 디스크](#on-demand-disks)
- [Retrieving Files](#retrieving-files)
- [파일 조회하기](#retrieving-files)
    - [Downloading Files](#downloading-files)
    - [파일 다운로드](#downloading-files)
    - [File URLs](#file-urls)
    - [파일 URL](#file-urls)
    - [Temporary URLs](#temporary-urls)
    - [임시 URLs](#temporary-urls)
    - [File Metadata](#file-metadata)
    - [파일의 메타 데이터](#file-metadata)
- [Storing Files](#storing-files)
- [파일 저장하기](#storing-files)
    - [Prepending & Appending To Files](#prepending-appending-to-files)
    - [파일의 앞에 내용 추가하기 & 뒤에 내용 추가하기](#prepending-appending-to-files)
    - [Copying & Moving Files](#copying-moving-files)
    - [파일 복사 & 이동](#copying-moving-files)
    - [Automatic Streaming](#automatic-streaming)
    - [자동 스트리밍](#automatic-streaming)
    - [File Uploads](#file-uploads)
    - [파일 업로드](#file-uploads)
    - [File Visibility](#file-visibility)
    - [파일 Visibility](#file-visibility)
- [Deleting Files](#deleting-files)
- [파일 삭제하기](#deleting-files)
- [Directories](#directories)
- [디렉토리들](#directories)
- [Testing](#testing)
- [테스팅](#testing)
- [Custom Filesystems](#custom-filesystems)
- [사용자 정의 파일 시스템](#custom-filesystems)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel provides a powerful filesystem abstraction thanks to the wonderful [Flysystem](https://github.com/thephpleague/flysystem) PHP package by Frank de Jonge. The Laravel Flysystem integration provides simple drivers for working with local filesystems, SFTP, and Amazon S3. Even better, it's amazingly simple to switch between these storage options between your local development machine and production server as the API remains the same for each system.

라라벨은 Frank de Jonge가 만든 [Flysystem](https://github.com/thephpleague/flysystem) 패키지를 기반으로 하는 강력한 추상화된 파일 시스템을 제공합니다. 라라벨의 통합된 FlySystem은 로컬 파일시스템, SFTP, Amazon S3를 드라이버들을 통해서 간단하게 사용할 수 있게 제공합니다. 더욱 놀라운 것은, 각각의 시스템에 대해 동일한 API를 사용하고 있기 때문에 로컬 개발 시스템과 프로덕션 서버 간에 스토리지를 매우 쉽게 변경할 수 있다는 것입니다.

<a name="configuration"></a>
## Configuration
## 설정하기

Laravel's filesystem configuration file is located at `config/filesystems.php`. Within this file, you may configure all of your filesystem "disks". Each disk represents a particular storage driver and storage location. Example configurations for each supported driver are included in the configuration file so you can modify the configuration to reflect your storage preferences and credentials.

라라벨의 파일시스템 설정 파일은 `config/file/systems.php` 파일에 위치하고 있습니다. 이 파일 안에서 여러분은 여러분의 모든 파일 시스템 "디스크"를 설정할 수 있습니다. 각각의 디스크는 개별 스토리지 드라이버와 스토리지의 위치를 나타냅니다. 설정 파일에는 각각 지원하고 있는 드라이버에 대한 예제 설정이 포함되어 있으므로 스토리지 구성 및 인증정보를 반영하도록 설정을 수정할 수 있습니다.

The `local` driver interacts with files stored locally on the server running the Laravel application while the `s3` driver is used to write to Amazon's S3 cloud storage service.

`local` 드라이버는 라라벨 애플리케이션을 실행하는 서버에 로컬로 저장된 파일을 다루는 반면 `s3` 드라이버는 Amazon의 S3 클라우드 스토리지 서비스에 사용됩니다.

> **Note**  
> You may configure as many disks as you like and may even have multiple disks that use the same driver.

> **Note**  
> 여러분은 원하는만큼 디스크를 설정할 수 있으며, 동일한 드라이버에 대해 여러개의 디스크를 가질 수도 있습니다.

<a name="the-local-driver"></a>
### The Local Driver
### 로컬 드라이버

When using the `local` driver, all file operations are relative to the `root` directory defined in your `filesystems` configuration file. By default, this value is set to the `storage/app` directory. Therefore, the following method would write to `storage/app/example.txt`:

`local` 드라이버를 사용하는 경우, `filesystems` 설정 파일에 정의되어있는 `root` 디렉토리를 기준으로 모든 파일이 상대적으로 조작됩니다. 기본적으로 이 값은 `storage/app` 디렉토리로 설정됩니다. 따라서 다음 메소드는 `storage/app/example.txt`에 작성합니다.

    use Illuminate\Support\Facades\Storage;

    Storage::disk('local')->put('example.txt', 'Contents');

<a name="the-public-disk"></a>
### The Public Disk
### Public 디스크

The `public` disk included in your application's `filesystems` configuration file is intended for files that are going to be publicly accessible. By default, the `public` disk uses the `local` driver and stores its files in `storage/app/public`.

애플리케이션의 `filesystems` 구성 파일에 포함된 `public` 디스크는 파일을 누구나 접근 가능하다는 것을 의도합니다. 기본적으로, `public` 디스크는 `local` 드라이버를 사용하고 파일들을 `storage/app/public` 에 저장합니다.

To make these files accessible from the web, you should create a symbolic link from `public/storage` to `storage/app/public`. Utilizing this folder convention will keep your publicly accessible files in one directory that can be easily shared across deployments when using zero down-time deployment systems like [Envoyer](https://envoyer.io).

웹에서 이러한 파일에 액세스할 수 있도록 하려면 `public/storage` 를 `storage/app/public` 으로 심볼릭 링크를 생성해야 합니다. 이 폴더 규칙으로 접근가능한 파일들은 [Envoyer](https://envoyer.io)로 무중단 배포를 사용하는 경우, 손쉽게 공유될 수 있습니다.

To create the symbolic link, you may use the `storage:link` Artisan command:

심볼릭 링크를 생성하기 위해서 `storage:link` 아티즌 명령어를 사용할 수 있습니다.

```shell
php artisan storage:link
```

Once a file has been stored and the symbolic link has been created, you can create a URL to the files using the `asset` helper:

파일을 저장하고 심볼릭 링크가 생성되어 있다면, `asset` 헬퍼를 통해서 파일에 대한 URL을 생성할 수 있습니다.

    echo asset('storage/file.txt');

You may configure additional symbolic links in your `filesystems` configuration file. Each of the configured links will be created when you run the `storage:link` command:

`filesystems`설정 파일에서 추가적인 심볼릭 링크를 구성 할 수 있습니다. `storage:link` 커맨드를 실행할 때 설정한 각 링크가 생성됩니다.

    'links' => [
        public_path('storage') => storage_path('app/public'),
        public_path('images') => storage_path('app/images'),
    ],

<a name="driver-prerequisites"></a>
### Driver Prerequisites
### 드라이버 사용시 준비사항

<a name="s3-driver-configuration"></a>
#### S3 Driver Configuration
#### S3 드라이버 설정하기

Before using the S3 driver, you will need to install the Flysystem S3 package via the Composer package manager:

S3 드라이버를 사용하기 전에 Composer 패키지 관리자를 통해 Flysystem S3 패키지를 설치해야 합니다.

```shell
composer require league/flysystem-aws-s3-v3 "^3.0" --with-all-dependencies
```

The S3 driver configuration information is located in your `config/filesystems.php` configuration file. This file contains an example configuration array for an S3 driver. You are free to modify this array with your own S3 configuration and credentials. For convenience, these environment variables match the naming convention used by the AWS CLI.

S3 드라이버 설정 정보는 `config/filesystems.php` 설정 파일안에 있습니다. 이 파일은 S3 드라이버 설정에 대한 예제 배열을 포함하고 있습니다. 여러분은 자유롭게 여러분의 S3 설정과 인증을 위해서 이 배열을 수정할 수 있습니다. 편의를 위해서, 이 환경설정값들은 AWS CLI를 사용하여 네이밍 컨벤션과 매칭됩니다.

<a name="ftp-driver-configuration"></a>
#### FTP Driver Configuration
#### FTP 드라이버 설정하기

Before using the FTP driver, you will need to install the Flysystem FTP package via the Composer package manager:

FTP 드라이버를 사용하기 전에 Composer 패키지 관리자를 통해 Flysystem FTP 패키지를 설치해야 합니다.

```shell
composer require league/flysystem-ftp "^3.0"
```

Laravel's Flysystem integrations work great with FTP; however, a sample configuration is not included with the framework's default `filesystems.php` configuration file. If you need to configure an FTP filesystem, you may use the configuration example below:

라라벨의 통합 Flysystem은 FTP를 통해서도 원활하게 동작합니다; 하지만 `filesystems.php` 설정 파일에는 기본적으로 이 설정 내용이 포함되어 있지 않습니다. 만약 FTP 파일 시스템 설정이 필요하다면, 다음과 같은 구성 예제를 참고할 수 있습니다.

    'ftp' => [
        'driver' => 'ftp',
        'host' => env('FTP_HOST'),
        'username' => env('FTP_USERNAME'),
        'password' => env('FTP_PASSWORD'),

        // Optional FTP Settings...
        // 'port' => env('FTP_PORT', 21),
        // 'root' => env('FTP_ROOT'),
        // 'passive' => true,
        // 'ssl' => true,
        // 'timeout' => 30,
    ],

<a name="sftp-driver-configuration"></a>
#### SFTP Driver Configuration
#### SFTP 드라이버 설정하기

Before using the SFTP driver, you will need to install the Flysystem SFTP package via the Composer package manager:

SFTP 드라이버를 사용하기 전에 Composer 패키지 관리자를 통해 Flysystem SFTP 패키지를 설치해야 합니다.

```shell
composer require league/flysystem-sftp-v3 "^3.0"
```

Laravel's Flysystem integrations work great with SFTP; however, a sample configuration is not included with the framework's default `filesystems.php` configuration file. If you need to configure an SFTP filesystem, you may use the configuration example below:

라라벨의 Flysystem은 SFTP를 통해서도 원활하게 동작합니다; 하지만 `filesystems.php` 설정 파일에는 기본적으로 이 설정 내용이 포함되어 있지 않습니다. 만약에 SFTP 파일 시스템 설정이 필요하다면, 다음과 같은 구성 예제를 참고할 수 있습니다.

    'sftp' => [
        'driver' => 'sftp',
        'host' => env('SFTP_HOST'),

        // Settings for basic authentication...
        'username' => env('SFTP_USERNAME'),
        'password' => env('SFTP_PASSWORD'),

        // Settings for SSH key based authentication with encryption password...
        'privateKey' => env('SFTP_PRIVATE_KEY'),
        'passphrase' => env('SFTP_PASSPHRASE'),

        // Settings for file / directory permissions...
        'visibility' => 'private', // `private` = 0600, `public` = 0644
        'directory_visibility' => 'private', // `private` = 0700, `public` = 0755

        // Optional SFTP Settings...
        // 'hostFingerprint' => env('SFTP_HOST_FINGERPRINT'),
        // 'maxTries' => 4,
        // 'passphrase' => env('SFTP_PASSPHRASE'),
        // 'port' => env('SFTP_PORT', 22),
        // 'root' => env('SFTP_ROOT', ''),
        // 'timeout' => 30,
        // 'useAgent' => true,
    ],

<a name="scoped-and-read-only-filesystems"></a>
### Scoped & Read-Only Filesystems
### 범위 제한 & 읽기 전용 파일시스템

Scoped disks allow you to define a filesystem where all paths are automatically prefixed with a given path prefix. Before creating a scoped filesystem disk, you will need to install an additional Flysystem package via the Composer package manager:

범위 지정 디스크를 사용하면 모든 경로에 지정된 경로 접두사가 자동으로 붙는 파일 시스템을 정의할 수 있습니다. 범위가 지정된 파일 시스템 디스크를 만들기 전에 Composer 패키지 관리자를 통해 추가 Flysystem 패키지를 설치해야 합니다.

```shell
composer require league/flysystem-path-prefixing "^3.0"
```

You may create a path scoped instance of any existing filesystem disk by defining a disk that utilizes the `scoped` driver. For example, you may create a disk which scopes your existing `s3` disk to a specific path prefix, and then every file operation using your scoped disk will utilize the specified prefix:

`scoped` 드라이버를 사용하는 디스크를 정의하여 기존 파일 시스템 디스크의 경로 범위 인스턴스를 만들 수 있습니다. 예를 들어, 기존 `s3` 디스크의 범위를 특정 경로 접두사로 지정하는 디스크를 생성하면 범위 지정 디스크를 사용하는 모든 파일 작업이 지정된 접두사를 활용할 수 있습니다.

```php
's3-videos' => [
    'driver' => 'scoped',
    'disk' => 's3',
    'prefix' => 'path/to/videos',
],
```

"Read-only" disks allow you to create filesystem disks that do not allow write operations. Before using the `read-only` configuration option, you will need to install an additional Flysystem package via the Composer package manager:

"읽기 전용" 디스크는 쓰기 작업을 허용하지 않는 디스크를 만들 수 있게 해줍니다. `read-only` 설정 옵션을 사용하기 전에 Composer 패키지 매니저를 통해 추가적인 Flysystem 패키지를 설치해야 합니다.

```shell
composer require league/flysystem-read-only "^3.0"
```

Next, you may include the `read-only` configuration option in one or more of your disk's configuration arrays:

그러고 나면 `read-only` 설정 옵션을 디스크 설정 배열에 하나 이상 포함 할 수 있습니다.

```php
's3-videos' => [
    'driver' => 's3',
    // ...
    'read-only' => true,
],
```

<a name="amazon-s3-compatible-filesystems"></a>
### Amazon S3 Compatible Filesystems
### Amazon S3 호환 파일 시스템

By default, your application's `filesystems` configuration file contains a disk configuration for the `s3` disk. In addition to using this disk to interact with Amazon S3, you may use it to interact with any S3 compatible file storage service such as [MinIO](https://github.com/minio/minio) or [DigitalOcean Spaces](https://www.digitalocean.com/products/spaces/).

기본적으로 애플리케이션의 `filesystems` 구성 파일에는 `s3` 디스크에 대한 디스크 구성이 포함되어 있습니다. 이 디스크를 사용하여 Amazon S3를 다루는 것 외에도 [MinIO](https://github.com/minio/minio) 또는 [DigitalOcean Spaces](https://www.digitalocean.com/products/spaces) 와 같은 S3 호환 파일 스토리지 서비스를  사용할 수 있습니다.

Typically, after updating the disk's credentials to match the credentials of the service you are planning to use, you only need to update the value of the `endpoint` configuration option. This option's value is typically defined via the `AWS_ENDPOINT` environment variable:

일반적으로 사용하려는 서비스의 자격 증명과 일치하도록 디스크 자격 증명을 업데이트한 후 `endpoint` 구성 옵션의 값만 업데이트하면 됩니다. 이 옵션의 값은 일반적으로 `AWS_ENDPOINT` 환경 변수를 통해 정의됩니다.

    'endpoint' => env('AWS_ENDPOINT', 'https://minio:9000'),

<a name="minio"></a>
#### MinIO
#### MinIO

In order for Laravel's Flysystem integration to generate proper URLs when using MinIO, you should define the `AWS_URL` environment variable so that it matches your application's local URL and includes the bucket name in the URL path:

Laravel의 Flysystem 통합이 MinIO를 사용할 때 올바른 URL을 생성하도록 하려면 `AWS_URL` 환경 변수를 정의하여 애플리케이션의 로컬 URL과 URL 경로에 버킷 이름이 포함되도록 해야 합니다.

```ini
AWS_URL=http://localhost:9000/local
```

> **Warning**  
> Generating temporary storage URLs via the `temporaryUrl` method is not supported when using MinIO.

> **Warning**  
> MinIO를 사용할 때는 `temporaryUrl` 메소드를 통해 임시 저장소 URL을 생성하는 것은 지원되지 않습니다.

<a name="obtaining-disk-instances"></a>
## Obtaining Disk Instances
## 디스크 인스턴스 획득하기

The `Storage` facade may be used to interact with any of your configured disks. For example, you may use the `put` method on the facade to store an avatar on the default disk. If you call methods on the `Storage` facade without first calling the `disk` method, the method will automatically be passed to the default disk:

`Storage` 파사드를 사용하면 설정된 디스크에 대한 작업을 처리할 수 있습니다. 예를 들어 사용자 아바타 이미지를 기본으로 설정된 디스크에 저장하기 위해서 `put` 메소드를 사용할 수 있습니다. `Storage` 파사드에서 `disk` 메소드 호출이 없을 경우에는 메소드는 자동으로 기본으로 설정된 디스크에 대해서 실행됩니다.

    use Illuminate\Support\Facades\Storage;

    Storage::put('avatars/1', $content);

If your application interacts with multiple disks, you may use the `disk` method on the `Storage` facade to work with files on a particular disk:

만약 여러분의 애플리케이션이 여러개의 디스크를 다룬다면, `Storage` 파사드의 `disk` 메소드를 사용하여 개별 디스크의 파일에 대해서 작업을 수행할 수 있습니다.

    Storage::disk('s3')->put('avatars/1', $content);

<a name="on-demand-disks"></a>
### On-Demand Disks
### 온디멘드 디스크

Sometimes you may wish to create a disk at runtime using a given configuration without that configuration actually being present in your application's `filesystems` configuration file. To accomplish this, you may pass a configuration array to the `Storage` facade's `build` method:

때로는 애플리케이션의 `filesystems` 설정 파일에 존재하지 않는 설정을 사용하여 런타임에 디스크를 생성 할 수 있습니다. 이를 수행하려면 `Storage` 파사드의 `build` 메소드에 설정 배열을 전달 해야 합니다.

```php
use Illuminate\Support\Facades\Storage;

$disk = Storage::build([
    'driver' => 'local',
    'root' => '/path/to/root',
]);

$disk->put('image.jpg', $content);
```

<a name="retrieving-files"></a>
## Retrieving Files
## 파일 조회하기

The `get` method may be used to retrieve the contents of a file. The raw string contents of the file will be returned by the method. Remember, all file paths should be specified relative to the disk's "root" location:

`get` 메소드는 파일의 내용을 검색하는 데 사용합니다. 이 메소드는 파일의 내용을 그대로 돌려 줍니다. 주의할 점은 모든 파일의 패스는 디스크의 "루트" 와 상대 경로로 지정되어 져야 한다는 것입니다.

    $contents = Storage::get('file.jpg');

If the file you are retrieving contains JSON, you may use the `json` method to retrieve the file and decode its contents:

만약 조회하려면 파일이 JSON을 가지고 있다면 `json` 메서드를 사용하여 내용을 바로 확인할 수 있습니다.

    $orders = Storage::json('orders.json');

The `exists` method may be used to determine if a file exists on the disk:

`exists` 메소드는 파일이 디스크에 존재하고 있는지를 확인하기 위해 사용합니다.

    if (Storage::disk('s3')->exists('file.jpg')) {
        // ...
    }

The `missing` method may be used to determine if a file is missing from the disk:

`missing` 메소드는 파일이 디스크에서 누락되었는지 판별하는 데 사용될 수 있습니다.

    if (Storage::disk('s3')->missing('file.jpg')) {
        // ...
    }

<a name="downloading-files"></a>
### Downloading Files
### 파일 다운로드

The `download` method may be used to generate a response that forces the user's browser to download the file at the given path. The `download` method accepts a filename as the second argument to the method, which will determine the filename that is seen by the user downloading the file. Finally, you may pass an array of HTTP headers as the third argument to the method:

`download` 메소드는 브라우저에서 주어진 경로의 파일에 대한 다운로드를 수행하는 응답-response를 생성합니다. `download` 메소드는 다운로드 받을 때 결정되는 파일 이름을 두번째 인자로 전달받습니다. 그리고 세번째 인자로 HTTP 헤더 배열을 인자로 받습니다.

    return Storage::download('file.jpg');

    return Storage::download('file.jpg', $name, $headers);

<a name="file-urls"></a>
### File URLs
### 파일 URL

You may use the `url` method to get the URL for a given file. If you are using the `local` driver, this will typically just prepend `/storage` to the given path and return a relative URL to the file. If you are using the `s3` driver, the fully qualified remote URL will be returned:

주어진 파일의 URL을 얻기 위해서 `url` 메소드를 사용할 수 있습니다. `local` 드라이버를 사용중이라면 일반적으로 주어진 경로 앞에 `/storage` 를 덧붙여서 파일에 대한 URL이 반환됩니다. `s3` 드라이버를 사용중이라면 전체 URL이 반환됩니다.

    use Illuminate\Support\Facades\Storage;

    $url = Storage::url('file.jpg');

When using the `local` driver, all files that should be publicly accessible should be placed in the `storage/app/public` directory. Furthermore, you should [create a symbolic link](#the-public-disk) at `public/storage` which points to the `storage/app/public` directory.

`local` 드라이버를 사용하는 경우, 공개적으로 접근이 가능한 모든 파일들은 `storage/app/public` 디렉토리 안에 위치해야 합니다. 또한 `storage/app/public` 디렉토리를 가리키는 `public/storage` [심볼릭 링크](#the-public-disk)를 생성해야 합니다.

> **Warning**  
> When using the `local` driver, the return value of `url` is not URL encoded. For this reason, we recommend always storing your files using names that will create valid URLs.

> **Warning**  
> `local` 드라이버를 사용할 때, 반환되는 `url` 은 URL 인코딩된 값이 아닙니다. 따라서, 파일 이름을 항상 유효한 URL이 되도록 저장하는 것을 권장합니다.

#### URL Host Customization
#### URL 호스트 커스터마이징

If you would like to pre-define the host for URLs generated using the `Storage` facade, you may add a `url` option to the disk's configuration array:

`Storage` 파사드를 사용하여 생성된 URL에 대한 호스트를 미리 정의하려면 디스크의 설정 배열에 `url` 옵션을 추가하면 됩니다.

    'public' => [
        'driver' => 'local',
        'root' => storage_path('app/public'),
        'url' => env('APP_URL').'/storage',
        'visibility' => 'public',
    ],

<a name="temporary-urls"></a>
### Temporary URLs
### 임시 URLs

Using the `temporaryUrl` method, you may create temporary URLs to files stored using the `s3` driver. This method accepts a path and a `DateTime` instance specifying when the URL should expire:

`temporaryUrl` 메소드를 사용하여 `s3` 드라이버를 사용하여 저장된 파일에 대한 임시 URL을 생성할 수 있습니다. 이 메소드는 파일의 경로 및 언제 URL이 만료되어야 하는지 지정한 `DateTime` 인스턴스를 인자로 받습니다.

    use Illuminate\Support\Facades\Storage;

    $url = Storage::temporaryUrl(
        'file.jpg', now()->addMinutes(5)
    );

If you need to specify additional [S3 request parameters](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html#RESTObjectGET-requests), you may pass the array of request parameters as the third argument to the `temporaryUrl` method:

추가 [S3 요청 파라미터](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html#RESTObjectGET-requests) 를 지정해야하는 경우 요청 파라미터 배열을 `temporaryUrl` 메소드의 인수의 세 번째 파라미터로 전달할 수 있습니다.

    $url = Storage::temporaryUrl(
        'file.jpg',
        now()->addMinutes(5),
        [
            'ResponseContentType' => 'application/octet-stream',
            'ResponseContentDisposition' => 'attachment; filename=file2.jpg',
        ]
    );

If you need to customize how temporary URLs are created for a specific storage disk, you can use the `buildTemporaryUrlsUsing` method. For example, this can be useful if you have a controller that allows you to download files stored via a disk that doesn't typically support temporary URLs. Usually, this method should be called from the `boot` method of a service provider:

특정 스토리지 디스크에 대해 임시 URL이 생성되는 방식을 커스터마이징 해야 하는 경우 `buildTemporaryUrlsUsing` 메소드를 사용할 수 있습니다. 예를 들어, 임시 URL을 지원하지 않는 디스크를 통해 저장된 파일을 다운로드할 수 있는 컨트롤러가 있는 경우 유용할 수 있습니다. 일반적으로 이 메소드는 서비스 프로바이더의 `boot` 메소드에서 호출해야 합니다.

    <?php

    namespace App\Providers;

    use DateTime;
    use Illuminate\Support\Facades\Storage;
    use Illuminate\Support\Facades\URL;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Bootstrap any application services.
         */
        public function boot(): void
        {
            Storage::disk('local')->buildTemporaryUrlsUsing(
                function (string $path, DateTime $expiration, array $options) {
                    return URL::temporarySignedRoute(
                        'files.download',
                        $expiration,
                        array_merge($options, ['path' => $path])
                    );
                }
            );
        }
    }

<a name="temporary-upload-urls"></a>
#### Temporary Upload URLs
#### 임시 업로드 URL

> **Warning**  
> The ability to generate temporary upload URLs is only supported by the `s3` driver.

> **Warning**  
> 임시 업로드 URL을 생성하는 기능은 `s3` 드라이버에서만 지원됩니다.

If you need to generate a temporary URL that can be used to upload a file directly from your client-side application, you may use the `temporaryUploadUrl` method. This method accepts a path and a `DateTime` instance specifying when the URL should expire. The `temporaryUploadUrl` method returns an associative array which may be destructured into the upload URL and the headers that should be included with the upload request:

클라이언트측 애플리케이션에서 직접 파일을 업로드하는 데 사용할 수 있는 임시 URL을 생성해야 하는 경우 `temporaryUploadUrl` 메서드를 사용할 수 있습니다. 이 메서드는 경로와 URL이 만료되는 시기를 지정하는 `DateTime` 인스턴스를 허용합니다. `temporaryUploadUrl` 메서드는 업로드 URL과 업로드 요청에 포함되어야 하는 헤더로 분해될 수 있는 연관 배열을 반환합니다.

    use Illuminate\Support\Facades\Storage;

    ['url' => $url, 'headers' => $headers] = Storage::temporaryUploadUrl(
        'file.jpg', now()->addMinutes(5)
    );

This method is primarily useful in serverless environments that require the client-side application to directly upload files to a cloud storage system such as Amazon S3.

이 방법은 클라이언트 측 애플리케이션이 파일을 Amazon S3와 같은 클라우드 스토리지 시스템에 직접 업로드해야 하는 서버리스 환경에서 주로 유용합니다.

<a name="file-metadata"></a>
### File Metadata
### 파일 메타 데이터

In addition to reading and writing files, Laravel can also provide information about the files themselves. For example, the `size` method may be used to get the size of a file in bytes:

파일을 읽고 쓰는 것 이외도 라라벨은 파일 자체에 대한 정보도 제공할 수 있습니다. 예를 들어, `size` 메소드는 파일 크기를 바이트로 조회하기 위해서 사용합니다.

    use Illuminate\Support\Facades\Storage;

    $size = Storage::size('file.jpg');

The `lastModified` method returns the UNIX timestamp of the last time the file was modified:

`lastModified` 메소드는 마지막에 파일이 업데이트되었을 때의 UNIX 타임 스탬프값을 반환합니다.

    $time = Storage::lastModified('file.jpg');

The MIME type of a given file may be obtained via the `mimeType` method:

주어진 파일의 MIME 타입은 `mimeType` 메소드를 통해 조회할 수 있습니다.

    $mime = Storage::mimeType('file.jpg')

<a name="file-paths"></a>
#### File Paths
#### 파일 경로

You may use the `path` method to get the path for a given file. If you are using the `local` driver, this will return the absolute path to the file. If you are using the `s3` driver, this method will return the relative path to the file in the S3 bucket:

`path` 메소드를 이용하여 지정된 파일의 경로를 가져올 수 있습니다. `local` 드라이버를 사용하는 경우, 파일의 절대 경로를 반환합니다. `S3` 드라이버를 사용하는 경우, S3 버킷의 파일 상대 경로를 반환합니다.

    use Illuminate\Support\Facades\Storage;

    $path = Storage::path('file.jpg');

<a name="storing-files"></a>
## Storing Files
## 파일 저장하기

The `put` method may be used to store file contents on a disk. You may also pass a PHP `resource` to the `put` method, which will use Flysystem's underlying stream support. Remember, all file paths should be specified relative to the "root" location configured for the disk:

`put` 메소드는 파일의 내용을 디스크에 저장하는데 사용됩니다. 또한 `put` 메소드에 `resource` 를 전달하여 파일시스템의 스트림을 사용할 수도 있습니다. 모든 파일 경로는 디스크에 대해 구성된 "root" 위치를 기준으로 지정해야합니다.

    use Illuminate\Support\Facades\Storage;

    Storage::put('file.jpg', $contents);

    Storage::put('file.jpg', $resource);

<a name="failed-writes"></a>
#### Failed Writes
#### 쓰기 실패

If the `put` method (or other "write" operations) is unable to write the file to disk, `false` will be returned:

`put` 메소드 (또는 "쓰기" 작업)이 디스크에 파일을 수정하는데 실패하면 `false` 가 반환됩니다.

    if (! Storage::put('file.jpg', $contents)) {
        // The file could not be written to disk...
    }

If you wish, you may define the `throw` option within your filesystem disk's configuration array. When this option is defined as `true`, "write" methods such as `put` will throw an instance of `League\Flysystem\UnableToWriteFile` when write operations fail:

원하는 경우, 파일시스템 디스크 설정 배열값에 `throw` 옵션을 설정할 수 있습니다. 이 옵션값이 `true` 로 설정되어 있다면 `put` 과 같은 "쓰기" 동작은 작업이 실패하는 경우 `League\Flysystem\UnableToWriteFile` 가 발생합니다.

    'public' => [
        'driver' => 'local',
        // ...
        'throw' => true,
    ],

<a name="prepending-appending-to-files"></a>
### Prepending & Appending To Files
### 파일의 앞에 내용 추가하기 & 뒤에 내용 추가하기

The `prepend` and `append` methods allow you to write to the beginning or end of a file:

`prepend` 와 `append` 메소드를 사용하면 파일의 처음과 끝에 내용을 추가할 수 있습니다.

    Storage::prepend('file.log', 'Prepended Text');

    Storage::append('file.log', 'Appended Text');

<a name="copying-moving-files"></a>
### Copying & Moving Files
### 파일 복사 & 이동

The `copy` method may be used to copy an existing file to a new location on the disk, while the `move` method may be used to rename or move an existing file to a new location:

`copy` 메소드는 존재하는 파일을 디스크의 새로운 위치에 복사하는데 사용되고, `move` 메소드는 존재하는 파일의 이름을 변경하거나 새로운 위치에 이동시키는데 사용됩니다.

    Storage::copy('old/file.jpg', 'new/file.jpg');

    Storage::move('old/file.jpg', 'new/file.jpg');

<a name="automatic-streaming"></a>
### Automatic Streaming
### 자동 스트리밍

Streaming files to storage offers significantly reduced memory usage. If you would like Laravel to automatically manage streaming a given file to your storage location, you may use the `putFile` or `putFileAs` method. This method accepts either an `Illuminate\Http\File` or `Illuminate\Http\UploadedFile` instance and will automatically stream the file to your desired location:

파일을 스토리지로 스트리밍하면 메모리 사용량이 크게 줄어듭니다. 만약 주어진 파일이 여러분의 스토리지 위치에 자동적으로 스트리밍되도록 라라벨이 관리하기를 바란다면 `putFile` 이나 `putFileAs` 메소드를 사용하면 됩니다. 이 메소드는 `Illuminate\Http\File` 이나 `Illuminate\Http\UploadedFile` 인스턴스를 인자로 받아들이고 자동으로 명시한 위치에 파일을 스트림 처리할 것입니다.

    use Illuminate\Http\File;
    use Illuminate\Support\Facades\Storage;

    // Automatically generate a unique ID for filename...
    $path = Storage::putFile('photos', new File('/path/to/photo'));

    // Manually specify a filename...
    $path = Storage::putFileAs('photos', new File('/path/to/photo'), 'photo.jpg');

There are a few important things to note about the `putFile` method. Note that we only specified a directory name and not a filename. By default, the `putFile` method will generate a unique ID to serve as the filename. The file's extension will be determined by examining the file's MIME type. The path to the file will be returned by the `putFile` method so you can store the path, including the generated filename, in your database.

`putFile` 메소드에는 몇가지 중요한 사항들이 있습니다. 파일 이름이 아니라 디렉토리 이름을 지정하는 것에 유의하십시오. 기본적으로 `putFile` 메소드는 파일이름을 기반으로 한 고유한 ID를 생성합니다. 파일의 확장자는 파일의 MIME 타입에 의해서 결정됩니다. `putFile` 메소드에 의해서 파일의 경로가 반환 될것이기 때문에, 이 경로에 생성된 파일 이름을 포함하여 데이터베이스에 저장할 수 있습니다.

The `putFile` and `putFileAs` methods also accept an argument to specify the "visibility" of the stored file. This is particularly useful if you are storing the file on a cloud disk such as Amazon S3 and would like the file to be publicly accessible via generated URLs:

`putFile` 과 `putFileAs` 메소드는 또한 저장되는 파일의 "visibility"를 지정하는 인자를 받아들입니다. 이는 특히 Amazon S3 와 같은 클라우드 디스크에 파일을 저장하고 파일의 접근 권한을 설정하고자 할 때 유용합니다.

    Storage::putFile('photos', new File('/path/to/photo'), 'public');

<a name="file-uploads"></a>
### File Uploads
### 파일 업로드

In web applications, one of the most common use-cases for storing files is storing user uploaded files such as photos and documents. Laravel makes it very easy to store uploaded files using the `store` method on an uploaded file instance. Call the `store` method with the path at which you wish to store the uploaded file:

웹 애플리케이션에서 파일을 저장하는 가장 공통적인 사용 예 중 하나는 사진, 그리고 문서와 같은 사용자가 업로드한 파일을 저장하는 것입니다. 라라벨은 업로드된 파일 인스턴스에서 `store` 메소드를 사용하여 업로드한 파일이 저장되는 것을 아주 쉽게 처리합니다. 저장된 파일이 저장되고자 하는 경로를 넘겨주며 `store` 메소드를 호출하면 됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;

    class UserAvatarController extends Controller
    {
        /**
         * Update the avatar for the user.
         */
        public function update(Request $request): string
        {
            $path = $request->file('avatar')->store('avatars');

            return $path;
        }
    }

There are a few important things to note about this example. Note that we only specified a directory name, not a filename. By default, the `store` method will generate a unique ID to serve as the filename. The file's extension will be determined by examining the file's MIME type. The path to the file will be returned by the `store` method so you can store the path, including the generated filename, in your database.

이 예제에서는 몇가지 중요한 사항들이 있습니다. 예제에서 파일 이름이 아니라 디렉토리 이름을 지정하는 것에 유의하십시오. 기본적으로 `store` 메소드는 파일이름을 기반으로 한 고유한 ID를 생성합니다. 파일의 확장자는 파일의 MIME 타입에 의해서 결정됩니다. `store` 메소드에 의해서 파일의 경로가 반환 될 것이기 때문에, 이 경로에 생성된 파일 이름을 포함하여 데이터베이스에 저장할 수 있습니다.

You may also call the `putFile` method on the `Storage` facade to perform the same file storage operation as the example above:

또한 다음 예제와 같이 `Storage` 파사드의 `putFile` 메소드를 호출하여 동일한 파일 저장 작업을 수행할 수 있습니다.

    $path = Storage::putFile('avatars', $request->file('avatar'));

<a name="specifying-a-file-name"></a>
#### Specifying A File Name
#### 파일 이름 지정하기

If you do not want a filename to be automatically assigned to your stored file, you may use the `storeAs` method, which receives the path, the filename, and the (optional) disk as its arguments:

저장되는 파일에 이름이 자동으로 지정되지 않게 하고 싶다면, `storeAs` 메소드에 파일의 경로와 이름 그리고 (선택적으로) 디스크를 인자로 전달하여 사용할 수 있습니다.

    $path = $request->file('avatar')->storeAs(
        'avatars', $request->user()->id
    );

You may also use the `putFileAs` method on the `Storage` facade, which will perform the same file storage operation as the example above:

다음의 예제와 같이 동일하게 `Storage` 파사드의 `putFileAs` 메소드를 사용하여 파일 저장 작업을 수행할 수도 있습니다.

    $path = Storage::putFileAs(
        'avatars', $request->file('avatar'), $request->user()->id
    );

> **Warning**  
> Unprintable and invalid unicode characters will automatically be removed from file paths. Therefore, you may wish to sanitize your file paths before passing them to Laravel's file storage methods. File paths are normalized using the `League\Flysystem\WhitespacePathNormalizer::normalizePath` method.

> **Warning**  
> 출력할 수 없거나 유효하지 않은 유니코드 문자는 파일 경로에서 자동으로 삭제됩니다. 그러므로 파일 경로를 라라벨 파일 저장 메소드에 전달하기 전에 sanitize 하시기 바랍니다. 파일 경로는 `League\Flysystem\WhitespacePathNormalizer::normalizePath` 메소드로 정규화됩니다.

<a name="specifying-a-disk"></a>
#### Specifying A Disk
#### 디스크 지정하기

By default, this uploaded file's `store` method will use your default disk. If you would like to specify another disk, pass the disk name as the second argument to the `store` method:

기본적으로, 업로드된 파일의 `store` 메소드는 기본 디스크를 사용할 것입니다. 만약 여러분이 다른 디스크를 지정하고자 한다면, `store` 메소드의 두번째 인자로 디스크의 이름을 전달하면 됩니다.

    $path = $request->file('avatar')->store(
        'avatars/'.$request->user()->id, 's3'
    );

If you are using the `storeAs` method, you may pass the disk name as the third argument to the method:

`storeAs` 메소드를 사용하는 경우 디스크 이름을 메소드의 세 번째 인수로 전달할 수 있습니다.

    $path = $request->file('avatar')->storeAs(
        'avatars',
        $request->user()->id,
        's3'
    );

<a name="other-uploaded-file-information"></a>
#### Other Uploaded File Information
#### 기타 업로드 파일 정보

If you would like to get the original name and extension of the uploaded file, you may do so using the `getClientOriginalName` and `getClientOriginalExtension` methods:

업로드 된 파일의 원래 이름과 확장자를 얻으려면 `getClientOriginalName` 와 `getClientOriginalExtension` 메소드를 사용하십시오.

    $file = $request->file('avatar');

    $name = $file->getClientOriginalName();
    $extension = $file->getClientOriginalExtension();

However, keep in mind that the `getClientOriginalName` and `getClientOriginalExtension` methods are considered unsafe, as the file name and extension may be tampered with by a malicious user. For this reason, you should typically prefer the `hashName` and `extension` methods to get a name and an extension for the given file upload:

하지만, 파일 이름과 확장자가 악의적인 사용자에 의해 변조될 수 있으므로 `getClientOriginalName` 와 `getClientOriginalExtension` 메소드는 안전하지 않을 수 있습니다. 이러한 이유로 업로드된 파일의 이름과 확장자를 얻기 위해 `hashName` 과 `extension` 메소드를 사용해야 합니다.

    $file = $request->file('avatar');

    $name = $file->hashName(); // Generate a unique, random name...
    $extension = $file->extension(); // Determine the file's extension based on the file's MIME type...

<a name="file-visibility"></a>
### File Visibility
### 파일 Visibility

In Laravel's Flysystem integration, "visibility" is an abstraction of file permissions across multiple platforms. Files may either be declared `public` or `private`. When a file is declared `public`, you are indicating that the file should generally be accessible to others. For example, when using the S3 driver, you may retrieve URLs for `public` files.

라라벨의 Flysystem 구성에서, "visibility" 는 다양한 플랫폼에서 파일에 대한 권한의 추상화 입니다. 파일들은 `public` 이거나 `private` 로 지정될 수 있습니다. 파일을 `public` 으로 선언하면, 다른 사람들 일반적으로 파일에 접근할 수 있다는 것을 나타냅니다. 예를 들어 S3 드라이버를 사용할 때, `public` 파일들에 대해서 URL을 조회할 수 있습니다.

You can set the visibility when writing the file via the `put` method:

`put` 메소드를 통해서 파일을 설정할 때 visibility를 설정할 수 있습니다.

    use Illuminate\Support\Facades\Storage;

    Storage::put('file.jpg', $contents, 'public');

If the file has already been stored, its visibility can be retrieved and set via the `getVisibility` and `setVisibility` methods:

만약 파일이 이미 저장되어 있다면, 이 파일의 visibility 는 `getVisibility` 와 `setVisibility`를 통해서 조회하고 설정할 수 있습니다.

    $visibility = Storage::getVisibility('file.jpg');

    Storage::setVisibility('file.jpg', 'public');

When interacting with uploaded files, you may use the `storePublicly` and `storePubliclyAs` methods to store the uploaded file with `public` visibility:

업로드 된 파일과 상호 작용할 때 `storePublicly` 및 `storePubliclyAs` 메소드를 사용하여 업로드 된 파일을 `public`으로 저장할 수 있습니다.

    $path = $request->file('avatar')->storePublicly('avatars', 's3');

    $path = $request->file('avatar')->storePubliclyAs(
        'avatars',
        $request->user()->id,
        's3'
    );

<a name="local-files-and-visibility"></a>
#### Local Files & Visibility
#### 로컬 파일 및 Visibility

When using the `local` driver, `public` [visibility](#file-visibility) translates to `0755` permissions for directories and `0644` permissions for files. You can modify the permissions mappings in your application's `filesystems` configuration file:

`local` 드라이버를 사용할 때 `public` [파일 visibility](file-visibility)는 디렉토리에 대한 `0755` 권한과 파일에 대한 `0644` 권한으로 변환됩니다. 애플리케이션의 `filesystems` 설정 파일에서 권한 매핑을 수정할 수 있습니다.

    'local' => [
        'driver' => 'local',
        'root' => storage_path('app'),
        'permissions' => [
            'file' => [
                'public' => 0644,
                'private' => 0600,
            ],
            'dir' => [
                'public' => 0755,
                'private' => 0700,
            ],
        ],
    ],

<a name="deleting-files"></a>
## Deleting Files
## 파일 삭제하기

The `delete` method accepts a single filename or an array of files to delete:

`delete` 메소드는 삭제할 하나의 파일 이름 또는 파일들의 배열을 인자로 받습니다.

    use Illuminate\Support\Facades\Storage;

    Storage::delete('file.jpg');

    Storage::delete(['file.jpg', 'file2.jpg']);

If necessary, you may specify the disk that the file should be deleted from:

필요한 경우, 파일이 어디에서 지워져야 하는지 디스크를 지정할 수 있습니다.

    use Illuminate\Support\Facades\Storage;

    Storage::disk('s3')->delete('path/file.jpg');

<a name="directories"></a>
## Directories
## 디렉토리들

<a name="get-all-files-within-a-directory"></a>
#### Get All Files Within A Directory
#### 디렉토리 안의 모든 파일들 확인하기

The `files` method returns an array of all of the files in a given directory. If you would like to retrieve a list of all files within a given directory including all subdirectories, you may use the `allFiles` method:

`files` 메소드는 주어진 디렉토리에 들어 있는 모든 파일들에 대한 배열을 반환합니다. 만약 지정된 디렉토리의 하위 디렉토리에 있는 모든 파일의 목록을 포함하기를 원한다면는 `allFiles` 메소드를 사용하면 됩니다.

    use Illuminate\Support\Facades\Storage;

    $files = Storage::files($directory);

    $files = Storage::allFiles($directory);

<a name="get-all-directories-within-a-directory"></a>
#### Get All Directories Within A Directory
#### 디렉토리에 들어 있는 모든 하위 디렉토리들 확인하기

The `directories` method returns an array of all the directories within a given directory. Additionally, you may use the `allDirectories` method to get a list of all directories within a given directory and all of its subdirectories:

`directories` 메소드는 주어진 디렉토리에 들어 있는 전체 디렉토리들에 대한 배열을 반환합니다. 추가적으로 지정된 디렉토리의 하위 디렉토리 및 하위 디렉토리 아래의 모든 디렉토리들에 대한 목록을 포함하기를 원한다면는 `allDirectories` 메소드를 사용하면 됩니다.

    $directories = Storage::directories($directory);

    $directories = Storage::allDirectories($directory);

<a name="create-a-directory"></a>
#### Create A Directory
#### 디렉토리 생성하기

The `makeDirectory` method will create the given directory, including any needed subdirectories:

`makeDirectory` 메소드는 필요한 하위 디렉토리를 포함하여 지정된 디렉토리를 만들 것입니다.

    Storage::makeDirectory($directory);

<a name="delete-a-directory"></a>
#### Delete A Directory
#### 디렉토리 삭제하기

Finally, the `deleteDirectory` method may be used to remove a directory and all of its files:

마지막으로 `deleteDirectory`메소드는 디렉토리와 포함 된 모든 파일을 삭제하는 데 사용됩니다.

    Storage::deleteDirectory($directory);

<a name="testing"></a>
## Testing
## 테스팅

The `Storage` facade's `fake` method allows you to easily generate a fake disk that, combined with the file generation utilities of the `Illuminate\Http\UploadedFile` class, greatly simplifies the testing of file uploads. For example:

`Storage` 파사드의 `fake` 메서드를 사용하면 `Illuminate\Http\UploadedFile` 클래스의 파일 생성 유틸리티와 결합하여 파일 업로드 테스트를 크게 단순화하는 가짜 디스크를 쉽게 생성할 수 있습니다. 예를 들어,

    <?php

    namespace Tests\Feature;

    use Illuminate\Http\UploadedFile;
    use Illuminate\Support\Facades\Storage;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_albums_can_be_uploaded(): void
        {
            Storage::fake('photos');

            $response = $this->json('POST', '/photos', [
                UploadedFile::fake()->image('photo1.jpg'),
                UploadedFile::fake()->image('photo2.jpg')
            ]);

            // Assert one or more files were stored...
            Storage::disk('photos')->assertExists('photo1.jpg');
            Storage::disk('photos')->assertExists(['photo1.jpg', 'photo2.jpg']);

            // Assert one or more files were not stored...
            Storage::disk('photos')->assertMissing('missing.jpg');
            Storage::disk('photos')->assertMissing(['missing.jpg', 'non-existing.jpg']);

            // Assert that a given directory is empty...
            Storage::disk('photos')->assertDirectoryEmpty('/wallpapers');
        }
    }

By default, the `fake` method will delete all files in its temporary directory. If you would like to keep these files, you may use the "persistentFake" method instead. For more information on testing file uploads, you may consult the [HTTP testing documentation's information on file uploads](/docs/{{version}}/http-tests#testing-file-uploads).

기본적으로 `fake` 메서드는 임시 디렉토리에 있는 모든 파일을 삭제합니다. 이러한 파일을 보관하려면 "persistentFake" 방법을 대신 사용할 수 있습니다. 파일 업로드 테스트에 대한 자세한 내용은 [HTTP 테스트 문서에 있는 파일 업로드 테스트하기](/docs/{{version}}/http-tests#testing-file-uploads)를 참조하십시오.

> **Warning**  
> The `image` method requires the [GD extension](https://www.php.net/manual/en/book.image.php).

<a name="custom-filesystems"></a>
## Custom Filesystems
## 커스텀(사용자 정의) 파일 시스템

Laravel's Flysystem integration provides support for several "drivers" out of the box; however, Flysystem is not limited to these and has adapters for many other storage systems. You can create a custom driver if you want to use one of these additional adapters in your Laravel application.

라라벨의 통합 FlySystem은 처음부터 다양한 “드라이버”를 지원 합니다. 하지만 파일시스템은 이러한 드라이버에 제한적이지 않고, 다른 스토리지 시스템에도 적용할 수 있습니다. 여러분은 라라벨 애플리케이션에 적합한 스토리지 시스템에 대한 사용자 정의 드라이버를 생성할 수 있습니다.

In order to define a custom filesystem you will need a Flysystem adapter. Let's add a community maintained Dropbox adapter to our project:

커스텀(사용자 정의) 파일 시스템을 정의하기 위해서는 Flysystem 어댑터가 필요합니다. 커뮤니티에서 관리되고 있는 Dropbox 어댑터를 프로젝트에 추가해보겠습니다.

```shell
composer require spatie/flysystem-dropbox
```

Next, you can register the driver within the `boot` method of one of your application's [service providers](/docs/{{version}}/providers). To accomplish this, you should use the `extend` method of the `Storage` facade:

다음으로, 애플리케이션의 [서비스 프로바이더](/docs/{{version}}/providers) 의 `boot` 메소드 내에서 드라이버를 등록할 수 있습니다. 이를 수행하려면 `Storage` 파사드의 `extend` 메소드를 사용해야 합니다.

    <?php

    namespace App\Providers;

    use Illuminate\Contracts\Foundation\Application;
    use Illuminate\Filesystem\FilesystemAdapter;
    use Illuminate\Support\Facades\Storage;
    use Illuminate\Support\ServiceProvider;
    use League\Flysystem\Filesystem;
    use Spatie\Dropbox\Client as DropboxClient;
    use Spatie\FlysystemDropbox\DropboxAdapter;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
         */
        public function register(): void
        {
            // ...
        }

        /**
         * Bootstrap any application services.
         */
        public function boot(): void
        {
            Storage::extend('dropbox', function (Application $app, array $config) {
                $adapter = new DropboxAdapter(new DropboxClient(
                    $config['authorization_token']
                ));

                return new FilesystemAdapter(
                    new Filesystem($adapter, $config),
                    $adapter,
                    $config
                );
            });
        }
    }

The first argument of the `extend` method is the name of the driver and the second is a closure that receives the `$app` and `$config` variables. The closure must return an instance of `Illuminate\Filesystem\FilesystemAdapter`. The `$config` variable contains the values defined in `config/filesystems.php` for the specified disk.

`extend` 메소드의 첫번째 인자는 드라이버의 이름이고, 두번째는 `$app` 과 `$config` 변수를 전달 받는 클로저가 됩니다. 이 클로저는 `Illuminate\Filesystem\FilesystemAdapter` 에 대한 인스턴스를 반환해야 합니다. `$config` 변수는 `config/filesystems.php` 파일 안에 정의된 디스크 값을 가지고 있습니다.

Once you have created and registered the extension's service provider, you may use the `dropbox` driver in your `config/filesystems.php` configuration file.

확장 기능의 서비스 프로바이더를 만들고 등록한 후 `config/filesystems.php` 설정 파일에서 `dropbox` 드라이버를 사용할 수 있습니다.
