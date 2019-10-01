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
    - [발신자 번호 수정하기](#customizing-the-from-number)
    - [SMS 알림 라우팅](#routing-sms-notifications)
- [슬랙-Slack 알림](#slack-notifications)
    - [사전준비사항](#slack-prerequisites)
    - [Slack 알림 포맷 지정하기](#formatting-slack-notifications)
    - [슬랙 첨부파일](#slack-attachments)
    - [슬랙 알림 라우팅](#routing-slack-notifications)
- [알림의 현지화](#localizing-notifications)
- [알림 이벤트](#notification-events)
- [사용자 정의(커스텀)채널](#custom-channels)

<a name="introduction"></a>
## 시작하기

[이메일 발송](/docs/{{version}}/mail)기능을 지원하는 것에 더해, 라라벨은 이메일을 포함해 SMS([Nexmo](https://www.nexmo.com/)를 통해서 제공), [슬랙](https://slack.com)등  다양한 드라이버 채널을 통해서 알림을 발송하는 기능을 제공합니다.

일반적으로, 알림은 애플리케이션 안에서 발생한 어떤 사항을 사용자에게 전달하기 위한 짧은 정보를 담은 메세지입니다. 예를들어, 결제가 필요한 애플리케이션을 작성중이라면, "결제 완료" 알림을 이메일이나 SMS를 통해서 사용자에게 발송할 수 있습니다.

<a name="creating-notifications"></a>
## 알림 생성하기

라라벨에서 각각의 알림은 하나의 클래스로 표현할 수 있습니다. (일반적으로 `app/Notifications` 디렉토리에 저장된) 애플리케이션에서 이 디렉토리를 볼 수 없더라도 걱정하지 마십시오, `make:notification` 아티즌 명령어를 실행할 때 디렉토리가 생성됩니다.

    php artisan make:notification InvoicePaid

이 명령어는 새로운 알림 클래스를 `app/Notifications`디렉토리에 생성합니다. 각각의 알림 클래스는 `via` 메소드와 특정 채널에 최적화된 메세지로 변환하는 몇가지 메세지를 작성하는 방법(`toMail` 또는 `toDatabase` 와 같은)을 가지고 있습니다.

<a name="sending-notifications"></a>
## 알림 발송하기

<a name="using-the-notifiable-trait"></a>
### Notifiable 트레이트 사용하기

알림은 두가지 방법으로 발송할 수 있습니다. `Notifiable`트레이트의 `notify` 메소드를 사용하거나, `Notification` [파사드](/docs/{{version}}/facades)를 사용하는 것입니다. 먼저 트레이트를 사용하는 경우를 살펴보겠습니다.

    <?php

    namespace App;

    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable
    {
        use Notifiable;
    }

이 트레이트-trait는 기본적으로 `App\User` 모델에서 사용되며 알림을 보내는데 사용할 수 있는 `notify` 메소드를 가지고 있습니다. `notify` 메소드는 알림 인스턴스를 전달 받습니다.

    use App\Notifications\InvoicePaid;

    $user->notify(new InvoicePaid($invoice));

> {tip} `Illuminate\Notifications\Notifiable` 트레이트는 여러분이 가지고 있는 어떤 모델에서도 사용할 수 있다는 것을 기억하십시오. `User` 모델에서만 사용할 수 있도록 제한되어 있지 않습니다.

<a name="using-the-notification-facade"></a>
### Notification 파사드 사용하기

대신에, `Notification` [파사드](/docs/{{version}}/facades)를 사용하여 알림을 발송할 수 있습니다. 이것은 주로 사용자 컬렉션과 같은 여러개의 알림 가능한 엔티티들에 대해서 알림을 발송하는데 유용합니다. 파사드를 사용하여 알림을 보내기 위해서는 알림이 가능한 엔트리들과 알림 인스턴스를 `send` 메소드에 전달하면 됩니다.

    Notification::send($users, new InvoicePaid($invoice));

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

알림을 보내는데는 시간이 걸리는데, 특히 알림을 발송하기 위해서 채널이 외부 API를 호출하는 경우에 더욱 그러합니다. 애플리케이션의 응답 속도를 높이기 위해서는 `ShouldQueue` 인터페이스와 `Queueable` 트레이트를 여러분의 클래스에 추가하여 알림이 큐를 통해서 발송되도록 할 수 있습니다. `make:notification` 을 통해서 생성한 모든 알림 클래스에는 이 인터페이스와 트레이트를 바로 클래스에 추가할 수 있도록 import 되어 있습니다.

    <?php

    namespace App\Notifications;

    use Illuminate\Bus\Queueable;
    use Illuminate\Notifications\Notification;
    use Illuminate\Contracts\Queue\ShouldQueue;

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

<a name="on-demand-notifications"></a>
### 필요시 대상을 지정한 Notifications

때로는 애플리케이션의 "사용자(User)"가 아닌 특정 누군가에게 알림을 보내고자 할 수도 있습니다. `Notification::route` 메소드를 사용하여, 알림이 전달될 누군가의 정보를 지정할 수 있습니다.

    Notification::route('mail', 'taylor@example.com')
                ->route('nexmo', '5555555555')
                ->notify(new InvoicePaid($invoice));

<a name="mail-notifications"></a>
## 이메일을 통한 알림

<a name="formatting-mail-messages"></a>
### 알림 메세지 포맷 지정하기

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

> {tip} `toMail` 메소드 안에서 `$this->invoice->id`를 사용하고 있다는 것을 주의해주십시오. 알림이 메세지를 생성하기 위해서 필요한 그 어떤 데이터라도 알림 클래스의 생성자에 전달할 수 있습니다.

이 예제에서는 인사말, 텍스트 한줄, 액션 호출 그리고 다른 텍스트 몇줄을 등록합니다. `MailMessage` 객체에서 제공되는 이 메소드들은 간단한 이메일을 쉽고 빠르게 포맷팅할 수 있습니다. 메일 채널은 메세지 컴포넌트를 일반 텍스트로 된 멋진 반응형 HTML 이메일 템플릿으로 변환합니다. 다음은 `mail` 채널에 의해서 생성된 이메일의 예제입니다.

<img src="https://laravel.com/img/docs/notification-example.png" width="551" height="596">

> {tip} 메일 알림을 발송할 때에는 `config/app.php` 설정 파일에서 `name` 값을 설정해야합니다. 이 값은 여러분의 이메일 알림 메세지의 헤더와 푸터에 사용됩니다.

#### 기타 알림 포매팅 옵션

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
#### 에러 메세지

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
                    ->from('test@example.com', 'Example')
                    ->line('...');
    }

<a name="customizing-the-recipient"></a>
### 수신자 설정하기

`email` 채널을 통해서 알림을 발송할때, 알림 시스템은 자동으로 알림 엔티티 클래스의 `email` 속성을 찾습니다. 알림을 전송하는데 사용하는 이메일 주소를 커스터마이징 하려면 엔티티에 `routeNotificationForMail` 메소드를 정의하면 됩니다.

    <?php

    namespace App;

    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

    class User extends Authenticatable
    {
        use Notifiable;

        /**
         * Route notifications for the mail channel.
         *
         * @param  \Illuminate\Notifications\Notification  $notification
         * @return string
         */
        public function routeNotificationForMail($notification)
        {
            return $this->email_address;
        }
    }

<a name="customizing-the-subject"></a>
### 제목 설정하기

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
### 템플릿 설정하기

여러분은 알림 패키지의 리소스의 메일 알림에서 사용되는 HTML 과 일반 텍스트를 수정할 수 있습니다. 이 명령어를 사용하고 나면 메일 알림 템플릿이 `resources/views/vendor/notifications` 디렉토리에 저장됩니다.

    php artisan vendor:publish --tag=laravel-notifications

<a name="previewing-mail-notifications"></a>
### 메일 알림 미리보기

메일 알림 템플릿을 디자인 할 때, 이것은 일반적인 블레이드 템플릿처럼 브라우저에서 렌더링 된 메일 메시지를 빠르게 미리보기에 편리합니다. 따라서 Laravel은 라우트나 컨트롤러에서 메일 알림을 통해 생성 된 모든 메일 메시지를 직접 반환 할 수 있습니다. `MailMessage`가 반환되면 브라우저에 렌더링되고 표시되므로 실제 이메일 주소로 보낼 필요없이 디자인을 빠르게 미리 볼 수 있습니다.

    Route::get('mail', function () {
        $invoice = App\Invoice::find(1);

        return (new App\Notifications\InvoicePaid($invoice))
                    ->toMail($invoice->user);
    });

<a name="markdown-mail-notifications"></a>
## 마크다운 이메일 알림

마크다운 메일 알림을 사용하면 사전에 작성된 메일 알림 템플릿을 활용할 수 있으며, 컨텐츠를 더 길게 작성할 수도 있으며, 메세지를 커스터마이징하는데 유용합니다. 마크다운을 통해서 메세지를 작성하게 되면서, 라라벨은 메세지를 보다 원활하게 렌더링 하고, 반응형 HTML 템플릿을 사용하는 동시에 일반 텍스트를 자동으로 생성할 수 있게 되었습니다.

<a name="generating-the-message"></a>
### 메세지 클래스 생성하기

일치하는 마크다운 템플릿으로 알림 클래스를 생성하려면 `make:notification` 아티즌 명령어에 `--markdown` 옵션을 사용하면 됩니다.

    php artisan make:notification InvoicePaid --markdown=mail.invoice.paid

기타 메일 알림과 마찬가지로, 마크다운 템플릿을 사용하는 알림 클래스는 `toMail` 메소드를 정의해야 합니다. 하지만, `line` 과 `action` 메소드를 사용하여 알림을 만드는 대신에, 사용할 마크다운 템플릿 이름을 지정하기 위해서 `markdown` 메서드를 사용합니다.

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

버튼 컴포넌트는 가운데 있는 버튼 링크를 렌더링 합니다. 컴포넌트는 두개의 인자를 전달 받는데, `url` 과 옵셔널하게 `color`를 전달받습니다. 사용가능한 컬러는 `blue`, `green`, 그리고 `red` 입니다. 원하는만큼, 여러개의 버튼 컴포넌트를 알림에 추가할 수 있습니다.

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

`database` 알림 채널은 알림 정보를 데이터베이스 테이블에 저장합니다. 이 테이블은 알림에 대한 정보를 커스텀 JSON 데이터와 같이 알림 타입으로 포함하게 될 것입니다.

애플리케이션의 사용자 인터페이스에 알림을 표시하기 위해서 테이블에 쿼리를 질의할 수 있습니다. 하지만, 이렇게 하기 전에 알림을 저장할 데이터베이스 테이블을 생성할 필요가 있습니다. `notifications:table` 명령어를 사용하여 적절한 테이블 스키마를 구성하는 마이그레이션 파일을 생성할 수 있습니다.

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

`toArray` 메소드는 `broadcast` 채널에서 자바스크립트 클라이언트에 어떤 데이터를 브로드캐스팅할지 결정하는데도 사용됩니다. 만약 여러분이 `database` 와 `broadcast` 채널에서 두개의 다르게 표현되는 배열을 가지고자 한다면 `toArray`  메소드 대신에 `toDatabase` 메소드를 정의해야 합니다.

<a name="accessing-the-notifications"></a>
### 알림에 엑세스하기

데이터베이스에 알림이 저장되고 난 뒤에, 여러분은 알림 엔티티에서 저장된 알림에 편리하게 엑세스 할 수 있는 방법이 필요합니다. 라라벨의 기본 `App\User` 모델에 포함되어 있는 `Illuminate\Notifications\Notifiable` 트레이트는 엔티티의 알림을 반환하는 `notifications` Eloquent 관계를 가지고 있습니다. 알림을 가져오기 위해서 다른 Eloquent 관계와 마찬가지로, 이 메소드에 엑세스 할 수 있습니다. 기본적으로 알림은 `created_at` 타임스탬프를 기준으로 정렬됩니다.

    $user = App\User::find(1);

    foreach ($user->notifications as $notification) {
        echo $notification->type;
    }

"unread"(읽지않은) 알림만 조회하고자 한다면, `unreadNotifications` 메소드를 사용할 수 있습니다. 다시 한번 말하지만, 이 알림들은 `created_at` 타임스탬프에 의해서 정렬됩니다.

    $user = App\User::find(1);

    foreach ($user->unreadNotifications as $notification) {
        echo $notification->type;
    }

> {tip} 자바스크립트 클라이언트에서 알림에 엑세스하려면, 애플리케이션에서 현재 사용자와 같은 알림이 가능한 엔티티에 대한 알림을 반환하는 알림 컨트롤러를 정의해야 합니다. 그런 다음 자바스크립트 클라이언트에서 해당 컨트롤러의 URI에 대한 HTTP 요청을 만들 수 있습니다.

<a name="marking-notifications-as-read"></a>
### 알림을 읽음 표시로 전환하기

일반적으로, 사용자가 알림을 확인했을 때 이를 "읽음"으로 표시하기를 원할 것입니다. `Illuminate\Notifications\Notifiable` 트레이트는 알림 데이터베이스 레코드에서 `read_at` 컬럼을 업데이트 하는 `markAsRead` 메소드를 제공합니다.

    $user = App\User::find(1);

    foreach ($user->unreadNotifications as $notification) {
        $notification->markAsRead();
    }

이경우, 각각의 알림을 반복문 안에서 수정하는 대신에, `markAsRead` 메소드를 알림 컬렉션에 바로 사용할 수도 있습니다.

    $user->unreadNotifications->markAsRead();

또한, 데이터베이스에서 별도로 조회하지 않고서 바로 알림을 읽음 표시 하는데 mass-update(대량 업데이트) 쿼리를 사용할 수도 있습니다.

    $user = App\User::find(1);

    $user->unreadNotifications()->update(['read_at' => now()]);

여러분은 테이블 엔티티에서 알림을 삭제하기 위해서 `delete` 메소드를 사용할 수 있습니다.

    $user->notifications()->delete();

<a name="broadcast-notifications"></a>
## 알림 브로드 캐스팅

<a name="broadcast-prerequisites"></a>
### 사전준비사항

알림을 브로드캐스팅하기 전에, 여러분은 라라벨의 [이벤트 브로드캐스팅](/docs/{{version}}/broadcasting)에 대해서 이해하고 이를 설정해야 합니다. 이벤트 브로드캐스팅은 자바스크립트 클라이언트에서 서버 사이드에서 발생한 라라벨 이벤트에 반응하는 방법을 제공합니다.

<a name="formatting-broadcast-notifications"></a>
### 브로드캐스팅 알림 포맷 지정하기

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

#### 브로드캐스트 Queue-큐 설정하기

모든 브로드캐스트 알림은 큐를 사용합니다. 브로드캐스팅을 수행할 때 사용할 큐 커넥션이나 큐 이름을 지정하려면, `BroadcastMessage` 의 `onConnection` 과 `onQueue` 메소드를 사용하면 됩니다.

    return (new BroadcastMessage($data))
                    ->onConnection('sqs')
                    ->onQueue('broadcasts');

> {tip} 여러분이 지정한 데이터에 더해서, 브로드캐스트 알림은 알림의 클래스 이름을 가지고 있는 `type` 필드를 포함합니다.

<a name="listening-for-notifications"></a>
### 알림 수신하기

비공개 채널에 브로드캐스팅 되는 알림은 `{notifiable}.{id}` 컨벤션에 의해서 포맷이 지정됩니다. 따라서 ID 가 `1`인 `App\User` 인스턴스를 알림으로 보내는 경우 알림은 `App.User.1` 비공개 채널에 브로드캐스팅 됩니다. [라라벨 에코](/docs/{{version}}/broadcasting)를 사용하는 경우, 채널에서 `notification` 헬퍼 메소드를 사용하여 알림을 손쉽게 수신할 수 있습니다.

    Echo.private('App.User.' + userId)
        .notification((notification) => {
            console.log(notification.type);
        });

#### 알림 채널 커스터미이징하기

알림 엔티티가 알림을 수신하는 채널을 커스터마이징하고자 한다면, notificable 엔티티에 `receivesBroadcastNotificationsOn` 메소드를 정의하면 됩니다.

    <?php

    namespace App;

    use Illuminate\Notifications\Notifiable;
    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Foundation\Auth\User as Authenticatable;

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

라라벨에서 SMS 알림 전송은 [Nexmo](https://www.nexmo.com/)를 사용합니다. Nexmo를 통해서 알림을 전송할 수 있도록, 먼저 `laravel/nexmo-notification-channel` 컴포저 패키지를 설치해야 합니다.

    composer require laravel/nexmo-notification-channel

그 다음, `config/services.php` 설정 파일에 몇가지 옵션들을 추가해야합니다. 이 옵션은 다음의 예제와 같은 모양입니다. 복사해서 사용하십시오:

    'nexmo' => [
        'key' => env('NEXMO_KEY'),
        'secret' => env('NEXMO_SECRET'),
        'sms_from' => '15556666666',
    ],

`sms_from` 옵션은 SMS 메세지가 전송되는 전화번호 입니다. Nexmo 설정 패널에서 애플리케이션의 전화번호를 생성해야 합니다.

<a name="formatting-sms-notifications"></a>
### SMS 알림 포맷 지정하기

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

#### 유니코드 컨텐츠

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
### "발신자" 번호 지정하기

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
### SMS 알림 라우팅(수신자 지정)

Nexmo 알림을 올바른 전화 번호로 전송하려면 알림 가능한 엔터티에 `routeNotificationForNexmo` 메소드를 정의하십시오.

    <?php

    namespace App;

    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

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

슬랙을 통해서 알림을 보내기 전에, 컴포저를 통해서 슬랙 알림 채널 패키지를 설치해야 합니다.

    composer require laravel/slack-notification-channel

슬랙 팀에서 ["Incoming Webhook"](https://api.slack.com/incoming-webhooks) intergration을 설정해야합니다. 이 intergration은 [슬랙 알림을 전송](#routing-slack-notifications)할 때 사용하는 하나의 URL을 제공해줍니다.

<a name="formatting-slack-notifications"></a>
### 슬랙 알림 포맷 지정하기

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

이 예제에서는 슬랙에 하나의 텍스트 라인을 전송하였고, 다음과 같은 메세지가 생성됩니다.

<img src="https://laravel.com/img/docs/basic-slack-notification.png">

#### 발신자 & 수신자 설정하기

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
                    ->image('https://laravel.com/favicon.png')
                    ->content('This will display the Laravel logo next to the message');
    }

<a name="slack-attachments"></a>
### 슬랙 첨부파일

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

이 예제는 다음과 같이 보여지는 슬랙 메세지를 생성할 것입니다.

<img src="https://laravel.com/img/docs/basic-slack-attachment.png">

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

이 예제는 다음과 같이 보여지는 슬랙 메세지를 생성할 것입니다.

<img src="https://laravel.com/img/docs/slack-fields-attachment.png">

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

슬랙 알림이 원하는 위치로 전송되게 하기 위해서 알림 엔티티에 `routeNotificationForSlack` 메소드를 정의할 수 있습니다. 이 메소드는 어느 알림이 전송될 webhook URL을 반환해야합니다. Webhook URL은 슬랙 팀 설정의 "Incoming Webhook" 서비스에서 생성할 수 있습니다.

    <?php

    namespace App;

    use Illuminate\Notifications\Notifiable;
    use Illuminate\Foundation\Auth\User as Authenticatable;

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

Laravel을 사용하면 현재 언어가 아닌 언어로도 알림을 보낼 수 있으며 알림이 대기중인 경우에도 이 언어을 기억할 수 있습니다.

이를 위해 `Illuminate\Notifications\Mailable` 클래스는 원하는 언어를 지정하는 `locale` 메소드를 제공합니다. 알림의 양식이 만들어 질 때 애플리케이션이 이 언어로 변경되고 양식의 생성이 완료되면 이전 언어로 되돌립니다.

    $user->notify((new InvoicePaid($invoice))->locale('es'));

알림이 가능한 항목들의 현지화를 `알림(Notification)` 파사드로 처리 할 수도 있습니다.

    Notification::locale('es')->send($users, new InvoicePaid($invoice));

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

일단 인터페이스를 구현하면 Laravel은 알림 및 모델을 모델로 전송할 때 자동으로 기본 설정 언어를 사용합니다. 따라서 이 인터페이스를 사용할 때는 `locale` 메소드를 호출 할 필요가 없습니다 :

    $user->notify(new InvoicePaid($invoice));

<a name="notification-events"></a>
## 알림 이벤트

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

> {tip} `EventServiceProvider` 에서 리스너를 등록하고 난후, `event:generate` 아티즌 명령어를 사용하여 빠르게 리스너 클래스를 생성할 수 있습니다.

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
## 사용자 정의(커스텀) 채널

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

알림 채널 클래스를 정의하고 나면, 알림의 `via` 메소드에서 클래스 이름을 반환하면 됩니다.

    <?php

    namespace App\Notifications;

    use Illuminate\Bus\Queueable;
    use App\Channels\VoiceChannel;
    use App\Channels\Messages\VoiceMessage;
    use Illuminate\Notifications\Notification;
    use Illuminate\Contracts\Queue\ShouldQueue;

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
