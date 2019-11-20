# Filesystem / Cloud Storage
# 파일시스템 / 클라우드 스토리지

- [Introduction 소개](#introduction)
- [Configuration 설정](#configuration)
- [Basic Usage 기본적인 사용법](#basic-usage)
- [Custom Filesystems 사용자 지정 파일 시스템](#custom-filesystems)

<a name="introduction"></a>
## Introduction
## 소개

Laravel provides a wonderful filesystem abstraction thanks to the [Flysystem](https://github.com/thephpleague/flysystem) PHP package by Frank de Jonge. The Laravel Flysystem integration provides simple to use drivers for working with local filesystems, Amazon S3, and Rackspace Cloud Storage. Even better, it's amazingly simple to switch between these storage options as the API remains the same for each system!

라라벨은 Frank de Jonge가 만든 [Flysystem](https://github.com/thephpleague/flysystem) 패키지를 기반으로 하는 강력한 추상화된 파일 시스템을 제공합니다. 라라벨의 통합된 파일 시스템은 로컬, 아마존 S3 그리고 Rackspace 클라우드 스토리지를 드라이버들을 통해서 간단하게 사용할 수 있게 제공합니다. 더욱 놀라운 것은, 각각의 시스템에 대해 동일한 API를 사용하고 있기 때문에 스토리지를 매우 쉽게 변경할 수 있다는 것입니다!

<a name="configuration"></a>
## Configuration
## 설정

The filesystem configuration file is located at `config/filesystems.php`. Within this file you may configure all of your "disks". Each disk represents a particular storage driver and storage location. Example configurations for each supported driver is included in the configuration file. So, simply modify the configuration to reflect your storage preferences and credentials!

파일시스템의 설정 파일은 `config/filesystems.php` 파일에 위치하고 있습니다. 이 파일 안에서 여러분은 여러분의 모든 “디스크”에 대해 설정할 수 있습니다. 각각의 디스크는 개별 스토리지 드라이버와 스토리지의 위치를 나타냅니다. 설정 파일에는 각각 지원하고 있는 드라이버에 대한 예제 설정이 들어 있습니다. 따라서 스토리지 구성 및 인증정보를 반영하도록 간단하게 설정을 수정할 수 있습니다.

Before using the S3 or Rackspace drivers, you will need to install the appropriate package via Composer:

S3 또는 Rackspace 드라이버를 사용하기 전에 여러분은 해당하는 패키지를 컴포저를 통해서 설치해야 합니다.

- Amazon S3: `league/flysystem-aws-s3-v2 ~1.0`
- Rackspace: `league/flysystem-rackspace ~1.0`

Of course, you may configure as many disks as you like, and may even have multiple disks that use the same driver.

물론 여러분은 원하는만큼 디스크를 설정할 수 있으며, 동일한 드라이버에 대해 여러개의 디스크를 가질 수도 있습니다.

When using the `local` driver, note that all file operations are relative to the `root` directory defined in your configuration file. By default, this value is set to the `storage/app` directory. Therefore, the following method would store a file in `storage/app/file.txt`:

`local` 드라이버를 사용하는 경우, 설정 파일에 정의되어있는 `root` 디렉토리를 기준으로 모든 파일이 상대적으로 조작된다는 것에 주의하십시오. 기본적으로 이 값은 `storage/app` 디렉토리로 설정 되어 있습니다. 따라서 다음의 코드는 `storage/app/file.txt` 에 파일을 저장합니다.

	Storage::disk('local')->put('file.txt', 'Contents');

<a name="basic-usage"></a>
## Basic Usage
## 기본적인 사용법

The `Storage` facade may be used to interact with any of your configured disks. Alternatively, you may type-hint the `Illuminate\Contracts\Filesystem\Factory` contract on any class that is resolved via the Laravel [service container](/docs/{{version}}/container).

`Storage` 파사드를 사용하여 설정된 디스크에 대한 작업을 처리할 수 있습니다. 또한 라라벨의 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 의존성이 해결될 수 있도록 `Illuminate\Contracts\Filesystem\Factory` contract를 타입힌트에 사용할 수도 있습니다.

#### Retrieving A Particular Disk
#### 지정된 디스크 객체 반환하기

	$disk = Storage::disk('s3');

	$disk = Storage::disk('local');

#### Determining If A File Exists
#### 파일이 존재하는지 확인하기

	$exists = Storage::disk('s3')->exists('file.jpg');

#### Calling Methods On The Default Disk
#### 기본 스토리지에 파일이 존재하는지 확인하기

	if (Storage::exists('file.jpg'))
	{
		//
	}

#### Retrieving A File's Contents
#### 파일 내용 가져오기

	$contents = Storage::get('file.jpg');

#### Setting A File's Contents
#### 파일에 내용 작성하기

	Storage::put('file.jpg', $contents);

#### Prepend To A File
#### 파일의 앞부분에 내용 추가하기

	Storage::prepend('file.log', 'Prepended Text');

#### Append To A File
#### 파일의 마지막에 내용 추가하기

	Storage::append('file.log', 'Appended Text');

#### Delete A File
#### 파일 삭제하기

	Storage::delete('file.jpg');

	Storage::delete(['file1.jpg', 'file2.jpg']);

#### Copy A File To A New Location
#### 파일을 새로운 위치에 복사하기

	Storage::copy('old/file1.jpg', 'new/file1.jpg');

#### Move A File To A New Location
#### 파일을 새로운 위치로 이동하기

	Storage::move('old/file1.jpg', 'new/file1.jpg');

#### Get File Size
#### 파일 사이즈 가져오기

	$size = Storage::size('file1.jpg');

#### Get The Last Modification Time (UNIX)
#### 파일의 최근 수정일자 가져오기(UNIX)

	$time = Storage::lastModified('file1.jpg');

#### Get All Files Within A Directory
#### 지정한 디렉토리 안에 모든 파일들 가져오기

	$files = Storage::files($directory);

	// Recursive...
	$files = Storage::allFiles($directory);

#### Get All Directories Within A Directory
#### 지정한 디렉토리 안의 모든 디렉토리 가져오기

	$directories = Storage::directories($directory);

	// Recursive...
	$directories = Storage::allDirectories($directory);

#### Create A Directory
#### 디렉토리 생성하기

	Storage::makeDirectory($directory);

#### Delete A Directory
#### 디렉토리 삭제하기

	Storage::deleteDirectory($directory);

<a name="custom-filesystems"></a>
## Custom Filesystems
## 사용자 지정 파일 시스템

Laravel's Flysystem integration provides drivers for several "drivers" out of the box; however, Flysystem is not limited to these and has adapters for many other storage systems. You can create a custom driver if you want to use one of these additional adapters in your Laravel application. Don't worry, it's not too hard!

라라벨의 통합 파일시스템은 처음부터 다양한 “드라이버”가 제공됩니다. 하지만 파일시스템은 이러한 드라이버에 제한적이지 않고, 다은 스토리지 시스템에도 적용할 수 있습니다. 여러분은 라라벨 애플리케이션에 적합한 스토리지 시스템에 대한 사용자 정의 드라이버를 생성할 수 있습니다. 걱정마십시오. 그렇게 어렵지 않습니다.

In order to set up the custom filesystem you will need to create a service provider such as `DropboxFilesystemServiceProvider`. In the provider's `boot` method, you can inject an instance of the `Illuminate\Contracts\Filesystem\Factory` contract and call the `extend` method of the injected instance. Alternatively, you may use the `Disk` facade's `extend` method.

예를 들어 DropboxFilesystemServiceProvider 같은 만드는 사용자 정의 파일 시스템을 위해 서비스 제공자를 준비하십시오. 제공자의 boot 메소드 속으로, Illuminate \ Contracts \ Filesystem \ Factory 계약의 인스턴스를 주입하고 extend 메소드를 호출합니다. 혹은 Disk 외관 extend 메소드를 사용할 수 있습니다.

The first argument of the `extend` method is the name of the driver and the second is a Closure that receives the `$app` and `$config` variables. The resolver Closure must return an instance of `League\Flysystem\Filesystem`.

`extend` 메소드의 첫번째 인자는 드라이버의 이름이고, 두번째는 `$app` 과 `$config` 변수를 전달 받는 클로저가 됩니다. 이 클로저는 `League\Flysystem\Filesystem` 에 대한 인스턴스를 반환해야 합니다.

> **Note:** The $config variable will already contain the values defined in `config/filesystems.php` for the specified disk.

> **참고** $config 변수는 지정된 디스크를 위한 `config/filesystems.php` 파일에 정의된 값을 포함할 것입니다.

#### Dropbox Example
#### 드랍박스 예제

	<?php namespace App\Providers;

	use Storage;
	use League\Flysystem\Filesystem;
	use Dropbox\Client as DropboxClient;
	use League\Flysystem\Dropbox\DropboxAdapter;
	use Illuminate\Support\ServiceProvider;

	class DropboxFilesystemServiceProvider extends ServiceProvider {

		public function boot()
		{
			Storage::extend('dropbox', function($app, $config)
			{
				$client = new DropboxClient($config['accessToken'], $config['clientIdentifier']);

				return new Filesystem(new DropboxAdapter($client));
			});
		}

		public function register()
		{
			//
		}

	}
