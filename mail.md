# 메일

- [시작하기](#introduction)
- [메일 보내기](#sending-mail)
    - [첨부 파일](#attachments)
    - [인라인 첨부](#inline-attachments)
    - [메일을 큐를 통해서 보내기](#queueing-mail)
- [로컬 개발에서의 메일](#mail-and-local-development)
- [이벤트](#events)

<a name="introduction"></a>
## 시작하기

라라벨은 인기있는 [SwiftMailer](http://swiftmailer.org)를 통해서 깔끔하고 단순한 API 를 제공합니다. 라라벨은 로컬과 클라우드 기반의 메일 서비스를 통해서 어렵지 않게 메일을 사용할 수 있도록 SMTP, Mailgun, Mandrill, 아마존 SES, PHP 내장 `mail` 함수 그리고 `sendmail` 드라이버를 제공합니다.

### Driver 사전 준비사항

API를 기반으로한 Mailgun 과 Mandrill 드라이버의 경우 대게 SMTP 서버 보다 빠르고 간편합니다. 이 두 API 드라이버를 사용하기 위해서는 애플리케이션에 Guzzle HTTP 라이브러리를 설치해야 합니다. `composer.json` 파일에 다음 라인을 추가하여 Guzzle 을 설치할 수 있습니다.

    "guzzlehttp/guzzle": "~5.3|~6.0"

#### Mailgun 드라이버

Mailgun 드라이버를 사용하려면 `config/mail.php` 설정파일에 `driver` 옵션을 `mailgun`으로 설정하면 됩니다. 다음으로 `config/services.php` 설정 파일이 다음 내용을 포함하고 있는지 확인하십시오.

    'mailgun' => [
        'domain' => 'your-mailgun-domain',
        'secret' => 'your-mailgun-key',
    ],

#### Mandrill 드라이버

Mandrill 드라이버를 사용하려면 `config/mail.php` 설정파일에 `driver` 옵션을 `mandrill`으로 설정하면 됩니다. 다음으로 `config/services.php` 설정 파일이 다음 내용을 포함하고 있는지 확인하십시오.

    'mandrill' => [
        'secret' => 'your-mandrill-key',
    ],

#### SES 드라이버

아마존 SES 드라이버를 사용하려면, 아마존 AWS SDK for PHP 를 설치해야 합니다. `composer.json` 파일의 `require` 부분에 다음 라인을 추가하여 설치할 수 있습니다.

    "aws/aws-sdk-php": "~3.0"

다음으로 `config/mail.php` 설정 파일의 `driver` 옵션을 `ses`로 설정하십시오. 그리고 `config/services.php` 설정 파일에 다음 내용을 포함하고 있는지 확인하십시오.

    'ses' => [
        'key' => 'your-ses-key',
        'secret' => 'your-ses-secret',
        'region' => 'ses-region',  // e.g. us-east-1
    ],

<a name="sending-mail"></a>
## 메일 보내기

라라벨에서는 이메일 메세지를 [views](/docs/{{version}}/views) 형태로 저장해 놓을 수 있습니다. 예를 들어 이메일을 구성하기 위해서 여러분은 `resources/views` 디렉토리 안에 `emails` 디렉토리를 생성할 수 있습니다.

메일을 보내기 위해서는 `Mail` [파사드](/docs/{{version}}/facades)에서 `send` 메소드를 사용하면 됩니다. `send` 메소드는 3개의 인자를 전달 받습니다. 첫번째는
이메일의 내용을 포함하고 있는 [view](/docs/{{version}}/views)의 이름을 의미합니다. 두번째는 전달된 view 에서 사용될 데이터들의 배열입니다. 마지막으로 `Closure` 콜백은 이메일 메세지 인스턴스를 전달받아, 이메일 수신자, 제목 및 다양한 이메일 메세지에 대한 옵션을 지정하는데 사용됩니다.

    <?php

    namespace App\Http\Controllers;

    use Mail;
    use App\User;
    use Illuminate\Http\Request;
    use App\Http\Controllers\Controller;

    class UserController extends Controller
    {
        /**
         * Send an e-mail reminder to the user.
         *
         * @param  Request  $request
         * @param  int  $id
         * @return Response
         */
        public function sendEmailReminder(Request $request, $id)
        {
            $user = User::findOrFail($id);

            Mail::send('emails.reminder', ['user' => $user], function ($m) use ($user) {
                $m->from('hello@app.com', 'Your Application');

                $m->to($user->email, $user->name)->subject('Your Reminder!');
            });
        }
    }

예제에서 `user` 키에 포함하는 배열을 전달하였기 때문에, 다음과 같은 PHP 코드를 통해서 이메일 뷰 파일안에서 사용자의 이름을 표시할 수 있습니다.

    <?php echo $user->name; ?>

> **참고:** `$message`라는 이름의 변수가 항상 이메일 뷰에 전달되고, 인라인 첨부를 사용가능하게 합니다. 따라서 `message` 라는 이름의 변수는 메일 뷰에서 사용하지 말아야 합니다.

#### 메세지 구성하기

앞서 이야기 한 것처럼 `send` 메소드의 세번째 인자로 주어진 `Closure` 는 이메일 메세지에 대한 다양한 옵션을 지정할 수 있습니다. 이 클로저를 통해서 메세지의 다른 속성들, 예를 들어 CC와 BCC등의 속성을 지정할 수도 있습니다.

    Mail::send('emails.welcome', $data, function ($message) {
        $message->from('us@example.com', 'Laravel');

        $message->to('foo@example.com')->cc('bar@example.com');
    });

다음은 `$message` 메세지 빌더 인스턴스에서 사용가능한 메소드들의 목록입니다:

    $message->from($address, $name = null);
    $message->sender($address, $name = null);
    $message->to($address, $name = null);
    $message->cc($address, $name = null);
    $message->bcc($address, $name = null);
    $message->replyTo($address, $name = null);
    $message->subject($subject);
    $message->priority($level);
    $message->attach($pathToFile, array $options = []);

    // Attach a file from a raw $data string...
    $message->attachData($data, $name, array $options = []);

    // Get the underlying SwiftMailer message instance...
    $message->getSwiftMessage();

> **참고:** `Mail::send` 클로저로 전달되는 메세지 인스턴스는 SwiftMailer 메세지 클래스를 상속받고 있기 때문에, 메일 메세지를 구성하는데 필요한 어떤 메소드라도 호출이 가능합니다.

#### 일반 텍스트 메일 보내기

기본적으로, `send` 메소드에 주어진 뷰는 HTML로 구성된다고 가정됩니다. 그러나, `send` 메소드의 첫번째 인자를 배열로 구성하면 HTML 뷰에 추가적으로 일반 텍스트 뷰를 지정할 수도 있습니다.

    Mail::send(['html.view', 'text.view'], $data, $callback);

또는 일반 텍스트 형태로만 메일을 보낼 필요가 있다면, 배열에 `text` 키만 지정하면 됩니다.

    Mail::send(['text' => 'view'], $data, $callback);

#### Raw 문자열 메일 보내기

raw 문자열을 그대로 메일로 보내려면 `raw` 메소드를 사용하면 됩니다.

    Mail::raw('Text to e-mail', function ($message) {
        //
    });

<a name="attachments"></a>
### 첨부 파일

이메일에 파일을 첨부하려면 클로저에서 전달받는 `$message` 객체에 `attach` 메소드를 사용하면 됩니다. `attach` 메소드는 파일의 전체 패스(full path)를 첫번째 인자로 받습니다.

    Mail::send('emails.welcome', $data, function ($message) {
        //

        $message->attach($pathToFile);
    });

이메일에 파일이 첨부 될 때, `attach` 메소드의 두번째 인자로 첨부 파일의 표시되는 이름과 MIME 타입을 지정할 수 있는 `배열`을 지정할 수도 있습니다.

    $message->attach($pathToFile, ['as' => $display, 'mime' => $mime]);

<a name="inline-attachments"></a>
### 인라인 첨부

#### 이메일 뷰 안에서 이미지 포함시키기

이메일에 인라인 이미지를 포함시키는 것은 번거로운 일입니다. 하지만 라라벨에서는 이메일에 이미지를 첨부하고 최적의 CID를 얻을 수 있는 편리한 방법을 제공합니다. 인라인 이미지를 포함시키기 위해서는 이메일 뷰 안에서 `$message` 메소드에 `embed` 메소드를 사용하면 됩니다. 라라벨은 자동적으로 모든 이메일 뷰 안에서 `$message` 변수를 사용할 수 있도록 할 것이라는 점을 유념하십시오.

    <body>
        Here is an image:

        <img src="<?php echo $message->embed($pathToFile); ?>">
    </body>

이메일 뷰에서 Raw 데이터를 첨부하는 방법

이미 이메일 메세지에 포함시키고자 하는 raw 데이터 문자열을 가지고 있다면, `$message` 변수에서 `embedData` 메소드를 사용하면 됩니다.

    <body>
        Here is an image from raw data:

        <img src="<?php echo $message->embedData($data, $name); ?>">
    </body>

<a name="queueing-mail"></a>
### 메일을 큐를 통해서 보내기

#### 이메일을 큐로 보내기

메일을 송신하는 것은 애플리케이션의 응답 시간을 크게 저하시키기 때문에 많은 개발자들은 이메일 메세지를 백그라운드 에서 보낼 수 있도록 큐-대기행열에 넣어 처리하도록 합니다. 라라벨에서는 내장되어 있는 [일관된 큐 API](/docs/{{version}}/queues)를 통해서 이러한 작업을 손쉽게 수행할 수 있게 합니다. 이메일 메세지를 대기 큐에 넣기 위해서는 `Mail` 파사드의 `queue` 메소드를 호출하도록 하면 됩니다.

    Mail::queue('emails.welcome', $data, function ($message) {
        //
    });

이 메소드는 자동으로 큐에 작업을 추가하여 이메일이 백그라운드에서 보내지도록 할것입니다. 이 기능을 사용하기 위해서는 [큐 설정하기](/docs/{{version}}/queues)를 확인하셔야 합니다.

#### 큐에서 메일을 지연시켜서 보내기

큐를 통해서 이메일을 보낼 때 시간을 지연시켜서 보내고자 한다면, `later` 메소드를 사용하면 됩니다. 시간 지연을 위해서는 메소드의 첫번째 인자로 몇초동안 지연시킬 것인지 나타내는 숫자를 전달하면 됩니다.

    Mail::later(5, 'emails.welcome', $data, function ($message) {
        //
    });

#### 지정된 큐로 작업 보내기

지정된 큐에서 메세지를 처리하도록 하고자 한다면, `queueOn` 과 `laterOn` 메소드를 사용하면 됩니다.

    Mail::queueOn('queue-name', 'emails.welcome', $data, function ($message) {
        //
    });

    Mail::laterOn('queue-name', 5, 'emails.welcome', $data, function ($message) {
        //
    });

<a name="mail-and-local-development"></a>
## 로컬 개발에서의 메일

이메일을 송신하는 애플리케이션을 개발 중이라면 실제로 이메일이 보내지기를 원하지는 않으실겁니다. 라라벨은 이메일을 보내는 것은 "비활성화" 시킬 수 있는 몇가지 방법을 제공합니다.

#### 로그 드라이버

첫번째 해결책은 로컬 개발동안에 `log` 메일 드라이버를 사용하는 것입니다. 이 드라이버는 모든 이메일 메세지를 로그 파일에다가 기록할 것입니다. 환경별 애플리케이션의 보다 자세한 설정에 대한 사항은 [설정 문서](/docs/{{version}}/installation#environment-configuration)를 참고하시기 바랍니다.

#### 모든 메일의 수신자 고정하기

두번째 해결책은 프레임워크에서 보내는 모든 이메일의 수신자를 고정 시켜버리는 방법입니다. 이렇게 하면 애플리케이션에서 보내는 이메일들이 각자의 수신자에게 보내지는 대신에, 모두 하나의 지정된 주소로만 보내 질 것입니다. 이렇게 하려면 `config/mail.php` 설정 파일에서 `to` 옵션을 지정하면 됩니다.

    'to' => [
        'address' => 'dev@domain.com',
        'name' => 'Dev Example'
    ],

#### Mailtrap

마지막으로 [Mailtrap](https://mailtrap.io)과 같은 서비스를 사용하여 `smtp` 드라이버에서 실제 이메일 클라이언트에서 내용을 확인할 수 있는 "더미"의 수신함으로 메일을 보내는 방법입니다. 이 방법의 장점은 최종적으로 보내지는 메일을 실제로 확인할 수 있다는 점입니다.

<a name="events"></a>
## 이벤트

라라벨은 이메일을 보내기 전에 `mailer.sending` 이벤트를 발생시킵니다. 주의할 점은 이벤트는 이메일이 큐를 통하지 않고 바로 *보내질* 때 발생한다는 것입니다. 여러분은 `EventServiceProvider` 에서 이벤트 리스너를 등록할 수 있습니다:

    /**
     * Register any other events for your application.
     *
     * @param  \Illuminate\Contracts\Events\Dispatcher  $events
     * @return void
     */
    public function boot(DispatcherContract $events)
    {
        parent::boot($events);

        $events->listen('mailer.sending', function ($message) {
            //
        });
    }

