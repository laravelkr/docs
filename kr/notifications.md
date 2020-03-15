# Notifications
# Notifications-알림

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Creating Notifications](#creating-notifications)
- [알림 생성하기](#creating-notifications)
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
    - [Customizing The Templates](#customizing-the-templates)
    - [템플릿 설정하기](#customizing-the-templates)
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
    - [Formatting Shortcode Notifications](#formatting-shortcode-notifications)
    - [Shortcode 알림 포맷 지정하기](#formatting-shortcode-notifications)
    - [Customizing The "From" Number](#customizing-the-from-number)
    - [발신자 번호 수정하기](#customizing-the-from-number)
    - [Routing SMS Notifications](#routing-sms-notifications)
    - [SMS 알림 라우팅](#routing-sms-notifications)
- [Slack Notifications](#slack-notifications)
- [슬랙-Slack 알림](#slack-notifications)
    - [Prerequisites](#slack-prerequisites)
    - [사전준비사항](#slack-prerequisites)
    - [Formatting Slack Notifications](#formatting-slack-notifications)
    - [Slack 알림 포맷 지정하기](#formatting-slack-notifications)
    - [Slack Attachments](#slack-attachments)
    - [슬랙 첨부파일](#slack-attachments)
    - [Routing Slack Notifications](#routing-slack-notifications)
    - [슬랙 알림 라우팅](#routing-slack-notifications)
- [Localizing Notifications](#localizing-notifications)
- [알림의 현지화](#localizing-notifications)
- [Notification Events](#notification-events)
- [알림 이벤트](#notification-events)
- [Custom Channels](#custom-channels)
- [사용자 정의(커스텀)채널](#custom-channels)

<a name="introduction"></a>
## Introduction
## 시작하기

In addition to support for [sending email](/docs/{{version}}/mail), Laravel provides support for sending notifications across a variety of delivery channels, including mail, SMS (via [Nexmo](https://www.nexmo.com/)), and [Slack](https://slack.com). Notifications may also be stored in a database so they may be displayed in your web interface.

[이메일 발송](/docs/{{version}}/mail)기능을 지원하는 것에 더해, 라라벨은 이메일을 포함해 SMS([Nexmo](https://www.nexmo.com/)를 통해서 제공), [슬랙](https://slack.com)등  다양한 드라이버 채널을 통해서 알림을 발송하는 기능을 제공합니다.

Typically, notifications should be short, informational messages that notify users of something that occurred in your application. For example, if you are writing a billing application, you might send an "Invoice Paid" notification to your users via the email and SMS channels.

일반적으로, 알림은 애플리케이션 안에서 발생한 어떤 사항을 사용자에게 전달하기 위한 짧은 정보를 담은 메세지입니다. 예를들어, 결제가 필요한 애플리케이션을 작성중이라면, "결제 완료" 알림을 이메일이나 SMS를 통해서 사용자에게 발송할 수 있습니다.

<a name="creating-notifications"></a>
## Creating Notifications
## 알림 생성하기

In Laravel, each notification is represented by a single class (typically stored in the `app/Notifications` directory). Don't worry if you don't see this directory in your application, it will be created for you when you run the `make:notification` Artisan command:

라라벨에서 각각의 알림은 하나의 클래스로 표현할 수 있습니다. (일반적으로 `app/Notifications` 디렉토리에 저장된) 애플리케이션에서 이 디렉토리를 볼 수 없더라도 걱정하지 마십시오, `make:notification` 아티즌 명령어를 실행할 때 디렉토리가 생성됩니다.

    php artisan make:notification InvoicePaid

This command will place a fresh notification class in your `app/Notifications` directory. Each notification class contains a `via` method and a variable number of message building methods (such as `toMail` or `toDatabase`) that convert the notification to a message optimized for that particular channel.

이 명령어는 새로운 알림 클래스를 `app/Notifications`디렉토리에 생성합니다. 각각의 알림 클래스는 `via` 메소드와 특정 채널에 최적화된 메세지로 변환하는 몇가지 메세지를 작성하는 방법(`toMail` 또는 `toDatabase` 와 같은)을 가지고 있습니다.

<a name="sending-notifications"></a>
## Sending Notifications
## 알림 발송하기

<a name="using-the-notifiable-trait"></a>
### Using The Notifiable Trait
### Notifiable 트레이트 사용하기

Notifications may be sent in two ways: using the `notify` method of the `Notifiable` trait or using the `Notification` [facade](/docs/{{version}}/facades). First, let's explore using the trait:

알림은 두가지 방법으로 발송할 수 있습니다. `Notifiable`트레이트의 `notify` 메소드를 사용하거나, `Notification` [파사드](/docs/{{version}}/facades)를 사용하는 것입니다. 먼저 트레이트를 사용하는 경우를 살펴보겠습니다.

    <?php

    namespace App;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;

    class User extends Authenticatable
    {
        use Notifiable;
    }

This trait is utilized by the default `App\User` model and contains one method that may be used to send notifications: `notify`. The `notify` method expects to receive a notification instance:

이 트레이트-trait는 기본적으로 `App\User` 모델에서 사용되며 알림을 보내는데 사용할 수 있는 `notify` 메소드를 가지고 있습니다. `notify` 메소드는 알림 인스턴스를 전달 받습니다.

    use App\Notifications\InvoicePaid;

    $user->notify(new InvoicePaid($invoice));

> {tip} Remember, you may use the `Illuminate\Notifications\Notifiable` trait on any of your models. You are not limited to only including it on your `User` model.

> {tip} `Illuminate\Notifications\Notifiable` 트레이트는 여러분이 가지고 있는 어떤 모델에서도 사용할 수 있다는 것을 기억하십시오. `User` 모델에서만 사용할 수 있도록 제한되어 있지 않습니다.

<a name="using-the-notification-facade"></a>
### Using The Notification Facade
### Notification 파사드 사용하기

Alternatively, you may send notifications via the `Notification` [facade](/docs/{{version}}/facades). This is useful primarily when you need to send a notification to multiple notifiable entities such as a collection of users. To send notifications using the facade, pass all of the notifiable entities and the notification instance to the `send` method:

대신에, `Notification` [파사드](/docs/{{version}}/facades)를 사용하여 알림을 발송할 수 있습니다. 이것은 주로 사용자 컬렉션과 같은 여러개의 알림 가능한 엔티티들에 대해서 알림을 발송하는데 유용합니다. 파사드를 사용하여 알림을 보내기 위해서는 알림이 가능한 엔트리들과 알림 인스턴스를 `send` 메소드에 전달하면 됩니다.

    Notification::send($users, new InvoicePaid($invoice));

<a name="specifying-delivery-channels"></a>
### Specifying Delivery Channels
### 전달할 채널 지정하기

Every notification class has a `via` method that determines on which channels the notification will be delivered. Notifications may be sent on the `mail`, `database`, `broadcast`, `nexmo`, and `slack` channels.

모든 알림 클래스는 알림이 어떤 채널에 전달될지 결정할 수 있는 `via` 메소드를 가지고 있습니다. 알림은 `mail`, `database`, `broadcast`, `nexmo` 그리고 `slack` 채널을 통해서 보낼 수 있습니다.

> {tip} If you would like to use other delivery channels such as Telegram or Pusher, check out the community driven [Laravel Notification Channels website](http://laravel-notification-channels.com).

> {tip} 텔레그램이나 Pusher 와 같은 다른 채널을 사용하고자 한다면, [라라벨 알림 채널 웹사이트](http://laravel-notification-channels.com)와 같은 커뮤니티 드라이버를 확인해 보십시오.

The `via` method receives a `$notifiable` instance, which will be an instance of the class to which the notification is being sent. You may use `$notifiable` to determine which channels the notification should be delivered on:

`via` 메소드는 알림을 보내고 있는 클래스의 인스턴스인 `$notifiable` 인스턴스를 전달받습니다. 어떤 채널에 알림이 전달되는지 결정하기 위해서 `$notifiable`을 사용할 수 있습니다.

    /**
     * Get the notification's delivery channels.
     *
     * @param  mixed  $notifiable
     * @return array
     */
    public function via($notifiable)
    {
        return $notifiable->prefers_sms ? ['nexmo'] : ['mail', 'database'];
    }

<a name="queueing-notifications"></a>
### Queueing Notifications
### Queue-큐를 통한 Notifications 사용

> {note} Before queueing notifications you should configure your queue and [start a worker](/docs/{{version}}/queues).

> {note} 알림을 보내는데 Queue-큐를 사용하기 전에 Queue-큐 설정을 완료하고 [Queue-큐 워커를 구동](/docs/{{version}}/queues)해야 합니다.

Sending notifications can take time, especially if the channel needs an external API call to deliver the notification. To speed up your application's response time, let your notification be queued by adding the `ShouldQueue` interface and `Queueable` trait to your class. The interface and trait are already imported for all notifications generated using `make:notification`, so you may immediately add them to your notification class:

알림을 보내는데는 시간이 걸리는데, 특히 알림을 발송하기 위해서 채널이 외부 API를 호출하는 경우에 더욱 그러합니다. 애플리케이션의 응답 속도를 높이기 위해서는 `ShouldQueue` 인터페이스와 `Queueable` 트레이트를 여러분의 클래스에 추가하여 알림이 큐를 통해서 발송되도록 할 수 있습니다. `make:notification` 을 통해서 생성한 모든 알림 클래스에는 이 인터페이스와 트레이트를 바로 클래스에 추가할 수 있도록 import 되어 있습니다.

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

If you would like to delay the delivery of the notification, you may chain the `delay` method onto your notification instantiation:

알림을 보낼 때 이를 지연해서 보낼 필요가 있다면, 알림 인스턴스에 `delay` 메소드를 체이닝하면 됩니다.

    $when = now()->addMinutes(10);

    $user->notify((new InvoicePaid($invoice))->delay($when));

<a name="on-demand-notifications"></a>
### On-Demand Notifications
### 필요시 대상을 지정한 Notifications

Sometimes you may need to send a notification to someone who is not stored as a "user" of your application. Using the `Notification::route` method, you may specify ad-hoc notification routing information before sending the notification:

때로는 애플리케이션의 "사용자(User)"가 아닌 특정 누군가에게 알림을 보내고자 할 수도 있습니다. `Notification::route` 메소드를 사용하여, 알림이 전달될 누군가의 정보를 지정할 수 있습니다.

    Notification::route('mail', 'taylor@example.com')
                ->route('nexmo', '5555555555')
                ->route('slack', 'https://hooks.slack.com/services/...')
                ->notify(new InvoicePaid($invoice));

<a name="mail-notifications"></a>
## Mail Notifications
## 이메일을 통한 알림

<a name="formatting-mail-messages"></a>
### Formatting Mail Messages
### 알림 메세지 포맷 지정하기

If a notification supports being sent as an email, you should define a `toMail` method on the notification class. This method will receive a `$notifiable` entity and should return a `Illuminate\Notifications\Messages\MailMessage` instance. Mail messages may contain lines of text as well as a "call to action". Let's take a look at an example `toMail` method:

알림이 이메일을 통해서 발송되는 것을 지원하고 있는 경우, 여러분은 알림 클래스에 `toMail` 메소드를 정의해야 합니다. 이 메소드는 `$notifiable` 엔티티를 전달 받아 `Illuminate\Notifications\Messages\MailMessage` 인스턴스를 반환해야합니다. 메일 메세지는 "call to action"(액션 호출)과 같은 택스트 라인을 포함할 수 있습니다. 다음 `toMail` 메소드의 예제를 살펴보겠습니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
    {
        $url = url('/invoice/'.$this->invoice->id);

        return (new MailMessage)
                    ->greeting('Hello!')
                    ->line('One of your invoices has been paid!')
                    ->action('View Invoice', $url)
                    ->line('Thank you for using our application!');
    }

> {tip} Note we are using `$this->invoice->id` in our `toMail` method. You may pass any data your notification needs to generate its message into the notification's constructor.

> {tip} `toMail` 메소드 안에서 `$this->invoice->id`를 사용하고 있다는 것을 주의해주십시오. 알림이 메세지를 생성하기 위해서 필요한 그 어떤 데이터라도 알림 클래스의 생성자에 전달할 수 있습니다.

In this example, we register a greeting, a line of text, a call to action, and then another line of text. These methods provided by the `MailMessage` object make it simple and fast to format small transactional emails. The mail channel will then translate the message components into a nice, responsive HTML email template with a plain-text counterpart. Here is an example of an email generated by the `mail` channel:

이 예제에서는 인사말, 텍스트 한줄, 액션 호출 그리고 다른 텍스트 몇줄을 등록합니다. `MailMessage` 객체에서 제공되는 이 메소드들은 간단한 이메일을 쉽고 빠르게 포맷팅할 수 있습니다. 메일 채널은 메세지 컴포넌트를 일반 텍스트로 된 멋진 반응형 HTML 이메일 템플릿으로 변환합니다. 다음은 `mail` 채널에 의해서 생성된 이메일의 예제입니다.

<img src="https://laravel.com/img/docs/notification-example.png" width="551" height="596">

> {tip} When sending mail notifications, be sure to set the `name` value in your `config/app.php` configuration file. This value will be used in the header and footer of your mail notification messages.

> {tip} 메일 알림을 발송할 때에는 `config/app.php` 설정 파일에서 `name` 값을 설정해야합니다. 이 값은 여러분의 이메일 알림 메세지의 헤더와 푸터에 사용됩니다.

#### Other Notification Formatting Options
#### 기타 알림 포매팅 옵션

Instead of defining the "lines" of text in the notification class, you may use the `view` method to specify a custom template that should be used to render the notification email:

알림 클래스안에 "lines"을 정의하는 대신에, 알림 이메일이 렌더링 되는데 사용할 커스텀 템플릿을 지정할 수 있는 `view` 메소드를 사용할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
    {
        return (new MailMessage)->view(
            'emails.name', ['invoice' => $this->invoice]
        );
    }

In addition, you may return a [mailable object](/docs/{{version}}/mail) from the `toMail` method:

이에 더해서, `toMail` 메소드에서 [mailable 객체](/docs/{{version}}/mail)를 반환할 수도 있습니다.

    use App\Mail\InvoicePaid as Mailable;

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return Mailable
     */
    public function toMail($notifiable)
    {
        return (new Mailable($this->invoice))->to($this->user->email);
    }

<a name="error-messages"></a>
#### Error Messages
#### 에러 메세지

Some notifications inform users of errors, such as a failed invoice payment. You may indicate that a mail message is regarding an error by calling the `error` method when building your message. When using the `error` method on a mail message, the call to action button will be red instead of blue:

일부 알림은 사용자에게 청구서 지불이 실패한 것과 같은 오류를 알려주게 됩니다. 메일 메세지가 구성될 때 `error` 메소드를 호출하여 메일 메세지가 오류와 관련되었다는 것을 알려줄 수 있습니다. 메일 메세지에서 `error` 메소드를 사용할 때 "call to action" 버튼은 파란색 대신에 빨간색이 될것입니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Message
     */
    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->error()
                    ->subject('Notification Subject')
                    ->line('...');
    }

<a name="customizing-the-sender"></a>
### Customizing The Sender
### Sender 커스터마이징

By default, the email's sender / from address is defined in the `config/mail.php` configuration file. However, you may specify the from address for a specific notification using the `from` method:

기본적으로 이메일의 보낸 사람과 주소는 `config/mail.php` 설정 파일에 정의되어 있습니다. 그러나 `from`메소드를 사용하여 특정 알림에 대해 보낸 사람 주소를 지정할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->from('test@example.com', 'Example')
                    ->line('...');
    }

<a name="customizing-the-recipient"></a>
### Customizing The Recipient
### 수신자 설정하기

When sending notifications via the `mail` channel, the notification system will automatically look for an `email` property on your notifiable entity. You may customize which email address is used to deliver the notification by defining a `routeNotificationForMail` method on the entity:

`email` 채널을 통해서 알림을 발송할때, 알림 시스템은 자동으로 알림 엔티티 클래스의 `email` 속성을 찾습니다. 알림을 전송하는데 사용하는 이메일 주소를 커스터마이징 하려면 엔티티에 `routeNotificationForMail` 메소드를 정의하면 됩니다.

    <?php

    namespace App;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * Route notifications for the mail channel.
         *
         * @param  \Illuminate\Notifications\Notification  $notification
         * @return array|string
         */
        public function routeNotificationForMail($notification)
        {
            // Return email address only...
            return $this->email_address;

            // Return name and email address...
            return [$this->email_address => $this->name];
        }
    }

<a name="customizing-the-subject"></a>
### Customizing The Subject
### 제목 설정하기

By default, the email's subject is the class name of the notification formatted to "title case". So, if your notification class is named `InvoicePaid`, the email's subject will be `Invoice Paid`. If you would like to specify an explicit subject for the message, you may call the `subject` method when building your message:

기본적으로 이메일의 제목은 알림 클래스의 이름을 "title case"에 포맷한 결과입니다. (역자주 : 클래스 이름을 내장 헬퍼 함수 결과인 title case 형태로 구성하였다는 의미입니다) 따라서 여러분의 알림 클래스가 `InvoicePaid` 라면 이메일의 제목은 `Invoice Paid` 가 될 것입니다. 이메일의 제목을 명확하게 지정하고자 한다면, 메세지를 구성할 때 `subject` 메소드를 호출하면 됩니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->subject('Notification Subject')
                    ->line('...');
    }

<a name="customizing-the-templates"></a>
### Customizing The Templates
### 템플릿 설정하기

You can modify the HTML and plain-text template used by mail notifications by publishing the notification package's resources. After running this command, the mail notification templates will be located in the `resources/views/vendor/notifications` directory:

여러분은 알림 패키지의 리소스의 메일 알림에서 사용되는 HTML 과 일반 텍스트를 수정할 수 있습니다. 이 명령어를 사용하고 나면 메일 알림 템플릿이 `resources/views/vendor/notifications` 디렉토리에 저장됩니다.

    php artisan vendor:publish --tag=laravel-notifications

<a name="previewing-mail-notifications"></a>
### Previewing Mail Notifications
### 메일 알림 미리보기

When designing a mail notification template, it is convenient to quickly preview the rendered mail message in your browser like a typical Blade template. For this reason, Laravel allows you to return any mail message generated by a mail notification directly from a route Closure or controller. When a `MailMessage` is returned, it will be rendered and displayed in the browser, allowing you to quickly preview its design without needing to send it to an actual email address:

메일 알림 템플릿을 디자인 할 때, 이것은 일반적인 블레이드 템플릿처럼 브라우저에서 렌더링 된 메일 메시지를 빠르게 미리보기에 편리합니다. 따라서 Laravel은 라우트나 컨트롤러에서 메일 알림을 통해 생성 된 모든 메일 메시지를 직접 반환 할 수 있습니다. `MailMessage`가 반환되면 브라우저에 렌더링되고 표시되므로 실제 이메일 주소로 보낼 필요없이 디자인을 빠르게 미리 볼 수 있습니다.

    Route::get('mail', function () {
        $invoice = App\Invoice::find(1);

        return (new App\Notifications\InvoicePaid($invoice))
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

    php artisan make:notification InvoicePaid --markdown=mail.invoice.paid

Like all other mail notifications, notifications that use Markdown templates should define a `toMail` method on their notification class. However, instead of using the `line` and `action` methods to construct the notification, use the `markdown` method to specify the name of the Markdown template that should be used:

기타 메일 알림과 마찬가지로, 마크다운 템플릿을 사용하는 알림 클래스는 `toMail` 메소드를 정의해야 합니다. 하지만, `line` 과 `action` 메소드를 사용하여 알림을 만드는 대신에, 사용할 마크다운 템플릿 이름을 지정하기 위해서 `markdown` 메소드를 사용합니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
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

    @component('mail::message')
    # Invoice Paid

    Your invoice has been paid!

    @component('mail::button', ['url' => $url])
    View Invoice
    @endcomponent

    Thanks,<br>
    {{ config('app.name') }}
    @endcomponent

#### Button Component
#### 버튼 컴포넌트

The button component renders a centered button link. The component accepts two arguments, a `url` and an optional `color`. Supported colors are `blue`, `green`, and `red`. You may add as many button components to a notification as you wish:

버튼 컴포넌트는 가운데 있는 버튼 링크를 렌더링 합니다. 컴포넌트는 두개의 인자를 전달 받는데, `url` 과 옵셔널하게 `color`를 전달받습니다. 사용가능한 컬러는 `blue`, `green`, 그리고 `red` 입니다. 원하는만큼, 여러개의 버튼 컴포넌트를 알림에 추가할 수 있습니다.

    @component('mail::button', ['url' => $url, 'color' => 'green'])
    View Invoice
    @endcomponent

#### Panel Component
#### 패널 컴포넌트

The panel component renders the given block of text in a panel that has a slightly different background color than the rest of the notification. This allows you to draw attention to a given block of text:

패널 컴포넌트는 나머지 텍스트와 배경색이 약간 다른 패널에 텍스트 블럭을 렌더링합니다. 이렇게하면 주어진 텍스트 블럭을 보다 강조할 수 있습니다.

    @component('mail::panel')
    This is the panel content.
    @endcomponent

#### Table Component
#### 테이블 컴포넌트

The table component allows you to transform a Markdown table into an HTML table. The component accepts the Markdown table as its content. Table column alignment is supported using the default Markdown table alignment syntax:

테이블 컴포넌트는 마크다운 테이블을 HTML 테이블로 변환합니다. 이 컴포넌트는 마크다운 테이블을 내용으로 전달받습니다. 테이블 컬럼 정렬은 기본적인 마크다운 테이블 정렬 문법을 사용합니다.

    @component('mail::table')
    | Laravel       | Table         | Example  |
    | ------------- |:-------------:| --------:|
    | Col 2 is      | Centered      | $10      |
    | Col 3 is      | Right-Aligned | $20      |
    @endcomponent

<a name="customizing-the-components"></a>
### Customizing The Components
### 컴포넌트 커스터마이징

You may export all of the Markdown notification components to your own application for customization. To export the components, use the `vendor:publish` Artisan command to publish the `laravel-mail` asset tag:

애플리케이션에서 사용하는 모든 마크다운 알림 컴포넌트는 커스터마이징이 가능합니다. 먼저 컴포넌트를 내보내기 위해서 `vendor:publish` 아티즌 명령어를 사용하여 `laravel-mail` 애셋 태그를 지정합니다.

    php artisan vendor:publish --tag=laravel-mail

This command will publish the Markdown mail components to the `resources/views/vendor/mail` directory. The `mail` directory will contain an `html` and a `text` directory, each containing their respective representations of every available component. You are free to customize these components however you like.

이 명령어는 마크다운 메일 컴포넌트를 `resources/views/vendor/mail` 디렉토리에 퍼블리싱 합니다. `mail` 디렉토리는 `html` 과 `text` 디렉토리를 가지고 있는데, 각각은 사용가능한 컴포넌트의 표현들이 들어 있습니다. 원하시는대로 이 컴포넌트를 커스터마이징 할 수 있습니다.

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
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
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

The `database` notification channel stores the notification information in a database table. This table will contain information such as the notification type as well as custom JSON data that describes the notification.

`database` 알림 채널은 알림 정보를 데이터베이스 테이블에 저장합니다. 이 테이블은 알림에 대한 정보를 커스텀 JSON 데이터와 같이 알림 타입으로 포함하게 될 것입니다.

You can query the table to display the notifications in your application's user interface. But, before you can do that, you will need to create a database table to hold your notifications. You may use the `notifications:table` command to generate a migration with the proper table schema:

애플리케이션의 사용자 인터페이스에 알림을 표시하기 위해서 테이블에 쿼리를 질의할 수 있습니다. 하지만, 이렇게 하기 전에 알림을 저장할 데이터베이스 테이블을 생성할 필요가 있습니다. `notifications:table` 명령어를 사용하여 적절한 테이블 스키마를 구성하는 마이그레이션 파일을 생성할 수 있습니다.

    php artisan notifications:table

    php artisan migrate

<a name="formatting-database-notifications"></a>
### Formatting Database Notifications
### 데이터베이스 알림 포맷 지정하기

If a notification supports being stored in a database table, you should define a `toDatabase` or `toArray` method on the notification class. This method will receive a `$notifiable` entity and should return a plain PHP array. The returned array will be encoded as JSON and stored in the `data` column of your `notifications` table. Let's take a look at an example `toArray` method:

알림이 데이터베이스 테이블에 저장하는 것을 지원하는 경우, 알림 클래스에 `toDatabase` 또는 `toArray` 메소드를 정의해야 합니다. 이 메소드는 `$notifiable`를 전달 받고 순수 PHP 배열을 반환해야 합니다. 반환된 배열은 JSON 으로 인코딩 되어 `notification` 테이블의 `data` 컬럼에 저장될 것입니다. `toArray` 메소드에 대한 예제를 살펴보겠습니다.

    /**
     * Get the array representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return array
     */
    public function toArray($notifiable)
    {
        return [
            'invoice_id' => $this->invoice->id,
            'amount' => $this->invoice->amount,
        ];
    }

#### `toDatabase` Vs. `toArray`
#### `toDatabase` Vs `toArray`

The `toArray` method is also used by the `broadcast` channel to determine which data to broadcast to your JavaScript client. If you would like to have two different array representations for the `database` and `broadcast` channels, you should define a `toDatabase` method instead of a `toArray` method.

`toArray` 메소드는 `broadcast` 채널에서 자바스크립트 클라이언트에 어떤 데이터를 브로드캐스팅할지 결정하는데도 사용됩니다. 만약 여러분이 `database` 와 `broadcast` 채널에서 두개의 다르게 표현되는 배열을 가지고자 한다면 `toArray`  메소드 대신에 `toDatabase` 메소드를 정의해야 합니다.

<a name="accessing-the-notifications"></a>
### Accessing The Notifications
### 알림에 엑세스하기

Once notifications are stored in the database, you need a convenient way to access them from your notifiable entities. The `Illuminate\Notifications\Notifiable` trait, which is included on Laravel's default `App\User` model, includes a `notifications` Eloquent relationship that returns the notifications for the entity. To fetch notifications, you may access this method like any other Eloquent relationship. By default, notifications will be sorted by the `created_at` timestamp:

데이터베이스에 알림이 저장되고 난 뒤에, 여러분은 알림 엔티티에서 저장된 알림에 편리하게 엑세스 할 수 있는 방법이 필요합니다. 라라벨의 기본 `App\User` 모델에 포함되어 있는 `Illuminate\Notifications\Notifiable` 트레이트는 엔티티의 알림을 반환하는 `notifications` Eloquent 관계를 가지고 있습니다. 알림을 가져오기 위해서 다른 Eloquent 관계와 마찬가지로, 이 메소드에 엑세스 할 수 있습니다. 기본적으로 알림은 `created_at` 타임스탬프를 기준으로 정렬됩니다.

    $user = App\User::find(1);

    foreach ($user->notifications as $notification) {
        echo $notification->type;
    }

If you want to retrieve only the "unread" notifications, you may use the `unreadNotifications` relationship. Again, these notifications will be sorted by the `created_at` timestamp:

"unread"(읽지않은) 알림만 조회하고자 한다면, `unreadNotifications` 메소드를 사용할 수 있습니다. 다시 한번 말하지만, 이 알림들은 `created_at` 타임스탬프에 의해서 정렬됩니다.

    $user = App\User::find(1);

    foreach ($user->unreadNotifications as $notification) {
        echo $notification->type;
    }

> {tip} To access your notifications from your JavaScript client, you should define a notification controller for your application which returns the notifications for a notifiable entity, such as the current user. You may then make an HTTP request to that controller's URI from your JavaScript client.

> {tip} 자바스크립트 클라이언트에서 알림에 엑세스하려면, 애플리케이션에서 현재 사용자와 같은 알림이 가능한 엔티티에 대한 알림을 반환하는 알림 컨트롤러를 정의해야 합니다. 그런 다음 자바스크립트 클라이언트에서 해당 컨트롤러의 URI에 대한 HTTP 요청을 만들 수 있습니다.

<a name="marking-notifications-as-read"></a>
### Marking Notifications As Read
### 알림을 읽음 표시로 전환하기

Typically, you will want to mark a notification as "read" when a user views it. The `Illuminate\Notifications\Notifiable` trait provides a `markAsRead` method, which updates the `read_at` column on the notification's database record:

일반적으로, 사용자가 알림을 확인했을 때 이를 "읽음"으로 표시하기를 원할 것입니다. `Illuminate\Notifications\Notifiable` 트레이트는 알림 데이터베이스 레코드에서 `read_at` 컬럼을 업데이트 하는 `markAsRead` 메소드를 제공합니다.

    $user = App\User::find(1);

    foreach ($user->unreadNotifications as $notification) {
        $notification->markAsRead();
    }

However, instead of looping through each notification, you may use the `markAsRead` method directly on a collection of notifications:

이경우, 각각의 알림을 반복문 안에서 수정하는 대신에, `markAsRead` 메소드를 알림 컬렉션에 바로 사용할 수도 있습니다.

    $user->unreadNotifications->markAsRead();

You may also use a mass-update query to mark all of the notifications as read without retrieving them from the database:

또한, 데이터베이스에서 별도로 조회하지 않고서 바로 알림을 읽음 표시 하는데 mass-update(대량 업데이트) 쿼리를 사용할 수도 있습니다.

    $user = App\User::find(1);

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

Before broadcasting notifications, you should configure and be familiar with Laravel's [event broadcasting](/docs/{{version}}/broadcasting) services. Event broadcasting provides a way to react to server-side fired Laravel events from your JavaScript client.

알림을 브로드캐스팅하기 전에, 여러분은 라라벨의 [이벤트 브로드캐스팅](/docs/{{version}}/broadcasting)에 대해서 이해하고 이를 설정해야 합니다. 이벤트 브로드캐스팅은 자바스크립트 클라이언트에서 서버 사이드에서 발생한 라라벨 이벤트에 반응하는 방법을 제공합니다.

<a name="formatting-broadcast-notifications"></a>
### Formatting Broadcast Notifications
### 브로드캐스팅 알림 포맷 지정하기

The `broadcast` channel broadcasts notifications using Laravel's [event broadcasting](/docs/{{version}}/broadcasting) services, allowing your JavaScript client to catch notifications in realtime. If a notification supports broadcasting, you can define a `toBroadcast` method on the notification class. This method will receive a `$notifiable` entity and should return a `BroadcastMessage` instance. If the `toBroadcast` method does not exist, the `toArray` method will be used to gather the data that should be broadcast. The returned data will be encoded as JSON and broadcast to your JavaScript client. Let's take a look at an example `toBroadcast` method:

`broadcast` 채널은 라라벨의 [이벤트 브로드캐스팅](/docs/{{version}}/broadcasting) 서비스를 사용하여 알림을 브로드캐스팅 하고 여러분의 자바스크립트 클리이언트가 실시간으로 이벤트를 받을 수 있도록 합니다. 만약 알림이 브로드캐스팅을 지원하려면, 알림 클래스에 `toBroadcast` 메소드를 정의 할 수 있습니다. 이 메소드는 `$notifiable` 엔티티를 전달 받아 `BroadcastMessage` 인스턴스를 반환해야 합니다. `toBroadcast` 메소드가 존재하지 않으면, `toArray` 메소드가 브로드 캐스팅 되어야하는 데이터를 가져오는 데 사용될 것입니다. 반환된 데이터는 JSON 으로 인코딩 되어 자바스크립트 클라이언트로 브로드 캐스팅될 것입니다. `toBroadcast` 메소드에 대한 예제를 살펴보겠습니다.

    use Illuminate\Notifications\Messages\BroadcastMessage;

    /**
     * Get the broadcastable representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return BroadcastMessage
     */
    public function toBroadcast($notifiable)
    {
        return new BroadcastMessage([
            'invoice_id' => $this->invoice->id,
            'amount' => $this->invoice->amount,
        ]);
    }

#### Broadcast Queue Configuration
#### 브로드캐스트 Queue-큐 설정하기

All broadcast notifications are queued for broadcasting. If you would like to configure the queue connection or queue name that is used to queue the broadcast operation, you may use the `onConnection` and `onQueue` methods of the `BroadcastMessage`:

모든 브로드캐스트 알림은 큐를 사용합니다. 브로드캐스팅을 수행할 때 사용할 큐 커넥션이나 큐 이름을 지정하려면, `BroadcastMessage` 의 `onConnection` 과 `onQueue` 메소드를 사용하면 됩니다.

    return (new BroadcastMessage($data))
                    ->onConnection('sqs')
                    ->onQueue('broadcasts');

> {tip} In addition to the data you specify, broadcast notifications will also contain a `type` field containing the class name of the notification.

> {tip} 여러분이 지정한 데이터에 더해서, 브로드캐스트 알림은 알림의 클래스 이름을 가지고 있는 `type` 필드를 포함합니다.

<a name="listening-for-notifications"></a>
### Listening For Notifications
### 알림 수신하기

Notifications will broadcast on a private channel formatted using a `{notifiable}.{id}` convention. So, if you are sending a notification to a `App\User` instance with an ID of `1`, the notification will be broadcast on the `App.User.1` private channel. When using [Laravel Echo](/docs/{{version}}/broadcasting), you may easily listen for notifications on a channel using the `notification` helper method:

비공개 채널에 브로드캐스팅 되는 알림은 `{notifiable}.{id}` 컨벤션에 의해서 포맷이 지정됩니다. 따라서 ID 가 `1`인 `App\User` 인스턴스를 알림으로 보내는 경우 알림은 `App.User.1` 비공개 채널에 브로드캐스팅 됩니다. [라라벨 에코](/docs/{{version}}/broadcasting)를 사용하는 경우, 채널에서 `notification` 헬퍼 메소드를 사용하여 알림을 손쉽게 수신할 수 있습니다.

    Echo.private('App.User.' + userId)
        .notification((notification) => {
            console.log(notification.type);
        });

#### Customizing The Notification Channel
#### 알림 채널 커스터미이징하기

If you would like to customize which channels a notifiable entity receives its broadcast notifications on, you may define a `receivesBroadcastNotificationsOn` method on the notifiable entity:

알림 엔티티가 알림을 수신하는 채널을 커스터마이징하고자 한다면, notificable 엔티티에 `receivesBroadcastNotificationsOn` 메소드를 정의하면 됩니다.

    <?php

    namespace App;

    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * The channels the user receives notification broadcasts on.
         *
         * @return string
         */
        public function receivesBroadcastNotificationsOn()
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

Sending SMS notifications in Laravel is powered by [Nexmo](https://www.nexmo.com/). Before you can send notifications via Nexmo, you need to install the `laravel/nexmo-notification-channel` Composer package:

라라벨에서 SMS 알림 전송은 [Nexmo](https://www.nexmo.com/)를 사용합니다. Nexmo를 통해서 알림을 전송할 수 있도록, 먼저 `laravel/nexmo-notification-channel` 컴포저 패키지를 설치해야 합니다.

    composer require laravel/nexmo-notification-channel

This will also install the [`nexmo/laravel`](https://github.com/Nexmo/nexmo-laravel) package. This package includes [its own configuration file](https://github.com/Nexmo/nexmo-laravel/blob/master/config/nexmo.php). You can use the `NEXMO_KEY` and `NEXMO_SECRET` environment variables to set your Nexmo public and secret key.

[`nexmo/laravel`](https://github.com/Nexmo/nexmo-laravel) 패키지도 설치됩니다. 이 패키지에는 [자체 설정 파일](https://github.com/Nexmo/nexmo-laravel/blob/master/config/nexmo.php)이 포함되어 있습니다. `NEXMO_KEY` 및 `NEXMO_SECRET` 환경 변수를 사용하여 Nexmo 공개 및 비밀 키를 설정할 수 있습니다.

Next, you will need to add a configuration option to your `config/services.php` configuration file. You may copy the example configuration below to get started:

다음으로 `config/services.php` 설정 파일에 설정 옵션을 추가해야합니다. 아래 예제 구성을 복사하여 시작할 수 있습니다.

    'nexmo' => [
        'sms_from' => '15556666666',
    ],

The `sms_from` option is the phone number that your SMS messages will be sent from. You should generate a phone number for your application in the Nexmo control panel.

`sms_from` 옵션은 SMS 메세지가 전송되는 전화번호 입니다. Nexmo 설정 패널에서 애플리케이션의 전화번호를 생성해야 합니다.

<a name="formatting-sms-notifications"></a>
### Formatting SMS Notifications
### SMS 알림 포맷 지정하기

If a notification supports being sent as an SMS, you should define a `toNexmo` method on the notification class. This method will receive a `$notifiable` entity and should return a `Illuminate\Notifications\Messages\NexmoMessage` instance:

알림이 SMS로 전송되는것을 지원하려면, 알림 클래스에 `toNexmo` 메소드를 정의해야 합니다. 이 메소드는 `$notifiable` 엔티티를 전달받고 `Illuminate\Notifications\Messages\NexmoMessage` 인스턴스를 반환해야합니다.

    /**
     * Get the Nexmo / SMS representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return NexmoMessage
     */
    public function toNexmo($notifiable)
    {
        return (new NexmoMessage)
                    ->content('Your SMS message content');
    }

<a name="formatting-shortcode-notifications"></a>
### Formatting Shortcode Notifications
### Shortcode 알림 포맷 지정하기

Laravel also supports sending shortcode notifications, which are pre-defined message templates in your Nexmo account. You may specify the type of notification (`alert`, `2fa`, or `marketing`), as well as the custom values that will populate the template:

Laravel은 Nexmo 계정에서 미리 정의 된 메시지 템플릿 인 shortcode 알림 전송도 지원합니다. 템플릿을 채울 사용자 정의 값 뿐만 아니라 알림 유형 (`alert`, `2fa` 또는 `marketing`)을 지정할 수 있습니다.

    /**
     * Get the Nexmo / Shortcode representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return array
     */
    public function toShortcode($notifiable)
    {
        return [
            'type' => 'alert',
            'custom' => [
                'code' => 'ABC123',
            ];
        ];
    }

> {tip} Like [routing SMS Notifications](#routing-sms-notifications), you should implement the `routeNotificationForShortcode` method on your notifiable model.

> {tip} [SMS 알림 라우팅](#routing-sms-notifications)과 같이 알림 가능한 모델에 `routeNotificationForShortcode` 메소드를 구현해야합니다.

#### Unicode Content
#### 유니코드 컨텐츠

If your SMS message will contain unicode characters, you should call the `unicode` method when constructing the `NexmoMessage` instance:

SMS 메세지에 유니코드 문자가 포함되어 있다면, `NexmoMessage` 인스턴스를 생성할 때 `unicode` 메소드를 호출해야 합니다.

    /**
     * Get the Nexmo / SMS representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return NexmoMessage
     */
    public function toNexmo($notifiable)
    {
        return (new NexmoMessage)
                    ->content('Your unicode message')
                    ->unicode();
    }

<a name="customizing-the-from-number"></a>
### Customizing The "From" Number
### "발신자" 번호 지정하기

If you would like to send some notifications from a phone number that is different from the phone number specified in your `config/services.php` file, you may use the `from` method on a `NexmoMessage` instance:

`config/services.php` 파일에서 지정한 발신자 전화번호와 다른 전화번호로 표시되는 SMS 알림을 보내고자 하는 경우, `NexmoMessage` 인스턴스의 `from` 메소드를 사용하면 됩니다.

    /**
     * Get the Nexmo / SMS representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return NexmoMessage
     */
    public function toNexmo($notifiable)
    {
        return (new NexmoMessage)
                    ->content('Your SMS message content')
                    ->from('15554443333');
    }

<a name="routing-sms-notifications"></a>
### Routing SMS Notifications
### SMS 알림 라우팅(수신자 지정)

To route Nexmo notifications to the proper phone number, define a `routeNotificationForNexmo` method on your notifiable entity:

Nexmo 알림을 올바른 전화 번호로 전송하려면 알림 가능한 엔터티에 `routeNotificationForNexmo` 메소드를 정의하십시오.

    <?php

    namespace App;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * Route notifications for the Nexmo channel.
         *
         * @param  \Illuminate\Notifications\Notification  $notification
         * @return string
         */
        public function routeNotificationForNexmo($notification)
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

Before you can send notifications via Slack, you must install the notification channel via Composer:

슬랙을 통해서 알림을 보내기 전에, 컴포저를 통해서 슬랙 알림 채널 패키지를 설치해야 합니다.

    composer require laravel/slack-notification-channel

You will also need to configure an ["Incoming Webhook"](https://api.slack.com/incoming-webhooks) integration for your Slack team. This integration will provide you with a URL you may use when [routing Slack notifications](#routing-slack-notifications).

슬랙 팀에서 ["Incoming Webhook"](https://api.slack.com/incoming-webhooks) intergration을 설정해야합니다. 이 intergration은 [슬랙 알림을 전송](#routing-slack-notifications)할 때 사용하는 하나의 URL을 제공해줍니다.

<a name="formatting-slack-notifications"></a>
### Formatting Slack Notifications
### 슬랙 알림 포맷 지정하기

If a notification supports being sent as a Slack message, you should define a `toSlack` method on the notification class. This method will receive a `$notifiable` entity and should return a `Illuminate\Notifications\Messages\SlackMessage` instance. Slack messages may contain text content as well as an "attachment" that formats additional text or an array of fields. Let's take a look at a basic `toSlack` example:

알림이 슬랙 메세지로 전송되도록 지원하려면, 알림 클래스에 `toSlack` 메소드를 정의해야합니다. 이 메소드는 `$notifiable` 엔티티를 전달받고 `Illuminate\Notifications\Messages\SlackMessage` 인스턴스를 반환해야합니다. 슬랙 메세지는 텍스트 컨텐츠와 동시에 추가적인 텍스트 또는 필드의 배열을 "첨부"로 포함할 수 있습니다. 다음의 기본적인 `toSlack` 메세지를 살펴보겠습니다.

    /**
     * Get the Slack representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return SlackMessage
     */
    public function toSlack($notifiable)
    {
        return (new SlackMessage)
                    ->content('One of your invoices has been paid!');
    }

In this example we are just sending a single line of text to Slack, which will create a message that looks like the following:

이 예제에서는 슬랙에 하나의 텍스트 라인을 전송하였고, 다음과 같은 메세지가 생성됩니다.

<img src="https://laravel.com/img/docs/basic-slack-notification.png">

#### Customizing The Sender & Recipient
#### 발신자 & 수신자 설정하기

You may use the `from` and `to` methods to customize the sender and recipient. The `from` method accepts a username and emoji identifier, while the `to` method accepts a channel or username:

`from` 과 `to` 메소드를 사용하여 발신자와 수신자를 변경할 수 있습니다. `from` 메소드는 사용자 이름과 이모지 식별자를 인자로 받으며, `to` 메소드는 채널 또는 사용자 이름을 전달받습니다.

    /**
     * Get the Slack representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return SlackMessage
     */
    public function toSlack($notifiable)
    {
        return (new SlackMessage)
                    ->from('Ghost', ':ghost:')
                    ->to('#other')
                    ->content('This will be sent to #other');
    }

You may also use an image as your logo instead of an emoji:

이모지 대신에 이미지를 로고로 사용할 수도 있습니다.

    /**
     * Get the Slack representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return SlackMessage
     */
    public function toSlack($notifiable)
    {
        return (new SlackMessage)
                    ->from('Laravel')
                    ->image('https://laravel.com/img/favicon/favicon.ico')
                    ->content('This will display the Laravel logo next to the message');
    }

<a name="slack-attachments"></a>
### Slack Attachments
### 슬랙 첨부파일

You may also add "attachments" to Slack messages. Attachments provide richer formatting options than simple text messages. In this example, we will send an error notification about an exception that occurred in an application, including a link to view more details about the exception:

또한 슬랙 메세지에 "첨부파일"을 추가할 수도 있습니다. 첨부파일은 간단한 텍스트 메세지보다 풍부한 포맷 옵션을 제공합니다. 다음 예제에서, 애플리케이션에서 발생한 예외-exception에 대한 에러 알림에 보다 자세한 정보를 표시하는 링크를 포함하여 전송할 것입니다.

    /**
     * Get the Slack representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return SlackMessage
     */
    public function toSlack($notifiable)
    {
        $url = url('/exceptions/'.$this->exception->id);

        return (new SlackMessage)
                    ->error()
                    ->content('Whoops! Something went wrong.')
                    ->attachment(function ($attachment) use ($url) {
                        $attachment->title('Exception: File Not Found', $url)
                                   ->content('File [background.jpg] was not found.');
                    });
    }

The example above will generate a Slack message that looks like the following:

이 예제는 다음과 같이 보여지는 슬랙 메세지를 생성할 것입니다.

<img src="https://laravel.com/img/docs/basic-slack-attachment.png">

Attachments also allow you to specify an array of data that should be presented to the user. The given data will be presented in a table-style format for easy reading:

첨부파일은 또한 사용자에게 보여져야할 데이터의 배열을 지정할 수도 있습니다. 주어진 데이터는 쉽게 읽을 수 있도록 테이블 스타일의 형식으로 표시됩니다.

    /**
     * Get the Slack representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return SlackMessage
     */
    public function toSlack($notifiable)
    {
        $url = url('/invoices/'.$this->invoice->id);

        return (new SlackMessage)
                    ->success()
                    ->content('One of your invoices has been paid!')
                    ->attachment(function ($attachment) use ($url) {
                        $attachment->title('Invoice 1322', $url)
                                   ->fields([
                                        'Title' => 'Server Expenses',
                                        'Amount' => '$1,234',
                                        'Via' => 'American Express',
                                        'Was Overdue' => ':-1:',
                                    ]);
                    });
    }

The example above will create a Slack message that looks like the following:

이 예제는 다음과 같이 보여지는 슬랙 메세지를 생성할 것입니다.

<img src="https://laravel.com/img/docs/slack-fields-attachment.png">

#### Markdown Attachment Content
#### 마크다운에 첨부파일 표시하기

If some of your attachment fields contain Markdown, you may use the `markdown` method to instruct Slack to parse and display the given attachment fields as Markdown formatted text. The values accepted by this method are: `pretext`, `text`, and / or `fields`. For more information about Slack attachment formatting, check out the [Slack API documentation](https://api.slack.com/docs/message-formatting#message_formatting):

첨부 파일 필드 중 일부가 마크다운이 포함되어 있는 경우 `markdown` 메소드를 사용하여 슬랙에 주어진 마크다운 텍스트를 파싱하여 첨부파일이 표시하도록 할 수 있습니다.

    /**
     * Get the Slack representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return SlackMessage
     */
    public function toSlack($notifiable)
    {
        $url = url('/exceptions/'.$this->exception->id);

        return (new SlackMessage)
                    ->error()
                    ->content('Whoops! Something went wrong.')
                    ->attachment(function ($attachment) use ($url) {
                        $attachment->title('Exception: File Not Found', $url)
                                   ->content('File [background.jpg] was *not found*.')
                                   ->markdown(['text']);
                    });
    }

<a name="routing-slack-notifications"></a>
### Routing Slack Notifications
### 슬랙 알림 라우팅(수신자설정)

To route Slack notifications to the proper location, define a `routeNotificationForSlack` method on your notifiable entity. This should return the webhook URL to which the notification should be delivered. Webhook URLs may be generated by adding an "Incoming Webhook" service to your Slack team:

슬랙 알림이 원하는 위치로 전송되게 하기 위해서 알림 엔티티에 `routeNotificationForSlack` 메소드를 정의할 수 있습니다. 이 메소드는 어느 알림이 전송될 webhook URL을 반환해야합니다. Webhook URL은 슬랙 팀 설정의 "Incoming Webhook" 서비스에서 생성할 수 있습니다.

    <?php

    namespace App;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * Route notifications for the Slack channel.
         *
         * @param  \Illuminate\Notifications\Notification  $notification
         * @return string
         */
        public function routeNotificationForSlack($notification)
        {
            return 'https://hooks.slack.com/services/...';
        }
    }

<a name="localizing-notifications"></a>
## Localizing Notifications
## 알림의 현지화

Laravel allows you to send notifications in a locale other than the current language, and will even remember this locale if the notification is queued.

Laravel을 사용하면 현재 언어가 아닌 언어로도 알림을 보낼 수 있으며 알림이 대기중인 경우에도 이 언어을 기억할 수 있습니다.

To accomplish this, the `Illuminate\Notifications\Notification` class offers a `locale` method to set the desired language. The application will change into this locale when the notification is being formatted and then revert back to the previous locale when formatting is complete:

이를 위해 `Illuminate\Notifications\Mailable` 클래스는 원하는 언어를 지정하는 `locale` 메소드를 제공합니다. 알림의 양식이 만들어 질 때 애플리케이션이 이 언어로 변경되고 양식의 생성이 완료되면 이전 언어로 되돌립니다.

    $user->notify((new InvoicePaid($invoice))->locale('es'));

Localization of multiple notifiable entries may also be achieved via the `Notification` facade:

알림이 가능한 항목들의 현지화를 `알림(Notification)` 파사드로 처리 할 수도 있습니다.

    Notification::locale('es')->send($users, new InvoicePaid($invoice));

### User Preferred Locales
### 사용자 선호 언어

Sometimes, applications store each user's preferred locale. By implementing the `HasLocalePreference` contract on your notifiable model, you may instruct Laravel to use this stored locale when sending a notification:

때로는 애플리케이션의 각 사용자 마다 기본 언어설정이 저장됩니다. 하나 이상의 모델에 `HasLocalePreference` contract을 구현하면 Laravel이 알림을 보낼 때 이 저장된 언어를 사용하게 할 수 있습니다.

    use Illuminate\Contracts\Translation\HasLocalePreference;

    class User extends Model implements HasLocalePreference
    {
        /**
         * Get the user's preferred locale.
         *
         * @return string
         */
        public function preferredLocale()
        {
            return $this->locale;
        }
    }

Once you have implemented the interface, Laravel will automatically use the preferred locale when sending notifications and mailables to the model. Therefore, there is no need to call the `locale` method when using this interface:

일단 인터페이스를 구현하면 Laravel은 알림 및 모델을 모델로 전송할 때 자동으로 기본 설정 언어를 사용합니다. 따라서 이 인터페이스를 사용할 때는 `locale` 메소드를 호출 할 필요가 없습니다.

    $user->notify(new InvoicePaid($invoice));

<a name="notification-events"></a>
## Notification Events
## 알림 이벤트

When a notification is sent, the `Illuminate\Notifications\Events\NotificationSent` event is fired by the notification system. This contains the "notifiable" entity and the notification instance itself. You may register listeners for this event in your `EventServiceProvider`:

알림이 전송될 때, 알림 시스템에 의해서 `Illuminate\Notifications\Events\NotificationSent` 이벤트가 발생합니다. 이 이벤트는 "notifiable" 엔티티와 알림 인스턴스 자체를 가지고 있습니다. `EventServiceProvider` 에서 이 이벤트에 대한 리스너를 등록할 수 있습니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Notifications\Events\NotificationSent' => [
            'App\Listeners\LogNotification',
        ],
    ];

> {tip} After registering listeners in your `EventServiceProvider`, use the `event:generate` Artisan command to quickly generate listener classes.

> {tip} `EventServiceProvider` 에서 리스너를 등록하고 난후, `event:generate` 아티즌 명령어를 사용하여 빠르게 리스너 클래스를 생성할 수 있습니다.

Within an event listener, you may access the `notifiable`, `notification`, and `channel` properties on the event to learn more about the notification recipient or the notification itself:

이벤트 리스너 안에서 알림의 수신자 또는 알림 그 자체에 대해서 보다 자세한 정보를 얻기 위해서 이벤트의 `notifiable`, `notification`, 그리고 `channel` 속성에 엑세스 할 수 있습니다.

    /**
     * Handle the event.
     *
     * @param  NotificationSent  $event
     * @return void
     */
    public function handle(NotificationSent $event)
    {
        // $event->channel
        // $event->notifiable
        // $event->notification
        // $event->response
    }

<a name="custom-channels"></a>
## Custom Channels
## 사용자 정의(커스텀) 채널

Laravel ships with a handful of notification channels, but you may want to write your own drivers to deliver notifications via other channels. Laravel makes it simple. To get started, define a class that contains a `send` method. The method should receive two arguments: a `$notifiable` and a `$notification`:

라라벨에서는 몇가지 알림 채널을 제공하고 있지만, 다른 채널을 통해서 알림을 전송하기 위해서 고유한 드라이버를 작성하기를 원할 수도 있습니다. 라라벨에서는 이를 손쉽게 할 수 있습니다. 시작하려면 `send` 메소드를 가지고 있는 클래스를 정의합니다. 이 메소드는 `$notifiable` 와 `$notification` 두개의 인자를 전달 받야야 합니다.

    <?php

    namespace App\Channels;

    use Illuminate\Notifications\Notification;

    class VoiceChannel
    {
        /**
         * Send the given notification.
         *
         * @param  mixed  $notifiable
         * @param  \Illuminate\Notifications\Notification  $notification
         * @return void
         */
        public function send($notifiable, Notification $notification)
        {
            $message = $notification->toVoice($notifiable);

            // Send notification to the $notifiable instance...
        }
    }

Once your notification channel class has been defined, you may return the class name from the `via` method of any of your notifications:

알림 채널 클래스를 정의하고 나면, 알림의 `via` 메소드에서 클래스 이름을 반환하면 됩니다.

    <?php

    namespace App\Notifications;

    use App\Channels\Messages\VoiceMessage;
    use App\Channels\VoiceChannel;
    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Notifications\Notification;

    class InvoicePaid extends Notification
    {
        use Queueable;

        /**
         * Get the notification channels.
         *
         * @param  mixed  $notifiable
         * @return array|string
         */
        public function via($notifiable)
        {
            return [VoiceChannel::class];
        }

        /**
         * Get the voice representation of the notification.
         *
         * @param  mixed  $notifiable
         * @return VoiceMessage
         */
        public function toVoice($notifiable)
        {
            // ...
        }
    }
