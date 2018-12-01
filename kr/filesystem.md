# Filesystem / Cloud Storage
# 파일시스템 / 클라우드 스토리지

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정](#configuration)
- [Basic Usage](#basic-usage)
- [기본적인 사용법](#basic-usage)
    - [Obtaining Disk Instances](#obtaining-disk-instances)
    - [Disk 인스턴스 획득하기](#obtaining-disk-instances)
    - [Retrieving Files](#retrieving-files)
    - [파일 찾기](#retrieving-files)
    - [Storing Files](#storing-files)
    - [파일 저장하기](#storing-files)
    - [Deleting Files](#deleting-files)
    - [파일 삭제하기](#deleting-files)
    - [Directories](#directories)
    - [디렉토리들](#directories)
- [Custom Filesystems](#custom-filesystems)
- [사용자 지정 파일 시스템](#custom-filesystems)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel provides a powerful filesystem abstraction thanks to the wonderful [Flysystem](https://github.com/thephpleague/flysystem) PHP package by Frank de Jonge. The Laravel Flysystem integration provides simple to use drivers for working with local filesystems, Amazon S3, and Rackspace Cloud Storage. Even better, it's amazingly simple to switch between these storage options as the API remains the same for each system.

라라벨은 Frank de Jonge가 만든 [Flysystem](https://github.com/thephpleague/flysystem) 패키지를 기반으로 하는 강력한 추상화된 파일 시스템을 제공합니다. 라라벨의 통합된 파일 시스템은 로컬, 아마존 S3 그리고 Rackspace 클라우드 스토리지를 드라이버들을 통해서 간단하게 사용할 수 있게 제공합니다. 더욱 놀라운 것은, 각각의 시스템에 대해 동일한 API를 사용하고 있기 때문에 스토리지를 매우 쉽게 변경할 수 있다는 것입니다.

<a name="configuration"></a>
## Configuration
## 설정

The filesystem configuration file is located at `config/filesystems.php`. Within this file you may configure all of your "disks". Each disk represents a particular storage driver and storage location. Example configurations for each supported driver is included in the configuration file. So, simply modify the configuration to reflect your storage preferences and credentials.

파일시스템의 설정 파일은 `config/filesystems.php` 파일에 위치하고 있습니다. 이 파일 안에서 여러분은 여러분의 모든 “디스크”에 대해 설정할 수 있습니다. 각각의 디스크는 개별 스토리지 드라이버와 스토리지의 위치를 나타냅니다. 설정 파일에는 각각 지원하고 있는 드라이버에 대한 예제 설정이 들어 있습니다. 따라서 스토리지 구성 및 인증정보를 반영하도록 간단하게 설정을 수정할 수 있습니다.

Of course, you may configure as many disks as you like, and may even have multiple disks that use the same driver.

물론 여러분은 원하는만큼 디스크를 설정할 수 있으며, 동일한 드라이버에 대해 여러개의 디스크를 가질 수도 있습니다.

#### The Local Driver
#### 로컬 드라이버

When using the `local` driver, note that all file operations are relative to the `root` directory defined in your configuration file. By default, this value is set to the `storage/app` directory. Therefore, the following method would store a file in `storage/app/file.txt`:

`local` 드라이버를 사용하는 경우, 설정 파일에 정의되어있는 `root` 디렉토리를 기준으로 모든 파일이 상대적으로 조작된다는 것에 주의하십시오. 기본적으로 이 값은 `storage/app` 디렉토리로 설정 되어 있습니다. 따라서 다음의 코드는 `storage/app/file.txt` 에 파일을 저장합니다.

    Storage::disk('local')->put('file.txt', 'Contents');

#### Other Driver Prerequisites
#### 다른 드라이버 사용시 준비사항

Before using the S3 or Rackspace drivers, you will need to install the appropriate package via Composer:

S3 또는 Rackspace 드라이버를 사용하기 전에 여러분은 해당하는 패키지를 컴포저를 통해서 설치해야 합니다.

- Amazon S3: `league/flysystem-aws-s3-v3 ~1.0`
- Rackspace: `league/flysystem-rackspace ~1.0`

<a name="basic-usage"></a>
## Basic Usage
## 기본적인 사용법

<a name="obtaining-disk-instances"></a>
### Obtaining Disk Instances

The `Storage` facade may be used to interact with any of your configured disks. For example, you may use the `put` method on the facade to store an avatar on the default disk. If you call methods on the `Storage` facade without first calling the `disk` method, the method call will automatically be passed to the default disk:

`Storage` 파사드를 사용하면 설정된 디스크에 대한 작업을 처리할 수 있습니다. 예를 들어 사용자 아바타 이미지를 기본으로 설정된 디스크에 저장하기 위해서 `put` 메소드르 사용할 수 있습니다. `Storage` 파사드에서 `disk` 메소드 호출이 없을 경우에는 메소드는 자동으로 기본으로 설정된 디스크에 대해서 실행됩니다.

    <?php

    namespace App\Http\Controllers;

    use Storage;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Update the avatar for the given user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function updateAvatar(Request $request, $id)
        {
            $user = User::findOrFail($id);

            Storage::put(
                'avatars/'.$user->id,
                file_get_contents($request->file('avatar')->getRealPath())
            );
        }
    }

When using multiple disks, you may access a particular disk using the `disk` method on the `Storage` facade. Of course, you may continue to chain methods to execute methods on the disk:

다양한 디스크를 사용하는 경우에는 `Storage` 파사드의 `disk` 메소드를 사용하여 개별 디스크에 엑세스 할 수 있습니다. 당연하게도, 개별 디스크에서 메소드 체이닝 형태로 사용할 수도 있습니다.

    $disk = Storage::disk('s3');

    $contents = Storage::disk('local')->get('file.jpg');

<a name="retrieving-files"></a>
### Retrieving Files
### 파일 찾기

The `get` method may be used to retrieve the contents of a given file. The raw string contents of the file will be returned by the method:

`get` 메소드는 지정한 파일의 내용을 검색하는 데 사용합니다. 이 메소드는 파일의 내용을 그대로 돌려줍니다.

    $contents = Storage::get('file.jpg');

The `has` method may be used to determine if a given file exists on the disk:

`has` 메소드는 지정한 파일이 디스크에 존재하고 있는지를 확인하기 위해 사용합니다.

    $exists = Storage::disk('s3')->has('file.jpg');

#### File Meta Information
#### 파일 메타 정보

The `size` method may be used to get the size of the file in bytes:

`size` 메소드는 지정한 파일의 용량을 바이트 단위로 확인하는데 사용합니다.

    $size = Storage::size('file1.jpg');

The `lastModified` method returns the UNIX timestamp of the last time the file was modified:

`lastModified` 메소드는 마지막에 파일이 업데이트되었을 때의 UNIX 타임 스탬프값을 반환합니다.

    $time = Storage::lastModified('file1.jpg');

<a name="storing-files"></a>
### Storing Files
### 파일 저장하기

The `put` method may be used to store a file on disk. You may also pass a PHP `resource` to the `put` method, which will use Flysystem's underlying stream support. Using streams is greatly recommended when dealing with large files:

`put` 메소드는 디스크에 파일을 저장하는데 사용됩니다. 또한 `put` 메소드에 `resource` 를 전달하여 파일시스템의 스트림을 사용할 수도 있습니다. 큰 파일을 사용하는 경우 스트림을 사용하기를 권장합니다.

    Storage::put('file.jpg', $contents);

    Storage::put('file.jpg', $resource);

The `copy` method may be used to copy an existing file to a new location on the disk:

`copy` 메소드는 존재하는 파일을 디스크의 새로운 위치에 복사하는데 사용됩니다.

    Storage::copy('old/file1.jpg', 'new/file1.jpg');

The `move` method may be used to rename or move an existing file to a new location:

`move` 메소드는 존재하는 파일의 이름을 변경하거나 새로운 위치에 이동시키는데 사용됩니다.

    Storage::move('old/file1.jpg', 'new/file1.jpg');

#### Prepending / Appending To Files
#### 파일의 앞 / 뒤에 내용 추가하기

The `prepend` and `append` methods allow you to easily insert content at the beginning or end of a file:

`prepend` 와 `append` 메소드를 사용하면 파일의 처음과 끝에 새로운 내용을 손쉽게 추가할 수 있습니다.

    Storage::prepend('file.log', 'Prepended Text');

    Storage::append('file.log', 'Appended Text');

<a name="deleting-files"></a>
### Deleting Files
### 파일 삭제하기

The `delete` method accepts a single filename or an array of files to remove from the disk:

`delete` 메소드는 삭제할 하나의 파일 이름 또는 파일들의 배열을 인자로 받습니다.

    Storage::delete('file.jpg');

    Storage::delete(['file1.jpg', 'file2.jpg']);

<a name="directories"></a>
### Directories
### 디렉토리들

#### Get All Files Within A Directory
#### 디렉토리 안의 모든 파일들 확인하기

The `files` method returns an array of all of the files in a given directory. If you would like to retrieve a list of all files within a given directory including all sub-directories, you may use the `allFiles` method:

`files` 메소드는 주어진 디렉토리에 들어 있는 모든 파일들에 대한 배열을 반환합니다. 만약 지정된 디렉토리의 하위 디렉토리에 있는 모든 파일의 목록을 포함하기를 원한다면는 `allFiles` 메소드를 사용하면 됩니다.

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

Finally, the `deleteDirectory` may be used to remove a directory, including all of its files, from the disk:

마지막으로 `deleteDirectory` 는 디렉토리와 포함 된 모든 파일을 삭제하는 데 사용됩니다.

    Storage::deleteDirectory($directory);

<a name="custom-filesystems"></a>
## Custom Filesystems

Laravel's Flysystem integration provides drivers for several "drivers" out of the box; however, Flysystem is not limited to these and has adapters for many other storage systems. You can create a custom driver if you want to use one of these additional adapters in your Laravel application.

라라벨의 통합 파일시스템은 처음부터 다양한 “드라이버”가 제공됩니다. 하지만 파일시스템은 이러한 드라이버에 제한적이지 않고, 다은 스토리지 시스템에도 적용할 수 있습니다. 여러분은 라라벨 애플리케이션에 적합한 스토리지 시스템에 대한 사용자 정의 드라이버를 생성할 수 있습니다.

In order to set up the custom filesystem you will need to create a [service provider](/docs/{{version}}/providers) such as `DropboxServiceProvider`. In the provider's `boot` method, you may use the `Storage` facade's `extend` method to define the custom driver:

사용자 지정 파일 시스템을 구성하기 위해서는 `DropboxServiceProvider`와 같은 [서비스 프로바이더](/docs/{{version}}/providers)를 생성해야 할것입니다. 프로바이더의 `boot` 메소드에서 여러분은 `Storage` 파사드의 `extend` 메소드 사용자 지정 드라이버를 지정하는데 사용할 수 있습니다

    <?php

    namespace App\Providers;

    use Storage;
    use League\Flysystem\Filesystem;
    use Dropbox\Client as DropboxClient;
    use Illuminate\Support\ServiceProvider;
    use League\Flysystem\Dropbox\DropboxAdapter;

    class DropboxServiceProvider extends ServiceProvider
    {
        /**
         * Perform post-registration booting of services.
         *
         * @return void
         */
        public function boot()
        {
            Storage::extend('dropbox', function($app, $config) {
                $client = new DropboxClient(
                    $config['accessToken'], $config['clientIdentifier']
                );

                return new Filesystem(new DropboxAdapter($client));
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

The first argument of the `extend` method is the name of the driver and the second is a Closure that receives the `$app` and `$config` variables. The resolver Closure must return an instance of `League\Flysystem\Filesystem`. The `$config` variable contains the values defined in `config/filesystems.php` for the specified disk.

`extend` 메소드의 첫번째 인자는 드라이버의 이름이고, 두번째는 `$app` 과 `$config` 변수를 전달 받는 클로저가 됩니다. 이 클로저는 `League\Flysystem\Filesystem` 에 대한 인스턴스를 반환해야 합니다. `$config` 변수는 `config/filesystems.php` 파일 안에 정의된 디스크 값을 가지고 있습니다.

Once you have created the service provider to register the extension, you may use the `dropbox` driver in your `config/filesystem.php` configuration file.

확장된 드라이버를 등록하는 서비스 프로바이더를 생성한 뒤에, `config/filesystem.php` 설정 파일 안에서 `dropbox` 드라이버를 사용할 수 있습니다.
