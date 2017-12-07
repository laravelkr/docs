# 업그레이드 가이드

- [5.4에서 5.5.0 으로 업그레이드 하기](#upgrade-5.4.0)

<a name="upgrade-5.5.0"></a>
## 5.4에서 5.5.0으로 업그레이드 하기

#### 업그레이드 예상 시간 : 약 1시간

> {note} 가능한 모든 변경 내용을 기록하려고 했습니다. 그렇지만 변경 사항 중 일부는 프레임워크의 명확하지 않은 부분에서 이루어 지기 때문에 이중 일부가 실제 어플리케이션에 영향을 끼칠 수도 있습니다.

### 의존성 업데이트

`composer.json` 파일에 있는 laravel/framework 의존성을 `5.5.*` 로 변경합니다. 그리고 `phpunit/phpunit`을 `~6.0` 으로 업데이트 해야 합니다. 다음으로 `composer.json` 파일의 `require-dev` 부분에 `filp/whoops` 버전 `~2.0`을 추가하십시오. 마지막으로, `composer.json` 파일의 `scripts` 부분에 `post-autoload-dump` 이벤트에 `package:discover` 명령어를 실행하도록 추가하십시오:

    "scripts": {
        ...
        "post-autoload-dump": [
            "Illuminate\\Foundation\\ComposerScripts::postAutoloadDump",
            "@php artisan package:discover"
        ],
    }

또한, 어플리케이션에서 사용하는 써드파티 패키지를 확인하고 라라벨 5.5를 지원하는 적절한 버전을 사용하고 있는지 확인하십시오.

#### 라라벨 인스톨러

> {tip} `laravel new` 를 통해서 라라벨을 설치했었다면, `composer global update` 명령어를 사용하여 라라벨 인스톨러를 업데이트 해야합니다.

#### 라라벨 Dusk

라라벨 Dusk `2.0.0`이 릴리즈되어 라라벨 5.5와 Headless 크롬 테스팅의 호환성을 제공합니다.

#### Pusher

Push 이벤트 브로드캐시팅 드라이버는 이제 Pusher SDK의 `~3.0` 릴리즈 버전을 필요로 합니다.

#### Swift Mailer

라라벨 5.5는 Swift 메일러의 `~6.0` 를 필요로 합니다.

### Artisan

#### Auto-Loading Commands

라라벨 5.5에서는 아티즌 명령어는 자동으로 등록되기 때문에, 커널에서 수동으로 명령어를 등록해줄 필요가 없습니다. 이 기능을 사용하기 위해서는 `App\Console\Kernel` 클래스의 `commands` 메소드에 다음 라인을 추가하십시오:

    $this->load(__DIR__.'/Commands');

#### `fire` 메소드

아티즌 명령어에 존재하는 `fire` 메소드는 `handle` 으로 이름을 변경해야 합니다.

#### `optimize` 명령어

최신의 PHP op-code 캐싱에 의해서, `optimize` 아티즌 명령어가 더이상 필요로하지 않습니다. 앞으로의 라라벨 릴리즈에서는 이 명령어가 삭제될 것이기 때문에, 배포스크립트에서 이 명령어를 사용하고 있다면 미리 삭제해두는 것이 좋습니다.

### 권한 승인-authorization

#### `authorizeResource` 컨트롤러 메소드

When passing a multi-word model name to the `authorizeResource` method, the resulting route segment will now be "snake" case, matching the behavior of resource controllers.

#### `before` Policy 메소드

policy 클래스의 `before` 메소드는 클래스가 검사되는 ability 이름과 일치하는 메소드가 없는 경우에 이를 호출하지 않습니다.

### 캐시

#### 데이터베이스 드라이버

데이터베이스 캐시 드라이버를 사용하고 있었다면, 라라벨 5.5으로 업그레이드한 어플리케이션을 배포하고 나서 처음 한번 `php artisan cache:clear` 메소드를 실행해야 합니다.

### Eloquent

#### `belongsToMany` 메소드

Eloquent 모델에서 `belongsToMany` 메소드를 오버라이딩해서 사용하고 있었다면, 몇개의 추가된 인자들에 대응하기 위해서 메소드를 수정해야 합니다:

    /**
     * Define a many-to-many relationship.
     *
     * @param  string  $related
     * @param  string  $table
     * @param  string  $foreignPivotKey
     * @param  string  $relatedPivotKey
     * @param  string  $parentKey
     * @param  string  $relatedKey
     * @param  string  $relation
     * @return \Illuminate\Database\Eloquent\Relations\BelongsToMany
     */
    public function belongsToMany($related, $table = null, $foreignPivotKey = null,
                                  $relatedPivotKey = null, $parentKey = null,
                                  $relatedKey = null, $relation = null)
    {
        //
    }

#### BelongsToMany `getQualifiedRelatedKeyName`

`getQualifiedRelatedKeyName` 메소드가 `getQualifiedRelatedPivotKeyName` 으로 이름이 변경되었습니다.

#### BelongsToMany `getQualifiedForeignKeyName`

The `getQualifiedForeignKeyName` method has been renamed to `getQualifiedForeignPivotKeyName`.

`getQualifiedForeignKeyName` 메소드가 `getQualifiedForeignPivotKeyName` 으로 이름이 변경되었습니다.

#### 모델의 `is` 메소드

Eloquent 모델의 `is` 메소드를 오버라이딩해서 사용하고 있었다면, 이 메소드에 `Model` 타입 힌트를 제거해야합니다. 이렇게 하면 `is` 메소드가 `null`을 인자로 받을 수 있습니다:

    /**
     * Determine if two models have the same ID and belong to the same table.
     *
     * @param  \Illuminate\Database\Eloquent\Model|null  $model
     * @return bool
     */
    public function is($model)
    {
        //
    }

#### 모델의 `$events` 속성

모델의 `$events` 속성은 `$dispatchesEvents`으로 이름이 변경되었습니다. 이 변경사항은 많은 사용자들이 `event` 라는 관계를 정의하고자 하여 기존의 이름과 충돌을 유발했기 때문입니다.

#### 피벗-Pivot `$parent` 속성

`Illuminate\Database\Eloquent\Relations\Pivot` 클래스의 `$parent` 속성은 `$pivotParent` 으로 이름이 변경되었습니다.

#### Relationship-관계에서의 `create` 메소드

`BelongsToMany`, `HasOneOrMany`, 그리고 `MorphOneOrMany` 클래스의 `create` 메소드는 `$attributes` 인자를 위해서 기본값을 제공하도록 변경되었습니다. 이 메소드를 오버라이딩 한경우, 변경사항에 매칭되도록 사용한 곳을 수정해야 합니다:

    public function create(array $attributes = [])
    {
        //
    }

#### Soft 삭제 메소드

"soft deleted" 모델을 삭제할 때에는 모델의 `exists` 속성을 `true` 를 유지합니다.

#### `withCount` 컬럼의 포맷팅

alias-별칭을 사용할 때 `withCount` 메소드는 더이상 자동으로 결과 컬럼 이름에 `_count`을 붙이지 않습니다. 예를 들어, 라라벨 5.4에서는 다음 쿼리의 결과에 `bar_count` 컬럼이 추가되었습니다:

    $users = User::withCount('foo as bar')->get();

하지만, 라라벨 5.5에서는 alias-별칭이 그대로 사용됩니다. 결과 컬럼에 `_count`를 붙이고자 한다면, alias-별칭을 정의할 때 접미사를 지정해야합니다:

    $users = User::withCount('foo as bar_count')->get();

#### 모델 메소드 & 속성의 이름

배열을 통한 엑세스를 사용할 때 모델의 private 속성에 엑세스하지 못하도록, 더 이상 속성이나 속성과 동일한 이름을 가지는 모델 메소드를 사용할 수 없습니다. 이렇게 하면 배열 엑세스를 통한 (`$user['name']`) 또는 `data_get` 헬퍼 함수를 통해서 모델의 속성에 엑세스 할 때 예외-exception이 발생합니다.

### Exception 포맷

라라벨 5.5에서는 유효성 검사-validation exception-예외를 포함하여 모든 exception-예외는 exception 핸들러에 의해서 HTTP 응답-response으로 변환합니다. 또한 JSON 유효성 검사에러에 대한 기본 포맷이 변경되었습니다. 새로운 포맷은 다음 형태를 따릅니다:

    {
        "message": "The given data was invalid.",
        "errors": {
            "field-1": [
                "Error 1",
                "Error 2"
            ],
            "field-2": [
                "Error 1",
                "Error 2"
            ],
        }
    }

하지만, 라라벨 5.4의 JSON 에러 포맷을 유지하고자 한다면, `App\Exceptions\Handler` 클래스에 다음 메서드를 추가 하면 됩니다:

    use Illuminate\Validation\ValidationException;

    /**
     * Convert a validation exception into a JSON response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Illuminate\Validation\ValidationException  $exception
     * @return \Illuminate\Http\JsonResponse
     */
    protected function invalidJson($request, ValidationException $exception)
    {
        return response()->json($exception->errors(), $exception->status);
    }

#### JSON 인증 시도

이 변경사항은 JSON을 통한 인증 시도에서 validation-유효성 검증 에러가 났을 때 이를 포맷팅 하는데 영향을 줍니다. 라라벨 5.5에서는 JSON 인증 실패는 앞서 설명한 새로운 형태의 규칙을 따르는 에러 메세지를 반환합니다.

#### Form Request 주의사항

개별 form request에서 response-응답 포맷을 커스터마이징 했었다면, 이제 개별 form request 클래스의 `failedValidation`를 오버라이드 하고, 커스텀 응답을 포함하는 `HttpResponseException` 인스턴스를 발생시켜야 합니다:

    use Illuminate\Http\Exceptions\HttpResponseException;

    /**
     * Handle a failed validation attempt.
     *
     * @param  \Illuminate\Contracts\Validation\Validator  $validator
     * @return void
     *
     * @throws \Illuminate\Validation\ValidationException
     */
    protected function failedValidation(Validator $validator)
    {
        throw new HttpResponseException(response()->json(..., 422));
    }

### 파일 시스템

#### `files` 메소드

`files` 메소드는 이제 `allFiles` 메소드와 비슷하게 `SplFileInfo` 객체의 배열을 반환합니다. 이전까지는 `files` 메소드는 문자형태의 경로값을 가지는 배열을 반환했었습니다.

### 메일

#### 사용하지 않는 파라미터

`Illuminate\Contracts\Mail\MailQueue` contracts 의 `queue` 와 `later` 메소드의 `$data`와 `$callback` 인자는 제거되었습니다:

    /**
     * Queue a new e-mail message for sending.
     *
     * @param  string|array|MailableContract  $view
     * @param  string  $queue
     * @return mixed
     */
    public function queue($view, $queue = null);

    /**
     * Queue a new e-mail message for sending after (n) seconds.
     *
     * @param  \DateTimeInterface|\DateInterval|int  $delay
     * @param  string|array|MailableContract  $view
     * @param  string  $queue
     * @return mixed
     */
    public function later($delay, $view, $queue = null);

### 큐-Queues

#### `dispatch` 헬퍼 함수

`handle` 메소드에서 job을 즉시 처리하고 결과값을 필요로 한다면, `dispatch_now` 또는 `Bus::dispatchNow` 메소드를 사용해야 합니다:

    use Illuminate\Support\Facades\Bus;

    $value = dispatch_now(new Job);

    $value = Bus::dispatchNow(new Job);

### Requests-요청

#### `has` 메소드

`$request->has` 메소드는 이제 빈 문자열 그리고 `null` 에 대해서도 `true` 값을 반환합니다. 이전의 `has` 메소드의 동작은 새로운 `$request->filled` 메소드가 대신합니다.

#### `intersect` 메소드

`intersect` 메소드는 제거되었습니다. `$request->only` 호출에 `array_filter`를 사용하여 동일한 동작을 수행할 수 있습니다:

    return array_filter($request->only('foo'));

#### `only` 메소드

`only` 메소드는 이제 request payload에 실재로 존재하는 속성만 반환합니다. `only` 메소드의 이전 동작을 유지하려면 대신 `all` 메소드를 사용하면 됩니다.

    return $request->all('foo');

#### The `request()` Helper

`request` 헬퍼 함수는 더이상 중첩된 키를 조회하지 않습니다. 필요하다면, 이 동작을 위해서 request 의 `input` 메소드를 사용할 수 있습니다:

    return request()->input('filters.date');

### 테스팅

#### 인증 Assertions

몇몇의 인증 관련 테스팅 확인사항들은 프레임워크의 다른 부분들과의 일관성을 위해서 이름이 변경되었습니다:

- `seeIsAuthenticated` 는 `assertAuthenticated`으로 이름이 변경되었습니다.
- `dontSeeIsAuthenticated` 는  `assertGuest` 으로 이름이 변경되었습니다.
- `seeIsAuthenticatedAs` 는 `assertAuthenticatedAs` 으로 이름이 변경되었습니다.
- `seeCredentials` 는 `assertCredentials` 으로 이름이 변경되었습니다.
- `dontSeeCredentials` 는 `assertInvalidCredentials` 으로 이름이 변경되었습니다.

#### Mail Fake

메일이 request 를 처리하는 동안 **queue**를 통해서 처리되었는지 확인하기 위해서 `Mail` fake 를 사용하고 있었다면, `Mail::assertSent` 대신에 `Mail::assertQueued`를 사용해야 합니다. 이러한 구분방법을 통해서 메일이 백그라운드 전송을 위해서 queue에 대기중이며, request 를 처리하는 그 동안에 전송되지 않았다는 것을 확인할 수 있습니다.

#### Tinker

라라벨 Tinker 는 이제 어플리케이션 클래스를 참조할 때 네임스페이스를 생략하는 기능을 지원합니다. 이 기능을 사용하려면 최적화된 컴포저 클래스 맵이 필요합니다. 따라서 `composer.json` 파일의 `config` 섹션에 `optimize-autoloader` 지시어를 추가하십시오:

    "config": {
        ...
        "optimize-autoloader": true
    }

### 다국어

#### `LoaderInterface`

`Illuminate\Translation\LoaderInterface` 인터페이스는 `Illuminate\Contracts\Translation\Loader`로 이동되었습니다.

### 유효성 검사

#### Validator 메소드

validator의 모든 유효성 검사 메소드들은 이제 `protected` 대신에 `public`으로 변경되었습니다.

### Views-뷰

#### 동적 "With" 변수 이름

동적 `__call` 메소드가 뷰-view 와 변수를 공유할 때, 이 변수들은 자동으로 "카멜" 케이스 형태가 됩니다. 예를 들어 다음과 같습니다:

    return view('pool')->withMaximumVotes(100);

`maximumVotes` 변수는 템플릿 안에서 다음과 같이 엑세스 할 수 있습니다:

    {{ $maximumVotes }}

#### `@php` 블레이드 지시어

`@php` 블레이드 지시어는 더이상 인라인 태그를 허용하지 않습니다. 그 대신에 지시어를 열고 닫는 형태로 사용하십시오:

    @php
        $teamMember = true;
    @endphp

### 기타

또한 `laravel/laravel` [GitHub repository](https://github.com/laravel/laravel) GitHub 저장소에서 변경사항을 확인하는 것이 좋습니다. 이러한 변경사항이 꼭 필요하지는 않지만, 여러분의 어플리케이션을 이 변경사항들에 맞추어 항상 최신의 상태로 유지하고자 할 수도 있습니다. 변경사항 중 일부는 이 업그레이드 가이드에서 다루지만, 설정 파일이나, 설명의 변경같은 경우 일부는 문서에서 기술하지 않을 수도 있습니다. GitHub 에서 Diff 툴을 사용하여 변경사항을 보다 쉽게 확인하고, 필요한 업데이트를 적용할 수도 있습니다.
