# Laravel Cashier Paddle
# 라라벨 캐셔 패들

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Upgrading Cashier](#upgrading-cashier)
- [캐셔 업그레이드](#upgrading-cashier)
- [Installation](#installation)
- [설치하기](#installation)
- [Configuration](#configuration)
- [환경설정](#configuration)
    - [Billable Model](#billable-model)
    - [청구 가능 모델](#billable-model)
    - [API Keys](#api-keys)
    - [API Keys](#api-keys)
    - [Paddle JS](#paddle-js)
    - [Paddle JS](#paddle-js)
    - [Currency Configuration](#currency-configuration)
    - [통화 설정하기](#currency-configuration)
- [Core Concepts](#core-concepts)
- [핵심 개념](#core-concepts)
    - [Pay Links](#pay-links)
    - [결제 링크](#pay-links)
    - [Inline Checkout](#inline-checkout)
    - [인라인 체크아웃](#inline-checkout)
    - [User Identification](#user-identification)
    - [사용자 식별](#user-identification)
- [Prices](#prices)
- [가격](#prices)
- [Customers](#customers)
- [고객](#customers)
    - [Customer Defaults](#customer-defaults)
    - [고객 기본값](#customer-defaults)
- [Subscriptions](#subscriptions)
- [구독](#subscriptions)
    - [Creating Subscriptions](#creating-subscriptions)
    - [구독 생성](#creating-subscriptions)
    - [Checking Subscription Status](#checking-subscription-status)
    - [구독 상태 확인](#checking-subscription-status)
    - [Subscription Single Charges](#subscription-single-charges)
    - [일회성 구독 청구](#subscription-single-charges)
    - [Updating Payment Information](#updating-payment-information)
    - [결제 정보 업데이트](#updating-payment-information)
    - [Changing Plans](#changing-plans)
    - [플랜 변경](#changing-plans)
    - [Subscription Quantity](#subscription-quantity)
    - [구독 수량](#subscription-quantity)
    - [Pausing Subscriptions](#pausing-subscriptions)
    - [구독 일시 중지](#pausing-subscriptions)
    - [Cancelling Subscriptions](#cancelling-subscriptions)
    - [구독 취소](#cancelling-subscriptions)
- [Subscription Trials](#subscription-trials)
- [구독 평가판](#subscription-trials)
    - [With Payment Method Up Front](#with-payment-method-up-front)
    - [결제수단 등록 후 시작하기](#with-payment-method-up-front)
    - [Without Payment Method Up Front](#without-payment-method-up-front)
    - [결제수단 등록없이 시작하기](#without-payment-method-up-front)
- [Handling Paddle Webhooks](#handling-paddle-webhooks)
- [패들 웹훅 처리](#handling-paddle-webhooks)
    - [Defining Webhook Event Handlers](#defining-webhook-event-handlers)
    - [Webhook 이벤트 핸들러 정의](#defining-webhook-event-handlers)
    - [Failed Subscriptions](#handling-failed-subscriptions)
    - [실패한 구독](#handling-failed-subscriptions)
    - [Verifying Webhook Signatures](#verifying-webhook-signatures)
    - [Webhook 서명 확인](#verifying-webhook-signatures)
- [Single Charges](#single-charges)
- [Single Charges](#single-charges)
    - [Simple Charge](#simple-charge)
    - [Simple Charge](#simple-charge)
    - [Charging Products](#charging-products)
    - [Charging Products](#charging-products)
    - [Refunding Orders](#refunding-orders)
    - [Refunding Orders](#refunding-orders)
- [Receipts](#receipts)
- [Receipts](#receipts)
    - [Past & Upcoming Payments](#past-and-upcoming-payments)
    - [Past & Upcoming Payments](#past-and-upcoming-payments)
- [Handling Failed Payments](#handling-failed-payments)
- [Handling Failed Payments](#handling-failed-payments)
- [Testing](#testing)
- [Testing](#testing)

<a name="introduction"></a>
## Introduction
## 시작하기

Laravel Cashier Paddle provides an expressive, fluent interface to [Paddle's](https://paddle.com) subscription billing services. It handles almost all of the boilerplate subscription billing code you are dreading. In addition to basic subscription management, Cashier can handle: coupons, swapping subscription, subscription "quantities", cancellation grace periods, and more.

라라벨 캐셔 패들은 [Paddle](https://paddle.com) 구독 결제 서비스에 대한 손쉽고 편리한 인터페이스를 제공합니다. 패들은 당신이 두려워하는 거의 모든 상용 구독 청구 코드를 처리합니다. 기본 구독 관리 외에도 캐셔는 쿠폰, 구독 교환, 구독 "수량", 취소 유예 기간 등을 처리 할 수 있습니다.

While working with Cashier we recommend you also refer to Paddle's [user guides](https://developer.paddle.com/guides) and [API documentation](https://developer.paddle.com/api-reference/intro).

캐셔로 작업하는 동안 Paddle의 [사용자 가이드](https://developer.paddle.com/guides) 및 [API 문서](https://developer.paddle.com/api-reference/intro)도 참조하는 것이 좋습니다.

<a name="upgrading-cashier"></a>
## Upgrading Cashier
## 캐셔 업그레이드

When upgrading to a new version of Cashier, it's important that you carefully review [the upgrade guide](https://github.com/laravel/cashier-paddle/blob/master/UPGRADE.md).

캐셔를 새 버전으로 업그레이드 할 때는 [업그레이드 가이드](https://github.com/laravel/cashier-paddle/blob/master/UPGRADE.md)를 자세히 검토하세요.

<a name="installation"></a>
## Installation
## 설치하기

First, require the Cashier package for Paddle with Composer:

먼저 Composer의 패들 용 캐셔 패키지가 필요합니다.

    composer require laravel/cashier-paddle

> {note} To ensure Cashier properly handles all Paddle events, remember to [set up Cashier's webhook handling](#handling-paddle-webhooks).

> {note} 캐셔가 모든 Paddle 이벤트를 올바르게 처리하도록하려면 [캐셔의 웹훅 처리 설정](#handling-paddle-webhooks)을 기억하세요.

#### Database Migrations
#### Database Migrations

The Cashier service provider registers its own database migration directory, so remember to migrate your database after installing the package. The Cashier migrations will create a new `customers` table. In addition, a new `subscriptions` table will be created to store all of your customer's subscriptions. Finally, a new `receipts` table will be created to store all of your receipt information:

캐셔 서비스 프로바이더는 자체적으로 가지고 있는 데이터베이스 마이그레이션 디렉토리를 등록하므로 패키지를 설치 한 후 데이터베이스를 마이그레이션해야합니다. 캐셔 마이그레이션은 새로운 `customers`테이블을 생성합니다. 또한 고객의 모든 구독을 저장하기 위해 새로운 `subscriptions`테이블이 생성됩니다. 마지막으로 모든 영수증 정보를 저장하기 위해 새로운 `receipts` 테이블이 생성됩니다.

    php artisan migrate

If you need to overwrite the migrations that ship with the Cashier package, you can publish them using the `vendor:publish` Artisan command:

캐셔 패키지와 함께 제공되는 마이그레이션을 수정해야하는 경우 Artisan 명령 `vendor:publish`를 사용하여 파일을 생성 할 수 있습니다.

    php artisan vendor:publish --tag="cashier-migrations"

If you would like to prevent Cashier's migrations from running entirely, you may use the `ignoreMigrations` provided by Cashier. Typically, this method should be called in the `register` method of your `AppServiceProvider`:

캐셔의 마이그레이션이 실행되는 것을 완전히 방지하려면 캐셔가 제공하는 `ignoreMigrations`를 사용할 수 있습니다. 일반적으로 이 메서드는 `AppServiceProvider`의`register` 메서드에서 호출해야합니다.

    use Laravel\Paddle\Cashier;

    Cashier::ignoreMigrations();

<a name="configuration"></a>
## Configuration
## 환경설정

<a name="billable-model"></a>
### Billable Model
### 청구 가능 모델

Before using Cashier, you must add the `Billable` trait to your user model definition. This trait provides various methods to allow you to perform common billing tasks, such as creating subscriptions, applying coupons and updating payment method information:

캐셔를 사용하기 전에 유저 모델에 `Billable` trait를 추가해야합니다. 이 trait는 구독 생성, 쿠폰 적용 및 결제 방법 정보 업데이트와 같은 일반적인 결제 작업을 수행 할 수있는 다양한 메서드를 제공합니다.

    use Laravel\Paddle\Billable;

    class User extends Authenticatable
    {
        use Billable;
    }

If you have billable entities that are not users, you may also add the trait to those classes:

유저가 아닌 청구 가능한 엔티티가 있는 경우 해당 클래스에 trait를 추가 할 수도 있습니다.

    use Laravel\Paddle\Billable;

    class Team extends Model
    {
        use Billable;
    }

<a name="api-keys"></a>
### API Keys
### API Keys

Next, you should configure your Paddle keys in your `.env` file. You can retrieve your Paddle API keys from the Paddle control panel:

다음으로 `.env` 파일에서 패들의 키를 구성해야합니다. Paddle 제어판에서 Paddle API 키를 찾을 수 있습니다.

    PADDLE_VENDOR_ID=your-paddle-vendor-id
    PADDLE_VENDOR_AUTH_CODE=your-paddle-vendor-auth-code
    PADDLE_PUBLIC_KEY="your-paddle-public-key"

<a name="paddle-js"></a>
### Paddle JS
### Paddle JS

Paddle relies on its own JavaScript library to initiate the Paddle checkout widget. You can load the JavaScript library by placing the `@paddleJS` directive right before your application layout's closing `</head>` tag:

Paddle은 자체 JavaScript 라이브러리를 사용하여 Paddle 체크 아웃 위젯을 생성합니다. 애플리케이션 레이아웃의 닫는 `</ head>`태그 바로 앞에 `@paddleJS` 지시문을 배치하여 JavaScript 라이브러리를 로드 할 수 있습니다.

    <head>
        ...

        @paddleJS
    </head>

<a name="currency-configuration"></a>
### Currency Configuration
### 통화 설정하기

The default Cashier currency is United States Dollars (USD). You can change the default currency by setting the `CASHIER_CURRENCY` environment variable:

캐셔의 기본 통화는 미국 달러(USD)입니다. `CASHIER_CURRENCY` 환경 변수를 설정하여 기본 통화를 변경할 수 있습니다.

    CASHIER_CURRENCY=EUR

In addition to configuring Cashier's currency, you may also specify a locale to be used when formatting money values for display on invoices. Internally, Cashier utilizes [PHP's `NumberFormatter` class](https://www.php.net/manual/en/class.numberformatter.php) to set the currency locale:

캐셔의 통화를 설정하는 것 외에도 송장에 표시 할 화폐 값을 형식화 할 때 사용할 로케일을 지정할 수도 있습니다. 내부적으로 Cashier는 [PHP의 `NumberFormatter` 클래스](https://www.php.net/manual/en/class.numberformatter.php)를 사용하여 통화 로케일을 설정합니다.

    CASHIER_CURRENCY_LOCALE=nl_BE

> {note} In order to use locales other than `en`, ensure the `ext-intl` PHP extension is installed and configured on your server.

> {note} `en` 이외의 로케일을 사용하려면 `ext-intl` PHP 익스텐션이 서버에 설치 및 설정되어 있는지 확인하십시오.

<a name="core-concepts"></a>
## Core Concepts
## Core Concepts

<a name="pay-links"></a>
### Pay Links
### 결제 링크

Paddle lacks an extensive CRUD API to perform state changes. Therefore, most interactions with Paddle are done through [its checkout widget](https://developer.paddle.com/guides/how-tos/checkout/paddle-checkout). Before we can display the checkout widget, we will generate a "pay link" using Cashier:

Paddle에는 상태 변경을 수행하기위한 광범위한 CRUD API가 없습니다. 따라서 Paddle과의 대부분의 상호 작용은 [결제 위젯](https://developer.paddle.com/guides/how-tos/checkout/paddle-checkout)을 통해 이루어집니다. 결제 위젯을 표시하기 전에 캐셔을 사용하여 "결제 링크"를 생성합니다.

    $user = User::find(1);

    $payLink = $user->newSubscription('default', $premium = 34567)
        ->returnTo(route('home'))
        ->create();

    return view('billing', ['payLink' => $payLink]);

Cashier includes a `paddle-button` Blade component. We may pass the pay link URL to this component as a "prop". When this button is clicked, Paddle's checkout widget will be displayed:

캐셔에는 `paddle-button`블레이드 컴포넌트가 포함되어 있습니다. 결제링크 URL을 이 컴포넌트에 "prop"으로 전달할 수 있습니다. 이 버튼을 클릭하면 Paddle의 결제 위젯이 표시됩니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4">
        Subscribe
    </x-paddle-button>

By default, this will display a button with the standard Paddle styling. You can remove all Paddle styling by adding the `data-theme="none"` attribute to the component:

기본적으로 표준 패들 스타일이 적용된 버튼이 표시됩니다. 컴포넌트에 `data-theme="none"` 속성을 추가하여 모든 패들 스타일을 제거 할 수 있습니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4" data-theme="none">
        Subscribe
    </x-paddle-button>

The Paddle checkout widget is asynchronous. Once the user creates or updates a subscription within the widget, Paddle will send our application webhooks so that we may properly update the subscription state in our own database. Therefore, it's important that you properly [set up webhooks](#handling-paddle-webhooks) to accommodate for state changes from Paddle.

패들 체크 아웃 위젯은 비동기식입니다. 사용자가 위젯 내에서 구독을 생성하거나 업데이트하면 Paddle은 자체 데이터베이스에서 구독 상태를 적절하게 업데이트 할 수 있도록 애플리케이션 웹훅을 보냅니다. 따라서 Paddle의 상태 변경 요청을 받아서 처리 할 수 있도록 [웹훅 설정](#handling-paddle-webhooks)을 적절하게 설정하는 것이 중요합니다.

After a subscription state change, the delay for receiving the corresponding webhook is typically minimal but you should account for this in your application by considering that your user's subscription might not be immediately available after completing the checkout.

구독 상태가 변경된 후 해당 웹훅 수신의 지연은 일반적으로 최소화하지만 사용자의 구독이 결제 완료 후 즉시 사용 가능하지 않을 수 있다는 점을 애플리케이션에서 고려해야합니다.

For more information, you may review [the Paddle API documentation on pay link generation](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink).

자세한 내용은 [결제 링크 생성에 대한 Paddle API 문서](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)를 참고 할 수 있습니다.

<a name="inline-checkout"></a>
### Inline Checkout
### 인라인 체크아웃

If you don't want to make use of the "overlay" style checkout widget, Paddle also has an option to display the widget inline. While this approach does not allow you to adjust any of the checkout's HTML fields, it allows you to embed the widget within your application.

"오버레이"스타일의 체크아웃 위젯을 사용하지 않으려는 경우 Paddle에는 위젯을 인라인으로 표시하는 옵션도 있습니다. 이 접근 방식으로 체크 아웃의 HTML 필드를 조정할 수는 없지만 애플리케이션 내에 위젯을 포함 할 수 있습니다.

To make it easy for you to get started with inline checkout, Cashier includes a `paddle-checkout` Blade component. To get started, you should [generate a pay link](#pay-links) and pass the pay link to the component's `override` attribute:

인라인 체크아웃을 쉽게 시작할 수 있도록 캐셔에는 `paddle-checkout` 블레이드 컴포넌트가 포함되어 있습니다. 시작하려면 [결제 링크를 생성](#pay-links)하고 결제 링크를 컴포넌트의 `override` 속성에 전달하면 됩니다.

    <x-paddle-checkout :override="$payLink" class="w-full" />

To adjust the height of the inline checkout component, you may pass the `height` attribute to the Blade component:

인라인 결제 컴포넌트의 높이를 조정하려면 블레이드 컴포넌트에 `height` 속성을 전달하면 됩니다.

    <x-paddle-checkout :override="$payLink" class="w-full" height="500" />

#### Inline Checkout Without Pay Links
#### 결제링크가 없는 인라인 체크아웃

Alternatively, you may customize the widget with custom options instead of using a pay link:

또는 결제 링크를 사용하는 대신 사용자 지정 옵션으로 위젯을 커스터마이징할 수 있습니다.

    $options = [
        'product' => $productId,
        'title' => 'Product Title',
    ];

    <x-paddle-checkout :options="$options" class="w-full" />

Please consult Paddle's [guide on Inline Checkout](https://developer.paddle.com/guides/how-tos/checkout/inline-checkout) as well as their [Parameter Reference](https://developer.paddle.com/reference/paddle-js/parameters) for further details on available options.

패들에서 사용 가능한 옵션에 대한 자세한 내용은 [Inline Checkout 가이드](https://developer.paddle.com/guides/how-tos/checkout/inline-checkout) 및 해당 [Parameter Reference](https://developer.paddle.com)를 참조하세요.

> {note} If you would like to also use the `passthrough` option when specifying custom options, you should provide a key / value array since Cashier will automatically handle converting the array to a JSON string. In addition, the `customer_id` passthrough option is reserved for internal Cashier usage.

> {note} 커스텀 옵션을 지정할 때 `passthrough` 옵션도 사용하려면 캐셔가 배열을 JSON 문자열로 자동으로 변환하므로 키 / 값 배열을 제공해야합니다. 또한 `customer_id` 패스 스루 옵션은 내부 캐셔 사용을 위해 예약되어 있습니다.

<a name="user-identification"></a>
### User Identification
### 사용자 식별

In contrast to Stripe, Paddle users are unique across the whole of Paddle, not unique per Paddle account. Because of this, Paddle's API's do not currently provide a method to update a user's details such as their email address. When generating pay links, Paddle identifies users using the `customer_email` parameter. When creating a subscription, Paddle will try to match the user provided email to an existing Paddle user.

Stripe와 달리 Paddle 사용자는 Paddle 계정별로 고유하지 않고 Paddle 전체에서 고유합니다. 이 때문에 Paddle의 API는 현재 이메일 주소와 같은 사용자 세부 정보를 업데이트하는 방법을 제공하지 않습니다. 결제 링크를 생성 할 때 Paddle은 `customer_email` 매개 변수를 사용하여 사용자를 식별합니다. 구독을 만들 때 Paddle은 사용자가 제공 한 이메일을 기존 Paddle 사용자와 일치 시키려고합니다.

In light of this behavior, there are some important things to keep in mind when using Cashier and Paddle. First, you should be aware that even though subscriptions in Cashier are tied to the same application user, **they could be tied to different users within Paddle's internal systems**. Secondly, each subscription has its own connected payment method information and could also have different email addresses within Paddle's internal systems (depending on which email was assigned to the user when the subscription was created).

이 동작에 비추어 캐셔과 패들을 사용할 때 유의해야 할 몇 가지 중요한 사항이 있습니다. 첫째, 캐셔의 구독이 동일한 애플리케이션 사용자에게 연결되어 있더라도 **Paddle의 내부 시스템 내에서 다른 사용자에게 연결될 수 있음** 을 알아야합니다. 둘째, 각 구독에는 자체 연결된 결제 방법 정보가 있으며 Paddle의 내부 시스템 내에서 다른 이메일 주소를 가질 수도 있습니다 (구독을 만들 때 사용자에게 할당 된 이메일에 따라 다름).

Therefore, when displaying subscriptions you should always inform the user which email address or payment method information is connected to the subscription on a per-subscription basis. Retrieving this information can be done with the following methods on the `Subscription` model:

따라서 구독을 표시 할 때는 구독별로 구독에 연결된 이메일 주소 또는 결제 방법 정보를 항상 사용자에게 알려야합니다. 이 정보 검색은 `Subscription` 모델에서 다음 메서드를 통해 실행 할 수 있습니다.

    $subscription = $user->subscription('default');

    $customerEmailAddress = $subscription->paddleEmail();
    $paymentMethod = $subscription->paymentMethod();
    $cardBrand = $subscription->cardBrand();
    $cardLastFour = $subscription->cardLastFour();
    $cardExpirationDate = $subscription->cardExpirationDate();

There is currently no way to modify a user's email address through the Paddle API. When a user wants to update their email address within Paddle, the only way for them to do so is to contact Paddle customer support. When communicating with Paddle, they need to provide the `paddleEmail` value of the subscription to assist Paddle in updating the correct user.

현재는 Paddle API를 통해 사용자의 이메일 주소를 수정 할 수있는 방법이 없습니다. 사용자가 Paddle 내에서 자신의 이메일 주소를 업데이트하려는 경우, 그렇게 할 수있는 유일한 방법은 Paddle 고객 지원에 문의하는 것입니다. Paddle과 통신 할 때 Paddle이 올바른 사용자를 업데이트하는 데 도움이 되도록 구독의 `paddleEmail`값을 제공해야합니다.

<a name="prices"></a>
## Prices
## 가격

Paddle allows you to customize prices per currency, essentially allowing you to configure different prices for different countries. Cashier Paddle allows you to retrieve all of the prices for a given product using the `productPrices` method:

Paddle을 사용하면 통화별로 가격을 커스터마이징 할 수 있으므로 기본적으로 국가마다 다른 가격을 설정 할 수 있습니다. Cashier Paddle을 사용하면 `productPrices` 메소드를 사용하여 특정 제품의 모든 가격을 검색 할 수 있습니다.

    use Laravel\Paddle\Cashier;

    // Retrieve prices for two products...
    $prices = Cashier::productPrices([123, 456]);

The currency will be determined based on the IP address of the request; however, you may optionally provide a specific country to retrieve prices for:

통화는 요청의 IP 주소에 따라 결정됩니다. 그러나 필요하면 특정 국가를 지정하여 가격을 조회 할 수 있습니다.

    use Laravel\Paddle\Cashier;

    // Retrieve prices for two products...
    $prices = Cashier::productPrices([123, 456], ['customer_country' => 'BE']);

After retrieving the prices you may display them however you wish:

가격을 조회 한 후 원하는대로 표시 할 수 있습니다.

    <ul>
        @foreach ($prices as $price)
            <li>{{ $price->product_title }} - {{ $price->price()->gross() }}</li>
        @endforeach
    </ul>

You may also display the net price (excludes tax) and display the tax amount separately:

정가 (세금 제외)를 표시하고 세액을 별도로 표시 할 수도 있습니다.

    <ul>
        @foreach ($prices as $price)
            <li>{{ $price->product_title }} - {{ $price->price()->net() }} (+ {{ $price->price()->tax() }} tax)</li>
        @endforeach
    </ul>

If you retrieved prices for subscription plans you can display their initial and recurring price separately:

구독 플랜의 가격을 조회 한 경우 최초 및 연장결제의 가격을 별도로 표시 할 수 있습니다.

    <ul>
        @foreach ($prices as $price)
            <li>{{ $price->product_title }} - Initial: {{ $price->initialPrice()->gross() }} - Recurring: {{ $price->recurringPrice()->gross() }}</li>
        @endforeach
    </ul>

For more information, [check Paddle's API documentation on prices](https://developer.paddle.com/api-reference/checkout-api/prices/getprices).

자세한 내용은 [가격에 대한 Paddle의 API 문서](https://developer.paddle.com/api-reference/checkout-api/prices/getprices)를 참고하세요.

#### Customers
#### 고객

If a user is already a customer and you would like to display the prices that apply to that customer, you may do so by retrieving the prices directly from the customer instance:

사용자가 이미 고객이고 해당 고객에게 적용되는 가격을 표시하려는 경우 고객 인스턴스에서 직접 가격을 조회 할 수 있습니다.

    use App\User;

    // Retrieve prices for two products...
    $prices = User::find(1)->productPrices([123, 456]);

Internally, Cashier will use the user's [`paddleCountry` method](#customer-defaults) to retrieve the prices in their currency. So, for example, a user living in the United States will see prices in USD while a user in Belgium will see prices in EUR. If no matching currency can be found the default currency of the product will be used. You can customize all prices of a product or subscription plan in the Paddle control panel.

내부적으로 캐셔는 사용자의 [`paddleCountry` 메소드](#customer-defaults)를 사용하여 통화로 가격을 조회합니다. 예를 들어 미국에 거주하는 사용자는 USD로 가격이 표시되고 벨기에 사용자는 EUR로 가격이 표시됩니다. 일치하는 통화를 찾을 수 없는 경우 제품의 기본 통화가 사용됩니다. 패들 제어판에서 제품 또는 구독 플랜의 모든 가격을 커스터마이징 할 수 있습니다.

#### Coupons
#### 쿠폰

You may also choose to display prices after a coupon reduction. When calling the `productPrices` method, coupons may be passed as a comma delimited string:

쿠폰 할인 후 가격을 표시하도록 선택할 수도 있습니다. `productPrices` 메소드를 호출 할 때 쿠폰이 쉼표로 구분 된 문자열로 전달 될 수 있습니다.


    use Laravel\Paddle\Cashier;

    $prices = Cashier::productPrices([123, 456], ['coupons' => 'SUMMERSALE,20PERCENTOFF']);

Then, display the calculated prices using the `price` method:

그런 다음 `price` 메서드를 사용하여 계산 된 가격을 표시합니다.

    <ul>
        @foreach ($prices as $price)
            <li>{{ $price->product_title }} - {{ $price->price()->gross() }}</li>
        @endforeach
    </ul>

You may display the original listed prices (without coupon discounts) using the `listPrice` method:

`listPrice`메소드를 사용하여 원래 나열된 가격 (쿠폰 할인 제외)을 표시 할 수 있습니다.

    <ul>
        @foreach ($prices as $price)
            <li>{{ $price->product_title }} - {{ $price->listPrice()->gross() }}</li>
        @endforeach
    </ul>

> {note} When using the prices API, Paddle only allows to apply coupons to one-time purchase products and not to subscription plans.

> {note} 가격 API를 사용할 때 Paddle은 일회성 구매 제품에만 쿠폰을 적용 할 수 있으며 구독 플랜에는 적용 할 수 없습니다.

<a name="customers"></a>
## Customers
## 고객

<a name="customer-defaults"></a>
### Customer Defaults
### 고객 기본값

Cashier allows you to set some useful defaults for your customer when creating pay links. Setting these defaults allow you to pre-fill a customer's email address, country, and postcode so that they can immediately move on to the payment portion of the checkout widget. You can set these defaults by overriding the following methods on your billable user:

캐셔을 사용하면 결제 링크를 만들 때 고객에게 유용한 기본값을 설정할 수 있습니다. 이러한 기본값을 설정하면 고객이 결제 위젯의 결제 부분으로 즉시 이동할 수 있도록 고객의 이메일 주소, 국가 및 우편 번호를 미리 채워 둘 수 있습니다. 청구 가능한 사용자에 대해 다음 메서드를 재정의하여 이러한 기본값을 설정할 수 있습니다.

    /**
     * Get the customer's email address to associate with Paddle.
     *
     * @return string|null
     */
    public function paddleEmail()
    {
        return $this->email;
    }

    /**
     * Get the customer's country to associate with Paddle.
     *
     * This needs to be a 2 letter code. See the link below for supported countries.
     *
     * @return string|null
     * @link https://developer.paddle.com/reference/platform-parameters/supported-countries
     */
    public function paddleCountry()
    {
        //
    }

    /**
     * Get the customer's postcode to associate with Paddle.
     *
     * See the link below for countries which require this.
     *
     * @return string|null
     * @link https://developer.paddle.com/reference/platform-parameters/supported-countries#countries-requiring-postcode
     */
    public function paddlePostcode()
    {
        //
    }

These defaults will be used for every action in Cashier that generates a [pay link](#pay-links).

이러한 기본값은 [결제 링크](#pay-links)를 생성하는 캐셔의 모든 작업에 사용됩니다.

<a name="subscriptions"></a>
## Subscriptions
## 구독

<a name="creating-subscriptions"></a>
### Creating Subscriptions
### 구독 생성

To create a subscription, first retrieve an instance of your billable model, which typically will be an instance of `App\User`. Once you have retrieved the model instance, you may use the `newSubscription` method to create the model's subscription pay link:

구독을 생성하려면 먼저 청구 가능한 모델의 인스턴스를 조회하세요. 일반적으로 `App\User`의 인스턴스가 됩니다. 모델 인스턴스를 조회 한 후에는 `newSubscription` 메서드를 사용하여 모델의 구독 결제 링크를 만들 수 있습니다.

    $user = User::find(1);

    $payLink = $user->newSubscription('default', $premium = 12345)
        ->returnTo(route('home'))
        ->create();

    return view('billing', ['payLink' => $payLink]);

The first argument passed to the `newSubscription` method should be the name of the subscription. If your application only offers a single subscription, you might call this `default` or `primary`. The second argument is the specific plan the user is subscribing to. This value should correspond to the plan's identifier in Paddle. The `returnTo` method accepts a URL that your user will be redirected to after they successfully complete the checkout.

`newSubscription` 메소드에 전달 된 첫 번째 인수는 구독의 이름이어야합니다. 애플리케이션이 단일 구독 만 제공하는 경우이를 `default` 또는 `primary`라고 부를 수 있습니다. 두 번째 인수는 사용자가 구독하는 특정 플랜입니다. 이 값은 Paddle의 플랜 식별자와 일치해야합니다. `returnTo` 메소드는 사용자가 체크 아웃을 성공적으로 완료 한 후 리디렉션되는 URL을 입력받습니다.

The `create` method will create a pay link which you can use to generate a payment button. The payment button can be generated using the `paddle-button` Blade component that ships with Cashier Paddle:

`create`메서드는 결제 버튼을 생성하는 데 사용할 수있는 결제 링크를 생성합니다. 결제 버튼은 캐셔 패들과 함께 제공되는 `paddle-button` 블레이드 컴포넌트를 사용하여 생성 할 수 있습니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4">
        Subscribe
    </x-paddle-button>

After the user has finished their checkout, a `subscription_created` webhook will be dispatched from Paddle. Cashier will receive this webhook and setup the subscription for your customer. In order to make sure all webhooks are properly received and handled by your application, ensure you have properly [setup webhook handling](#handling-paddle-webhooks).

사용자가 결제를 완료하면 `subscription_created` 웹훅이 Paddle에서 발송됩니다. 캐셔는 이 웹훅을 받고 고객의 구독을 설정합니다. 모든 웹훅이 애플리케이션에서 제대로 수신되고 처리되는지 확인하려면 [웹훅 처리 설정](#handling-paddle-webhooks)이 올바르게 설정되어 있는지 확인하세요.

#### Additional Details
#### 추가적인 세부사항

If you would like to specify additional customer or subscription details, you may do so by passing them as a key / value array to the `create` method:

추가적인 고객 또는 구독의 세부 사항를 지정하려면 키 / 값 배열로 `create` 메소드에 전달하면됩니다.

    $payLink = $user->newSubscription('default', $monthly = 12345)
        ->returnTo(route('home'))
        ->create([
            'vat_number' => $vatNumber,
        ]);

To learn more about the additional fields supported by Paddle, check out Paddle's documentation on [generating pay links](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink).

Paddle에서 지원하는 추가 필드에 대해 자세히 알아 보려면 [결제 링크 생성](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)에 대한 Paddle의 문서를 확인하세요.

#### Coupons
#### 쿠폰

If you would like to apply a coupon when creating the subscription, you may use the `withCoupon` method:

구독을 생성 할 때 쿠폰을 적용하려면 `withCoupon` 메소드를 사용할 수 있습니다.

    $payLink = $user->newSubscription('default', $monthly = 12345)
        ->returnTo(route('home'))
        ->withCoupon('code')
        ->create();

#### Metadata
#### Metadata

You can also pass an array of metadata using the `withMetadata` method:

`withMetadata` 메소드를 사용하여 메타 데이터 배열을 전달할 수도 있습니다.

    $payLink = $user->newSubscription('default', $monthly = 12345)
        ->returnTo(route('home'))
        ->withMetadata(['key' => 'value'])
        ->create();

> {note} When providing metadata, please avoid using `subscription_name` as a metadata key. This key is reserved for internal use by Cashier.

> {note} 메타 데이터 제공시 `subscription_name`을 메타 데이터 키로 사용하지 마세요. 이 키는 캐셔 내부 용으로 예약되어 있습니다.

<a name="checking-subscription-status"></a>
### Checking Subscription Status
### 구독 상태 확인

Once a user is subscribed to your application, you may check their subscription status using a variety of convenient methods. First, the `subscribed` method returns `true` if the user has an active subscription, even if the subscription is currently within its trial period:

사용자가 귀하의 애플리케이션을 구독하면 다양하고 편리한 메서드들을 사용하여 구독 상태를 확인할 수 있습니다. 먼저 구독이 현재 평가 기간 내에 있더라도 사용자에게 활성 구독이 있는 경우 `subscribed` 메소드는 `true`를 반환합니다.

    if ($user->subscribed('default')) {
        //
    }

The `subscribed` method also makes a great candidate for a [route middleware](/docs/{{version}}/middleware), allowing you to filter access to routes and controllers based on the user's subscription status:

`subscribed` 메서드는 또한 [route middleware](/docs/{{version}}/middleware)에 대한 훌륭한 보조도구가 되어 사용자의 구독 상태에 따라 라우트 및 컨트롤러에 대한 접근을 필터링 할 수 있습니다.

    public function handle($request, Closure $next)
    {
        if ($request->user() && ! $request->user()->subscribed('default')) {
            // This user is not a paying customer...
            return redirect('billing');
        }

        return $next($request);
    }

If you would like to determine if a user is still within their trial period, you may use the `onTrial` method. This method can be useful for displaying a warning to the user that they are still on their trial period:

사용자가 아직 평가 기간 내에 있는지 확인하려면 `onTrial`메서드를 사용할 수 있습니다. 이 메서드는 사용자에게 아직 평가판 사용 중이라는 경고를 표시하는 데 유용 할 수 있습니다.

    if ($user->subscription('default')->onTrial()) {
        //
    }

The `subscribedToPlan` method may be used to determine if the user is subscribed to a given plan based on a given Paddle plan ID. In this example, we will determine if the user's `default` subscription is actively subscribed to the monthly plan:

`subscribedToPlan` 메소드는 주어진 Paddle 계획 ID를 기반으로 사용자가 주어진 계획에 가입했는지 여부를 결정하는 데 사용될 수 있습니다. 이 예제에서는 사용자의 `default` 구독이 월간 요금제에 적극적으로 구독하고 있는지 확인합니다.

    if ($user->subscribedToPlan($monthly = 12345, 'default')) {
        //
    }

By passing an array to the `subscribedToPlan` method, you may determine if the user's `default` subscription is actively subscribed to the monthly or the yearly plan:

배열을 `subscribedToPlan` 메소드에 전달하면 사용자의 `default` 구독이 월간 또는 연간 요금제를 적극적으로 구독하고 있는지 확인할 수 있습니다.

    if ($user->subscribedToPlan([$monthly = 12345, $yearly = 54321], 'default')) {
        //
    }

The `recurring` method may be used to determine if the user is currently subscribed and is no longer within their trial period:

`recurring`메서드를 사용하여 사용자가 현재 구독하고 있으며 더 이상 평가 기간 내에 있지 않은지 확인할 수 있습니다.

    if ($user->subscription('default')->recurring()) {
        //
    }

#### Cancelled Subscription Status
#### 취소된 구독상태

To determine if the user was once an active subscriber, but has cancelled their subscription, you may use the `cancelled` method:

사용자가 한때 활성 구독자 였지만 구독을 취소했는지 확인하려면 `cancelled` 메소드를 사용할 수 있습니다.

    if ($user->subscription('default')->cancelled()) {
        //
    }

You may also determine if a user has cancelled their subscription, but are still on their "grace period" until the subscription fully expires. For example, if a user cancels a subscription on March 5th that was originally scheduled to expire on March 10th, the user is on their "grace period" until March 10th. Note that the `subscribed` method still returns `true` during this time:

또한 사용자가 구독을 취소했지만 구독이 완전히 만료 될 때까지의 "유예 기간"에 남아 있는지 확인할 수도 있습니다. 예를 들어 사용자가 원래 3 월 10 일에 만료 될 예정이었던 3 월 5 일에 구독을 취소하면 사용자는 3 월 10 일까지 "유예 기간"중에 있습니다. 이 기간 동안 `subscribed` 메소드는 여전히 `true`를 반환합니다.

    if ($user->subscription('default')->onGracePeriod()) {
        //
    }

To determine if the user has cancelled their subscription and is no longer within their "grace period", you may use the `ended` method:

사용자가 구독을 취소했고 더 이상 "유예 기간"에 있지 않은지 확인하려면 `ended`메소드를 사용할 수 있습니다.

    if ($user->subscription('default')->ended()) {
        //
    }

#### Subscription Scopes
#### 구독 스코프

Most subscription states are also available as query scopes so that you may easily query your database for subscriptions that are in a given state:

대부분의 구독 상태는 쿼리 스코프로도 사용할 수 있으므로 지정한 상태의 구독을 데이터베이스에서 쉽게 조회 할 수 있습니다.

    // Get all active subscriptions...
    $subscriptions = Subscription::query()->active()->get();

    // Get all of the cancelled subscriptions for a user...
    $subscriptions = $user->subscriptions()->cancelled()->get();

A complete list of available scopes is available below:

사용 가능한 스코프의 전체 목록은 아래에서 확인할 수 있습니다.

    Subscription::query()->active();
    Subscription::query()->onTrial();
    Subscription::query()->notOnTrial();
    Subscription::query()->pastDue();
    Subscription::query()->recurring();
    Subscription::query()->ended();
    Subscription::query()->paused();
    Subscription::query()->notPaused();
    Subscription::query()->onPausedGracePeriod();
    Subscription::query()->notOnPausedGracePeriod();
    Subscription::query()->cancelled();
    Subscription::query()->notCancelled();
    Subscription::query()->onGracePeriod();
    Subscription::query()->notOnGracePeriod();

#### Past Due Status
#### 연체 상태

If a payment fails for a subscription, it will be marked as `past_due`. When your subscription is in this state it will not be active until the customer has updated their payment information. You may determine if a subscription is past due using the `pastDue` method on the subscription instance:

구독 결제에 실패하면 `past_due`로 표시됩니다. 구독이 이 상태이면 고객이 결제 정보를 업데이트 할 때까지 활성화되지 않습니다. 구독 인스턴스에서 `pastDue` 메소드를 사용하여 구독 기한이 지난 지 확인할 수 있습니다.

    if ($user->subscription('default')->pastDue()) {
        //
    }

When a subscription is past due, you should instruct the user to [update their payment information](#updating-payment-information). You may configure how past due subscriptions are handled in your [Paddle subscription settings](https://vendors.paddle.com/subscription-settings).

구독 기한이 지난 경우 사용자에게 [결제 정보를 업데이트](#updating-payment-information)하도록 안내해야합니다. [패들 구독 설정](https://vendors.paddle.com/subscription-settings)에서 기한이 지난 구독을 처리하는 방법을 설정 할 수 있습니다.

If you would like subscriptions to still be considered active when they are `past_due`, you may use the `keepPastDueSubscriptionsActive` method provided by Cashier. Typically, this method should be called in the `register` method of your `AppServiceProvider`:

구독이 `past_due` 인 경우에도 활성 상태로 간주되도록하려면 캐셔에서 제공하는 `keepPastDueSubscriptionsActive` 메서드를 사용할 수 있습니다. 일반적으로 이 메서드는 `AppServiceProvider`의 `register` 메서드에서 호출해야합니다.

    use Laravel\Paddle\Cashier;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        Cashier::keepPastDueSubscriptionsActive();
    }

> {note} When a subscription is in a `past_due` state it cannot be changed until payment information has been updated. Therefore, the `swap` and `updateQuantity` methods will throw an exception when the subscription is in a `past_due` state.

> {note} 구독이 `past_due` 상태 인 경우 결제 정보가 업데이트 될 때까지 변경할 수 없습니다. 따라서 구독이 `past_due`상태 일 때 `swap` 및 `updateQuantity` 메소드는 예외를 발생시킵니다.

<a name="subscription-single-charges"></a>
### Subscription Single Charges
### 일회성 구독 청구

Subscription single charges allow you to charge subscribers with a one-time charge on top of their subscriptions:

일회성 구독 요금을 사용하면 구독에 추가로 일회성 요금을 구독자에게 청구 할 수 있습니다.

    $response = $user->subscription('default')->charge(12.99, 'Support Add-on');

In contrast to [single charges](#single-charges), this method will immediately charge the customer's stored payment method for the subscription. The charge amount is always in the currency of which the subscription currently is set to.

[일회성 청구](#single-charges)와 달리 이 방법은 구독에 대해 고객의 저장된 결제 수단으로 즉시 청구됩니다. 청구 금액은 항상 구독이 현재 설정된 통화로 표시됩니다.

<a name="updating-payment-information"></a>
### Updating Payment Information
### 결제 정보 업데이트

Paddle always saves a payment method per subscription. If you want to update the default payment method for a subscription, you should first generate a subscription "update URL" using the `updateUrl` method on the subscription model:

Paddle은 항상 구독 마다 결제 방법을 저장합니다. 구독에 대한 기본 결제 수단을 업데이트하려면 먼저 구독 모델에서 `updateUrl`메서드를 사용하여 구독의 '업데이트용 URL'을 생성해야합니다.

    $user = App\User::find(1);

    $updateUrl = $user->subscription('default')->updateUrl();

Then, you may use the generated URL in combination with Cashier's provided `paddle-button` Blade component to allow the user to initiate the Paddle widget and update their payment information:

그런 다음 생성 된 URL을 캐셔가 제공하는 `paddle-button` 블레이드 컴포넌트와 함께 사용하여 사용자가 패들 위젯을 시작하고 결제 정보를 업데이트 할 수 있습니다.

    <x-paddle-button :url="$updateUrl" class="px-8 py-4">
        Update Card
    </x-paddle-button>

When a user has finished updating their information, a `subscription_updated` webhook will be dispatched by Paddle and the subscription details will be updated in your application's database.

사용자가 정보 업데이트를 완료하면 `subscription_updated` 웹훅이 Paddle에 의해 전달되고 구독 세부 정보가 애플리케이션의 데이터베이스에서 업데이트됩니다.

<a name="changing-plans"></a>
### Changing Plans
### 플랜 변경

After a user has subscribed to your application, they may occasionally want to change to a new subscription plan. To swap a user to a new subscription, you should pass the Paddle plan's identifier to the subscription's `swap` method:

사용자가 애플리케이션을 구독 한 후 때때로 새 구독 플랜으로 변경하고 싶을 수 있습니다. 사용자를 새 구독으로 바꾸려면 Paddle 요금제의 식별자를 구독의 `swap` 메서드에 전달해야합니다.

    $user = App\User::find(1);

    $user->subscription('default')->swap($premium = 34567);

If the user is on trial, the trial period will be maintained. Also, if a "quantity" exists for the subscription, that quantity will also be maintained.

사용자가 평가판을 사용중인 경우 평가 기간이 유지됩니다. 또한 구독에 대한 "수량"이있는 경우 해당 수량도 유지됩니다.

If you would like to swap plans and cancel any trial period the user is currently on, you may use the `skipTrial` method:

요금제를 바꾸고 사용자가 현재 사용중인 평가판 기간을 취소하려면 `skipTrial` 메서드를 사용할 수 있습니다.

    $user->subscription('default')
            ->skipTrial()
            ->swap($premium = 34567);

If you would like to swap plans and immediately invoice the user instead of waiting for their next billing cycle, you may use the `swapAndInvoice` method:

다음 결제주기를 기다리지 않고 요금제를 바꾸고 즉시 사용자에게 인보이스를 보내려면 `swapAndInvoice` 메서드를 사용할 수 있습니다.

    $user = App\User::find(1);

    $user->subscription('default')->swapAndInvoice($premium = 34567);

#### Prorations
#### 비례 배분하기

By default, Paddle prorates charges when swapping between plans. The `noProrate` method may be used to update the subscription's without prorating the charges:

기본적으로 Paddle은 요금제간에 전환 할 때 요금을 비례 배분합니다. `noProrate`메서드를 사용하면 요금을 일할 계산하지 않고 구독을 업데이트 할 수 있습니다.

    $user->subscription('default')->noProrate()->swap($premium = 34567);

<a name="subscription-quantity"></a>
### Subscription Quantity
### 구독 수량

Sometimes subscriptions are affected by "quantity". For example, your application might charge $10 per month **per user** on an account. To easily increment or decrement your subscription quantity, use the `incrementQuantity` and `decrementQuantity` methods:

때때로 구독은 "수량"의 영향을받습니다. 예를 들어 애플리케이션에서 계정 당 **사용자 당** 월 10 달러를 청구 할 수 있습니다. 구독 수량을 쉽게 늘리거나 줄이려면`incrementQuantity` 및 `decrementQuantity` 메소드를 사용하십시오.

    $user = User::find(1);

    $user->subscription('default')->incrementQuantity();

    // Add five to the subscription's current quantity...
    $user->subscription('default')->incrementQuantity(5);

    $user->subscription('default')->decrementQuantity();

    // Subtract five to the subscription's current quantity...
    $user->subscription('default')->decrementQuantity(5);

Alternatively, you may set a specific quantity using the `updateQuantity` method:

또는 `updateQuantity` 메소드를 사용하여 특정 수량을 설정할 수 있습니다.

    $user->subscription('default')->updateQuantity(10);

The `noProrate` method may be used to update the subscription's quantity without prorating the charges:

`noProrate`메소드를 사용하여 요금을 일할 계산하지 않고 구독 수량을 업데이트 할 수 있습니다.

    $user->subscription('default')->noProrate()->updateQuantity(10);

<a name="pausing-subscriptions"></a>
### Pausing Subscriptions
### 구독 일시 중지

To pause a subscription, call the `pause` method on the user's subscription:

구독을 일시 중지하려면 사용자의 구독에서 `pause` 메소드를 호출합니다.

    $user->subscription('default')->pause();

When a subscription is paused, Cashier will automatically set the `paused_from` column in your database. This column is used to know when the `paused` method should begin returning `true`. For example, if a customer pauses a subscription on March 1st, but the subscription was not scheduled to recur until March 5th, the `paused` method will continue to return `false` until March 5th.

구독이 일시 중지되면 캐셔는 자동으로 데이터베이스에 `paused_from` 컬럼을 설정합니다. 이 컬럼은 `paused`메소드가 `true`를 반환하기 시작해야하는 시기를 확인하는 데 사용됩니다. 예를 들어 고객이 3월 1일에 구독을 일시 중지했지만 구독이 3월 5일까지 반복되지 않도록 예약 된 경우 `paused`메서드는 3월 5일까지 계속 `false`를 반환합니다.

You may determine if a user has paused their subscription but are still on their "grace period" using the `onPausedGracePeriod` method:

`onPausedGracePeriod`메소드를 사용하여 사용자가 구독을 일시 중지했지만 여전히 "유예 기간"에 있는지 확인할 수 있습니다.

    if ($user->subscription('default')->onPausedGracePeriod()) {
        //
    }

To resume a paused a subscription, you may call the `unpause` method on the user's subscription:

일시 중지 된 구독을 재개하려면 사용자의 구독에 대해 `unpause` 메소드를 호출 할 수 있습니다.

    $user->subscription('default')->unpause();

> {note} A subscription cannot be modified while it is paused. If you want to swap to a different plan or update quantities you must resume the subscription first.

> {note} 일시 중지 된 구독은 수정할 수 없습니다. 다른 플랜으로 교체하거나 수량을 업데이트하려면 먼저 구독을 재개해야합니다.

<a name="cancelling-subscriptions"></a>
### Cancelling Subscriptions
### 구독 취소

To cancel a subscription, call the `cancel` method on the user's subscription:

구독을 취소하려면 사용자의 구독에서 `cancel` 메서드를 호출하세요.

    $user->subscription('default')->cancel();

When a subscription is cancelled, Cashier will automatically set the `ends_at` column in your database. This column is used to know when the `subscribed` method should begin returning `false`. For example, if a customer cancels a subscription on March 1st, but the subscription was not scheduled to end until March 5th, the `subscribed` method will continue to return `true` until March 5th.

구독이 취소되면 캐셔는 자동으로 데이터베이스에 `ends_at` 컬럼을 설정합니다. 이 컬럼은 `subscribed`메소드가 `false`를 반환하기 시작해야하는 시기를 확인하는 데 사용됩니다. 예를 들어 고객이 3월 1일에 구독을 취소했지만 구독이 3월 5일까지 종료되도록 예약되지 않은 경우 `subscribed`메서드는 3월 5일까지 계속 `true`를 반환합니다.

You may determine if a user has cancelled their subscription but are still on their "grace period" using the `onGracePeriod` method:

`onGracePeriod` 메소드를 사용하여 사용자가 구독을 취소했지만 여전히 '유예 기간'에 있는지 확인할 수 있습니다.

    if ($user->subscription('default')->onGracePeriod()) {
        //
    }

If you wish to cancel a subscription immediately, you may call the `cancelNow` method on the user's subscription:

구독을 즉시 취소하려면 사용자의 구독에서 `cancelNow` 메서드를 호출 할 수 있습니다.

    $user->subscription('default')->cancelNow();

> {note} Paddle's subscriptions cannot be resumed after cancellation. If your customer wishes to resume their subscription, they will have to subscribe to a new subscription.

> {note} 취소 후에는 Paddle의 구독을 재개 할 수 없습니다. 고객이 구독을 재개하려면 새 구독을 구독해야합니다.

<a name="subscription-trials"></a>
## Subscription Trials
## 구독 평가판

<a name="with-payment-method-up-front"></a>
### With Payment Method Up Front
### 결제수단 등록 후 시작하기

> {note} While trialing and collecting payment method details up front, Paddle prevents any subscription changes such as swapping plans or updating quantities. If you want to allow a customer to swap plans during a trial the subscription must be cancelled and recreated.

> {note} 결제 수단 세부 정보를 미리 평가하고 수집하는 동안 Paddle은 요금제 교환이나 수량 업데이트와 같은 구독의 변경을 방지합니다. 평가판 중에 고객이 요금제를 교체 할 수 있도록 하려면 구독을 취소하고 다시 만들어야합니다.

If you would like to offer trial periods to your customers while still collecting payment method information up front, you should use the `trialDays` method when creating your subscription pay links:

결제 수단 정보를 미리 수집하면서 고객에게 평가판 기간을 제공하려면 구독 결제 링크를 만들 때 `trialDays`메서드를 사용해야합니다.

    $user = User::find(1);

    $payLink = $user->newSubscription('default', $monthly = 12345)
                ->returnTo(route('home'))
                ->trialDays(10)
                ->create();

    return view('billing', ['payLink' => $payLink]);

This method will set the trial period ending date on the subscription record within the database, as well as instruct Paddle to not begin billing the customer until after this date.

이 방법은 데이터베이스 내의 구독 기록에 평가 기간 종료 날짜를 설정하고 이 날짜 이후까지 고객에게 청구를 시작하지 않도록 Paddle에 지시합니다.

> {note} If the customer's subscription is not cancelled before the trial ending date they will be charged as soon as the trial expires, so you should be sure to notify your users of their trial ending date.

> {note} 평가판 종료일 이전에 고객의 구독을 취소하지 않으면 평가판이 만료되는 즉시 요금이 청구되므로 사용자에게 평가판 종료일을 알려야합니다.

You may determine if the user is within their trial period using either the `onTrial` method of the user instance or the `onTrial` method of the subscription instance. The two examples below have identical behavior:

사용자 인스턴스의 `onTrial`메서드 또는 구독 인스턴스의 `onTrial`메서드를 사용하여 사용자가 평가 기간 내에 있는지 확인할 수 있습니다. 아래 두 가지 예는 동일한 동작을 합니다.

    if ($user->onTrial('default')) {
        //
    }

    if ($user->subscription('default')->onTrial()) {
        //
    }

#### Defining Trial Days In Paddle / Cashier
#### 패들 / 캐셔에서 평가기간 정의

You may choose to define how many trial days your plan's receive in the Paddle dashboard or always pass them explicitly using Cashier. If you choose to define your plan's trial days in Paddle you should be aware that new subscriptions, including new subscriptions for a customer that had a subscription in the past, will always receive a trial period unless you explicitly call the `trialDays(0)` method.

Paddle 대시 보드에서 플랜의 평가 기간을 정의하거나 항상 캐셔을 사용하여 명시적으로 전달할 수 있습니다. Paddle에서 플랜의 평가 기간을 정의하기로 선택한 경우 과거에 구독 한 고객에 대한 새 구독을 포함하여 새로운 구독은 명시 적으로 `trialDays(0)`를 호출하지 않는 한 항상 평가 기간을 받게됩니다.

<a name="without-payment-method-up-front"></a>
### Without Payment Method Up Front
### 결제수단 등록없이 시작하기

If you would like to offer trial periods without collecting the user's payment method information up front, you may set the `trial_ends_at` column on the customer record attached to your user to your desired trial ending date. This is typically done during user registration:

사용자의 결제 수단 정보를 미리 수집하지 않고 평가 기간을 제공하려면 사용자에게 첨부 된 고객 기록의 `trial_ends_at` 컬럼을 원하는 평가판 종료 날짜로 설정할 수 있습니다. 이는 일반적으로 사용자 등록 중에 수행됩니다.

    $user = User::create([
        // Other user properties...
    ]);

    $user->createAsCustomer([
        'trial_ends_at' => now()->addDays(10)
    ]);

Cashier refers to this type of trial as a "generic trial", since it is not attached to any existing subscription. The `onTrial` method on the `User` instance will return `true` if the current date is not past the value of `trial_ends_at`:

캐셔는 이러한 유형의 평가판이 기존 구독에 연결되어 있지 않기 때문에 "일반 평가판"이라고합니다. `User` 인스턴스의 `onTrial` 메소드는 현재 날짜가 `trial_ends_at` 값을 지나지 않았다면 `true`를 반환합니다.

    if ($user->onTrial()) {
        // User is within their trial period...
    }

You may also use the `onGenericTrial` method if you wish to know specifically that the user is within their "generic" trial period and has not created an actual subscription yet:

사용자가 "일반"평가 기간 내에 있고 아직 실제 구독을 생성하지 않았음을 구체적으로 알고 싶다면 `onGenericTrial`메서드를 사용할 수도 있습니다.

    if ($user->onGenericTrial()) {
        // User is within their "generic" trial period...
    }

Once you are ready to create an actual subscription for the user, you may use the `newSubscription` method as usual:

사용자를 위한 실제 구독을 만들 준비가 되면 평소와 같이 `newSubscription` 메서드를 사용할 수 있습니다.

    $user = User::find(1);

    $payLink = $user->newSubscription('default', $monthly = 12345)
        ->returnTo(route('home'))
        ->create();

> {note} There is no way to extend or modify a trial period on a Paddle subscription after it has been created.

> {note} Paddle 구독이 생성 된 후에는 평가 기간을 연장하거나 수정할 수 없습니다.

<a name="handling-paddle-webhooks"></a>
## Handling Paddle Webhooks
## 패들 웹훅 처리

> {tip} You may use [Valet's `share` command](https://laravel.com/docs/{{version}}/valet#sharing-sites) to help test webhooks during local development.

> {tip} [Valet의 `share` 명령어](https://laravel.com/docs/{{version}}/valet#sharing-sites)를 사용하여 로컬 개발 중에 웹훅을 테스트 할 수 있습니다.

Paddle can notify your application of a variety of events via webhooks. By default, a route that points to Cashier's webhook controller is configured through the Cashier service provider. This controller will handle all incoming webhook requests.

Paddle은 웹훅을 통해 다양한 이벤트를 애플리케이션에 알릴 수 있습니다. 기본적으로 캐셔의 웹훅 컨트롤러를 가리키는 경로는 캐셔 서비스 프로바이더를 통해 구성됩니다. 이 컨트롤러는 들어오는 모든 웹훅 요청을 처리합니다.

By default, this controller will automatically handle cancelling subscriptions that have too many failed charges ([as defined by your Paddle subscription settings](https://vendors.paddle.com/subscription-settings)), subscription updates, and payment method changes; however, as we'll soon discover, you can extend this controller to handle any webhook event you like.

기본적으로 이 컨트롤러는 결제 실패가 너무 많은 구독을 취소 ([Paddle 구독 설정에 정의 됨](https://vendors.paddle.com/subscription-settings)), 구독 업데이트 및 결제 수단 변경을 자동으로 처리합니다. 그러나 곧 알게 되겠지만 이 컨트롤러를 확장하여 원하는 웹훅 이벤트를 처리 할 수 있습니다.

To ensure your application can handle Paddle webhooks, be sure to [configure the webhook URL in the Paddle control panel](https://vendors.paddle.com/alerts-webhooks). By default, Cashier's webhook controller listens to the `/paddle/webhook` URL path. The full list of all webhooks you should configure in the Paddle control panel are:

애플리케이션에서 Paddle 웹훅을 처리 할 수 있도록하려면 [Paddle 제어판에서 웹훅 URL 구성](https://vendors.paddle.com/alerts-webhooks)을 확인하세요. 기본적으로 Cashier의 웹훅 컨트롤러는 `/paddle/webhook`URL 라우트로 수신됩니다. Paddle 제어판에서 구성해야하는 모든 웹훅의 전체 목록은 다음과 같습니다.

- Subscription Created
- Subscription Updated
- Subscription Deleted
- Payment Succeeded
- Subscription Payment Succeeded

- 구독 생성
- 구독 업데이트
- 구독 삭제
- 결제 성공
- 구독 결제 성공

> {note} Make sure you protect incoming requests with Cashier's included [webhook signature verification](/docs/{{version}}/cashier-paddle#verifying-webhook-signatures) middleware.

> {note} 캐셔에 포함 된 [webhook 서명 확인](/docs/{{version}}/cashier-paddle#verifying-webhook-signatures) 미들웨어로 들어오는 요청을 보호하세요.

#### Webhooks & CSRF Protection
#### 웹훅 및 CSRF 보호

Since Paddle webhooks need to bypass Laravel's [CSRF protection](/docs/{{version}}/csrf), be sure to list the URI as an exception in your `VerifyCsrfToken` middleware or list the route outside of the `web` middleware group:

Paddle 웹훅은 Laravel의 [CSRF 보호](/docs/{{version}}/csrf)를 우회해야하므로 `VerifyCsrfToken` 미들웨어에 URI를 예외로 나열하거나 `web` 미들웨어 그룹의 외부에 라우트로 추가해야합니다.

    protected $except = [
        'paddle/*',
    ];

<a name="defining-webhook-event-handlers"></a>
### Defining Webhook Event Handlers
### Webhook 이벤트 핸들러 정의

Cashier automatically handles subscription cancellation on failed charges, but if you have additional webhook events you would like to handle, you should extend the `WebhookController`. Your method names should correspond to Cashier's expected convention, specifically, methods should be prefixed with `handle` and the "camel case" name of the webhook you wish to handle. For example, if you wish to handle the `payment_succeeded` webhook, you should add a `handlePaymentSucceeded` method to the controller:

캐셔는 청구 실패시 구독 취소를 자동으로 처리하지만 처리하려는 추가 웹훅 이벤트가 있는 경우 `WebhookController`를 확장해야합니다. 메서드 이름은 캐셔의 예상 규칙과 일치해야합니다. 특히 메서드 앞에는 `handle` 과 처리하려는 웹훅의 "캐멀 케이스"이름이 접두사로 추가되어야합니다. 예를 들어 `payment_succeeded`웹훅을 처리하려면 컨트롤러에 `handlePaymentSucceeded`메소드를 추가해야합니다.

    <?php

    namespace App\Http\Controllers;

    use Laravel\Paddle\Http\Controllers\WebhookController as CashierController;

    class WebhookController extends CashierController
    {
        /**
         * Handle payment succeeded.
         *
         * @param  array  $payload
         * @return void
         */
        public function handlePaymentSucceeded($payload)
        {
            // Handle The Event
        }
    }

Next, define a route to your Cashier controller within your `routes/web.php` file. This will overwrite the route included with Cashier:

다음으로 `routes/web.php` 파일 내에서 캐셔 컨트롤러에 대한 라우트를 정의합니다. 캐셔에 포함 된 라우트를 덮어 씁니다.

    Route::post(
        'paddle/webhook',
        '\App\Http\Controllers\WebhookController'
    );

Cashier emits a `Laravel\Paddle\Events\WebhookReceived` event when a webhook is received and a `Laravel\Paddle\Events\WebhookHandled` event when a webhook was handled. Both events contain the full payload of the Paddle webhook.

캐셔는 웹훅이 수신되면 `Laravel\Paddle\Events\WebhookReceived` 이벤트를, 웹훅이 처리되면 `Laravel\Paddle\Events\WebhookHandled` 이벤트를 내 보냅니다. 두 이벤트 모두 Paddle webhook의 전체 페이로드를 포함합니다.

Cashier also emit events dedicated to the type of the received webhook. In addition to the full payload from Paddle, they also contain the relevant models that were used to process the webhook such as the billable model, the subscription, or the receipt:

캐셔는 또한 수신 된 웹훅 유형 전용 이벤트를 발생시킵니다. Paddle의 전체 페이로드 외에도 청구 가능 모델, 구독 또는 영수증과 같은 웹훅을 처리하는 데 사용 된 관련 모델도 포함됩니다.

- `PaymentSucceeded`
- `SubscriptionPaymentSucceeded`
- `SubscriptionCreated`
- `SubscriptionUpdated`
- `SubscriptionCancelled`

You can optionally also override the default, built-in webhook route by setting the `CASHIER_WEBHOOK` env variable in your `.env` file. This value should be the full URL to your webhook route and needs to match the URL set in your Paddle control panel:

선택적으로 `.env` 파일에서 `CASHIER_WEBHOOK` env 변수를 설정하여 기본 내장 웹훅 경로를 재정의 할 수도 있습니다. 이 값은 웹훅 경로의 전체 URL이어야하며 Paddle 제어판에 설정된 URL과 일치해야합니다.

    CASHIER_WEBHOOK=https://example.com/my-paddle-webhook-url

<a name="handling-failed-subscriptions"></a>
### Failed Subscriptions
### 실패한 구독

What if a customer's credit card expires? No worries - Cashier's Webhook controller will cancel the customer's subscription for you. Failed payments will automatically be captured and handled by the controller. The controller will cancel the customer's subscription when Paddle determines the subscription has failed (normally after three failed payment attempts).

고객의 신용 카드가 만료되면 어떻게 될까요? 걱정하지 마세요. 캐셔의 Webhook 컨트롤러가 고객의 구독을 취소합니다. 실패한 지불은 컨트롤러에 의해 자동으로 캡처되고 처리됩니다. Paddle이 구독이 실패했다고 판단하면 컨트롤러는 고객의 구독을 취소합니다 (일반적으로 세 번의 결제 시도 실패 후에 처리됩니다).

<a name="verifying-webhook-signatures"></a>
### Verifying Webhook Signatures
### Webhook 서명 확인

To secure your webhooks, you may use [Paddle's webhook signatures](https://developer.paddle.com/webhook-reference/verifying-webhooks). For convenience, Cashier automatically includes a middleware which validates that the incoming Paddle webhook request is valid.

웹훅을 보호하려면 [Paddle의 웹훅 서명](https://developer.paddle.com/webhook-reference/verifying-webhooks)을 사용할 수 있습니다. 편의를 위해 캐셔는 들어오는 Paddle webhook 요청이 유효한지 확인하는 미들웨어를 자동으로 포함합니다.

To enable webhook verification, ensure that the `PADDLE_PUBLIC_KEY` environment variable is set in your `.env` file. The public key may be retrieved from your Paddle account dashboard.

웹훅 확인을 사용하려면 `.env` 파일에 `PADDLE_PUBLIC_KEY` 환경 변수가 설정되어 있는지 확인하십시오. 공개 키는 Paddle 계정 대시 보드에서 검색 할 수 있습니다.

<a name="single-charges"></a>
## Single Charges
## 일회성 청구

<a name="simple-charge"></a>
### Simple Charge
### 기본 청구

If you would like to make a "one off" charge against a customer, you may use the `charge` method on a billable model instance to generate a pay link for the charge. The `charge` method accepts the charge amount (float) as its first argument and a charge description as its second argument:

고객에 대해 "일회성"청구를 하려면 청구 가능 모델 인스턴스에서 `charge`메서드를 사용하여 요금에 대한 결제 링크를 생성 할 수 있습니다. `charge`메서드는 첫 번째 인수로 청구 금액 (float)을, 두 번째 인수로 결제 설명을 입력받습니다.

    $payLink = $user->charge(12.99, 'Product Title');

    return view('pay', ['payLink' => $payLink]);

After generating the pay link, you may use Cashier's provided `paddle-button` Blade component to allow the user to initiate the Paddle widget and complete the charge:

결제 링크를 생성 한 후 캐셔가 제공하는 `paddle-button`블레이드 컴포넌트를 사용하여 사용자가 패들 위젯을 시작하고 청구를 완료 할 수 있습니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4">
        Buy
    </x-paddle-button>

The `charge` method accepts an array as its third argument, allowing you to pass any options you wish to the underlying Paddle pay link creation. Please consult [the Paddle documentation](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink) to learn more about the options available to you when creating charges:

`charge` 메소드는 세 번째 인수로 배열을 허용하므로 원하는 모든 옵션을 기본 Paddle 결제 링크 생성에 전달할 수 있습니다. 청구시 사용할 수있는 옵션에 대한 자세한 내용은 [Paddle 문서](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)를 참조하세요.

    $payLink = $user->charge(12.99, 'Product Title', [
        'custom_option' => $value,
    ]);

Charges happen in the currency specified in the `cashier.currency` configuration option. By default, this is set to USD. You may override the default currency by setting the `CASHIER_CURRENCY` in your `.env` file:

`cashier.currency` 설정 옵션에 지정된 통화로 청구됩니다. 기본적으로 이것은 USD로 설정됩니다. `.env` 파일에서 `CASHIER_CURRENCY`를 설정하여 기본 통화를 재정의 할 수 있습니다.

    CASHIER_CURRENCY=EUR

You can also [override prices per currency](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink#price-overrides) using Paddle's dynamic pricing matching system. To do so, pass an array of prices instead of a fixed amount:

Paddle의 동적 가격 매칭 시스템을 사용하여 [통화 당 가격을 재정의](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink#price-overrides) 할 수도 있습니다. 이렇게하려면 고정 금액 대신 가격 배열을 전달합니다.

    $payLink = $user->charge([
        'USD:19.99',
        'EUR:15.99',
    ], 'Product Title');

<a name="charging-products"></a>
### Charging Products
### 제품 결제

If you would like to make a "one off" charge against a specific product configured within Paddle, you may use the `chargeProduct` method on a billable model instance to generate a pay link:

Paddle 내에 구성된 특정 제품에 대해 "일회성"청구를 하려면 청구 가능 모델 인스턴스에서 `chargeProduct`메소드를 사용하여 결제 링크를 생성 할 수 있습니다.

    $payLink = $user->chargeProduct($productId);

    return view('pay', ['payLink' => $payLink]);

Then, you may provide the pay link to the `paddle-button` component to allow the user to initialize the Paddle widget:

그런 다음 사용자가 Paddle 위젯을 초기화 할 수 있도록 `paddle-button` 컴포넌트에 대한 결제 링크를 제공 할 수 있습니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4">
        Buy
    </x-paddle-button>

The `chargeProduct` method accepts an array as its second argument, allowing you to pass any options you wish to the underlying Paddle pay link creation. Please consult [the Paddle documentation](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink) regarding the options that are available to you when creating charges:

`chargeProduct` 메소드는 두 번째 인수로 배열을 허용하므로 원하는 옵션을 기본 Paddle 결제 링크 생성에 전달할 수 있습니다. 청구를 생성 할 때 사용할 수있는 옵션에 대해서는 [Paddle 문서](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)를 참조하세요.

    $payLink = $user->chargeProduct($productId, [
        'custom_option' => $value,
    ]);

<a name="refunding-orders"></a>
### Refunding Orders
### 주문 환불

If you need to refund a Paddle order, you may use the `refund` method. This method accepts the Paddle Order ID as its first argument. You may retrieve receipts for a given billable entity using the `receipts` method:

패들 주문을 환불해야하는 경우 `refund`메서드를 사용할 수 있습니다. 이 메서드는 패들 주문 ID를 첫 번째 인수로 받습니다. `receipts`메소드를 사용하여 특정 청구 가능 항목에 대한 영수증을 검색 할 수 있습니다.

    $receipt = $user->receipts()->first();

    $refundRequestId = $user->refund($receipt->order_id);

You may also optionally specify a specific amount to refund as well as a reason for the refund:

선택적으로 환불 할 특정 금액과 환불 사유를 지정할 수도 있습니다.

    $receipt = $user->receipts()->first();

    $refundRequestId = $user->refund(
        $receipt->order_id, 5.00, 'Unused product time'
    );

> {tip} You can use the `$refundRequestId` as a reference for the refund when contacting Paddle support.

> {tip} Paddle 지원팀에 문의 할 때 `$refundRequestId`를 환불 참조로 사용할 수 있습니다.

<a name="receipts"></a>
## Receipts
## 영수증

You may easily retrieve an array of a billable model's receipts using the `receipts` method:

`receipts` 메소드를 사용하여 청구 가능 모델의 영수증 배열을 쉽게 검색 할 수 있습니다.

    $receipts = $user->receipts();

When listing the receipts for the customer, you may use the receipt's helper methods to display the relevant receipt information. For example, you may wish to list every receipt in a table, allowing the user to easily download any of the receipts:

고객에 대한 영수증을 나열 할 때 영수증의 헬퍼 메서드를 사용하여 관련 영수증 정보를 표시 할 수 있습니다. 예를 들어, 테이블에 모든 영수증을 나열하여 사용자가 영수증을 쉽게 다운로드 할 수 있도록 할 수 있습니다.

    <table>
        @foreach ($receipts as $receipt)
            <tr>
                <td>{{ $receipt->paid_at->toFormattedDateString() }}</td>
                <td>{{ $receipt->amount() }}</td>
                <td><a href="{{ $receipt->receipt_url }}" target="_blank">Download</a></td>
            </tr>
        @endforeach
    </table>

<a name="past-and-upcoming-payments"></a>
### Past & Upcoming Payments
### 과거 및 향후 지급

You may use the `lastPayment` and `nextPayment` methods to display a customer's past or upcoming payments for recurring subscriptions:

`lastPayment` 및 `nextPayment` 메소드를 사용하여 반복 구독에 대한 고객의 과거 또는 향후 결제를 표시 할 수 있습니다.

    $subscription = $user->subscription('default');

    $lastPayment = $subscription->lastPayment();
    $nextPayment = $subscription->nextPayment();

Both of these methods will return an instance of `Laravel\Paddle\Payment`; however, `nextPayment` will return `null` when the billing cycle has ended (such as when a subscription has been cancelled):

이 두 방법 모두 `Laravel\Paddle\Payment` 의 인스턴스를 반환합니다. 그러나 `nextPayment` 는 결제주기가 종료되면 (예 : 구독이 취소 된 경우) `null`을 반환합니다.

    Next payment: {{ $nextPayment->amount() }} due on {{ $nextPayment->date()->format('d/m/Y') }}

<a name="handling-failed-payments"></a>
## Handling Failed Payments
## 실패한 결제 처리

Subscription payments fail for various reasons, such as expired cards or a card having insufficient funds. When this happens, we recommend that you let Paddle handle payment failures for you. Specifically, you may [setup Paddle's automatic billing emails](https://vendors.paddle.com/subscription-settings) in your Paddle dashboard.

만료 된 카드 또는 자금이 부족한 카드와 같은 다양한 이유로 구독 결제가 실패합니다. 이 경우 Paddle에서 결제 실패를 처리하도록 하는 것이 좋습니다. 구체적으로 Paddle 대시 보드에서 [Paddle의 자동 결제 이메일 설정](https://vendors.paddle.com/subscription-settings) 할 수 있습니다.

Alternatively, you can perform more precise customization by catching the [`subscription_payment_failed`](https://developer.paddle.com/webhook-reference/subscription-alerts/subscription-payment-failed) webhook and enabling the "Subscription Payment Failed" option in the Webhook settings of your Paddle dashboard:

또는 Paddle 대시 보드의 Webhook 설정에 있는 옵션 [`subscription_payment_failed`](https://developer.paddle.com/webhook-reference/subscription-alerts/subscription-payment-failed) 을 찾아 "구독 결제 실패"를 활성화하여 보다 정확한 커스터마이징을 할 수 있습니다.

    <?php

    namespace App\Http\Controllers;

    use Laravel\Paddle\Http\Controllers\WebhookController as CashierController;

    class WebhookController extends CashierController
    {
        /**
         * Handle subscription payment failed.
         *
         * @param  array  $payload
         * @return void
         */
        public function handleSubscriptionPaymentFailed($payload)
        {
            // Handle the failed subscription payment...
        }
    }

<a name="testing"></a>
## Testing
## Testing

Paddle currently lacks a proper CRUD API so you will need to manually test your billing flow. Paddle also lacks a sandboxed developer environment so any card charges you make are live charges. In order to work around this, we recommend you use coupons with a 100% discount or free products during testing.

Paddle에는 현재 적절한 CRUD API가 없으므로 결제 흐름을 수동으로 테스트해야 합니다. Paddle에는 샌드박스 개발자 환경이 없기 때문에 카드 청구는 실시간 청구입니다. 이 문제를 해결하려면 테스트 중에 100% 할인 쿠폰 또는 무료 제품을 사용하는 것이 좋습니다.
