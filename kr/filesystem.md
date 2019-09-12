# File Storage
# 파일 스토리지

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [The Public Disk](#the-public-disk)
    - [Public 디스크](#the-public-disk)
    - [The Local Driver](#the-local-driver)
    - [로컬 드라이버](#the-local-driver)
    - [Driver Prerequisites](#driver-prerequisites)
    - [드라이버 사용시 준비사항](#driver-prerequisites)
    - [Caching](#caching)
    - [캐싱](#caching)
- [Obtaining Disk Instances](#obtaining-disk-instances)
- [Disk 인스턴스 획득하기](#obtaining-disk-instances)
- [Retrieving Files](#retrieving-files)
- [파일 조회하기](#retrieving-files)
    - [Downloading Files](#downloading-files)
    - [파일 다운로드](#downloading-files)
    - [File URLs](#file-urls)
    - [파일 URL](#file-urls)
    - [File Metadata](#file-metadata)
    - [파일의 메타 데이터](#file-metadata)
- [Storing Files](#storing-files)
- [파일 저장하기](#storing-files)
    - [File Uploads](#file-uploads)
    - [파일 업로드](#file-uploads)
    - [File Visibility](#file-visibility)
    - [파일 Visibility](#file-visibility)
- [Deleting Files](#deleting-files)
- [파일 삭제하기](#deleting-files)
- [Directories](#directories)
- [디렉토리들](#directories)
- [Custom Filesystems](#custom-filesystems)
- [사용자 정의 파일 시스템](#custom-filesystems)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel provides a powerful filesystem abstraction thanks to the wonderful [Flysystem](https://github.com/thephpleague/flysystem) PHP package by Frank de Jonge. The Laravel Flysystem integration provides simple to use drivers for working with local filesystems and Amazon S3. Even better, it's amazingly simple to switch between these storage options as the API remains the same for each system.

라라벨은 Frank de Jonge가 만든 [Flysystem](https://github.com/thephpleague/flysystem) 패키지를 기반으로 하는 강력한 추상화된 파일 시스템을 제공합니다. 라라벨의 통합된 FlySystem은 로컬 파일시스템과 아마존 S3를 드라이버들을 통해서 간단하게 사용할 수 있게 제공합니다. 더욱 놀라운 것은, 각각의 시스템에 대해 동일한 API를 사용하고 있기 때문에 스토리지를 매우 쉽게 변경할 수 있다는 것입니다.

<a name="configuration"></a>
## Configuration
## 설정하기

The filesystem configuration file is located at `config/filesystems.php`. Within this file you may configure all of your "disks". Each disk represents a particular storage driver and storage location. Example configurations for each supported driver are included in the configuration file. So, modify the configuration to reflect your storage preferences and credentials.

파일시스템의 설정 파일은 `config/filesystems.php` 파일에 위치하고 있습니다. 이 파일 안에서 여러분은 여러분의 모든 “디스크”에 대해 설정할 수 있습니다. 각각의 디스크는 개별 스토리지 드라이버와 스토리지의 위치를 나타냅니다. 설정 파일에는 각각 지원하고 있는 드라이버에 대한 예제 설정이 들어 있습니다. 따라서 스토리지 구성 및 인증정보를 반영하도록 설정을 수정할 수 있습니다.

You may configure as many disks as you like, and may even have multiple disks that use the same driver.

여러분은 원하는만큼 디스크를 설정할 수 있으며, 동일한 드라이버에 대해 여러개의 디스크를 가질 수도 있습니다.

<a name="the-public-disk"></a>
### The Public Disk
### Public 디스크

The `public` disk is intended for files that are going to be publicly accessible. By default, the `public` disk uses the `local` driver and stores these files in `storage/app/public`. To make them accessible from the web, you should create a symbolic link from `public/storage` to `storage/app/public`. This convention will keep your publicly accessible files in one directory that can be easily shared across deployments when using zero down-time deployment systems like [Envoyer](https://envoyer.io).

`public` 디스크는 파일을 누구나 접근 가능하다는 것을 의도합니다. 기본적으로, `public` 디스크는 `local` 드라이버를 사용하고 파일들을 `storage/app/public` 에 저장합니다. 이것들을 웹에서 접근할 수 있도록 하려면, `public/storage` 를 `storage/app/public` 으로 심볼릭 링크를 생성해야 합니다. 이 방법으로 접근가능한 파일들은 [Envoyer](https://envoyer.io)로 무중단 배포를 사용하는 경우, 손쉽게 공유될 수 있습니다.

To create the symbolic link, you may use the `storage:link` Artisan command:

심볼릭 링크를 생성하기 위해서 `storage:link` 아티즌 명령어를 사용할 수 있습니다.

    php artisan storage:link

Once a file has been stored and the symbolic link has been created, you can create a URL to the files using the `asset` helper:

파일을 저장하고 심볼릭 링크가 생성되어 있다면, `asset` 헬퍼를 통해서 파일에 대한 URL을 생성할 수 있습니다.

    echo asset('storage/file.txt');

<a name="the-local-driver"></a>
### The Local Driver
### 로컬 드라이버

When using the `local` driver, all file operations are relative to the `root` directory defined in your `filesystems` configuration file. By default, this value is set to the `storage/app` directory. Therefore, the following method would store a file in `storage/app/file.txt`:

`local` 드라이버를 사용하는 경우, `filesystems` 설정 파일에 정의되어있는 `root` 디렉토리를 기준으로 모든 파일이 상대적으로 조작됩니다. 기본적으로 이 값은 `storage/app` 디렉토리로 설정 되어 있습니다. 따라서 다음의 코드는 `storage/app/file.txt` 에 파일을 저장합니다.

    Storage::disk('local')->put('file.txt', 'Contents');

#### Permissions
#### 권한

The `public` [visibility](#file-visibility) translates to `0755` for directories and `0644` for files. You can modify the permissions mappings in your `filesystems` configuration file:

`public`의 [파일 Visibility](#file-visibility)는 디렉토리의 경우 `0755`, 파일의 경우 `0644`로 변환됩니다. `filesystems` 설정 파일에서 권한 매핑을 수정할 수 있습니다.

    'local' => [
        'driver' => 'local',
        'root' => storage_path('app'),
        'permissions' => [
            'file' => [
                'public' => 0664,
                'private' => 0600,
            ],
            'dir' => [
                'public' => 0775,
                'private' => 0700,
            ],
        ],
    ],

<a name="driver-prerequisites"></a>
### Driver Prerequisites
### 드라이버 사용시 준비사항

#### Composer Packages
#### 컴포저 패키지

Before using the SFTP or S3 drivers, you will need to install the appropriate package via Composer:

SFTP 또는 S3 드라이버를 사용하기 전에 해당하는 패키지를 컴포저를 통해서 설치해야 합니다.

- SFTP: `league/flysystem-sftp ~1.0`
- Amazon S3: `league/flysystem-aws-s3-v3 ~1.0`

An absolute must for performance is to use a cached adapter. You will need an additional package for this:

캐싱된 어댑터를 사용하면 성능이 절대적으로 향상됩니다. 이 경우에는 추가 패키지를 필요로 합니다.

- CachedAdapter: `league/flysystem-cached-adapter ~1.0`

#### S3 Driver Configuration
#### S3 드라이버 설정하기

The S3 driver configuration information is located in your `config/filesystems.php` configuration file. This file contains an example configuration array for an S3 driver. You are free to modify this array with your own S3 configuration and credentials. For convenience, these environment variables match the naming convention used by the AWS CLI.

S3 드라이버 설정 정보는 `config/filesystems.php` 설정 파일안에 있습니다. 이 파일은 S3 드라이버 설정에 대한 예제 배열을 포함하고 있습니다. 여러분은 자유롭게 여러분의 S3 설정과 인증을 위해서 이 배열을 수정할 수 있습니다. 편의를 위해서, 이 환경설정값들은 AWS CLI를 사용하여 네이밍 컨벤션과 매칭됩니다.

#### FTP Driver Configuration
#### FTP 드라이버 설정하기

Laravel's Flysystem integrations works great with FTP; however, a sample configuration is not included with the framework's default `filesystems.php` configuration file. If you need to configure a FTP filesystem, you may use the example configuration below:

라라벨의 통합 Flysystem은 FTP를 통해서도 원활하게 동작합니다; 하지만 `filesystems.php` 설정 파일에는 기본적으로 이 설정 내용이 포함되어 있지 않습니다. 만약 FTP 파일 시스템 설정이 필요하다면, 다음과 같은 예제를 참고할 수 있습니다.

    'ftp' => [
        'driver'   => 'ftp',
        'host'     => 'ftp.example.com',
        'username' => 'your-username',
        'password' => 'your-password',

        // Optional FTP Settings...
        // 'port'     => 21,
        // 'root'     => '',
        // 'passive'  => true,
        // 'ssl'      => true,
        // 'timeout'  => 30,
    ],

#### SFTP Driver Configuration
#### SFTP 드라이버 설정하기

Laravel's Flysystem integrations works great with SFTP; however, a sample configuration is not included with the framework's default `filesystems.php` configuration file. If you need to configure a SFTP filesystem, you may use the example configuration below:

라라벨의 Flysystem은 SFTP를 통해서도 원할하게 동작합니다; 하지만 `filesystems.php` 설정 파일에는 기본적으로 이 설정 내용이 포함되어 있지 않습니다. 만약에 SFTP 파일 시스템 설정이 필요하다면, 다음과 같은 예제를 참고할 수 있습니다.

    'sftp' => [
        'driver' => 'sftp',
        'host' => 'example.com',
        'username' => 'your-username',
        'password' => 'your-password',

        // Settings for SSH key based authentication...
        // 'privateKey' => '/path/to/privateKey',
        // 'password' => 'encryption-password',

        // Optional SFTP Settings...
        // 'port' => 22,
        // 'root' => '',
        // 'timeout' => 30,
    ],

<a name="caching"></a>
### Caching
### 캐싱

To enable caching for a given disk, you may add a `cache` directive to the disk's configuration options. The `cache` option should be an array of caching options containing the `disk` name, the `expire` time in seconds, and the cache `prefix`:

주어진 디스크의 캐싱을 활성화 하려면, 디스크의 설정 옵션에 `cache` 지시어를 추가하면 됩니다. `cache` 옵션은 `disk` 이름, `expire` 시간 (초) 그리고 캐시 `prefix`로 구성된 배열형태여야 합니다.

    's3' => [
        'driver' => 's3',

        // Other Disk Options...

        'cache' => [
            'store' => 'memcached',
            'expire' => 600,
            'prefix' => 'cache-prefix',
        ],
    ],

<a name="obtaining-disk-instances"></a>
## Obtaining Disk Instances
## 디스크 인스턴스 획득하기

The `Storage` facade may be used to interact with any of your configured disks. For example, you may use the `put` method on the facade to store an avatar on the default disk. If you call methods on the `Storage` facade without first calling the `disk` method, the method call will automatically be passed to the default disk:

`Storage` 파사드를 사용하면 설정된 디스크에 대한 작업을 처리할 수 있습니다. 예를 들어 사용자 아바타 이미지를 기본으로 설정된 디스크에 저장하기 위해서 `put` 메소드르 사용할 수 있습니다. `Storage` 파사드에서 `disk` 메소드 호출이 없을 경우에는 메소드는 자동으로 기본으로 설정된 디스크에 대해서 실행됩니다.

    use Illuminate\Support\Facades\Storage;

    Storage::put('avatars/1', $fileContents);

If your application interacts with multiple disks, you may use the `disk` method on the `Storage` facade to work with files on a particular disk:

만약 여러분의 애플리케이션이 여러개의 디스크를 다룬다면, `Storage` 파사드의 `disk` 메소드를 사용하여 개별 디스크의 파일에 대해서 작업을 수행할 수 있습니다.

    Storage::disk('s3')->put('avatars/1', $fileContents);

<a name="retrieving-files"></a>
## Retrieving Files
## 파일 조회하기

The `get` method may be used to retrieve the contents of a file. The raw string contents of the file will be returned by the method. Remember, all file paths should be specified relative to the "root" location configured for the disk:

`get` 메소드는 파일의 내용을 검색하는 데 사용합니다. 이 메소드는 파일의 내용을 그대로 돌려줍니다. 주의할 점은 모든 파일의 패스는 디스크에 설정된 "루트" 와 상대 경로로 지정되어 져야 한다는 것입니다.

    $contents = Storage::get('file.jpg');

The `exists` method may be used to determine if a file exists on the disk:

`exists` 메소드는 파일이 디스크에 존재하고 있는지를 확인하기 위해 사용합니다.

    $exists = Storage::disk('s3')->exists('file.jpg');

<a name="downloading-files"></a>
### Downloading Files
### 파일 다운로드

The `download` method may be used to generate a response that forces the user's browser to download the file at the given path. The `download` method accepts a file name as the second argument to the method, which will determine the file name that is seen by the user downloading the file. Finally, you may pass an array of HTTP headers as the third argument to the method:

`download` 메소드는 브라우저에서 주어진 경로의 파일에 대한 다운로드를 수행하는 응답-response를 생성합니다. `download` 메소드는 다운로드 받을 때 결정되는 파일 이름을 두번째 인자로 전달받습니다. 그리고 세번째 인자로 HTTP 헤더 배열을 인자로 받습니다.

    return Storage::download('file.jpg');

    return Storage::download('file.jpg', $name, $headers);

<a name="file-urls"></a>
### File URLs
### 파일 URL

You may use the `url` method to get the URL for the given file. If you are using the `local` driver, this will typically just prepend `/storage` to the given path and return a relative URL to the file. If you are using the `s3` driver, the fully qualified remote URL will be returned:

주어진 파일의 URL을 얻기 위해서 `url` 메소드를 사용할 수 있습니다. `local` 드라이버를 사용중이라면 일반적으로 주어진 경로 앞에 `/storage` 를 덧붙여서 파일에 대한 URL이 반환됩니다. `s3` 드라이버를 사용중이라면 전체 URL이 반환됩니다.

    use Illuminate\Support\Facades\Storage;

    $url = Storage::url('file.jpg');

> {note} Remember, if you are using the `local` driver, all files that should be publicly accessible should be placed in the `storage/app/public` directory. Furthermore, you should [create a symbolic link](#the-public-disk) at `public/storage` which points to the `storage/app/public` directory.

> {note} 주의할 점은, 만약 `local` 드라이버를 사용하는 경우, 공개적으로 접근이 가능한 모든 파일들은 `storage/app/public` 디렉토리 안에 위치해야 한다는 것입니다. 또한 `storage/app/public` 디렉토리를 가리키는 `public/storage` [심볼릭 링크](#the-public-disk)를 생성해야 합니다.

#### Temporary URLs
#### 임시 URLs

For files stored using the `s3` you may create a temporary URL to a given file using the `temporaryUrl` method. This methods accepts a path and a `DateTime` instance specifying when the URL should expire:

`s3`를 사용하여 파일을 저장할 때, `temporaryUrl` 메소드를 사용하여 파일에 접근하기 위한 임시 URL을 생성할 수 있습니다. 이 메소드는 파일의 경로 및 언제 URL이 만료되어야 하는지 지정한 `DateTime` 인스턴스를 인자로 받습니다.

    $url = Storage::temporaryUrl(
        'file.jpg', now()->addMinutes(5)
    );

If you need to specify additional [S3 request parameters](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html#RESTObjectGET-requests), you may pass the array of request parameters as the third argument to the `temporaryUrl` method:

추가 [S3 요청 파라메터](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html#RESTObjectGET-requests)를 지정해야하는 경우 요청 파라메터 배열을 `temporaryUrl` 메소드의 인수의 세 번째 파라메터로 전달할 수 있습니다.

    $url = Storage::temporaryUrl(
        'file.jpg',
        now()->addMinutes(5),
        ['ResponseContentType' => 'application/octet-stream']
    );

#### Local URL Host Customization
#### 로컬 URL 호스트 커스터마이징

If you would like to pre-define the host for files stored on a disk using the `local` driver, you may add a `url` option to the disk's configuration array:

`local` 드라이버를 사용하여 디스크에 저장된 파일을 위한 호스트를 미리 정의하고자 한다면, 디스크 설정 배열에 `url` 옵션을 추가하면 됩니다.

    'public' => [
        'driver' => 'local',
        'root' => storage_path('app/public'),
        'url' => env('APP_URL').'/storage',
        'visibility' => 'public',
    ],


<a name="file-metadata"></a>
### File Metadata
### 파일 메타 데이터

In addition to reading and writing files, Laravel can also provide information about the files themselves. For example, the `size` method may be used to get the size of the file in bytes:

파일을 읽고 쓰는 것 이외도 라라벨은 파일 자체에 대한 정보도 제공할 수 있습니다. 예를 들어, `size` 메소드는 파일 크기를 바이트로 조회하기 위해서 사용합니다.

    use Illuminate\Support\Facades\Storage;

    $size = Storage::size('file.jpg');

The `lastModified` method returns the UNIX timestamp of the last time the file was modified:

`lastModified` 메소드는 마지막에 파일이 업데이트되었을 때의 UNIX 타임 스탬프값을 반환합니다:

    $time = Storage::lastModified('file.jpg');

<a name="storing-files"></a>
## Storing Files
## 파일 저장하기

The `put` method may be used to store raw file contents on a disk. You may also pass a PHP `resource` to the `put` method, which will use Flysystem's underlying stream support. Using streams is greatly recommended when dealing with large files:

`put` 메소드는 파일의 내용을 디스크에 저장하는데 사용됩니다. 또한 `put` 메소드에 `resource` 를 전달하여 파일시스템의 스트림을 사용할 수도 있습니다. 큰 파일을 사용하는 경우 스트림을 사용하기를 권장합니다.

    use Illuminate\Support\Facades\Storage;

    Storage::put('file.jpg', $contents);

    Storage::put('file.jpg', $resource);

#### Automatic Streaming
#### 자동 스트리밍

If you would like Laravel to automatically manage streaming a given file to your storage location, you may use the `putFile` or `putFileAs` method. This method accepts either a `Illuminate\Http\File` or `Illuminate\Http\UploadedFile` instance and will automatically stream the file to your desired location:

만약 주어진 파일이 여러분의 스토리지 위치에 자동적으로 스트리밍되도록 라라벨이 관리하기를 바란다면 `putFile` 이나 `putFileAs` 메소드를 사용하면 됩니다. 이 메소드는 `Illuminate\Http\File` 이나 `Illuminate\Http\UploadedFile` 인스턴스를 인자로 받아들이고 자동으로 명시한 위치에 파일을 스트림 처리할 것입니다.

    use Illuminate\Http\File;
    use Illuminate\Support\Facades\Storage;

    // Automatically generate a unique ID for file name...
    Storage::putFile('photos', new File('/path/to/photo'));

    // Manually specify a file name...
    Storage::putFileAs('photos', new File('/path/to/photo'), 'photo.jpg');

There are a few important things to note about the `putFile` method. Note that we only specified a directory name, not a file name. By default, the `putFile` method will generate a unique ID to serve as the file name. The file's extension will be determined by examining the file's MIME type. The path to the file will be returned by the `putFile` method so you can store the path, including the generated file name, in your database.

`putFile` 메소드에는 몇가지 중요한 사항들이 있습니다. 파일 이름이 아니라 디렉토리 이름을 지정하는 것에 유의하십시오. 기본적으로 `putFile` 메소드는 파일이름을 기반으로 한 고유한 ID를 생성합니다. 파일의 확장자는 파일의 MIME 타입에 의해서 결정됩니다. `putFile` 메소드에 의해서 파일의 경로가 반환 될것이기 때문에, 이 경로에 생성된 파일 이름을 포함하여 데이터베이스에 저장할 수 있습니다.

The `putFile` and `putFileAs` methods also accept an argument to specify the "visibility" of the stored file. This is particularly useful if you are storing the file on a cloud disk such as S3 and would like the file to be publicly accessible:

`putFile` 과 `putFileAs` 메소드는 또한 저장되는 파일의 "visibility"를 지정하는 인자를 받아들입니다. 이는 특히 S3 와 같은 클라우드 디스크에 파일을 저장하고 파일의 접근 권한을 설정하고자 할 때 유용합니다.

    Storage::putFile('photos', new File('/path/to/photo'), 'public');

#### Prepending & Appending To Files
#### 파일의 앞에 내용 추가하기 & 뒤에 내용 추가하기

The `prepend` and `append` methods allow you to write to the beginning or end of a file:

`prepend` 와 `append` 메소드를 사용하면 파일의 처음과 끝에 내용을 추가할 수 있습니다.

    Storage::prepend('file.log', 'Prepended Text');

    Storage::append('file.log', 'Appended Text');

#### Copying & Moving Files
#### 파일의 복사 & 이동

The `copy` method may be used to copy an existing file to a new location on the disk, while the `move` method may be used to rename or move an existing file to a new location:

`copy` 메소드는 존재하는 파일을 디스크의 새로운 위치에 복사하는데 사용되고, `move` 메소드는 존재하는 파일의 이름을 변경하거나 새로운 위치에 이동시키는데 사용됩니다.

    Storage::copy('old/file.jpg', 'new/file.jpg');

    Storage::move('old/file.jpg', 'new/file.jpg');

<a name="file-uploads"></a>
### File Uploads
### 파일 업로드

In web applications, one of the most common use-cases for storing files is storing user uploaded files such as profile pictures, photos, and documents. Laravel makes it very easy to store uploaded files using the `store` method on an uploaded file instance. Call the `store` method with the path at which you wish to store the uploaded file:

웹 애플리케이션에서 파일을 저장하는 가장 공통적인 사용예 중 하나는 프로필 이미지, 사진, 그리고 문서와 같은 사용자가 업로드한 파일을 저장하는 것입니다. 라라벨은 업로드된 파일 인스턴스에서 `store` 메소드를 사용하여 업로드한 파일이 저장되는 것을 아주 쉽게 처리합니다. 저장된 파일이 저장되고자 하는 경로를 넘겨주며 `store` 메소드를 호출하면 됩니다.

    <?php

    namespace App\Http\Controllers;

    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

    class UserAvatarController extends Controller
    {
        /**
         * Update the avatar for the user.
         *
         * @param  Request  $request
         * @return Response
         */
        public function update(Request $request)
        {
            $path = $request->file('avatar')->store('avatars');

            return $path;
        }
    }

There are a few important things to note about this example. Note that we only specified a directory name, not a file name. By default, the `store` method will generate a unique ID to serve as the file name. The file's extension will be determined by examining the file's MIME type. The path to the file will be returned by the `store` method so you can store the path, including the generated file name, in your database.

이 예제에서는 몇가지 중요한 사항들이 있습니다. 예제에서 파일 이름이 아니라 디렉토리 이름을 지정하는 것에 유의하십시오. 기본적으로 `store` 메소드는 파일이름을 기반으로 한 고유한 ID를 생성합니다. 파일의 확장자는 파일의 MIME 타입에 의해서 결정됩니다. `store` 메소드에 의해서 파일의 경로가 반환 될 것이기 때문에, 이 경로에 생성된 파일 이름을 포함하여 데이터베이스에 저장할 수 있습니다.

You may also call the `putFile` method on the `Storage` facade to perform the same file manipulation as the example above:

또한 다음 예제와 같이 `Storage` 파사드의 `putFile` 메소드를 호출하여 동일한 파일 조작을 수행할 수 있습니다.

    $path = Storage::putFile('avatars', $request->file('avatar'));

#### Specifying A File Name
#### 파일 이름 지정하기

If you would not like a file name to be automatically assigned to your stored file, you may use the `storeAs` method, which receives the path, the file name, and the (optional) disk as its arguments:

저장되는 파일에 이름이 자동으로 지정되지 않게 하고 싶다면, `storeAs` 메소드에 파일의 경로와 이름 그리고 (선택적으로) 디스크를 인자로 전달하여 사용할 수 있습니다.

    $path = $request->file('avatar')->storeAs(
        'avatars', $request->user()->id
    );

You may also use the `putFileAs` method on the `Storage` facade, which will perform the same file manipulation as the example above:

다음의 예제와 같이 동일하게 `Storage` 파사드의 `putFileAs` 메소드를 사용하여 파일 조작을 수행할 수도 있습니다.

    $path = Storage::putFileAs(
        'avatars', $request->file('avatar'), $request->user()->id
    );

> {note} Unprintable and invalid unicode characters will automatically be removed from file paths. Therefore, you may wish to sanitize your file paths before passing them to Laravel's file storage methods. File paths are normalized using the `League\Flysystem\Util::normalizePath` method.

> {note} 출력할 수 없거나 유효하지 않은 유니코드 문자는 파일 경로에서 자동으로 삭제됩니다. 그러므로 파일 경로를 라라벨 파일 저장 메소드에 전달하기 전에 sanitize 하시기 바랍니다. 파일 경로는 `League\Flysystem\Util::normalizePath` 메소드로 정규화됩니다.

#### Specifying A Disk
#### 디스크 지정하기

By default, this method will use your default disk. If you would like to specify another disk, pass the disk name as the second argument to the `store` method:

기본적으로, 이 메소드는 기본 디스크를 사용할 것입니다. 만약 여러분이 다른 디스크를 지정하고자 한다면, `store` 메소드의 두번째 인자로 디스크의 이름을 전달하면 됩니다.

    $path = $request->file('avatar')->store(
        'avatars/'.$request->user()->id, 's3'
    );

<a name="file-visibility"></a>
### File Visibility
### 파일 Visibility

In Laravel's Flysystem integration, "visibility" is an abstraction of file permissions across multiple platforms. Files may either be declared `public` or `private`. When a file is declared `public`, you are indicating that the file should generally be accessible to others. For example, when using the S3 driver, you may retrieve URLs for `public` files.

라라벨의 Flysystem 구성에서, "visibility" 는 다양한 플랫폼에서 파일에 대한 권한의 추상화 입니다. 파일들은 `public` 이거나 `private` 로 지정될 수 있습니다. 파일을 `public` 으로 선언하면, 다른 사람들 일반적으로 파일에 접근할 수 있다는 것을 나타냅니다. 예를 들어 S3 드라이버를 사용할 때, `public` 파일들에 대해서 URL을 조회할 수 있습니다.

You can set the visibility when setting the file via the `put` method:

`put` 메소드를 통해서 파일을 설정할 때 visibility를 지정할 수 있습니다.

    use Illuminate\Support\Facades\Storage;

    Storage::put('file.jpg', $contents, 'public');

If the file has already been stored, its visibility can be retrieved and set via the `getVisibility` and `setVisibility` methods:

만약 파일이 이미 저장되어 있다면, 이 파일의 visibility 는 `getVisibility` 와 `setVisibility`를 통해서 조회하고 설정할 수 있습니다:

    $visibility = Storage::getVisibility('file.jpg');

    Storage::setVisibility('file.jpg', 'public')

<a name="deleting-files"></a>
## Deleting Files
## 파일 삭제하기

The `delete` method accepts a single filename or an array of files to remove from the disk:

`delete` 메소드는 삭제할 하나의 파일 이름 또는 파일들의 배열을 인자로 받습니다.

    use Illuminate\Support\Facades\Storage;

    Storage::delete('file.jpg');

    Storage::delete(['file.jpg', 'file2.jpg']);

If necessary, you may specify the disk that the file should be deleted from:

필요한 경우, 파일이 어디에서 지워져야 하는지 디스크를 지정할 수 있습니다.

    use Illuminate\Support\Facades\Storage;

    Storage::disk('s3')->delete('folder_path/file_name.jpg');

<a name="directories"></a>
## Directories
## 디렉토리들

#### Get All Files Within A Directory
#### 디렉토리 안의 모든 파일들 확인하기

The `files` method returns an array of all of the files in a given directory. If you would like to retrieve a list of all files within a given directory including all sub-directories, you may use the `allFiles` method:

`files` 메소드는 주어진 디렉토리에 들어 있는 모든 파일들에 대한 배열을 반환합니다. 만약 지정된 디렉토리의 하위 디렉토리에 있는 모든 파일의 목록을 포함하기를 원한다면는 `allFiles` 메소드를 사용하면 됩니다.

    use Illuminate\Support\Facades\Storage;

    $files = Storage::files($directory);

    $files = Storage::allFiles($directory);

#### Get All Directories Within A Directory
#### 디렉토리에 들어 있는 모든 하위 디렉토리들 확인하기

The `directories` method returns an array of all the directories within a given directory. Additionally, you may use the `allDirectories` method to get a list of all directories within a given directory and all of its sub-directories:

`directories` 메소드는 주어진 디렉토리에 들어 있는 전체 디렉토리들에 대한 배열을 반환합니다. 추가적으로 지정된 디렉토리의 하위 디렉토리 및 하위 디렉토리 아래의 모든 디렉토리들에 대한 목록을 포함하기를 원한다면는 `allDirectories` 메소드를 사용하면 됩니다.

    $directories = Storage::directories($directory);

    // Recursive...
    $directories = Storage::allDirectories($directory);

#### Create A Directory
#### 디렉토리 생성하기

The `makeDirectory` method will create the given directory, including any needed sub-directories:

`makeDirectory` 메소드는 필요한 하위 디렉토리를 포함하여 지정된 디렉토리를 만들 것입니다.

    Storage::makeDirectory($directory);

#### Delete A Directory
#### 디렉토리 삭제하기

Finally, the `deleteDirectory` method may be used to remove a directory and all of its files:

마지막으로 `deleteDirectory`메소드는 디렉토리와 포함 된 모든 파일을 삭제하는 데 사용됩니다.

    Storage::deleteDirectory($directory);

<a name="custom-filesystems"></a>
## Custom Filesystems
## 커스텀(사용자 정의) 파일 시스템

Laravel's Flysystem integration provides drivers for several "drivers" out of the box; however, Flysystem is not limited to these and has adapters for many other storage systems. You can create a custom driver if you want to use one of these additional adapters in your Laravel application.

라라벨의 통합 FlySystem은 처음부터 다양한 “드라이버”가 제공됩니다. 하지만 파일시스템은 이러한 드라이버에 제한적이지 않고, 다은 스토리지 시스템에도 적용할 수 있습니다. 여러분은 라라벨 애플리케이션에 적합한 스토리지 시스템에 대한 사용자 정의 드라이버를 생성할 수 있습니다.

In order to set up the custom filesystem you will need a Flysystem adapter. Let's add a community maintained Dropbox adapter to our project:

커스텀(사용자 정의) 파일 시스템을 구성하기 위해서는 Flysystem 어댑터가 필요합니다. 커뮤니티에서 관리되고 있는 Dropbox 어댑터를 프로젝트에 추가해보겠습니다.

    composer require spatie/flysystem-dropbox

Next, you should create a [service provider](/docs/{{version}}/providers) such as `DropboxServiceProvider`. In the provider's `boot` method, you may use the `Storage` facade's `extend` method to define the custom driver:

그 다음에 `DropboxServiceProvider`와 같은 [서비스 프로바이더](/docs/{{version}}/providers)를 생성해야 합니다. 프로바이더의 `boot` 메소드에서 `Storage` 파사드의 `extend` 메소드를 사용하여 커스텀 드라이버를 정의할 수 있습니다.

    <?php

    namespace App\Providers;

    use Storage;
    use League\Flysystem\Filesystem;
    use Illuminate\Support\ServiceProvider;
    use Spatie\Dropbox\Client as DropboxClient;
    use Spatie\FlysystemDropbox\DropboxAdapter;

    class DropboxServiceProvider extends ServiceProvider
    {
        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            //
        }

        /**
         * Bootstrap any application services.
         *
         * @return void
         */
        public function boot()
        {
            Storage::extend('dropbox', function ($app, $config) {
                $client = new DropboxClient(
                    $config['authorization_token']
                );

                return new Filesystem(new DropboxAdapter($client));
            });
        }
    }

The first argument of the `extend` method is the name of the driver and the second is a Closure that receives the `$app` and `$config` variables. The resolver Closure must return an instance of `League\Flysystem\Filesystem`. The `$config` variable contains the values defined in `config/filesystems.php` for the specified disk.

`extend` 메소드의 첫번째 인자는 드라이버의 이름이고, 두번째는 `$app` 과 `$config` 변수를 전달 받는 클로저가 됩니다. 이 클로저는 `League\Flysystem\Filesystem` 에 대한 인스턴스를 반환해야 합니다. `$config` 변수는 `config/filesystems.php` 파일 안에 정의된 디스크 값을 가지고 있습니다.

Next, register the service provider in your `config/app.php` configuration file:

다음으로 `config/app.php` 설정 파일에 서비스 프로바이더를 등록하십시오.

    'providers' => [
        // ...
        App\Providers\DropboxServiceProvider::class,
    ];

Once you have created and registered the extension's service provider, you may use the `dropbox` driver in your `config/filesystems.php` configuration file.

확장 기능의 서비스 프로바이더를 만들고 등록한 후 `config/filesystems.php` 설정 파일에서 `dropbox` 드라이버를 사용할 수 있습니다.
