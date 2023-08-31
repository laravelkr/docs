# Rate Limiting

- [Rate Limiting](#rate-limiting)
  - [Introduction](#introduction)
  - [소개](#소개)
    - [Cache Configuration](#cache-configuration)
    - [캐시 구성](#캐시-구성)
  - [Basic Usage](#basic-usage)
  - [기본 사용법](#기본-사용법)
    - [Manually Incrementing Attempts](#manually-incrementing-attempts)
    - [수동으로 시도 횟수 증가](#수동으로-시도-횟수-증가)
      - [Determining Limiter Availability](#determining-limiter-availability)
      - [제한기 가용성 결정](#제한기-가용성-결정)
    - [Clearing Attempts](#clearing-attempts)
    - [시도 횟수 지우기](#시도-횟수-지우기)

<a name="introduction"></a>
## Introduction
## 소개

Laravel includes a simple to use rate limiting abstraction which, in conjunction with your application's [cache](cache), provides an easy way to limit any action during a specified window of time.

라라벨에는 [캐시](cache)와 함께 사용되는 간단한 속도 제한 추상화가 포함되어 있으며, 지정된 시간 동안 어떤 작업을 제한하는 간단한 방법을 제공합니다.

> **Note**  
> If you are interested in rate limiting incoming HTTP requests, please consult the [rate limiter middleware documentation](routing#rate-limiting).

> **참고**
> 들어오는 HTTP 요청의 속도 제한에 관심이 있다면, [속도 제한 미들웨어 문서](routing#rate-limiting)를 참조하십시오.

<a name="cache-configuration"></a>
### Cache Configuration
### 캐시 구성

Typically, the rate limiter utilizes your default application cache as defined by the `default` key within your application's `cache` configuration file. However, you may specify which cache driver the rate limiter should use by defining a `limiter` key within your application's `cache` configuration file:

일반적으로, 속도 제한기는 애플리케이션의 `cache` 구성 파일 내의 `default` 키로 정의된 기본 애플리케이션 캐시를 사용합니다. 그러나 애플리케이션의 `cache` 구성 파일 내에서 `limiter` 키를 정의하여 속도 제한기가 사용할 캐시 드라이버를 지정할 수 있습니다:

    'default' => 'memcached',

    'limiter' => 'redis',

<a name="basic-usage"></a>
## Basic Usage
## 기본 사용법

The `Illuminate\Support\Facades\RateLimiter` facade may be used to interact with the rate limiter. The simplest method offered by the rate limiter is the `attempt` method, which rate limits a given callback for a given number of seconds.

`Illuminate\Support\Facades\RateLimiter` 파사드는 속도 제한기와 상호 작용하는 데 사용할 수 있습니다. 속도 제한기가 제공하는 가장 간단한 메서드는 주어진 시간 동안 주어진 콜백의 속도를 제한하는 `attempt` 메서드입니다.

The `attempt` method returns `false` when the callback has no remaining attempts available; otherwise, the `attempt` method will return the callback's result or `true`. The first argument accepted by the `attempt` method is a rate limiter "key", which may be any string of your choosing that represents the action being rate limited:

`attempt` 메서드는 콜백에 남아있는 시도가 없을 때 `false`를 반환합니다. 그렇지 않으면 `attempt` 메서드는 콜백의 결과 또는 `true`를 반환합니다. `attempt` 메서드가 허용하는 첫 번째 인수는 속도 제한기 "키"이며, 이는 속도 제한되는 작업을 나타내는 사용자가 선택한 문자열일 수 있습니다:

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

If necessary, you may provide a fourth argument to the `attempt` method, which is the "decay rate", or the number of seconds until the available attempts are reset. For example, we can modify the example above to allow five attempts every two minutes:

필요한 경우, `attempt` 메서드에 네 번째 인수를 제공할 수 있습니다. 이는 "감쇠 속도" 또는 사용 가능한 시도가 재설정되는 시간(초)입니다. 예를 들어, 위의 예제를 수정하여 2분마다 5번의 시도를 허용할 수 있습니다:

    $executed = RateLimiter::attempt(
        'send-message:'.$user->id,
        $perTwoMinutes = 5,
        function() {
            // Send message...
        },
        $decayRate = 120,
    );

<a name="manually-incrementing-attempts"></a>
### Manually Incrementing Attempts
### 수동으로 시도 횟수 증가

If you would like to manually interact with the rate limiter, a variety of other methods are available. For example, you may invoke the `tooManyAttempts` method to determine if a given rate limiter key has exceeded its maximum number of allowed attempts per minute:

수동으로 속도 제한기와 상호 작용하려면, 다양한 다른 메서드를 사용할 수 있습니다. 예를 들어, `tooManyAttempts` 메서드를 호출하여 주어진 속도 제한기 키가 분당 허용 시도 횟수를 초과했는지 여부를 확인할 수 있습니다:

    use Illuminate\Support\Facades\RateLimiter;

    if (RateLimiter::tooManyAttempts('send-message:'.$user->id, $perMinute = 5)) {
        return 'Too many attempts!';
    }

    RateLimiter::hit('send-message:'.$user->id);

    // Send message...

Alternatively, you may use the `remaining` method to retrieve the number of attempts remaining for a given key. If a given key has retries remaining, you may invoke the `hit` method to increment the number of total attempts:

또는 `remaining` 메서드를 사용하여 주어진 키에 대한 남은 시도 횟수를 검색할 수 있습니다. 주어진 키에 남은 재시도가 있는 경우, `hit` 메서드를 호출하여 총 시도 횟수를 증가시킬 수 있습니다:

    use Illuminate\Support\Facades\RateLimiter;

    if (RateLimiter::remaining('send-message:'.$user->id, $perMinute = 5)) {
        RateLimiter::hit('send-message:'.$user->id);

        // Send message...
    }

<a name="determining-limiter-availability"></a>
#### Determining Limiter Availability
#### 제한기 가용성 결정

When a key has no more attempts left, the `availableIn` method returns the number of seconds remaining until more attempts will be available:

키에 더 이상 시도할 수 있는 횟수가 없으면 `availableIn` 메서드는 더 많은 시도를 할 수 있을 때까지 남은 시간(초)을 반환합니다:

    use Illuminate\Support\Facades\RateLimiter;

    if (RateLimiter::tooManyAttempts('send-message:'.$user->id, $perMinute = 5)) {
        $seconds = RateLimiter::availableIn('send-message:'.$user->id);

        return 'You may try again in '.$seconds.' seconds.';
    }

    RateLimiter::hit('send-message:'.$user->id);

    // Send message...

<a name="clearing-attempts"></a>
### Clearing Attempts
### 시도 횟수 지우기

You may reset the number of attempts for a given rate limiter key using the `clear` method. For example, you may reset the number of attempts when a given message is read by the receiver:

`clear` 메서드를 사용하여 주어진 속도 제한기 키에 대한 시도 횟수를 재설정할 수 있습니다. 예를 들어, 주어진 메시지가 수신자에 의해 읽힐 때 시도 횟수를 재설정할 수 있습니다:

    use App\Models\Message;
    use Illuminate\Support\Facades\RateLimiter;

    /**
     * Mark the message as read.
     */
    public function read(Message $message): Message
    {
        $message->markAsRead();

        RateLimiter::clear('send-message:'.$message->user_id);

        return $message;
    }
