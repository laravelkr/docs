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
    - [Attachable Objects](#attachable-objects)
    - [첨부 가능한 객체](#attachable-objects)
    - [Headers](#headers)
    - [헤더](#headers)
    - [Tags & Metadata](#tags-and-metadata)
    - [태그 & 메타데이터](#tags-and-metadata)
    - [Customizing The Symfony Message](#customizing-the-symfony-message)
    - [Symfony 메세지 커스터미아징하기](#customizing-the-symfony-message)
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
- [Custom Transports](#custom-transports)
- [메일 전송기능 커스터마이징](#custom-transports)
  - [Additional Symfony Transports](#additional-symfony-transports)
  - [추가적인 Symfony 전송기능](#additional-symfony-transports)


<a name="introduction"></a>
## Introduction

Sending email doesn't have to be complicated. Laravel provides a clean, simple email API powered by the popular [Symfony Mailer](https://symfony.com/doc/6.2/mailer.html) component. Laravel and Symfony Mailer provide drivers for sending email via SMTP, Mailgun, Postmark, Amazon SES, and `sendmail`, allowing you to quickly get started sending mail through a local or cloud based service of your choice.

이메일을 보내는 것은 복잡할 필요가 없습니다. 라라벨은 인기 있는 [Symfony Mailer](https://symfony.com/doc/6.2/mailer.html) 컴포넌트를 기반으로 하는 깨끗하고 간단한 이메일 API를 제공합니다. 라라벨 및 Symfony Mailer는 SMTP, Mailgun, Postmark, Amazon SES 및 `sendmail`을 통해 이메일을 전송하기 위한 드라이버를 제공하므로 선택한 로컬 또는 클라우드 기반 서비스를 통해 메일 전송을 빠르게 시작할 수 있습니다.

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

The API based drivers such as Mailgun, Postmark, and MailerSend are often simpler and faster than sending mail via SMTP servers. Whenever possible, we recommend that you use one of these drivers.

API를 기반으로한 Mailgun, Postmark, MailerSend 드라이버의 경우 SMTP 서버 보다 빠르고 간편합니다. 가능하다면, 이러한 드라이버 중 하나를 사용하는 것이 좋습니다. 

<a name="mailgun-driver"></a>
#### Mailgun Driver
#### Mailgun 드라이버

To use the Mailgun driver, install Symfony's Mailgun Mailer transport via Composer:

Mailgun 드라이버를 사용하려면, 컴포저를 사용하여 Symfony의 Mailgun Mailer를 설치하십시오. 

```shell
composer require symfony/mailgun-mailer symfony/http-client
```

Next, set the `default` option in your application's `config/mail.php` configuration file to `mailgun`. After configuring your application's default mailer, verify that your `config/services.php` configuration file contains the following options:

다음으로 `config/mail.php` 설정 파일의 `default` 옵션값을 `mailgun` 으로 지정하면 됩니다. 애플리케이션의 기본 메일러를 설정한다음에, `config/services.php` 설정 파일에 다음과 같은 옵션이 포함되어 있는지 확인하십시오.

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

To use the Postmark driver, install Symfony's Postmark Mailer transport via Composer:

Postmark 드라이버를 사용하려면, 컴포저를 사용하여 Symfony의 Postmark Mailer를 설치하십시오.

```shell
composer require symfony/postmark-mailer symfony/http-client
```

Next, set the `default` option in your application's `config/mail.php` configuration file to `postmark`. After configuring your application's default mailer, verify that your `config/services.php` configuration file contains the following options:

다음으로 `config/mail.php` 설정 파일의 `default` 옵션값을 `postmark` 으로 지정하면 됩니다. 애플리케이션의 기본 메일러를 설정한다음에, `config/services.php` 설정 파일에 다음과 같은 옵션이 포함되어 있는지 확인하십시오.

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

```shell
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

If you would like to define [additional options](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-sesv2-2019-09-27.html#sendemail) that Laravel should pass to the AWS SDK's `SendEmail` method when sending an email, you may define an `options` array within your `ses` configuration:

[추가 옵션](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-sesv2-2019-09-27.html#sendemail) 을 정의하고 싶다면 라라벨이 AWS SDK의 `SendEmail` 메소드를 보낼 때 전달해야 합니다. 이메일의 경우 `ses` 설정파일 내에서 `options` 배열을 정의할 수 있습니다.

    'ses' => [
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION', 'us-east-1'),
        'options' => [
            'ConfigurationSetName' => 'MyConfigurationSet',
            'EmailTags' => [
                ['Name' => 'foo', 'Value' => 'bar'],
            ],
        ],
    ],

<a name="mailersend-driver"></a>
#### MailerSend Driver
#### MailerSend 드라이버

[MailerSend](https://www.mailersend.com/), a transactional email and SMS service, maintains their own API based mail driver for Laravel. The package containing the driver may be installed via the Composer package manager:

[MailerSend](https://www.mailersend.com/), a transactional email and SMS service, maintains their own API based mail driver for Laravel. The package containing the driver may be installed via the Composer package manager:

이메일 및 SMS 서비스인 [MailerSend](https://www.mailersend.com/)는 자체 API 라라벨용 메일 드라이버를 제공합니다. 드라이버를 포함한 패키지는 컴포저를 통해 설치할 수 있습니다.

```shell
composer require mailersend/laravel-driver
```

Once the package is installed, add the `MAILERSEND_API_KEY` environment variable to your application's `.env` file. In addition, the `MAIL_MAILER` environment variable should be defined as `mailersend`:

패키지를 설치한 뒤에, 애플리케이션의 `.env` 파일에 `MAILERSEND_API_KEY` 환경 변수를 추가하십시오. `MAIL_MAILER`는 `mailersend` 로 정의되어 있어야 합니다.   

```shell
MAIL_MAILER=mailersend
MAIL_FROM_ADDRESS=app@yourdomain.com
MAIL_FROM_NAME="App Name"

MAILERSEND_API_KEY=your-api-key
```

To learn more about MailerSend, including how to use hosted templates, consult the [MailerSend driver documentation](https://github.com/mailersend/mailersend-laravel-driver#usage).

어떻게 템플릿을 설정하는지를 포함하여 MailerSend에 대해서 더 자세히 알아보려면 [MailerSend 드라이버 매뉴얼](https://github.com/mailersend/mailersend-laravel-driver#usage)를 참조하십시오.

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

```shell
php artisan make:mail OrderShipped
```

<a name="writing-mailables"></a>
## Writing Mailables
## Mailables 작성하기

Once you have generated a mailable class, open it up so we can explore its contents. Mailable class configuration is done in several methods, including the `envelope`, `content`, and `attachments` methods.

메일링 가능한 클래스를 생성했으면 해당 클래스의 내용을 탐색할 수 있도록 엽니다. 메일 가능 클래스 구성은 `envelope`, `content`, `attachments` 메소드를 포함한 여러 메소드에서 수행됩니다.

The `envelope` method returns an `Illuminate\Mail\Mailables\Envelope` object that defines the subject and, sometimes, the recipients of the message. The `content` method returns an `Illuminate\Mail\Mailables\Content` object that defines the [Blade template](/docs/{{version}}/blade) that will be used to generate the message content.

이 `envelope` 메서드는 제목과 메시지 수신자를 정의하는 `Illuminate\Mail\Mailables\Envelope` 개체를 반환합니다. `content` 메서드는 메시지 콘텐츠를 생성하는 데 사용할 [블레이드 템플릿](/docs/{{version}}/blade)을 정의하는 `Illuminate\Mail\Mailables\Content` 개체를 반환합니다.

<a name="configuring-the-sender"></a>
### Configuring The Sender
### 발송자 설정하기

<a name="using-the-envelope"></a>
#### Using The Envelope
#### 봉투 사용하기

First, let's explore configuring the sender of the email. Or, in other words, who the email is going to be "from". There are two ways to configure the sender. First, you may specify the "from" address on your message's envelope:

먼저 전자 메일의 발신자 구성을 살펴보겠습니다. 즉, "발신인"이 될 사람입니다. 발신자를 구성하는 방법에는 두 가지가 있습니다. 먼저 메시지 봉투에 "from" 주소를 지정할 수 있습니다.



    use Illuminate\Mail\Mailables\Address;
    use Illuminate\Mail\Mailables\Envelope;

    /**
     * Get the message envelope.
     */
    public function envelope(): Envelope
    {
        return new Envelope(
            from: new Address('jeffrey@example.com', 'Jeffrey Way'),
            subject: 'Order Shipped',
        );
    }

If you would like, you may also specify a `replyTo` address:

필요하다면 `replyTo` 주소를 지정할 수도 있습니다.

    return new Envelope(
        from: new Address('jeffrey@example.com', 'Jeffrey Way'),
        replyTo: [
            new Address('taylor@example.com', 'Taylor Otwell'),
        ],
        subject: 'Order Shipped',
    );

<a name="using-a-global-from-address"></a>
#### Using A Global `from` Address
#### 글로벌 `from` 메일 주소 사용하기

However, if your application uses the same "from" address for all of its emails, it can become cumbersome to add it to each mailable class you generate. Instead, you may specify a global "from" address in your `config/mail.php` configuration file. This address will be used if no other "from" address is specified within the mailable class:

하지만 애플리케이션이 이메일에서 사용하는 모든 "발송자(from)" 주소가 동일하다면, 생성한 각각의 mailable 클래스 에서 매번 발송자 주소를 추가하는 것은 번거로운 일입니다. 대신에, 글로벌 "발송자(from)" 주소를 `config/mail.php` 설정 파일에서 지정할 수 있습니다. mailable 클래스 안에서 다른 "발송자(from)" 주소를 지정하지 않는다면 이 글로벌 주소가 사용될 것입니다.

    'from' => [
        'address' => env('MAIL_FROM_ADDRESS', 'hello@example.com'),
        'name' => env('MAIL_FROM_NAME', 'Example'),
    ],

In addition, you may define a global "reply_to" address within your `config/mail.php` configuration file:

또한 `config/mail.php` 설정 파일 내에서 글로벌 "reply_to" 주소를 설정 할 수 있습니다.

    'reply_to' => ['address' => 'example@example.com', 'name' => 'App Name'],

<a name="configuring-the-view"></a>
### Configuring The View
### View-뷰 파일 설정하기

Within a mailable class's `content` method, you may define the `view`, or which template should be used when rendering the email's contents. Since each email typically uses a [Blade template](/docs/{{version}}/blade) to render its contents, you have the full power and convenience of the Blade templating engine when building your email's HTML:

mailable 클래스의 `content` 메소드 안에서 `view`나 이메일 컨텐츠를 렌더링할 때 어떤 템플릿을 사용할 것인지를 정의해야 합니다. 각각의 이메일은 컨텐츠를 렌더링 하기 위해서 일반적으로 [블레이드 템플릿](/docs/{{version}}/blade)을 사용하기 때문에, 이메일의 HTML을 구성하는데 블레이드 템플릿 엔진의 강력하고 편리한 기능을 사용할 수 있습니다.

    /**
     * Get the message content definition.
     */
    public function content(): Content
    {
        return new Content(
            view: 'emails.orders.shipped',
        );
    }

> **Note**  
> You may wish to create a `resources/views/emails` directory to house all of your email templates; however, you are free to place them wherever you wish within your `resources/views` directory.

> **Note**  
> 모든 이메일 템플릿을 모아놓기 위한 `resources/views/emails` 디렉토리를 만들기를 원할 수도 있습니다. 하지만, 실제로는 `resources/views` 디렉토리 안에 어디에 구성하더라도 상관없습니다.

<a name="plain-text-emails"></a>
#### Plain Text Emails
#### 텍스트 전용 이메일

If you would like to define a plain-text version of your email, you may specify the plain-text template when creating the message's `Content` definition. Like the `view` parameter, the `text` parameter should be a template name which will be used to render the contents of the email. You are free to define both an HTML and plain-text version of your message:

이메일을 순수 텍스트 버전으로 정의하고자 한다면, 메시지의 `Content` 정의를 생성할 때 순수 텍스트 템플릿을 지정해주면 됩니다.  `view` 메소드와 같이 `text` 파라미터는 이메일의 컨텐츠를 렌더링하는데 사용하게될 템플릿의 이름이어야 합니다. 이메일 메세지를 HTML 과 순수 텍스트 버전 원하는 것으로 정의할 수 있습니다.

    /**
     * Get the message content definition.
     */
    public function content(): Content
    {
        return new Content(
            view: 'emails.orders.shipped',
            text: 'emails.orders.shipped-text'
        );
    }

For clarity, the `html` parameter may be used as an alias of the `view` parameter:

`html` 파라미터는 `view` 파라미터의 별칭으로 사용될 수 있습니다.

    return new Content(
        html: 'emails.orders.shipped',
        text: 'emails.orders.shipped-text'
    );

<a name="view-data"></a>
### View Data
### 뷰 데이터

<a name="via-public-properties"></a>
#### Via Public Properties
#### public 속성을 사용하여

Typically, you will want to pass some data to your view that you can utilize when rendering the email's HTML. There are two ways you may make data available to your view. First, any public property defined on your mailable class will automatically be made available to the view. So, for example, you may pass data into your mailable class's constructor and set that data to public properties defined on the class:

일반적으로 이메일의 HTML을 렌더링 할 때, 뷰에서 구성할 수 있는 데이터를 전달하기를 원할 것입니다. 여기에는 뷰에 데이터를 전달하는 두 가지 방법이 있습니다. 먼저, mailable 클래스에 정의되어 있는 public 속성은 자동으로 뷰에서 사용할 수 있습니다. 따라서, 예를들어 데이터를 mailable 클래스의 생성자에 전달하고 이 데이터를 클래스에 정의되어 있는 public 속성에 지정할 수 있습니다.

    <?php

    namespace App\Mail;

    use App\Models\Order;
    use Illuminate\Bus\Queueable;
    use Illuminate\Mail\Mailable;
    use Illuminate\Mail\Mailables\Content;
    use Illuminate\Queue\SerializesModels;

    class OrderShipped extends Mailable
    {
        use Queueable, SerializesModels;

        /**
         * Create a new message instance.
         */
        public function __construct(
            public Order $order,
        ) {}

        /**
         * Get the message content definition.
         */
        public function content(): Content
        {
            return new Content(
                view: 'emails.orders.shipped',
            );
        }
    }

Once the data has been set to a public property, it will automatically be available in your view, so you may access it like you would access any other data in your Blade templates:

데이터가 public 속성에 지정되고 나면, 뷰에서 자동적으로 이를 사용할 수 있습니다. 따라서 블레이트 템플릿안에서 다른 데이터에 엑세스하는 것처럼 데이터에 접근할 수 있습니다.

    <div>
        Price: {{ $order->price }}
    </div>

<a name="via-the-with-parameter"></a>
#### Via The `with` Parameter:
#### `with` 파라미터를 사용하여:

If you would like to customize the format of your email's data before it is sent to the template, you may manually pass your data to the view via the `Content` definition's `with` parameter. Typically, you will still pass data via the mailable class's constructor; however, you should set this data to `protected` or `private` properties so the data is not automatically made available to the template:

만약 여러분이 이메일 데이터의 유형이 템플릿에 전달되기 전에 수정을 가하고 싶다면, `Content` 정의의 `with` 파라미터를 사용하여 수동으로 데이터를 뷰에 전달할 수 있습니다. 일반적으로, 이경우에도 여전히 데이터가 mailable 클래스의 생성자에 전달 될것입니다; 하지만 템플릿에서 자동으로 사용가능하지 않도록, 이 데이터를 `protected` 나 `private` 속성에 지정해야 합니다.

    <?php

    namespace App\Mail;

    use App\Models\Order;
    use Illuminate\Bus\Queueable;
    use Illuminate\Mail\Mailable;
    use Illuminate\Mail\Mailables\Content;
    use Illuminate\Queue\SerializesModels;

    class OrderShipped extends Mailable
    {
        use Queueable, SerializesModels;

        /**
         * Create a new message instance.
         */
        public function __construct(
            protected Order $order,
        ) {}

        /**
         * Get the message content definition.
         */
        public function content(): Content
        {
            return new Content(
                view: 'emails.orders.shipped',
                with: [
                    'orderName' => $this->order->name,
                    'orderPrice' => $this->order->price,
                ],
            );
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

To add attachments to an email, you will add attachments to the array returned by the message's `attachments` method. First, you may add an attachment by providing a file path to the `fromPath` method provided by the `Attachment` class:

이메일에 파일을 첨부하려면, 메시지의 `attachments` 메서드에 의해 반환되는 배열에 첨부물을 추가하게 됩니다. 우선 `Attachment` 클래스에 의해 제공되는 `fromPath` 메서드에 파일 경로를 제공함으로써 첨부물을 추가합니다.

    use Illuminate\Mail\Mailables\Attachment;

    /**
     * Get the attachments for the message.
     *
     * @return array<int, \Illuminate\Mail\Mailables\Attachment>
     */
    public function attachments(): array
    {
        return [
            Attachment::fromPath('/path/to/file'),
        ];
    }

When attaching files to a message, you may also specify the display name and / or MIME type for the attachment using the `as` and `withMime` methods:

메시지에 파일을 첨부할 때 표시되는 이름과 MIME 타입을 `as`와 `withMime` 메서드를 사용해서 지정해줄 수 있습니다.

    /**
     * Get the attachments for the message.
     *
     * @return array<int, \Illuminate\Mail\Mailables\Attachment>
     */
    public function attachments(): array
    {
        return [
            Attachment::fromPath('/path/to/file')
                    ->as('name.pdf')
                    ->withMime('application/pdf'),
        ];
    }

<a name="attaching-files-from-disk"></a>
#### Attaching Files From Disk
#### 디스크에 있는 파일 첨부하기

If you have stored a file on one of your [filesystem disks](/docs/{{version}}/filesystem), you may attach it to the email using the `fromStorage` attachment method:

[filesystem disks](/docs/{{version}}/filesystem) 중 하나에 파일을 저장했다면, `fromStorage` 메소드를 사용하여 이메일에 첨부 할 수 있습니다.

    /**
     * Get the attachments for the message.
     *
     * @return array<int, \Illuminate\Mail\Mailables\Attachment>
     */
    public function attachments(): array
    {
        return [
            Attachment::fromStorage('/path/to/file'),
        ];
    }

Of course, you may also specify the attachment's name and MIME type:

물론 첨부물의 이름과 MIME 타입도 지정할 수 있습니다.

    /**
     * Get the attachments for the message.
     *
     * @return array<int, \Illuminate\Mail\Mailables\Attachment>
     */
    public function attachments(): array
    {
        return [
            Attachment::fromStorage('/path/to/file')
                    ->as('name.pdf')
                    ->withMime('application/pdf'),
        ];
    }

The `fromStorageDisk` method may be used if you need to specify a storage disk other than your default disk:

`fromStorageDisk` 메소드는 기본 디스크가 아닌 다른 스토리지 디스크를 지정해야 할 때 사용할 수 있습니다.

    /**
     * Get the attachments for the message.
     *
     * @return array<int, \Illuminate\Mail\Mailables\Attachment>
     */
    public function attachments(): array
    {
        return [
            Attachment::fromStorageDisk('s3', '/path/to/file')
                    ->as('name.pdf')
                    ->withMime('application/pdf'),
        ];
    }

<a name="raw-data-attachments"></a>
#### Raw Data Attachments
#### Raw 데이터 첨부하기

The `fromData` attachment method may be used to attach a raw string of bytes as an attachment. For example, you might use this method if you have generated a PDF in memory and want to attach it to the email without writing it to disk. The `fromData` method accepts a closure which resolves the raw data bytes as well as the name that the attachment should be assigned:

`fromData` 메소드는 raw string 의 바이트를 첨부하는데 사용됩니다. 예를 들어 메모리에서 PDF를 생성했고 디스크에 저장하지 않고 메일에 첨부하려는 경우에 사용할 수 있습니다. `fromData` 메소드는 로우 데이터 바이트를 해결하는 클로저와 첨부 파일에 할당해야 하는 이름을 받습니다.

    /**
     * Get the attachments for the message.
     *
     * @return array<int, \Illuminate\Mail\Mailables\Attachment>
     */
    public function attachments(): array
    {
        return [
            Attachment::fromData(fn () => $this->pdf, 'Report.pdf')
                    ->withMime('application/pdf'),
        ];
    }

<a name="inline-attachments"></a>
### Inline Attachments
### 인라인 첨부

Embedding inline images into your emails is typically cumbersome; however, Laravel provides a convenient way to attach images to your emails. To embed an inline image, use the `embed` method on the `$message` variable within your email template. Laravel automatically makes the `$message` variable available to all of your email templates, so you don't need to worry about passing it in manually:

이메일에 인라인 이미지를 포함시키는 것은 번거로운 일입니다. 하지만 라라벨에서는 이메일에 이미지를 첨부할 수 있는 편리한 방법을 제공합니다. 인라인 이미지를 포함하려면 이메일 템플릿 내의 `$message` 변수에 `embed` 메소드를 사용하면 됩니다. 라라벨은 자동으로 `$message` 변수를 모든 이메일 템플릿에서 사용할 수 있도록 하므로 수동으로 전달하는 것에 대해 걱정할 필요가 없습니다.

```blade
<body>
    Here is an image:

    <img src="{{ $message->embed($pathToImage) }}">
</body>
```

> **Warning**  
> The `$message` variable is not available in plain-text message templates since plain-text messages do not utilize inline attachments.

> **Warning**  
> `$message` 변수는 플레인-텍스트 메시지가 인라인 첨부파일을 사용하지 않기 때문에 플레인-텍스트 메시지 템플릿에서 사용할 수 없습니다.

<a name="embedding-raw-data-attachments"></a>
#### Embedding Raw Data Attachments
#### Raw 데이터를 첨부하는 방법

If you already have a raw image data string you wish to embed into an email template, you may call the `embedData` method on the `$message` variable. When calling the `embedData` method, you will need to provide a filename that should be assigned to the embedded image:

이메일 템플릿에 포함시키고자 하는 raw 이미지 데이터 문자열을 가지고 있다면 `$message` 변수에서 `embedData` 메소드를 호출할 수 있습니다. `embedData` 메소드를 호출할 때 포함된 이미지에 파일 이름을 제공해야 합니다.

```blade
<body>
    Here is an image from raw data:

    <img src="{{ $message->embedData($data, 'example-image.jpg') }}">
</body>
```

<a name="attachable-objects"></a>
### Attachable Objects
### 첨부 가능한 개체

While attaching files to messages via simple string paths is often sufficient, in many cases the attachable entities within your application are represented by classes. For example, if your application is attaching a photo to a message, your application may also have a `Photo` model that represents that photo. When that is the case, wouldn't it be convenient to simply pass the `Photo` model to the `attach` method? Attachable objects allow you to do just that.

간단한 문자열 경로를 통해 메시지에 파일을 첨부하는 것으로 충분하지만 많은 경우에 응용 프로그램 내의 첨부 가능한 엔터티는 클래스로 표시됩니다. 예를 들어 응용 프로그램이 메시지에 사진을 첨부하는 경우 응용 프로그램에 해당 사진을 나타내는 `Photo` 모델이 있을 수도 있습니다. 그럴 땐 그냥 모델을 `Photo` 모델을 `attach` 메소드 에 넘기는 게 편하지 않을까요? 첨부 가능한 개체를 사용하면 그렇게 할 수 있습니다.

To get started, implement the `Illuminate\Contracts\Mail\Attachable` interface on the object that will be attachable to messages. This interface dictates that your class defines a `toMailAttachment` method that returns an `Illuminate\Mail\Attachment` instance:

메시지에 첨부할 개체에 `Illuminate\Contracts\Mail\Attachable` 인터페이스를 구현하십시오. 이 인터페이스는 클래스가 `Illuminate\Mail\Attachment` 인스턴스를 반환하는 `toMailAttachment` 메서드를 정의하도록 지시합니다.

    <?php

    namespace App\Models;

    use Illuminate\Contracts\Mail\Attachable;
    use Illuminate\Database\Eloquent\Model;
    use Illuminate\Mail\Attachment;

    class Photo extends Model implements Attachable
    {
        /**
         * Get the attachable representation of the model.
         */
        public function toMailAttachment(): Attachment
        {
            return Attachment::fromPath('/path/to/file');
        }
    }

Once you have defined your attachable object, you may return an instance of that object from the `attachments` method when building an email message:

첨부 가능한 객체를 정의하고 나면 이메일 메시지를 작성할 때 `attachments` 메소드에서 해당 객체의 인스턴스를 반환할 수 있습니다 .

    /**
     * Get the attachments for the message.
     *
     * @return array<int, \Illuminate\Mail\Mailables\Attachment>
     */
    public function attachments(): array
    {
        return [$this->photo];
    }

Of course, attachment data may be stored on a remote file storage service such as Amazon S3. So, Laravel also allows you to generate attachment instances from data that is stored on one of your application's [filesystem disks](/docs/{{version}}/filesystem):

물론 첨부 데이터는 Amazon S3와 같은 원격 파일 스토리지 서비스에 저장될 수 있습니다. 따라서 라라벨을 사용하면 애플리케이션의 [파일 시스템 디스크](/docs/{{version}}/filesystem) 중 하나에 저장된 데이터에서 첨부 파일 인스턴스를 생성할 수도 있습니다 .

    // Create an attachment from a file on your default disk...
    return Attachment::fromStorage($this->path);

    // Create an attachment from a file on a specific disk...
    return Attachment::fromStorageDisk('backblaze', $this->path);

In addition, you may create attachment instances via data that you have in memory. To accomplish this, provide a closure to the `fromData` method. The closure should return the raw data that represents the attachment:

또한 메모리에 있는 데이터를 통해 첨부 파일 인스턴스를 생성할 수 있습니다. 이를 수행하려면 `fromData` 메서드에 클로저를 제공합니다. 클로저는 첨부 파일을 나타내는 원시 데이터를 반환해야 합니다.

    return Attachment::fromData(fn () => $this->content, 'Photo Name');

Laravel also provides additional methods that you may use to customize your attachments. For example, you may use the `as` and `withMime` methods to customize the file's name and MIME type:

라라벨은 또한 첨부 파일을 사용자 정의하는 데 사용할 수 있는 추가 방법을 제공합니다. 예를 들어 `as` 및 `withMime` 메서드를 사용하여 파일 이름과 MIME 유형을 사용자 지정할 수 있습니다.

    return Attachment::fromPath('/path/to/file')
            ->as('Photo Name')
            ->withMime('image/jpeg');

<a name="headers"></a>
### Headers
### 헤더

Sometimes you may need to attach additional headers to the outgoing message. For instance, you may need to set a custom `Message-Id` or other arbitrary text headers.

때로는 보내는 메시지에 추가 헤더를 첨부해야 할 수도 있습니다. 예를 들어 사용자 지정 `Message-Id` 또는 기타 임의의 텍스트 헤더를 설정해야 할 수 있습니다.

To accomplish this, define a `headers` method on your mailable. The `headers` method should return an `Illuminate\Mail\Mailables\Headers` instance. This class accepts `messageId`, `references`, and `text` parameters. Of course, you may provide only the parameters you need for your particular message:

이를 수행하려면 메일러블에 `headers` 메소드를 정의하십시오. `headers` 메서드는 `Illuminate\Mail\Mailables\Headers` 인스턴스를 반환해야 합니다. 이 클래스는 `messageId`, `references` 및 `text` 매개변수를 허용합니다. 물론 특정 메시지에 필요한 매개변수만 제공할 수 있습니다.

    use Illuminate\Mail\Mailables\Headers;

    /**
     * Get the message headers.
     */
    public function headers(): Headers
    {
        return new Headers(
            messageId: 'custom-message-id@example.com',
            references: ['previous-message@example.com'],
            text: [
                'X-Custom-Header' => 'Custom Value',
            ],
        );
    }

<a name="tags-and-metadata"></a>
### Tags & Metadata
### 태그 & 메타데이터

Some third-party email providers such as Mailgun and Postmark support message "tags" and "metadata", which may be used to group and track emails sent by your application. You may add tags and metadata to an email message via your `Envelope` definition:

Mailgun 과 Postmark 와 같은 써드파티 이메일 공급자는 애플리케이션에서 보낸 이메일을 그룹으로 묶어서 관리할 수 있도록 "태그" 와 "메타데이터"를 지정하는 기능을 지원합니다. `Envelope` 정의를 통 이메일 메세지에 태그와 메타데이터를 지정할 수 있습니다.

    use Illuminate\Mail\Mailables\Envelope;

    /**
     * Get the message envelope.
     *
     * @return \Illuminate\Mail\Mailables\Envelope
     */
    public function envelope(): Envelope
    {
        return new Envelope(
            subject: 'Order Shipped',
            tags: ['shipment'],
            metadata: [
                'order_id' => $this->order->id,
            ],
        );
    }

If your application is using the Mailgun driver, you may consult Mailgun's documentation for more information on [tags](https://documentation.mailgun.com/en/latest/user_manual.html#tagging-1) and [metadata](https://documentation.mailgun.com/en/latest/user_manual.html#attaching-data-to-messages). Likewise, the Postmark documentation may also be consulted for more information on their support for [tags](https://postmarkapp.com/blog/tags-support-for-smtp) and [metadata](https://postmarkapp.com/support/article/1125-custom-metadata-faq).

애플리케이션에서 Mailgun 드라이버를 사용한다면 Mailgun의 [tags](https://documentation.mailgun.com/en/latest/user_manual.html#tagging-1) 와 [metadata](https://documentation.mailgun.com/en/latest/user_manual.html#attaching-data-to-messages) 매뉴얼을 참고하십시오. 마찬가지로 Postmark 의 경우에는 [tags](https://postmarkapp.com/blog/tags-support-for-smtp) 와 [metadata](https://postmarkapp.com/support/article/1125-custom-metadata-faq) 매뉴얼을 참고하십시오.

If your application is using Amazon SES to send emails, you should use the `metadata` method to attach [SES "tags"](https://docs.aws.amazon.com/ses/latest/APIReference/API_MessageTag.html) to the message.

애플리케이션에서 Amazone SES 를 사용한다면 `metadata` 메소드를 사용하여 [SES "tags"](https://docs.aws.amazon.com/ses/latest/APIReference/API_MessageTag.html)를 추가해야합니다.

<a name="customizing-the-symfony-message"></a>
### Customizing The Symfony Message
### Symfony 메세지 커스터마이징 하기

Laravel's mail capabilities are powered by Symfony Mailer. Laravel allows you to register custom callbacks that will be invoked with the Symfony Message instance before sending the message. This gives you an opportunity to deeply customize the message before it is sent. To accomplish this, define a `using` parameter on your `Envelope` definition:

라라벨의 메일 기능은 Symfony Mailer에 의해 구동됩니다. 라라벨을 사용하면 메시지를 보내기 전에 Symfony Message 인스턴스와 함께 호출될 사용자 정의 콜백을 등록할 수 있습니다. 이렇게 하면 메시지를 보내기 전에 세부적으로 사용자 지정할 수 있습니다. 이를 수행하려면 `Envelope` 정의에 `using` 매개변수를 정의 하십시오.

    use Illuminate\Mail\Mailables\Envelope;
    use Symfony\Component\Mime\Email;
    
    /**
     * Get the message envelope.
     */
    public function envelope(): Envelope
    {
        return new Envelope(
            subject: 'Order Shipped',
            using: [
                function (Email $message) {
                    // ...
                },
            ]
        );
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

```shell
php artisan make:mail OrderShipped --markdown=emails.orders.shipped
```

Then, when configuring the mailable `Content` definition within its `content` method, use the `markdown` parameter instead of the `view` parameter:

그런다음, `content` 메소드 안에서 메일러블 `Content` 정의를 설정할 때, `view` 파라미터 대신에 `markdown` 파라미터를 사용합니다. 

    use Illuminate\Mail\Mailables\Content;

    /**
     * Get the message content definition.
     */
    public function content(): Content
    {
        return new Content(
            markdown: 'emails.orders.shipped',
            with: [
                'url' => $this->orderUrl,
            ],
        );
    }

<a name="writing-markdown-messages"></a>
### Writing Markdown Messages
### 마크다운으로 메세지 작성하기

Markdown mailables use a combination of Blade components and Markdown syntax which allow you to easily construct mail messages while leveraging Laravel's pre-built email UI components:

마크다운 mailable 은 블레이드 컴포넌트와 마크다운 문법을 조합하여 라라벨이 사전에 구성해둔 이메일 UI 컴포넌트를 활용하여 메일 메시지를 쉽게 생성할 수 있습니다.

```blade
<x-mail::message>
# Order Shipped

Your order has been shipped!

<x-mail::button :url="$url">
View Order
</x-mail::button>

Thanks,<br>
{{ config('app.name') }}
</x-mail::message>
```

> **Note**  
> Do not use excess indentation when writing Markdown emails. Per Markdown standards, Markdown parsers will render indented content as code blocks.

> **Note**  
> 마크다운으로 메일을 작성할 때, 너무 많은 들여쓰기를 사용하지 마십시오. 마크다운 표준에 따라 마크다운 파서는 들여쓰기된 콘텐츠를 코드 블록으로 렌더링합니다.

<a name="button-component"></a>
#### Button Component
#### 버튼 컴포넌트

The button component renders a centered button link. The component accepts two arguments, a `url` and an optional `color`. Supported colors are `primary`, `success`, and `error`. You may add as many button components to a message as you wish:

버튼 컴포넌트는 가운데 있는 버튼 링크를 렌더링 합니다. 컴포넌트는 두개의 인자를 전달 받는데, `url` 과 옵셔널하게 `color`를 전달받습니다. 사용가능한 컬러는 `primary`, `success`, 그리고 `error` 입니다. 원하는 대로, 여러개의 버튼 컴포넌트를 추가할 수 있습니다.

```blade
<x-mail::button :url="$url" color="success">
View Order
</x-mail::button>
```

<a name="panel-component"></a>
#### Panel Component
#### 패널 컴포넌트

The panel component renders the given block of text in a panel that has a slightly different background color than the rest of the message. This allows you to draw attention to a given block of text:

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

You may export all of the Markdown mail components to your own application for customization. To export the components, use the `vendor:publish` Artisan command to publish the `laravel-mail` asset tag:

애플리케이션에서 사용하는 모든 마크다운 메일 컴포넌트는 커스터마이징이 가능합니다. 먼저 컴포넌트를 내보내기 위해서 `vendor:publish` 아티즌 명령어를 사용하여 `laravel-mail` 애셋 태그를 지정합니다.

```shell
php artisan vendor:publish --tag=laravel-mail
```

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
    use Illuminate\Http\RedirectResponse;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Mail;

    class OrderShipmentController extends Controller
    {
        /**
         * Ship the given order.
         */
        public function store(Request $request): RedirectResponse
        {
            $order = Order::findOrFail($request->order_id);

            // Ship the order...

            Mail::to($request->user())->send(new OrderShipped($order));

            return redirect('/orders');
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
        // ...
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
         */
        public function __construct()
        {
            $this->afterCommit();
        }
    }

> **Note**  
> To learn more about working around these issues, please review the documentation regarding [queued jobs and database transactions](/docs/{{version}}/queues#jobs-and-database-transactions).
 
> **Note**  
> 이러한 문제를 해결하는 방법에 대해 자세히 알아보려면 [대기 중인 작업 및 데이터베이스 트랜잭션](/docs/{{version}}/queues#jobs-and-database-transactions)에 관한 문서를 확인하세요.

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

> **Warning**  
> [Inline attachments](#inline-attachments) will not be rendered when a mailable is previewed in your browser. To preview these mailables, you should send them to an email testing application such as [Mailpit](https://github.com/axllent/mailpit) or [HELO](https://usehelo.com).

> **Warning**  
> [인라인 첨부 파일(Inline attachments)](#inline-attachments)은 브라우저에서 메일링을 미리 볼 때 렌더링되지 않습니다. 이러한 메일링을 미리 보려면 [Mailpit](https://github.com/axllent/mailpit) 또는 [HELO](https://usehelo.com)와 같은 이메일 테스트 애플리케이션으로 보내야 합니다.

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
         */
        public function preferredLocale(): string
        {
            return $this->locale;
        }
    }

Once you have implemented the interface, Laravel will automatically use the preferred locale when sending mailables and notifications to the model. Therefore, there is no need to call the `locale` method when using this interface:

일단 인터페이스를 구현하면 라라벨은 메일 및 알림을 모델로 전송할 때 자동으로 기본 설정 언어를 사용합니다. 따라서 이 인터페이스를 사용할 때는 `locale` 메소드를 호출 할 필요가 없습니다.

    Mail::to($request->user())->send(new OrderShipped($order));

<a name="testing-mailables"></a>
## Testing
## 테스팅

<a name="testing-mailable-content"></a>
### Testing Mailable Content
### Mailable 콘텐츠 테스팅

Laravel provides a variety of methods for inspecting your mailable's structure. In addition, Laravel provides several convenient methods for testing that your mailable contains the content that you expect. These methods are: `assertSeeInHtml`, `assertDontSeeInHtml`, `assertSeeInOrderInHtml`, `assertSeeInText`, `assertDontSeeInText`, `assertSeeInOrderInText`, `assertHasAttachment`, `assertHasAttachedData`, `assertHasAttachmentFromStorage`, and `assertHasAttachmentFromStorageDisk`.

라라벨은 메일러블 구조를 검사하는 다양한 메서드를 제공합니다. 라라벨은 당신의 mailables에 당신이 원하는 내용이 포함되어 있는지 테스트하기 위해 몇 가지 편리한 방법을 제공합니다. 이러한 메소드는 `assertSeeInHtml`, `assertDontSeeInHtml`, `assertSeeInOrderInHtml`, `assertSeeInText`, `assertDontSeeInText`, `assertSeeInOrderInText`, `assertHasAttachment`, `assertHasAttachedData`, `assertHasAttachmentFromStorage`, `assertHasAttachmentFromStorageDisk` 입니다.

As you might expect, the "HTML" assertions assert that the HTML version of your mailable contains a given string, while the "text" assertions assert that the plain-text version of your mailable contains a given string:

예상할 수 있듯이 "HTML"은 mailable의 HTML 버전이 주어진 문자열을 포함한다고 검증하는 반면, "text" 는 mailable의 일반 텍스트 버전이 주어진 문자열을 포함한다고 검증합니다.

    use App\Mail\InvoicePaid;
    use App\Models\User;

    public function test_mailable_content(): void
    {
        $user = User::factory()->create();

        $mailable = new InvoicePaid($user);

        $mailable->assertFrom('jeffrey@example.com');
        $mailable->assertTo('taylor@example.com');
        $mailable->assertHasCc('abigail@example.com');
        $mailable->assertHasBcc('victoria@example.com');
        $mailable->assertHasReplyTo('tyler@example.com');
        $mailable->assertHasSubject('Invoice Paid');
        $mailable->assertHasTag('example-tag');
        $mailable->assertHasMetadata('key', 'value');

        $mailable->assertSeeInHtml($user->email);
        $mailable->assertSeeInHtml('Invoice Paid');
        $mailable->assertSeeInOrderInHtml(['Invoice Paid', 'Thanks']);

        $mailable->assertSeeInText($user->email);
        $mailable->assertSeeInOrderInText(['Invoice Paid', 'Thanks']);

        $mailable->assertHasAttachment('/path/to/file');
        $mailable->assertHasAttachment(Attachment::fromPath('/path/to/file'));
        $mailable->assertHasAttachedData($pdfData, 'name.pdf', ['mime' => 'application/pdf']);
        $mailable->assertHasAttachmentFromStorage('/path/to/file', 'name.pdf', ['mime' => 'application/pdf']);
        $mailable->assertHasAttachmentFromStorageDisk('s3', '/path/to/file', 'name.pdf', ['mime' => 'application/pdf']);
    }

<a name="testing-mailable-sending"></a>
### Testing Mailable Sending
### Mailable 전송 테스트 

We suggest testing the content of your mailables separately from your tests that assert that a given mailable was "sent" to a specific user. Typically, the content of mailables is not relevant to the code you are testing, and it is sufficient to simply assert that Laravel was instructed to send a given mailable.

mailables가 특정 사용자에게 "전송" 되었다고 검증하는 테스트와 별도로 mailable의 내용을 테스트할 것을 권장합니다. 일반적으로 mailable의 내용은 테스트 중인 코드와 관련이 없으며, 단순히 라라벨이 특정 mailable을 보내도록 지시하는 것을 assert 하는 것으로 충분합니다.

You may use the `Mail` facade's `fake` method to prevent mail from being sent. After calling the `Mail` facade's `fake` method, you may then assert that mailables were instructed to be sent to users and even inspect the data the mailables received:

`Mail` 파사드의 `fake` 메서드를 사용하면 이메일이 실제로 전송되지는 않습니다. `Mail` 파사드의 `fake` 메서드를 호출한 후 mailable이 사용자에게 전송되도록 지시하는 것을 assert하고 mailable이 수신한 데이터를 검사할 수 있습니다.

    <?php

    namespace Tests\Feature;

    use App\Mail\OrderShipped;
    use Illuminate\Support\Facades\Mail;
    use Tests\TestCase;

    class ExampleTest extends TestCase
    {
        public function test_orders_can_be_shipped(): void
        {
            Mail::fake();

            // Perform order shipping...

            // Assert that no mailables were sent...
            Mail::assertNothingSent();

            // Assert that a mailable was sent...
            Mail::assertSent(OrderShipped::class);

            // Assert a mailable was sent twice...
            Mail::assertSent(OrderShipped::class, 2);

            // Assert a mailable was not sent...
            Mail::assertNotSent(AnotherMailable::class);

            // Assert 3 total mailables were sent...
            Mail::assertSentCount(3);
        }
    }

If you are queueing mailables for delivery in the background, you should use the `assertQueued` method instead of `assertSent`:

mailable 를 queue-큐를 통해서 백그라운드 전송을 한다면 `assertSent` 대신 `assertQueued`를 사용하여 확인하십시오.  

    Mail::assertQueued(OrderShipped::class);
    Mail::assertNotQueued(OrderShipped::class);
    Mail::assertNothingQueued();
    Mail::assertQueuedCount(3);

You may pass a closure to the `assertSent`, `assertNotSent`, `assertQueued`, or `assertNotQueued` methods in order to assert that a mailable was sent that passes a given "truth test". If at least one mailable was sent that passes the given truth test then the assertion will be successful:

특정 조건에 해당하는 mailable 이 전송되었는지 확인하기 위해서 `assertSent`, `assertNotSent`, `assertQueued`, `assertNotQueued` 에 클로저를 인자로 전달할 수 있습니다. 이 클로저에서 주어진 조건을 통과한 mailable 이 하나 이상 전송되었다고 판단되면 assertion은 통과합니다.  

    Mail::assertSent(function (OrderShipped $mail) use ($order) {
        return $mail->order->id === $order->id;
    });

When calling the `Mail` facade's assertion methods, the mailable instance accepted by the provided closure exposes helpful methods for examining the mailable:


`Mail` 파사드의 assertion 메서드를 호출할 때 전달된 클로저에 전달되는 mailable 인스턴스는 mailable를 검사하는데 유용하게 사용할 수 있는 메서드를 가지고 있습니다. 

    Mail::assertSent(OrderShipped::class, function (OrderShipped $mail) use ($user) {
        return $mail->hasTo($user->email) &&
               $mail->hasCc('...') &&
               $mail->hasBcc('...') &&
               $mail->hasReplyTo('...') &&
               $mail->hasFrom('...') &&
               $mail->hasSubject('...');
    });

The mailable instance also includes several helpful methods for examining the attachments on a mailable:

mailable 인스턴스에는 mailable의 첨부 파일을 검사하는 데 유용한 메서드도 몇 가지 포함되어 있습니다.

    use Illuminate\Mail\Mailables\Attachment;

    Mail::assertSent(OrderShipped::class, function (OrderShipped $mail) {
        return $mail->hasAttachment(
            Attachment::fromPath('/path/to/file')
                    ->as('name.pdf')
                    ->withMime('application/pdf')
        );
    });

    Mail::assertSent(OrderShipped::class, function (OrderShipped $mail) {
        return $mail->hasAttachment(
            Attachment::fromStorageDisk('s3', '/path/to/file')
        );
    });

    Mail::assertSent(OrderShipped::class, function (OrderShipped $mail) use ($pdfData) {
        return $mail->hasAttachment(
            Attachment::fromData(fn () => $pdfData, 'name.pdf')
        );
    });

You may have noticed that there are two methods for asserting that mail was not sent: `assertNotSent` and `assertNotQueued`. Sometimes you may wish to assert that no mail was sent **or** queued. To accomplish this, you may use the `assertNothingOutgoing` and `assertNotOutgoing` methods:

이메일이 전송되지 않았다고 assert하는 두 가지 메서드 (`assertNotSent`와 `assertNotQueued`)가 있다는 것을 눈치채셨을 것입니다. 때로는 메일이 발송되지 않았거나 **또는** queue-큐에 대기 중인 것을 assert하고 싶을 수도 있습니다. 이렇게 하려면 `assertNothingOutgoing` 그리고 `assertNotOutgoing` 메서드를 사용할 수 있습니다.

    Mail::assertNothingOutgoing();

    Mail::assertNotOutgoing(function (OrderShipped $mail) use ($order) {
        return $mail->order->id === $order->id;
    });

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
#### HELO / Mailtrap / Mailpit
#### HELO / Mailtrap / Mailpit

Alternatively, you may use a service like [HELO](https://usehelo.com) or [Mailtrap](https://mailtrap.io) and the `smtp` driver to send your email messages to a "dummy" mailbox where you may view them in a true email client. This approach has the benefit of allowing you to actually inspect the final emails in Mailtrap's message viewer.

로그 드라이버 대신에 실제 이메일 클라이언트에서 [HELO](https://usehelo.com) 또는 [Mailtrap](https://mailtrap.io) 및 `smtp` 드라이버와 같은 서비스를 사용하여 이메일 메시지를 볼 수 있는 "더미(dummy)" 사서함으로 보낼 수 있습니다. 이 방법을 사용하면 Mailtrap의 메시지 뷰어에서 최종 이메일을 실제로 확할인 수 있다는 장점이 있습니다.

If you are using [Laravel Sail](/docs/{{version}}/sail), you may preview your messages using [Mailpit](https://github.com/axllent/mailpit). When Sail is running, you may access the Mailpit interface at: `http://localhost:8025`.

[라라벨 세일(Sail)](/docs/{{version}}/sail)을 사용하는 경우 [Mailpit](https://github.com/axllent/mailpit) 를 사용하여 메시지를 미리 볼 수 있습니다. Sail이 실행 중일 때 `http://localhost:8025`에서 MailHog 인터페이스에 액세스할 수 있습니다.

<a name="using-a-global-to-address"></a>
#### Using A Global `to` Address
#### 글로벌 `to` 주소 사용하기

Finally, you may specify a global "to" address by invoking the `alwaysTo` method offered by the `Mail` facade. Typically, this method should be called from the `boot` method of one of your application's service providers:

마지막으로 `Mail` 파사드에서 제공하는 `alwaysTo` 메소드를 호출하여 글로벌 "to" 주소를 지정할 수 있습니다. 일반적으로 이 메소드는 애플리케이션 서비스 프로바이더 중 하나의 `boot` 메소드에서 호출해야 합니다.

    use Illuminate\Support\Facades\Mail;

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        if ($this->app->environment('local')) {
            Mail::alwaysTo('taylor@example.com');
        }
    }

<a name="events"></a>
## Events
## 이벤트

Laravel fires two events during the process of sending mail messages. The `MessageSending` event is fired prior to a message being sent, while the `MessageSent` event is fired after a message has been sent. Remember, these events are fired when the mail is being *sent*, not when it is queued. You may register event listeners for this event in your `App\Providers\EventServiceProvider` service provider:

라라벨은 이메일을 보내는 동안 두개의 이벤트를 발생시킵니다. `MessageSending` 이벤트는 메세지를 보내기 전에 발생하고, `MessageSent` 이벤트는 메세지를 보낸 후에 발생합니다. 주의할 점은 이 이벤트들은 메일이 큐를 통하지 않고 바로 *보내질* 때 발생한다는 것입니다. 여러분은 `App\Providers\EventServiceProvider` 서비스 프로바이더에서 이 이벤트에 대한 이벤트 리스너를 등록할 수 있습니다.

    use App\Listeners\LogSendingMessage;
    use App\Listeners\LogSentMessage;
    use Illuminate\Mail\Events\MessageSending;
    use Illuminate\Mail\Events\MessageSent;

    /**
     * The event listener mappings for the application.
     *
     * @var array
     */
    protected $listen = [
        MessageSending::class => [
            LogSendingMessage::class,
        ],

        MessageSent::class => [
            LogSentMessage::class,
        ],
    ];

<a name="custom-transports"></a>
## Custom Transports
## 이메일 전송기능 커스터마이징

Laravel includes a variety of mail transports; however, you may wish to write your own transports to deliver email via other services that Laravel does not support out of the box. To get started, define a class that extends the `Symfony\Component\Mailer\Transport\AbstractTransport` class. Then, implement the `doSend` and `__toString()` methods on your transport:

라라벨은 다양한 이메일 전송 기능을 포함하고 있지만, 경우에 따라서는 라라벨이 지원하지 않는 다른 이메일 공급자 서비스를 사용하는 고유한 메일 전송 드라이버를 작성하고자 할 수도 있습니다. 이런 경우에는 `Symfony\Component\Mailer\Transport\AbstractTransport` 클래스를 상속받는 클래스를 정의하면 됩니다. 고유한 전송 클래스에서는 `doSend` 와 `__toString()` 메서드를 구현해야합니다.

    use MailchimpTransactional\ApiClient;
    use Symfony\Component\Mailer\SentMessage;
    use Symfony\Component\Mailer\Transport\AbstractTransport;
    use Symfony\Component\Mime\Address;
    use Symfony\Component\Mime\MessageConverter;

    class MailchimpTransport extends AbstractTransport
    {
        /**
         * Create a new Mailchimp transport instance.
         */
        public function __construct(
            protected ApiClient $client,
        ) {
            parent::__construct();
        }

        /**
         * {@inheritDoc}
         */
        protected function doSend(SentMessage $message): void
        {
            $email = MessageConverter::toEmail($message->getOriginalMessage());

            $this->client->messages->send(['message' => [
                'from_email' => $email->getFrom(),
                'to' => collect($email->getTo())->map(function (Address $email) {
                    return ['email' => $email->getAddress(), 'type' => 'to'];
                })->all(),
                'subject' => $email->getSubject(),
                'text' => $email->getTextBody(),
            ]]);
        }

        /**
         * Get the string representation of the transport.
         */
        public function __toString(): string
        {
            return 'mailchimp';
        }
    }

Once you've defined your custom transport, you may register it via the `extend` method provided by the `Mail` facade. Typically, this should be done within the `boot` method of your application's `AppServiceProvider` service provider. A `$config` argument will be passed to the closure provided to the `extend` method. This argument will contain the configuration array defined for the mailer in the application's `config/mail.php` configuration file:

커스텀 전송 클래스를 정의하고 나면 `Mail` 파사드에서 제공하는 `extend` 메소드를 사용하여 이를 등록해야합니다. 일반적으로 이 작업은 애플리케이션의 `AppServiceProvider` 의 `boot` 메소드 안에서 수행됩니다. `extend` 메소드에 전달되는 클로저에는 `$config` 인자가 전달됩니다. 이 인자는 애플리케이션의 `config/mail.php` 설정 파일에 등록되어 있는 메일 관련 설정 배열이 들어 있습니다. 

    use App\Mail\MailchimpTransport;
    use Illuminate\Support\Facades\Mail;

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Mail::extend('mailchimp', function (array $config = []) {
            return new MailchimpTransport(/* ... */);
        });
    }

Once your custom transport has been defined and registered, you may create a mailer definition within your application's `config/mail.php` configuration file that utilizes the new transport:

커스텀 전송 클래스를 정의하고 등록하였다면, `config/mail.php` 설정 파일에서 새로운 전송방식을 사용할 수 있습니다.

    'mailchimp' => [
        'transport' => 'mailchimp',
        // ...
    ],

<a name="additional-symfony-transports"></a>
### Additional Symfony Transports
### 추가적인 Symfony 전송기능

Laravel includes support for some existing Symfony maintained mail transports like Mailgun and Postmark. However, you may wish to extend Laravel with support for additional Symfony maintained transports. You can do so by requiring the necessary Symfony mailer via Composer and registering the transport with Laravel. For example, you may install and register the "Brevo" (formerly "Sendinblue") Symfony mailer:

라라벨은 Symfony가 관리하는 Mailgun 과 Postmark 와 같은 메일 전송 지원을 포함하고 있습니다. 하지만 추가적인 Symfony 메일 전송기능을 사용할 수도 있습니다. 컴포저를 통해서 필요한 Symfony 메일러를 설치하고 라라벨에 등록하면 됩니다. 예를들어 다음과 같이 "Brevo" (이전에는 "Sendinblue"라고 하던) Symfony 메일러를 설치하고 등록할 수 있습니다. 

```none
composer require symfony/brevo-mailer symfony/http-client
```


Once the Brevo mailer package has been installed, you may add an entry for your Brevo API credentials to your application's `services` configuration file:

Brevo 메일러 패키지를 설치하고 나면 `services` 설정 파일에 다음과 같이 Brevo API 를 사용하기 위한 인증정보를 추가하면 됩니다.

    'brevo' => [
        'key' => 'your-api-key',
    ],

Next, you may use the `Mail` facade's `extend` method to register the transport with Laravel. Typically, this should be done within the `boot` method of a service provider:

다음으로 `Mail` 파사드의 `extend` 메소드를 사용하여 라라벨에 추가적인 전송 기능을 등록할 수 있습니다. 일반적으로 이 작업은 서비스프로바이더의 `boot` 메소드 안에서 수행되어야 합니다.

    use Illuminate\Support\Facades\Mail;
    use Symfony\Component\Mailer\Bridge\Brevo\Transport\BrevoTransportFactory;
    use Symfony\Component\Mailer\Transport\Dsn;

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Mail::extend('brevo', function () {
            return (new BrevoTransportFactory)->create(
                new Dsn(
                    'brevo+api',
                    'default',
                    config('services.brevo.key')
                )
            );
        });
    }

Once your transport has been registered, you may create a mailer definition within your application's config/mail.php configuration file that utilizes the new transport:

전송기능을 등록하였다면, `config/mail.php` 설정 파일에서 새로운 전송기능을 사용할 수 있습니다.

    'brevo' => [
        'transport' => 'brevo',
        // ...
    ],
