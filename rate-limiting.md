# 속도 제한

- [시작하기](#introduction)
    - [캐시 설정](#cache-configuration)
- [기본적인 사용법](#basic-usage)
    - [시도 횟수 수동 증가](#manually-incrementing-attempts)
    - [시도 횟수 초기화](#clearing-attempts)

<a name="introduction"></a>
## 시작하기

라라벨에는 애플리케이션의 [캐시](cache)와 함께 사용하기 쉬운 속도 제한 추상화가 포함되어 있어 지정된 시간 동안 모든 작업을 제한하는 쉬운 방법을 제공합니다.

> {팁} HTTP 요청의 속도 제한하는 방법에 관심이 있으면 [속도 제한기 미들웨어 문서](routing#rate-limiting)를 확인하세요.

<a name="cache-configuration"></a>
### 캐시 환경설정

일반적으로 속도 제한기는 애플리케이션의 `cache` 구성 파일 내 `default` 키로 정의된 기본 애플리케이션 캐시를 활용합니다. 그러나 애플리케이션의 `cache` 구성 파일 내에서 `limiter` 키를 정의하여 속도 제한기가 사용해야 하는 캐시 드라이버를 지정할 수 있습니다.

    'default' => 'memcached',

    'limiter' => 'redis',

<a name="basic-usage"></a>
## 기본적인 사용법

`Illuminate\Support\Facades\RateLimiter` 파사드는 속도 제한기와 상호 작용하는 데 사용할 수 있습니다. 속도 제한기가 제공하는 가장 간단한 방법은 주어진 시간(초) 동안 지정된 콜백 속도를 제한하는 `attempt` 메소드입니다.

콜백에 남은 시도가 없을 때 `attempt` 메서드는 `false`를 반환합니다. 그렇지 않으면 `attempt` 메소드는 콜백의 결과 또는 `true`를 리턴합니다. `attempt` 메소드에서 허용되는 첫 번째 인수는 속도 제한기 "키"이며, 속도 제한 중인 작업을 나타내는 임의의 문자열일 수 있습니다.

    use Illuminate\Support\Facades\RateLimiter;

    $executed = RateLimiter::attempt(
        'send-message:'.$user->id,
        $perMinute = 5,
        function() {
            // Send message...
        }
    );

    if (! $executed) {
      return 'Too many messages sent!';
    }

<a name="manually-incrementing-attempts"></a>
### 시도 횟수 수동 증가

속도 제한기와 수동으로 상호 작용하려는 경우 다양한 다른 방법을 사용할 수 있습니다. 예를 들어, `tooManyAttempts` 메소드를 호출하여 주어진 속도 제한기 키가 분당 허용되는 최대 시도 횟수를 초과했는지 확인할 수 있습니다.

    use Illuminate\Support\Facades\RateLimiter;

    if (RateLimiter::tooManyAttempts('send-message:'.$user->id, $perMinute = 5)) {
        return 'Too many attempts!';
    }

또는 `remaining` 메서드를 사용하여 주어진 키에 대해 남은 시도 횟수를 검색할 수 있습니다. 주어진 키에 남은 재시도가 있는 경우 `hit` 메서드를 호출하여 총 시도 횟수를 늘릴 수 있습니다.

    use Illuminate\Support\Facades\RateLimiter;

    if (RateLimiter::remaining('send-message:'.$user->id, $perMinute = 5)) {
        RateLimiter::hit('send-message:'.$user->id);

        // Send message...
    }

<a name="determining-limiter-availability"></a>
#### 리미터 가용성 결정

키에 더 이상 시도가 남아 있지 않으면 `availableIn` 메서드는 더 많은 시도를 사용할 수 있을 때까지 남은 시간(초)을 반환합니다.

    use Illuminate\Support\Facades\RateLimiter;

    if (RateLimiter::tooManyAttempts('send-message:'.$user->id, $perMinute = 5)) {
        $seconds = RateLimiter::availableIn('send-message:'.$user->id);

        return 'You may try again in '.$seconds.' seconds.';
    }

<a name="clearing-attempts"></a>
### 시도 횟수 초기화

`clear` 메소드를 사용하여 주어진 속도 제한기 키에 대한 시도 횟수를 재설정할 수 있습니다. 예를 들어 수신자가 주어진 메시지를 읽을 때 시도 횟수를 재설정할 수 있습니다.

    use App\Models\Message;
    use Illuminate\Support\Facades\RateLimiter;

    /**
     * Mark the message as read.
     *
     * @param  \App\Models\Message  $message
     * @return \App\Models\Message
     */
    public function read(Message $message)
    {
        $message->markAsRead();

        RateLimiter::clear('send-message:'.$message->user_id);

        return $message;
    }
