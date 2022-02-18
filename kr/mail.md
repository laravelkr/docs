# Mail
# 메일

- [Introduction](#introduction)
- [시작하기](#introduction)
    - [Configuration](#configuration)
    - [설정하기](#configuration)
    - [Driver Prerequisites](#driver-prerequisites)
    - [드라이버 사전 준비사항](#driver-prerequisites)
    - [Failover Configuration](#failover-configuration)
    - [장애 조치 설정하기](#failover-configuration)
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
- [Testing Mailables](#testing-mailables)
- [Mailables 테스팅](#testing-mailables)
- [Mail & Local Development](#mail-and-local-development)
- [메일 & 로컬 개발환경](#mail-and-local-development)
- [Events](#events)
- [이벤트](#events)

<a name="introduction"></a>
## Introduction

Sending email doesn't have to be complicated. Laravel provides a clean, simple email API powered by the popular [SwiftMailer](https://swiftmailer.symfony.com/) library. Laravel and SwiftMailer provide drivers for sending email via SMTP, Mailgun, Postmark, Amazon SES, and `sendmail`, allowing you to quickly get started sending mail through a local or cloud based service of your choice.

이메일을 보내는 것은 복잡할 필요가 없습니다. 라라벨은 인기 있는 [SwiftMailer](https://swiftmailer.symfony.com/) 라이브러리를 기반으로 하는 깨끗하고 간단한 이메일 API를 제공합니다. 라라벨 및 SwiftMailer는 SMTP, Mailgun, Postmark, Amazon SES 및 `sendmail`을 통해 이메일을 전송하기 위한 드라이버를 제공하므로 선택한 로컬 또는 클라우드 기반 서비스를 통해 메일 전송을 빠르게 시작할 수 있습니다.

<a name="configuration"></a>
### Configuration
### 설정하기

Laravel's email services may be configured via your application's `config/mail.php` configuration file. Each mailer configured within this file may have its own unique configuration and even its own unique "transport", allowing your application to use different email services to send certain email messages. For example, your application might use Postmark to send transactional emails while using Amazon SES to send bulk emails.

라라벨의 이메일 서비스는 애플리케이션의 `config/mail.php` 파일을 통해 설정할 수 있습니다. 이 파일에 구성된 각 메일러에는 고유한 설정과 고유한 "전송(transport)"이 있을 수 있으므로 어플리케이션에서 다른 이메일 서비스를 사용하여 특정 메일을 보낼 수 있습니다. 예를 들어 애플리케이션은 Postmark 를 사용하여 트랜잭션 이메일을 보내는 반면 Amazon SES를 사용하여 대량 이메일을 보낼 수 있습니다.

Within your `mail` configuration file, you will find a `mailers` configuration array. This array contains a sample configuration entry for each of the major mail drivers / transports supported by Laravel, while the `default` configuration value determines which mailer will be used by default when your application needs to send an email message.

`mail` 설정 파일 내에서 `mailers` 설정 배열을 찾을 수 있습니다. 이 배열에는 라라벨이 지원하는 각 주요 메일 드라이버 전송에 대한 샘플 설정 항목이 포함되어 있으며, `default` 설정 값은 애플리케이션이 이메일 메시지를 보내야 할 때 기본적으로 사용할 메일러를 결정합니다.

<a name="driver-prerequisites"></a>
### Driver / Transport Prerequisites
### 드라이버 / 트렌스포트 사전준비사항

The API based drivers such as Mailgun and Postmark are often simpler and faster than sending mail via SMTP servers. Whenever possible, we recommend that you use one of these drivers. All of the API based drivers require the Guzzle HTTP library, which may be installed via the Composer package manager:

API를 기반으로한 Mailgun 및 Postmark 드라이버의 경우 SMTP 서버 보다 빠르고 간편합니다. 가능하다면, 이러한 드라이버 중 하나를 사용하는 것이 좋습니다. 모든 API 기반 드라이버에는 컴포저 패키지 관리자를 통해 설치할 수 있는 Guzzle HTTP 라이브러리가 필요합니다.

    composer require guzzlehttp/guzzle

<a name="mailgun-driver"></a>
#### Mailgun Driver
#### Mailgun 드라이버


To use the Mailgun driver, first install the Guzzle HTTP library. Then, set the `default` option in your `config/mail.php` configuration file to `mailgun`. Next, verify that your `config/services.php` configuration file contains the following options:

Mailgun 드라이버를 사용하려면 먼저 Guzzle HTTP 라이브러리를 설치하시고, 그런 다음 `config/mail.php` 설정파일에 `default` 옵션을 `mailgun`으로 설정하면 됩니다. 다음으로 `config/services.php` 설정 파일에 다음 옵션이 포함되어 있는지 확인하십시오.

    'mailgun' => [
        'domain' => env('MAILGUN_DOMAIN'),
        'secret' => env('MAILGUN_SECRET'),
    ],

If you are not using the United States [Mailgun region](https://documentation.mailgun.com/en/latest/api-intro.html#mailgun-regions), you may define your region's endpoint in the `services` configuration file:

United States [Mailgun 지역](https://documentation.mailgun.com/en/latest/api-intro.html#mailgun-regions) 을 사용하지 않는 경우 지역(region)의 엔드포인트를 `services` 설정 파일에 정의 할 수 있습니다.

    'mailgun' => [
        'domain' => env('MAILGUN_DOMAIN'),
        'secret' => env('MAILGUN_SECRET'),
        'endpoint' => env('MAILGUN_ENDPOINT', 'api.eu.mailgun.net'),
    ],

<a name="postmark-driver"></a>
#### Postmark Driver
#### Postmark 드라이버

To use the Postmark driver, install Postmark's SwiftMailer transport via Composer:

Postmark 드라이버를 사용하려면, 컴포저를 사용해서 Postmark 의 SwiftMailer transport 를 설치해야 합니다.

    composer require wildbit/swiftmailer-postmark

Next, install the Guzzle HTTP library and set the `default` option in your `config/mail.php` configuration file to `postmark`. Finally, verify that your `config/services.php` configuration file contains the following options:

다음으로, Guzzle HTTP 라이브러리를 설치하고 `config/mail.php` 설정 파일의 `default` 옵션을 `postmark`로 설정합니다. 마지막으로 `config/services.php` 설정 파일에 다음 옵션이 포함되어 있는지 확인합니다.

    'postmark' => [
        'token' => env('POSTMARK_TOKEN'),
    ],

If you would like to specify the Postmark message stream that should be used by a given mailer, you may add the `message_stream_id` configuration option to the mailer's configuration array. This configuration array can be found in your application's `config/mail.php` configuration file:

지정된 메일러에서 사용할 Postmark 메시지 스트림을 지정하려면 `message_stream_id` 구성 옵션을 메일러의 구성 배열에 추가할 수 있습니다. 이 구성 배열은 애플리케이션의 `config/mail.php` 에서 찾을 수 있습니다.

    'postmark' => [
        'transport' => 'postmark',
        'message_stream_id' => env('POSTMARK_MESSAGE_STREAM_ID'),
    ],

This way you are also able to set up multiple Postmark mailers with different message streams.

이 방법으로 서로 다른 메시지 스트림을 가진 여러 개의 Postmark 메일러를 설정할 수 있습니다.

<a name="ses-driver"></a>
#### SES Driver
#### SES 드라이버

To use the Amazon SES driver you must first install the Amazon AWS SDK for PHP. You may install this library via the Composer package manager:

아마존 SES 드라이버를 사용하려면 먼저 PHP용 Amazon AWS SDK를 설치해야 합니다. 컴포저 패키지 관리자를 통해 라이브러리를 설치할 수 있습니다.

```bash
composer require aws/aws-sdk-php
```

Next, set the `default` option in your `config/mail.php` configuration file to `ses` and verify that your `config/services.php` configuration file contains the following options:

다음으로 `config/mail.php` 설정 파일의 `default` 옵션을 `ses` 로 설정하고 `config/services.php` 설정 파일이 다음과 같은 옵션을 포함하고 있는지 확인하십시오.

    'ses' => [
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION', 'us-east-1'),
    ],

To utilize AWS [temporary credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) via a session token, you may add a `token` key to your application's SES configuration:

세션 토큰을 통해 AWS [임시 자격 증명](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) 을 활용하려면 애플리케이션의 SES 설정파일에 `token` 키를 추가할 수 있습니다.

    'ses' => [
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION', 'us-east-1'),
        'token' => env('AWS_SESSION_TOKEN'),
    ],

If you would like to define [additional options](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-email-2010-12-01.html#sendrawemail) that Laravel should pass to the AWS SDK's `SendRawEmail` method when sending an email, you may define an `options` array within your `ses` configuration:

[추가 옵션](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-email-2010-12-01.html#sendrawemail) 을 정의하고 싶다면 라라벨이 AWS SDK의 `SendRawEmail` 메소드를 보낼 때 전달해야 합니다. 이메일의 경우 `ses` 설정파일 내에서 `options` 배열을 정의할 수 있습니다.

    'ses' => [
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION', 'us-east-1'),
        'options' => [
            'ConfigurationSetName' => 'MyConfigurationSet',
            'Tags' => [
                ['Name' => 'foo', 'Value' => 'bar'],
            ],
        ],
    ],

<a name="failover-configuration"></a>
### Failover Configuration
### 장애 조치 설정하기

Sometimes, an external service you have configured to send your application's mail may be down. In these cases, it can be useful to define one or more backup mail delivery configurations that will be used in case your primary delivery driver is down.

경우에 따라 어플리케이션의 메일을 발송하도록 설정한 외부 서비스가 다운될 수 있습니다. 이러한 경우 기본 딜리버리 드라이버가 다운될 경우 사용할 하나 이상의 백업 메일 딜리버리 설정파일을 정의하는 것이 유용할 수 있습니다.

To accomplish this, you should define a mailer within your application's `mail` configuration file that uses the `failover` transport. The configuration array for your application's `failover` mailer should contain an array of `mailers` that reference the order in which mail drivers should be chosen for delivery:

이렇게 하려면 `failover` 전송을 사용하는 mailer를 애플리케이션의 `mail` 구성 파일 내에서 mailer를 정의해야 합니다. 애플리케이션의 `failover` mailer에 대한 구성 배열에는 딜리버리를 위해 메일 드라이버를 선택해야 하는 순서를 참조하는 `mailers` 배열을 포함해야 합니다.

    'mailers' => [
        'failover' => [
            'transport' => 'failover',
            'mailers' => [
                'postmark',
                'mailgun',
                'sendmail',
            ],
        ],

        // ...
    ],

Once your failover mailer has been defined, you should set this mailer as the default mailer used by your application by specifying its name as the value of the `default` configuration key within your application's `mail` configuration file:

장애 조치(failover) 메일러를 정의한 후에는 이 메일러의 이름을 애플리케이션의 `mail` 구성 파일에서 `default` 구성 키의 값으로 이름을 지정하여 이 메일러를 애플리케이션에서 사용하는 기본 메일러로 설정해야 합니다.

    'default' => env('MAIL_MAILER', 'failover'),

<a name="generating-mailables"></a>
## Generating Mailables
## Mailables 생성하기

When building Laravel applications, each type of email sent by your application is represented as a "mailable" class. These classes are stored in the `app/Mail` directory. Don't worry if you don't see this directory in your application, since it will be generated for you when you create your first mailable class using the `make:mail` Artisan command:

라라벨에서 애플리케이션을 빌드할 때, 애플리케이션에 의해서 발송되는 이메일의 타입들은 "mailable" 클래스로 표시됩니다. 이 클래스들은 `app/Mail` 디렉토리에 저장됩니다. 애플리케이션에서 이 디렉터리가 없어도 `make:mail` Artisan 명령어를 사용하여 mailable 클래스를 처음 만들 때 생성됩니다.

    php artisan make:mail OrderShipped

<a name="writing-mailables"></a>
## Writing Mailables
## Mailables 작성하기

Once you have generated a mailable class, open it up so we can explore its contents. First, note that all of a mailable class' configuration is done in the `build` method. Within this method, you may call various methods such as `from`, `subject`, `view`, and `attach` to configure the email's presentation and delivery.

Mailable 클래스를 생성했으면 해당 클래스를 열어 내용을 탐색할 수 있습니다. 첫째, 모든 mailable 클래스 구성은 `build` 메소드로 실행됩니다. 이 메소드 안에서 여러분은 `from`, `subject`, `view` 그리고 `attach` 와 같은, 이메일의 형태와 발송에 대해서 설정할 수 있는 다양한 메소드를 사용할 수 있습니다.

> {tip} You may type-hint dependencies on the mailable's `build` method. The Laravel [service container](/docs/{{version}}/container) automatically injects these dependencies.

> {tip} mailable의 `build` 메소드에 의존성을 타입-힌트로 할 수 있습니다. Laravel [서비스 컨테이너](/docs/{{version}}/container)는 이러한 의존성을 자동으로 주입합니다.

<a name="configuring-the-sender"></a>
### Configuring The Sender
### 발송자 설정하기

<a name="using-the-from-method"></a>
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
        return $this->from('example@example.com', 'Example')
                    ->view('emails.orders.shipped');
    }

<a name="using-a-global-from-address"></a>
#### Using A Global `from` Address
#### 글로벌 `from` 메일 주소 사용하기

However, if your application uses the same "from" address for all of its emails, it can become cumbersome to call the `from` method in each mailable class you generate. Instead, you may specify a global "from" address in your `config/mail.php` configuration file. This address will be used if no other "from" address is specified within the mailable class:

하지만 애플리케이션이 이메일에서 사용하는 모든 "발송자(from)" 주소가 동일하다면, 생성한 각각의 mailable 클래스 에서 매번 `from` 메소드를 호출하는 것은 번거로운 일입니다. 대신에, 글로벌 "발송자(from)" 주소를 `config/mail.php` 설정 파일에서 지정할 수 있습니다. mailable 클래스 안에서 다른 "발송자(from)" 주소를 지정하지 않는다면 이 글로벌 주소가 사용될 것입니다.

    'from' => ['address' => 'example@example.com', 'name' => 'App Name'],

In addition, you may define a global "reply_to" address within your `config/mail.php` configuration file:

또한 `config/mail.php` 설정 파일 내에서 글로벌 "reply_to" 주소를 설정 할 수 있습니다.

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

> {tip} 모든 이메일 템플릿을 모아놓기 위한 `resources/views/emails` 디렉토리를 만들기를 원할 수도 있습니다. 하지만, 실제로는 `resources/views` 디렉토리 안에 어디에 구성하더라도 상관없습니다.

<a name="plain-text-emails"></a>
#### Plain Text Emails
#### 텍스트 전용 이메일

If you would like to define a plain-text version of your email, you may use the `text` method. Like the `view` method, the `text` method accepts a template name which will be used to render the contents of the email. You are free to define both an HTML and plain-text version of your message:

이메일을 순수 텍스트 버전으로 정의하고자 한다면, `text` 메소드를 사용하면 됩니다. `view` 메소드와 같이 `text` 메소드는 이메일의 컨텐츠를 렌더링하는데 사용하게될 템플릿의 이름을 인자로 전달받습니다. 이메일 메세지를 HTML 과 순수 텍스트 버전 원하는 것으로 정의할 수 있습니다.

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

<a name="via-public-properties"></a>
#### Via Public Properties
#### public 속성을 사용하여

Typically, you will want to pass some data to your view that you can utilize when rendering the email's HTML. There are two ways you may make data available to your view. First, any public property defined on your mailable class will automatically be made available to the view. So, for example, you may pass data into your mailable class' constructor and set that data to public properties defined on the class:

일반적으로 이메일의 HTML을 렌더링 할 때, 뷰에서 구성할 수 있는 데이터를 전달하기를 원할 것입니다. 여기에는 뷰에 데이터를 전달하는 두 가지 방법이 있습니다. 먼저, mailable 클래스에 정의되어 있는 public 속성은 자동으로 뷰에서 사용할 수 있습니다. 따라서, 예를들어 데이터를 mailable 클래스의 생성자에 전달하고 이 데이터를 클래스에 정의되어 있는 public 속성에 지정할 수 있습니다.

    <?php

    namespace App\Mail;

    use App\Models\Order;
    use Illuminate\Bus\Queueable;
    use Illuminate\Mail\Mailable;
    use Illuminate\Queue\SerializesModels;

    class OrderShipped extends Mailable
    {
        use Queueable, SerializesModels;

        /**
         * The order instance.
         *
         * @var \App\Models\Order
         */
        public $order;

        /**
         * Create a new message instance.
         *
         * @param  \App\Models\Order  $order
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

<a name="via-the-with-method"></a>
#### Via The `with` Method:
#### `with` 메소드를 사용하여:

If you would like to customize the format of your email's data before it is sent to the template, you may manually pass your data to the view via the `with` method. Typically, you will still pass data via the mailable class' constructor; however, you should set this data to `protected` or `private` properties so the data is not automatically made available to the template. Then, when calling the `with` method, pass an array of data that you wish to make available to the template:

만약 여러분이 이메일 데이터의 유형이 템플릿에 전달되기 전에 수정을 가하고 싶다면, `with` 메소드를 사용하여 수동으로 데이터를 뷰에 전달할 수 있습니다. 일반적으로, 이경우에도 여전히 데이터가 mailable 클래스의 생성자에 전달 될것입니다; 하지만 템플릿에서 자동으로 사용가능하지 않도록, 이 데이터를 `protected` 나 `private` 속성에 지정해야 합니다. 이제 템플릿에서 사용하고자 하는 데이터의 배열을 인자로 `with` 메소드를 호출 하십시오.

    <?php

    namespace App\Mail;

    use App\Models\Order;
    use Illuminate\Bus\Queueable;
    use Illuminate\Mail\Mailable;
    use Illuminate\Queue\SerializesModels;

    class OrderShipped extends Mailable
    {
        use Queueable, SerializesModels;

        /**
         * The order instance.
         *
         * @var \App\Models\Order
         */
        protected $order;

        /**
         * Create a new message instance.
         *
         * @param  \App\Models\Order  $order
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

이메일에 파일이 첨부 될 때, `attach` 메소드의 두번째 인자로 첨부 파일의 표시되는 이름과 MIME 타입을 지정할 수 있는 `array`을 지정할 수도 있습니다.

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

<a name="attaching-files-from-disk"></a>
#### Attaching Files From Disk
#### 디스크에 있는 파일 첨부하기

If you have stored a file on one of your [filesystem disks](/docs/{{version}}/filesystem), you may attach it to the email using the `attachFromStorage` method:

[filesystem disks](/docs/{{version}}/filesystem) 중 하나에 파일을 저장했다면, `attachFromStorage` 메소드를 사용하여 이메일에 첨부 할 수 있습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
       return $this->view('emails.orders.shipped')
                   ->attachFromStorage('/path/to/file');
    }

If necessary, you may specify the file's attachment name and additional options using the second and third arguments to the `attachFromStorage` method:

필요하다면 `attachFromStorage` 메소드의 두번째와 세번째 인자를 사용하여 파일의 첨부 파일 이름과 추가 옵션을 지정할 수 있습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
       return $this->view('emails.orders.shipped')
                   ->attachFromStorage('/path/to/file', 'name.pdf', [
                       'mime' => 'application/pdf'
                   ]);
    }

The `attachFromStorageDisk` method may be used if you need to specify a storage disk other than your default disk:

`attachFromStorageDisk` 메소드는 기본 디스크가 아닌 다른 스토리지 디스크를 지정해야 할 때 사용할 수 있습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
       return $this->view('emails.orders.shipped')
                   ->attachFromStorageDisk('s3', '/path/to/file');
    }

<a name="raw-data-attachments"></a>
#### Raw Data Attachments
#### Raw 데이터 첨부하기

The `attachData` method may be used to attach a raw string of bytes as an attachment. For example, you might use this method if you have generated a PDF in memory and want to attach it to the email without writing it to disk. The `attachData` method accepts the raw data bytes as its first argument, the name of the file as its second argument, and an array of options as its third argument:

`attachData` 메소드는 raw string 의 바이트를 첨부하는데 사용됩니다. 예를 들어 메모리에서 PDF를 생성했고 디스크에 저장하지 않고 메일에 첨부하려는 경우에 사용할 수 있습니다. `attachData` 메소드는 첫번째 인자로 raw 데이터 바이트를, 두번째 인자로 파일의 이름을, 그리고 세번째 인자로 옵션 배열을 전달 받습니다.

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

Embedding inline images into your emails is typically cumbersome; however, Laravel provides a convenient way to attach images to your emails. To embed an inline image, use the `embed` method on the `$message` variable within your email template. Laravel automatically makes the `$message` variable available to all of your email templates, so you don't need to worry about passing it in manually:

이메일에 인라인 이미지를 포함시키는 것은 번거로운 일입니다. 하지만 라라벨에서는 이메일에 이미지를 첨부할 수 있는 편리한 방법을 제공합니다. 인라인 이미지를 포함하려면 이메일 템플릿 내의 `$message` 변수에 `embed` 메소드를 사용하면 됩니다. 라라벨은 자동으로 `$message` 변수를 모든 이메일 템플릿에서 사용할 수 있도록 하므로 수동으로 전달하는 것에 대해 걱정할 필요가 없습니다.

    <body>
        Here is an image:

        <img src="{{ $message->embed($pathToImage) }}">
    </body>

> {note} The `$message` variable is not available in plain-text message templates since plain-text messages do not utilize inline attachments.

> {note} `$message` 변수는 플레인-텍스트 메시지가 인라인 첨부파일을 사용하지 않기 때문에 플레인-텍스트 메시지 템플릿에서 사용할 수 없습니다.

<a name="embedding-raw-data-attachments"></a>
#### Embedding Raw Data Attachments
#### Raw 데이터를 첨부하는 방법

If you already have a raw image data string you wish to embed into an email template, you may call the `embedData` method on the `$message` variable. When calling the `embedData` method, you will need to provide a filename that should be assigned to the embedded image:

이메일 템플릿에 포함시키고자 하는 raw 이미지 데이터 문자열을 가지고 있다면 `$message` 변수에서 `embedData` 메소드를 호출할 수 있습니다. `embedData` 메소드를 호출할 때 포함된 이미지에 파일 이름을 제공해야 합니다.

    <body>
        Here is an image from raw data:

        <img src="{{ $message->embedData($data, 'example-image.jpg') }}">
    </body>

<a name="customizing-the-swiftmailer-message"></a>
### Customizing The SwiftMailer Message
### SwiftMailer 메세지 커스터마이징 하기

The `withSwiftMessage` method of the `Mailable` base class allows you to register a closure which will be invoked with the SwiftMailer message instance before sending the message. This gives you an opportunity to deeply customize the message before it is delivered:

`Mailable` 기본 클래스의 `withSwiftMessage` 메소드를 사용하면 메시지를 보내기 전에 SwiftMailer 메시지 인스턴스와 함께 호출될 클로저를 등록할 수 있습니다. 이렇게 되면 메세지를 보내기 전에 커스터마이징 할 수 있습니다.

    /**
     * Build the message.
     *
     * @return $this
     */
    public function build()
    {
        $this->view('emails.orders.shipped');

        $this->withSwiftMessage(function ($message) {
            $message->getHeaders()->addTextHeader(
                'Custom-Header', 'Header Value'
            );
        });

        return $this;
    }

<a name="markdown-mailables"></a>
## Markdown Mailables
## 마크다운 Mailables

Markdown mailable messages allow you to take advantage of the pre-built templates and components of [mail notifications](/docs/{{version}}/notifications#mail-notifications) in your mailables. Since the messages are written in Markdown, Laravel is able to render beautiful, responsive HTML templates for the messages while also automatically generating a plain-text counterpart.

마크다운 mailable 메시지 기능을 사용하면 mailable 항목에서 미리 빌드된 템플릿 과 [메일 알림](/docs/{{version}}/notifications#mail-notifications)의 컴포넌트를 활용할 수 있습니다. 마크다운을 통해서 메세지를 작성하게 되면서, 라라벨은 메세지를 보다 원활하게 렌더링 하고, 반응형 HTML 템플릿을 사용하는 동시에 일반 텍스트를 자동으로 생성할 수 있게 되었습니다.

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
                    ->markdown('emails.orders.shipped', [
                        'url' => $this->orderUrl,
                    ]);
    }

<a name="writing-markdown-messages"></a>
### Writing Markdown Messages
### 마크다운으로 메세지 작성하기

Markdown mailables use a combination of Blade components and Markdown syntax which allow you to easily construct mail messages while leveraging Laravel's pre-built email UI components:

마크다운 mailable 은 블레이드 컴포넌트와 마크다운 문법을 조합하여 라라벨이 사전에 구성해둔 이메일 UI 컴포넌트를 활용하여 메일 메시지를 쉽게 생성할 수 있습니다.

    @component('mail::message')
    # Order Shipped

    Your order has been shipped!

    @component('mail::button', ['url' => $url])
    View Order
    @endcomponent

    Thanks,<br>
    {{ config('app.name') }}
    @endcomponent

> {tip} Do not use excess indentation when writing Markdown emails. Per Markdown standards, Markdown parsers will render indented content as code blocks.

> {tip} 마크다운으로 메일을 작성할 때, 너무 많은 들여쓰기를 사용하지 마십시오. 마크다운 표준에 따라 마크다운 파서는 들여쓰기된 콘텐츠를 코드 블록으로 렌더링합니다.

<a name="button-component"></a>
#### Button Component
#### 버튼 컴포넌트

The button component renders a centered button link. The component accepts two arguments, a `url` and an optional `color`. Supported colors are `primary`, `success`, and `error`. You may add as many button components to a message as you wish:

버튼 컴포넌트는 가운데 있는 버튼 링크를 렌더링 합니다. 컴포넌트는 두개의 인자를 전달 받는데, `url` 과 옵셔널하게 `color`를 전달받습니다. 사용가능한 컬러는 `primary`, `success`, 그리고 `error` 입니다. 원하는 대로, 여러개의 버튼 컴포넌트를 추가할 수 있습니다.

    @component('mail::button', ['url' => $url, 'color' => 'success'])
    View Order
    @endcomponent

<a name="panel-component"></a>
#### Panel Component
#### 패널 컴포넌트

The panel component renders the given block of text in a panel that has a slightly different background color than the rest of the message. This allows you to draw attention to a given block of text:

패널 컴포넌트는 나머지 텍스트와 배경색이 약간 다른 패널에 텍스트 블럭을 렌더링합니다. 이렇게하면 주어진 텍스트 블럭을 보다 강조할 수 있습니다.

    @component('mail::panel')
    This is the panel content.
    @endcomponent

<a name="table-component"></a>
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

This command will publish the Markdown mail components to the `resources/views/vendor/mail` directory. The `mail` directory will contain an `html` and a `text` directory, each containing their respective representations of every available component. You are free to customize these components however you like.

이 명령어는 마크다운 메일 컴포넌트를 `resources/views/vendor/mail` 디렉토리에 퍼블리싱 합니다. `mail` 디렉토리는 `html` 과 `text` 디렉토리를 가지고 있는데, 각각은 사용가능한 컴포넌트의 표현들이 들어 있습니다. 원한다면 이 컴포넌트를 커스터마이징 할 수 있습니다.

<a name="customizing-the-css"></a>
#### Customizing The CSS
#### CSS 커스터마이징

After exporting the components, the `resources/views/vendor/mail/html/themes` directory will contain a `default.css` file. You may customize the CSS in this file and your styles will automatically be converted to inline CSS styles within the HTML representations of your Markdown mail messages.

컴포넌트를 내보내기(export) 한 이후에, `resources/views/vendor/mail/html/themes` 디렉토리를 보면 `default.css` 파일을 확인할 수 있습니다. 이 파일에서 CSS를 커스터마이징 할 수 있으며 스타일은 마크다운 메일 메시지의 HTML 표현 내에서 인라인 CSS 스타일로 자동 변환됩니다.

If you would like to build an entirely new theme for Laravel's Markdown components, you may place a CSS file within the `html/themes` directory. After naming and saving your CSS file, update the `theme` option of your application's `config/mail.php` configuration file to match the name of your new theme.

라라벨의 Markdown 컴포넌트에 대해 완전히 새로운 테마를 구축하려면 `html/themes` 디렉토리에 CSS 파일을 배치 할 수 있습니다. CSS 파일의 이름을 지정하고 저장 한 후, 애플리케이션의 `config/mail.php` 설정 파일의 `theme` 옵션을 새 테마의 이름과 일치하도록 업데이트합니다.

To customize the theme for an individual mailable, you may set the `$theme` property of the mailable class to the name of the theme that should be used when sending that mailable.

개별 mailable에 대한 테마를 커스텀하기 위해 mailable 클래스의 `$theme` 속성을 해당 mailable을 보낼 때 사용해야하는 테마 이름으로 설정할 수 있습니다.

<a name="sending-mail"></a>
## Sending Mail
## 메일 발송하기

To send a message, use the `to` method on the `Mail` [facade](/docs/{{version}}/facades). The `to` method accepts an email address, a user instance, or a collection of users. If you pass an object or collection of objects, the mailer will automatically use their `email` and `name` properties when determining the email's recipients, so make sure these attributes are available on your objects. Once you have specified your recipients, you may pass an instance of your mailable class to the `send` method:

메일을 발송하기 위해서는 `Mail` [파사드](/docs/{{version}}/facades)에서 `to` 메서드를 사용하면 됩니다. `to` 메소드는 이메일 주소, 사용자 인스턴스 또는 사용자 컬렉션을 허용합니다. 하나의 객체나 객체 컬렉션을 전달하면 메일러는 이메일 수신자를 결정할 때 자동으로 `email` 및 `name` 속성을 사용하므로 이러한 속성을 객체에서 사용할 수 있는지 확인하십시오. 수신자를 지정하고 나면 mailable 클래스의 인스턴스를 `send` 메소드에 전달할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Mail\OrderShipped;
    use App\Models\Order;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Mail;

    class OrderShipmentController extends Controller
    {
        /**
         * Ship the given order.
         *
         * @param  \Illuminate\Http\Request  $request
         * @return \Illuminate\Http\Response
         */
        public function store(Request $request)
        {
            $order = Order::findOrFail($request->order_id);

            // Ship the order...

            Mail::to($request->user())->send(new OrderShipped($order));
        }
    }

You are not limited to just specifying the "to" recipients when sending a message. You are free to set "to", "cc", and "bcc" recipients by chaining their respective methods together:

메시지를 보낼 때 "받는 사람" 수신자를 지정하는 것으로 제한되지 않습니다. 각각의 메소드를 서로 연결하여 "to", "cc" 및 "bcc" 수신인을 자유롭게 설정할 수 있습니다.

    Mail::to($request->user())
        ->cc($moreUsers)
        ->bcc($evenMoreUsers)
        ->send(new OrderShipped($order));

<a name="looping-over-recipients"></a>
#### Looping Over Recipients
#### 수신자 반복하기

Occasionally, you may need to send a mailable to a list of recipients by iterating over an array of recipients / email addresses. However, since the `to` method appends email addresses to the mailable's list of recipients, each iteration through the loop will send another email to every previous recipient. Therefore, you should always re-create the mailable instance for each recipient:

경우에 따라 수신자 / 이메일 주소의 배열을 반복하여 수신자 목록에 메일로 보내야 할 수도 있습니다. 그러나 `to` 메소드는 메일의 수신자 목록에 이메일 주소를 추가하기 때문에 루프를 반복할 때마다 모든 이전 수신자에게 또 다른 이메일이 전송됩니다. 따라서 각 수신자에 대해 항상 메일링이 가능한 인스턴스를 다시 생성해야 합니다.

    foreach (['taylor@example.com', 'dries@example.com'] as $recipient) {
        Mail::to($recipient)->send(new OrderShipped($order));
    }

<a name="sending-mail-via-a-specific-mailer"></a>
#### Sending Mail Via A Specific Mailer
#### 특정 메일러를 통해 메일 보내기

By default, Laravel will send email using the mailer configured as the `default` mailer in your application's `mail` configuration file. However, you may use the `mailer` method to send a message using a specific mailer configuration:

라라벨은 기본적으로 애플리케이션의 `mail` 설정 파일에서 `default` 메일러로 설정된 메일러를 사용하여 메일을 보냅니다.  그러나 `mailer`메소드을 사용하여 특정 메일러 구성을 사용하여 메시지를 보낼 수 있습니다.

    Mail::mailer('postmark')
            ->to($request->user())
            ->send(new OrderShipped($order));

<a name="queueing-mail"></a>
### Queueing Mail
### 메일을 큐를 통해서 보내기

<a name="queueing-a-mail-message"></a>
#### Queueing A Mail Message
#### 이메일을 큐로 보내기

Since sending email messages can negatively impact the response time of your application, many developers choose to queue email messages for background sending. Laravel makes this easy using its built-in [unified queue API](/docs/{{version}}/queues). To queue a mail message, use the `queue` method on the `Mail` facade after specifying the message's recipients:

메일을 보내면 애플리케이션의 응답 시간을 크게 저하시키기 때문에 많은 개발자들은 이메일 메세지를 백그라운드 에서 보낼 수 있도록 큐-대기행열에 넣어 처리하도록 합니다. 라라벨에서는 내장되어 있는 [일관된 큐 API](/docs/{{version}}/queues)를 통해서 이러한 작업을 손쉽게 수행할 수 있게 합니다. 이메일 메세지를 대기-큐에 넣기 위해서는 메세지의 수신자를 지정한 다음에, `Mail` 파사드의 `queue` 메소드를 호출하도록 하면 됩니다.

    Mail::to($request->user())
        ->cc($moreUsers)
        ->bcc($evenMoreUsers)
        ->queue(new OrderShipped($order));

This method will automatically take care of pushing a job onto the queue so the message is sent in the background. You will need to [configure your queues](/docs/{{version}}/queues) before using this feature.

이 메소드는 자동으로 큐에 작업을 추가하여 메세지가 백그라운드에서 보내지도록 할것입니다. 이 기능을 사용하기 위해서는 [큐 설정하기](/docs/{{version}}/queues)를 확인하셔야 합니다.

<a name="delayed-message-queueing"></a>
#### Delayed Message Queueing
#### 큐에서 메일을 지연시켜서 보내기

If you wish to delay the delivery of a queued email message, you may use the `later` method. As its first argument, the `later` method accepts a `DateTime` instance indicating when the message should be sent:

큐를 통해서 이메일을 보낼 때 시간을 지연시켜서 보내고자 한다면, `later` 메소드를 사용하면 됩니다. `later` 메소드의 첫번째 인자로 몇초동안 지연시킬 것인지 나타내는 `DateTime` 인스턴스를 전달 받습니다.

    Mail::to($request->user())
        ->cc($moreUsers)
        ->bcc($evenMoreUsers)
        ->later(now()->addMinutes(10), new OrderShipped($order));

<a name="pushing-to-specific-queues"></a>
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

<a name="queueing-by-default"></a>
#### Queueing By Default
#### 기본을 큐로 발송하도록 설정하기

If you have mailable classes that you want to always be queued, you may implement the `ShouldQueue` contract on the class. Now, even if you call the `send` method when mailing, the mailable will still be queued since it implements the contract:

Mailable 클래스가 항상 큐를 통해서 처리되도록 하려면, 클래스에 `ShouldQueue` implement 를 추가하면 됩니다. 그러면 `send` 메소드가 호출되어 메일이 발송될 때 contract에 의해서 큐를 통해서 메일이 발송됩니다.

    use Illuminate\Contracts\Queue\ShouldQueue;

    class OrderShipped extends Mailable implements ShouldQueue
    {
        //
    }

<a name="queued-mailables-and-database-transactions"></a>
#### Queued Mailables & Database Transactions
#### Queued Mailables 및 데이터베이스 트랜잭션

When queued mailables are dispatched within database transactions, they may be processed by the queue before the database transaction has committed. When this happens, any updates you have made to models or database records during the database transaction may not yet be reflected in the database. In addition, any models or database records created within the transaction may not exist in the database. If your mailable depends on these models, unexpected errors can occur when the job that sends the queued mailable is processed.

큐에 있는 메일링 가능한 항목이 데이터베이스 트랜잭션 내에서 디스패치되면, 데이터베이스 트랜잭션이 커밋되기 전에 큐에서 의해 처리될 수 있습니다. 이런 경우 데이터베이스 트랜잭션 중 모델 또는 데이터베이스 레코드에 대해 업데이트가 아직 데이터베이스에 반영되지 않을 수 있습니다. 또한 트랜잭션 내에 작성된 모델 또는 데이터베이스 레코드가 데이터베이스에 존재하지 않을 수 있습니다. 이러한 모델에 따라 메일링이 달라지는 경우 큐에 있는 메일 파일을 보내는 작업이 처리될 때 예기치 않은 오류가 발생할 수 있습니다.

If your queue connection's `after_commit` configuration option is set to `false`, you may still indicate that a particular queued mailable should be dispatched after all open database transactions have been committed by calling the `afterCommit` method when sending the mail message:

큐 커넥션의 `after_commit` 환경설정 옵션이 `false`로 설정되어 있으면 메일 메시지를 보낼 때 `afterCommit` 메소드를 호출하여 모든 데이터베이스 트랜잭션이 커밋된 후 큐에 있는 특정 메일이 발송 가능한 항목을 표시할 수 있습니다.

    Mail::to($request->user())->send(
        (new OrderShipped($order))->afterCommit()
    );

Alternatively, you may call the `afterCommit` method from your mailable's constructor:

또는 mailable의 생성자에서 `afterCommit` 메소드를 호출할 수 있습니다.

    <?php

    namespace App\Mail;

    use Illuminate\Bus\Queueable;
    use Illuminate\Contracts\Queue\ShouldQueue;
    use Illuminate\Mail\Mailable;
    use Illuminate\Queue\SerializesModels;

    class OrderShipped extends Mailable implements ShouldQueue
    {
        use Queueable, SerializesModels;

        /**
         * Create a new message instance.
         *
         * @return void
         */
        public function __construct()
        {
            $this->afterCommit();
        }
    }

> {tip} To learn more about working around these issues, please review the documentation regarding [queued jobs and database transactions](/docs/{{version}}/queues#jobs-and-database-transactions).
 
> {tip} 이러한 문제를 해결하는 방법에 대해 자세히 알아보려면 [대기 중인 작업 및 데이터베이스 트랜잭션](/docs/{{version}}/queues#jobs-and-database-transactions)에 관한 문서를 확인하세요.

<a name="rendering-mailables"></a>
## Rendering Mailables
## 메일 렌더링

Sometimes you may wish to capture the HTML content of a mailable without sending it. To accomplish this, you may call the `render` method of the mailable. This method will return the evaluated HTML content of the mailable as a string:

때로는 메일링을 보내지 않고 HTML 컨텐츠만 캡처하고 싶을 수도 있습니다. 이를 위해 mailable의 `render` 메소드를 호출할 수 있습니다. 이 메서드는 mailable의 렌더링된 HTML 컨텐츠를 문자열로 반환합니다.

    use App\Mail\InvoicePaid;
    use App\Models\Invoice;

    $invoice = Invoice::find(1);

    return (new InvoicePaid($invoice))->render();

<a name="previewing-mailables-in-the-browser"></a>
### Previewing Mailables In The Browser
### 브라우저에서 메일 미리보기

When designing a mailable's template, it is convenient to quickly preview the rendered mailable in your browser like a typical Blade template. For this reason, Laravel allows you to return any mailable directly from a route closure or controller. When a mailable is returned, it will be rendered and displayed in the browser, allowing you to quickly preview its design without needing to send it to an actual email address:

메일 템플릿을 디자인 할 때 일반적인 블레이드 템플릿처럼 브라우저에서 렌더링 된 메일을 빠르게 미리 보는 것이 편리합니다. 이러한 이유로 라라벨은 라우트 클로저 또는 컨트롤러에서 직접 메일을 반환 할 수 있습니다. 메일이 반환되면 브라우저에 렌더링된 후 표시되므로 실제 이메일 주소로 보낼 필요없이 디자인을 빠르게 미리 볼 수 있습니다.

    Route::get('/mailable', function () {
        $invoice = App\Models\Invoice::find(1);

        return new App\Mail\InvoicePaid($invoice);
    });

> {note} [Inline attachments](#inline-attachments) will not be rendered when a mailable is previewed in your browser. To preview these mailables, you should send them to an email testing application such as [MailHog](https://github.com/mailhog/MailHog) or [HELO](https://usehelo.com).

> {note} [인라인 첨부 파일(Inline attachments)](#inline-attachments)은 브라우저에서 메일링을 미리 볼 때 렌더링되지 않습니다. 이러한 메일링을 미리 보려면 [MailHog](https://github.com/mailhog/MailHog) 또는 [HELO](https://usehelo.com)와 같은 이메일 테스트 애플리케이션으로 보내야 합니다.

<a name="localizing-mailables"></a>
## Localizing Mailables
## Mailables 현지화

Laravel allows you to send mailables in a locale other than the request's current locale, and will even remember this locale if the mail is queued.

라라벨을 사용하면 요청의 현재 로케일과 다른 로케일에서 메일링을 보낼 수 있으며 메일이 대기 중인 경우에도 이 로케일을 기억합니다.

To accomplish this, the `Mail` facade offers a `locale` method to set the desired language. The application will change into this locale when the mailable's template is being evaluated and then revert back to the previous locale when evaluation is complete:

이를 달성하기 위해 `Mail` 파사드는 원하는 언어를 설정하는 `locale` 메소드를 제공합니다. mailable의 템플릿이 만들어 질 때 애플리케이션이 이 로케일로 변경되고 포맷이 완료되면 이전 로케일로 되돌아갑니다.

    Mail::to($request->user())->locale('es')->send(
        new OrderShipped($order)
    );

<a name="user-preferred-locales"></a>
### User Preferred Locales
### 사용자 선호 언어

Sometimes, applications store each user's preferred locale. By implementing the `HasLocalePreference` contract on one or more of your models, you may instruct Laravel to use this stored locale when sending mail:

때로는 애플리케이션의 각 사용자 마다 기본 언어설정이 저장됩니다. 하나 이상의 모델에 `HasLocalePreference` contract을 구현하면 라라벨이 메일을 보낼 때 이 저장된 언어를 사용하게 할 수 있습니다.

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

일단 인터페이스를 구현하면 라라벨은 메일 및 알림을 모델로 전송할 때 자동으로 기본 설정 언어를 사용합니다. 따라서 이 인터페이스를 사용할 때는 `locale` 메소드를 호출 할 필요가 없습니다.

    Mail::to($request->user())->send(new OrderShipped($order));

<a name="testing-mailables"></a>
## Testing Mailables
## Mailables 테스팅

Laravel provides several convenient methods for testing that your mailables contain the content that you expect. These methods are: `assertSeeInHtml`, `assertDontSeeInHtml`, `assertSeeInText`, and `assertDontSeeInText`.

라라벨은 당신의 mailables에 당신이 원하는 내용이 포함되어 있는지 테스트하기 위해 몇 가지 편리한 방법을 제공합니다. 이러한 메소드는 `assertSeeInHtml`, `assertDontSeeInHtml`, `assertSeeInText` 및 `assertDontSeeInText`입니다.

As you might expect, the "HTML" assertions assert that the HTML version of your mailable contains a given string, while the "text" assertions assert that the plain-text version of your mailable contains a given string:

예상할 수 있듯이 "HTML"은 mailable의 HTML 버전이 주어진 문자열을 포함한다고 검증하는 반면, "text" 는 mailable의 일반 텍스트 버전이 주어진 문자열을 포함한다고 검증합니다.

    use App\Mail\InvoicePaid;
    use App\Models\User;

    public function test_mailable_content()
    {
        $user = User::factory()->create();

        $mailable = new InvoicePaid($user);

        $mailable->assertSeeInHtml($user->email);
        $mailable->assertSeeInHtml('Invoice Paid');

        $mailable->assertSeeInText($user->email);
        $mailable->assertSeeInText('Invoice Paid');
    }

<a name="testing-mailable-sending"></a>
#### Testing Mailable Sending
#### Mailable 전송 테스트 

We suggest testing the content of your mailables separately from your tests that assert that a given mailable was "sent" to a specific user. To learn how to test that mailables were sent, check out our documentation on the [Mail fake](/docs/{{version}}/mocking#mail-fake).

mailables가 특정 사용자에게 "전송" 되었다고 검증하는 테스트와 별도로 mailable의 내용을 테스트할 것을 권장합니다. mailables가 전송되었는지 테스트하는 방법은 [Mail fake](/docs/{{version}}/mocking#mail-fake) 문서를 참고하세요.

<a name="mail-and-local-development"></a>
## Mail & Local Development
## 메일 & 로컬 개발환경

When developing an application that sends email, you probably don't want to actually send emails to live email addresses. Laravel provides several ways to "disable" the actual sending of emails during local development.

이메일을 송신하는 애플리케이션을 개발 중이라면 실제로 이메일이 보내지기를 원하지는 않으실겁니다. 라라벨은 로컬 개발환경에서 이메일을 보내는 것은 "비활성화" 시킬 수 있는 몇가지 방법을 제공합니다.

<a name="log-driver"></a>
#### Log Driver
#### 로그 드라이버

Instead of sending your emails, the `log` mail driver will write all email messages to your log files for inspection. Typically, this driver would only be used during local development. For more information on configuring your application per environment, check out the [configuration documentation](/docs/{{version}}/configuration#environment-configuration).

이메일을 발송하는 대신, `log` 메일 드라이버는 모든 이메일 메시지를 확인하기 위해서 로그파일에 기록합니다. 일반적으로 이 드라이버는 로컬 개발환경 중에만 사용됩니다. 환경별 애플리케이션 구성에 대한 자세한 내용은 [구성 문서](/docs/{{version}}/configuration#environment-configuration)를 확인하세요.

<a name="mailtrap"></a>
#### HELO / Mailtrap / MailHog
#### HELO / Mailtrap / MailHog

Finally, you may use a service like [HELO](https://usehelo.com) or [Mailtrap](https://mailtrap.io) and the `smtp` driver to send your email messages to a "dummy" mailbox where you may view them in a true email client. This approach has the benefit of allowing you to actually inspect the final emails in Mailtrap's message viewer.

마지막으로 진정한 이메일 클라이언트에서 [HELO](https://usehelo.com) 또는 [Mailtrap](https://mailtrap.io) 및 `smtp` 드라이버와 같은 서비스를 사용하여 이메일 메시지를 볼 수 있는 "더미(dummy)" 사서함으로 보낼 수 있습니다. 이 방법을 사용하면 Mailtrap의 메시지 뷰어에서 최종 이메일을 실제로 확할인 수 있다는 장점이 있습니다.

If you are using [Laravel Sail](/docs/{{version}}/sail), you may preview your messages using [MailHog](https://github.com/mailhog/MailHog). When Sail is running, you may access the MailHog interface at: `http://localhost:8025`.

[라라벨 세일(Sail)](/docs/{{version}}/sail)을 사용하는 경우 [MailHog](https://github.com/mailhog/MailHog) 를 사용하여 메시지를 미리 볼 수 있습니다. Sail이 실행 중일 때 `http://localhost:8025`에서 MailHog 인터페이스에 액세스할 수 있습니다.

<a name="events"></a>
## Events
## 이벤트

Laravel fires two events during the process of sending mail messages. The `MessageSending` event is fired prior to a message being sent, while the `MessageSent` event is fired after a message has been sent. Remember, these events are fired when the mail is being *sent*, not when it is queued. You may register event listeners for this event in your `App\Providers\EventServiceProvider` service provider:

라라벨은 이메일을 보내는 동안 두개의 이벤트를 발생시킵니다. `MessageSending` 이벤트는 메세지를 보내기 전에 발생하고, `MessageSent` 이벤트는 메세지를 보낸 후에 발생합니다. 주의할 점은 이 이벤트들은 메일이 큐를 통하지 않고 바로 *보내질* 때 발생한다는 것입니다. 여러분은 `App\Providers\EventServiceProvider` 서비스 프로바이더에서 이 이벤트에 대한 이벤트 리스너를 등록할 수 있습니다.

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
