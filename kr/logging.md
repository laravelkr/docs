# Logging
# 로깅

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [Available Channel Drivers](#available-channel-drivers)
    - [사용 가능한 채널 드라이버](#available-channel-drivers)
    - [Channel Prerequisites](#channel-prerequisites)
    - [채널 전제 조건](#channel-prerequisites)
    - [Logging Deprecation Warnings](#logging-deprecation-warnings)
    - [사용 중단 경고 로깅](#logging-deprecation-warnings)
- [Building Log Stacks](#building-log-stacks)
- [로그 스택 설정하기](#building-log-stacks)
- [Writing Log Messages](#writing-log-messages)
- [로그 메세지 작성하기](#writing-log-messages)
    - [Contextual Information](#contextual-information)
    - [컨텍스트 정보](#contextual-information)
    - [Writing To Specific Channels](#writing-to-specific-channels)
    - [채널을 지정하여 로그 기록하기](#writing-to-specific-channels)
- [Monolog Channel Customization](#monolog-channel-customization)
- [Monolog 채널 커스터마이징하기](#monolog-channel-customization)
    - [Customizing Monolog For Channels](#customizing-monolog-for-channels)
    - [채널에서 사용하는 Monolog 커스터마이징하기](#customizing-monolog-for-channels)
    - [Creating Monolog Handler Channels](#creating-monolog-handler-channels)
    - [Monolog 핸들러 채널 생성하기](#creating-monolog-handler-channels)
    - [Creating Custom Channels Via Factories](#creating-custom-channels-via-factories)
    - [팩토리를 사용하여 커스텀 채널 생성하기](#creating-custom-channels-via-factories)

<a name="introduction"></a>
## Introduction
## 시작하기

To help you learn more about what's happening within your application, Laravel provides robust logging services that allow you to log messages to files, the system error log, and even to Slack to notify your entire team.

애플리케이션에서 어떤일이 일어나고 있는지 알기 위해서, 라라벨은 편리한 로깅 서비스를 제공합니다. 이 로깅 서비스는 로그 메세지를 파일에 남기거나, 시스템 에러에 출력하거나 또는 팀 슬랙에 알림을 보낼 수 있도록 해줍니다.

Laravel logging is based on "channels". Each channel represents a specific way of writing log information. For example, the `single` channel writes log files to a single log file, while the `slack` channel sends log messages to Slack. Log messages may be written to multiple channels based on their severity.

라라벨 로깅은 "채널"을 기반으로 합니다. 각 채널은 로그 정보를 작성하는 특정 방법을 나타냅니다. 예를 들어 `single` 채널은 단일 로그 파일에 로그 파일을 기록하고 `slack` 채널은 Slack에 로그 메시지를 보냅니다. 로그 메시지는 심각도에 따라 여러 채널에 기록될 수 있습니다.

Under the hood, Laravel utilizes the [Monolog](https://github.com/Seldaek/monolog) library, which provides support for a variety of powerful log handlers. Laravel makes it a cinch to configure these handlers, allowing you to mix and match them to customize your application's log handling.

라라벨은 내부적으로 다양하고 강력한 로그 핸들러를 제공하는 [Monolog](https://github.com/Seldaek/monolog) 라이브러리를 사용합니다. 라라벨은 이러한 핸들러 설정을 간편하게 해주고 이러한 로그 핸들러를 조합하고 커스터마이징하기 쉽도록 해줍니다.

<a name="configuration"></a>
## Configuration
## 설정하기

All of the configuration options for your application's logging behavior is housed in the `config/logging.php` configuration file. This file allows you to configure your application's log channels, so be sure to review each of the available channels and their options. We'll review a few common options below.

애플리케이션의 로깅 동작에 대한 모든 설정 옵션은 `config/logging.php` 설정 파일에 존재합니다. 이 파일을 사용하여 애플리케이션의 로그 채널을 설정할 수 있으므로, 사용 가능한 각 채널과 해당 옵션을 검토하십시오. 아래에서 몇 가지 일반적인 옵션을 검토합니다.

By default, Laravel will use the `stack` channel when logging messages. The `stack` channel is used to aggregate multiple log channels into a single channel. For more information on building stacks, check out the [documentation below](#building-log-stacks).

기본적으로 라라벨은 로그를 `stack` 채널을 통해서 생성합니다. `stack` 채널은 여러개의 로그 채널을 하나의 채널로 묶어서 사용한다는 의미입니다. 스택을 설정하는 보다 자세한 내용은 [아래의 문서](#building-log-stacks)를 참고하십시오.

<a name="configuring-the-channel-name"></a>
#### Configuring The Channel Name
#### 채널 이름 설정

By default, Monolog is instantiated with a "channel name" that matches the current environment, such as `production` or `local`. To change this value, add a `name` option to your channel's configuration:

기본적으로 Monolog는 현재의 환경과 일치하는 `production` 또는 `local` 과 같은 이름의 "채널 이름"으로 인스턴스가 생성됩니다. 이 값을 변경하려면, 채널 설정에서 `name` 옵션을 추가하면 됩니다.

    'stack' => [
        'driver' => 'stack',
        'name' => 'channel-name',
        'channels' => ['single', 'slack'],
    ],

<a name="available-channel-drivers"></a>
### Available Channel Drivers
### 사용 가능한 채널 드라이버

Each log channel is powered by a "driver". The driver determines how and where the log message is actually recorded. The following log channel drivers are available in every Laravel application. An entry for most of these drivers is already present in your application's `config/logging.php` configuration file, so be sure to review this file to become familiar with its contents:

각 로그 채널은 "드라이버"에 의해 구동됩니다. 드라이버는 로그 메시지가 실제로 기록되는 방법과 위치를 결정합니다. 다음 로그 채널 드라이버는 모든 Laravel 애플리케이션에서 사용할 수 있습니다. 대부분의 이러한 드라이버에 대한 항목은 이미 애플리케이션의 `config/logging.php` 설정 파일에 있으므로 이 파일을 검토하여 내용에 익숙해지도록 하십시오.

Name | Description
------------- | -------------
`custom` | A driver that calls a specified factory to create a channel
`daily` | A `RotatingFileHandler` based Monolog driver which rotates daily
`errorlog` | An `ErrorLogHandler` based Monolog driver
`monolog` | A Monolog factory driver that may use any supported Monolog handler
`null` | A driver that discards all log messages
`papertrail` | A `SyslogUdpHandler` based Monolog driver
`single` | A single file or path based logger channel (`StreamHandler`)
`slack` | A `SlackWebhookHandler` based Monolog driver
`stack` | A wrapper to facilitate creating "multi-channel" channels
`syslog` | A `SyslogHandler` based Monolog driver

이름 | 설명
------------- | -------------
`custom` | 지정된 팩토리를 호출하여 채널을 생성하는 드라이버
`daily` | 매일 회전하는 `RotatingFileHandler` 기반 Monolog 드라이버
`errorlog` | `ErrorLogHandler` 기반 Monolog 드라이버
`monolog` | 지원되는 Monolog 처리기를 사용할 수 있는 Monolog 팩토리 드라이버
`null` | 모든 로그 메시지를 버리는 드라이버
`papertrail` | `SyslogUdpHandler` 기반 Monolog 드라이버
`single` | 단일 파일 또는 경로 기반 로그 채널 (`StreamHandler`)
`slack` | `SlackWebhookHandler` 기반 Monolog 드라이버
`stack` | "다중 채널" 채널 생성을 용이하게 하는 래퍼
`syslog` | `SyslogHandler` 기반 Monolog 드라이버

> {tip} Check out the documentation on [advanced channel customization](#monolog-channel-customization) to learn more about the `monolog` and `custom` drivers.

> {tip} `monolog` 및 `custom` 드라이버에 대한 자세한 내용은 [고급 채널 사용자 정의](#monolog-channel-customization) 문서를 확인하세요.

<a name="channel-prerequisites"></a>
### Channel Prerequisites
### 채널 전제 조건

<a name="configuring-the-single-and-daily-channels"></a>
#### Configuring The Single and Daily Channels
#### 단일 및 데일리 채널 설정

The `single` and `daily` channels have three optional configuration options: `bubble`, `permission`, and `locking`.

`single` 및 `daily` 채널에는 `bubble`, `permission` 및 `locking`의 세 가지 선택적 설정 옵션이 있습니다.

Name | Description | Default
------------- | ------------- | -------------
`bubble` | Indicates if messages should bubble up to other channels after being handled | `true`
`locking` | Attempt to lock the log file before writing to it | `false`
`permission` | The log file's permissions | `0644`

이름 | 설명 | 기본
------------- | ------------- | -------------
`bubble` | 메시지가 처리된 후 다른 채널로 버블링되어야 하는지 여부를 나타냅니다. | `true`
`locking` | 쓰기 전에 로그 파일을 잠그십시오. | `false`
`permission` | 로그 파일의 권한 | `0644`

<a name="configuring-the-papertrail-channel"></a>
#### Configuring The Papertrail Channel
#### Papertrail 채널 설정하기

The `papertrail` channel requires the `host` and `port` configuration options. You can obtain these values from [Papertrail](https://help.papertrailapp.com/kb/configuration/configuring-centralized-logging-from-php-apps/#send-events-from-php-app).

`papertrail` 채널에는 `host` 및 `port` 설정 옵션이 필요합니다. 이 값들은 [Papertrail](https://help.papertrailapp.com/kb/configuration/configuring-centralized-logging-from-php-apps/#send-events-from-php-app) 에서 확인 할 수 있습니다.

<a name="configuring-the-slack-channel"></a>
#### Configuring The Slack Channel
#### 슬랙 채널 설정하기

The `slack` channel requires a `url` configuration option. This URL should match a URL for an [incoming webhook](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks) that you have configured for your Slack team.

`slack` 채널에는 `url` 설정 옵션이 필요합니다. 이 URL은 Slack에서 팀을 위해 설정한 [incoming webhook](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks) 의 URL과 일치해야 합니다.

By default, Slack will only receive logs at the `critical` level and above; however, you can adjust this in your `config/logging.php` configuration file by modifying the `level` configuration option within your Slack log channel's configuration array.

기본적으로 Slack은 `critical` 수준 이상의 로그만 수신합니다. 그러나 `config/logging.php` 설정 파일의 Slack 로그 채널의 설정 배열에서 `level` 설정 옵션을 수정하여 이를 조정할 수 있습니다.

<a name="logging-deprecation-warnings"></a>
### Logging Deprecation Warnings
### 사용 중단 경고 로깅

PHP, Laravel, and other libraries often notify their users that some of their features have been deprecated and will be removed in a future version. If you would like to log these deprecation warnings, you may specify your preferred `deprecations` log channel in your application's `config/logging.php` configuration file:

PHP, 라라벨 및 기타 라이브러리는 사용자에게 일부 기능이 더 이상 사용되지 않으며, 향후 버전에서 제거될 것임을 알리는 경우가 많습니다. 이러한 지원 중단 경고를 기록하려면 애플리케이션의 `config/logging.php` 설정 파일에서 선호하는 `deprecations` 로그 채널을 지정할 수 있습니다.

    'deprecations' => env('LOG_DEPRECATIONS_CHANNEL', 'null'),

    'channels' => [
        ...
    ]

Or, you may define a log channel named `deprecations`. If a log channel with this name exists, it will always be used to log deprecations:

또는 `deprecations`라는 로그 채널을 정의할 수 있습니다. 이 이름의 로그 채널이 있으면 항상 사용 중단을 기록하는 데 사용됩니다.

    'channels' => [
        'deprecations' => [
            'driver' => 'single',
            'path' => storage_path('logs/php-deprecation-warnings.log'),
        ],
    ],

<a name="building-log-stacks"></a>
## Building Log Stacks
## 로그 스택 설정하기

As mentioned previously, the `stack` driver allows you to combine multiple channels into a single log channel for convenience. To illustrate how to use log stacks, let's take a look at an example configuration that you might see in a production application:

앞서 언급했듯이 `stack` 드라이버를 사용하면, 편의를 위해 여러 채널을 단일 로그 채널로 결합할 수 있습니다. 로그 스택을 사용하는 방법을 설명하기 위해 프로덕션 애플리케이션에서 볼 수 있는 예제 설정을 살펴보겠습니다.

    'channels' => [
        'stack' => [
            'driver' => 'stack',
            'channels' => ['syslog', 'slack'],
        ],

        'syslog' => [
            'driver' => 'syslog',
            'level' => 'debug',
        ],

        'slack' => [
            'driver' => 'slack',
            'url' => env('LOG_SLACK_WEBHOOK_URL'),
            'username' => 'Laravel Log',
            'emoji' => ':boom:',
            'level' => 'critical',
        ],
    ],

Let's dissect this configuration. First, notice our `stack` channel aggregates two other channels via its `channels` option: `syslog` and `slack`. So, when logging messages, both of these channels will have the opportunity to log the message. However, as we will see below, whether these channels actually log the message may be determined by the message's severity / "level".

이 설정을 분석해 보겠습니다. 먼저 `stack` 채널이 `channels` 옵션을 통해 `syslog`와 `slack`이라는 두 개의 다른 채널을 합칩니다. 따라서 메시지를 기록할 때, 이 두 채널 모두 메시지를 기록할 기회를 갖게 됩니다. 그러나 아래에서 볼 수 있듯이 이러한 채널이 실제로 메시지를 기록하는지 여부는 메시지의 심각도 "레벨"에 따라 결정할 수 있습니다.

<a name="log-levels"></a>
#### Log Levels
#### 로그 레벨

Take note of the `level` configuration option present on the `syslog` and `slack` channel configurations in the example above. This option determines the minimum "level" a message must be in order to be logged by the channel. Monolog, which powers Laravel's logging services, offers all of the log levels defined in the [RFC 5424 specification](https://tools.ietf.org/html/rfc5424): **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info**, and **debug**.

앞의 예제에서 확인한 `syslog` 와 `slack` 채널 설정에 있는 `level` 옵션을 확인해 보십시오. 이 옵션은 채널에서 로그가 기록되어야 하는 최소 "레벨"을 결정합니다. 라라벨의 로그 서비스를 제공하는 Monolog는 [RFC 5424 표준 스펙](https://tools.ietf.org/html/rfc5424) 에 정의된 모든 **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info**, **debug** 레벨을 지원합니다.

So, imagine we log a message using the `debug` method:

따라서, `debug` 메소드를 사용하여 로그를 기록하는 것을 생각해 보겠습니다.

    Log::debug('An informational message.');

Given our configuration, the `syslog` channel will write the message to the system log; however, since the error message is not `critical` or above, it will not be sent to Slack. However, if we log an `emergency` message, it will be sent to both the system log and Slack since the `emergency` level is above our minimum level threshold for both channels:

주어진 설정에 따라서 `syslog` 채널은 전달된 메세지를 시스템 로그파일에 기록합니다. 그렇지만 슬랙 채널에는 에러 메세지가 `critical` 이상으로 설정되어 있기 때문에, 메세지가 전달되지 않습니다. `emergency` 메세지를 로깅 하려고 할때에는, 두 채널에 설정된 최소 로그 레벨이 `emergency` 레벨 보다 낮기 때문에, 두 채널 모두에서 메세지가 전달됩니다.

    Log::emergency('The system is down!');

<a name="writing-log-messages"></a>
## Writing Log Messages
## 로그 메세지 작성하기

You may write information to the logs using the `Log` [facade](/docs/{{version}}/facades). As previously mentioned, the logger provides the eight logging levels defined in the [RFC 5424 specification](https://tools.ietf.org/html/rfc5424): **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info** and **debug**:

`Log` [파사드](/docs/{{version}}/facades)를 사용하여 로그 메세지를 작성할 수 있습니다. 앞에서 언급했듯이, 로그는 [RFC 5424 스펙](https://tools.ietf.org/html/rfc5424) 에 정의된 8가지 로그 레벨 **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info**, **debug** 을 제공합니다.

    Log::emergency($message);
    Log::alert($message);
    Log::critical($message);
    Log::error($message);
    Log::warning($message);
    Log::notice($message);
    Log::info($message);
    Log::debug($message);

You may call any of these methods to log a message for the corresponding level. By default, the message will be written to the default log channel as configured by your `logging` configuration file:

이러한 메서드를 호출하여 해당 수준에 대한 메시지를 기록할 수 있습니다. 기본적으로 메시지는 `logging` 설정 파일에 의해 설정된 기본 로그 채널에 기록됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\User;
    use Illuminate\Support\Facades\Log;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return \Illuminate\Http\Response
         */
        public function show($id)
        {
            Log::info('Showing the user profile for user: '.$id);

            return view('user.profile', [
                'user' => User::findOrFail($id)
            ]);
        }
    }

<a name="contextual-information"></a>
### Contextual Information
### 상태 정보를 추가하기

An array of contextual data may be passed to the log methods. This contextual data will be formatted and displayed with the log message:

컨텍스트 데이터의 배열이 로그 메소드에 전달될 수 있습니다. 이 컨텍스트 데이터는 형식이 지정되고 로그 메시지와 함께 표시됩니다.

    use Illuminate\Support\Facades\Log;

    Log::info('User failed to login.', ['id' => $user->id]);

Occasionally, you may wish to specify some contextual information that should be included with all subsequent log entries. For example, you may wish to log a request ID that is associated with each incoming request to your application. To accomplish this, you may call the `Log` facade's `withContext` method:

경우에 따라 모든 후속 로그 항목에 포함되어야 하는 일부 컨텍스트 정보를 지정할 수 있습니다. 예를 들어 애플리케이션에 들어오는 각 요청과 연결된 요청 ID를 기록할 수 있습니다. 이를 달성하기 위해 `Log` 파사드의 `withContext` 메소드를 호출할 수 있습니다.

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Support\Facades\Log;
    use Illuminate\Support\Str;

    class AssignRequestId
    {
        /**
         * Handle an incoming request.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return mixed
         */
        public function handle($request, Closure $next)
        {
            $requestId = (string) Str::uuid();

            Log::withContext([
                'request-id' => $requestId
            ]);

            return $next($request)->header('Request-Id', $requestId);
        }
    }

<a name="writing-to-specific-channels"></a>
### Writing To Specific Channels
### 채널을 지정하여 로그 기록하기

Sometimes you may wish to log a message to a channel other than your application's default channel. You may use the `channel` method on the `Log` facade to retrieve and log to any channel defined in your configuration file:

때로는 애플리케이션의 기본 채널이 아닌, 다른 채널을 지정하여 로그를 남기길 원할 수도 있습니다. `Log` 파사드의 `channel` 메소드를 사용하면, 설정 파일에 정의된 채널을 찾아서 로그를 작성합니다.

    use Illuminate\Support\Facades\Log;

    Log::channel('slack')->info('Something happened!');

If you would like to create an on-demand logging stack consisting of multiple channels, you may use the `stack` method:

로그를 작성할 때 임시로 여러 채널을 묶은 로그 스택을 설정하려면 다음과 같이 `stack` 메소드를 사용하면 됩니다.

    Log::stack(['single', 'slack'])->info('Something happened!');

<a name="on-demand-channels"></a>
#### On-Demand Channels
#### 주문형-On-Demand 채널

It is also possible to create an on-demand channel by providing the configuration at runtime without that configuration being present in your application's `logging` configuration file. To accomplish this, you may pass a configuration array to the `Log` facade's `build` method:

애플리케이션의 `logging` 설정 파일에 해당 설정이 없는 상태에서, 런타임에 설정을 제공하여 주문형-On-Demand 채널을 생성할 수도 있습니다. 이를 달성하기 위해 `Log` 파사드의 `build` 메소드에 설정 배열을 전달할 수 있습니다.

    use Illuminate\Support\Facades\Log;

    Log::build([
      'driver' => 'single',
      'path' => storage_path('logs/custom.log'),
    ])->info('Something happened!');

You may also wish to include an on-demand channel in an on-demand logging stack. This can be achieved by including your on-demand channel instance in the array passed to the `stack` method:

주문형-On-Demand 로깅 스택에 주문형-On-Demand 채널을 포함할 수도 있습니다. 이것은 `stack` 메소드에 전달된 배열에 주문형-On-Demand 채널 인스턴스를 포함하여 달성할 수 있습니다.

    use Illuminate\Support\Facades\Log;

    $channel = Log::build([
      'driver' => 'single',
      'path' => storage_path('logs/custom.log'),
    ]);

    Log::stack(['slack', $channel])->info('Something happened!');

<a name="monolog-channel-customization"></a>
## Monolog Channel Customization
## Monolog 채널 커스터마이징하기

<a name="customizing-monolog-for-channels"></a>
### Customizing Monolog For Channels
### 채널에서 사용하는 Monolog 커스터마이징하기

Sometimes you may need complete control over how Monolog is configured for an existing channel. For example, you may want to configure a custom Monolog `FormatterInterface` implementation for Laravel's built-in `single` channel.

때로는 Monolog가 기존 채널에 대해 설정되는 방식을 원하는 대로 조정 할 수도 있습니다. 예를 들어, 라라벨의 내장 `single` 채널에 대한 사용자 지정 Monolog `FormatterInterface` 구현체로 변경 할 수 있습니다.

To get started, define a `tap` array on the channel's configuration. The `tap` array should contain a list of classes that should have an opportunity to customize (or "tap" into) the Monolog instance after it is created. There is no conventional location where these classes should be placed, so you are free to create a directory within your application to contain these classes:

시작하려면 채널 설정에서 `tap` 배열을 정의하세요. `tap` 배열은 생성된 후 Monolog 인스턴스를 사용자 정의(또는 "탭")할 기회가 있어야 하는 클래스 목록을 포함해야 합니다. 이런 클래스를 생성해야만 하는 지정된 위치가 없으므로, 애플리케이션 내에 이러한 클래스를 포함할 디렉토리를 원하는 대로 생성할 수 있습니다.

    'single' => [
        'driver' => 'single',
        'tap' => [App\Logging\CustomizeFormatter::class],
        'path' => storage_path('logs/laravel.log'),
        'level' => 'debug',
    ],

Once you have configured the `tap` option on your channel, you're ready to define the class that will customize your Monolog instance. This class only needs a single method: `__invoke`, which receives an `Illuminate\Log\Logger` instance. The `Illuminate\Log\Logger` instance proxies all method calls to the underlying Monolog instance:

채널에 `tap` 옵션을 설정했다면, Monolog 인스턴스를 커스터마이징 하는 클래스를 정의하면 됩니다. 이 클래스에서는 `Illuminate\Log\Logger` 인스턴스를 전달받는 `__invoke` 메소드 하나만 있으면 됩니다. `Illuminate\Log\Logger` 인스턴스는 기본 Monolog 인스턴스에 대한 모든 메소드 호출을 프록시합니다.

    <?php

    namespace App\Logging;

    use Monolog\Formatter\LineFormatter;

    class CustomizeFormatter
    {
        /**
         * Customize the given logger instance.
         *
         * @param  \Illuminate\Log\Logger  $logger
         * @return void
         */
        public function __invoke($logger)
        {
            foreach ($logger->getHandlers() as $handler) {
                $handler->setFormatter(new LineFormatter(
                    '[%datetime%] %channel%.%level_name%: %message% %context% %extra%'
                ));
            }
        }
    }

> {tip} All of your "tap" classes are resolved by the [service container](/docs/{{version}}/container), so any constructor dependencies they require will automatically be injected.

> {tip} 모든 "tap" 클래스는 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 의존성이 해결되기 때문에, 생성자에 정의된 의존성은 자동으로 주입됩니다.

<a name="creating-monolog-handler-channels"></a>
### Creating Monolog Handler Channels
### Monolog 핸들러 채널 생성하기

Monolog has a variety of [available handlers](https://github.com/Seldaek/monolog/tree/main/src/Monolog/Handler) and Laravel does not include a built-in channel for each one. In some cases, you may wish to create a custom channel that is merely an instance of a specific Monolog handler that does not have a corresponding Laravel log driver.  These channels can be easily created using the `monolog` driver.

Monolog에는 다양한 [사용 가능한 핸들러](https://github.com/Seldaek/monolog/tree/main/src/Monolog/Handler) 가 있으며 라라벨에는 각각에 대한 내장 채널이 포함되어 있지 않습니다. 어떤 경우에는 해당 라라벨 로그 드라이버가 없는 특정 Monolog 핸들러의 인스턴스인 사용자 정의 채널을 생성하고자 할 수 있습니다. 이러한 채널은 `monolog` 드라이버를 사용하여 쉽게 만들 수 있습니다.

When using the `monolog` driver, the `handler` configuration option is used to specify which handler will be instantiated. Optionally, any constructor parameters the handler needs may be specified using the `with` configuration option:

`monolog` 드라이버를 사용할 때, `handler` 설정 옵션은 어떤 핸들러가 인스턴스화 되어야 하는지 지정하는데 사용합니다. 옵션으로, 특정 핸들러의 생성자 파라미터가 필요한 경우 `with` 설정을 사용하여 필요한 옵션을 지정할 수 있습니다.

    'logentries' => [
        'driver'  => 'monolog',
        'handler' => Monolog\Handler\SyslogUdpHandler::class,
        'with' => [
            'host' => 'my.logentries.internal.datahubhost.company.com',
            'port' => '10000',
        ],
    ],

<a name="monolog-formatters"></a>
#### Monolog Formatters
#### Monolog Formatters

When using the `monolog` driver, the Monolog `LineFormatter` will be used as the default formatter. However, you may customize the type of formatter passed to the handler using the `formatter` and `formatter_with` configuration options:

`monolog` 드라이버를 사용할 때, Monolog 의 `LineFormatter` 가 기본적인 포맷터로 사용됩니다. 그렇지만, 핸들러가 사용할 `formatter` 와 `formatter_with` 설정 옵션을 사용하여 포맷터의 타입을 커스터마이징 할 수 있습니다.

    'browser' => [
        'driver' => 'monolog',
        'handler' => Monolog\Handler\BrowserConsoleHandler::class,
        'formatter' => Monolog\Formatter\HtmlFormatter::class,
        'formatter_with' => [
            'dateFormat' => 'Y-m-d',
        ],
    ],

If you are using a Monolog handler that is capable of providing its own formatter, you may set the value of the `formatter` configuration option to `default`:

고유한 formatter를 제공하는 Monolog 핸들러를 사용한다면, `formatter` 설정 옵션을 `default` 설정을 사용하면 됩니다.

    'newrelic' => [
        'driver' => 'monolog',
        'handler' => Monolog\Handler\NewRelicHandler::class,
        'formatter' => 'default',
    ],

<a name="creating-custom-channels-via-factories"></a>
### Creating Custom Channels Via Factories
### 팩토리를 사용하여 커스텀 채널 생성하기

If you would like to define an entirely custom channel in which you have full control over Monolog's instantiation and configuration, you may specify a `custom` driver type in your `config/logging.php` configuration file. Your configuration should include a `via` option that contains the name of the factory class which will be invoked to create the Monolog instance:

Monolog의 인스턴스화 및 설정을 완전히 제어할 수 있는 사용자 정의 채널을 정의하려면 `config/logging.php` 설정 파일에 `custom` 드라이버 유형을 지정할 수 있습니다. 설정에는 Monolog 인스턴스를 생성하기 위해 호출될 팩토리 클래스의 이름이 포함된 `via` 옵션이 포함되어야 합니다.

    'channels' => [
        'example-custom-channel' => [
            'driver' => 'custom',
            'via' => App\Logging\CreateCustomLogger::class,
        ],
    ],

Once you have configured the `custom` driver channel, you're ready to define the class that will create your Monolog instance. This class only needs a single `__invoke` method which should return the Monolog logger instance. The method will receive the channels configuration array as its only argument:

`custom` 드라이버 채널을 설정했으면 Monolog 인스턴스를 생성할 클래스를 정의할 준비가 된 것입니다. 이 클래스는 Monolog 로거 인스턴스를 반환해야 하는 단일 `__invoke` 메서드만 필요합니다. 이 메서드는 채널 설정 배열을 유일한 인수로 받습니다.

    <?php

    namespace App\Logging;

    use Monolog\Logger;

    class CreateCustomLogger
    {
        /**
         * Create a custom Monolog instance.
         *
         * @param  array  $config
         * @return \Monolog\Logger
         */
        public function __invoke(array $config)
        {
            return new Logger(...);
        }
    }
