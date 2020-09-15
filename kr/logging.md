# Logging
# 로깅

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Configuration](#configuration)
- [설정하기](#configuration)
    - [Building Log Stacks](#building-log-stacks)
    - [로그 스택 구성하기](#building-log-stacks)
- [Writing Log Messages](#writing-log-messages)
- [로그 메세지 작성하기](#writing-log-messages)
    - [Writing To Specific Channels](#writing-to-specific-channels)
    - [채널을 지정하여 로그 기록하기](#writing-to-specific-channels)
- [Advanced Monolog Channel Customization](#advanced-monolog-channel-customization)
- [확장된 Monolog 채널 커스터마이징하기](#advanced-monolog-channel-customization)
    - [Customizing Monolog For Channels](#customizing-monolog-for-channels)
    - [채널에서 사용하는 Monolog 커스터마이징하기](#customizing-monolog-for-channels)
    - [Creating Monolog Handler Channels](#creating-monolog-handler-channels)
    - [Monolog 핸들러 채널 생성하기](#creating-monolog-handler-channels)
    - [Creating Channels Via Factories](#creating-channels-via-factories)
    - [팩토리를 사용하여 채널 생성하기](#creating-channels-via-factories)

<a name="introduction"></a>
## Introduction
## 시작하기

To help you learn more about what's happening within your application, Laravel provides robust logging services that allow you to log messages to files, the system error log, and even to Slack to notify your entire team.

애플리케이션에서 어떤일이 일어나고 있는지 알기 위해서, 라라벨은 편리한 로깅 서비스를 제공합니다. 이 로깅 서비스는 로그 메세지를 파일에 남기거나, 시스템 에러에 출력하거나 또는 팀 슬랙에 알림을 보낼 수 있도록 해줍니다.

Under the hood, Laravel utilizes the [Monolog](https://github.com/Seldaek/monolog) library, which provides support for a variety of powerful log handlers. Laravel makes it a cinch to configure these handlers, allowing you to mix and match them to customize your application's log handling.

라라벨은 내부적으로 다양하고 강력한 로그 핸들러를 제공하는 [Monolog](https://github.com/Seldaek/monolog) 라이브러리를 사용합니다. 라라벨은 이러한 핸들러 설정을 간편하게 해주고 이러한 로그 핸들러를 조합하고 커스터마이징하기 쉽도록 해줍니다.

<a name="configuration"></a>
## Configuration
## 설정하기

All of the configuration for your application's logging system is housed in the `config/logging.php` configuration file. This file allows you to configure your application's log channels, so be sure to review each of the available channels and their options. We'll review a few common options below.

애플리케이션의 로깅 시스템 설정은 `config/logging.php` 파일에 지정되어 있습니다. 이 파일을 통해서 애플리케이션의 로그 채널을 설정하기 때문에, 사용가능한 채널과 옵션을 살펴보도록 하십시오. 다음의 몇가지 일반적인 옵션을 살펴보도록 하겠습니다.

By default, Laravel will use the `stack` channel when logging messages. The `stack` channel is used to aggregate multiple log channels into a single channel. For more information on building stacks, check out the [documentation below](#building-log-stacks).

기본적으로 라라벨은 로그를 `stack` 채널을 통해서 생성합니다. `stack` 채널은 여러개의 로그 채널을 하나의 채널로 묶어서 사용한다는 의미입니다. 스택을 구성하는 보다 자세한 내용은 [아래의 문서](#building-log-stacks)를 참고하십시오.

#### Configuring The Channel Name
#### 채널 이름 설정하기

By default, Monolog is instantiated with a "channel name" that matches the current environment, such as `production` or `local`. To change this value, add a `name` option to your channel's configuration:

기본적으로 Monolog는 현재의 환경과 일치하는 `production` 또는 `local` 과 같은 이름의 "채널 이름"으로 인스턴스가 생성됩니다. 이 값을 변경하려면, 채널 설정에서 `name` 옵션을 추가하면 됩니다.

    'stack' => [
        'driver' => 'stack',
        'name' => 'channel-name',
        'channels' => ['single', 'slack'],
    ],

#### Available Channel Drivers
#### 사용가능한 채널 드라이버

Name | Description
------------- | -------------
`stack` | A wrapper to facilitate creating "multi-channel" channels
`single` | A single file or path based logger channel (`StreamHandler`)
`daily` | A `RotatingFileHandler` based Monolog driver which rotates daily
`slack` | A `SlackWebhookHandler` based Monolog driver
`papertrail` | A `SyslogUdpHandler` based Monolog driver
`syslog` | A `SyslogHandler` based Monolog driver
`errorlog` | A `ErrorLogHandler` based Monolog driver
`monolog` | A Monolog factory driver that may use any supported Monolog handler
`custom` | A driver that calls a specified factory to create a channel

이름 | 설명
------------- | -------------
`stack` | "다중 채널"을 생성할 수 있는 채널
`single` | 하나의 파일이나 경로 기반 로거 채널(`StreamHandler`)
`daily` | Monolog 드라이버를 기반으로 한 일별 로테이션을 하는 `RotatingFileHandler`
`slack` | Monolog 드라이버를 기반으로 한 `SlackWebhookHandler`
`papertrail` | Monolog 드라이버를 기반으로 한 `SyslogUdpHandler`
`syslog` | Monolog 드라이버를 기반으로 한 `SyslogHandler`
`errorlog` | Monolog 드라이버를 기반으로한 `ErrorLogHandler`
`monolog` | 지원가능한 Monolog 핸들러를 사용하는 Molog 팩토리 드라이버
`custom` | 지정된 팩토리를 호출해서 채널을 생성하는 커스텀 드라이버

> {tip} Check out the documentation on [advanced channel customization](#advanced-monolog-channel-customization) to learn more about the `monolog` and `custom` drivers.

> {tip} [고급 채널 커스터마이징](#advanced-monolog-channel-customization)에 대한 매뉴얼을 확인하여 `monolog` 와 `custom` 드라이버에 대해서 자세히 알아보십시오.

#### Configuring The Single and Daily Channels
#### 단일 및 데일리 채널 구성

The `single` and `daily` channels have three optional configuration options: `bubble`, `permission`, and `locking`.

`single`과 `daily` 채널은 `bubble`, `permission`, `locking`의 세 가지 옵션 설정 옵션을 가지고 있습니다.

Name | Description | Default
------------- | ------------- | -------------
`bubble` | Indicates if messages should bubble up to other channels after being handled | `true`
`permission` | The log file's permissions | `0644`
`locking` | Attempt to lock the log file before writing to it | `false`

이름 | 설명 | 기본값
------------- | ------------- | -------------
`bubble`| 처리 된 후에 메시지가 다른 채널로 버블링되어야하는지 나타냅니다. | `true`
`permission`| 로그 파일의 사용 권한 | `0644`
`locking`| 기록하기 전에 로그 파일을 잠그십시오. | `거짓`

#### Configuring The Papertrail Channel
#### Papertrail 채널 설정하기

The `papertrail` channel requires the `url` and `port` configuration options. You can obtain these values from [Papertrail](https://help.papertrailapp.com/kb/configuration/configuring-centralized-logging-from-php-apps/#send-events-from-php-app).

`papertrail` 채널에는 `url` 및 `port` 설정 옵션이 필요합니다. [Papertrail](https://help.papertrailapp.com/kb/configuration/configuring-centralized-logging-from-php-apps/#send-events-from-php-app)에서 이 값을 얻을 수 있습니다.

#### Configuring The Slack Channel
#### 슬랙 채널 설정하기

The `slack` channel requires a `url` configuration option. This URL should match a URL for an [incoming webhook](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks) that you have configured for your Slack team. By default, Slack will only receive logs at the `critical` level and above; however, you can adjust this in your `logging` configuration file.

`slack` 채널은 `url` 옵션을 필요로 합니다. URL은 팀에서 지정한 슬랙 [incoming webhook](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks) URL과 일치해야 합니다. 기본적으로 슬랙은 `critical` 수준 이상의 로그만 받습니다. 그러나 `logging` 설정 파일에서 이것을 조정할 수 있습니다.

<a name="building-log-stacks"></a>
### Building Log Stacks
### 로그 스택 구성하기

As previously mentioned, the `stack` driver allows you to combine multiple channels into a single log channel. To illustrate how to use log stacks, let's take a look at an example configuration that you might see in a production application:

앞서 말한바와 같이, `stack` 드라이버는 여러개의 채널을 하나의 로그 채널로 묶어 줍니다. 로그 스택을 사용하는 방법을 설명하기 위해 프로덕션 애플리케이션에서 확인할 수 있는 설정 예를 살펴 보겠습니다.

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

Let's dissect this configuration. First, notice our `stack` channel aggregates two other channels via its `channels` option: `syslog` and `slack`. So, when logging messages, both of these channels will have the opportunity to log the message.

위의 설정을 자세히 확인해보겠습니다. 먼저, `stack` 채널이 `channels` 옵션의 `syslog` 와 `slack` 두개의 다른 채널을 묶어 줍니다. 따라서 메세지를 로깅할 때에는 두개의 채널 모두에게 로그를 기록합니다.

#### Log Levels
#### 로그 레벨

Take note of the `level` configuration option present on the `syslog` and `slack` channel configurations in the example above. This option determines the minimum "level" a message must be in order to be logged by the channel. Monolog, which powers Laravel's logging services, offers all of the log levels defined in the [RFC 5424 specification](https://tools.ietf.org/html/rfc5424): **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info**, and **debug**.

앞의 예제에서 확인한 `syslog` 와 `slack` 채널 설정에 있는 `level` 옵션을 확인해 보십시오. 이 옵션은 채널에서 로그가 기록되어야 하는 최소 "레벨"을 결정합니다. 라라벨의 로그 서비스를 제공하는 Monolog는 [RFC 5424 표준 스펙](https://tools.ietf.org/html/rfc5424)에 정의된 모든 **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info**, **debug** 레벨을 지원합니다.

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

`Log` [파사드](/docs/{{version}}/facades)를 사용하여 로그 메세지를 작성할 수 있습니다. 앞에서 언급했듯이, 로그는 [RFC 5424 스펙](https://tools.ietf.org/html/rfc5424)에 정의된 8가지 로그 레벨 **emergency**, **alert**, **critical**, **error**, **warning**, **notice**, **info**, **debug** 을 제공합니다.

    Log::emergency($message);
    Log::alert($message);
    Log::critical($message);
    Log::error($message);
    Log::warning($message);
    Log::notice($message);
    Log::info($message);
    Log::debug($message);

So, you may call any of these methods to log a message for the corresponding level. By default, the message will be written to the default log channel as configured by your `config/logging.php` configuration file:

따라서 로그 메세지 기록할 때 일치하는 레벨에 맞는 메소드를 호출하면 됩니다. 기본적으로 메세지는 `config/logging.php` 설정 파일에 정의된 로그 채널로 기록됩니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\User;
    use Illuminate\Support\Facades\Log;

    class UserController extends Controller
    {
        /**
         * Show the profile for the given user.
         *
         * @param  int  $id
         * @return Response
         */
        public function showProfile($id)
        {
            Log::info('Showing user profile for user: '.$id);

            return view('user.profile', ['user' => User::findOrFail($id)]);
        }
    }

#### Contextual Information
#### 상태 정보를 추가하기

An array of contextual data may also be passed to the log methods. This contextual data will be formatted and displayed with the log message:

로그 메소드에 원하는 상태 정보를 배열로 전달할 수 있습니다. 이 데이터는 로그 메세지와 함께 출력됩니다.

    Log::info('User failed to login.', ['id' => $user->id]);

<a name="writing-to-specific-channels"></a>
### Writing To Specific Channels
### 채널을 지정하여 로그 기록하기

Sometimes you may wish to log a message to a channel other than your application's default channel. You may use the `channel` method on the `Log` facade to retrieve and log to any channel defined in your configuration file:

때로는 애플리케이션의 기본 채널이 아닌, 다른 채널을 지정하여 로그를 남기길 원할 수도 있습니다. `Log` 파사드의 ``channel` 메소드를 사용하면, 설정 파일에 정의된 채널을 찾아서 로그를 작성합니다.

    Log::channel('slack')->info('Something happened!');

If you would like to create an on-demand logging stack consisting of multiple channels, you may use the `stack` method:

로그를 작성할 때 임시로 여러 채널을 묶은 로그 스택을 구성하려면 다음과 같이 `stack` 메소드를 사용하면 됩니다.

    Log::stack(['single', 'slack'])->info('Something happened!');

<a name="advanced-monolog-channel-customization"></a>
## Advanced Monolog Channel Customization
## 확장된 Monolog 채널 커스터마이징하기

<a name="customizing-monolog-for-channels"></a>
### Customizing Monolog For Channels
### 채널에서 사용하는 Monolog 커스터마이징하기

Sometimes you may need complete control over how Monolog is configured for an existing channel. For example, you may want to configure a custom Monolog `FormatterInterface` implementation for a given channel's handlers.

때로는 채널에서 사용하는 Monolog를 제어할 필요가 있을 수도 있습니다. 예를 들어, 주어진 채널 핸들러에 대해서 `FormatterInterface` 구현을 커스터마이징 하기를 원할 수도 있습니다.

To get started, define a `tap` array on the channel's configuration. The `tap` array should contain a list of classes that should have an opportunity to customize (or "tap" into) the Monolog instance after it is created:

이렇게 하기 위해서 먼저 채널 설정 배열에 `tap` 속성을 정의하면 됩니다. `tap` 배열은 생성 된 Monolog 인스턴스를 커스터마이징 할 수 있는 클래스의 리스트로 구성되어 있어야 합니다.

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

> {tip} 모든 "tap" 크래스는 [서비스 컨테이너](/docs/{{version}}/container)에 의해서 의존성이 해결되기 때문에, 생성자에 정의된 의존성은 자동으로 주입됩니다.

<a name="creating-monolog-handler-channels"></a>
### Creating Monolog Handler Channels
### Monolog 핸들러 채널 생성하기

Monolog has a variety of [available handlers](https://github.com/Seldaek/monolog/tree/master/src/Monolog/Handler). In some cases, the type of logger you wish to create is merely a Monolog driver with an instance of a specific handler.  These channels can be created using the `monolog` driver.

Monolog 는 다양한 [핸들러](https://github.com/Seldaek/monolog/tree/master/src/Monolog/Handler)를 가지고 있습니다. 몇 가지 경우에서, 생성하고자 하는 로거의 타입은 특정 핸들러 인스턴스를 가지는 Monolog 드라이버일 뿐입니다.

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

<a name="creating-channels-via-factories"></a>
### Creating Channels Via Factories
### 팩토리를 사용하여 채널 생성하기

If you would like to define an entirely custom channel in which you have full control over Monolog's instantiation and configuration, you may specify a `custom` driver type in your `config/logging.php` configuration file. Your configuration should include a `via` option to point to the factory class which will be invoked to create the Monolog instance:

Monolog 인스턴스와 설정을 완벽하게 제어할 수 있는 커스텀 채널을 정의하려면, `config/logging.php` 설정 파일에 `custom` 드라이버를 지정할 수 있습니다. 설정에서는 팩토리에서 Monolog 인스턴스를 생성하기 위해서 호출해야 하는 클래스를 지정하는 `via` 옵션을 포함해야 합니다.

    'channels' => [
        'custom' => [
            'driver' => 'custom',
            'via' => App\Logging\CreateCustomLogger::class,
        ],
    ],

Once you have configured the `custom` channel, you're ready to define the class that will create your Monolog instance. This class only needs a single method: `__invoke`, which should return the Monolog instance:

`custom` 채널을 설정한 다음에, Monolog 인스턴스를 생성하는 클래스를 정의하면 됩니다. 이 클래스에서는 Monolog 인스턴스를 반환하는 `__invoke` 메소드를 정의할 필요가 있습니다.

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
