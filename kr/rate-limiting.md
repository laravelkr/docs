# Rate Limiting
# Rate(요청 속도) 제한

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Cache Configuration](#cache-configuration)
    - [캐시 설정](#cache-configuration)
- [Basic Usage](#basic-usage)
- [기본적인 사용법](#basic-usage)
    - [Manually Incrementing Attempts](#manually-incrementing-attempts)
    - [시도 횟수를 수동 증가시키기](#manually-incrementing-attempts)
    - [Clearing Attempts](#clearing-attempts)
    - [시도 횟수 초기화](#clearing-attempts)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel includes a simple to use rate limiting abstraction which, in conjunction with your application's [cache](cache), provides an easy way to limit any action during a specified window of time.

라라벨에는 손쉽게 사용할 수 있는 Rate limiting(요청-속도-제한) 기능을 제공하고 있습니다. 이 기능은 애플리케이션의 [캐시](cache)를 사용하여 지정된 시간 동안 요청되는 작업의 횟수를 제한할 수 있습니다.

> **Note**  
> If you are interested in rate limiting incoming HTTP requests, please consult the [rate limiter middleware documentation](routing#rate-limiting).

> **Note**  
> HTTP 요청의 속도 제한하는 방법에 관심이 있으면 [속도 제한기 미들웨어 문서](routing#rate-limiting)를 확인하세요.

<a name="cache-configuration"></a>
### Cache Configuration
### 캐시 환경설정

Typically, the rate limiter utilizes your default application cache as defined by the `default` key within your application's `cache` configuration file. However, you may specify which cache driver the rate limiter should use by defining a `limiter` key within your application's `cache` configuration file:

일반적으로 요청-속도-제한(rate limit) 기능은 애플리케이션의 `cache` 설정 파일 안에 있는 `default` 키로 정의된 애플리케이션의 기본 캐시를 사용합니다. 그러나 애플리케이션의 `cache` 설정 파일 안에 `limiter` 키를 정의하여 요청 속도 제한 기능에서 사용할 캐시 드라이버를 지정할 수도 있습니다.

    'default' => 'memcached',

    'limiter' => 'redis',

<a name="basic-usage"></a>
## Basic Usage
## 기본적인 사용법

The `Illuminate\Support\Facades\RateLimiter` facade may be used to interact with the rate limiter. The simplest method offered by the rate limiter is the `attempt` method, which rate limits a given callback for a given number of seconds.

`Illuminate\Support\Facades\RateLimiter` 파사드는 요청-속도-제한(rate limit)기능을 다루는데 사용할 수 있습니다. 요청-속도-제한(rate limit) 기능을 사용하는 가장 간단한 방법은 주어진 시간(초) 동안 지정된 콜백(callback) 속도를 제한하는 `attempt` 메소드를 호출하는 것입니다.

The `attempt` method returns `false` when the callback has no remaining attempts available; otherwise, the `attempt` method will return the callback's result or `true`. The first argument accepted by the `attempt` method is a rate limiter "key", which may be any string of your choosing that represents the action being rate limited:

콜백 안에서 더 이상 요청을 처리할 수 없다고 판단되면 `attempt` 메서드는 `false`를 반환합니다. 요청을 계속 처리할 수 있는 경우라면 `attempt` 메소드는 콜백의 결과 또는 `true`를 반환합니다. `attempt` 메서드에서 전달되는 첫 번째 인자는 요청-속도-제한기(rate limiter)의 "key" 이며, 요청 속도 제한 중인 작업을 나타내는 임의의 문자열이 될 수도 있습니다.  

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
### Manually Incrementing Attempts
### 시도 횟수를 수동 증가시키기

If you would like to manually interact with the rate limiter, a variety of other methods are available. For example, you may invoke the `tooManyAttempts` method to determine if a given rate limiter key has exceeded its maximum number of allowed attempts per minute:

요청-속도-제한기(rate limiter)를 수동으로 제어하려는 경우 다양한 메서드를 사용할 수 있습니다. 예를 들어, `tooManyAttempts` 메소드를 호출하여 주어진 요청-속도-제한기(rate limiter)가 분당 허용되는 최대 시도 횟수를 초과했는지 확인할 수 있습니다.

    use Illuminate\Support\Facades\RateLimiter;

    if (RateLimiter::tooManyAttempts('send-message:'.$user->id, $perMinute = 5)) {
        return 'Too many attempts!';
    }

Alternatively, you may use the `remaining` method to retrieve the number of attempts remaining for a given key. If a given key has retries remaining, you may invoke the `hit` method to increment the number of total attempts:

또는 `remaining` 메서드를 사용하여 주어진 키에 대해 남은 시도 횟수를 조회할 수 있습니다. 지정된 키에 남아 있는 재시도 횟수가 있는 경우 `hit` 메서드를 호출하여 총 시도 횟수를 증가 시킬수 있습니다.

    use Illuminate\Support\Facades\RateLimiter;

    if (RateLimiter::remaining('send-message:'.$user->id, $perMinute = 5)) {
        RateLimiter::hit('send-message:'.$user->id);

        // Send message...
    }

<a name="determining-limiter-availability"></a>
#### Determining Limiter Availability
#### 리미터 가용성 결정

When a key has no more attempts left, the `availableIn` method returns the number of seconds remaining until more attempts will be available:

지정된 키에 더 이상 시도할 수 있는 횟수가 남아 있지 않은 경우, `availableIn` 메서드를 호출하면 앞으로 이 시도 횟수가 다시 사용할 수 있어 질 때까지 남은 시간(초)을 반환합니다.

    use Illuminate\Support\Facades\RateLimiter;

    if (RateLimiter::tooManyAttempts('send-message:'.$user->id, $perMinute = 5)) {
        $seconds = RateLimiter::availableIn('send-message:'.$user->id);

        return 'You may try again in '.$seconds.' seconds.';
    }

<a name="clearing-attempts"></a>
### Clearing Attempts
### 시도 횟수 초기화

You may reset the number of attempts for a given rate limiter key using the `clear` method. For example, you may reset the number of attempts when a given message is read by the receiver:

`clear` 메소드를 사용하여 주어진 요청-속도-제한기(rate limiter) 키에 대한 시도 횟수를 초기화할 수 있습니다. 예를 들어 메세지 모델의 수신자가 주어진 메시지를 읽는 시도 횟수를 초기화할 수 있습니다.

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
