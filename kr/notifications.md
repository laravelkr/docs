# Notifications
# Notifications-알림

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Generating Notifications](#generating-notifications)
- [알림 생성하기](#generating-notifications)
- [Sending Notifications](#sending-notifications)
- [알림 발송하기](#sending-notifications)
    - [Using The Notifiable Trait](#using-the-notifiable-trait)
    - [Notifiable 트레이트-trait 사용하기](#using-the-notifiable-trait)
    - [Using The Notification Facade](#using-the-notification-facade)
    - [Notification 파사드 사용하기](#using-the-notification-facade)
    - [Specifying Delivery Channels](#specifying-delivery-channels)
    - [전달할 채널 지정하기](#specifying-delivery-channels)
    - [Queueing Notifications](#queueing-notifications)
    - [Queue-큐를 통한 Notifications 사용](#queueing-notifications)
    - [On-Demand Notifications](#on-demand-notifications)
    - [필요시 대상을 지정한 Notifications](#on-demand-notifications)
- [Mail Notifications](#mail-notifications)
- [이메일을 통한 알림](#mail-notifications)
    - [Formatting Mail Messages](#formatting-mail-messages)
    - [이메일 메세지 포맷 지정하기](#formatting-mail-messages)
    - [Customizing The Sender](#customizing-the-sender)
    - [Sender 커스터마이징](#customizing-the-sender)
    - [Customizing The Recipient](#customizing-the-recipient)
    - [수신자 설정하기](#customizing-the-recipient)
    - [Customizing The Subject](#customizing-the-subject)
    - [제목 설정하기](#customizing-the-subject)
    - [Customizing The Mailer](#customizing-the-mailer)
    - [메일러 설정하기](#customizing-the-mailer)
    - [Customizing The Templates](#customizing-the-templates)
    - [템플릿 설정하기](#customizing-the-templates)
    - [Attachments](#mail-attachments)
    - [첨부파일](#mail-attachments)
    - [Adding Tags & Metadata](#adding-tags-metadata)
    - [태그 & 메타데이터 지정하기](#adding-tags-metadata)
    - [Customizing The Symfony Message](#customizing-the-symfony-message)
    - [심포니 메시지 커스터마이징](#customizing-the-symfony-message)
    - [Using Mailables](#using-mailables)
    - [Mailable 사용](#using-mailables)
    - [Previewing Mail Notifications](#previewing-mail-notifications)
    - [메일 알림 미리보기](#previewing-mail-notifications)
- [Markdown Mail Notifications](#markdown-mail-notifications)
- [마크다운 이메일 알림](#markdown-mail-notifications)
    - [Generating The Message](#generating-the-message)
    - [메세지 클래스 생성하기](#generating-the-message)
    - [Writing The Message](#writing-the-message)
    - [메세지 작성하기](#writing-the-message)
    - [Customizing The Components](#customizing-the-components)
    - [컴포넌트 커스터마이징 하기](#customizing-the-components)
- [Database Notifications](#database-notifications)
- [데이터베이스 알림](#database-notifications)
    - [Prerequisites](#database-prerequisites)
    - [사전준비사항](#database-prerequisites)
    - [Formatting Database Notifications](#formatting-database-notifications)
    - [데이터베이스 알림 포맷 지정하기](#formatting-database-notifications)
    - [Accessing The Notifications](#accessing-the-notifications)
    - [알림에 엑세스하기](#accessing-the-notifications)
    - [Marking Notifications As Read](#marking-notifications-as-read)
    - [알림을 읽음 표시로 전환하기](#marking-notifications-as-read)
- [Broadcast Notifications](#broadcast-notifications)
- [브로드캐스팅 알림](#broadcast-notifications)
    - [Prerequisites](#broadcast-prerequisites)
    - [사전 준비사항](#broadcast-prerequisites)
    - [Formatting Broadcast Notifications](#formatting-broadcast-notifications)
    - [브로드캐스팅 알림 포맷 지정하기](#formatting-broadcast-notifications)
    - [Listening For Notifications](#listening-for-notifications)
    - [알림을 위한 리스너](#listening-for-notifications)
- [SMS Notifications](#sms-notifications)
- [SMS 알림](#sms-notifications)
    - [Prerequisites](#sms-prerequisites)
    - [사전준비사항](#sms-prerequisites)
    - [Formatting SMS Notifications](#formatting-sms-notifications)
    - [SMS 알림 포맷 지정하기](#formatting-sms-notifications)
    - [Unicode Content](#unicode-content)
    - [유니코드 콘텐츠](#unicode-content)
    - [Customizing The "From" Number](#customizing-the-from-number)
    - [발신자 번호 수정하기](#customizing-the-from-number)
    - [Adding A Client Reference](#adding-a-client-reference)
    - [클라이언트 참조 추가](#adding-a-client-reference)
    - [Routing SMS Notifications](#routing-sms-notifications)
    - [SMS 알림 라우팅](#routing-sms-notifications)
- [Slack Notifications](#slack-notifications)
- [슬랙-Slack 알림](#slack-notifications)
    - [Prerequisites](#slack-prerequisites)
    - [사전준비사항](#slack-prerequisites)
    - [Formatting Slack Notifications](#formatting-slack-notifications)
    - [Slack 알림 포맷 지정하기](#formatting-slack-notifications)
    - [Slack Interactivity](#slack-interactivity)
    - [슬랙 앱의 상호작용 사용하기](#slack-interactivity)
    - [Routing Slack Notifications](#routing-slack-notifications)
    - [슬랙 알림 라우팅](#routing-slack-notifications)
    - [Notifying External Slack Workspaces](#notifying-external-slack-workspaces)
    - [외부 슬랙 워크스페이스에 알리기](#notifying-external-slack-workspaces)
- [Localizing Notifications](#localizing-notifications)
- [알림의 현지화](#localizing-notifications)
- [Testing](#testing)
- [테스팅](#testing)
- [Notification Events](#notification-events)
- [알림 이벤트](#notification-events)
- [Custom Channels](#custom-channels)
- [사용자 정의(커스텀)채널](#custom-channels)

<a name="introduction"></a>
## Introduction
## 시작하기

In addition to support for [sending email](/docs/{{version}}/mail), Laravel provides support for sending notifications across a variety of delivery channels, including email, SMS (via [Vonage](https://www.vonage.com/communications-apis/), formerly known as Nexmo), and [Slack](https://slack.com). In addition, a variety of [community built notification channels](https://laravel-notification-channels.com/about/#suggesting-a-new-channel) have been created to send notifications over dozens of different channels! Notifications may also be stored in a database so they may be displayed in your web interface.

[이메일 발송](/docs/{{version}}/mail)기능 이외에도 Laravel은 이메일, SMS ([Vonage](https://www.vonage.com/communications-apis/) 이전에 Nexmo로 알려진 서비스 제공회사) 또는 [Slack](https://slack.com) 를 포함한 다양한 전달 방법들을 통해 알림 전송기능을 제공합니다. 또한 수십 개의 다른 채널로 알림을 보낼 수 있도록 다양한 [커뮤니티 제작 알림 채널](https://laravel-notification-channels.com/about/#suggesting-a-new-channel) 만들어져 있습니다. 물론, 알림은 데이터베이스에 저장되어 웹 인터페이스에 표시될 수도 있습니다.

> **Note**  
> 역자주 국내 SMS으로 [SENS for Laravel](https://github.com/seungmun/laravel-sens) 이 있습니다.

Typically, notifications should be short, informational messages that notify users of something that occurred in your application. For example, if you are writing a billing application, you might send an "Invoice Paid" notification to your users via the email and SMS channels.

일반적으로, 알림은 애플리케이션 안에서 발생한 어떤 사항을 사용자에게 전달하기 위한 짧은 정보를 담은 메세지입니다. 예를들어, 결제가 필요한 애플리케이션을 작성중이라면, "결제 완료" 알림을 이메일이나 SMS를 통해서 사용자에게 발송할 수 있습니다.

<a name="generating-notifications"></a>
## Generating Notifications
## 알림 생성하기

In Laravel, each notification is represented by a single class that is typically stored in the `app/Notifications` directory. Don't worry if you don't see this directory in your application - it will be created for you when you run the `make:notification` Artisan command:

라라벨에서 각각의 알림은 일반적으로 `app/Notifications` 디렉토리에 저장된 단일 클래스로 표현할 수 있습니다. 애플리케이션에서 이 디렉토리를 볼 수 없더라도 걱정하지 마십시오, `make:notification` 아티즌 명령어를 실행할 때 디렉토리가 생성됩니다.

```shell
php artisan make:notification InvoicePaid
```

This command will place a fresh notification class in your `app/Notifications` directory. Each notification class contains a `via` method and a variable number of message building methods, such as `toMail` or `toDatabase`, that convert the notification to a message tailored for that particular channel.

이 명령어는 새로운 알림 클래스를 `app/Notifications` 디렉토리에 생성합니다. 각각의 알림 클래스는 `via` 메서드와 알림을 특정 채널에 맞는 메세지로 변환하는 `toMail` 또는 `toDatabase` 와 같은 몇가지 메세지를 작성하는 메서드를 가지고 있습니다.

<a name="sending-notifications"></a>
## Sending Notifications
## 알림 발송하기

<a name="using-the-notifiable-trait"></a>
### Using The Notifiable Trait
### Notifiable 트레이트-trait 사용하기

Notifications may be sent in two ways: using the `notify` method of the `Notifiable` trait or using the `Notification` [facade](/docs/{{version}}/facades). The `Notifiable` trait is included on your application's `App\Models\User` model by default:

알림은 두가지 방법으로 보낼 수 있습니다. `Notifiable` 트레이트-trait의 `notify` 메소드를 이용하거나 `Notification` [파사드](/docs/{{version}}/facades) 를 이용합니다. `Notifiable` 트레이트-trait는 어플리케이션에 `App\Models\User` 모델을 기본적으로 포함하고 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;

    class User extends Authenticatable
    {
        use Notifiable;
    }

The `notify` method that is provided by this trait expects to receive a notification instance:

이 트레이트-trait가 제공하는 `notify` 메소드는 Notification 인스턴스를 받아야 합니다.

    use App\Notifications\InvoicePaid;

    $user->notify(new InvoicePaid($invoice));

> **Note**  
> Remember, you may use the `Notifiable` trait on any of your models. You are not limited to only including it on your `User` model.

> **Note**  
> 기억하세요. 당신은 당신의 어떤 모델에도 `Notifiable` 트레이트-trait를 사용할 수 있습니다. 오직 `User` 모델에만 한정되지 않습니다.

<a name="using-the-notification-facade"></a>
### Using The Notification Facade
### Notification 파사드 사용하기

Alternatively, you may send notifications via the `Notification` [facade](/docs/{{version}}/facades). This approach is useful when you need to send a notification to multiple notifiable entities such as a collection of users. To send notifications using the facade, pass all of the notifiable entities and the notification instance to the `send` method:

다른 대안으로 당신은 `Notification` [파사드](/docs/{{version}}/facades)를 이용해 알림을 보낼 수 있습니다. 이 방식은 유저 집합과 같은 다수의 단체에 알림을 보낼때 필요합니다. 파사드를 이용해 알림을 보내기 위해서는 모든 알림 수신자와 Notification 인스턴스를 `send` 메소드에 넘겨줘야 합니다.

    use Illuminate\Support\Facades\Notification;

    Notification::send($users, new InvoicePaid($invoice));

You can also send notifications immediately using the `sendNow` method. This method will send the notification immediately even if the notification implements the `ShouldQueue` interface:

`sendNow` 메소드를 사용하여 즉시 알림을 보낼 수도 있습니다. 이 메소드는 알림이 `ShouldQueue` 인터페이스를 구현하더라도 즉시 알림을 보냅니다.

    Notification::sendNow($developers, new DeploymentCompleted($deployment));

<a name="specifying-delivery-channels"></a>
### Specifying Delivery Channels
### 전달할 채널 지정하기

Every notification class has a `via` method that determines on which channels the notification will be delivered. Notifications may be sent on the `mail`, `database`, `broadcast`, `vonage`, and `slack` channels.

모든 알림 클래스는 알림이 어떤 채널에 전달될지 결정할 수 있는 `via` 메소드를 가지고 있습니다. 알림은 `mail`, `database`, `broadcast`, `vonage` 그리고 `slack` 채널을 통해서 보낼 수 있습니다.

> **Note**  
> If you would like to use other delivery channels such as Telegram or Pusher, check out the community driven [Laravel Notification Channels website](http://laravel-notification-channels.com).

> **Note**  
> 텔레그램이나 Pusher 와 같은 다른 채널을 사용하고자 한다면, [라라벨 알림 채널 웹사이트](http://laravel-notification-channels.com)와 같은 커뮤니티 드라이버를 확인해 보십시오.

The `via` method receives a `$notifiable` instance, which will be an instance of the class to which the notification is being sent. You may use `$notifiable` to determine which channels the notification should be delivered on:

`via` 메소드는 알림을 보내고 있는 클래스의 인스턴스인 `$notifiable` 인스턴스를 전달받습니다. 어떤 채널에 알림이 전달되는지 결정하기 위해서 `$notifiable`을 사용할 수 있습니다.

    /**
     * Get the notification's delivery channels.
     *
     * @return array<int, string>
     */
    public function via(object $notifiable): array
    {
        return $notifiable->prefers_sms ? ['vonage'] : ['mail', 'database'];
    }

<a name="queueing-notifications"></a>
### Queueing Notifications
### Queue-큐를 통한 Notifications 사용

> **Warning**  
> Before queueing notifications you should configure your queue and [start a worker](/docs/{{version}}/queues).

> **Warning**  
> 알림을 보내는데 Queue-큐를 사용하기 전에 Queue-큐 설정을 완료하고 [Queue-큐 워커를 구동](/docs/{{version}}/queues)해야 합니다.

Sending notifications can take time, especially if the channel needs to make an external API call to deliver the notification. To speed up your application's response time, let your notification be queued by adding the `ShouldQueue` interface and `Queueable` trait to your class. The interface and trait are already imported for all notifications generated using the `make:notification` command, so you may immediately add them to your notification class:

특히 채널이 알림을 전달하기 위해 외부 API를 호출해야 하는 경우 알림을 보내는 데 시간이 걸릴 수 있습니다. 애플리케이션의 응답 시간을 단축하려면 `ShouldQueue` 인터페이스와 `Queueable` 특성을 클래스에 추가하여 알림을 대기열에 추가하세요. 인터페이스와 특성은 `make:notification` 명령을 사용하여 생성된 모든 알림에 대해 이미 임포트되어 있으므로 즉시 알림 클래스에 추가할 수 있습니다.

    <?php

    namespace App\Notifications;

    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Notifications\Notification;

    class InvoicePaid extends Notification implements ShouldQueue
    {
        use Queueable;

        // ...
    }

Once the `ShouldQueue` interface has been added to your notification, you may send the notification like normal. Laravel will detect the `ShouldQueue` interface on the class and automatically queue the delivery of the notification:

`ShouldQueue` 인터페이스를 여러분의 알림 클래스에 추가한 뒤에는, 보통과 같이 알림을 보낼 수 있습니다. 라라벨은 클래스의 `ShouldQueue` 인터페이스를 감지하고 자동으로 큐를 통해서 알림을 전달합니다.

    $user->notify(new InvoicePaid($invoice));

When queueing notifications, a queued job will be created for each recipient and channel combination. For example, six jobs will be dispatched to the queue if your notification has three recipients and two channels.

알림이 대기열에 등록될 때 대기열에 등록되는 잡은 각 수신인과 채널의 조합으로 생성될 것입니다. 예를 들어 여러분의 알림이 세 명의 수신인과 두 개의 채널을 가지고 있다면 총 6개의 잡이 큐에 디스패치 될 것 입니다.

<a name="delaying-notifications"></a>
#### Delaying Notifications
#### 알림 지연시키기

If you would like to delay the delivery of the notification, you may chain the `delay` method onto your notification instantiation:

알림을 보낼 때 이를 지연해서 보낼 필요가 있다면, 알림 인스턴스에 `delay` 메소드를 체이닝하면 됩니다.

    $delay = now()->addMinutes(10);

    $user->notify((new InvoicePaid($invoice))->delay($delay));

<a name="delaying-notifications-per-channel"></a>
#### Delaying Notifications Per Channel
#### 채널별로 알림 지연시키기

You may pass an array to the `delay` method to specify the delay amount for specific channels:

특정 채널에 대한 지연을 설정하기 위해 배열을 `delay` 메소드에 전달할 수 있습니다.

    $user->notify((new InvoicePaid($invoice))->delay([
        'mail' => now()->addMinutes(5),
        'sms' => now()->addMinutes(10),
    ]));

Alternatively, you may define a `withDelay` method on the notification class itself. The `withDelay` method should return an array of channel names and delay values:

다른 방법으로는 알림 클래스 자체에 `withDelay` 메서드를 정의하는 방법이 있습니다. `withDelay` 메서드는 채널 이름과 지연 값의 배열을 반환해야 합니다.

    /**
     * Determine the notification's delivery delay.
     *
     * @return array<string, \Illuminate\Support\Carbon>
     */
    public function withDelay(object $notifiable): array
    {
        return [
            'mail' => now()->addMinutes(5),
            'sms' => now()->addMinutes(10),
        ];
    }

<a name="customizing-the-notification-queue-connection"></a>
#### Customizing The Notification Queue Connection
#### 알림 대기열 커넥션 커스터마이징

By default, queued notifications will be queued using your application's default queue connection. If you would like to specify a different connection that should be used for a particular notification, you may define a `$connection` property on the notification class:

기본적으로 대기 중인 알림은 애플리케이션의 디폴트 대기열 연결을 사용하여 대기합니다. 특정 알림에 사용해야 하는 다른 연결을 지정하려면 알림 클래스에 `$connection` 속성을 정의할 수 있습니다.

    /**
     * The name of the queue connection to use when queueing the notification.
     *
     * @var string
     */
    public $connection = 'redis';

Or, if you would like to specify a specific queue connection that should be used for each notification channel supported by the notification, you may define a `viaConnections` method on your notification. This method should return an array of channel name / queue connection name pairs:

또는 알림이 지원하는 각 알림 채널에 사용해야 하는 특정 대기열 연결을 지정하려면 알림에 `viaConnections` 메서드를 정의할 수 있습니다. 이 메서드는 채널 이름/대기열 연결 이름 쌍의 배열을 반환해야 합니다.

    /**
     * Determine which connections should be used for each notification channel.
     *
     * @return array<string, string>
     */
    public function viaConnections(): array
    {
        return [
            'mail' => 'redis',
            'database' => 'sync',
        ];
    }

<a name="customizing-notification-channel-queues"></a>
#### Customizing Notification Channel Queues
#### 알림 채널 대기열 커스터마이징

If you would like to specify a specific queue that should be used for each notification channel supported by the notification, you may define a `viaQueues` method on your notification. This method should return an array of channel name / queue name pairs:

알림이 지원하는 각 알림 채널에 사용해야하는 특정 대기열을 지정하려면 알림에 `viaQueues` 메소드를 정의 할 수 있습니다. 이 메서드는 채널 이름 / 큐 이름 쌍의 배열을 반환해야합니다.

    /**
     * Determine which queues should be used for each notification channel.
     *
     * @return array<string, string>
     */
    public function viaQueues(): array
    {
        return [
            'mail' => 'mail-queue',
            'slack' => 'slack-queue',
        ];
    }

<a name="queued-notifications-and-database-transactions"></a>
#### Queued Notifications & Database Transactions
#### 대기 중인 알림 및 데이터베이스 트랜잭션

When queued notifications are dispatched within database transactions, they may be processed by the queue before the database transaction has committed. When this happens, any updates you have made to models or database records during the database transaction may not yet be reflected in the database. In addition, any models or database records created within the transaction may not exist in the database. If your notification depends on these models, unexpected errors can occur when the job that sends the queued notification is processed.

대기열에 있는 알림이 데이터베이스 트랜잭션 내에서 전달되면 데이터베이스 트랜잭션이 커밋되기 전에 대기열에서 처리될 수 있습니다. 이 경우 데이터베이스 트랜잭션 중 모델 또는 데이터베이스 레코드에 대한 업데이트가 아직 데이터베이스에 반영되지 않을 수 있습니다. 또한 트랜잭션 내에서 생성된 모델 또는 데이터베이스 레코드는 데이터베이스에 존재하지 않을 수 있습니다. 알림이 이러한 모델에 의존하는 경우 대기 중인 알림을 보내는 작업이 처리될 때 예기치 않은 오류가 발생할 수 있습니다.

If your queue connection's `after_commit` configuration option is set to `false`, you may still indicate that a particular queued notification should be dispatched after all open database transactions have been committed by calling the `afterCommit` method when sending the notification:

대기열 연결의 `after_commit` 설정 옵션이 `false`로 설정되어 있으면 알림을 보낼 때 `afterCommit` 메소드를 호출하여 열려 있는 모든 데이터베이스 트랜잭션이 커밋된 후에 특정 대기열에 있는 알림이 전달되어야 함을 계속 표시할 수 있습니다.

    use App\Notifications\InvoicePaid;

    $user->notify((new InvoicePaid($invoice))->afterCommit());

Alternatively, you may call the `afterCommit` method from your notification's constructor:

다른 방법으로는, 알림 생성자에서 `afterCommit` 메소드를 호출할 수 있습니다.

    <?php

    namespace App\Notifications;

    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Notifications\Notification;

    class InvoicePaid extends Notification implements ShouldQueue
    {
        use Queueable;

        /**
         * Create a new notification instance.
         */
        public function __construct()
        {
            $this->afterCommit();
        }
    }

> **Note**  
> To learn more about working around these issues, please review the documentation regarding [queued jobs and database transactions](/docs/{{version}}/queues#jobs-and-database-transactions).

> **Note**  
> 이러한 문제를 해결하는 방법에 대해 자세히 알아보려면 [대기 중인 작업 및 데이터베이스 트랜잭션](/docs/{{version}}/queues#jobs-and-database-transactions) 문서를 검토하세요.


<a name="determining-if-the-queued-notification-should-be-sent"></a>
#### Determining If A Queued Notification Should Be Sent
#### 대기 중인 알림을 보내야 하는지 여부 결정

After a queued notification has been dispatched for the queue for background processing, it will typically be accepted by a queue worker and sent to its intended recipient.

대기 중인 알림이 백그라운드 처리를 위해 대기열에 등록된 후 일반적으로 대기열 작업자(Queue worker)가 이를 수락하고 의도한 수신자에게 보냅니다.

However, if you would like to make the final determination on whether the queued notification should be sent after it is being processed by a queue worker, you may define a `shouldSend` method on the notification class. If this method returns `false`, the notification will not be sent:

그러나 대기열 작업자가 처리한 후 대기열에 있는 알림을 보낼지 여부를 최종 결정하려면 알림 클래스에 `shouldSend` 메소드를 정의할 수 있습니다. 이 메소드가 `false`를 반환하면 알림이 전송되지 않습니다.

    /**
     * Determine if the notification should be sent.
     */
    public function shouldSend(object $notifiable, string $channel): bool
    {
        return $this->invoice->isPaid();
    }

<a name="on-demand-notifications"></a>
### On-Demand Notifications
### 필요시 대상을 지정한 Notifications

Sometimes you may need to send a notification to someone who is not stored as a "user" of your application. Using the `Notification` facade's `route` method, you may specify ad-hoc notification routing information before sending the notification:

때로는 애플리케이션의 "User"모델로 저장되지 않은 사람에게 알림을 보내야 할 수도 있습니다. `Notification` 파사드의 `route` 메소드를 사용하여 알림을 보내기 전에 임시 알림 라우팅 정보를 지정할 수 있습니다.

    use Illuminate\Broadcasting\Channel;
    use Illuminate\Support\Facades\Notification;

    Notification::route('mail', 'taylor@example.com')
                ->route('vonage', '5555555555')
                ->route('slack', 'https://hooks.slack.com/services/...')
                ->route('broadcast', [new Channel('channel-name')])
                ->notify(new InvoicePaid($invoice));

If you would like to provide the recipient's name when sending an on-demand notification to the `mail` route, you may provide an array that contains the email address as the key and the name as the value of the first element in the array:

필요에 따라 임시적인 알림을 `mail` 경로로 보낼 때 수신자의 이름을 제공하려면 이메일 주소를 키로 포함하고 이름을 배열의 첫 번째 요소 값으로 포함하는 배열을 제공해야 합니다.

    Notification::route('mail', [
        'barrett@example.com' => 'Barrett Blair',
    ])->notify(new InvoicePaid($invoice));

<a name="mail-notifications"></a>
## Mail Notifications
## 이메일을 통한 알림

<a name="formatting-mail-messages"></a>
### Formatting Mail Messages
### 알림 메세지 포맷 지정하기

If a notification supports being sent as an email, you should define a `toMail` method on the notification class. This method will receive a `$notifiable` entity and should return an `Illuminate\Notifications\Messages\MailMessage` instance.

알림이 이메일로 전송되는 것을 지원하는 경우 알림 클래스에 `toMail` 메소드를 정의해야 합니다. 이 메소드는 `$notifiable` 구현체를 수신하고 `Illuminate\Notifications\Messages\MailMessage` 인스턴스를 반환해야 합니다.

The `MailMessage` class contains a few simple methods to help you build transactional email messages. Mail messages may contain lines of text as well as a "call to action". Let's take a look at an example `toMail` method:

`MailMessage` 클래스에는 트랜잭션 이메일 메시지를 작성하는 데 도움이 되는 몇 가지 간단한 메소드가 포함되어 있습니다. 메일 메시지에는 "행동 유도문안"과 함께 한 줄의 텍스트가 포함될 수 있습니다. `toMail` 메소드의 예를 살펴보겠습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        $url = url('/invoice/'.$this->invoice->id);

        return (new MailMessage)
                    ->greeting('Hello!')
                    ->line('One of your invoices has been paid!')
                    ->lineIf($this->amount > 0, "Amount paid: {$this->amount}")
                    ->action('View Invoice', $url)
                    ->line('Thank you for using our application!');
    }

> **Note**  
> Note we are using `$this->invoice->id` in our `toMail` method. You may pass any data your notification needs to generate its message into the notification's constructor.

> **Note**  
> `toMail` 메소드 안에서 `$this->invoice->id`를 사용하고 있다는 것을 주의해주십시오. 알림이 메세지를 생성하기 위해서 필요한 그 어떤 데이터라도 알림 클래스의 생성자에 전달할 수 있습니다.

In this example, we register a greeting, a line of text, a call to action, and then another line of text. These methods provided by the `MailMessage` object make it simple and fast to format small transactional emails. The mail channel will then translate the message components into a beautiful, responsive HTML email template with a plain-text counterpart. Here is an example of an email generated by the `mail` channel:

이 예시에서는 인사말, 한 줄의 텍스트, 클릭 유도 및 다른 텍스트 줄을 등록합니다. `MailMessage` 객체에서 제공하는 이러한 메소드를 사용하면 작은 트랜잭션 이메일을 간단하고 빠르게 포맷할 수 있습니다. 그러면 메일 채널은 메시지를 일반 텍스트에서 아름답고 반응이 빠른 HTML 이메일 템플릿으로 변환합니다. 다음은 `mail` 채널에서 생성된 이메일의 예입니다.

<img src="https://laravel.com/img/docs/notification-example-2.png">

> **Note**  
> When sending mail notifications, be sure to set the `name` configuration option in your `config/app.php` configuration file. This value will be used in the header and footer of your mail notification messages.

> **Note**  
> 메일 알림을 보낼 때 `config/app.php` 설정 파일에서 `name` 설정 옵션을 설정해야 합니다. 이 값은 메일 알림 메시지의 머리글과 바닥글에 사용됩니다..

<a name="error-messages"></a>
#### Error Messages
#### 에러 메시지

Some notifications inform users of errors, such as a failed invoice payment. You may indicate that a mail message is regarding an error by calling the `error` method when building your message. When using the `error` method on a mail message, the call to action button will be red instead of black:

일부 알림은 사용자에게 결제 실패와 같은 에러를 알려줍니다. 메시지를 만들 때 `error` 메서드를 호출함으로써 메시지가 에러에 관한 것이라는 것을 나타낼 수 있습니다. 메일 메시지에 `error` 메서드를 사용하면 행위 유도 버튼이 검정색이 아닌 빨간색으로 표시될 것입니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->error()
                    ->subject('Invoice Payment Failed')
                    ->line('...');
    }

<a name="other-mail-notification-formatting-options"></a>
#### Other Mail Notification Formatting Options
#### 기타 메일 알림 포매팅 옵션

Instead of defining the "lines" of text in the notification class, you may use the `view` method to specify a custom template that should be used to render the notification email:

알림 클래스안에 "lines"을 정의하는 대신에, 알림 이메일이 렌더링 되는데 사용할 커스텀 템플릿을 지정할 수 있는 `view` 메소드를 사용할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)->view(
            'emails.name', ['invoice' => $this->invoice]
        );
    }

You may specify a plain-text view for the mail message by passing the view name as the second element of an array that is given to the `view` method:

`view` 메소드에 지정된 배열의 두 번째 요소로 뷰 이름을 전달하여 메일 메시지에 대한 일반 텍스트 뷰를 지정할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)->view(
            ['emails.name.html', 'emails.name.plain'],
            ['invoice' => $this->invoice]
        );
    }

<a name="customizing-the-sender"></a>
### Customizing The Sender
### Sender 커스터마이징

By default, the email's sender / from address is defined in the `config/mail.php` configuration file. However, you may specify the from address for a specific notification using the `from` method:

기본적으로 이메일의 보낸 사람과 주소는 `config/mail.php` 설정 파일에 정의되어 있습니다. 그러나 `from`메소드를 사용하여 특정 알림에 대해 보낸 사람 주소를 지정할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->from('barrett@example.com', 'Barrett Blair')
                    ->line('...');
    }

<a name="customizing-the-recipient"></a>
### Customizing The Recipient
### 수신자 설정하기

When sending notifications via the `mail` channel, the notification system will automatically look for an `email` property on your notifiable entity. You may customize which email address is used to deliver the notification by defining a `routeNotificationForMail` method on the notifiable entity:

`mail` 채널을 통해 알림을 보낼 때 알림 시스템은 대상이 되는 알림 클래스에서 `email` 속성을 자동으로 찾습니다. 알림을 받을 수 있는 클래스에 `routeNotificationForMail` 메소드를 정의하여 알림을 전달하는 데 사용되는 이메일 주소를 사용자 지정할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Notifications\Notification;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * Route notifications for the mail channel.
         *
         * @return  array<string, string>|string
         */
        public function routeNotificationForMail(Notification $notification): array|string
        {
            // Return email address only...
            return $this->email_address;

            // Return email address and name...
            return [$this->email_address => $this->name];
        }
    }

<a name="customizing-the-subject"></a>
### Customizing The Subject
### 제목 설정하기

By default, the email's subject is the class name of the notification formatted to "Title Case". So, if your notification class is named `InvoicePaid`, the email's subject will be `Invoice Paid`. If you would like to specify a different subject for the message, you may call the `subject` method when building your message:

기본적으로 이메일 제목은 "Title Case" 형식의 알림 클래스 이름입니다. 따라서 알림 클래스의 이름이 `InvoicePaid`인 경우 이메일 제목은 `Invoice Paid`가 됩니다. 메시지에 다른 제목을 지정하려면 메시지를 작성할 때 `subject` 메소드를 호출할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->subject('Notification Subject')
                    ->line('...');
    }

<a name="customizing-the-mailer"></a>
### Customizing The Mailer
### 메일러 설정하기

By default, the email notification will be sent using the default mailer defined in the `config/mail.php` configuration file. However, you may specify a different mailer at runtime by calling the `mailer` method when building your message:

기본적으로 이메일 알림은 `config/mail.php` 설정 파일에 정의된 기본 메일러를 사용하여 전송됩니다. 그러나 메시지를 작성할 때 `mailer` 메소드를 호출하여 런타임에 다른 메일러를 지정할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->mailer('postmark')
                    ->line('...');
    }

<a name="customizing-the-templates"></a>
### Customizing The Templates
### 템플릿 설정하기

You can modify the HTML and plain-text template used by mail notifications by publishing the notification package's resources. After running this command, the mail notification templates will be located in the `resources/views/vendor/notifications` directory:

여러분은 알림 패키지의 리소스의 메일 알림에서 사용되는 HTML 과 일반 텍스트를 수정할 수 있습니다. 이 명령어를 사용하고 나면 메일 알림 템플릿이 `resources/views/vendor/notifications` 디렉토리에 저장됩니다.

```shell
php artisan vendor:publish --tag=laravel-notifications
```

<a name="mail-attachments"></a>
### Attachments
### 첨부 파일

To add attachments to an email notification, use the `attach` method while building your message. The `attach` method accepts the absolute path to the file as its first argument:

이메일 알림에 첨부 파일을 추가하려면 메시지를 작성하는 동안 `attach` 메소드을 사용하세요. `attach` 메소드는 파일의 절대 경로를 첫 번째 인수로 받아들입니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->greeting('Hello!')
                    ->attach('/path/to/file');
    }

> **Note**  
> The `attach` method offered by notification mail messages also accepts [attachable objects](/docs/{{version}}/mail#attachable-objects). Please consult the comprehensive [attachable object documentation](/docs/{{version}}/mail#attachable-objects) to learn more.

> **Note**  
> 알림 메일 메시지가 제공하는 `attach` 메서드는 [첨부 가능한 개체](/docs/{{version}}/mail#attachable-objects)도 받습니다. 전체 내용은 [첨부 가능 개체 문서](/docs/{{version}}/mail#attachable-objects)를 참고하세요.

When attaching files to a message, you may also specify the display name and / or MIME type by passing an `array` as the second argument to the `attach` method:

메시지에 파일을 첨부할 때 `attach` 메소드의 두 번째 인수로 `array`를 전달하여 표시 이름, MIME 유형을 지정할 수도 있습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->greeting('Hello!')
                    ->attach('/path/to/file', [
                        'as' => 'name.pdf',
                        'mime' => 'application/pdf',
                    ]);
    }

Unlike attaching files in mailable objects, you may not attach a file directly from a storage disk using `attachFromStorage`. You should rather use the `attach` method with an absolute path to the file on the storage disk. Alternatively, you could return a [mailable](/docs/{{version}}/mail#generating-mailables) from the `toMail` method:

메일 가능 개체에 파일을 첨부하는 것과 달리 `attachFromStorage`를 사용하여 스토리지 디스크에서 직접 파일을 첨부할 수 없습니다. 저장 디스크의 파일에 대한 절대 경로와 함께 `attach` 메소드을 사용해야 합니다. 다른 대안으로, `toMail` 메소드에 [mailable](/docs/{{version}}/mail#generating-mailables)을 리턴할 수 있습니다.

    use App\Mail\InvoicePaid as InvoicePaidMailable;

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): Mailable
    {
        return (new InvoicePaidMailable($this->invoice))
                    ->to($notifiable->email)
                    ->attachFromStorage('/path/to/file');
    }

When necessary, multiple files may be attached to a message using the `attachMany` method:

필요하다면 `attachMany` 메서드를 이용하여 여러 파일을 첨부할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->greeting('Hello!')
                    ->attachMany([
                        '/path/to/forge.svg',
                        '/path/to/vapor.svg' => [
                            'as' => 'Logo.svg',
                            'mime' => 'image/svg+xml',
                        ],
                    ]);
    }

<a name="raw-data-attachments"></a>
#### Raw Data Attachments
#### 로우 데이터 첨부 파일

The `attachData` method may be used to attach a raw string of bytes as an attachment. When calling the `attachData` method, you should provide the filename that should be assigned to the attachment:

`attachData` 메소드는 로우 문자열의 바이트를 첨부 파일로 첨부하는 데 사용할 수 있습니다. `attachData` 메소드를 호출할 때 첨부 파일에 할당해야 하는 파일 이름을 제공해야 합니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->greeting('Hello!')
                    ->attachData($this->pdf, 'name.pdf', [
                        'mime' => 'application/pdf',
                    ]);
    }

<a name="adding-tags-metadata"></a>
### Adding Tags & Metadata
### 태그 & 메타데이터 지정하기

Some third-party email providers such as Mailgun and Postmark support message "tags" and "metadata", which may be used to group and track emails sent by your application. You may add tags and metadata to an email message via the `tag` and `metadata` methods:

메일건과 포스트마크 같은 제삼자 이메일 제공자들은 여러분의 애플리케이션에서 보낸 이메일을 그룹으로 묶거나 추적하는 데 사용되는 "태그"와 메타데이터를 지원합니다. `tag`와 `metadata` 메서드를 이용하여 이메일 메시지에 태그와 메타데이터를 추가할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->greeting('Comment Upvoted!')
                    ->tag('upvote')
                    ->metadata('comment_id', $this->comment->id);
    }

If your application is using the Mailgun driver, you may consult Mailgun's documentation for more information on [tags](https://documentation.mailgun.com/en/latest/user_manual.html#tagging-1) and [metadata](https://documentation.mailgun.com/en/latest/user_manual.html#attaching-data-to-messages). Likewise, the Postmark documentation may also be consulted for more information on their support for [tags](https://postmarkapp.com/blog/tags-support-for-smtp) and [metadata](https://postmarkapp.com/support/article/1125-custom-metadata-faq).

여러분의 애플리케이션이 메일건 드라이버를 사용중이라면 [태그](https://documentation.mailgun.com/en/latest/user_manual.html#tagging-1)와 [메타데이터](https://documentation.mailgun.com/en/latest/user_manual.html#attaching-data-to-messages)에 관한 추가 정보는 메일건의 문서를 참고하세요. 마찬가지로 포스트마크의 문서에서도 [태그](https://postmarkapp.com/blog/tags-support-for-smtp)와 [메타데이터](https://postmarkapp.com/support/article/1125-custom-metadata-faq)에 대한 추가 정보를 얻을 수 있을 겁니다.

If your application is using Amazon SES to send emails, you should use the `metadata` method to attach [SES "tags"](https://docs.aws.amazon.com/ses/latest/APIReference/API_MessageTag.html) to the message.

여러분의 애플리케이션이 이메일을 보내는데 아마존 SES를 사용하고 있다면 [SES "태그"](https://docs.aws.amazon.com/ses/latest/APIReference/API_MessageTag.html)를 메시지에 첨부하기 위해 `metadata` 메서드를 사용해야 합니다.

<a name="customizing-the-symfony-message"></a>
### Customizing The Symfony Message
### 심포니 메시지 커스터마이징하기

The `withSymfonyMessage` method of the `MailMessage` class allows you to register a closure which will be invoked with the Symfony Message instance before sending the message. This gives you an opportunity to deeply customize the message before it is delivered:

`MailMessage` 클래스의 `withSymfonyMessage` 메서드는 메시지를 전송하기 전에 심포지 메시지 인스턴스와 함께 실행되는 클로저를 등록할 수 있게 해줍니다. 이 기능은 메시지가 전달되기 전에 커스터마이즈할 수 있는 기회를 줍니다.

    use Symfony\Component\Mime\Email;

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->withSymfonyMessage(function (Email $message) {
                        $message->getHeaders()->addTextHeader(
                            'Custom-Header', 'Header Value'
                        );
                    });
    }

<a name="using-mailables"></a>
### Using Mailables
### Mailable 사용

If needed, you may return a full [mailable object](/docs/{{version}}/mail) from your notification's `toMail` method. When returning a `Mailable` instead of a `MailMessage`, you will need to specify the message recipient using the mailable object's `to` method:

필요한 경우 알림의 `toMail` 메소드에서 전체 [메일가능한 객체](docs/{{version}}/mail)을 반환할 수 있습니다. `MailMessage` 대신 `Mailable`을 반환할 때 메일 가능 객체의 `to` 메소드를 사용하여 메시지 수신자를 지정해야 합니다.

    use App\Mail\InvoicePaid as InvoicePaidMailable;
    use Illuminate\Mail\Mailable;

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): Mailable
    {
        return (new InvoicePaidMailable($this->invoice))
                    ->to($notifiable->email);
    }

<a name="mailables-and-on-demand-notifications"></a>
#### Mailables & On-Demand Notifications
#### 메일가능한 객체 & 임시적인 알림

If you are sending an [on-demand notification](#on-demand-notifications), the `$notifiable` instance given to the `toMail` method will be an instance of `Illuminate\Notifications\AnonymousNotifiable`, which offers a `routeNotificationFor` method that may be used to retrieve the email address the on-demand notification should be sent to:

[온디멘드 알림](#on-demand-notifications)을 보내는 경우 `toMail` 메소드에 제공된 `$notifiable` 인스턴스는 온디멘드 알림을 보내야 하는 이메일 주소를 검색하는 데 사용할 수 있는 메소드인 `routeNotificationFor`를 제공하는 `Illuminate\Notifications\AnonymousNotifiable`의 인스턴스가 됩니다.

    use App\Mail\InvoicePaid as InvoicePaidMailable;
    use Illuminate\Notifications\AnonymousNotifiable;
    use Illuminate\Mail\Mailable;

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): Mailable
    {
        $address = $notifiable instanceof AnonymousNotifiable
                ? $notifiable->routeNotificationFor('mail')
                : $notifiable->email;

        return (new InvoicePaidMailable($this->invoice))
                    ->to($address);
    }

<a name="previewing-mail-notifications"></a>
### Previewing Mail Notifications
### 메일 알림 미리보기

When designing a mail notification template, it is convenient to quickly preview the rendered mail message in your browser like a typical Blade template. For this reason, Laravel allows you to return any mail message generated by a mail notification directly from a route closure or controller. When a `MailMessage` is returned, it will be rendered and displayed in the browser, allowing you to quickly preview its design without needing to send it to an actual email address:

메일 알림 템플릿을 디자인할 때 일반적인 Blade 템플릿처럼 브라우저에서 렌더링된 메일 메시지를 빠르게 미리 보는 것이 편리합니다. 이러한 이유로 라라벨을 사용하면 메일 알림에 의해 생성된 메일 메시지를 라우트 클로저 또는 컨트롤러에서 직접 반환할 수 있습니다. `MailMessage`가 반환되면 브라우저에 렌더링되고 표시되므로 실제 이메일 주소로 보낼 필요 없이 디자인을 빠르게 미리 볼 수 있습니다.

    use App\Models\Invoice;
    use App\Notifications\InvoicePaid;

    Route::get('/notification', function () {
        $invoice = Invoice::find(1);

        return (new InvoicePaid($invoice))
                    ->toMail($invoice->user);
    });

<a name="markdown-mail-notifications"></a>
## Markdown Mail Notifications
## 마크다운 이메일 알림

Markdown mail notifications allow you to take advantage of the pre-built templates of mail notifications, while giving you more freedom to write longer, customized messages. Since the messages are written in Markdown, Laravel is able to render beautiful, responsive HTML templates for the messages while also automatically generating a plain-text counterpart.

마크다운 메일 알림을 사용하면 사전에 작성된 메일 알림 템플릿을 활용할 수 있으며, 컨텐츠를 더 길게 작성할 수도 있으며, 메세지를 커스터마이징하는데 유용합니다. 마크다운을 통해서 메세지를 작성하게 되면서, 라라벨은 메세지를 보다 원활하게 렌더링 하고, 반응형 HTML 템플릿을 사용하는 동시에 일반 텍스트를 자동으로 생성할 수 있게 되었습니다.

<a name="generating-the-message"></a>
### Generating The Message
### 메세지 클래스 생성하기

To generate a notification with a corresponding Markdown template, you may use the `--markdown` option of the `make:notification` Artisan command:

일치하는 마크다운 템플릿으로 알림 클래스를 생성하려면 `make:notification` 아티즌 명령어에 `--markdown` 옵션을 사용하면 됩니다.

```shell
php artisan make:notification InvoicePaid --markdown=mail.invoice.paid
```

Like all other mail notifications, notifications that use Markdown templates should define a `toMail` method on their notification class. However, instead of using the `line` and `action` methods to construct the notification, use the `markdown` method to specify the name of the Markdown template that should be used. An array of data you wish to make available to the template may be passed as the method's second argument:

다른 모든 메일 알림과 마찬가지로 Markdown 템플릿을 사용하는 알림은 알림 클래스에 `toMail` 메소드를 정의해야 합니다. 그러나 `line` 및 `action` 메소드를 사용하여 알림을 설정하는 대신 `markdown` 메소드를 사용하여 사용해야 하는 Markdown 템플릿의 이름을 지정합니다. 템플릿에서 사용할 수 있도록 하려는 데이터 배열이 메소드의 두 번째 인수로 전달될 수 있습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        $url = url('/invoice/'.$this->invoice->id);

        return (new MailMessage)
                    ->subject('Invoice Paid')
                    ->markdown('mail.invoice.paid', ['url' => $url]);
    }

<a name="writing-the-message"></a>
### Writing The Message
### 메세지 작성하기

Markdown mail notifications use a combination of Blade components and Markdown syntax which allow you to easily construct notifications while leveraging Laravel's pre-crafted notification components:

마크다운 이메일 알림은 블레이드 컴포넌트와 마크다운 문법을 조합하여 라라벨이 사전에 구성해둔 알림 컴포넌트를 활용하면서 손쉽게 알림을 생성할 수 있게 해줍니다.

```blade
<x-mail::message>
# Invoice Paid

Your invoice has been paid!

<x-mail::button :url="$url">
View Invoice
</x-mail::button>

Thanks,<br>
{{ config('app.name') }}
</x-mail::message>
```

<a name="button-component"></a>
#### Button Component
#### 버튼 컴포넌트

The button component renders a centered button link. The component accepts two arguments, a `url` and an optional `color`. Supported colors are `primary`, `green`, and `red`. You may add as many button components to a notification as you wish:

버튼 컴포넌트는 가운데에 있는 버튼 링크를 렌더링합니다. 설정요소는 `url`과 선택적 `color`의 두 인수를 허용합니다. 지원되는 색상은 `primary`, `green`, `red` 입니다. 알림에 원하는 만큼 버튼 컴포넌트를 추가할 수 있습니다.

```blade
<x-mail::button :url="$url" color="green">
View Invoice
</x-mail::button>
```

<a name="panel-component"></a>
#### Panel Component
#### 패널 컴포넌트

The panel component renders the given block of text in a panel that has a slightly different background color than the rest of the notification. This allows you to draw attention to a given block of text:

패널 컴포넌트는 나머지 텍스트와 배경색이 약간 다른 패널에 텍스트 블럭을 렌더링합니다. 이렇게하면 주어진 텍스트 블럭을 보다 강조할 수 있습니다.

```blade
<x-mail::panel>
This is the panel content.
</x-mail::panel>
```

<a name="table-component"></a>
#### Table Component
#### 테이블 컴포넌트

The table component allows you to transform a Markdown table into an HTML table. The component accepts the Markdown table as its content. Table column alignment is supported using the default Markdown table alignment syntax:

테이블 컴포넌트는 마크다운 테이블을 HTML 테이블로 변환합니다. 이 컴포넌트는 마크다운 테이블을 내용으로 전달받습니다. 테이블 컬럼 정렬은 기본적인 마크다운 테이블 정렬 문법을 사용합니다.

```blade
<x-mail::table>
| Laravel       | Table         | Example  |
| ------------- |:-------------:| --------:|
| Col 2 is      | Centered      | $10      |
| Col 3 is      | Right-Aligned | $20      |
</x-mail::table>
```

<a name="customizing-the-components"></a>
### Customizing The Components
### 컴포넌트 커스터마이징

You may export all of the Markdown notification components to your own application for customization. To export the components, use the `vendor:publish` Artisan command to publish the `laravel-mail` asset tag:

애플리케이션에서 사용하는 모든 마크다운 알림 컴포넌트는 커스터마이징이 가능합니다. 먼저 컴포넌트를 내보내기 위해서 `vendor:publish` 아티즌 명령어를 사용하여 `laravel-mail` 애셋 태그를 지정합니다.

```shell
php artisan vendor:publish --tag=laravel-mail
```

This command will publish the Markdown mail components to the `resources/views/vendor/mail` directory. The `mail` directory will contain an `html` and a `text` directory, each containing their respective representations of every available component. You are free to customize these components however you like.

이 명령어는 마크다운 메일 컴포넌트를 `resources/views/vendor/mail` 디렉토리에 퍼블리싱 합니다. `mail` 디렉토리는 `html` 과 `text` 디렉토리를 가지고 있는데, 각각은 사용가능한 컴포넌트의 표현들이 들어 있습니다. 원하시는대로 이 컴포넌트를 커스터마이징 할 수 있습니다.

<a name="customizing-the-css"></a>
#### Customizing The CSS
#### CSS 커스터마이징

After exporting the components, the `resources/views/vendor/mail/html/themes` directory will contain a `default.css` file. You may customize the CSS in this file and your styles will automatically be in-lined within the HTML representations of your Markdown notifications.

컴포넌트를 내보내기(export) 한 이후에, `resources/views/vendor/mail/html/themes` 디렉토리를 보면 `default.css` 파일을 확인할 수 있습니다. 이 파일에서 CSS를 커스터마이징 할 수 있으며, 마크다운 알림의 HTML 표현에서 스타일이 자동으로 적용됩니다.

If you would like to build an entirely new theme for Laravel's Markdown components, you may place a CSS file within the `html/themes` directory. After naming and saving your CSS file, update the `theme` option of the `mail` configuration file to match the name of your new theme.

Laravel의 Markdown 컴포넌트에 대해 완전히 새로운 테마를 구축하려면 `html/themes` 디렉토리에 CSS 파일을 추가 할 수 있습니다. CSS 파일의 이름을 지정하고 저장 한 후, `mail` 컴포넌트의 `theme` 옵션을 새 테마의 이름과 동일하도록 수정하십시오.

To customize the theme for an individual notification, you may call the `theme` method while building the notification's mail message. The `theme` method accepts the name of the theme that should be used when sending the notification:

알림 메일 메시지를 작성하는 동안 개별 알림에 대한 테마를 커스터마이징하기 위해 `theme`메소드를 사용 할 수 있습니다. `theme` 메소드는 알림을 보낼 때 사용해야하는 테마의 이름을 입력받습니다.

    /**
     * Get the mail representation of the notification.
     */
    public function toMail(object $notifiable): MailMessage
    {
        return (new MailMessage)
                    ->theme('invoice')
                    ->subject('Invoice Paid')
                    ->markdown('mail.invoice.paid', ['url' => $url]);
    }

<a name="database-notifications"></a>
## Database Notifications
## 데이터베이스 알림

<a name="database-prerequisites"></a>
### Prerequisites
### 사전 준비사항

The `database` notification channel stores the notification information in a database table. This table will contain information such as the notification type as well as a JSON data structure that describes the notification.

`database` 알림 채널은 알림 정보를 데이터베이스 테이블에 저장합니다. 이 테이블에는 알림 유형 및 알림을 설명하는 JSON 데이터 구조와 같은 정보가 포함됩니다.

You can query the table to display the notifications in your application's user interface. But, before you can do that, you will need to create a database table to hold your notifications. You may use the `notifications:table` command to generate a [migration](/docs/{{version}}/migrations) with the proper table schema:

테이블을 쿼리하여 애플리케이션의 사용자 인터페이스에 알림을 표시할 수 있습니다. 그러나 그렇게 하기 전에 알림을 보관할 데이터베이스 테이블을 만들어야 합니다. `notifications:table` 명령을 사용하여 적절한 테이블 스키마로 [마이그레이션](docs/{{version}}/migrations)을 생성할 수 있습니다.

```shell
php artisan notifications:table

php artisan migrate
```

> **Note**  
> If your notifiable models are using [UUID or ULID primary keys](/docs/{{version}}/eloquent#uuid-and-ulid-keys), you should replace the `morphs` method with [`uuidMorphs`](docs/{{version}}/migrations#column-method-uuidMorphs) or [`ulidMorphs`](/docs/{{version}}/migrations#column-method-ulidMorphs) in the notification table migration.

> **Note**  
> 알림 가능 모델이 [UUID 혹은 ULID primary keys](/docs/{{version}}/eloquent#uuid-and-ulid-keys)를 사용한다면 알림 테이블 마이그레이션에서 `morphs` 메서드 대신 [`uuidMorphs`](docs/{{version}}/migrations#column-method-uuidMorphs) 또는 [`ulidMorphs`](/docs/{{version}}/migrations#column-method-ulidMorphs) 메서드를 사용하십시오.

<a name="formatting-database-notifications"></a>
### Formatting Database Notifications
### 데이터베이스 알림 포맷 지정하기

If a notification supports being stored in a database table, you should define a `toDatabase` or `toArray` method on the notification class. This method will receive a `$notifiable` entity and should return a plain PHP array. The returned array will be encoded as JSON and stored in the `data` column of your `notifications` table. Let's take a look at an example `toArray` method:

알림이 데이터베이스 테이블에 저장하는 것을 지원하는 경우, 알림 클래스에 `toDatabase` 또는 `toArray` 메소드를 정의해야 합니다. 이 메소드는 `$notifiable`를 전달 받고 순수 PHP 배열을 반환해야 합니다. 반환된 배열은 JSON 으로 인코딩 되어 `notification` 테이블의 `data` 컬럼에 저장될 것입니다. `toArray` 메소드에 대한 예제를 살펴보겠습니다.

    /**
     * Get the array representation of the notification.
     *
     * @return array<string, mixed>
     */
    public function toArray(object $notifiable): array
    {
        return [
            'invoice_id' => $this->invoice->id,
            'amount' => $this->invoice->amount,
        ];
    }

<a name="todatabase-vs-toarray"></a>
#### `toDatabase` Vs. `toArray`
#### `toDatabase` Vs `toArray`

The `toArray` method is also used by the `broadcast` channel to determine which data to broadcast to your JavaScript powered frontend. If you would like to have two different array representations for the `database` and `broadcast` channels, you should define a `toDatabase` method instead of a `toArray` method.

`toArray` 메소드는 `broadcast` 채널에서도 JavaScript 기반 프론트엔드로 브로드캐스트할 데이터를 결정하는 데 사용됩니다. `database` 및 `broadcast` 채널에 대해 두 가지 다른 배열 표현을 사용하려면 `toArray` 메소드 대신 `toDatabase` 메소드를 정의해야 합니다.

<a name="accessing-the-notifications"></a>
### Accessing The Notifications
### 알림에 엑세스하기

Once notifications are stored in the database, you need a convenient way to access them from your notifiable entities. The `Illuminate\Notifications\Notifiable` trait, which is included on Laravel's default `App\Models\User` model, includes a `notifications` [Eloquent relationship](/docs/{{version}}/eloquent-relationships) that returns the notifications for the entity. To fetch notifications, you may access this method like any other Eloquent relationship. By default, notifications will be sorted by the `created_at` timestamp with the most recent notifications at the beginning of the collection:

알림이 데이터베이스에 저장되면 알림 인스턴스에서 액세스할 수 있는 편리한 방법이 필요합니다. 라라벨의 기본 `App\Models\User` 모델에 포함된 `Illuminate\Notifications\Notifiable` 트레이트-trait에는 알림을 가져오기 위해 다른 Eloquent 관계[엘로퀀트 연관관계](/docs/{{version}}/eloquent-relationships) 처럼 이 방법에 액세스할 수 있는 모델  `notifications`이 있습니다. 기본적으로 알림은 컬렉션 시작 부분에 가장 최근 알림과 함께 'created_at' 타임스탬프로 정렬됩니다.

    $user = App\Models\User::find(1);

    foreach ($user->notifications as $notification) {
        echo $notification->type;
    }

If you want to retrieve only the "unread" notifications, you may use the `unreadNotifications` relationship. Again, these notifications will be sorted by the `created_at` timestamp with the most recent notifications at the beginning of the collection:

"읽지 않은" 알림만 검색하려면 `unreadNotifications` 관계를 사용할 수 있습니다. 다시 말하지만, 이러한 알림은 컬렉션 시작 부분에 가장 최근 알림과 함께 `created_at` 타임스탬프에 따라 정렬됩니다.

    $user = App\Models\User::find(1);

    foreach ($user->unreadNotifications as $notification) {
        echo $notification->type;
    }

> **Note**  
> To access your notifications from your JavaScript client, you should define a notification controller for your application which returns the notifications for a notifiable entity, such as the current user. You may then make an HTTP request to that controller's URL from your JavaScript client.

> **Note**  
> 자바스크립트 클라이언트에서 알림에 액세스하려면 현재 사용자와 같이 notifiable 관계 모델에 대한 알림을 반환하는 알림 컨트롤러를 애플리케이션에 정의해야 합니다. 그런 다음 JavaScript 클라이언트에서 해당 컨트롤러의 URL로 HTTP 요청을 할 수 있습니다.

<a name="marking-notifications-as-read"></a>
### Marking Notifications As Read
### 알림을 읽음 표시로 전환하기

Typically, you will want to mark a notification as "read" when a user views it. The `Illuminate\Notifications\Notifiable` trait provides a `markAsRead` method, which updates the `read_at` column on the notification's database record:

일반적으로, 사용자가 알림을 확인했을 때 이를 "읽음"으로 표시하기를 원할 것입니다. `Illuminate\Notifications\Notifiable` 트레이트-trait는 알림 데이터베이스 레코드에서 `read_at` 컬럼을 업데이트 하는 `markAsRead` 메소드를 제공합니다.

    $user = App\Models\User::find(1);

    foreach ($user->unreadNotifications as $notification) {
        $notification->markAsRead();
    }

However, instead of looping through each notification, you may use the `markAsRead` method directly on a collection of notifications:

이경우, 각각의 알림을 반복문 안에서 수정하는 대신에, `markAsRead` 메소드를 알림 컬렉션에 바로 사용할 수도 있습니다.

    $user->unreadNotifications->markAsRead();

You may also use a mass-update query to mark all of the notifications as read without retrieving them from the database:

또한, 데이터베이스에서 별도로 조회하지 않고서 바로 알림을 읽음 표시 하는데 mass-update(대량 업데이트) 쿼리를 사용할 수도 있습니다.

    $user = App\Models\User::find(1);

    $user->unreadNotifications()->update(['read_at' => now()]);

You may `delete` the notifications to remove them from the table entirely:

여러분은 테이블 엔티티에서 알림을 삭제하기 위해서 `delete` 메소드를 사용할 수 있습니다.

    $user->notifications()->delete();

<a name="broadcast-notifications"></a>
## Broadcast Notifications
## 알림 브로드 캐스팅

<a name="broadcast-prerequisites"></a>
### Prerequisites
### 사전준비사항

Before broadcasting notifications, you should configure and be familiar with Laravel's [event broadcasting](/docs/{{version}}/broadcasting) services. Event broadcasting provides a way to react to server-side Laravel events from your JavaScript powered frontend.

알림을 브로드캐스팅하기 전에 라라벨의 [이벤트 브로드캐스팅](docs/{{version}}/broadcasting) 서비스를 설정하고 숙지해야 합니다. 이벤트 브로드캐스팅은 JavaScript 기반 프론트엔드에서 서버 측 Laravel 이벤트에 반응하는 방법을 제공합니다.

<a name="formatting-broadcast-notifications"></a>
### Formatting Broadcast Notifications
### 브로드캐스팅 알림 포맷 지정하기

The `broadcast` channel broadcasts notifications using Laravel's [event broadcasting](/docs/{{version}}/broadcasting) services, allowing your JavaScript powered frontend to catch notifications in realtime. If a notification supports broadcasting, you can define a `toBroadcast` method on the notification class. This method will receive a `$notifiable` entity and should return a `BroadcastMessage` instance. If the `toBroadcast` method does not exist, the `toArray` method will be used to gather the data that should be broadcast. The returned data will be encoded as JSON and broadcast to your JavaScript powered frontend. Let's take a look at an example `toBroadcast` method:

`broadcast` 채널은 라라벨의 [이벤트 브로드캐스팅](docs/{{version}}/broadcasting) 서비스를 사용하여 알림을 브로드캐스트하므로 JavaScript 기반 프론트엔드가 실시간으로 알림을 받을 수 있습니다. 알림이 브로드캐스팅을 지원하는 경우 알림 클래스에서 `toBroadcast` 메소드를 정의할 수 있습니다. 이 메소드는 `$notifiable` 모델를 수신하고 `BroadcastMessage` 인스턴스를 반환해야 합니다. `toBroadcast` 메소드가 존재하지 않으면 `toArray` 메소드를 사용하여 브로드캐스트해야 하는 데이터를 수집합니다. 반환된 데이터는 JSON으로 인코딩되어 JavaScript 기반 프런트엔드로 브로드캐스트됩니다. `toBroadcast` 메소드의 예를 살펴보겠습니다.

    use Illuminate\Notifications\Messages\BroadcastMessage;

    /**
     * Get the broadcastable representation of the notification.
     */
    public function toBroadcast(object $notifiable): BroadcastMessage
    {
        return new BroadcastMessage([
            'invoice_id' => $this->invoice->id,
            'amount' => $this->invoice->amount,
        ]);
    }

<a name="broadcast-queue-configuration"></a>
#### Broadcast Queue Configuration
#### 브로드캐스트 Queue-큐 설정하기

All broadcast notifications are queued for broadcasting. If you would like to configure the queue connection or queue name that is used to queue the broadcast operation, you may use the `onConnection` and `onQueue` methods of the `BroadcastMessage`:

모든 브로드캐스트 알림은 큐를 사용합니다. 브로드캐스팅을 수행할 때 사용할 큐 커넥션이나 큐 이름을 지정하려면, `BroadcastMessage` 의 `onConnection` 과 `onQueue` 메소드를 사용하면 됩니다.

    return (new BroadcastMessage($data))
                    ->onConnection('sqs')
                    ->onQueue('broadcasts');

<a name="customizing-the-notification-type"></a>
#### Customizing The Notification Type
#### 알림 유형 사용자 정의

In addition to the data you specify, all broadcast notifications also have a `type` field containing the full class name of the notification. If you would like to customize the notification `type`, you may define a `broadcastType` method on the notification class:

지정한 데이터 외에도 모든 브로드캐스트 알림에는 알림의 전체 클래스 이름이 포함된 `type` 필드가 있습니다. 알림 `type`을 사용자 지정하려면 알림 클래스에 `broadcastType` 메소드를 정의할 수 있습니다.

    use Illuminate\Notifications\Messages\BroadcastMessage;

    /**
     * Get the type of the notification being broadcast.
     */
    public function broadcastType(): string
    {
        return 'broadcast.message';
    }

<a name="listening-for-notifications"></a>
### Listening For Notifications
### 알림 수신하기

Notifications will broadcast on a private channel formatted using a `{notifiable}.{id}` convention. So, if you are sending a notification to an `App\Models\User` instance with an ID of `1`, the notification will be broadcast on the `App.Models.User.1` private channel. When using [Laravel Echo](/docs/{{version}}/broadcasting#client-side-installation), you may easily listen for notifications on a channel using the `notification` method:

알림은 `{notifiable}.{id}` 규칙을 사용하여 형식이 지정된 비공개 채널에서 브로드캐스트됩니다. 따라서 ID가 `1`인 `App\Models\User` 인스턴스에 알림을 보내는 경우 알림은 `App.Models.User.1` 비공개 채널에서 브로드캐스트됩니다. [Laravel Echo](docs/{{version}}/broadcasting#client-side-installation)를 사용할 때 `notification` 메소드를 사용하여 채널의 알림을 쉽게 들을 수 있습니다.

    Echo.private('App.Models.User.' + userId)
        .notification((notification) => {
            console.log(notification.type);
        });

<a name="customizing-the-notification-channel"></a>
#### Customizing The Notification Channel
#### 알림 채널 커스터미이징하기

If you would like to customize which channel that an entity's broadcast notifications are broadcast on, you may define a `receivesBroadcastNotificationsOn` method on the notifiable entity:

모델의 브로드캐스트 알림이 브로드캐스트되는 채널을 지정하려면 알림 대상 모델에 `receivesBroadcastNotificationsOn` 메소드를 정의할 수 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * The channels the user receives notification broadcasts on.
         */
        public function receivesBroadcastNotificationsOn(): string
        {
            return 'users.'.$this->id;
        }
    }

<a name="sms-notifications"></a>
## SMS Notifications
## SMS 알림

<a name="sms-prerequisites"></a>
### Prerequisites
### 사전 준비사항

Sending SMS notifications in Laravel is powered by [Vonage](https://www.vonage.com/) (formerly known as Nexmo). Before you can send notifications via Vonage, you need to install the `laravel/vonage-notification-channel` and `guzzlehttp/guzzle` packages:

라라벨에서 SMS 알림 보내기는 [Vonage](https://www.vonage.com)(이전 Nexmo로 알려짐)에서 제공합니다. Vonage를 통해 알림을 보내기 전에 `laravel/vonage-notification-channel` 및 `guzzlehttp/guzzle` Composer 패키지를 설치해야 합니다.

    composer require laravel/vonage-notification-channel guzzlehttp/guzzle

The package includes a [configuration file](https://github.com/laravel/vonage-notification-channel/blob/3.x/config/vonage.php). However, you are not required to export this configuration file to your own application. You can simply use the `VONAGE_KEY` and `VONAGE_SECRET` environment variables to define your Vonage public and secret keys.

패키지에는 [설정 파일](https://github.com/laravel/vonage-notification-channel/blob/3.x/config/vonage.php)이 포함되어 있습니다. 이 설정 파일을 애플리케이션의 설정 디렉토리로 복사할 필요는 없습니다. `VONAGE_KEY` 및 `VONAGE_SECRET` 환경 변수를 사용하여 Vonage 공개 및 비밀 키를 설정할 수 있습니다.

After defining your keys, you should set a `VONAGE_SMS_FROM` environment variable that defines the phone number that your SMS messages should be sent from by default. You may generate this phone number within the Vonage control panel:

키를 정의한 뒤에는 어떤 SMS 번호로 송신되는지 표시할 발신자 번호를 `VONAGE_SMS_FROM` 환경변수로 설정해야합니다. 이 번호는 Vonage 관리툴에서 생성할 수 있습니다.

    VONAGE_SMS_FROM=15556666666

<a name="formatting-sms-notifications"></a>
### Formatting SMS Notifications
### SMS 알림 포맷 지정하기

If a notification supports being sent as an SMS, you should define a `toVonage` method on the notification class. This method will receive a `$notifiable` entity and should return an `Illuminate\Notifications\Messages\VonageMessage` instance:

알림이 SMS로 전송되는 것을 지원하는 경우 알림 클래스에 `toVonage` 메소드를 정의해야 합니다. 이 메소드는 `notifiable` 모델를 수신하고 `Illuminate\Notifications\Messages\NexmoMessage` 인스턴스를 반환해야 합니다.

    use Illuminate\Notifications\Messages\VonageMessage;

    /**
     * Get the Vonage / SMS representation of the notification.
     */
    public function toVonage(object $notifiable): VonageMessage
    {
        return (new VonageMessage)
                    ->content('Your SMS message content');
    }

<a name="unicode-content"></a>
#### Unicode Content
#### 유니코드 컨텐츠

If your SMS message will contain unicode characters, you should call the `unicode` method when constructing the `VonageMessage` instance:

SMS 메세지에 유니코드 문자가 포함되어 있다면, `VonageMessage` 인스턴스를 생성할 때 `unicode` 메소드를 호출해야 합니다.

    use Illuminate\Notifications\Messages\VonageMessage;

    /**
     * Get the Vonage / SMS representation of the notification.
     */
    public function toVonage(object $notifiable): VonageMessage
    {
        return (new VonageMessage)
                    ->content('Your unicode message')
                    ->unicode();
    }

<a name="customizing-the-from-number"></a>
### Customizing The "From" Number
### "발신자" 번호 지정하기

If you would like to send some notifications from a phone number that is different from the phone number specified by your `VONAGE_SMS_FROM` environment variable, you may call the `from` method on a `VonageMessage` instance:

`VONAGE_SMS_FROM` 환경 변수에서 지정한 발신자 전화번호와 다른 전화번호로 표시되는 SMS 알림을 보내고자 하는 경우, `VonageMessage` 인스턴스의 `from` 메소드를 사용하면 됩니다.

    use Illuminate\Notifications\Messages\VonageMessage;

    /**
     * Get the Vonage / SMS representation of the notification.
     */
    public function toVonage(object $notifiable): VonageMessage
    {
        return (new VonageMessage)
                    ->content('Your SMS message content')
                    ->from('15554443333');
    }

<a name="adding-a-client-reference"></a>
### Adding a Client Reference
### 클라이언트 참조 추가

If you would like to keep track of costs per user, team, or client, you may add a "client reference" to the notification. Vonage will allow you to generate reports using this client reference so that you can better understand a particular customer's SMS usage. The client reference can be any string up to 40 characters:

사용자, 팀 또는 클라이언트당 비용을 추적하려면 알림에 "client reference"를 추가할 수 있습니다. Vonage를 사용하면 이 클라이언트 참조를 사용하여 보고서를 생성할 수 있으므로 특정 고객의 SMS 사용을 더 잘 이해할 수 있습니다. 클라이언트 참조는 최대 40자의 문자열일 수 있습니다.

    use Illuminate\Notifications\Messages\VonageMessage;

    /**
     * Get the Vonage / SMS representation of the notification.
     */
    public function toVonage(object $notifiable): VonageMessage
    {
        return (new VonageMessage)
                    ->clientReference((string) $notifiable->id)
                    ->content('Your SMS message content');
    }

<a name="routing-sms-notifications"></a>
### Routing SMS Notifications
### SMS 알림 라우팅

To route Vonage notifications to the proper phone number, define a `routeNotificationForVonage` method on your notifiable entity:

Vonage 알림을 적절한 전화번호로 라우팅하려면 알림 대상이 되는 모델에 `routeNotificationForVonage` 메소드를 정의합니다.

    <?php

    namespace App\Models;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Notifications\Notification;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * Route notifications for the Vonage channel.
         */
        public function routeNotificationForVonage(Notification $notification): string
        {
            return $this->phone_number;
        }
    }

<a name="slack-notifications"></a>
## Slack Notifications
## 슬랙 알림

<a name="slack-prerequisites"></a>
### Prerequisites
### 사전 준비사항

Before sending Slack notifications, you should install the Slack notification channel via Composer:

Slack을 통해 알림을 보내려면 먼저 Composer를 통해 Slack 알림 채널 패키지를 설치해야 합니다.

```shell
composer require laravel/slack-notification-channel
```

Additionally, you must create a [Slack App](https://api.slack.com/apps?new_app=1) for your Slack workspace.

그리고 슬랙 워크스페이스에 [슬랙 앱](https://api.slack.com/apps?new_app=1)을 생성해야합니다.

If you only need to send notifications to the same Slack workspace that the App is created in, you should ensure that your App has the `chat:write`, `chat:write.public`, and `chat:write.customize` scopes. These scopes can be added from the "OAuth & Permissions" App management tab within Slack.

생성된 앱과 동일한 슬랙 워크스페이스에 알림을 보내려면 생성한 앱이 `chat:write`, `chat:write.public`, `chat:write.customize` 스코프 권한을 가지고 있는지 확인하십시오. 이 설정은 슬랙의 앱관리 영역페이지의 "OAuth & Permissions" 탭에서 추가할 수 있습니다.

Next, copy the App's "Bot User OAuth Token" and place it within a `slack` configuration array in your application's `services.php` configuration file. This token can be found on the "OAuth & Permissions" tab within Slack:

그런다음 앱의 "봇 사용자 OAuth 토큰"을 복사하여 애플리케이션의 `services.php` 설정 파일의 `slack` 배열 안에 넣습니다. 이 토큰은 Slack의 "OAuth & Permissions" 탭에서 찾을 수 있습니다:

    'slack' => [
        'notifications' => [
            'bot_user_oauth_token' => env('SLACK_BOT_USER_OAUTH_TOKEN'),
            'channel' => env('SLACK_BOT_USER_DEFAULT_CHANNEL'),
        ],
    ],

<a name="slack-app-distribution"></a>
#### App Distribution
#### 앱 배포

If your application will be sending notifications to external Slack workspaces that are owned by your application's users, you will need to "distribute" your App via Slack. App distribution can be managed from your App's "Manage Distribution" tab within Slack. Once your App has been distributed, you may use [Socialite](/docs/{{version}}/socialite) to [obtain Slack Bot tokens](/docs/{{version}}/socialite#slack-bot-scopes) on behalf of your application's users.

애플리케이션이 사용자가 소유한 외부 슬랙 워크스페이스에 알림을 보내려고 한다면 슬랙을 통해서 앱을 "배포" 해야합니다. 앱 배포는 슬랙 내부 앱의 "Manage Distribution" 탭에서 관리할 수 있습니다. 앱이 배포되고나면 애플리케이션 사용자를 대신하여 [Socialite](/docs/{{version}}/socialite) 를 사용하여 [슬랙 봇 토큰 획득하기](/docs/{{version}}/socialite#slack-bot-scopes) 작업을 수행할 수 있습니다. 

<a name="formatting-slack-notifications"></a>
### Formatting Slack Notifications
### 슬랙 알림 포맷 지정하기

If a notification supports being sent as a Slack message, you should define a `toSlack` method on the notification class. This method will receive a `$notifiable` entity and should return an `Illuminate\Notifications\Slack\SlackMessage` instance. You can construct rich notifications using [Slack's Block Kit API](https://api.slack.com/block-kit). The following example may be previewed in [Slack's Block Kit builder](https://app.slack.com/block-kit-builder/T01KWS6K23Z#%7B%22blocks%22:%5B%7B%22type%22:%22header%22,%22text%22:%7B%22type%22:%22plain_text%22,%22text%22:%22Invoice%20Paid%22%7D%7D,%7B%22type%22:%22context%22,%22elements%22:%5B%7B%22type%22:%22plain_text%22,%22text%22:%22Customer%20%231234%22%7D%5D%7D,%7B%22type%22:%22section%22,%22text%22:%7B%22type%22:%22plain_text%22,%22text%22:%22An%20invoice%20has%20been%20paid.%22%7D,%22fields%22:%5B%7B%22type%22:%22mrkdwn%22,%22text%22:%22*Invoice%20No:*%5Cn1000%22%7D,%7B%22type%22:%22mrkdwn%22,%22text%22:%22*Invoice%20Recipient:*%5Cntaylor@laravel.com%22%7D%5D%7D,%7B%22type%22:%22divider%22%7D,%7B%22type%22:%22section%22,%22text%22:%7B%22type%22:%22plain_text%22,%22text%22:%22Congratulations!%22%7D%7D%5D%7D):

알림 클래스가 Slack 메시지로 전송되는 것을 지원하려면 알림 클래스에 `toSlack` 메소드를 정의하면 됩니다. 이 메소드는 `$notifiable` 모델을 인자로 받아 `Illuminate\Notifications\Slack\SlackMessage` 인스턴스를 반환해야 합니다. [슬랙 블록 kit API](https://api.slack.com/block-kit)를 사용하여 알림의 형태를 조정하십시오. 아래의 예제는 다음과 같이 보여집니다. 

    use Illuminate\Notifications\Slack\BlockKit\Blocks\ContextBlock;
    use Illuminate\Notifications\Slack\BlockKit\Blocks\SectionBlock;
    use Illuminate\Notifications\Slack\BlockKit\Composites\ConfirmObject;
    use Illuminate\Notifications\Slack\SlackMessage;

    /**
     * Get the Slack representation of the notification.
     */
    public function toSlack(object $notifiable): SlackMessage
    {
        return (new SlackMessage)
                ->text('One of your invoices has been paid!')
                ->headerBlock('Invoice Paid')
                ->contextBlock(function (ContextBlock $block) {
                    $block->text('Customer #1234');
                })
                ->sectionBlock(function (SectionBlock $block) {
                    $block->text('An invoice has been paid.');
                    $block->field("*Invoice No:*\n1000")->markdown();
                    $block->field("*Invoice Recipient:*\ntaylor@laravel.com")->markdown();
                })
                ->dividerBlock()
                ->sectionBlock(function (SectionBlock $block) {
                    $block->text('Congratulations!');
                });
    }

<a name="slack-interactivity"></a>
### Slack Interactivity
### 슬랙 앱의 상호작용 사용하기

Slack's Block Kit notification system provides powerful features to [handle user interaction](https://api.slack.com/interactivity/handling). To utilize these features, your Slack App should have "Interactivity" enabled and a "Request URL" configured that points to a URL served by your application. These settings can be managed from the "Interactivity & Shortcuts" App management tab within Slack.

슬랙의 블록킷 알림 시스템은 슬랙에서 [사용자가 다양한 상호작용을 처리](handle user interaction](https://api.slack.com/interactivity/handling)하는 강력한 기능을 제공합니다. 이 기능을 활용하려면 슬랙 앱에서 "Interactivity"을 활성화 하고 "요청 URL"을 애플리케이션이 제공하는 URL을 가리키도록 설정해야합니다. 이 설정은 슬랙의 개별 앱 관리 기능 안에 있는 "Interactivity & Shortcuts" 메뉴에서 찾을 수 있습니다. 

In the following example, which utilizes the `actionsBlock` method, Slack will send a `POST` request to your "Request URL" with a payload containing the Slack user who clicked the button, the ID of the clicked button, and more. Your application can then determine the action to take based on the payload. You should also [verify the request](https://api.slack.com/authentication/verifying-requests-from-slack) was made by Slack:


액션블록 메서드를 사용하는 다음 예제에서는 버튼을 클릭한 사용자, 클릭한 버튼의 ID 등을 포함하는 페이로드와 함께 등록한 "요청 URL"로 POST 요청을 보냅니다. 그러면 애플리케이션에서 페이로드를 기반으로 수행할 작업을 결정할 수 있습니다. 또한 [요청이 Slack에 의해 이루어졌는지](https://api.slack.com/authentication/verifying-requests-from-slack) 확인해야 합니다.

    use Illuminate\Notifications\Slack\BlockKit\Blocks\ActionsBlock;
    use Illuminate\Notifications\Slack\BlockKit\Blocks\ContextBlock;
    use Illuminate\Notifications\Slack\BlockKit\Blocks\SectionBlock;
    use Illuminate\Notifications\Slack\SlackMessage;

    /**
     * Get the Slack representation of the notification.
     */
    public function toSlack(object $notifiable): SlackMessage
    {
        return (new SlackMessage)
                ->text('One of your invoices has been paid!')
                ->headerBlock('Invoice Paid')
                ->contextBlock(function (ContextBlock $block) {
                    $block->text('Customer #1234');
                })
                ->sectionBlock(function (SectionBlock $block) {
                    $block->text('An invoice has been paid.');
                })
                ->actionsBlock(function (ActionsBlock $block) {
                     // ID defaults to "button_acknowledge_invoice"...
                    $block->button('Acknowledge Invoice')->primary();

                    // Manually configure the ID...
                    $block->button('Deny')->danger()->id('deny_invoice');
                });
    }

<a name="slack-confirmation-modals"></a>
#### Confirmation Modals
#### 확인 모달창

If you would like users to be required to confirm an action before it is performed, you may invoke the `confirm` method when defining your button. The `confirm` method accepts a message and a closure which receives a `ConfirmObject` instance:

사용자가 실제 액션을 수행하기 전에 확인하는 과정을 거치도록 하려면 버튼을 정의할 때 `confirm` 메서드를 호출하면 됩니다. `confirm` 메서드는 화면에 표시할 메세지와 `ConfirmObject` 인스턴스를 인자로 받는 클로저를 인자로 전달받습니다.  

    use Illuminate\Notifications\Slack\BlockKit\Blocks\ActionsBlock;
    use Illuminate\Notifications\Slack\BlockKit\Blocks\ContextBlock;
    use Illuminate\Notifications\Slack\BlockKit\Blocks\SectionBlock;
    use Illuminate\Notifications\Slack\BlockKit\Composites\ConfirmObject;
    use Illuminate\Notifications\Slack\SlackMessage;

    /**
     * Get the Slack representation of the notification.
     */
    public function toSlack(object $notifiable): SlackMessage
    {
        return (new SlackMessage)
                ->text('One of your invoices has been paid!')
                ->headerBlock('Invoice Paid')
                ->contextBlock(function (ContextBlock $block) {
                    $block->text('Customer #1234');
                })
                ->sectionBlock(function (SectionBlock $block) {
                    $block->text('An invoice has been paid.');
                })
                ->actionsBlock(function (ActionsBlock $block) {
                    $block->button('Acknowledge Invoice')
                        ->primary()
                        ->confirm(
                            'Acknowledge the payment and send a thank you email?',
                            function (ConfirmObject $dialog) {
                                $dialog->confirm('Yes');
                                $dialog->deny('No');
                            }
                        );
                });
    }

<a name="inspecting-slack-blocks"></a>
#### Inspecting Slack Blocks
#### 작성한 슬랙 블록 검사하기

If you would like to quickly inspect the blocks you've been building, you can invoke the `dd` method on the `SlackMessage` instance. The `dd` method will generate and dump a URL to Slack's [Block Kit Builder](https://app.slack.com/block-kit-builder/), which displays a preview of the payload and notification in your browser. You may pass `true` to the `dd` method to dump the raw payload:

작성한 블록이 동작하는지 빠르게 확인하는 방법은 `SlackMessage` 인스턴스의 `dd` 메서드를 호출하는 것입니다. 그러면 `dd` 메서드가 슬랙의 [블록 킷 빌더](https://app.slack.com/block-kit-builder/)에 대한 URL을 생성하고 내용을 덤프하여 브라우저에서 페이로드와 알림의 미리보기를 표시합니다. 만약 페이로드를 날것 그대로(raw) 확인하려면 `dd` 헬퍼에 인자로 `true`를 전달하면 됩니다.  

    return (new SlackMessage)
            ->text('One of your invoices has been paid!')
            ->headerBlock('Invoice Paid')
            ->dd();

<a name="routing-slack-notifications"></a>
### Routing Slack Notifications
### 슬랙 알림 라우팅(수신자설정)

To direct Slack notifications to the appropriate Slack team and channel, define a `routeNotificationForSlack` method on your notifiable model. This method can return one of three values:

정확한 팀 그리고 채널로 알림을 전달하려면, 알림 모델에 `routeNotificationForSlack` 메서드를 정의하면 됩니다. 이 메서드는 다음의 세 가지 값 중 하나를 반환하면 됩니다.  

- `null` - which defers routing to the channel configured in the notification itself. You may use the `to` method when building your `SlackMessage` to configure the channel within the notification.
- A string specifying the Slack channel to send the notification to, e.g. `#support-channel`.
- A `SlackRoute` instance, which allows you to specify an OAuth token and channel name, e.g. `SlackRoute::make($this->slack_channel, $this->slack_token)`. This method should be used to send notifications to external workspaces.

- `null` - 알림 자체에 설정된 채널로 라우팅을 처리합니다. `SlackMessage`를 빌드할 때 `to` 메서드를 사용하여 알림 내에서 채널을 직접 지정할 수도 있습니다.
- 알림을 보내려는 채널을 지정하는 문자열(예: `#support-channel`).
- OAuth 토큰 및 채널 이름을 지정할 수 있는 `SlackRoute` 인스턴스(예: `SlackRoute::make($this->slack_channel, $this->slack_token)`). 이 메서드는 외부 워크스페이스로 알림을 보내는 데 사용해야 합니다.

For instance, returning `#support-channel` from the `routeNotificationForSlack` method will send the notification to the `#support-channel` channel in the workspace associated with the Bot User OAuth token located in your application's `services.php` configuration file:

예를 들어 `routeNotificationForSlack` 메서드에서 `#support-channel`을 반환하면 애플리케이션의 `services.php` 설정 파일에 있는 봇 사용자 OAuth 토큰과 연결된 워크스페이스의 `#support-channel` 채널로 알림이 전송됩니다.
 
    <?php

    namespace App\Models;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Notifications\Notification;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * Route notifications for the Slack channel.
         */
        public function routeNotificationForSlack(Notification $notification): mixed
        {
            return '#support-channel';
        }
    }

<a name="notifying-external-slack-workspaces"></a>
### Notifying External Slack Workspaces
### 외부 슬랙 워크스페이스에 알리기

> **Note**
> Before sending notifications to external Slack workspaces, your Slack App must be [distributed](#slack-app-distribution).

> **Note**
> 알림을 외부 슬랙 워크스페이스로 보내기 전에 반드시 [앱 배포-distributed](#slack-app-distribution)가 되어 있어야 합니다.

Of course, you will often want to send notifications to the Slack workspaces owned by your application's users. To do so, you will first need to obtain a Slack OAuth token for the user. Thankfully, [Laravel Socialite](/docs/{{version}}/socialite) includes a Slack driver that will allow you to easily authenticate your application's users with Slack and [obtain a bot token](/docs/{{version}}/socialite#slack-bot-scopes).

대부분은 애플리케이션 사용자가 소유한 슬랙 워크스페이스로 알림을 보내는 경우가 많습니다. 외부 슬랙 워크스페이스로 알림을 보내려면 Slack OAuth 토큰을 가져와야 합니다. 고맙게도 [라라벨 소셜라이트](/docs/{{version}}/socialite)에는 Slack 애플리케이션 사용자를 쉽게 인증하고 [봇 토큰을 획득](/docs/{{version}}/socialite#slack-bot-scopes)할 수 있는 슬랙 드라이버가 포함되어 있습니다. 

Once you have obtained the bot token and stored it within your application's database, you may utilize the `SlackRoute::make` method to route a notification to the user's workspace. In addition, your application will likely need to offer an opportunity for the user to specify which channel notifications should be sent to:

봇 토큰을 획득하고 이를 애플리케이션 데이터베이스에 저장한 뒤에, `SlackRoute::make` 메서드를 사용하여 알림을 사용자의 워크스페이스로 보낼 수 있습니다. 추가적으로 앱의 사용자가 어떤 채널로 알림을 보낼지 지정할 수 있는 방법을 제공해야합니다. 

    <?php

    namespace App\Models;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;
    use Illuminate\Notifications\Notification;
    use Illuminate\Notifications\Slack\SlackRoute;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * Route notifications for the Slack channel.
         */
        public function routeNotificationForSlack(Notification $notification): mixed
        {
            return SlackRoute::make($this->slack_channel, $this->slack_token);
        }
    }

<a name="localizing-notifications"></a>
## Localizing Notifications
## 알림의 현지화

Laravel allows you to send notifications in a locale other than the HTTP request's current locale, and will even remember this locale if the notification is queued.

라라벨을 사용하면 HTTP 요청의 현재 로케일이 아닌 다른 로케일로 알림을 보낼 수 있으며 알림이 대기 중인 경우에도 이 로케일을 기억합니다.

To accomplish this, the `Illuminate\Notifications\Notification` class offers a `locale` method to set the desired language. The application will change into this locale when the notification is being evaluated and then revert back to the previous locale when evaluation is complete:

이를 위해 `Illuminate\Notifications\Notification` 클래스는 원하는 언어를 설정하는 `locale` 메소드를 제공합니다. 알림이 평가될 때 애플리케이션이 이 로케일로 변경되고 평가가 완료되면 이전 로케일로 되돌아갑니다.

    $user->notify((new InvoicePaid($invoice))->locale('es'));

Localization of multiple notifiable entries may also be achieved via the `Notification` facade:

`Notification` 파사드를 통해 여러 알림 가능한 항목을 현지화할 수도 있습니다.

    Notification::locale('es')->send(
        $users, new InvoicePaid($invoice)
    );

<a name="user-preferred-locales"></a>
### User Preferred Locales
### 사용자 선호 언어

Sometimes, applications store each user's preferred locale. By implementing the `HasLocalePreference` contract on your notifiable model, you may instruct Laravel to use this stored locale when sending a notification:

때로는 애플리케이션의 각 사용자 마다 기본 언어설정이 저장됩니다. 하나 이상의 모델에 `HasLocalePreference` contract을 구현하면 Laravel이 알림을 보낼 때 이 저장된 언어를 사용하게 할 수 있습니다.

    use Illuminate\Contracts\Translation\HasLocalePreference;

    class User extends Model implements HasLocalePreference
    {
        /**
         * Get the user's preferred locale.
         */
        public function preferredLocale(): string
        {
            return $this->locale;
        }
    }

Once you have implemented the interface, Laravel will automatically use the preferred locale when sending notifications and mailables to the model. Therefore, there is no need to call the `locale` method when using this interface:

일단 인터페이스를 구현하면 라라벨은 알림 및 모델을 모델로 전송할 때 자동으로 기본 설정 언어를 사용합니다. 따라서 이 인터페이스를 사용할 때는 `locale` 메소드를 호출 할 필요가 없습니다.

    $user->notify(new InvoicePaid($invoice));

<a name="testing"></a>
## Testing
## 테스팅

You may use the `Notification` facade's `fake` method to prevent notifications from being sent. Typically, sending notifications is unrelated to the code you are actually testing. Most likely, it is sufficient to simply assert that Laravel was instructed to send a given notification.

알림이 전송되지 않도록 `Notification` 파사드의 `fake` 메소드를 사용할 수 있습니다. 일반적으로 알림 전송은 실제로 테스트 중인 코드와 관련이 없습니다. 대부분의 경우 라라벨이 주어진 알림을 보내도록 지시했다는 것만 검증하면 충분합니다.

After calling the `Notification` facade's `fake` method, you may then assert that notifications were instructed to be sent to users and even inspect the data the notifications received:

`Notification` 파사드의 `fake` 메서드를 호출한 후 사용자에게 알림을 전송하도록 지시했는지 검증하고 알림이 수신한 데이터를 검사할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Notifications\OrderShipped;
    use Illuminate\Support\Facades\Notification;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_orders_can_be_shipped(): void
        {
            Notification::fake();

            // Perform order shipping...

            // Assert that no notifications were sent...
            Notification::assertNothingSent();

            // Assert a notification was sent to the given users...
            Notification::assertSentTo(
                [$user], OrderShipped::class
            );

            // Assert a notification was not sent...
            Notification::assertNotSentTo(
                [$user], AnotherNotification::class
            );

            // Assert that a given number of notifications were sent...
            Notification::assertCount(3);
        }
    }

You may pass a closure to the `assertSentTo` or `assertNotSentTo` methods in order to assert that a notification was sent that passes a given "truth test". If at least one notification was sent that passes the given truth test then the assertion will be successful:

주어진 "실제 테스트"를 통과하는 알림이 전송되었음을 확인하기 위해 `assertSentTo` 또는 `assertNotSentTo` 메서드에 클로저를 전달할 수 있습니다. 주어진 실제 테스트를 통과하는 알림이 하나 이상 전송된 경우 검증이 성공합니다.

    Notification::assertSentTo(
        $user,
        function (OrderShipped $notification, array $channels) use ($order) {
            return $notification->order->id === $order->id;
        }
    );

<a name="on-demand-notifications"></a>
#### On-Demand Notifications
#### 대상을 지정한 Notifications

If the code you are testing sends [on-demand notifications](#on-demand-notifications), you can test that the on-demand notification was sent via the `assertSentOnDemand` method:

테스트 중인 코드가 [대상을 지정한 notifications](#on-demand-notifications)를 보내는 경우 `assertSentOnDemand` 메서드를 통해 대상을 지정한 알림이 전송되었는지 테스트할 수 있습니다.

    Notification::assertSentOnDemand(OrderShipped::class);

By passing a closure as the second argument to the `assertSentOnDemand` method, you may determine if an on-demand notification was sent to the correct "route" address:

클로저를 `assertSentOnDemand` 메서드의 두 번째 인수로 전달하면 대상을 지정한 알림이 올바른 "경로" 주소로 전송되었는지 확인할 수 있습니다.

    Notification::assertSentOnDemand(
        OrderShipped::class,
        function (OrderShipped $notification, array $channels, object $notifiable) use ($user) {
            return $notifiable->routes['mail'] === $user->email;
        }
    );

<a name="notification-events"></a>
## Notification Events
## 알림 이벤트

<a name="notification-sending-event"></a>
#### Notification Sending Event
#### 알림 전송 이벤트

When a notification is sending, the `Illuminate\Notifications\Events\NotificationSending` [event](/docs/{{version}}/events) is dispatched by the notification system. This contains the "notifiable" entity and the notification instance itself. You may register listeners for this event in your application's `EventServiceProvider`:

알림이 전송되면 알림 시스템에서 `Illuminate\Notifications\Events\NotificationSending` [event](/docs/{{version}}/events)을 전달합니다. 여기에는 "notifiable" 모델과 알림 인스턴스 자체가 포함됩니다. 애플리케이션의 `EventServiceProvider`에서 이 이벤트에 대한 리스너를 등록할 수 있습니다.

    use App\Listeners\CheckNotificationStatus;
    use Illuminate\Notifications\Events\NotificationSending;
    
    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        NotificationSending::class => [
            CheckNotificationStatus::class,
        ],
    ];

The notification will not be sent if an event listener for the `NotificationSending` event returns `false` from its `handle` method:

`NotificationSending` 이벤트에 대한 이벤트 리스너가 `handle` 메소드에서 `false`를 반환하면 알림이 전송되지 않습니다.

    use Illuminate\Notifications\Events\NotificationSending;

    /**
     * Handle the event.
     */
    public function handle(NotificationSending $event): void
    {
        return false;
    }

Within an event listener, you may access the `notifiable`, `notification`, and `channel` properties on the event to learn more about the notification recipient or the notification itself:

이벤트 리스너 내에서 이벤트의 `notifiable`, `notification` 및 `channel` 속성에 액세스하여 알림 수신자 또는 알림 자체에 대해 자세히 알아볼 수 있습니다.

    /**
     * Handle the event.
     */
    public function handle(NotificationSending $event): void
    {
        // $event->channel
        // $event->notifiable
        // $event->notification
    }

<a name="notification-sent-event"></a>
#### Notification Sent Event
#### 알림 전송 이벤트

When a notification is sent, the `Illuminate\Notifications\Events\NotificationSent` [event](/docs/{{version}}/events) is dispatched by the notification system. This contains the "notifiable" entity and the notification instance itself. You may register listeners for this event in your `EventServiceProvider`:

알림이 전송되면 알림 시스템에서 `Illuminate\Notifications\Events\NotificationSent` [event](/docs/{{version}}/events)를 전달합니다. 여기에는 "notifiable" 모델와 알림 인스턴스 자체가 포함됩니다. `EventServiceProvider`에서 이 이벤트에 대한 리스너를 등록할 수 있습니다.

    use App\Listeners\LogNotification;
    use Illuminate\Notifications\Events\NotificationSent;
    
    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        NotificationSent::class => [
            LogNotification::class,
        ],
    ];

> **Note**  
> After registering listeners in your `EventServiceProvider`, use the `event:generate` Artisan command to quickly generate listener classes.

> **Note**  
> `EventServiceProvider` 에서 리스너를 등록하고 난후, `event:generate` 아티즌 명령어를 사용하여 빠르게 리스너 클래스를 생성할 수 있습니다.

Within an event listener, you may access the `notifiable`, `notification`, `channel`, and `response` properties on the event to learn more about the notification recipient or the notification itself:

이벤트 리스너 안에서 알림의 수신자 또는 알림 그 자체에 대해서 보다 자세한 정보를 얻기 위해서 이벤트의 `notifiable`, `notification`, `channel`, 그리고 `response` 속성에 엑세스 할 수 있습니다.

    /**
     * Handle the event.
     */
    public function handle(NotificationSent $event): void
    {
        // $event->channel
        // $event->notifiable
        // $event->notification
        // $event->response
    }

<a name="custom-channels"></a>
## Custom Channels
## 사용자정의(커스텀) 채널

Laravel ships with a handful of notification channels, but you may want to write your own drivers to deliver notifications via other channels. Laravel makes it simple. To get started, define a class that contains a `send` method. The method should receive two arguments: a `$notifiable` and a `$notification`.

라라벨에서는 몇가지 알림 채널을 제공하고 있지만, 다른 채널을 통해서 알림을 전송하기 위해서 고유한 드라이버를 작성하기를 원할 수도 있습니다. 라라벨에서는 이를 손쉽게 할 수 있습니다. 시작하려면 `send` 메소드를 가지고 있는 클래스를 정의합니다. 이 메소드는 `$notifiable` 와 `$notification` 두개의 인자를 전달 받야야 합니다.

Within the `send` method, you may call methods on the notification to retrieve a message object understood by your channel and then send the notification to the `$notifiable` instance however you wish:

`send` 메소드 내에서 알림에 대한 메소드를 호출하여 채널에서 이해하는 메시지 개체를 검색한 다음 원하는 대로 `$notifiable` 인스턴스에 알림을 보낼 수 있습니다.

    <?php

    namespace App\Notifications;

    use Illuminate\Notifications\Notification;

    class VoiceChannel
    {
        /**
         * Send the given notification.
         */
        public function send(object $notifiable, Notification $notification): void
        {
            $message = $notification->toVoice($notifiable);

            // Send notification to the $notifiable instance...
        }
    }

Once your notification channel class has been defined, you may return the class name from the `via` method of any of your notifications. In this example, the `toVoice` method of your notification can return whatever object you choose to represent voice messages. For example, you might define your own `VoiceMessage` class to represent these messages:

알림 채널 클래스가 정의되면 알림의 `via` 메소드에서 클래스 이름을 반환할 수 있습니다. 이 예에서 알림의 `toVoice` 메소드는 음성 메시지를 나타내기 위해 선택한 모든 객체를 반환할 수 있습니다. 예를 들어 다음 메시지를 나타내기 위해 고유한 `VoiceMessage` 클래스를 정의할 수 있습니다.

    <?php

    namespace App\Notifications;

    use App\Notifications\Messages\VoiceMessage;
    use App\Notifications\VoiceChannel;
    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Notifications\Notification;

    class InvoicePaid extends Notification
    {
        use Queueable;

        /**
         * Get the notification channels.
         */
        public function via(object $notifiable): string
        {
            return VoiceChannel::class;
        }

        /**
         * Get the voice representation of the notification.
         */
        public function toVoice(object $notifiable): VoiceMessage
        {
            // ...
        }
    }
