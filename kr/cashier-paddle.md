# Laravel Cashier (Paddle)
# 라라벨 캐셔 패들

- [Introduction](#introduction)
- [시작하기](#introduction)
- [Upgrading Cashier](#upgrading-cashier)
- [캐셔 업그레이드](#upgrading-cashier)
- [Installation](#installation)
- [설치하기](#installation)
    - [Paddle Sandbox](#paddle-sandbox)
    - [Paddle Sandbox](#paddle-sandbox)
    - [Database Migrations](#database-migrations)
    - [데이터베이스 마이그레이션](#database-migrations)
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
    - [Overriding Default Models](#overriding-default-models)
    - [기본 모델 재정의](#overriding-default-models)
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
    - [Subscription Modifiers](#subscription-modifiers)
    - [구독 수정](#subscription-modifiers)
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
    - [Verifying Webhook Signatures](#verifying-webhook-signatures)
    - [Webhook 서명 확인](#verifying-webhook-signatures)
- [Single Charges](#single-charges)
- [단일 요금](#single-charges)
    - [Simple Charge](#simple-charge)
    - [Simple Charge](#simple-charge)
    - [Charging Products](#charging-products)
    - [Charging Products](#charging-products)
    - [Refunding Orders](#refunding-orders)
    - [Refunding Orders](#refunding-orders)
- [Receipts](#receipts)
- [영수증](#receipts)
    - [Past & Upcoming Payments](#past-and-upcoming-payments)
    - [과거 및 향후 지급](#past-and-upcoming-payments)
- [Handling Failed Payments](#handling-failed-payments)
- [실패한 결제 처리](#handling-failed-payments)
- [Testing](#testing)
- [Testing](#testing)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Cashier Paddle](https://github.com/laravel/cashier-paddle) provides an expressive, fluent interface to [Paddle's](https://paddle.com) subscription billing services. It handles almost all of the boilerplate subscription billing code you are dreading. In addition to basic subscription management, Cashier can handle: coupons, swapping subscription, subscription "quantities", cancellation grace periods, and more.

[Laravel Cashier Paddle](https://github.com/laravel/cashier-paddle)은 [Paddle](https://paddle.com) 구독 결제 서비스에 대한 쉽고 편리한 인터페이스를 제공합니다. 패들은 당신이 두려워하는 거의 모든 상용 구독 청구 코드를 처리합니다. 기본 구독 관리 외에도 캐셔는 쿠폰, 구독 교환, 구독 "수량", 취소 유예 기간 등을 처리할 수 있습니다.

While working with Cashier we recommend you also review Paddle's [user guides](https://developer.paddle.com/guides) and [API documentation](https://developer.paddle.com/api-reference/intro).

캐셔로 작업하는 동안 Paddle의 [사용자 가이드](https://developer.paddle.com/guides) 및 [API 문서](https://developer.paddle.com/api-reference/intro)도 함께 보는것이 좋습니다.

<a name="upgrading-cashier"></a>
## Upgrading Cashier
## 캐셔 업그레이드

When upgrading to a new version of Cashier, it's important that you carefully review [the upgrade guide](https://github.com/laravel/cashier-paddle/blob/master/UPGRADE.md).

캐셔를 새 버전으로 업그레이드 할 때는 [업그레이드 가이드](https://github.com/laravel/cashier-paddle/blob/master/UPGRADE.md)를 자세히 검토하세요.

<a name="installation"></a>
## Installation
## 설치하기

First, install the Cashier package for Paddle using the Composer package manager:

먼저 Composer의 패들 용 캐셔 패키지를 설치합니다.

```shell
composer require laravel/cashier-paddle
```

> {note} To ensure Cashier properly handles all Paddle events, remember to [set up Cashier's webhook handling](#handling-paddle-webhooks).

> {note} 캐셔의 모든 패들 이벤트를 올바르게 처리하도록 하려면 [캐셔 webhook 처리 설정](#handling-paddle-webhooks)을 기억하세요.

<a name="paddle-sandbox"></a>
### Paddle Sandbox
### Paddle Sandbox

During local and staging development, you should [register a Paddle Sandbox account](https://developer.paddle.com/getting-started/sandbox). This account will give you a sandboxed environment to test and develop your applications without making actual payments. You may use Paddle's [test card numbers](https://developer.paddle.com/getting-started/sandbox#test-cards) to simulate various payment scenarios.

로컬 및 스테이징 개발 중에 [Paddle Sandbox](https://developer.paddle.comgetting-startedsandbox)계정을 등록해야 합니다. 이 계정은 실제 지불 없이 애플리케이션을 테스트하고 개발할 수 있는 샌드박스 환경을 제공합니다. 패들의 [테스트 카드 번호](https://developer.paddle.com/getting-started/sandbox#test-cards)를 사용하여 다양한 결제 시나리오를 시험해 볼 수 있습니다.

When using the Paddle Sandbox environment, you should set the `PADDLE_SANDBOX` environment variable to `true` within your application's `.env` file:

Paddle Sandbox 환경을 사용할 때 애플리케이션의 `.env` 파일 내에서 `PADDLE_SANDBOX` 환경 변수를 `true`로 설정해야 합니다.

```ini
PADDLE_SANDBOX=true
```

After you have finished developing your application you may [apply for a Paddle vendor account](https://paddle.com).

애플리케이션 개발을 완료한 후 [패들 벤더 계정 신청](https://paddle.com)을 할 수 있습니다.

<a name="database-migrations"></a>
### Database Migrations
### 데이터베이스 마이그레이션

The Cashier service provider registers its own database migration directory, so remember to migrate your database after installing the package. The Cashier migrations will create a new `customers` table. In addition, a new `subscriptions` table will be created to store all of your customer's subscriptions. Finally, a new `receipts` table will be created to store all of your application's receipt information:

캐셔 서비스 프로바이더는 자체적으로 가지고 있는 데이터베이스 마이그레이션 디렉토리를 등록하므로 패키지를 설치 한 후 데이터베이스를 마이그레이션해야합니다. 캐셔 마이그레이션은 새로운 `customers`테이블을 생성합니다. 또한 고객의 모든 구독을 저장하기 위해 새로운 `subscriptions`테이블이 생성됩니다. 마지막으로 모든 영수증 정보를 저장하기 위해 새로운 `receipts` 테이블이 생성됩니다.

```shell
php artisan migrate
```

If you need to overwrite the migrations that are included with Cashier, you can publish them using the `vendor:publish` Artisan command:

캐셔에 포함된 마이그레이션을 덮어써야 하는 경우 `vendor:publish` Artisan 명령을 사용하여 게시할 수 있습니다.

```shell
php artisan vendor:publish --tag="cashier-migrations"
```

If you would like to prevent Cashier's migrations from running entirely, you may use the `ignoreMigrations` provided by Cashier. Typically, this method should be called in the `register` method of your `AppServiceProvider`:

캐셔의 마이그레이션이 실행되는 것을 완전히 방지하려면 캐셔가 제공하는 `ignoreMigrations`를 사용할 수 있습니다. 일반적으로 이 메서드는 `AppServiceProvider`의`register` 메서드에서 호출해야합니다.

    use Laravel\Paddle\Cashier;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        Cashier::ignoreMigrations();
    }

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

    use Illuminate\Database\Eloquent\Model;
    use Laravel\Paddle\Billable;

    class Team extends Model
    {
        use Billable;
    }

<a name="api-keys"></a>
### API Keys
### API Keys

Next, you should configure your Paddle keys in your application's `.env` file. You can retrieve your Paddle API keys from the Paddle control panel:

다음으로 애플리케이션의 `.env` 파일에서 패들 키를 구성해야 합니다. Paddle 제어판에서 Paddle API 키를 검색할 수 있습니다.

```ini
PADDLE_VENDOR_ID=your-paddle-vendor-id
PADDLE_VENDOR_AUTH_CODE=your-paddle-vendor-auth-code
PADDLE_PUBLIC_KEY="your-paddle-public-key"
PADDLE_SANDBOX=true
```

The `PADDLE_SANDBOX` environment variable should be set to `true` when you are using [Paddle's Sandbox environment](#paddle-sandbox). The `PADDLE_SANDBOX` variable should be set to `false` if you are deploying your application to production and are using Paddle's live vendor environment.

[패들의 샌드박스 환경](#paddle-sandbox) 사용 시 `PADDLE_SANDBOX` 환경 변수를 `true`로 설정해야 합니다. 애플리케이션을 프로덕션에 배포하고 Paddle의 라이브 공급업체 환경을 사용하는 경우 `PADDLE_SANDBOX` 변수를 `false`로 설정해야 합니다.

<a name="paddle-js"></a>
### Paddle JS
### Paddle JS

Paddle relies on its own JavaScript library to initiate the Paddle checkout widget. You can load the JavaScript library by placing the `@paddleJS` Blade directive right before your application layout's closing `</head>` tag:

Paddle은 자체 JavaScript 라이브러리에 사용하여 Paddle 체크아웃 위젯을 시작합니다. 애플리케이션 레이아웃의 닫는 `</head>` 태그 바로 앞에 `@paddleJS` Blade 지시문을 배치하여 JavaScript 라이브러리를 로드할 수 있습니다.

```blade
<head>
    ...

    @paddleJS
</head>
```

<a name="currency-configuration"></a>
### Currency Configuration
### 통화 설정하기

The default Cashier currency is United States Dollars (USD). You can change the default currency by defining a `CASHIER_CURRENCY` environment variable within your application's `.env` file:

캐셔의 기본 통화는 미국 달러(USD)입니다. 애플리케이션의 `.env` 파일 내에서 `CASHIER_CURRENCY` 환경 변수를 정의하여 기본 통화를 변경할 수 있습니다.

```ini
CASHIER_CURRENCY=EUR
```

In addition to configuring Cashier's currency, you may also specify a locale to be used when formatting money values for display on invoices. Internally, Cashier utilizes [PHP's `NumberFormatter` class](https://www.php.net/manual/en/class.numberformatter.php) to set the currency locale:

캐셔의 통화를 설정하는 것 외에도 송장에 표시 할 화폐 값을 형식화 할 때 사용할 로케일을 지정할 수도 있습니다. 내부적으로 Cashier는 [PHP의 `NumberFormatter` 클래스](https://www.php.net/manual/en/class.numberformatter.php)를 사용하여 통화 로케일을 설정합니다.

```ini
CASHIER_CURRENCY_LOCALE=nl_BE
```

> {note} In order to use locales other than `en`, ensure the `ext-intl` PHP extension is installed and configured on your server.

> {note} `en` 이외의 로케일을 사용하려면 `ext-intl` PHP 확장이 서버에 설치 및 설정되어 있는지 확인하세요.

<a name="overriding-default-models"></a>
### Overriding Default Models
### 기본 모델 재정의

You are free to extend the models used internally by Cashier by defining your own model and extending the corresponding Cashier model:

여러분은 자신의 모델을 정의하고 해당 캐셔 모델을 확장하여 캐셔에서 내부적으로 사용하는 모델을 자유롭게 사용할 수 있습니다.

    use Laravel\Paddle\Subscription as CashierSubscription;

    class Subscription extends CashierSubscription
    {
        // ...
    }

After defining your model, you may instruct Cashier to use your custom model via the `Laravel\Paddle\Cashier` class. Typically, you should inform Cashier about your custom models in the `boot` method of your application's `App\Providers\AppServiceProvider` class:

모델을 정의한 후 `Laravel\Paddle\Cashier` 클래스를 통해 사용자 정의 모델을 사용하도록 캐셔에 지시할 수 있습니다. 일반적으로 애플리케이션의 `App\Providers\AppServiceProvider` 클래스의 `boot` 메소드에서 사용자 정의 모델에 대해 캐셔에게 알려야 합니다.

    use App\Models\Cashier\Receipt;
    use App\Models\Cashier\Subscription;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Cashier::useReceiptModel(Receipt::class);
        Cashier::useSubscriptionModel(Subscription::class);
    }

<a name="core-concepts"></a>
## Core Concepts
## 핵심 개념

<a name="pay-links"></a>
### Pay Links
### 결제 링크

Paddle lacks an extensive CRUD API to perform subscription state changes. Therefore, most interactions with Paddle are done through its [checkout widget](https://developer.paddle.com/guides/how-tos/checkout/paddle-checkout). Before we can display the checkout widget, we must generate a "pay link" using Cashier. A "pay link" will inform the checkout widget of the billing operation we wish to perform:

Paddle에는 상태 변경을 수행하기위한 광범위한 CRUD API가 없습니다. 따라서 Paddle과의 대부분의 상호 작용은 [결제 위젯](https://developer.paddle.com/guides/how-tos/checkout/paddle-checkout)을 통해 이루어집니다. 결제 위젯을 표시하기 전에 캐셔를 사용하여 "결제 링크"를 생성합니다.

    use App\Models\User;
    use Illuminate\Http\Request;

    Route::get('/user/subscribe', function (Request $request) {
        $payLink = $request->user()->newSubscription('default', $premium = 34567)
            ->returnTo(route('home'))
            ->create();

        return view('billing', ['payLink' => $payLink]);
    });

Cashier includes a `paddle-button` [Blade component](/docs/{{version}}/blade#components). We may pass the pay link URL to this component as a "prop". When this button is clicked, Paddle's checkout widget will be displayed:

캐셔에는 `paddle-button` [블레이드 컴포넌트](/docs/{{version}}/blade#components)가 포함되어 있습니다. 이 컴포넌트에 "prop"으로 결제 링크 URL을 전달할 수 있습니다. 이 버튼을 클릭하면 Paddle의 결제 위젯이 표시됩니다.


    <x-paddle-button :url="$payLink" class="px-8 py-4">
        Subscribe
    </x-paddle-button>

By default, this will display a button with the standard Paddle styling. You can remove all Paddle styling by adding the `data-theme="none"` attribute to the component:

기본적으로 표준 패들 스타일이 적용된 버튼이 표시됩니다. 컴포넌트에 `data-theme="none"` 속성을 추가하여 모든 패들 스타일을 제거 할 수 있습니다.

    <x-paddle-button :url="$payLink" class="px-8 py-4" data-theme="none">
        Subscribe
    </x-paddle-button>

The Paddle checkout widget is asynchronous. Once the user creates or updates a subscription within the widget, Paddle will send your application webhooks so that you may properly update the subscription state in our own database. Therefore, it's important that you properly [set up webhooks](#handling-paddle-webhooks) to accommodate for state changes from Paddle.

패들 체크 아웃 위젯은 비동기식입니다. 사용자가 위젯 내에서 구독을 생성하거나 업데이트하면 Paddle은 자체 데이터베이스에서 구독 상태를 적절하게 업데이트 할 수 있도록 애플리케이션 웹훅을 보냅니다. 따라서 Paddle의 상태 변경 요청을 받아서 처리 할 수 있도록 [웹훅 설정](#handling-paddle-webhooks)을 적절하게 설정하는 것이 중요합니다.

For more information on pay links, you may review [the Paddle API documentation on pay link generation](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink).

결제 링크에 대한 자세한 내용은 [결제 링크 생성에 대한 Paddle API 문서](https://developer.paddle.comapi-referenceproduct-apipay-linkscreatepaylink)를 참조하세요.

> {note} After a subscription state change, the delay for receiving the corresponding webhook is typically minimal but you should account for this in your application by considering that your user's subscription might not be immediately available after completing the checkout.

> {note} 구독 상태가 변경된 후 해당 웹훅 수신 지연은 일반적으로 최소화되지만 체크아웃을 완료한 후 사용자의 구독을 즉시 사용할 수 없을 수도 있다는 점을 고려하여 애플리케이션에서 이를 고려해야 합니다.

<a name="manually-rendering-pay-links"></a>
#### Manually Rendering Pay Links
#### 수동으로 결제 링크 렌더링

You may also manually render a pay link without using Laravel's built-in Blade components. To get started, generate the pay link URL as demonstrated in previous examples:

Laravel의 내장 Blade 컴포넌트를 사용하지 않고 수동으로 결제 링크를 렌더링할 수도 있습니다. 시작하려면 이전 예에서 설명한 대로 결제 링크 URL을 생성하십시오.

    $payLink = $request->user()->newSubscription('default', $premium = 34567)
        ->returnTo(route('home'))
        ->create();

Next, simply attach the pay link URL to an `a` element in your HTML:

다음으로 HTML의 `a` 요소에 결제 링크 URL을 첨부하기만 하면 됩니다.

    <a href="#!" class="ml-4 paddle_button" data-override="{{ $payLink }}">
        Paddle Checkout
    </a>

<a name="payments-requiring-additional-confirmation"></a>
#### Payments Requiring Additional Confirmation
#### 추가 확인이 필요한 결제

Sometimes additional verification is required in order to confirm and process a payment. When this happens, Paddle will present a payment confirmation screen. Payment confirmation screens presented by Paddle or Cashier may be tailored to a specific bank or card issuer's payment flow and can include additional card confirmation, a temporary small charge, separate device authentication, or other forms of verification.

결제를 확인하고 처리하기 위해 추가 확인이 필요한 경우가 있습니다. 이 경우 Paddle은 결제 확인 화면을 표시합니다. Paddle 또는 Cashier가 제공하는 결제 확인 화면은 특정 은행 또는 카드 발급사의 결제 흐름에 맞게 조정될 수 있으며 추가 카드 확인, 일시적인 소액 청구, 별도의 장치 인증 또는 기타 형식의 확인이 포함될 수 있습니다.

<a name="inline-checkout"></a>
### Inline Checkout
### 인라인 체크아웃

If you don't want to make use of Paddle's "overlay" style checkout widget, Paddle also provides the option to display the widget inline. While this approach does not allow you to adjust any of the checkout's HTML fields, it allows you to embed the widget within your application.

Paddle의 "오버레이" 스타일 체크아웃 위젯을 사용하고 싶지 않다면 Paddle은 위젯을 인라인으로 표시하는 옵션도 제공합니다. 이 접근 방식을 사용하면 결제의 HTML 필드를 조정할 수 없지만 애플리케이션 내에 위젯을 포함할 수 있습니다.

To make it easy for you to get started with inline checkout, Cashier includes a `paddle-checkout` Blade component. To get started, you should [generate a pay link](#pay-links) and pass the pay link to the component's `override` attribute:

인라인 체크아웃을 쉽게 시작할 수 있도록 캐셔에는 `paddle-checkout` 블레이드 컴포넌트가 포함되어 있습니다. 시작하려면 [결제 링크를 생성](#pay-links)하고 결제 링크를 컴포넌트의 `override` 속성에 전달하면 됩니다.

```blade
<x-paddle-checkout :override="$payLink" class="w-full" />
```

To adjust the height of the inline checkout component, you may pass the `height` attribute to the Blade component:

인라인 결제 컴포넌트의 높이를 조정하려면 블레이드 컴포넌트에 `height` 속성을 전달하면 됩니다.

```blade
<x-paddle-checkout :override="$payLink" class="w-full" height="500" />
```

<a name="inline-checkout-without-pay-links"></a>
#### Inline Checkout Without Pay Links
#### 결제링크가 없는 인라인 체크아웃

Alternatively, you may customize the widget with custom options instead of using a pay link:

또는 결제 링크를 사용하는 대신 사용자 지정 옵션으로 위젯을 커스터마이징할 수 있습니다.

```blade
@php
$options = [
    'product' => $productId,
    'title' => 'Product Title',
];
@endphp

<x-paddle-checkout :options="$options" class="w-full" />
```

Please consult Paddle's [guide on Inline Checkout](https://developer.paddle.com/guides/how-tos/checkout/inline-checkout) as well as their [parameter reference](https://developer.paddle.com/reference/paddle-js/parameters) for further details on the inline checkout's available options.

인라인 체크아웃의 사용 가능한 옵션에 대한 자세한 내용은 Paddle의 [인라인 체크아웃 가이드](https://developer.paddle.com/guides/how-tos/checkoutinline-checkout) 및 [매개변수 참조](https://developer.paddle.com/reference/paddle-js/parameters)를 참조하세요.

> {note} If you would like to also use the `passthrough` option when specifying custom options, you should provide a key / value array as its value. Cashier will automatically handle converting the array to a JSON string. In addition, the `customer_id` passthrough option is reserved for internal Cashier usage.

> {note} 사용자 지정 옵션을 지정할 때 `passthrough` 옵션도 사용하려면 키 값 배열을 값으로 제공해야 합니다. 캐셔는 배열을 JSON 문자열로 변환하는 작업을 자동으로 처리합니다. 또한 `customer_id` 패스스루 옵션은 내부 캐셔 사용을 위해 예약되어 있습니다.

<a name="manually-rendering-an-inline-checkout"></a>
#### Manually Rendering An Inline Checkout
#### 인라인 체크아웃 수동 렌더링

You may also manually render an inline checkout without using Laravel's built-in Blade components. To get started, generate the pay link URL [as demonstrated in previous examples](#pay-links).

라라벨의 내장 Blade 컴포넌트를 사용하지 않고 인라인 체크아웃을 수동으로 렌더링할 수도 있습니다. 시작하려면 [결제 링크 URL](#pay-links)을 생성하십시오.

Next, you may use Paddle.js to initialize the checkout. To keep this example simple, we will demonstrate this using [Alpine.js](https://github.com/alpinejs/alpine); however, you are free to translate this example to your own frontend stack:

다음으로 Paddle.js를 사용하여 결제를 초기화할 수 있습니다. 이 예제를 간단하게 설명하기위해 [Alpine.js](https://github.com/alpinejs/alpine)을 사용하겠습니다. 이 예제를 자신의 프론트엔드 스택으로 자유롭게 변경할 수 있습니다.

```alpine
<div class="paddle-checkout" x-data="{}" x-init="
    Paddle.Checkout.open({
        override: {{ $payLink }},
        method: 'inline',
        frameTarget: 'paddle-checkout',
        frameInitialHeight: 366,
        frameStyle: 'width: 100%; background-color: transparent; border: none;'
    });
">
</div>
```

<a name="user-identification"></a>
### User Identification
### 사용자 식별

In contrast to Stripe, Paddle users are unique across all of Paddle, not unique per Paddle account. Because of this, Paddle's API's do not currently provide a method to update a user's details such as their email address. When generating pay links, Paddle identifies users using the `customer_email` parameter. When creating a subscription, Paddle will try to match the user provided email to an existing Paddle user.

Stripe와 달리 Paddle 사용자는 Paddle 계정별로 고유하지 않고 Paddle 전체에서 고유합니다. 이 때문에 Paddle의 API는 현재 이메일 주소와 같은 사용자 세부 정보를 업데이트하는 방법을 제공하지 않습니다. 결제 링크를 생성할 때 Paddle은 `customer_email` 매개변수를 사용하여 사용자를 식별합니다. 구독을 만들 때 Paddle은 사용자가 제공한 이메일을 기존 Paddle 사용자와 일치시키려고 시도합니다.

In light of this behavior, there are some important things to keep in mind when using Cashier and Paddle. First, you should be aware that even though subscriptions in Cashier are tied to the same application user, **they could be tied to different users within Paddle's internal systems**. Secondly, each subscription has its own connected payment method information and could also have different email addresses within Paddle's internal systems (depending on which email was assigned to the user when the subscription was created).

이 동작에 비추어 캐셔과 패들을 사용할 때 유의해야 할 몇 가지 중요한 사항이 있습니다. 첫째, 캐셔의 구독이 동일한 애플리케이션 사용자에게 연결되어 있더라도 **Paddle의 내부 시스템 내에서 다른 사용자에게 연결될 수 있음** 을 알아야합니다. 둘째, 각 구독에는 자체 연결된 결제 방법 정보가 있으며 Paddle의 내부 시스템 내에서 다른 이메일 주소를 가질 수도 있습니다 (구독을 만들 때 사용자에게 할당 된 이메일에 따라 다름).

Therefore, when displaying subscriptions you should always inform the user which email address or payment method information is connected to the subscription on a per-subscription basis. Retrieving this information can be done with the following methods provided by the `Laravel\Paddle\Subscription` model:

따라서 구독을 표시 할 때는 구독별로 구독에 연결된 이메일 주소 또는 결제 방법 정보를 항상 사용자에게 알려야 합니다. 이 정보를 검색하는 것은 `Laravel\Paddle\Subscription` 모델에서 제공하는 다음 방법으로 수행할 수 있습니다.

    $subscription = $user->subscription('default');

    $subscription->paddleEmail();
    $subscription->paymentMethod();
    $subscription->cardBrand();
    $subscription->cardLastFour();
    $subscription->cardExpirationDate();

There is currently no way to modify a user's email address through the Paddle API. When a user wants to update their email address within Paddle, the only way for them to do so is to contact Paddle customer support. When communicating with Paddle, they need to provide the `paddleEmail` value of the subscription to assist Paddle in updating the correct user.

현재는 Paddle API를 통해 사용자의 이메일 주소를 수정 할 수있는 방법이 없습니다. 사용자가 Paddle 내에서 자신의 이메일 주소를 업데이트하려는 경우, 그렇게 할 수있는 유일한 방법은 Paddle 고객 지원에 문의하는 것입니다. Paddle과 통신 할 때 Paddle이 올바른 사용자를 업데이트하는 데 도움이 되도록 구독의 `paddleEmail`값을 제공해야합니다.

<a name="prices"></a>
## Prices
## 가격

Paddle allows you to customize prices per currency, essentially allowing you to configure different prices for different countries. Cashier Paddle allows you to retrieve all of the prices for a given product using the `productPrices` method. This method accepts the product IDs of the products you wish to retrieve prices for:

Paddle을 사용하면 통화별로 가격을 커스터마이징 할 수 있으므로 기본적으로 국가별로 다른 가격을 설정할 수 있습니다. Cashier Paddle을 사용하면 `productPrices` 메소드를 사용하여 주어진 제품의 모든 가격을 검색 할 수 있습니다. 이 메서드는 가격을 검색하려는 제품의 제품 ID를 허용합니다.

    use Laravel\Paddle\Cashier;

    $prices = Cashier::productPrices([123, 456]);

The currency will be determined based on the IP address of the request; however, you may optionally provide a specific country to retrieve prices for:

통화는 요청의 IP 주소에 따라 결정됩니다. 그러나 필요하면 특정 국가를 지정하여 가격을 조회 할 수 있습니다.

    use Laravel\Paddle\Cashier;

    $prices = Cashier::productPrices([123, 456], ['customer_country' => 'BE']);

After retrieving the prices you may display them however you wish:

가격을 조회 한 후 원하는대로 표시 할 수 있습니다.

```blade
<ul>
    @foreach ($prices as $price)
        <li>{{ $price->product_title }} - {{ $price->price()->gross() }}</li>
    @endforeach
</ul>
```

You may also display the net price (excludes tax) and display the tax amount separately:

정가 (세금 제외)를 표시하고 세액을 별도로 표시 할 수도 있습니다.

```blade
<ul>
    @foreach ($prices as $price)
        <li>{{ $price->product_title }} - {{ $price->price()->net() }} (+ {{ $price->price()->tax() }} tax)</li>
    @endforeach
</ul>
```

If you retrieved prices for subscription plans you can display their initial and recurring price separately:

구독 플랜의 가격을 조회 한 경우 최초 및 연장결제의 가격을 별도로 표시 할 수 있습니다.

```blade
<ul>
    @foreach ($prices as $price)
        <li>{{ $price->product_title }} - Initial: {{ $price->initialPrice()->gross() }} - Recurring: {{ $price->recurringPrice()->gross() }}</li>
    @endforeach
</ul>
```

For more information, [check Paddle's API documentation on prices](https://developer.paddle.com/api-reference/checkout-api/prices/getprices).

자세한 내용은 [가격에 대한 Paddle의 API 문서](https://developer.paddle.com/api-reference/checkout-api/prices/getprices)를 참고하세요.

<a name="prices-customers"></a>
#### Customers
#### 고객

If a user is already a customer and you would like to display the prices that apply to that customer, you may do so by retrieving the prices directly from the customer instance:

사용자가 이미 고객이고 해당 고객에게 적용되는 가격을 표시하려는 경우 고객 인스턴스에서 직접 가격을 조회 할 수 있습니다.

    use App\Models\User;

    $prices = User::find(1)->productPrices([123, 456]);

Internally, Cashier will use the user's [`paddleCountry` method](#customer-defaults) to retrieve the prices in their currency. So, for example, a user living in the United States will see prices in USD while a user in Belgium will see prices in EUR. If no matching currency can be found the default currency of the product will be used. You can customize all prices of a product or subscription plan in the Paddle control panel.

내부적으로 캐셔는 사용자의 [`paddleCountry` 메소드](#customer-defaults)를 사용하여 통화로 가격을 조회합니다. 예를 들어 미국에 거주하는 사용자는 USD로 가격이 표시되고 벨기에 사용자는 EUR로 가격이 표시됩니다. 일치하는 통화를 찾을 수 없는 경우 제품의 기본 통화가 사용됩니다. 패들 제어판에서 제품 또는 구독 플랜의 모든 가격을 커스터마이징 할 수 있습니다.

<a name="prices-coupons"></a>
#### Coupons
#### 쿠폰

You may also choose to display prices after a coupon reduction. When calling the `productPrices` method, coupons may be passed as a comma delimited string:

쿠폰 할인 후 가격을 표시하도록 선택할 수도 있습니다. `productPrices` 메소드를 호출 할 때 쿠폰이 쉼표로 구분 된 문자열로 전달 될 수 있습니다.


    use Laravel\Paddle\Cashier;

    $prices = Cashier::productPrices([123, 456], [
        'coupons' => 'SUMMERSALE,20PERCENTOFF'
    ]);

Then, display the calculated prices using the `price` method:

그런 다음 `price` 메서드를 사용하여 계산 된 가격을 표시합니다.

```blade
<ul>
    @foreach ($prices as $price)
        <li>{{ $price->product_title }} - {{ $price->price()->gross() }}</li>
    @endforeach
</ul>
```

You may display the original listed prices (without coupon discounts) using the `listPrice` method:

`listPrice`메소드를 사용하여 원래 나열된 가격 (쿠폰 할인 제외)을 표시 할 수 있습니다.

```blade
<ul>
    @foreach ($prices as $price)
        <li>{{ $price->product_title }} - {{ $price->listPrice()->gross() }}</li>
    @endforeach
</ul>
```

> {note} When using the prices API, Paddle only allows applying coupons to one-time purchase products and not to subscription plans.

> {note} 가격 API를 사용할 때 Paddle은 일회성 구매 제품에만 쿠폰을 적용 할 수 있으며 구독 플랜에는 적용 할 수 없습니다.

<a name="customers"></a>
## Customers
## 고객

<a name="customer-defaults"></a>
### Customer Defaults
### 고객 기본값

Cashier allows you to define some useful defaults for your customers when creating pay links. Setting these defaults allow you to pre-fill a customer's email address, country, and postal code so that they can immediately move on to the payment portion of the checkout widget. You can set these defaults by overriding the following methods on your billable model:

캐셔를 사용하면 지불 링크를 생성할 때 고객에게 몇 가지 유용한 기본값을 정의할 수 있습니다. 이러한 기본값을 설정하면 고객의 이메일 주소, 국가 및 우편 번호를 미리 입력할 수 있으므로 고객이 즉시 결제 위젯의 결제 부분으로 이동할 수 있습니다. 청구 가능 모델에서 다음 메서드를 재정의하여 이러한 기본값을 설정할 수 있습니다.

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
     * Get the customer's postal code to associate with Paddle.
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

To create a subscription, first retrieve an instance of your billable model from your database, which typically will be an instance of `App\Models\User`. Once you have retrieved the model instance, you may use the `newSubscription` method to create the model's subscription pay link:

구독을 생성하려면 먼저 데이터베이스에서 청구 가능한 모델의 인스턴스를 조회하세요. 일반적으로 `App\Models\User`의 인스턴스가 됩니다. 모델 인스턴스를 조회 한 후에는 `newSubscription` 메서드를 사용하여 모델의 구독 결제 링크를 만들 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/user/subscribe', function (Request $request) {
        $payLink = $request->user()->newSubscription('default', $premium = 12345)
            ->returnTo(route('home'))
            ->create();

        return view('billing', ['payLink' => $payLink]);
    });

The first argument passed to the `newSubscription` method should be the internal name of the subscription. If your application only offers a single subscription, you might call this `default` or `primary`. This subscription name is only for internal application usage and is not meant to be shown to users. In addition, it should not contain spaces and it should never be changed after creating the subscription. The second argument given to the `newSubscription` method is the specific plan the user is subscribing to. This value should correspond to the plan's identifier in Paddle. The `returnTo` method accepts a URL that your user will be redirected to after they successfully complete the checkout.

`newSubscription` 메소드에 전달 된 첫 번째 인수는 구독의 이름이어야합니다. 애플리케이션이 단일 구독 만 제공하는 경우이를 `default` 또는 `primary`라고 부를 수 있습니다. 이 구독 이름은 내부 애플리케이션 용도로만 사용되며 사용자에게 표시되지 않습니다. 또한 공백을 포함해서는 안 되며 구독을 생성한 후 변경해서는 안 됩니다. `newSubscription` 메소드에 제공된 두 번째 인수는 사용자가 구독 중인 특정 플랜입니다. 이 값은 Paddle의 계획 식별자와 일치해야 합니다. `returnTo` 메소드는 사용자가 결제를 성공적으로 완료한 후 리디렉션될 URL을 입력받습니다.

The `create` method will create a pay link which you can use to generate a payment button. The payment button can be generated using the `paddle-button` [Blade component](/docs/{{version}}/blade#components) that is included with Cashier Paddle:

`create` 메소드는 지불 버튼을 생성하는 데 사용할 수 있는 지불 링크를 생성합니다. 결제 버튼은 Cashier Paddle에 포함된 `paddle-button` [Blade component](/docs/{{version}}/blade#components)를 사용하여 생성할 수 있습니다.

```blade
<x-paddle-button :url="$payLink" class="px-8 py-4">
    Subscribe
</x-paddle-button>
```

After the user has finished their checkout, a `subscription_created` webhook will be dispatched from Paddle. Cashier will receive this webhook and setup the subscription for your customer. In order to make sure all webhooks are properly received and handled by your application, ensure you have properly [setup webhook handling](#handling-paddle-webhooks).

사용자가 결제를 완료하면 `subscription_created` 웹훅이 Paddle에서 발송됩니다. 캐셔는 이 웹훅을 받고 고객의 구독을 설정합니다. 모든 웹훅이 애플리케이션에서 제대로 수신되고 처리되는지 확인하려면 [웹훅 처리 설정](#handling-paddle-webhooks)이 올바르게 설정되어 있는지 확인하세요.

<a name="additional-details"></a>
#### Additional Details
#### 추가적인 세부사항

If you would like to specify additional customer or subscription details, you may do so by passing them as an array of key / value pairs to the `create` method. To learn more about the additional fields supported by Paddle, check out Paddle's documentation on [generating pay links](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink):

추가 고객 또는 구독 세부 정보를 지정하려면 키 / 값 의 배열로 `create` 메서드에 전달하면 됩니다. Paddle에서 지원하는 추가 필드에 대해 자세히 알아보려면 [지불 링크 생성](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)에 대한 Paddle의 문서를 확인하세요.

    $payLink = $user->newSubscription('default', $monthly = 12345)
        ->returnTo(route('home'))
        ->create([
            'vat_number' => $vatNumber,
        ]);

<a name="subscriptions-coupons"></a>
#### Coupons
#### 쿠폰

If you would like to apply a coupon when creating the subscription, you may use the `withCoupon` method:

구독을 생성 할 때 쿠폰을 적용하려면 `withCoupon` 메소드를 사용할 수 있습니다.

    $payLink = $user->newSubscription('default', $monthly = 12345)
        ->returnTo(route('home'))
        ->withCoupon('code')
        ->create();

<a name="metadata"></a>
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

`subscribed` 메서드는 또한 [route middleware](/docs/{{version}}/middleware)의 훌륭한 보조도구가 되므로 사용자의 구독 상태에 따라 경로 및 컨트롤러에 대한 접근을 필터링할 수 있습니다.

    <?php

    namespace App\Http\Middleware;

    use Closure;

    class EnsureUserIsSubscribed
    {
        /**
         * Handle an incoming request.
         *
         * @param  \Illuminate\Http\Request  $request
         * @param  \Closure  $next
         * @return mixed
         */
        public function handle($request, Closure $next)
        {
            if ($request->user() && ! $request->user()->subscribed('default')) {
                // This user is not a paying customer...
                return redirect('billing');
            }

            return $next($request);
        }
    }

If you would like to determine if a user is still within their trial period, you may use the `onTrial` method. This method can be useful for determining if you should display a warning to the user that they are still on their trial period:

사용자가 아직 평가판 기간 내에 있는지 확인하려면 `onTrial` 방법을 사용할 수 있습니다. 이 방법은 사용자에게 아직 평가판 기간에 대한 경고를 표시해야 하는지 여부를 결정하는 데 유용할 수 있습니다.

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

<a name="cancelled-subscription-status"></a>
#### Cancelled Subscription Status
#### 취소된 구독상태

To determine if the user was once an active subscriber but has cancelled their subscription, you may use the `cancelled` method:

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

<a name="past-due-status"></a>
#### Past Due Status
#### 연체 상태

If a payment fails for a subscription, it will be marked as `past_due`. When your subscription is in this state it will not be active until the customer has updated their payment information. You may determine if a subscription is past due using the `pastDue` method on the subscription instance:

구독 결제에 실패하면 `past_due`로 표시됩니다. 구독이 이 상태이면 고객이 결제 정보를 업데이트할 때까지 활성화되지 않습니다. 구독 인스턴스에서 `pastDue` 메서드를 사용하여 구독이 기한이 지났는지 확인할 수 있습니다.

    if ($user->subscription('default')->pastDue()) {
        //
    }

When a subscription is past due, you should instruct the user to [update their payment information](#updating-payment-information). You may configure how past due subscriptions are handled in your [Paddle subscription settings](https://vendors.paddle.com/subscription-settings).

구독 기한이 지난 경우 사용자에게 [결제 정보 업데이트](#updating-payment-information)를 안내해야 합니다. [패들 구독 설정](https://vendors.paddle.com/subscription-settings)에서 연체 구독을 처리하는 방법을 구성할 수 있습니다.

If you would like subscriptions to still be considered active when they are `past_due`, you may use the `keepPastDueSubscriptionsActive` method provided by Cashier. Typically, this method should be called in the `register` method of your `AppServiceProvider`:

구독이 `past_due`일 때 여전히 활성 상태로 간주되도록 하려면 캐셔에서 제공하는 `keepPastDueSubscriptionsActive` 메서드를 사용할 수 있습니다. 일반적으로 이 메소드는 `AppServiceProvider`의 `register` 메소드에서 호출해야 합니다.

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

> {note} 구독이 `past_due` 상태인 경우 결제 정보가 업데이트될 때까지 변경할 수 없습니다. 따라서 `swap` 및 `updateQuantity` 메소드는 구독이 `past_due` 상태에 있을 때 예외를 발생시킵니다.

<a name="subscription-scopes"></a>
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

<a name="subscription-single-charges"></a>
### Subscription Single Charges
### 일회성 구독 청구

Subscription single charges allow you to charge subscribers with a one-time charge on top of their subscriptions:

일회성 구독 요금을 사용하면 구독에 추가로 일회성 요금을 구독자에게 청구 할 수 있습니다.

    $response = $user->subscription('default')->charge(12.99, 'Support Add-on');

In contrast to [single charges](#single-charges), this method will immediately charge the customer's stored payment method for the subscription. The charge amount should always be defined in the currency of the subscription.

[일회성 청구](#single-charges)와 달리 이 방법은 구독에 대해 고객의 저장된 결제 수단으로 즉시 청구됩니다. 청구 금액은 항상 구독이 현재 설정된 통화로 표시됩니다.

<a name="updating-payment-information"></a>
### Updating Payment Information
### 결제 정보 업데이트

Paddle always saves a payment method per subscription. If you want to update the default payment method for a subscription, you should first generate a subscription "update URL" using the `updateUrl` method on the subscription model:

Paddle은 항상 구독 마다 결제 방법을 저장합니다. 구독에 대한 기본 결제 수단을 업데이트하려면 먼저 구독 모델에서 `updateUrl`메서드를 사용하여 구독의 '업데이트용 URL'을 생성해야합니다.

    use App\Models\User;

    $user = User::find(1);

    $updateUrl = $user->subscription('default')->updateUrl();

Then, you may use the generated URL in combination with Cashier's provided `paddle-button` Blade component to allow the user to initiate the Paddle widget and update their payment information:

그런 다음 생성 된 URL을 캐셔가 제공하는 `paddle-button` 블레이드 컴포넌트와 함께 사용하여 사용자가 패들 위젯을 시작하고 결제 정보를 업데이트 할 수 있습니다.

```blade
<x-paddle-button :url="$updateUrl" class="px-8 py-4">
    Update Card
</x-paddle-button>
```

When a user has finished updating their information, a `subscription_updated` webhook will be dispatched by Paddle and the subscription details will be updated in your application's database.

사용자가 정보 업데이트를 완료하면 `subscription_updated` 웹훅이 Paddle에 의해 전달되고 구독 세부 정보가 애플리케이션의 데이터베이스에서 업데이트됩니다.

<a name="changing-plans"></a>
### Changing Plans
### 플랜 변경

After a user has subscribed to your application, they may occasionally want to change to a new subscription plan. To update the subscription plan for a user, you should pass the Paddle plan's identifier to the subscription's `swap` method:

사용자가 애플리케이션을 구독한 후 때때로 새 구독 플랜으로 변경하고 싶어할 수 있습니다. 사용자의 구독 플랜을 업데이트하려면 Paddle 계획의 식별자를 구독의 `swap` 메서드에 전달해야 합니다.

    use App\Models\User;

    $user = User::find(1);

    $user->subscription('default')->swap($premium = 34567);

If you would like to swap plans and immediately invoice the user instead of waiting for their next billing cycle, you may use the `swapAndInvoice` method:

다음 결제주기를 기다리지 않고 요금제를 바꾸고 즉시 사용자에게 인보이스를 보내려면 `swapAndInvoice` 메서드를 사용할 수 있습니다.

    $user = User::find(1);

    $user->subscription('default')->swapAndInvoice($premium = 34567);

> {note} Plans may not be swapped when a trial is active. For additional information regarding this limitation, please see the [Paddle documentation](https://developer.paddle.com/api-reference/subscription-api/users/updateuser#usage-notes).

> {note} 평가판이 활성 상태일 때는 플랜을 바꿀 수 없습니다. 이 제한 사항에 대한 추가 정보는 [패들 문서](https://developer.paddle.comapi-reference/subscription-api/users/updateuser#usage-notes)를 참조하세요.

<a name="prorations"></a>
#### Prorations
#### 비례 배분하기

By default, Paddle prorates charges when swapping between plans. The `noProrate` method may be used to update the subscription's without prorating the charges:

기본적으로 Paddle은 요금제간에 전환 할 때 요금을 비례 배분합니다. `noProrate`메서드를 사용하면 요금을 일할 계산하지 않고 구독을 업데이트 할 수 있습니다.

    $user->subscription('default')->noProrate()->swap($premium = 34567);

<a name="subscription-quantity"></a>
### Subscription Quantity
### 구독 수량

Sometimes subscriptions are affected by "quantity". For example, a project management application might charge $10 per month per project. To easily increment or decrement your subscription's quantity, use the `incrementQuantity` and `decrementQuantity` methods:

때때로 구독은 "수량"의 영향을 받습니다. 예를 들어 프로젝트 관리 애플리케이션은 프로젝트당 월 10달러를 청구할 수 있습니다. 구독 수량을 쉽게 늘리거나 줄이려면 `incrementQuantity` 및 `decrementQuantity` 메서드를 사용하세요.

    $user = User::find(1);

    $user->subscription('default')->incrementQuantity();

    // Add five to the subscription's current quantity...
    $user->subscription('default')->incrementQuantity(5);

    $user->subscription('default')->decrementQuantity();

    // Subtract five from the subscription's current quantity...
    $user->subscription('default')->decrementQuantity(5);

Alternatively, you may set a specific quantity using the `updateQuantity` method:

또는 `updateQuantity` 메소드를 사용하여 특정 수량을 설정할 수 있습니다.

    $user->subscription('default')->updateQuantity(10);

The `noProrate` method may be used to update the subscription's quantity without prorating the charges:

`noProrate`메소드를 사용하여 요금을 일할 계산하지 않고 구독 수량을 업데이트 할 수 있습니다.

    $user->subscription('default')->noProrate()->updateQuantity(10);

<a name="subscription-modifiers"></a>
### Subscription Modifiers
### 구독 수정자

Subscription modifiers allow you to implement [metered billing](https://developer.paddle.com/guides/how-tos/subscriptions/metered-billing#using-subscription-price-modifiers) or extend subscriptions with add-ons.

구독 수정자를 사용하면 [계량 청구](https://developer.paddle.com/guides/how-tos/subscriptions/metered-billing#using-subscription-price-modifiers)를 구현하거나 추가 기능으로 구독을 연장할 수 있습니다.

For example, you might want to offer a "Premium Support" add-on with your standard subscription. You can create this modifier like so:

예를 들어 표준 구독과 함께 "프리미엄 지원" 추가 기능을 제공할 수 있습니다. 이 수정자를 다음과 같이 만들 수 있습니다.

    $modifier = $user->subscription('default')->newModifier(12.99)->create();

The example above will add a $12.99 add-on to the subscription. By default, this charge will recur on every interval you have configured for the subscription. If you would like, you can add a readable description to the modifier using the modifier's `description` method:

위의 예는 구독 추가 하나당 12.99달러에 기능을 추가합니다. 기본적으로 이 요금은 구독에 대해 구성한 모든 간격에 대해 반복됩니다. 원하는 경우 수정자의 `description` 메서드를 사용하여 수정자에 읽기 가능한 설명을 추가할 수 있습니다.

    $modifier = $user->subscription('default')->newModifier(12.99)
        ->description('Premium Support')
        ->create();

To illustrate how to implement metered billing using modifiers, imagine your application charges per SMS message sent by the user. First, you should create a $0 plan in your Paddle dashboard. Once the user has been subscribed to this plan, you can add modifiers representing each individual charge to the subscription:

수정자를 사용하여 계량 청구를 구현하는 방법을 설명하기 위해 사용자가 보낸 SMS 메시지당 애플리케이션 요금을 상상해 보십시오. 먼저 Paddle 대시보드에서 0달러 플랜을 만들어야 합니다. 사용자가 이 플랜을 구독하면 구독에 각 개별 청구를 나타내는 수정자를 추가할 수 있습니다.

    $modifier = $user->subscription('default')->newModifier(0.99)
        ->description('New text message')
        ->oneTime()
        ->create();

As you can see, we invoked the `oneTime` method when creating this modifier. This method will ensure the modifier is only charged once and does not recur every billing interval.

보시다시피 이 수식어를 만들 때 `oneTime` 메서드를 호출했습니다. 이 방법을 사용하면 수정자가 한 번만 청구되고 모든 청구 간격이 반복되지 않습니다.

<a name="retrieving-modifiers"></a>
#### Retrieving Modifiers
#### 수정자 검색

You may retrieve a list of all modifiers for a subscription via the `modifiers` method:

`modifiers` 메소드를 통해 구독에 대한 모든 수정자 목록을 검색할 수 있습니다.

    $modifiers = $user->subscription('default')->modifiers();

    foreach ($modifiers as $modifier) {
        $modifier->amount(); // $0.99
        $modifier->description; // New text message.
    }

<a name="deleting-modifiers"></a>
#### Deleting Modifiers
#### 수정자 삭제

Modifiers may be deleted by invoking the `delete` method on a `Laravel\Paddle\Modifier` instance:

수정자는 `Laravel\Paddle\Modifier` 인스턴스에서 `delete` 메소드를 호출하여 삭제할 수 있습니다.

    $modifier->delete();

<a name="pausing-subscriptions"></a>
### Pausing Subscriptions
### 구독 일시 중지

To pause a subscription, call the `pause` method on the user's subscription:

구독을 일시 중지하려면 사용자의 구독에서 `pause` 메소드를 호출합니다.

    $user->subscription('default')->pause();

When a subscription is paused, Cashier will automatically set the `paused_from` column in your database. This column is used to know when the `paused` method should begin returning `true`. For example, if a customer pauses a subscription on March 1st, but the subscription was not scheduled to recur until March 5th, the `paused` method will continue to return `false` until March 5th. This is done because a user is typically allowed to continue using an application until the end of their billing cycle.

구독이 일시 중지되면 캐셔는 자동으로 데이터베이스에 `paused_from` 컬럼을 설정합니다. 이 컬럼은 `paused`메소드가 `true`를 반환하기 시작해야하는 시기를 확인하는 데 사용됩니다. 예를 들어 고객이 3월 1일에 구독을 일시 중지했지만 구독이 3월 5일까지 반복되지 않도록 예약 된 경우 `paused`메서드는 3월 5일까지 계속 `false`를 반환합니다. 이는 일반적으로 사용자가 청구 주기가 끝날 때까지 애플리케이션을 계속 사용할 수 있기 때문에 수행됩니다.

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

When a subscription is cancelled, Cashier will automatically set the `ends_at` column in your database. This column is used to know when the `subscribed` method should begin returning `false`. For example, if a customer cancels a subscription on March 1st, but the subscription was not scheduled to end until March 5th, the `subscribed` method will continue to return `true` until March 5th. This is done because a user is typically allowed to continue using an application until the end of their billing cycle.

구독이 취소되면 캐셔는 자동으로 데이터베이스에 `ends_at` 컬럼을 설정합니다. 이 컬럼은 `subscribed`메소드가 `false`를 반환하기 시작해야하는 시기를 확인하는 데 사용됩니다. 예를 들어 고객이 3월 1일에 구독을 취소했지만 구독이 3월 5일까지 종료되도록 예약되지 않은 경우 `subscribed`메서드는 3월 5일까지 계속 `true`를 반환합니다. 이는 일반적으로 사용자가 청구 주기가 끝날 때까지 애플리케이션을 계속 사용할 수 있기 때문에 수행됩니다.

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

    use Illuminate\Http\Request;

    Route::get('/user/subscribe', function (Request $request) {
        $payLink = $request->user()->newSubscription('default', $monthly = 12345)
                    ->returnTo(route('home'))
                    ->trialDays(10)
                    ->create();

        return view('billing', ['payLink' => $payLink]);
    });

This method will set the trial period ending date on the subscription record within your application's database, as well as instruct Paddle to not begin billing the customer until after this date.

이 방법은 애플리케이션 데이터베이스 내의 구독 레코드에 평가 기간 종료 날짜를 설정하고 Paddle이 이 날짜 이후까지 고객에게 청구를 시작하지 않도록 지시합니다.

> {note} If the customer's subscription is not cancelled before the trial ending date they will be charged as soon as the trial expires, so you should be sure to notify your users of their trial ending date.

> {note} 평가판 종료일 이전에 고객의 구독을 취소하지 않으면 평가판이 만료되는 즉시 요금이 청구되므로 사용자에게 평가판 종료일을 알려야합니다.

You may determine if the user is within their trial period using either the `onTrial` method of the user instance or the `onTrial` method of the subscription instance. The two examples below are equivalent:

사용자 인스턴스의 `onTrial` 메서드 또는 구독 인스턴스의 `onTrial` 메서드를 사용하여 사용자가 평가판 기간 내에 있는지 확인할 수 있습니다. 아래 두 가지 예는 동일합니다.

    if ($user->onTrial('default')) {
        //
    }

    if ($user->subscription('default')->onTrial()) {
        //
    }

<a name="defining-trial-days-in-paddle-cashier"></a>
#### Defining Trial Days In Paddle / Cashier
#### 패들 / 캐셔에서 평가기간 정의

You may choose to define how many trial days your plan's receive in the Paddle dashboard or always pass them explicitly using Cashier. If you choose to define your plan's trial days in Paddle you should be aware that new subscriptions, including new subscriptions for a customer that had a subscription in the past, will always receive a trial period unless you explicitly call the `trialDays(0)` method.

Paddle 대시 보드에서 플랜의 평가 기간을 정의하거나 항상 캐셔을 사용하여 명시적으로 전달할 수 있습니다. Paddle에서 플랜의 평가 기간을 정의하기로 선택한 경우 과거에 구독 한 고객에 대한 새 구독을 포함하여 새로운 구독은 명시 적으로 `trialDays(0)`를 호출하지 않는 한 항상 평가 기간을 받게됩니다.

<a name="without-payment-method-up-front"></a>
### Without Payment Method Up Front
### 결제수단 등록없이 시작하기

If you would like to offer trial periods without collecting the user's payment method information up front, you may set the `trial_ends_at` column on the customer record attached to your user to your desired trial ending date. This is typically done during user registration:

사용자의 결제 수단 정보를 미리 수집하지 않고 평가 기간을 제공하려면 사용자에게 첨부 된 고객 기록의 `trial_ends_at` 컬럼을 원하는 평가판 종료 날짜로 설정할 수 있습니다. 이는 일반적으로 사용자 등록 중에 수행됩니다.

    use App\Models\User;

    $user = User::create([
        // ...
    ]);

    $user->createAsCustomer([
        'trial_ends_at' => now()->addDays(10)
    ]);

Cashier refers to this type of trial as a "generic trial", since it is not attached to any existing subscription. The `onTrial` method on the `User` instance will return `true` if the current date is not past the value of `trial_ends_at`:

캐셔는 이러한 유형의 평가판이 기존 구독에 연결되어 있지 않기 때문에 "일반 평가판"이라고합니다. `User` 인스턴스의 `onTrial` 메소드는 현재 날짜가 `trial_ends_at` 값을 지나지 않았다면 `true`를 반환합니다.

    if ($user->onTrial()) {
        // User is within their trial period...
    }

Once you are ready to create an actual subscription for the user, you may use the `newSubscription` method as usual:

사용자를 위한 실제 구독을 만들 준비가 되면 평소와 같이 `newSubscription` 메서드를 사용할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/user/subscribe', function (Request $request) {
        $payLink = $user->newSubscription('default', $monthly = 12345)
            ->returnTo(route('home'))
            ->create();

        return view('billing', ['payLink' => $payLink]);
    });

To retrieve the user's trial ending date, you may use the `trialEndsAt` method. This method will return a Carbon date instance if a user is on a trial or `null` if they aren't. You may also pass an optional subscription name parameter if you would like to get the trial ending date for a specific subscription other than the default one:

사용자의 평가판 종료 날짜를 검색하려면 `trialEndsAt` 메서드를 사용할 수 있습니다. 이 메서드는 사용자가 평가판을 사용 중이면 Carbon 날짜 인스턴스를 반환하고 그렇지 않은 경우 `null`을 반환합니다. 기본 구독이 아닌 특정 구독에 대한 평가판 종료 날짜를 얻으려면 선택적 구독 이름 매개변수를 전달할 수도 있습니다.

    if ($user->onTrial()) {
        $trialEndsAt = $user->trialEndsAt('main');
    }

You may use the `onGenericTrial` method if you wish to know specifically that the user is within their "generic" trial period and has not created an actual subscription yet:

사용자가 "일반" 평가판 기간 내에 있고 아직 실제 구독을 생성하지 않았는지 구체적으로 알고 싶다면 `onGenericTrial` 방법을 사용할 수 있습니다.

    if ($user->onGenericTrial()) {
        // User is within their "generic" trial period...
    }

> {note} There is no way to extend or modify a trial period on a Paddle subscription after it has been created.

> {note} Paddle 구독이 생성 된 후에는 평가 기간을 연장하거나 수정할 수 없습니다.

<a name="handling-paddle-webhooks"></a>
## Handling Paddle Webhooks
## 패들 웹훅 처리

> {tip} You may use [Valet's `share` command](https://laravel.com/docs/{{version}}/valet#sharing-sites) to help test webhooks during local development.

> {tip} [Valet의 `share` 명령어](https://laravel.com/docs/{{version}}/valet#sharing-sites)를 사용하여 로컬 개발 중에 웹훅을 테스트 할 수 있습니다.

Paddle can notify your application of a variety of events via webhooks. By default, a route that points to Cashier's webhook controller is registered by the Cashier service provider. This controller will handle all incoming webhook requests.

Paddle은 웹훅을 통해 애플리케이션에 다양한 이벤트를 알릴 수 있습니다. 기본적으로 Cashier의 webhook 컨트롤러를 가리키는 경로는 Cashier 서비스 제공자가 등록합니다. 이 컨트롤러는 들어오는 모든 웹훅 요청을 처리합니다.

By default, this controller will automatically handle cancelling subscriptions that have too many failed charges ([as defined by your Paddle subscription settings](https://vendors.paddle.com/subscription-settings)), subscription updates, and payment method changes; however, as we'll soon discover, you can extend this controller to handle any Paddle webhook event you like.

기본적으로 이 컨트롤러는 실패한 요금이 너무 많은 구독 취소([Paddle 구독 설정에 정의](https://vendors.paddle.com/subscription-settings)), 구독 업데이트 및 결제 방법 변경을 자동으로 처리합니다. 그러나 곧 알게 되겠지만 이 컨트롤러를 확장하여 원하는 Paddle 웹훅 이벤트를 처리할 수 있습니다.

To ensure your application can handle Paddle webhooks, be sure to [configure the webhook URL in the Paddle control panel](https://vendors.paddle.com/alerts-webhooks). By default, Cashier's webhook controller responds to the `/paddle/webhook` URL path. The full list of all webhooks you should enable in the Paddle control panel are:

애플리케이션에서 Paddle 웹훅을 처리할 수 있도록 하려면 [Paddle 제어판에서 웹훅 URL 구성](https://vendors.paddle.com/alerts-webhooks)을 확인하세요. 기본적으로 Cashier의 webhook 컨트롤러는 `paddle/webhook` URL 경로에 응답합니다. Paddle 제어판에서 활성화해야 하는 모든 웹훅의 전체 목록은 다음과 같습니다.

- Subscription Created
- Subscription Updated
- Subscription Cancelled
- Payment Succeeded
- Subscription Payment Succeeded

- 구독 생성
- 구독 업데이트
- 구독 취소
- 결제 성공
- 구독 결제 성공

> {note} Make sure you protect incoming requests with Cashier's included [webhook signature verification](/docs/{{version}}/cashier-paddle#verifying-webhook-signatures) middleware.

> {note} 캐셔에 포함 된 [webhook 서명 확인](/docs/{{version}}/cashier-paddle#verifying-webhook-signatures) 미들웨어로 들어오는 요청을 보호하세요.

<a name="webhooks-csrf-protection"></a>
#### Webhooks & CSRF Protection
#### 웹훅 및 CSRF 보호

Since Paddle webhooks need to bypass Laravel's [CSRF protection](/docs/{{version}}/csrf), be sure to list the URI as an exception in your `App\Http\Middleware\VerifyCsrfToken` middleware or list the route outside of the `web` middleware group:

Paddle 웹훅은 라라벨의 [CSRF 보호](/docs/{{version}}/csrf)를 우회해야하므로 `App\Http\Middleware\VerifyCsrfToken` 미들웨어에 URI를 예외로 나열하거나 `web` 미들웨어 그룹의 외부에 라우트로 추가해야합니다.

    protected $except = [
        'paddle/*',
    ];

<a name="webhooks-local-development"></a>
#### Webhooks & Local Development
#### 웹훅 및 로컬 개발

For Paddle to be able to send your application webhooks during local development, you will need to expose your application via a site sharing service such as [Ngrok](https://ngrok.com/) or [Expose](https://expose.dev/docs/introduction). If you are developing your application locally using [Laravel Sail](/docs/{{version}}/sail), you may use Sail's [site sharing command](/docs/{{version}}/sail#sharing-your-site).

Paddle이 로컬 개발 중에 애플리케이션 웹훅을 보낼 수 있으려면 [Ngrok](https://ngrok.com/) 또는 [Expose](https://expose.dev/docs/introduction)와 같은 사이트 공유 서비스를 통해 애플리케이션을 노출해야 합니다. [Laravel Sail](/docs/{{version}}/sail)을 사용하여 로컬에서 애플리케이션을 개발하는 경우 Sail의 [사이트 공유 명령](/docs/{{version}}/sail#sharing-your-site)을 사용할 수 있습니다.

<a name="defining-webhook-event-handlers"></a>
### Defining Webhook Event Handlers
### Webhook 이벤트 핸들러 정의

Cashier automatically handles subscription cancellation on failed charges and other common Paddle webhooks. However, if you have additional webhook events you would like to handle, you may do so by listening to the following events that are dispatched by Cashier:

캐셔는 실패한 요금 및 기타 일반적인 Paddle 웹훅에 대한 구독 취소를 자동으로 처리합니다. 그러나 처리하려는 추가 webhook 이벤트가 있는 경우 캐셔에서 전달하는 다음 이벤트를 수신하여 처리할 수 있습니다.

- `Laravel\Paddle\Events\WebhookReceived`
- `Laravel\Paddle\Events\WebhookHandled`

Both events contain the full payload of the Paddle webhook. For example, if you wish to handle the `invoice.payment_succeeded` webhook, you may register a [listener](/docs/{{version}}/events#defining-listeners) that will handle the event:

두 이벤트 모두 Paddle 웹훅의 전체 페이로드를 포함합니다. 예를 들어 `invoice.payment_succeeded` 웹훅을 처리하려는 경우 이벤트를 처리할 [listener](/docs/{{version}}/events#defining-listeners)를 등록할 수 있습니다.

    <?php

    namespace App\Listeners;

    use Laravel\Paddle\Events\WebhookReceived;

    class PaddleEventListener
    {
        /**
         * Handle received Paddle webhooks.
         *
         * @param  \Laravel\Paddle\Events\WebhookReceived  $event
         * @return void
         */
        public function handle(WebhookReceived $event)
        {
            if ($event->payload['alert_name'] === 'payment_succeeded') {
                // Handle the incoming event...
            }
        }
    }

Once your listener has been defined, you may register it within your application's `EventServiceProvider`:

리스너가 정의되면 애플리케이션의 `EventServiceProvider` 내에 리스너를 등록할 수 있습니다.

    <?php

    namespace App\Providers;

    use App\Listeners\PaddleEventListener;
    use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;
    use Laravel\Paddle\Events\WebhookReceived;

    class EventServiceProvider extends ServiceProvider
    {
        protected $listen = [
            WebhookReceived::class => [
                PaddleEventListener::class,
            ],
        ];
    }

Cashier also emit events dedicated to the type of the received webhook. In addition to the full payload from Paddle, they also contain the relevant models that were used to process the webhook such as the billable model, the subscription, or the receipt:

캐셔는 또한 수신 된 웹훅 유형 전용 이벤트를 발생시킵니다. Paddle의 전체 페이로드 외에도 청구 가능 모델, 구독 또는 영수증과 같은 웹훅을 처리하는 데 사용 된 관련 모델도 포함됩니다.

- `Laravel\Paddle\Events\PaymentSucceeded`
- `Laravel\Paddle\Events\SubscriptionPaymentSucceeded`
- `Laravel\Paddle\Events\SubscriptionCreated`
- `Laravel\Paddle\Events\SubscriptionUpdated`
- `Laravel\Paddle\Events\SubscriptionCancelled`

You can also override the default, built-in webhook route by defining the `CASHIER_WEBHOOK` environment variable in your application's `.env` file. This value should be the full URL to your webhook route and needs to match the URL set in your Paddle control panel:

애플리케이션의 `.env` 파일에 `CASHIER_WEBHOOK` 환경 변수를 정의하여 기본 내장 웹훅 경로를 재정의할 수도 있습니다. 이 값은 웹훅 경로의 전체 URL이어야 하며 Paddle 제어판에 설정된 URL과 일치해야 합니다.

```ini
CASHIER_WEBHOOK=https://example.com/my-paddle-webhook-url
```

<a name="verifying-webhook-signatures"></a>
### Verifying Webhook Signatures
### Webhook 서명 확인

To secure your webhooks, you may use [Paddle's webhook signatures](https://developer.paddle.com/webhook-reference/verifying-webhooks). For convenience, Cashier automatically includes a middleware which validates that the incoming Paddle webhook request is valid.

웹훅을 보호하려면 [Paddle의 웹훅 서명](https://developer.paddle.com/webhook-reference/verifying-webhooks)을 사용할 수 있습니다. 편의를 위해 캐셔는 들어오는 Paddle webhook 요청이 유효한지 확인하는 미들웨어를 자동으로 포함합니다.

To enable webhook verification, ensure that the `PADDLE_PUBLIC_KEY` environment variable is defined in your application's `.env` file. The public key may be retrieved from your Paddle account dashboard.

웹훅 확인을 활성화하려면 `PADDLE_PUBLIC_KEY` 환경 변수가 애플리케이션의 `.env` 파일에 정의되어 있는지 확인하세요. 공개 키는 Paddle 계정 대시보드에서 검색할 수 있습니다.

<a name="single-charges"></a>
## Single Charges
## 일회성 청구

<a name="simple-charge"></a>
### Simple Charge
### 기본 청구

If you would like to make a one-time charge against a customer, you may use the `charge` method on a billable model instance to generate a pay link for the charge. The `charge` method accepts the charge amount (float) as its first argument and a charge description as its second argument:

고객에 대해 일회성 청구를 하려면 청구 가능한 모델 인스턴스에서 `charge` 메서드를 사용하여 청구에 대한 지불 링크를 생성할 수 있습니다. `charge` 메소드는 첫 번째 인수로 청구 금액(float)을 허용하고 두 번째 인수로 청구 금액 설명을 허용합니다.

    use Illuminate\Http\Request;

    Route::get('/store', function (Request $request) {
        return view('store', [
            'payLink' => $user->charge(12.99, 'Action Figure')
        ]);
    });

After generating the pay link, you may use Cashier's provided `paddle-button` Blade component to allow the user to initiate the Paddle widget and complete the charge:

결제 링크를 생성 한 후 캐셔가 제공하는 `paddle-button`블레이드 컴포넌트를 사용하여 사용자가 패들 위젯을 시작하고 청구를 완료 할 수 있습니다.

```blade
<x-paddle-button :url="$payLink" class="px-8 py-4">
    Buy
</x-paddle-button>
```

The `charge` method accepts an array as its third argument, allowing you to pass any options you wish to the underlying Paddle pay link creation. Please consult [the Paddle documentation](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink) to learn more about the options available to you when creating charges:

`charge` 메소드는 세 번째 인수로 배열을 허용하므로 원하는 모든 옵션을 기본 Paddle 결제 링크 생성에 전달할 수 있습니다. 청구시 사용할 수있는 옵션에 대한 자세한 내용은 [Paddle 문서](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)를 참조하세요.

    $payLink = $user->charge(12.99, 'Action Figure', [
        'custom_option' => $value,
    ]);

Charges happen in the currency specified in the `cashier.currency` configuration option. By default, this is set to USD. You may override the default currency by defining the `CASHIER_CURRENCY` environment variable in your application's `.env` file:

청구는 `cashier.currency` 설정 옵션에 지정된 통화로 발생합니다. 기본적으로 USD로 설정되어 있습니다. 애플리케이션의 `.env` 파일에서 `CASHIER_CURRENCY` 환경 변수를 정의하여 기본 통화를 재정의할 수 있습니다.

```ini
CASHIER_CURRENCY=EUR
```

You can also [override prices per currency](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink#price-overrides) using Paddle's dynamic pricing matching system. To do so, pass an array of prices instead of a fixed amount:

Paddle의 동적 가격 매칭 시스템을 사용하여 [통화 당 가격을 재정의](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink#price-overrides) 할 수도 있습니다. 이렇게하려면 고정 금액 대신 가격 배열을 전달합니다.

    $payLink = $user->charge([
        'USD:19.99',
        'EUR:15.99',
    ], 'Action Figure');

<a name="charging-products"></a>
### Charging Products
### 제품 결제

If you would like to make a one-time charge against a specific product configured within Paddle, you may use the `chargeProduct` method on a billable model instance to generate a pay link:

Paddle 내에 구성된 특정 제품에 대해 일회성 요금을 부과하려면 청구 가능한 모델 인스턴스에서 `chargeProduct` 메서드를 사용하여 지불 링크를 생성할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/store', function (Request $request) {
        return view('store', [
            'payLink' => $request->user()->chargeProduct($productId = 123)
        ]);
    });

Then, you may provide the pay link to the `paddle-button` component to allow the user to initialize the Paddle widget:

그런 다음 사용자가 Paddle 위젯을 초기화 할 수 있도록 `paddle-button` 컴포넌트에 대한 결제 링크를 제공 할 수 있습니다.

```blade
<x-paddle-button :url="$payLink" class="px-8 py-4">
    Buy
</x-paddle-button>
```

The `chargeProduct` method accepts an array as its second argument, allowing you to pass any options you wish to the underlying Paddle pay link creation. Please consult [the Paddle documentation](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink) regarding the options that are available to you when creating charges:

`chargeProduct` 메소드는 두 번째 인수로 배열을 허용하므로 원하는 옵션을 기본 Paddle 결제 링크 생성에 전달할 수 있습니다. 청구를 생성 할 때 사용할 수있는 옵션에 대해서는 [Paddle 문서](https://developer.paddle.com/api-reference/product-api/pay-links/createpaylink)를 참조하세요.

    $payLink = $user->chargeProduct($productId, [
        'custom_option' => $value,
    ]);

<a name="refunding-orders"></a>
### Refunding Orders
### 주문 환불

If you need to refund a Paddle order, you may use the `refund` method. This method accepts the Paddle order ID as its first argument. You may retrieve the receipts for a given billable model using the `receipts` method:

패들 주문을 환불해야 하는 경우 `refund` 방법을 사용할 수 있습니다. 이 메서드는 첫 번째 인수로 Paddle 주문 ID를 허용합니다. `receipts` 메소드를 사용하여 주어진 청구 가능 모델에 대한 영수증을 검색할 수 있습니다.

    use App\Models\User;

    $user = User::find(1);

    $receipt = $user->receipts()->first();

    $refundRequestId = $user->refund($receipt->order_id);

You may optionally specify a specific amount to refund as well as a reason for the refund:

선택적으로 환불할 특정 금액과 환불 사유를 지정할 수 있습니다.

    $receipt = $user->receipts()->first();

    $refundRequestId = $user->refund(
        $receipt->order_id, 5.00, 'Unused product time'
    );

> {tip} You can use the `$refundRequestId` as a reference for the refund when contacting Paddle support.

> {tip} Paddle 지원팀에 문의 할 때 `$refundRequestId`를 환불 참조로 사용할 수 있습니다.

<a name="receipts"></a>
## Receipts
## 영수증

You may easily retrieve an array of a billable model's receipts via the `receipts` property:

`receipts` 속성을 통해 청구 가능한 모델의 영수증 배열을 쉽게 검색할 수 있습니다.

    use App\Models\User;

    $user = User::find(1);

    $receipts = $user->receipts;

When listing the receipts for the customer, you may use the receipt instance's methods to display the relevant receipt information. For example, you may wish to list every receipt in a table, allowing the user to easily download any of the receipts:

고객에 대한 영수증을 나열할 때 영수증 인스턴스의 메서드를 사용하여 관련 영수증 정보를 표시할 수 있습니다. 예를 들어, 모든 영수증을 테이블에 나열하여 사용자가 영수증을 쉽게 다운로드할 수 있도록 할 수 있습니다.

```blade
<table>
    @foreach ($receipts as $receipt)
        <tr>
            <td>{{ $receipt->paid_at->toFormattedDateString() }}</td>
            <td>{{ $receipt->amount() }}</td>
            <td><a href="{{ $receipt->receipt_url }}" target="_blank">Download</a></td>
        </tr>
    @endforeach
</table>
```

<a name="past-and-upcoming-payments"></a>
### Past & Upcoming Payments
### 과거 및 향후 지급

You may use the `lastPayment` and `nextPayment` methods to retrieve and display a customer's past or upcoming payments for recurring subscriptions:

`lastPayment` 및 `nextPayment` 메소드를 사용하여 반복 구독에 대한 고객의 과거 또는 향후 결제를 검색하고 표시할 수 있습니다.

    use App\Models\User;

    $user = User::find(1);

    $subscription = $user->subscription('default');

    $lastPayment = $subscription->lastPayment();
    $nextPayment = $subscription->nextPayment();

Both of these methods will return an instance of `Laravel\Paddle\Payment`; however, `nextPayment` will return `null` when the billing cycle has ended (such as when a subscription has been cancelled):

이 두 방법 모두 `Laravel\Paddle\Payment` 의 인스턴스를 반환합니다. 그러나 `nextPayment` 는 결제주기가 종료되면 (예 : 구독이 취소 된 경우) `null`을 반환합니다.

```blade
Next payment: {{ $nextPayment->amount() }} due on {{ $nextPayment->date()->format('d/m/Y') }}
```

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

While testing, you should manually test your billing flow to make sure your integration works as expected.

테스트 진행시, 청구 흐름을 수동으로 테스트하여 통합이 예상대로 작동하는지 확인해야 합니다.

For automated tests, including those executed within a CI environment, you may use [Laravel's HTTP Client](/docs/{{version}}/http-client#testing) to fake HTTP calls made to Paddle. Although this does not test the actual responses from Paddle, it does provide a way to test your application without actually calling Paddle's API.

CI 환경 내에서 실행되는 테스트를 포함하여 자동화된 테스트의 경우 [Laravel의 HTTP 클라이언트](/docs/{{version}}/http-client#testing)를 사용하여 Paddle에 대한 가짜 HTTP 호출을 수행할 수 있습니다. 이것은 Paddle의 실제 응답을 테스트하지 않지만 실제로 Paddle의 API를 호출하지 않고 애플리케이션을 테스트하는 방법을 제공합니다.
