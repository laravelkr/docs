# 파일 스토리지

- [시작하기](#introduction)
- [설정하기](#configuration)
    - [Public 디스크](#the-public-disk)
    - [로컬 드라이버](#the-local-driver)
    - [드라이버 사용시 준비사항](#driver-prerequisites)
    - [캐싱](#caching)
- [Disk 인스턴스 획득하기](#obtaining-disk-instances)
- [파일 조회하기](#retrieving-files)
    - [파일 다운로드](#downloading-files)
    - [파일 URL](#file-urls)
    - [파일의 메타 데이터](#file-metadata)
- [파일 저장하기](#storing-files)
    - [파일 업로드](#file-uploads)
    - [파일 Visibility](#file-visibility)
- [파일 삭제하기](#deleting-files)
- [디렉토리들](#directories)
- [사용자 정의 파일 시스템](#custom-filesystems)

<a name="introduction"></a>
## 시작하기

라라벨은 Frank de Jonge가 만든 [Flysystem](https://github.com/thephpleague/flysystem) 패키지를 기반으로 하는 강력한 추상화된 파일 시스템을 제공합니다. 라라벨의 통합된 FlySystem은 로컬 파일시스템과 아마존 S3를 드라이버들을 통해서 간단하게 사용할 수 있게 제공합니다. 더욱 놀라운 것은, 각각의 시스템에 대해 동일한 API를 사용하고 있기 때문에 스토리지를 매우 쉽게 변경할 수 있다는 것입니다.

<a name="configuration"></a>
## 설정하기

파일시스템의 설정 파일은 `config/filesystems.php` 파일에 위치하고 있습니다. 이 파일 안에서 여러분은 여러분의 모든 “디스크”에 대해 설정할 수 있습니다. 각각의 디스크는 개별 스토리지 드라이버와 스토리지의 위치를 나타냅니다. 설정 파일에는 각각 지원하고 있는 드라이버에 대한 예제 설정이 들어 있습니다. 따라서 스토리지 구성 및 인증정보를 반영하도록 설정을 수정할 수 있습니다.

여러분은 원하는만큼 디스크를 설정할 수 있으며, 동일한 드라이버에 대해 여러개의 디스크를 가질 수도 있습니다.

<a name="the-public-disk"></a>
### Public 디스크

`public` 디스크는 파일을 누구나 접근 가능하다는 것을 의도합니다. 기본적으로, `public` 디스크는 `local` 드라이버를 사용하고 파일들을 `storage/app/public` 에 저장합니다. 이것들을 웹에서 접근할 수 있도록 하려면, `public/storage` 를 `storage/app/public` 으로 심볼릭 링크를 생성해야 합니다. 이 방법으로 접근가능한 파일들은 [Envoyer](https://envoyer.io)로 무중단 배포를 사용하는 경우, 손쉽게 공유될 수 있습니다.

심볼릭 링크를 생성하기 위해서 `storage:link` 아티즌 명령어를 사용할 수 있습니다.

    php artisan storage:link

파일을 저장하고 심볼릭 링크가 생성되어 있다면, `asset` 헬퍼를 통해서 파일에 대한 URL을 생성할 수 있습니다.

    echo asset('storage/file.txt');

`filesystems`설정 파일에서 추가적인 심볼릭 링크를 구성 할 수 있습니다. `storage:link` 커맨드를 실행할 때 설정한 각 링크가 생성됩니다.

    'links' => [
        public_path('storage') => storage_path('app/public'),
        public_path('images') => storage_path('app/images'),
    ],

<a name="the-local-driver"></a>
### 로컬 드라이버

`local` 드라이버를 사용하는 경우, `filesystems` 설정 파일에 정의되어있는 `root` 디렉토리를 기준으로 모든 파일이 상대적으로 조작됩니다. 기본적으로 이 값은 `storage/app` 디렉토리로 설정 되어 있습니다. 따라서 다음의 코드는 `storage/app/file.txt` 에 파일을 저장합니다.

    Storage::disk('local')->put('file.txt', 'Contents');

#### 권한

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
### 드라이버 사용시 준비사항

#### 컴포저 패키지

SFTP 또는 S3 드라이버를 사용하기 전에 해당하는 패키지를 컴포저를 통해서 설치해야 합니다.

- SFTP: `league/flysystem-sftp ~1.0`
- Amazon S3: `league/flysystem-aws-s3-v3 ~1.0`

캐싱된 어댑터를 사용하면 성능이 절대적으로 향상됩니다. 이 경우에는 추가 패키지를 필요로 합니다.

#### S3 드라이버 설정하기

S3 드라이버 설정 정보는 `config/filesystems.php` 설정 파일안에 있습니다. 이 파일은 S3 드라이버 설정에 대한 예제 배열을 포함하고 있습니다. 여러분은 자유롭게 여러분의 S3 설정과 인증을 위해서 이 배열을 수정할 수 있습니다. 편의를 위해서, 이 환경설정값들은 AWS CLI를 사용하여 네이밍 컨벤션과 매칭됩니다.

#### FTP 드라이버 설정하기

라라벨의 통합 Flysystem은 FTP를 통해서도 원활하게 동작합니다; 하지만 `filesystems.php` 설정 파일에는 기본적으로 이 설정 내용이 포함되어 있지 않습니다. 만약 FTP 파일 시스템 설정이 필요하다면, 다음과 같은 예제를 참고할 수 있습니다.

    'ftp' => [
        'driver' => 'ftp',
        'host' => 'ftp.example.com',
        'username' => 'your-username',
        'password' => 'your-password',

        // Optional FTP Settings...
        // 'port' => 21,
        // 'root' => '',
        // 'passive' => true,
        // 'ssl' => true,
        // 'timeout' => 30,
    ],

#### SFTP 드라이버 설정하기

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
### 캐싱

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
## 디스크 인스턴스 획득하기

`Storage` 파사드를 사용하면 설정된 디스크에 대한 작업을 처리할 수 있습니다. 예를 들어 사용자 아바타 이미지를 기본으로 설정된 디스크에 저장하기 위해서 `put` 메소드르 사용할 수 있습니다. `Storage` 파사드에서 `disk` 메소드 호출이 없을 경우에는 메소드는 자동으로 기본으로 설정된 디스크에 대해서 실행됩니다.

    use Illuminate\Support\Facades\Storage;

    Storage::put('avatars/1', $fileContents);

만약 여러분의 애플리케이션이 여러개의 디스크를 다룬다면, `Storage` 파사드의 `disk` 메소드를 사용하여 개별 디스크의 파일에 대해서 작업을 수행할 수 있습니다.

    Storage::disk('s3')->put('avatars/1', $fileContents);

<a name="retrieving-files"></a>
## 파일 조회하기

`get` 메소드는 파일의 내용을 검색하는 데 사용합니다. 이 메소드는 파일의 내용을 그대로 돌려줍니다. 주의할 점은 모든 파일의 패스는 디스크에 설정된 "루트" 와 상대 경로로 지정되어 져야 한다는 것입니다.

    $contents = Storage::get('file.jpg');

`exists` 메소드는 파일이 디스크에 존재하고 있는지를 확인하기 위해 사용합니다.

    $exists = Storage::disk('s3')->exists('file.jpg');

`missing` 메소드는 파일이 디스크에서 누락되었는지 판별하는 데 사용될 수 있습니다.

    $missing = Storage::disk('s3')->missing('file.jpg');

<a name="downloading-files"></a>
### 파일 다운로드

`download` 메소드는 브라우저에서 주어진 경로의 파일에 대한 다운로드를 수행하는 응답-response를 생성합니다. `download` 메소드는 다운로드 받을 때 결정되는 파일 이름을 두번째 인자로 전달받습니다. 그리고 세번째 인자로 HTTP 헤더 배열을 인자로 받습니다.

    return Storage::download('file.jpg');

    return Storage::download('file.jpg', $name, $headers);

<a name="file-urls"></a>
### 파일 URL

주어진 파일의 URL을 얻기 위해서 `url` 메소드를 사용할 수 있습니다. `local` 드라이버를 사용중이라면 일반적으로 주어진 경로 앞에 `/storage` 를 덧붙여서 파일에 대한 URL이 반환됩니다. `s3` 드라이버를 사용중이라면 전체 URL이 반환됩니다.

    use Illuminate\Support\Facades\Storage;

    $url = Storage::url('file.jpg');

`local` 드라이버를 사용하는 경우, 공개적으로 접근이 가능한 모든 파일들은 `storage/app/public` 디렉토리 안에 위치해야 합니다. 또한 `storage/app/public` 디렉토리를 가리키는 `public/storage` [심볼릭 링크](#the-public-disk)를 생성해야 합니다.

> {note} `local` 드라이버를 사용할 때, 반환되는 `url` 은 URL 인코딩된 값이 아닙니다. 따라서, 파일 이름을 항상 유효한 URL이 되도록 저장하는 것을 권장합니다.

#### 임시 URLs

`s3`를 사용하여 파일을 저장할 때, `temporaryUrl` 메소드를 사용하여 파일에 접근하기 위한 임시 URL을 생성할 수 있습니다. 이 메소드는 파일의 경로 및 언제 URL이 만료되어야 하는지 지정한 `DateTime` 인스턴스를 인자로 받습니다.

    $url = Storage::temporaryUrl(
        'file.jpg', now()->addMinutes(5)
    );

추가 [S3 요청 파라미터](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html#RESTObjectGET-requests)를 지정해야하는 경우 요청 파라미터 배열을 `temporaryUrl` 메소드의 인수의 세 번째 파라미터로 전달할 수 있습니다.

    $url = Storage::temporaryUrl(
        'file.jpg',
        now()->addMinutes(5),
        [
            'ResponseContentType' => 'application/octet-stream',
            'ResponseContentDisposition' => 'attachment; filename=file2.jpg',
        ]
    );

#### URL 호스트 커스터마이징

`Storage` 파사드를 사용하여 생성된 파일 URL에 대한 호스트를 미리 정의하려면 디스크의 설정 배열에 `url` 옵션을 추가하면 됩니다.

    'public' => [
        'driver' => 'local',
        'root' => storage_path('app/public'),
        'url' => env('APP_URL').'/storage',
        'visibility' => 'public',
    ],

<a name="file-paths"></a>
### 파일 경로

`path` 메소드를 이용하여 지정된 파일의 경로를 가져올 수 있습니다. `local` 드라이버를 사용하는 경우, 파일의 절대 경로를 반환합니다. `S3` 드라이버를 사용하는 경우, S3 버킷의 파일 상대 경로를 반환합니다.

    use Illuminate\Support\Facades\Storage;

    $path = Storage::path('file.jpg');

<a name="file-metadata"></a>
### 파일 메타 데이터

파일을 읽고 쓰는 것 이외도 라라벨은 파일 자체에 대한 정보도 제공할 수 있습니다. 예를 들어, `size` 메소드는 파일 크기를 바이트로 조회하기 위해서 사용합니다.

    use Illuminate\Support\Facades\Storage;

    $size = Storage::size('file.jpg');

`lastModified` 메소드는 마지막에 파일이 업데이트되었을 때의 UNIX 타임 스탬프값을 반환합니다.

    $time = Storage::lastModified('file.jpg');

<a name="storing-files"></a>
## 파일 저장하기

`put` 메소드는 파일의 내용을 디스크에 저장하는데 사용됩니다. 또한 `put` 메소드에 `resource` 를 전달하여 파일시스템의 스트림을 사용할 수도 있습니다. 모든 파일 경로는 디스크에 대해 구성된 "root" 위치를 기준으로 지정해야합니다.

    use Illuminate\Support\Facades\Storage;

    Storage::put('file.jpg', $contents);

    Storage::put('file.jpg', $resource);

#### 자동 스트리밍

만약 주어진 파일이 여러분의 스토리지 위치에 자동적으로 스트리밍되도록 라라벨이 관리하기를 바란다면 `putFile` 이나 `putFileAs` 메소드를 사용하면 됩니다. 이 메소드는 `Illuminate\Http\File` 이나 `Illuminate\Http\UploadedFile` 인스턴스를 인자로 받아들이고 자동으로 명시한 위치에 파일을 스트림 처리할 것입니다.

    use Illuminate\Http\File;
    use Illuminate\Support\Facades\Storage;

    // Automatically generate a unique ID for file name...
    Storage::putFile('photos', new File('/path/to/photo'));

    // Manually specify a file name...
    Storage::putFileAs('photos', new File('/path/to/photo'), 'photo.jpg');

`putFile` 메소드에는 몇가지 중요한 사항들이 있습니다. 파일 이름이 아니라 디렉토리 이름을 지정하는 것에 유의하십시오. 기본적으로 `putFile` 메소드는 파일이름을 기반으로 한 고유한 ID를 생성합니다. 파일의 확장자는 파일의 MIME 타입에 의해서 결정됩니다. `putFile` 메소드에 의해서 파일의 경로가 반환 될것이기 때문에, 이 경로에 생성된 파일 이름을 포함하여 데이터베이스에 저장할 수 있습니다.

`putFile` 과 `putFileAs` 메소드는 또한 저장되는 파일의 "visibility"를 지정하는 인자를 받아들입니다. 이는 특히 S3 와 같은 클라우드 디스크에 파일을 저장하고 파일의 접근 권한을 설정하고자 할 때 유용합니다.

    Storage::putFile('photos', new File('/path/to/photo'), 'public');

#### 파일의 앞에 내용 추가하기 & 뒤에 내용 추가하기

`prepend` 와 `append` 메소드를 사용하면 파일의 처음과 끝에 내용을 추가할 수 있습니다.

    Storage::prepend('file.log', 'Prepended Text');

    Storage::append('file.log', 'Appended Text');

#### 파일의 복사 & 이동

`copy` 메소드는 존재하는 파일을 디스크의 새로운 위치에 복사하는데 사용되고, `move` 메소드는 존재하는 파일의 이름을 변경하거나 새로운 위치에 이동시키는데 사용됩니다.

    Storage::copy('old/file.jpg', 'new/file.jpg');

    Storage::move('old/file.jpg', 'new/file.jpg');

<a name="file-uploads"></a>
### 파일 업로드

웹 애플리케이션에서 파일을 저장하는 가장 공통적인 사용예 중 하나는 프로필 이미지, 사진, 그리고 문서와 같은 사용자가 업로드한 파일을 저장하는 것입니다. 라라벨은 업로드된 파일 인스턴스에서 `store` 메소드를 사용하여 업로드한 파일이 저장되는 것을 아주 쉽게 처리합니다. 저장된 파일이 저장되고자 하는 경로를 넘겨주며 `store` 메소드를 호출하면 됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Http\Request;

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

이 예제에서는 몇가지 중요한 사항들이 있습니다. 예제에서 파일 이름이 아니라 디렉토리 이름을 지정하는 것에 유의하십시오. 기본적으로 `store` 메소드는 파일이름을 기반으로 한 고유한 ID를 생성합니다. 파일의 확장자는 파일의 MIME 타입에 의해서 결정됩니다. `store` 메소드에 의해서 파일의 경로가 반환 될 것이기 때문에, 이 경로에 생성된 파일 이름을 포함하여 데이터베이스에 저장할 수 있습니다.

또한 다음 예제와 같이 `Storage` 파사드의 `putFile` 메소드를 호출하여 동일한 파일 조작을 수행할 수 있습니다.

    $path = Storage::putFile('avatars', $request->file('avatar'));

#### 파일 이름 지정하기

저장되는 파일에 이름이 자동으로 지정되지 않게 하고 싶다면, `storeAs` 메소드에 파일의 경로와 이름 그리고 (선택적으로) 디스크를 인자로 전달하여 사용할 수 있습니다.

    $path = $request->file('avatar')->storeAs(
        'avatars', $request->user()->id
    );

다음의 예제와 같이 동일하게 `Storage` 파사드의 `putFileAs` 메소드를 사용하여 파일 조작을 수행할 수도 있습니다.

    $path = Storage::putFileAs(
        'avatars', $request->file('avatar'), $request->user()->id
    );

> {note} 출력할 수 없거나 유효하지 않은 유니코드 문자는 파일 경로에서 자동으로 삭제됩니다. 그러므로 파일 경로를 라라벨 파일 저장 메소드에 전달하기 전에 sanitize 하시기 바랍니다. 파일 경로는 `League\Flysystem\Util::normalizePath` 메소드로 정규화됩니다.

#### 디스크 지정하기

기본적으로, 이 메소드는 기본 디스크를 사용할 것입니다. 만약 여러분이 다른 디스크를 지정하고자 한다면, `store` 메소드의 두번째 인자로 디스크의 이름을 전달하면 됩니다.

    $path = $request->file('avatar')->store(
        'avatars/'.$request->user()->id, 's3'
    );

`storeAs` 메서드를 사용하는 경우 디스크 이름을 메서드의 세 번째 인수로 전달할 수 있습니다.

    $path = $request->file('avatar')->storeAs(
        'avatars',
        $request->user()->id,
        's3'
    );

#### 다른 파일 정보

업로드 된 파일의 원래 이름을 얻으려면 `getClientOriginalName` 메소드를 사용하십시오.

    $name = $request->file('avatar')->getClientOriginalName();

`extension` 메소드는 업로드 된 파일의 확장자를 얻기 위해 사용될 수 있습니다.

    $extension = $request->file('avatar')->extension();

<a name="file-visibility"></a>
### 파일 Visibility

라라벨의 Flysystem 구성에서, "visibility" 는 다양한 플랫폼에서 파일에 대한 권한의 추상화 입니다. 파일들은 `public` 이거나 `private` 로 지정될 수 있습니다. 파일을 `public` 으로 선언하면, 다른 사람들 일반적으로 파일에 접근할 수 있다는 것을 나타냅니다. 예를 들어 S3 드라이버를 사용할 때, `public` 파일들에 대해서 URL을 조회할 수 있습니다.

`put` 메소드를 통해서 파일을 설정할 때 visibility를 지정할 수 있습니다.

    use Illuminate\Support\Facades\Storage;

    Storage::put('file.jpg', $contents, 'public');

만약 파일이 이미 저장되어 있다면, 이 파일의 visibility 는 `getVisibility` 와 `setVisibility`를 통해서 조회하고 설정할 수 있습니다.

    $visibility = Storage::getVisibility('file.jpg');

    Storage::setVisibility('file.jpg', 'public');

업로드 된 파일과 상호 작용할 때 `storePublicly` 및 `storePubliclyAs` 메소드를 사용하여 업로드 된 파일을 `public`으로 저장할 수 있습니다.

    $path = $request->file('avatar')->storePublicly('avatars', 's3');

    $path = $request->file('avatar')->storePubliclyAs(
        'avatars',
        $request->user()->id,
        's3'
    );

<a name="deleting-files"></a>
## 파일 삭제하기

`delete` 메소드는 삭제할 하나의 파일 이름 또는 파일들의 배열을 인자로 받습니다.

    use Illuminate\Support\Facades\Storage;

    Storage::delete('file.jpg');

    Storage::delete(['file.jpg', 'file2.jpg']);

필요한 경우, 파일이 어디에서 지워져야 하는지 디스크를 지정할 수 있습니다.

    use Illuminate\Support\Facades\Storage;

    Storage::disk('s3')->delete('folder_path/file_name.jpg');

<a name="directories"></a>
## 디렉토리들

#### 디렉토리 안의 모든 파일들 확인하기

`files` 메소드는 주어진 디렉토리에 들어 있는 모든 파일들에 대한 배열을 반환합니다. 만약 지정된 디렉토리의 하위 디렉토리에 있는 모든 파일의 목록을 포함하기를 원한다면는 `allFiles` 메소드를 사용하면 됩니다.

    use Illuminate\Support\Facades\Storage;

    $files = Storage::files($directory);

    $files = Storage::allFiles($directory);

#### 디렉토리에 들어 있는 모든 하위 디렉토리들 확인하기

`directories` 메소드는 주어진 디렉토리에 들어 있는 전체 디렉토리들에 대한 배열을 반환합니다. 추가적으로 지정된 디렉토리의 하위 디렉토리 및 하위 디렉토리 아래의 모든 디렉토리들에 대한 목록을 포함하기를 원한다면는 `allDirectories` 메소드를 사용하면 됩니다.

    $directories = Storage::directories($directory);

    // Recursive...
    $directories = Storage::allDirectories($directory);

#### 디렉토리 생성하기

`makeDirectory` 메소드는 필요한 하위 디렉토리를 포함하여 지정된 디렉토리를 만들 것입니다.

    Storage::makeDirectory($directory);

#### 디렉토리 삭제하기

마지막으로 `deleteDirectory`메소드는 디렉토리와 포함 된 모든 파일을 삭제하는 데 사용됩니다.

    Storage::deleteDirectory($directory);

<a name="custom-filesystems"></a>
## 커스텀(사용자 정의) 파일 시스템

라라벨의 통합 FlySystem은 처음부터 다양한 “드라이버”가 제공됩니다. 하지만 파일시스템은 이러한 드라이버에 제한적이지 않고, 다른 스토리지 시스템에도 적용할 수 있습니다. 여러분은 라라벨 애플리케이션에 적합한 스토리지 시스템에 대한 사용자 정의 드라이버를 생성할 수 있습니다.

커스텀(사용자 정의) 파일 시스템을 구성하기 위해서는 Flysystem 어댑터가 필요합니다. 커뮤니티에서 관리되고 있는 Dropbox 어댑터를 프로젝트에 추가해보겠습니다.

    composer require spatie/flysystem-dropbox

그 다음에 `DropboxServiceProvider`와 같은 [서비스 프로바이더](/docs/{{version}}/providers)를 생성해야 합니다. 프로바이더의 `boot` 메소드에서 `Storage` 파사드의 `extend` 메소드를 사용하여 커스텀 드라이버를 정의할 수 있습니다.

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Storage;
    use Illuminate\Support\ServiceProvider;
    use League\Flysystem\Filesystem;
    use Spatie\Dropbox\Client as DropboxClient;
    use Spatie\FlysystemDropbox\DropboxAdapter;

    class DropboxServiceProvider extends ServiceProvider
    {
        /**
         * Register any application services.
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

`extend` 메소드의 첫번째 인자는 드라이버의 이름이고, 두번째는 `$app` 과 `$config` 변수를 전달 받는 클로저가 됩니다. 이 클로저는 `League\Flysystem\Filesystem` 에 대한 인스턴스를 반환해야 합니다. `$config` 변수는 `config/filesystems.php` 파일 안에 정의된 디스크 값을 가지고 있습니다.

다음으로 `config/app.php` 설정 파일에 서비스 프로바이더를 등록하십시오.

    'providers' => [
        // ...
        App\Providers\DropboxServiceProvider::class,
    ];

확장 기능의 서비스 프로바이더를 만들고 등록한 후 `config/filesystems.php` 설정 파일에서 `dropbox` 드라이버를 사용할 수 있습니다.
