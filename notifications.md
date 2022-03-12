# Notifications-알림

- [시작하기](#introduction)
- [알림 생성하기](#creating-notifications)
- [알림 발송하기](#sending-notifications)
    - [Notifiable 트레이트-trait 사용하기](#using-the-notifiable-trait)
    - [Notification 파사드 사용하기](#using-the-notification-facade)
    - [전달할 채널 지정하기](#specifying-delivery-channels)
    - [Queue-큐를 통한 Notifications 사용](#queueing-notifications)
    - [필요시 대상을 지정한 Notifications](#on-demand-notifications)
- [이메일을 통한 알림](#mail-notifications)
    - [이메일 메세지 포맷 지정하기](#formatting-mail-messages)
    - [Sender 커스터마이징](#customizing-the-sender)
    - [수신자 설정하기](#customizing-the-recipient)
    - [제목 설정하기](#customizing-the-subject)
    - [메일러 설정하기](#customizing-the-mailer)
    - [템플릿 설정하기](#customizing-the-templates)
    - [메일 알림 미리보기](#previewing-mail-notifications)
- [마크다운 이메일 알림](#markdown-mail-notifications)
    - [메세지 클래스 생성하기](#generating-the-message)
    - [메세지 작성하기](#writing-the-message)
    - [컴포넌트 커스터마이징 하기](#customizing-the-components)
- [데이터베이스 알림](#database-notifications)
    - [사전준비사항](#database-prerequisites)
    - [데이터베이스 알림 포맷 지정하기](#formatting-database-notifications)
    - [알림에 엑세스하기](#accessing-the-notifications)
    - [알림을 읽음 표시로 전환하기](#marking-notifications-as-read)
- [브로드캐스팅 알림](#broadcast-notifications)
    - [사전 준비사항](#broadcast-prerequisites)
    - [브로드캐스팅 알림 포맷 지정하기](#formatting-broadcast-notifications)
    - [알림을 위한 리스너](#listening-for-notifications)
- [SMS 알림](#sms-notifications)
    - [사전준비사항](#sms-prerequisites)
    - [SMS 알림 포맷 지정하기](#formatting-sms-notifications)
    - [Shortcode 알림 포맷 지정하기](#formatting-shortcode-notifications)
    - [발신자 번호 수정하기](#customizing-the-from-number)
    - [클라이언트 참조 추가](#adding-a-client-reference)
    - [SMS 알림 라우팅](#routing-sms-notifications)
- [슬랙-Slack 알림](#slack-notifications)
    - [사전준비사항](#slack-prerequisites)
    - [Slack 알림 포맷 지정하기](#formatting-slack-notifications)
    - [슬랙 첨부 파일](#slack-attachments)
    - [슬랙 알림 라우팅](#routing-slack-notifications)
- [알림의 현지화](#localizing-notifications)
- [알림 이벤트](#notification-events)
- [사용자 정의(커스텀)채널](#custom-channels)

<a name="introduction"></a>
## 시작하기

[이메일 발송](/docs/{{version}}/mail)기능 이외에도 Laravel은 이메일, SMS ([Vonage](https://www.vonage.com/communications-apis/) 이전에 Nexmo로 알려진 서비스 제공회사) 또는 [Slack](https:slack.com) 를 포함한 다양한 전달 방법들을 통해 알림 전송기능을 제공합니다. 또한 수십 개의 다른 채널로 알림을 보낼 수 있도록 다양한 [커뮤니티 제작 알림 채널](https://laravel-notification-channels.com/about/#suggesting-a-new-channel) 만들어져 있습니다. 물론, 알림은 데이터베이스에 저장되어 웹 인터페이스에 표시될 수도 있습니다.
> {tip} 역자주 국내 SMS으로 [SENS for Laravel](https://github.com/seungmun/laravel-sens) 이 있습니다.

일반적으로, 알림은 애플리케이션 안에서 발생한 어떤 사항을 사용자에게 전달하기 위한 짧은 정보를 담은 메세지입니다. 예를들어, 결제가 필요한 애플리케이션을 작성중이라면, "결제 완료" 알림을 이메일이나 SMS를 통해서 사용자에게 발송할 수 있습니다.

<a name="creating-notifications"></a>
## 알림 생성하기

라라벨에서 각각의 알림은 하나의 클래스로 표현할 수 있습니다. (일반적으로 `app/Notifications` 디렉토리에 저장된) 애플리케이션에서 이 디렉토리를 볼 수 없더라도 걱정하지 마십시오, `make:notification` 아티즌 명령어를 실행할 때 디렉토리가 생성됩니다.

    php artisan make:notification InvoicePaid

이 명령어는 새로운 알림 클래스를 `app/Notifications`디렉토리에 생성합니다. 각각의 알림 클래스는 `via` 메소드와 특정 채널에 최적화된 메세지로 변환하는 몇가지 메세지를 작성하는 방법(`toMail` 또는 `toDatabase` 와 같은)을 가지고 있습니다.

<a name="sending-notifications"></a>
## 알림 발송하기

<a name="using-the-notifiable-trait"></a>
### Notifiable 트레이트-trait 사용하기

알림은 두가지 방법으로 보낼 수 있습니다. `Notifiable` 트레이트-trait의 `notify` 메소드를 이용하거나 `Notification` [파사드](/docs/{{version}}/facades) 를 이용합니다. `Notifiable` 트레이트-trait는 어플리케이션에 `App\Models\User` 모델을 기본적으로 포함하고 있습니다.

    <?php

    namespace App\Models;

    use Illuminate\Foundation\Auth\User as Authenticatable;
    use Illuminate\Notifications\Notifiable;

    class User extends Authenticatable
    {
        use Notifiable;
    }

이 트레이트-trait가 제공하는 `notify` 메소드는 Notification 인스턴스를 받아야 합니다.

    use App\Notifications\InvoicePaid;

    $user->notify(new InvoicePaid($invoice));

> {tip} 기억하세요. 당신은 당신의 어떤 모델에도 `Notifiable` 트레이트-trait를 사용할 수 있습니다. 오직 `User` 모델에만 한정되지 않습니다.

<a name="using-the-notification-facade"></a>
### Notification 파사드 사용하기

다른 대안으로 당신은 `Notification` [파사드](/docs/{{version}}/facades)를 이용해 알림을 보낼 수 있습니다. 이 방식은 유저 집합과 같은 다수의 단체에 알림을 보낼때 필요합니다. 파사드를 이용해 알림을 보내기 위해서는 모든 알림 수신자와 Notification 인스턴스를 `send` 메소드에 넘겨줘야 합니다.

    use Illuminate\Support\Facades\Notification;

    Notification::send($users, new InvoicePaid($invoice));

`sendNow` 메소드를 사용하여 즉시 알림을 보낼 수도 있습니다. 이 메소드는 알림이 `ShouldQueue` 인터페이스를 구현하더라도 즉시 알림을 보냅니다.

    Notification::sendNow($developers, new DeploymentCompleted($deployment));

<a name="specifying-delivery-channels"></a>
### 전달할 채널 지정하기

모든 알림 클래스는 알림이 어떤 채널에 전달될지 결정할 수 있는 `via` 메소드를 가지고 있습니다. 알림은 `mail`, `database`, `broadcast`, `nexmo` 그리고 `slack` 채널을 통해서 보낼 수 있습니다.

> {tip} 텔레그램이나 Pusher 와 같은 다른 채널을 사용하고자 한다면, [라라벨 알림 채널 웹사이트](http://laravel-notification-channels.com)와 같은 커뮤니티 드라이버를 확인해 보십시오.

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
### Queue-큐를 통한 Notifications 사용

> {note} 알림을 보내는데 Queue-큐를 사용하기 전에 Queue-큐 설정을 완료하고 [Queue-큐 워커를 구동](/docs/{{version}}/queues)해야 합니다.

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

`ShouldQueue` 인터페이스를 여러분의 알림 클래스에 추가한 뒤에는, 보통과 같이 알림을 보낼 수 있습니다. 라라벨은 클래스의 `ShouldQueue` 인터페이스를 감지하고 자동으로 큐를 통해서 알림을 전달합니다.

    $user->notify(new InvoicePaid($invoice));

알림을 보낼 때 이를 지연해서 보낼 필요가 있다면, 알림 인스턴스에 `delay` 메소드를 체이닝하면 됩니다.

    $when = now()->addMinutes(10);

    $user->notify((new InvoicePaid($invoice))->delay($when));

특정 채널에 대한 지연을 설정하기 위해 배열을 `delay` 메소드에 전달할 수 있습니다.

    $user->notify((new InvoicePaid($invoice))->delay([
        'mail' => now()->addMinutes(5),
        'sms' => now()->addMinutes(10),
    ]));

알림을 대기열(queue)에 넣을 때 각 수신자 및 채널 조합에 대해 대기열에 있는 작업이 생성됩니다. 예를 들어 알림에 3명의 수신자와 2개의 채널이 있는 경우 6개의 작업이 대기열로 발송됩니다.

#### 알림 대기열 커넥션 커스터마이징

기본적으로 대기 중인 알림은 애플리케이션의 디폴트 대기열 연결을 사용하여 대기합니다. 특정 알림에 사용해야 하는 다른 연결을 지정하려면 알림 클래스에 `$connection` 속성을 정의할 수 있습니다.

    /**
     * The name of the queue connection to use when queueing the notification.
     *
     * @var string
     */
    public $connection = 'redis';


#### 알림 채널 대기열 커스터마이징

알림이 지원하는 각 알림 채널에 사용해야하는 특정 대기열을 지정하려면 알림에 `viaQueues` 메소드를 정의 할 수 있습니다. 이 메서드는 채널 이름 / 큐 이름 쌍의 배열을 반환해야합니다.

    /**
     * Determine which queues should be used for each notification channel.
     *
     * @return array
     */
    public function viaQueues()
    {
        return [
            'mail' => 'mail-queue',
            'slack' => 'slack-queue',
        ];
    }

#### 대기 중인 알림 및 데이터베이스 트랜잭션

대기열에 있는 알림이 데이터베이스 트랜잭션 내에서 전달되면 데이터베이스 트랜잭션이 커밋되기 전에 대기열에서 처리될 수 있습니다. 이 경우 데이터베이스 트랜잭션 중 모델 또는 데이터베이스 레코드에 대한 업데이트가 아직 데이터베이스에 반영되지 않을 수 있습니다. 또한 트랜잭션 내에서 생성된 모델 또는 데이터베이스 레코드는 데이터베이스에 존재하지 않을 수 있습니다. 알림이 이러한 모델에 의존하는 경우 대기 중인 알림을 보내는 작업이 처리될 때 예기치 않은 오류가 발생할 수 있습니다.

대기열 연결의 `after_commit` 설정 옵션이 `false`로 설정되어 있으면 알림을 보낼 때 `afterCommit` 메소드를 호출하여 열려 있는 모든 데이터베이스 트랜잭션이 커밋된 후에 특정 대기열에 있는 알림이 전달되어야 함을 계속 표시할 수 있습니다.

    use App\Notifications\InvoicePaid;

    $user->notify((new InvoicePaid($invoice))->afterCommit());

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
         *
         * @return void
         */
        public function __construct()
        {
            $this->afterCommit();
        }
    }

> {tip} 이러한 문제를 해결하는 방법에 대해 자세히 알아보려면 [대기 중인 작업 및 데이터베이스 트랜잭션](/docs/{{version}}/queues#jobs-and-database-transactions) 문서를 검토하세요.


#### 대기 중인 알림을 보내야 하는지 여부 결정

대기 중인 알림이 백그라운드 처리를 위해 대기열에 등록된 후 일반적으로 대기열 작업자(Queue worker)가 이를 수락하고 의도한 수신자에게 보냅니다.

그러나 대기열 작업자가 처리한 후 대기열에 있는 알림을 보낼지 여부를 최종 결정하려면 알림 클래스에 `shouldSend` 메소드를 정의할 수 있습니다. 이 메소드가 `false`를 반환하면 알림이 전송되지 않습니다.

    /**
     * Determine if the notification should be sent.
     *
     * @param  mixed  $notifiable
     * @param  string  $channel
     * @return bool
     */
    public function shouldSend($notifiable, $channel)
    {
        return $this->invoice->isPaid();
    }

<a name="on-demand-notifications"></a>
### 필요시 대상을 지정한 Notifications

때로는 애플리케이션의 "User"모델로 저장되지 않은 사람에게 알림을 보내야 할 수도 있습니다. `Notification` 파사드의 `route` 메소드를 사용하여 알림을 보내기 전에 임시 알림 라우팅 정보를 지정할 수 있습니다.

    Notification::route('mail', 'taylor@example.com')
                ->route('nexmo', '5555555555')
                ->route('slack', 'https://hooks.slack.com/services/...')
                ->notify(new InvoicePaid($invoice));

필요에 따라 임시적인 알림을 `mail` 경로로 보낼 때 수신자의 이름을 제공하려면 이메일 주소를 키로 포함하고 이름을 배열의 첫 번째 요소 값으로 포함하는 배열을 제공해야 합니다.

    Notification::route('mail', [
        'barrett@example.com' => 'Barrett Blair',
    ])->notify(new InvoicePaid($invoice));

<a name="mail-notifications"></a>
## 이메일을 통한 알림

<a name="formatting-mail-messages"></a>
### 알림 메세지 포맷 지정하기

알림이 이메일로 전송되는 것을 지원하는 경우 알림 클래스에 `toMail` 메소드를 정의해야 합니다. 이 메소드는 `$notifiable` 구현체를 수신하고 `Illuminate\Notifications\Messages\MailMessage` 인스턴스를 반환해야 합니다.

`MailMessage` 클래스에는 트랜잭션 이메일 메시지를 작성하는 데 도움이 되는 몇 가지 간단한 메소드가 포함되어 있습니다. 메일 메시지에는 "행동 유도문안"과 함께 한 줄의 텍스트가 포함될 수 있습니다. `toMail` 메소드의 예를 살펴보겠습니다.

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

> {tip} `toMail` 메소드 안에서 `$this->invoice->id`를 사용하고 있다는 것을 주의해주십시오. 알림이 메세지를 생성하기 위해서 필요한 그 어떤 데이터라도 알림 클래스의 생성자에 전달할 수 있습니다.

이 예시에서는 인사말, 한 줄의 텍스트, 클릭 유도 및 다른 텍스트 줄을 등록합니다. `MailMessage` 객체에서 제공하는 이러한 메소드를 사용하면 작은 트랜잭션 이메일을 간단하고 빠르게 포맷할 수 있습니다. 그러면 메일 채널은 메시지를 일반 텍스트에서 아름답고 반응이 빠른 HTML 이메일 템플릿으로 변환합니다. 다음은 `mail` 채널에서 생성된 이메일의 예입니다.

<img src="https://laravel.com/img/docs/notification-example-2.png">

> {tip} 메일 알림을 보낼 때 `config/app.php` 설정 파일에서 `name` 설정 옵션을 설정해야 합니다. 이 값은 메일 알림 메시지의 머리글과 바닥글에 사용됩니다..

#### 기타 메일 알림 포매팅 옵션

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

`view` 메소드에 지정된 배열의 두 번째 요소로 뷰 이름을 전달하여 메일 메시지에 대한 일반 텍스트 뷰를 지정할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
    {
        return (new MailMessage)->view(
            ['emails.name.html', 'emails.name.plain'],
            ['invoice' => $this->invoice]
        );
    }

<a name="error-messages"></a>
#### 에러 메세지

일부 알림은 결제 실패와 같은 오류를 사용자에게 알려줍니다. 메시지를 작성할 때 `error` 메소드를 호출하여 메일 메시지가 오류에 관한 것임을 나타낼 수 있습니다. 메일 메시지에 `error` 메소드를 사용하면 클릭 유도문안 버튼이 검은색 대신 빨간색으로 표시됩니다.

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
### Sender 커스터마이징

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
                    ->from('barrett@example.com', 'Barrett Blair')
                    ->line('...');
    }

<a name="customizing-the-recipient"></a>
### 수신자 설정하기

`mail` 채널을 통해 알림을 보낼 때 알림 시스템은 대상이 되는 알림 클래스에서 `email` 속성을 자동으로 찾습니다. 알림을 받을 수 있는 클래스에 `routeNotificationForMail` 메소드를 정의하여 알림을 전달하는 데 사용되는 이메일 주소를 사용자 지정할 수 있습니다.

    <?php

    namespace App\Models;

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

            // Return email address and name...
            return [$this->email_address => $this->name];
        }
    }

<a name="customizing-the-subject"></a>
### 제목 설정하기

기본적으로 이메일 제목은 "Title Case" 형식의 알림 클래스 이름입니다. 따라서 알림 클래스의 이름이 `InvoicePaid`인 경우 이메일 제목은 `Invoice Paid`가 됩니다. 메시지에 다른 제목을 지정하려면 메시지를 작성할 때 `subject` 메소드를 호출할 수 있습니다.

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

<a name="customizing-the-mailer"></a>
### 메일러 설정하기

기본적으로 이메일 알림은 `config/mail.php` 설정 파일에 정의된 기본 메일러를 사용하여 전송됩니다. 그러나 메시지를 작성할 때 `mailer` 메소드를 호출하여 런타임에 다른 메일러를 지정할 수 있습니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->mailer('postmark')
                    ->line('...');
    }

<a name="customizing-the-templates"></a>
### 템플릿 설정하기

여러분은 알림 패키지의 리소스의 메일 알림에서 사용되는 HTML 과 일반 텍스트를 수정할 수 있습니다. 이 명령어를 사용하고 나면 메일 알림 템플릿이 `resources/views/vendor/notifications` 디렉토리에 저장됩니다.

    php artisan vendor:publish --tag=laravel-notifications

### 첨부 파일

이메일 알림에 첨부 파일을 추가하려면 메시지를 작성하는 동안 `attach` 메소드을 사용하세요. `attach` 메소드는 파일의 절대 경로를 첫 번째 인수로 받아들입니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->greeting('Hello!')
                    ->attach('/path/to/file');
    }

메시지에 파일을 첨부할 때 `attach` 메소드의 두 번째 인수로 `array`를 전달하여 표시 이름, MIME 유형을 지정할 수도 있습니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->greeting('Hello!')
                    ->attach('/path/to/file', [
                        'as' => 'name.pdf',
                        'mime' => 'application/pdf',
                    ]);
    }

메일 가능 개체에 파일을 첨부하는 것과 달리 `attachFromStorage`를 사용하여 스토리지 디스크에서 직접 파일을 첨부할 수 없습니다. 저장 디스크의 파일에 대한 절대 경로와 함께 `attach` 메소드을 사용해야 합니다. 다른 대안으로, `toMail` 메소드에 [mailable](/docs/{{version}}/mail#generating-mailables)을 리턴할 수 있습니다.

    use App\Mail\InvoicePaid as InvoicePaidMailable;

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return Mailable
     */
    public function toMail($notifiable)
    {
        return (new InvoicePaidMailable($this->invoice))
                    ->to($notifiable->email)
                    ->attachFromStorage('/path/to/file');
    }

#### 로우 데이터 첨부 파일

`attachData` 메소드는 로우 문자열의 바이트를 첨부 파일로 첨부하는 데 사용할 수 있습니다. `attachData` 메소드를 호출할 때 첨부 파일에 할당해야 하는 파일 이름을 제공해야 합니다.

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\MailMessage
     */
    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->greeting('Hello!')
                    ->attachData($this->pdf, 'name.pdf', [
                        'mime' => 'application/pdf',
                    ]);
    }

### Mailables 사용

필요한 경우 알림의 `toMail` 메소드에서 전체 [메일가능한 객체](docs/{{version}}/mail)을 반환할 수 있습니다. `MailMessage` 대신 `Mailable`을 반환할 때 메일 가능 객체의 `to` 메소드를 사용하여 메시지 수신자를 지정해야 합니다.

    use App\Mail\InvoicePaid as InvoicePaidMailable;

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return Mailable
     */
    public function toMail($notifiable)
    {
        return (new InvoicePaidMailable($this->invoice))
                    ->to($notifiable->email);
    }

#### 메일가능한 객체 & 임시적인 알림

[온디멘드 알림](#on-demand-notifications)을 보내는 경우 `toMail` 메소드에 제공된 `$notifiable` 인스턴스는 온디멘드 알림을 보내야 하는 이메일 주소를 검색하는 데 사용할 수 있는 메소드인 `routeNotificationFor`를 제공하는 `Illuminate\Notifications\AnonymousNotifiable`의 인스턴스가 됩니다.

    use App\Mail\InvoicePaid as InvoicePaidMailable;
    use Illuminate\Notifications\AnonymousNotifiable;

    /**
     * Get the mail representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return Mailable
     */
    public function toMail($notifiable)
    {
        $address = $notifiable instanceof AnonymousNotifiable
                ? $notifiable->routeNotificationFor('mail')
                : $notifiable->email;

        return (new InvoicePaidMailable($this->invoice))
                    ->to($address);
    }

<a name="previewing-mail-notifications"></a>
### 메일 알림 미리보기

메일 알림 템플릿을 디자인할 때 일반적인 Blade 템플릿처럼 브라우저에서 렌더링된 메일 메시지를 빠르게 미리 보는 것이 편리합니다. 이러한 이유로 라라벨을 사용하면 메일 알림에 의해 생성된 메일 메시지를 라우트 클로저 또는 컨트롤러에서 직접 반환할 수 있습니다. `MailMessage`가 반환되면 브라우저에 렌더링되고 표시되므로 실제 이메일 주소로 보낼 필요 없이 디자인을 빠르게 미리 볼 수 있습니다.

    use App\Models\Invoice;
    use App\Notifications\InvoicePaid;

    Route::get('/notification', function () {
        $invoice = Invoice::find(1);

        return (new InvoicePaid($invoice))
                    ->toMail($invoice->user);
    });

<a name="markdown-mail-notifications"></a>
## 마크다운 이메일 알림

마크다운 메일 알림을 사용하면 사전에 작성된 메일 알림 템플릿을 활용할 수 있으며, 컨텐츠를 더 길게 작성할 수도 있으며, 메세지를 커스터마이징하는데 유용합니다. 마크다운을 통해서 메세지를 작성하게 되면서, 라라벨은 메세지를 보다 원활하게 렌더링 하고, 반응형 HTML 템플릿을 사용하는 동시에 일반 텍스트를 자동으로 생성할 수 있게 되었습니다.

<a name="generating-the-message"></a>
### 메세지 클래스 생성하기

일치하는 마크다운 템플릿으로 알림 클래스를 생성하려면 `make:notification` 아티즌 명령어에 `--markdown` 옵션을 사용하면 됩니다.

    php artisan make:notification InvoicePaid --markdown=mail.invoice.paid

다른 모든 메일 알림과 마찬가지로 Markdown 템플릿을 사용하는 알림은 알림 클래스에 `toMail` 메소드를 정의해야 합니다. 그러나 `line` 및 `action` 메소드를 사용하여 알림을 설정하는 대신 `markdown` 메소드를 사용하여 사용해야 하는 Markdown 템플릿의 이름을 지정합니다. 템플릿에서 사용할 수 있도록 하려는 데이터 배열이 메소드의 두 번째 인수로 전달될 수 있습니다.

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
### 메세지 작성하기

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

#### 버튼 컴포넌트

버튼 컴포넌트는 가운데에 있는 버튼 링크를 렌더링합니다. 설정요소는 `url`과 선택적 `color`의 두 인수를 허용합니다. 지원되는 색상은 `primary`, `green`, `red` 입니다. 알림에 원하는 만큼 버튼 컴포넌트를 추가할 수 있습니다.

    @component('mail::button', ['url' => $url, 'color' => 'green'])
    View Invoice
    @endcomponent

#### 패널 컴포넌트

패널 컴포넌트는 나머지 텍스트와 배경색이 약간 다른 패널에 텍스트 블럭을 렌더링합니다. 이렇게하면 주어진 텍스트 블럭을 보다 강조할 수 있습니다.

    @component('mail::panel')
    This is the panel content.
    @endcomponent

#### 테이블 컴포넌트

테이블 컴포넌트는 마크다운 테이블을 HTML 테이블로 변환합니다. 이 컴포넌트는 마크다운 테이블을 내용으로 전달받습니다. 테이블 컬럼 정렬은 기본적인 마크다운 테이블 정렬 문법을 사용합니다.

    @component('mail::table')
    | Laravel       | Table         | Example  |
    | ------------- |:-------------:| --------:|
    | Col 2 is      | Centered      | $10      |
    | Col 3 is      | Right-Aligned | $20      |
    @endcomponent

<a name="customizing-the-components"></a>
### 컴포넌트 커스터마이징

애플리케이션에서 사용하는 모든 마크다운 알림 컴포넌트는 커스터마이징이 가능합니다. 먼저 컴포넌트를 내보내기 위해서 `vendor:publish` 아티즌 명령어를 사용하여 `laravel-mail` 애셋 태그를 지정합니다.

    php artisan vendor:publish --tag=laravel-mail

이 명령어는 마크다운 메일 컴포넌트를 `resources/views/vendor/mail` 디렉토리에 퍼블리싱 합니다. `mail` 디렉토리는 `html` 과 `text` 디렉토리를 가지고 있는데, 각각은 사용가능한 컴포넌트의 표현들이 들어 있습니다. 원하시는대로 이 컴포넌트를 커스터마이징 할 수 있습니다.

#### CSS 커스터마이징

컴포넌트를 내보내기(export) 한 이후에, `resources/views/vendor/mail/html/themes` 디렉토리를 보면 `default.css` 파일을 확인할 수 있습니다. 이 파일에서 CSS를 커스터마이징 할 수 있으며, 마크다운 알림의 HTML 표현에서 스타일이 자동으로 적용됩니다.

Laravel의 Markdown 컴포넌트에 대해 완전히 새로운 테마를 구축하려면 `html/themes` 디렉토리에 CSS 파일을 추가 할 수 있습니다. CSS 파일의 이름을 지정하고 저장 한 후, `mail` 컴포넌트의 `theme` 옵션을 새 테마의 이름과 동일하도록 수정하십시오.

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
## 데이터베이스 알림

<a name="database-prerequisites"></a>
### 사전 준비사항

`database` 알림 채널은 알림 정보를 데이터베이스 테이블에 저장합니다. 이 테이블에는 알림 유형 및 알림을 설명하는 JSON 데이터 구조와 같은 정보가 포함됩니다.

테이블을 쿼리하여 애플리케이션의 사용자 인터페이스에 알림을 표시할 수 있습니다. 그러나 그렇게 하기 전에 알림을 보관할 데이터베이스 테이블을 만들어야 합니다. `notifications:table` 명령을 사용하여 적절한 테이블 스키마로 [마이그레이션](docs/{{version}}/migrations)을 생성할 수 있습니다.

    php artisan notifications:table

    php artisan migrate

<a name="formatting-database-notifications"></a>
### 데이터베이스 알림 포맷 지정하기

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

#### `toDatabase` Vs `toArray`

`toArray` 메소드는 `broadcast` 채널에서도 JavaScript 기반 프론트엔드로 브로드캐스트할 데이터를 결정하는 데 사용됩니다. `database` 및 `broadcast` 채널에 대해 두 가지 다른 배열 표현을 사용하려면 `toArray` 메소드 대신 `toDatabase` 메소드를 정의해야 합니다.

<a name="accessing-the-notifications"></a>
### 알림에 엑세스하기

알림이 데이터베이스에 저장되면 알림 인스턴스에서 액세스할 수 있는 편리한 방법이 필요합니다. 라라벨의 기본 `App\Models\User` 모델에 포함된 `Illuminate\Notifications\Notifiable` 트레이트-trait에는 알림을 가져오기 위해 다른 Eloquent 관계[엘로퀀트 연관관계](/docs/{{version}}/eloquent-relationships) 처럼 이 방법에 액세스할 수 있는 모델  `notifications`이 있습니다. 기본적으로 알림은 컬렉션 시작 부분에 가장 최근 알림과 함께 'created_at' 타임스탬프로 정렬됩니다.

    $user = App\Models\User::find(1);

    foreach ($user->notifications as $notification) {
        echo $notification->type;
    }

"읽지 않은" 알림만 검색하려면 `unreadNotifications` 관계를 사용할 수 있습니다. 다시 말하지만, 이러한 알림은 컬렉션 시작 부분에 가장 최근 알림과 함께 `created_at` 타임스탬프에 따라 정렬됩니다.

    $user = App\Models\User::find(1);

    foreach ($user->unreadNotifications as $notification) {
        echo $notification->type;
    }

> {tip} 자바스크립트 클라이언트에서 알림에 액세스하려면 현재 사용자와 같이 notifiable 관계 모델에 대한 알림을 반환하는 알림 컨트롤러를 애플리케이션에 정의해야 합니다. 그런 다음 JavaScript 클라이언트에서 해당 컨트롤러의 URL로 HTTP 요청을 할 수 있습니다.

<a name="marking-notifications-as-read"></a>
### 알림을 읽음 표시로 전환하기

일반적으로, 사용자가 알림을 확인했을 때 이를 "읽음"으로 표시하기를 원할 것입니다. `Illuminate\Notifications\Notifiable` 트레이트-trait는 알림 데이터베이스 레코드에서 `read_at` 컬럼을 업데이트 하는 `markAsRead` 메소드를 제공합니다.

    $user = App\Models\User::find(1);

    foreach ($user->unreadNotifications as $notification) {
        $notification->markAsRead();
    }

이경우, 각각의 알림을 반복문 안에서 수정하는 대신에, `markAsRead` 메소드를 알림 컬렉션에 바로 사용할 수도 있습니다.

    $user->unreadNotifications->markAsRead();

또한, 데이터베이스에서 별도로 조회하지 않고서 바로 알림을 읽음 표시 하는데 mass-update(대량 업데이트) 쿼리를 사용할 수도 있습니다.

    $user = App\Models\User::find(1);

    $user->unreadNotifications()->update(['read_at' => now()]);

여러분은 테이블 엔티티에서 알림을 삭제하기 위해서 `delete` 메소드를 사용할 수 있습니다.

    $user->notifications()->delete();

<a name="broadcast-notifications"></a>
## 알림 브로드 캐스팅

<a name="broadcast-prerequisites"></a>
### 사전준비사항

알림을 브로드캐스팅하기 전에 라라벨의 [이벤트 브로드캐스팅](docs/{{version}}/broadcasting) 서비스를 설정하고 숙지해야 합니다. 이벤트 브로드캐스팅은 JavaScript 기반 프론트엔드에서 서버 측 Laravel 이벤트에 반응하는 방법을 제공합니다.

<a name="formatting-broadcast-notifications"></a>
### 브로드캐스팅 알림 포맷 지정하기

`broadcast` 채널은 라라벨의 [이벤트 브로드캐스팅](docs/{{version}}/broadcasting) 서비스를 사용하여 알림을 브로드캐스트하므로 JavaScript 기반 프론트엔드가 실시간으로 알림을 받을 수 있습니다. 알림이 브로드캐스팅을 지원하는 경우 알림 클래스에서 `toBroadcast` 메소드를 정의할 수 있습니다. 이 메소드는 `$notifiable` 모델를 수신하고 `BroadcastMessage` 인스턴스를 반환해야 합니다. `toBroadcast` 메소드가 존재하지 않으면 `toArray` 메소드를 사용하여 브로드캐스트해야 하는 데이터를 수집합니다. 반환된 데이터는 JSON으로 인코딩되어 JavaScript 기반 프런트엔드로 브로드캐스트됩니다. `toBroadcast` 메소드의 예를 살펴보겠습니다.

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

#### 브로드캐스트 Queue-큐 설정하기

모든 브로드캐스트 알림은 큐를 사용합니다. 브로드캐스팅을 수행할 때 사용할 큐 커넥션이나 큐 이름을 지정하려면, `BroadcastMessage` 의 `onConnection` 과 `onQueue` 메소드를 사용하면 됩니다.

    return (new BroadcastMessage($data))
                    ->onConnection('sqs')
                    ->onQueue('broadcasts');

#### 알림 유형 사용자 정의

지정한 데이터 외에도 모든 브로드캐스트 알림에는 알림의 전체 클래스 이름이 포함된 `type` 필드가 있습니다. 알림 `type`을 사용자 지정하려면 알림 클래스에 `broadcastType` 메소드를 정의할 수 있습니다.

    use Illuminate\Notifications\Messages\BroadcastMessage;

    /**
     * Get the type of the notification being broadcast.
     *
     * @return string
     */
    public function broadcastType()
    {
        return 'broadcast.message';
    }

### 알림 수신하기

알림은 `{notifiable}.{id}` 규칙을 사용하여 형식이 지정된 비공개 채널에서 브로드캐스트됩니다. 따라서 ID가 `1`인 `App\Models\User` 인스턴스에 알림을 보내는 경우 알림은 `App.Models.User.1` 비공개 채널에서 브로드캐스트됩니다. [Laravel Echo](docs/{{version}}/broadcasting#client-side-installation)를 사용할 때 `notification` 메소드를 사용하여 채널의 알림을 쉽게 들을 수 있습니다.

    Echo.private('App.Models.User.' + userId)
        .notification((notification) => {
            console.log(notification.type);
        });

#### 알림 채널 커스터미이징하기

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
         *
         * @return string
         */
        public function receivesBroadcastNotificationsOn()
        {
            return 'users.'.$this->id;
        }
    }

<a name="sms-notifications"></a>
## SMS 알림

<a name="sms-prerequisites"></a>
### 사전 준비사항

라라벨에서 SMS 알림 보내기는 [Vonage](https://www.vonage.com)(이전 Nexmo로 알려짐)에서 제공합니다. Vonage를 통해 알림을 보내기 전에 `laravel/nexmo-notification-channel` 및 `nexmo/laravel` Composer 패키지를 설치해야 합니다.

    composer require laravel/nexmo-notification-channel nexmo/laravel

`nexmo/laravel` 패키지에는 [자체 설정 파일](https://github.com/Nexmo/nexmo-laravel/blob/master/config/nexmo.php) 이 포함되어 있습니다. 그러나 이 설정 파일을 자신의 애플리케이션으로 내보낼 필요는 없습니다. `NEXMO_KEY` 및 `NEXMO_SECRET` 환경 변수를 사용하여 Vonage 공개 및 비밀 키를 설정할 수 있습니다.

다음으로 `config/services.php` 설정 파일에 `nexmo` 설정 항목을 추가해야 합니다. 시작하기 위해 아래의 예시 설정을 복사할 수 있습니다.

    'nexmo' => [
        'sms_from' => '15556666666',
    ],

`sms_from` 옵션은 SMS 메시지를 보낼 전화번호입니다. Vonage 제어판에서 애플리케이션의 전화번호를 생성해야 합니다.

<a name="formatting-sms-notifications"></a>
### SMS 알림 포맷 지정하기

알림이 SMS로 전송되는 것을 지원하는 경우 알림 클래스에 `toNexmo` 메소드를 정의해야 합니다. 이 메소드는 `notifiable` 모델를 수신하고 `Illuminate\Notifications\Messages\NexmoMessage` 인스턴스를 반환해야 합니다.

    /**
     * Get the Vonage / SMS representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\NexmoMessage
     */
    public function toNexmo($notifiable)
    {
        return (new NexmoMessage)
                    ->content('Your SMS message content');
    }

<a name="formatting-shortcode-notifications"></a>
### Shortcode 알림 포맷 지정하기

라라벨은 또한 Vonage 계정에 미리 정의된 메시지 템플릿인 단축 코드 알림 전송을 지원합니다. 단축 코드 SMS 알림을 보내려면 알림 클래스에 `toShortcode` 메소드를 정의해야 합니다. 이 메소드 내에서 알림 유형(`alert`, `2fa` 또는 `marketing`)과 템플릿을 채울 커스터마이징 값을 지정하는 배열을 반환할 수 있습니다.

    /**
     * Get the Vonage / Shortcode representation of the notification.
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
            ],
        ];
    }

> {tip} [SMS 알림 라우팅](#routing-sms-notifications) 과 마찬가지로 알림 가능한 모델에 `routeNotificationForShortcode` 메소드를 구현해야 합니다.

#### 유니코드 컨텐츠

SMS 메세지에 유니코드 문자가 포함되어 있다면, `NexmoMessage` 인스턴스를 생성할 때 `unicode` 메소드를 호출해야 합니다.

    /**
     * Get the Vonage / SMS representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\NexmoMessage
     */
    public function toNexmo($notifiable)
    {
        return (new NexmoMessage)
                    ->content('Your unicode message')
                    ->unicode();
    }

<a name="customizing-the-from-number"></a>
### "발신자" 번호 지정하기

`config/services.php` 파일에서 지정한 발신자 전화번호와 다른 전화번호로 표시되는 SMS 알림을 보내고자 하는 경우, `NexmoMessage` 인스턴스의 `from` 메소드를 사용하면 됩니다.

    /**
     * Get the Vonage / SMS representation of the notification.
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

### 클라이언트 참조 추가

사용자, 팀 또는 클라이언트당 비용을 추적하려면 알림에 "client reference"를 추가할 수 있습니다. Vonage를 사용하면 이 클라이언트 참조를 사용하여 보고서를 생성할 수 있으므로 특정 고객의 SMS 사용을 더 잘 이해할 수 있습니다. 클라이언트 참조는 최대 40자의 문자열일 수 있습니다.

    /**
     * Get the Vonage / SMS representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return NexmoMessage
     */
    public function toNexmo($notifiable)
    {
        return (new NexmoMessage)
                    ->clientReference((string) $notifiable->id)
                    ->content('Your SMS message content');
    }

<a name="routing-sms-notifications"></a>
### SMS 알림 라우팅

Vonage 알림을 적절한 전화번호로 라우팅하려면 알림 대상이 되는 모델에 `routeNotificationForNexmo` 메소드를 정의합니다.

    <?php

    namespace App\Models;

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
## 슬랙 알림

<a name="slack-prerequisites"></a>
### 사전 준비사항

Slack을 통해 알림을 보내려면 먼저 Composer를 통해 Slack 알림 채널을 설치해야 합니다.

    composer require laravel/slack-notification-channel

팀을 위한 [Slack 앱](https://api.slack.com/apps?new_app=1) 도 생성해야 합니다. 앱을 생성한 후 작업 영역에 대해 "Incoming Webhook"을 설정해야 합니다. 그러면 Slack에서 [Slack 알림 라우팅](#routing-slack-notifications) 시 사용할 수 있는 웹훅 URL을 제공합니다.

<a name="formatting-slack-notifications"></a>
### 슬랙 알림 포맷 지정하기

알림이 Slack 메시지로 전송되는 것을 지원하는 경우 알림 클래스에 `toSlack` 메소드를 정의해야 합니다. 이 메소드는 `$notifiable` 모델을 수신하고 `Illuminate\Notifications\Messages\SlackMessage` 인스턴스를 반환해야 합니다. Slack 메시지에는 추가 텍스트 또는 필드 배열의 형식을 지정하는 "첨부 파일"뿐만 아니라 텍스트 콘텐츠가 포함될 수 있습니다. 기본 `toSlack` 예제를 살펴보겠습니다.

    /**
     * Get the Slack representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\SlackMessage
     */
    public function toSlack($notifiable)
    {
        return (new SlackMessage)
                    ->content('One of your invoices has been paid!');
    }드

#### 발신자 & 수신자 설정하기

`from` 과 `to` 메소드를 사용하여 발신자와 수신자를 변경할 수 있습니다. `from` 메소드는 사용자 이름과 이모지 식별자를 인자로 받으며, `to` 메소드는 채널 또는 사용자 이름을 전달받습니다.

    /**
     * Get the Slack representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\SlackMessage
     */
    public function toSlack($notifiable)
    {
        return (new SlackMessage)
                    ->from('Ghost', ':ghost:')
                    ->to('#bots')
                    ->content('This will be sent to #bots');
    }

이모티콘 대신 "로고" 이미지를 사용할 수도 있습니다.

    /**
     * Get the Slack representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\SlackMessage
     */
    public function toSlack($notifiable)
    {
        return (new SlackMessage)
                    ->from('Laravel')
                    ->image('https://laravel.com/img/favicon/favicon.ico')
                    ->content('This will display the Laravel logo next to the message');
    }

<a name="slack-attachments"></a>
### 슬랙 첨부 파일

또한 슬랙 메세지에 "첨부파일"을 추가할 수도 있습니다. 첨부파일은 간단한 텍스트 메세지보다 풍부한 포맷 옵션을 제공합니다. 다음 예제에서, 애플리케이션에서 발생한 예외-exception에 대한 에러 알림에 보다 자세한 정보를 표시하는 링크를 포함하여 전송할 것입니다.

    /**
     * Get the Slack representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return \Illuminate\Notifications\Messages\SlackMessage
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

#### 마크다운에 첨부파일 표시하기

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
### 슬랙 알림 라우팅(수신자설정)

Slack 알림을 적절한 Slack 팀 및 채널로 라우팅하려면 알림 대상 모델에서 `routeNotificationForSlack` 메소드를 정의하세요. 알림이 전달되어야 하는 웹훅 URL을 반환해야 합니다. Webhook URL은 Slack 팀에 "Incoming Webhook" 서비스를 추가하여 생성할 수 있습니다.

    <?php

    namespace App\Models;

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
## 알림의 현지화

라라벨을 사용하면 HTTP 요청의 현재 로케일이 아닌 다른 로케일로 알림을 보낼 수 있으며 알림이 대기 중인 경우에도 이 로케일을 기억합니다.

이를 위해 `Illuminate\Notifications\Notification` 클래스는 원하는 언어를 설정하는 `locale` 메소드를 제공합니다. 알림이 평가될 때 애플리케이션이 이 로케일로 변경되고 평가가 완료되면 이전 로케일로 되돌아갑니다.

    $user->notify((new InvoicePaid($invoice))->locale('es'));

`Notification` 파사드를 통해 여러 알림 가능한 항목을 현지화할 수도 있습니다.

    Notification::locale('es')->send(
        $users, new InvoicePaid($invoice)
    );

### 사용자 선호 언어

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

일단 인터페이스를 구현하면 라라벨은 알림 및 모델을 모델로 전송할 때 자동으로 기본 설정 언어를 사용합니다. 따라서 이 인터페이스를 사용할 때는 `locale` 메소드를 호출 할 필요가 없습니다.

    $user->notify(new InvoicePaid($invoice));

<a name="notification-events"></a>
## 알림 이벤트

#### 알림 전송 이벤트

알림이 전송되면 알림 시스템에서 `Illuminate\Notifications\Events\NotificationSending` [event](/docs/{{version}}/events)을 전달합니다. 여기에는 "notifiable" 모델과 알림 인스턴스 자체가 포함됩니다. 애플리케이션의 `EventServiceProvider`에서 이 이벤트에 대한 리스너를 등록할 수 있습니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Notifications\Events\NotificationSending' => [
            'App\Listeners\CheckNotificationStatus',
        ],
    ];

`NotificationSending` 이벤트에 대한 이벤트 리스너가 `handle` 메소드에서 `false`를 반환하면 알림이 전송되지 않습니다.

    use Illuminate\Notifications\Events\NotificationSending;

    /**
     * Handle the event.
     *
     * @param  \Illuminate\Notifications\Events\NotificationSending  $event
     * @return void
     */
    public function handle(NotificationSending $event)
    {
        return false;
    }

이벤트 리스너 내에서 이벤트의 `notifiable`, `notification` 및 `channel` 속성에 액세스하여 알림 수신자 또는 알림 자체에 대해 자세히 알아볼 수 있습니다.

    /**
     * Handle the event.
     *
     * @param  \Illuminate\Notifications\Events\NotificationSending  $event
     * @return void
     */
    public function handle(NotificationSending $event)
    {
        // $event->channel
        // $event->notifiable
        // $event->notification
    }

#### 알림 전송 이벤트

알림이 전송되면 알림 시스템에서 `Illuminate\Notifications\Events\NotificationSent` [event](/docs/{{version}}/events)를 전달합니다. 여기에는 "notifiable" 모델와 알림 인스턴스 자체가 포함됩니다. `EventServiceProvider`에서 이 이벤트에 대한 리스너를 등록할 수 있습니다.

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

> {tip} `EventServiceProvider` 에서 리스너를 등록하고 난후, `event:generate` 아티즌 명령어를 사용하여 빠르게 리스너 클래스를 생성할 수 있습니다.

이벤트 리스너 안에서 알림의 수신자 또는 알림 그 자체에 대해서 보다 자세한 정보를 얻기 위해서 이벤트의 `notifiable`, `notification`, 그리고 `channel` 속성에 엑세스 할 수 있습니다.

    /**
     * Handle the event.
     *
     * @param  \Illuminate\Notifications\Events\NotificationSent  $event
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
## 사용자정의(커스텀) 채널

라라벨에서는 몇가지 알림 채널을 제공하고 있지만, 다른 채널을 통해서 알림을 전송하기 위해서 고유한 드라이버를 작성하기를 원할 수도 있습니다. 라라벨에서는 이를 손쉽게 할 수 있습니다. 시작하려면 `send` 메소드를 가지고 있는 클래스를 정의합니다. 이 메소드는 `$notifiable` 와 `$notification` 두개의 인자를 전달 받야야 합니다.

`send` 메소드 내에서 알림에 대한 메소드를 호출하여 채널에서 이해하는 메시지 개체를 검색한 다음 원하는 대로 `$notifiable` 인스턴스에 알림을 보낼 수 있습니다.

    <?php

    namespace App\Notifications;

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
