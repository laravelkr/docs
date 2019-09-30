# Mail
# 메일

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Driver Prerequisites](#driver-prerequisites)
    - [드라이버 사전 준비사항](#driver-prerequisites)
- [Generating Mailables](#generating-mailables)
- [Mailables 생성하기](#generating-mailables)
- [Writing Mailables](#writing-mailables)
- [Mailables 작성하기](#writing-mailables)
    - [Configuring The Sender](#configuring-the-sender)
    - [발송자 설정하기](#configuring-the-sender)
    - [Configuring The View](#configuring-the-view)
    - [View-뷰 설정하기](#configuring-the-view)
    - [View Data](#view-data)
    - [뷰 데이터](#view-data)
    - [Attachments](#attachments)
    - [첨부 파일](#attachments)
    - [Inline Attachments](#inline-attachments)
    - [인라인 첨부](#inline-attachments)
    - [Customizing The SwiftMailer Message](#customizing-the-swiftmailer-message)
    - [SwiftMailer 메세지 커스터미아징하기](#customizing-the-swiftmailer-message)
- [Markdown Mailables](#markdown-mailables)
- [마크다운 Mailables](#markdown-mailables)
    - [Generating Markdown Mailables](#generating-markdown-mailables)
    - [마크다운 Mailable 생성하기](#generating-markdown-mailables)
    - [Writing Markdown Messages](#writing-markdown-messages)
    - [마크다운으로 메세지 작성하기](#writing-markdown-messages)
    - [Customizing The Components](#customizing-the-components)
    - [컴포넌트 커스터마이징](#customizing-the-components)
- [Previewing Mailables In The Browser](#previewing-mailables-in-the-browser)
- [브라우저에서 Mailables 미리보기](#previewing-mailables-in-the-browser)
- [Sending Mail](#sending-mail)
- [메일 발송](#sending-mail)
    - [Queueing Mail](#queueing-mail)
    - [큐를 통한 메일 처리](#queueing-mail)
- [Rendering Mailables](#rendering-mailables)
- [Mailables 객체 렌더링](#rendering-mailables)
    - [Previewing Mailables In The Browser](#previewing-mailables-in-the-browser)
    - [브라우저에서 Mailable 객체 미리보기](#previewing-mailables-in-the-browser)
- [Localizing Mailables](#localizing-mailables)
- [Mailables 현지화](#localizing-mailables)
- [Mail & Local Development](#mail-and-local-development)
- [메일 & 로컬 개발환경](#mail-and-local-development)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel provides a clean, simple API over the popular [SwiftMailer](https://swiftmailer.symfony.com/) library with drivers for SMTP, Mailgun, Postmark, Amazon SES, and `sendmail`, allowing you to quickly get started sending mail through a local or cloud based service of your choice.

라라벨은 인기있는 [SwiftMailer](https://swiftmailer.symfony.com/)를 통해서 깔끔하고 단순한 API 를 제공하며, 로컬과 클라우드 기반의 메일 서비스를 통해서 어렵지 않게 메일을 사용할 수 있도록 SMTP, Mailgun, Postmark, 아마존 SES, 그리고 `sendmail` 드라이버를 제공합니다.

<a name="driver-prerequisites"></a>
### Driver Prerequisites
### 드라이버 사전준비사항

The API based drivers such as Mailgun and Postmark are often simpler and faster than SMTP servers. If possible, you should use one of these drivers. All of the API drivers require the Guzzle HTTP library, which may be installed via the Composer package manager:

API를 기반으로한 Mailgun 그리고 Postmark 드라이버의 경우 대개 SMTP 서버 보다 빠르고 간편합니다. 가능하다면, 이 드라이버들 중 하나를 사용하길 바랍니다. 모든 API 드라이버는 컴포저 패키지 매니저를 통해서 설치할 수 있는 Guzzle HTTP 라이브러리를 필요로 합니다.

    composer require guzzlehttp/guzzle

#### Mailgun Driver
#### Mailgun 드라이버

To use the Mailgun driver, first install Guzzle, then set the `driver` option in your `config/mail.php` configuration file to `mailgun`. Next, verify that your `config/services.php` configuration file contains the following options:

Mailgun 드라이버를 사용하려면 먼저 Guzzle 을 설치하고, `config/mail.php` 설정파일에 `driver` 옵션을 `mailgun`으로 설정하면 됩니다. 다음으로 `config/services.php` 설정 파일이 다음 내용을 포함하고 있는지 확인하십시오:

    'mailgun' => [
        'domain' => 'your-mailgun-domain',
        'secret' => 'your-mailgun-key',
    ],

If you are not using the "US" [Mailgun region](https://documentation.mailgun.com/en/latest/api-intro.html#mailgun-regions), you may define your region's endpoint in the `services` configuration file:

"US"[Mailgun region](https://documentation.mailgun.com/en/latest/api-intro.html#mailgun-regions)을 사용하지 않는 경우 지역(region)의 엔드포인트를 `services` 설정 파일에 정의 할 수 있습니다 :

    'mailgun' => [
        'domain' => 'your-mailgun-domain',
        'secret' => 'your-mailgun-key',
        'endpoint' => 'api.eu.mailgun.net',
    ],

#### Postmark Driver
#### Postmark 드라이버

To use the Postmark driver, install Postmark's SwiftMailer transport via Composer:

Postmark 드라이버를 사용하려면, 컴포저를 사용해서 Postmark 의 SwiftMailer transport 를 설치해야 합니다.

    composer require wildbit/swiftmailer-postmark

Next, install Guzzle and set the `driver` option in your `config/mail.php` configuration file to `postmark`. Finally, verify that your `config/services.php` configuration file contains the following options:

다음으로, Guzzle을 설치하고, `config/mail.php` 설정 파일에 `driver` 옵션을 `postmark`으로 설정하면 됩니다. 마지막으로, `config/services.php` 설정 파일이 다음 내용을 포함하고 있는지 확인하십시오:

    'postmark' => [
        'token' => 'your-postmark-token',
    ],

#### SES Driver
#### SES 드라이버

To use the Amazon SES driver you must first install the Amazon AWS SDK for PHP. You may install this library by adding the following line to your `composer.json` file's `require` section and running the `composer update` command:

아마존 SES 드라이버를 사용하려면 먼저 반드시 아마존 AWS SDK for PHP 를 설치해야 합니다. `composer.json` 파일의 `require` 부분에 다음 라인을 추가하고 `composer update` 명령어를 실행하여 설치할 수 있습니다.

    "aws/aws-sdk-php": "~3.0"

Next, set the `driver` option in your `config/mail.php` configuration file to `ses` and verify that your `config/services.php` configuration file contains the following options:

다음으로 `config/mail.php` 설정 파일의 `driver` 옵션을 `ses` 로 설정하고 `config/services.php` 설정 파일이 다음과 같은 옵션을 포함하고 있는지 확인하십시오:

    'ses' => [
        'key' => 'your-ses-key',
        'secret' => 'your-ses-secret',
        'region' => 'ses-region',  // e.g. us-east-1
    ],

If you need to include [additional options](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-email-2010-12-01.html#sendrawemail) when executing the SES `SendRawEmail` request, you may define an `options` array within your `ses` configuration:

SES의 `SendRawEmail` 요청을 실행할 때 [추가 옵션](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-email-2010-12-01.html#sendrawemail)을 추가해야한다면 `ses` 설정 내에서 `options` 배열에 정의 할 수 있습니다 :

    'ses' => [
        'key' => 'your-ses-key',
        'secret' => 'your-ses-secret',
        'region' => 'ses-region',  // e.g. us-east-1
        'options' => [
            'ConfigurationSetName' => 'MyConfigurationSet',
            'Tags' => [
                [
                    'Name' => 'foo',
                    'Value' => 'bar',
                ],
            ],
        ],
    ],

<a name="generating-mailables"></a>
## Generating Mailables
## Mailables 생성하기

In Laravel, each type of email sent by your application is represented as a "mailable" class. These classes are stored in the `app/Mail` directory. Don't worry if you don't see this directory in your application, since it will be generated for you when you create your first mailable class using the `make:mail` command:

라라벨에서, 애플리케이션에 의해서 발송되는 이메일의 각 타입들은 "mailable" 클래스로 표현할 수 있습니다. 이 클래스들은 `app/Mail` 디렉토리안에 들어 있습니다. 애플리케이션에서 이 디렉토리가 보이지 않더라도 걱정하지 마십시오. `make:mail` 명령어를 사용하여 첫번째 mailable 클래스를 생성하면 자동으로 생성됩니다.

    php artisan make:mail OrderShipped

<a name="writing-mailables"></a>
## Writing Mailables
## Mailables 작성하기

All of a mailable class' configuration is done in the `build` method. Within this method, you may call various methods such as `from`, `subject`, `view`, and `attach` to configure the email's presentation and delivery.

모든 mailable 클래스의 설정은 `build` 메소드에 들어 있습니다. 이 메소드 안에서 여러분은 `from`, `subject`, `view` 그리고 `attach` 와 같은, 이메일의 형태와 발송에 대해서 설정할 수 있는 다양한 메소드를 사용할 수 있습니다.

<a name="configuring-the-sender"></a>
### Configuring The Sender
### 발송자 설정하기

#### Using The `from` Method
#### `from` 메소드 사용하기

First, let's explore configuring the sender of the email. Or, in other words, who the email is going to be "from". There are two ways to configure the sender. First, you may use the `from` method within your mailable class' `build` method:

먼저 이메일의 발송자 설정을 살펴보겠습니다. 또는 다른 말로 누구로 부터 이메일이 전달되는지에 대해서 말입니다. 발송자를 설정하는 방법에는 두가지가 있습니다. 먼저 mailable 클래스의 `build` 메소드 안에서 `from` 메소드를 사용하는 것입니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->from('example@example.com')
                    ->view('emails.orders.shipped');
    }

#### Using A Global `from` Address
#### 글로벌 `from` 메일 주소 사용하기

However, if your application uses the same "from" address for all of its emails, it can become cumbersome to call the `from` method in each mailable class you generate. Instead, you may specify a global "from" address in your `config/mail.php` configuration file. This address will be used if no other "from" address is specified within the mailable class:

하지만 애플리케이션이 이메일에서 사용하는 모든 "발송자" 주소가 동일하다면, 생성한 각각의 mailable 클래스 에서 매번 `from` 메소드를 호출하는 것은 번거로운 일입니다. 대신에, 글로벌 "발송자" 주소를 `config/mail.php` 설정 파일에서 지정할 수 있습니다. mailable 클래스 안에서 다른 "from" 주소를 지정하지 않는다면 이 글로벌 주소가 사용될 것입니다.

    'from' => ['address' => 'example@example.com', 'name' => 'App Name'],

In addition, you may define a global "reply_to" address within your `config/mail.php` configuration file:

또한 `config/mail.php` 설정 파일 내에서 글로벌 "reply_to"주소를 설정 할 수 있습니다 :

    'reply_to' => ['address' => 'example@example.com', 'name' => 'App Name'],

<a name="configuring-the-view"></a>
### Configuring The View
### View-뷰 파일 설정하기

Within a mailable class' `build` method, you may use the `view` method to specify which template should be used when rendering the email's contents. Since each email typically uses a [Blade template](/docs/{{version}}/blade) to render its contents, you have the full power and convenience of the Blade templating engine when building your email's HTML:

mailable 클래스의 `build` 메소드 안에서 이메일 컨텐츠를 렌더링 할때 사용해야 하는 템플릿을 지정하기 위해서 `view` 메소드를 사용할 수 있습니다. 각각의 이메일은 컨텐츠를 렌더링 하기 위해서 일반적으로 [블레이드 템플릿](/docs/{{version}}/blade)을 사용하기 때문에, 이메일의 HTML을 구성하는데 블레이드 템플릿 엔진의 강력하고 편리한 기능을 사용할 수 있습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->view('emails.orders.shipped');
    }

> {tip} You may wish to create a `resources/views/emails` directory to house all of your email templates; however, you are free to place them wherever you wish within your `resources/views` directory.

> {tip} 모든 이메일 템플릿을 모아놓기 위한 `resources/views/emails` 디렉토리를 만들기를 원할 수도 있습니다; 하지만, 실제로는 `resources/views` 디렉토리 안에 어디에 구성하더라도 상관없습니다.

#### Plain Text Emails
#### 텍스트 전용 이메일

If you would like to define a plain-text version of your email, you may use the `text` method. Like the `view` method, the `text` method accepts a template name which will be used to render the contents of the email. You are free to define both a HTML and plain-text version of your message:

이메일을 순수 텍스트 버전으로 정의하고자 한다면, `text` 메소드를 사용하면 됩니다. `view` 메소드와 같이 `text` 메소드는 이메일의 컨텐츠를 렌더링하는데 사용하게될 템플릿의 이름을 인자로 전달받습니다. 이메일 메세지를 HTML 과 순수 텍스트 버전 어느것으로도 정의할 수 있습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->view('emails.orders.shipped')
                    ->text('emails.orders.shipped_plain');
    }

<a name="view-data"></a>
### View Data
### 뷰 데이터

#### Via Public Properties
#### public 속성을 사용하여

Typically, you will want to pass some data to your view that you can utilize when rendering the email's HTML. There are two ways you may make data available to your view. First, any public property defined on your mailable class will automatically be made available to the view. So, for example, you may pass data into your mailable class' constructor and set that data to public properties defined on the class:

일반적으로 이메일의 HTML을 렌더링 할 때, 뷰에서 구성할 수 있는 데이터를 전달하기를 원할 것입니다. 여기에는 뷰에 데이터를 전달하는 두 가지 방법이 있습니다. 먼저, mailable 클래스에 정의되어 있는 public 속성은 자동으로 뷰에서 사용할 수 있습니다. 따라서, 예를들어 데이터를 mailable 클래스의 생성자에 전달하고 이 데이터를 클래스에 정의되어 있는 public 속성에 지정할 수 있습니다.

    <?php

    namespace App\Mail;

    use App\Order;
    use Illuminate\Bus\Queueable;
    use Illuminate\Mail\Mailable;
    use Illuminate\Queue\SerializesModels;

    class OrderShipped extends Mailable
    {
        use Queueable, SerializesModels;

        /**
         * The order instance.
         *
         * @var Order
         */
        public $order;

        /**
         * Create a new message instance.
         *
         * @return void
         */
        public function __construct(Order $order)
        {
            $this->order = $order;
        }

        /**
         * Build the message.
         *
         * @return $this
         */
        public function build()
        {
            return $this->view('emails.orders.shipped');
        }
    }

Once the data has been set to a public property, it will automatically be available in your view, so you may access it like you would access any other data in your Blade templates:

데이터가 public 속성에 지정되고 나면, 뷰에서 자동적으로 이를 사용할 수 있습니다. 따라서 블레이트 템플릿안에서 다른 데이터에 엑세스하는 것처럼 데이터에 접근할 수 있습니다.

    <div>
        Price: {{ $order->price }}
    </div>

#### Via The `with` Method:
#### `with` 메소드를 사용하여:

If you would like to customize the format of your email's data before it is sent to the template, you may manually pass your data to the view via the `with` method. Typically, you will still pass data via the mailable class' constructor; however, you should set this data to `protected` or `private` properties so the data is not automatically made available to the template. Then, when calling the `with` method, pass an array of data that you wish to make available to the template:

만약 여러분이 이메일 데이터의 유형이 템플릿에 전달되기 전에 수정을 가하고 싶다면, `with` 메소드를 사용하여 수동으로 데이터를 뷰에 전달할 수 있습니다. 일반적으로, 이경우에도 여전히 데이터가 mailable 클래스의 생성자에 전달 될것입니다; 하지만 템플릿에서 자동으로 사용가능하지 않도록, 이 데이터를 `protected` 나 `private` 속성에 지정해야 합니다. 이제 템플릿에서 사용하고자 하는 데이터의 배열을 인자로 `with` 메소드를 호출 하십시오.

    <?php

    namespace App\Mail;

    use App\Order;
    use Illuminate\Bus\Queueable;
    use Illuminate\Mail\Mailable;
    use Illuminate\Queue\SerializesModels;

    class OrderShipped extends Mailable
    {
        use Queueable, SerializesModels;

        /**
         * The order instance.
         *
         * @var Order
         */
        protected $order;

        /**
         * Create a new message instance.
         *
         * @return void
         */
        public function __construct(Order $order)
        {
            $this->order = $order;
        }

        /**
         * Build the message.
         *
         * @return $this
         */
        public function build()
        {
            return $this->view('emails.orders.shipped')
                        ->with([
                            'orderName' => $this->order->name,
                            'orderPrice' => $this->order->price,
                        ]);
        }
    }

Once the data has been passed to the `with` method, it will automatically be available in your view, so you may access it like you would access any other data in your Blade templates:

`with` 메소드에 데이터가 전달되면, 이는 자동적으로 뷰에서 사용이 가능합니다. 따라서 블레이트 템플릿안에서 다른 데이터에 엑세스하는 것처럼 데이터에 접근할 수 있습니다.

    <div>
        Price: {{ $orderPrice }}
    </div>

<a name="attachments"></a>
### Attachments
### 첨부 파일

To add attachments to an email, use the `attach` method within the mailable class' `build` method. The `attach` method accepts the full path to the file as its first argument:

이메일에 파일을 첨부하려면, mailable 클래스의 `build` 메소드 안에서 `attach` 메소드를 사용하면 됩니다. `attach` 메소드는 파일의 전체 패스(full path)를 첫번째 인자로 전달 받습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->view('emails.orders.shipped')
                    ->attach('/path/to/file');
    }

When attaching files to a message, you may also specify the display name and / or MIME type by passing an `array` as the second argument to the `attach` method:

이메일에 파일이 첨부 될 때, `attach` 메소드의 두번째 인자로 첨부 파일의 표시되는 이름과 MIME 타입을 지정할 수 있는 `배열`을 지정할 수도 있습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->view('emails.orders.shipped')
                    ->attach('/path/to/file', [
                        'as' => 'name.pdf',
                        'mime' => 'application/pdf',
                    ]);
    }

#### Attaching Files from Disk
#### 디스크에 있는 파일 첨부하기

If you have stored a file on one of your [filesystem disks](/docs/{{version}}/filesystem), you may attach it to the email using the `attachFromStorage` method:

[filesystem disks](/docs/{{version}}/filesystem) 중 하나에 파일을 저장했다면, `attachFromStorage` 메소드를 사용하여 이메일에 첨부 할 수 있습니다 :

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
       return $this->view('email.orders.shipped')
                   ->attachFromStorage('/path/to/file');
    }

If necessary, you may specify the file's attachment name and additional options using the second and third arguments to the `attachFromStorage` method:

필요하다면 `attachFromStorage` 메소드의 두번째와 세번째 인자를 사용하여 파일의 첨부 파일 이름과 추가 옵션을 지정할 수 있습니다 :

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
       return $this->view('email.orders.shipped')
                   ->attachFromStorage('/path/to/file', 'name.pdf', [
                       'mime' => 'application/pdf'
                   ]);
    }

The `attachFromStorageDisk` method may be used if you need to specify a storage disk other than your default disk:

`attachFromStorageDisk` 메소드는 기본 디스크가 아닌 다른 스토리지 디스크를 지정해야 할 때 사용할 수 있습니다 :

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
       return $this->view('email.orders.shipped')
                   ->attachFromStorageDisk('s3', '/path/to/file');
    }

#### Raw Data Attachments
#### Raw 데이터 첨부하기

The `attachData` method may be used to attach a raw string of bytes as an attachment. For example, you might use this method if you have generated a PDF in memory and want to attach it to the email without writing it to disk. The `attachData` method accepts the raw data bytes as its first argument, the name of the file as its second argument, and an array of options as its third argument:

`attachData` 메소드는 raw string 의 바이트를 첨부하는데 사용됩니다. 예를 들어 메모리에서 PDF 파일을 생성하고 이 파일을 디스크에 저장하지 않고 바로 메일에 첨부하는 경우에 사용할 수 있습니다. `attachData` 메소드는 첫번째 인자로 raw 데이터 바이트를, 두번째 인자로 파일의 이름을, 그리고 세번째 인자로 옵션배열을 전달 받습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->view('emails.orders.shipped')
                    ->attachData($this->pdf, 'name.pdf', [
                        'mime' => 'application/pdf',
                    ]);
    }

<a name="inline-attachments"></a>
### Inline Attachments
### 인라인 첨부

Embedding inline images into your emails is typically cumbersome; however, Laravel provides a convenient way to attach images to your emails and retrieving the appropriate CID. To embed an inline image, use the `embed` method on the `$message` variable within your email template. Laravel automatically makes the `$message` variable available to all of your email templates, so you don't need to worry about passing it in manually:

이메일에 인라인 이미지를 포함시키는 것은 번거로운 일입니다. 하지만 라라벨에서는 이메일에 이미지를 첨부하고 최적의 CID를 얻을 수 있는 편리한 방법을 제공합니다. 인라인 이미지를 포함시키기 위해서는 이메일 뷰 안에서 `$message` 메소드에 `embed` 메소드를 사용하면 됩니다. 라라벨은 자동으로 모든 이메일 뷰 안에서 `$message` 변수를 사용할 수 있도록 만들기 때문에, 이를 수동으로 전달하는 것을 걱정할 필요가 없습니다.

    <body>
        Here is an image:

        <img src="{{ $message->embed($pathToImage) }}">
    </body>

> {note} `$message` variable is not available in plain-text messages since plain-text messages do not utilize inline attachments.

> {note} 플레인-텍스트 메세지는 인파인 첨부파일이 동작하지 않으므로, 플레인-텍스트 메세지에서는 `$message` 변수를 사용할 수 없습니다.

#### Embedding Raw Data Attachments
#### Raw 데이터를 첨부하는 방법

If you already have a raw data string you wish to embed into an email template, you may use the `embedData` method on the `$message` variable:

이미 이메일 템플릿에 포함시키고자 하는 raw 데이터 문자열을 가지고 있다면, `$message` 변수에서 `embedData` 메소드를 사용하면 됩니다.

    <body>
        Here is an image from raw data:

        <img src="{{ $message->embedData($data, $name) }}">
    </body>

<a name="customizing-the-swiftmailer-message"></a>
### Customizing The SwiftMailer Message
### SwiftMailer 메세지 커스터마이징 하기

The `withSwiftMessage` method of the `Mailable` base class allows you to register a callback which will be invoked with the raw SwiftMailer message instance before sending the message. This gives you an opportunity to customize the message before it is delivered:

`Mailable` 기본 클래스의 `withSwiftMessage` 메소드를 사용하면 메세지를 보내기 전에, Raw SwiftMailer 인스턴스를 인자로 호출할 콜백을 등록할 수 있습니다. 이렇게 되면 메세지를 보내기 전에 커스터마이징 할 수 있습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        $this->view('emails.orders.shipped');

        $this->withSwiftMessage(function ($message) {
            $message->getHeaders()
                    ->addTextHeader('Custom-Header', 'HeaderValue');
        });
    }

<a name="markdown-mailables"></a>
## Markdown Mailables
## 마크다운 Mailables

Markdown mailable messages allow you to take advantage of the pre-built templates and components of mail notifications in your mailables. Since the messages are written in Markdown, Laravel is able to render beautiful, responsive HTML templates for the messages while also automatically generating a plain-text counterpart.

마크다운 mailable 메세지 기능을 사용하면 미리 템플릿과 메일 컴포넌트를 활용할 수 있는 장점이 있습니다. 마크다운을 통해서 메세지를 작성하게 되면서, 라라벨은 메세지를 보다 원활하게 렌더링 하고, 반응형 HTML 템플릿을 사용하는 동시에 일반 텍스트를 자동으로 생성할 수 있게 되었습니다.

<a name="generating-markdown-mailables"></a>
### Generating Markdown Mailables
### 마크다운 Mailables 생성하기

To generate a mailable with a corresponding Markdown template, you may use the `--markdown` option of the `make:mail` Artisan command:

일치하는 마크다운 템플릿으로 mailable 클래스를 생성하려면 `make:mail` 아티즌 명령어에 `--markdown` 옵션을 사용하면 됩니다.

    php artisan make:mail OrderShipped --markdown=emails.orders.shipped

Then, when configuring the mailable within its `build` method, call the `markdown` method instead of the `view` method. The `markdown` method accepts the name of the Markdown template and an optional array of data to make available to the template:

그런다음, `build` 메소드 안에서 mailable을 설정할 때 `view` 메소드 대신에 `markdown` 메소드를 호출합니다. `markdown` 메소드는 마크다운 템플릿의 이름과 선택적으로 사용할 수 있는 데이터 배열을 인자로 받습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        return $this->from('example@example.com')
                    ->markdown('emails.orders.shipped');
    }

<a name="writing-markdown-messages"></a>
### Writing Markdown Messages
### 마크다운으로 메세지 작성하기

Markdown mailables use a combination of Blade components and Markdown syntax which allow you to easily construct mail messages while leveraging Laravel's pre-crafted components:

마크다운 mailable 은 블레이드 컴포넌트와 마크다운 문법을 조합하여 라라벨이 사전에 구성해둔 컴포넌트를 활용하면서 손쉽게 메일을 생성할 수 있게 해줍니다.

    @component('mail::message')
    # Order Shipped

    Your order has been shipped!

    @component('mail::button', ['url' => $url])
    View Order
    @endcomponent

    Thanks,<br>
    {{ config('app.name') }}
    @endcomponent

> {tip} Do not use excess indentation when writing Markdown emails. Markdown parsers will render indented content as code blocks.

> {tip} 마크다운으로 이메일을 작성할 때, 너무 많은 들여 쓰기를 하지마십시오. 마크다운 파서는 들여 쓰기 된 내용을 코드 블록으로 렌더링합니다.

#### Button Component
#### 버튼 컴포넌트


The button component renders a centered button link. The component accepts two arguments, a `url` and an optional `color`. Supported colors are `primary`, `success`, and `error`. You may add as many button components to a message as you wish:

버튼 컴포넌트는 가운데 있는 버튼 링크를 렌더링 합니다. 컴포넌트는 두개의 인자를 전달 받는데, `url` 과 옵셔널하게 `color`를 전달받습니다. 사용가능한 컬러는 `primary`, `success`, 그리고 `error` 입니다. 원하는 대로, 여러개의 버튼 컴포넌트를 추가할 수 있습니다.

    @component('mail::button', ['url' => $url, 'color' => 'success'])
    View Order
    @endcomponent

#### Panel Component
#### 패널 컴포넌트

The panel component renders the given block of text in a panel that has a slightly different background color than the rest of the message. This allows you to draw attention to a given block of text:

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

You may export all of the Markdown mail components to your own application for customization. To export the components, use the `vendor:publish` Artisan command to publish the `laravel-mail` asset tag:

애플리케이션에서 사용하는 모든 마크다운 메일 컴포넌트는 커스터마이징이 가능합니다. 먼저 컴포넌트를 내보내기 위해서 `vendor:publish` 아티즌 명령어를 사용하여 `laravel-mail` 애셋 태그를 지정합니다.

    php artisan vendor:publish --tag=laravel-mail

This command will publish the Markdown mail components to the `resources/views/vendor/mail` directory. The `mail` directory will contain a `html` and a `text` directory, each containing their respective representations of every available component. You are free to customize these components however you like.

이 명령어는 마크다운 메일 컴포넌트를 `resources/views/vendor/mail` 디렉토리에 퍼블리싱 합니다. `mail` 디렉토리는 `html` 과 `text` 디렉토리를 가지고 있는데, 각각은 사용가능한 컴포넌트의 표현들이 들어 있습니다. 원한다면 이 컴포넌트를 커스터마이징 할 수 있습니다.

#### Customizing The CSS
#### CSS 커스터마이징

After exporting the components, the `resources/views/vendor/mail/html/themes` directory will contain a `default.css` file. You may customize the CSS in this file and your styles will automatically be in-lined within the HTML representations of your Markdown mail messages.

컴포넌트를 내보내기(export) 한 이후에, `resources/views/vendor/mail/html/themes` 디렉토리를 보면 `default.css` 파일을 확인할 수 있습니다. 이 파일에서 CSS를 커스터마이징 할 수 있으며, 마크다운 메일 메세지의 HTML 표현에서 스타일이 자동으로 적용됩니다.

> {tip} If you would like to build an entirely new theme for the Markdown components, write a new CSS file within the `html/themes` directory and change the `theme` option of your `mail` configuration file.

> {tip} 완전히 새로운 마크다운 컴포넌트 테마를 생성하려면 `html/themes` 디렉토리에 새로운 CSS 파일을 작성하고, `mail` 설정 파일의 `theme` 옵션을 변경하면 됩니다.

<a name="previewing-mailables-in-the-browser"></a>
## Previewing Mailables In The Browser
## 브라우저에서 Mailables 미리보기

When designing a mailable's template, it is convenient to quickly preview the rendered mailable in your browser like a typical Blade template. For this reason, Laravel allows you to return any mailable directly from a route Closure or controller. When a mailable is returned, it will be rendered and displayed in the browser, allowing you to quickly preview its design without needing to send it to an actual email address:

mailable의 템플릿을 구성할 때, 일반적인 블레이드 템플릿과 같이 브라우저에서 렌더링된 mailable 결과를 미리볼 수 있는 것은 빠르고 편리한 방법입니다. 이런 이유로, 라라벨은 라우트 클로저나 컨트롤러에서 mailable 을 바로 반환할 수 있게 허용하고 있습니다. mailable 이 반환되면, 브라우저에서 보여 질 수 있도록 렌더링 되며, 실제 이메일이 어떤 형태로 보여지게 될지 빠르게 확인할 수 있습니다.

    Route::get('/mailable', function () {
        $invoice = App\Invoice::find(1);

        return new App\Mail\InvoicePaid($invoice);
    });

<a name="sending-mail"></a>
## Sending Mail
## 메일 발송하기

To send a message, use the `to` method on the `Mail` [facade](/docs/{{version}}/facades). The `to` method accepts an email address, a user instance, or a collection of users. If you pass an object or collection of objects, the mailer will automatically use their `email` and `name` properties when setting the email recipients, so make sure these attributes are available on your objects. Once you have specified your recipients, you may pass an instance of your mailable class to the `send` method:

메일을 발송하기 위해서는 `Mail` [파사드](/docs/{{version}}/facades)에서 `to` 메소드를 사용하면 됩니다. `to` 메소드는 이메일 주소, 하나의 사용자 인스턴스, 그리고 사용자들의 컬렉션을 전달 받습니다. 하나의 객체나 객체들의 컬렉션이 전달된다면 메일러는 자동으로 이것들의 `email` 과 `name` 속성을 이메일 수신자로 설정할 것입니다. 따라서 객체에서 이 속성들이 사용가능한지 확인해야 합니다. 수신자들을 지정하고 나면, mailable 클래스의 인스턴스를 `send` 메소드에 전달합니다.

    <?php

    namespace App\Http\Controllers;

    use App\Order;
    use App\Mail\OrderShipped;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Mail;
    use App\Http\Controllers\Controller;

    class OrderController extends Controller
    {
        /**
         * Ship the given order.
         *
         * @param  Request  $request
         * @param  int  $orderId
         * @return Response
         */
        public function ship(Request $request, $orderId)
        {
            $order = Order::findOrFail($orderId);

            // Ship order...

            Mail::to($request->user())->send(new OrderShipped($order));
        }
    }

You are not limited to just specifying the "to" recipients when sending a message. You are free to set "to", "cc", and "bcc" recipients all within a single, chained method call:

메일을 보낼 때 "to"에서 수신자를 지정하는데 제한이 있지는 않습니다. "to", "cc", "bcc" 를 사용한 수신자 설정을 하나의 체이닝된 호출로 사용할 수도 있습니다.

    Mail::to($request->user())
        ->cc($moreUsers)
        ->bcc($evenMoreUsers)
        ->send(new OrderShipped($order));

<a name="rendering-mailables"></a>
## Rendering Mailables
## Mailable 객체 렌더링하기

Sometimes you may wish to capture the HTML content of a mailable without sending it. To accomplish this, you may call the `render` method of the mailable. This method will return the evaluated contents of the mailable as a string:

mailable 객체의 실제 HTML 내용을 메일로 보내지 않고서도 확인해야될 때가 있습니다. 이를 위해서는 mailable 객체에서 `render` 메소드를 호출하면 됩니다. 이 메소드는 mailable 객체의 내용을 연산하여 문자형태로 반환합니다.

    $invoice = App\Invoice::find(1);

    return (new App\Mail\InvoicePaid($invoice))->render();

<a name="previewing-mailables-in-the-browser"></a>
### Previewing Mailables In The Browser
### 브라우저에서 Mailable 객체 미리보기

When designing a mailable's template, it is convenient to quickly preview the rendered mailable in your browser like a typical Blade template. For this reason, Laravel allows you to return any mailable directly from a route Closure or controller. When a mailable is returned, it will be rendered and displayed in the browser, allowing you to quickly preview its design without needing to send it to an actual email address:

mailable 객체의 템플릿을 구성할 때 일반적인 블레이드 템플릿과 같이 브라우저에서 렌더링된 mailable 객체의 내용을 확인하는 것이 편합니다. 이러한 이유로 라라벨에서는 라우트 클로저나 컨트롤러에서 mailable 객체를 바로 반환할 수 있습니다. mailable 객체가 반환되면, 브라우저에서 바로 확인할 수 있도록 렌더링되어 실제 이메일을 보내지 않고도 내용을 미리 볼 수 있습니다.

    Route::get('mailable', function () {
        $invoice = App\Invoice::find(1);

        return new App\Mail\InvoicePaid($invoice);
    });

<a name="queueing-mail"></a>
### Queueing Mail
### 메일을 큐를 통해서 보내기

#### Queueing A Mail Message
#### 이메일을 큐로 보내기

Since sending email messages can drastically lengthen the response time of your application, many developers choose to queue email messages for background sending. Laravel makes this easy using its built-in [unified queue API](/docs/{{version}}/queues). To queue a mail message, use the `queue` method on the `Mail` facade after specifying the message's recipients:

메일을 송신하는 것은 애플리케이션의 응답 시간을 크게 저하시키기 때문에 많은 개발자들은 이메일 메세지를 백그라운드 에서 보낼 수 있도록 큐-대기행열에 넣어 처리하도록 합니다. 라라벨에서는 내장되어 있는 [일관된 큐 API](/docs/{{version}}/queues)를 통해서 이러한 작업을 손쉽게 수행할 수 있게 합니다. 이메일 메세지를 대기 큐에 넣기 위해서는 메세지의 수신자를 지정한 다음에, `Mail` 파사드의 `queue` 메소드를 호출하도록 하면 됩니다.

    Mail::to($request->user())
        ->cc($moreUsers)
        ->bcc($evenMoreUsers)
        ->queue(new OrderShipped($order));

This method will automatically take care of pushing a job onto the queue so the message is sent in the background. You will need to [configure your queues](/docs/{{version}}/queues) before using this feature.

이 메소드는 자동으로 큐에 작업을 추가하여 메세지가 백그라운드에서 보내지도록 할것입니다. 이 기능을 사용하기 위해서는 [큐 설정하기](/docs/{{version}}/queues)를 확인하셔야 합니다.

#### Delayed Message Queueing
#### 큐에서 메일을 지연시켜서 보내기

If you wish to delay the delivery of a queued email message, you may use the `later` method. As its first argument, the `later` method accepts a `DateTime` instance indicating when the message should be sent:

큐를 통해서 이메일을 보낼 때 시간을 지연시켜서 보내고자 한다면, `later` 메소드를 사용하면 됩니다. `later` 메소드의 첫번째 인자로 몇초동안 지연시킬 것인지 나타내는 `DateTime` 인스턴스를 전달 받습니다.

    $when = now()->addMinutes(10);

    Mail::to($request->user())
        ->cc($moreUsers)
        ->bcc($evenMoreUsers)
        ->later($when, new OrderShipped($order));

#### Pushing To Specific Queues
#### 지정된 큐로 작업 보내기

Since all mailable classes generated using the `make:mail` command make use of the `Illuminate\Bus\Queueable` trait, you may call the `onQueue` and `onConnection` methods on any mailable class instance, allowing you to specify the connection and queue name for the message:

`make:mail` 명령어를 사용하여 생성된 모든 mailable 클래스들은 `Illuminate\Bus\Queueable` 트레이트를 사용하기 때문에 어떤 mailable 클래스 인스턴스라도 `onQueue` 와 `onConnection` 메소드를 호출할 수 있습니다. 이렇게 하면 메세지를 보내는 커넥션과 큐 이름을 지정할 수 있습니다.

    $message = (new OrderShipped($order))
                    ->onConnection('sqs')
                    ->onQueue('emails');

    Mail::to($request->user())
        ->cc($moreUsers)
        ->bcc($evenMoreUsers)
        ->queue($message);

#### Queueing By Default
#### 기본을 큐로 발송하도록 설정하기

If you have mailable classes that you want to always be queued, you may implement the `ShouldQueue` contract on the class. Now, even if you call the `send` method when mailing, the mailable will still be queued since it implements the contract:

mailable 클래스가 항상 큐를 통해서 처리되도록 하려면, 클래스에 `ShouldQueue` implement 를 추가하면 됩니다. 그러면 `send` 메소드가 호출되어 메일이 발송될 때 contract에 의해서 큐를 통해서 메일이 발송됩니다.

    use Illuminate\Contracts\Queue\ShouldQueue;

    class OrderShipped extends Mailable implements ShouldQueue
    {
        //
    }

<a name="localizing-mailables"></a>
## Localizing Mailables
## Mailables 현지화

Laravel allows you to send mailables in a locale other than the current language, and will even remember this locale if the mail is queued.

Laravel을 사용하면 현재 언어가 아닌 다른 언어로도 메일을 보낼 수 있으며 메일이 대기중인 경우에도 이 언어를 기억할 수 있습니다.

To accomplish this, the `Mail` facade offers a `locale` method to set the desired language. The application will change into this locale when the mailable is being formatted and then revert back to the previous locale when formatting is complete:

이를 위해 `Mail` 파사드는 원하는 언어를 설정하는 `locale` 메소드를 제공합니다. mailable의 양식이 만들어 질 때 애플리케이션은 이 언어로 변경되고 포맷이 완료되면 이전 언어로 되돌립니다.

    Mail::to($request->user())->locale('es')->send(
        new OrderShipped($order)
    );

### User Preferred Locales
### 사용자 선호 언어

Sometimes, applications store each user's preferred locale. By implementing the `HasLocalePreference` contract on one or more of your models, you may instruct Laravel to use this stored locale when sending mail:

때로는 애플리케이션의 각 사용자 마다 기본 언어설정이 저장됩니다. 하나 이상의 모델에 `HasLocalePreference` contract을 구현하면 Laravel이 메일을 보낼 때 이 저장된 언어를 사용하게 할 수 있습니다.

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

Once you have implemented the interface, Laravel will automatically use the preferred locale when sending mailables and notifications to the model. Therefore, there is no need to call the `locale` method when using this interface:

일단 인터페이스를 구현하면 Laravel은 메일 및 알림을 모델로 전송할 때 자동으로 기본 설정 언어를 사용합니다. 따라서 이 인터페이스를 사용할 때는 `locale` 메소드를 호출 할 필요가 없습니다 :

    Mail::to($request->user())->send(new OrderShipped($order));

<a name="mail-and-local-development"></a>
## Mail & Local Development
## 메일 & 로컬 개발환경

When developing an application that sends email, you probably don't want to actually send emails to live email addresses. Laravel provides several ways to "disable" the actual sending of emails during local development.

이메일을 송신하는 애플리케이션을 개발 중이라면 실제로 이메일이 보내지기를 원하지는 않으실겁니다. 라라벨은 로컬 개발환경에서 이메일을 보내는 것은 "비활성화" 시킬 수 있는 몇가지 방법을 제공합니다.

#### Log Driver
#### 로그 드라이버

Instead of sending your emails, the `log` mail driver will write all email messages to your log files for inspection. For more information on configuring your application per environment, check out the [configuration documentation](/docs/{{version}}/configuration#environment-configuration).

이메일을 발송하는 대신, `log` 메일 드라이버는 모든 이메일 메세지를 확인하기 위해서 로그파일에 기록합니다. 구동 환경별 애플리케이션을 설정하는 보다 자세한 정보는 [설정 매뉴얼](/docs/{{version}}/configuration#environment-configuration)을 확인하십시오.

#### Universal To
#### 모든 메일의 수신자 고정하기

Another solution provided by Laravel is to set a universal recipient of all emails sent by the framework. This way, all the emails generated by your application will be sent to a specific address, instead of the address actually specified when sending the message. This can be done via the `to` option in your `config/mail.php` configuration file:

두번째 해결책은 프레임워크에서 보내는 모든 이메일의 수신자를 고정 시켜버리는 방법입니다. 이렇게 하면 애플리케이션에서 보내는 이메일들이 각자의 수신자에게 보내지는 대신에, 모두 하나의 지정된 주소로만 보내 질 것입니다. 이렇게 하려면 `config/mail.php` 설정 파일에서 `to` 옵션을 지정하면 됩니다.

    'to' => [
        'address' => 'example@example.com',
        'name' => 'Example'
    ],

#### Mailtrap
#### Mailtrap

Finally, you may use a service like [Mailtrap](https://mailtrap.io) and the `smtp` driver to send your email messages to a "dummy" mailbox where you may view them in a true email client. This approach has the benefit of allowing you to actually inspect the final emails in Mailtrap's message viewer.

마지막으로 [Mailtrap](https://mailtrap.io)과 같은 서비스를 사용하여 `smtp` 드라이버에서 실제 이메일 클라이언트에서 내용을 확인할 수 있는 "더미"의 수신함으로 메일을 보내는 방법입니다. 이 방법의 장점은 최종적으로 보내지는 메일을 실제로 확인할 수 있다는 점입니다.

<a name="events"></a>
## Events
## 이벤트

Laravel fires two events during the process of sending mail messages. The `MessageSending` event is fired prior to a message being sent, while the `MessageSent` event is fired after a message has been sent. Remember, these events are fired when the mail is being *sent*, not when it is queued. You may register an event listener for this event in your `EventServiceProvider`:

라라벨은 이메일을 보내는 동안 두개의 이벤트를 발생시킵니다. `MessageSending` 이벤트는 메세지를 보내기 전에 발생하고, `MessageSent` 이벤트는 메세지를 보낸 후에 발생합니다. 주의할 점은 이 이벤트들은 메일이 큐를 통하지 않고 바로 *보내질* 때 발생한다는 것입니다. 여러분은 `EventServiceProvider` 에서 이 이벤트들을 위한 이벤트 리스너를 등록할 수 있습니다.

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        'Illuminate\Mail\Events\MessageSending' => [
            'App\Listeners\LogSendingMessage',
        ],
        'Illuminate\Mail\Events\MessageSent' => [
            'App\Listeners\LogSentMessage',
        ],
    ];
