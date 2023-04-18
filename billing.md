# Laravel Cashier (Stripe)

- [Laravel Cashier (Stripe)](#laravel-cashier-stripe)
  - [Introduction](#introduction)
  - [시작하기](#시작하기)
  - [Upgrading Cashier](#upgrading-cashier)
  - [Cashier 업그레이드](#cashier-업그레이드)
  - [Installation](#installation)
  - [설치](#설치)
    - [Database Migrations](#database-migrations)
    - [데이터베이스 마이그레이션](#데이터베이스-마이그레이션)
  - [Configuration](#configuration)
  - [구성](#구성)
    - [Billable Model](#billable-model)
    - [청구 가능 모델](#청구-가능-모델)
    - [API Keys](#api-keys)
    - [API 키](#api-키)
    - [Currency Configuration](#currency-configuration)
    - [통화 구성](#통화-구성)
    - [Tax Configuration](#tax-configuration)
    - [세금 구성](#세금-구성)
    - [Logging](#logging)
    - [로깅](#로깅)
    - [Using Custom Models](#using-custom-models)
    - [사용자 커스텀 모델 사용](#사용자-커스텀-모델-사용)
  - [Customers](#customers)
  - [고객](#고객)
    - [Retrieving Customers](#retrieving-customers)
    - [고객 검색](#고객-검색)
    - [Creating Customers](#creating-customers)
    - [고객 생성](#고객-생성)
    - [Updating Customers](#updating-customers)
    - [고객 업데이트](#고객-업데이트)
    - [Balances](#balances)
    - [잔액](#잔액)
    - [Tax IDs](#tax-ids)
    - [세금 ID](#세금-id)
    - [Syncing Customer Data With Stripe](#syncing-customer-data-with-stripe)
    - [고객 데이터를 Stripe와 동기화](#고객-데이터를-stripe와-동기화)
    - [Billing Portal](#billing-portal)
    - [청구 포털](#청구-포털)
  - [Payment Methods](#payment-methods)
  - [결제 방법](#결제-방법)
    - [Storing Payment Methods](#storing-payment-methods)
    - [결제 방법 저장](#결제-방법-저장)
      - [Payment Methods For Subscriptions](#payment-methods-for-subscriptions)
      - [구독을 위한 결제 방법](#구독을-위한-결제-방법)
      - [Payment Methods For Single Charges](#payment-methods-for-single-charges)
      - [단일 결제를 위한 결제 방법](#단일-결제를-위한-결제-방법)
    - [Retrieving Payment Methods](#retrieving-payment-methods)
    - [결제 방법 검색](#결제-방법-검색)
    - [Determining If A User Has A Payment Method](#determining-if-a-user-has-a-payment-method)
    - [사용자가 결제 방법을 가지고 있는지 확인](#사용자가-결제-방법을-가지고-있는지-확인)
    - [Updating The Default Payment Method](#updating-the-default-payment-method)
    - [기본 결제 방법 업데이트](#기본-결제-방법-업데이트)
    - [Adding Payment Methods](#adding-payment-methods)
    - [결제 방법 추가](#결제-방법-추가)
    - [Deleting Payment Methods](#deleting-payment-methods)
    - [결제 방법 삭제](#결제-방법-삭제)
  - [Subscriptions](#subscriptions)
  - [구독](#구독)
    - [Creating Subscriptions](#creating-subscriptions)
    - [구독 생성](#구독-생성)
      - [Collecting Recurring Payments Via Invoice Emails](#collecting-recurring-payments-via-invoice-emails)
      - [인보이스 이메일을 통한 정기 결제 수집](#인보이스-이메일을-통한-정기-결제-수집)
      - [Quantities](#quantities)
      - [수량](#수량)
      - [Additional Details](#additional-details)
      - [추가 정보](#추가-정보)
      - [Coupons](#coupons)
      - [쿠폰](#쿠폰)
      - [Adding Subscriptions](#adding-subscriptions)
      - [추가 구독](#추가-구독)
      - [Creating Subscriptions From The Stripe Dashboard](#creating-subscriptions-from-the-stripe-dashboard)
      - [Stripe 대시보드에서 구독 생성](#stripe-대시보드에서-구독-생성)
    - [Checking Subscription Status](#checking-subscription-status)
    - [구독 상태 확인](#구독-상태-확인)
      - [Canceled Subscription Status](#canceled-subscription-status)
      - [취소된 구독 상태](#취소된-구독-상태)
      - [Incomplete and Past Due Status](#incomplete-and-past-due-status)
      - [미완료 및 연체 상태](#미완료-및-연체-상태)
      - [Subscription Scopes](#subscription-scopes)
      - [구독 범위](#구독-범위)
    - [Changing Prices](#changing-prices)
    - [가격 변경](#가격-변경)
      - [Prorations](#prorations)
      - [프로레이션](#프로레이션)
    - [Subscription Quantity](#subscription-quantity)
    - [구독 수량](#구독-수량)
      - [Quantities For Subscriptions With Multiple Products](#quantities-for-subscriptions-with-multiple-products)
      - [여러 제품 구독의 수량](#여러-제품-구독의-수량)
    - [Subscriptions With Multiple Products](#subscriptions-with-multiple-products)
    - [여러 제품 구독](#여러-제품-구독)
      - [Swapping Prices](#swapping-prices)
      - [가격 교환](#가격-교환)
      - [Proration](#proration)
      - [조정](#조정)
      - [Quantities](#quantities-1)
      - [수량](#수량-1)
      - [Subscription Items](#subscription-items)
      - [구독 항목](#구독-항목)
    - [Multiple Subscriptions](#multiple-subscriptions)
    - [다중 구독](#다중-구독)
    - [Metered Billing](#metered-billing)
    - [계량 요금](#계량-요금)
      - [Reporting Usage](#reporting-usage)
      - [사용량 보고](#사용량-보고)
      - [Retrieving Usage Records](#retrieving-usage-records)
      - [사용 기록 검색](#사용-기록-검색)
    - [Subscription Taxes](#subscription-taxes)
    - [구독 세금](#구독-세금)
      - [Syncing Tax Rates](#syncing-tax-rates)
      - [세금 비율 동기화](#세금-비율-동기화)
      - [Tax Exemption](#tax-exemption)
      - [세금 면제](#세금-면제)
    - [Subscription Anchor Date](#subscription-anchor-date)
    - [구독 앵커 날짜](#구독-앵커-날짜)
    - [Cancelling Subscriptions](#cancelling-subscriptions)
    - [구독 취소](#구독-취소)
    - [Resuming Subscriptions](#resuming-subscriptions)
    - [구독 재개](#구독-재개)
  - [Subscription Trials](#subscription-trials)
  - [구독 시험판](#구독-시험판)
    - [With Payment Method Up Front](#with-payment-method-up-front)
    - [사전 결제 방법](#사전-결제-방법)
      - [Defining Trial Days In Stripe / Cashier](#defining-trial-days-in-stripe--cashier)
      - [Stripe / Cashier에서 시험판 일수 정의](#stripe--cashier에서-시험판-일수-정의)
    - [Without Payment Method Up Front](#without-payment-method-up-front)
    - [사전 결제 방법 없이](#사전-결제-방법-없이)
    - [Extending Trials](#extending-trials)
    - [시험판 연장](#시험판-연장)
  - [Handling Stripe Webhooks](#handling-stripe-webhooks)
  - [Stripe 웹훅 처리](#stripe-웹훅-처리)
      - [Webhooks \& CSRF Protection](#webhooks--csrf-protection)
      - [웹훅 및 CSRF 보호](#웹훅-및-csrf-보호)
    - [Defining Webhook Event Handlers](#defining-webhook-event-handlers)
    - [웹훅 이벤트 핸들러 정의](#웹훅-이벤트-핸들러-정의)
    - [Verifying Webhook Signatures](#verifying-webhook-signatures)
    - [웹훅 서명 확인](#웹훅-서명-확인)
  - [Single Charges](#single-charges)
  - [단일 요금](#단일-요금)
    - [Simple Charge](#simple-charge)
    - [간편 충전](#간편-충전)
    - [Charge With Invoice](#charge-with-invoice)
    - [청구서와 함께 충전](#청구서와-함께-충전)
    - [Creating Payment Intents](#creating-payment-intents)
    - [결제 인텐트 생성](#결제-인텐트-생성)
    - [Refunding Charges](#refunding-charges)
    - [환불](#환불)
  - [Invoices](#invoices)
  - [청구서](#청구서)
    - [Retrieving Invoices](#retrieving-invoices)
    - [청구서 검색](#청구서-검색)
      - [Displaying Invoice Information](#displaying-invoice-information)
      - [청구서 정보 표시](#청구서-정보-표시)
    - [Upcoming Invoices](#upcoming-invoices)
    - [예정된 청구서](#예정된-청구서)
    - [Previewing Subscription Invoices](#previewing-subscription-invoices)
    - [구독 청구서 미리보기](#구독-청구서-미리보기)
    - [Generating Invoice PDFs](#generating-invoice-pdfs)
    - [청구서 PDF 생성](#청구서-pdf-생성)
      - [Custom Invoice Renderer](#custom-invoice-renderer)
      - [커스텀 청구서 렌더러](#커스텀-청구서-렌더러)
  - [Checkout](#checkout)
  - [체크아웃](#체크아웃)
    - [Product Checkouts](#product-checkouts)
    - [제품 체크아웃](#제품-체크아웃)
      - [Promotion Codes](#promotion-codes)
      - [프로모션 코드](#프로모션-코드)
    - [Single Charge Checkouts](#single-charge-checkouts)
    - [단일 요금 체크아웃](#단일-요금-체크아웃)
    - [Subscription Checkouts](#subscription-checkouts)
    - [구독 체크아웃](#구독-체크아웃)
      - [Stripe Checkout \& Trial Periods](#stripe-checkout--trial-periods)
      - [Stripe Checkout \& 체험 기간](#stripe-checkout--체험-기간)
      - [Subscriptions \& Webhooks](#subscriptions--webhooks)
      - [구독 \& 웹훅](#구독--웹훅)
    - [Collecting Tax IDs](#collecting-tax-ids)
    - [세금 ID 수집](#세금-id-수집)
    - [Guest Checkouts](#guest-checkouts)
    - [게스트 체크아웃](#게스트-체크아웃)
  - [Handling Failed Payments](#handling-failed-payments)
  - [실패한 결제 처리](#실패한-결제-처리)
  - [Strong Customer Authentication](#strong-customer-authentication)
  - [강력한 고객 인증](#강력한-고객-인증)
    - [Payments Requiring Additional Confirmation](#payments-requiring-additional-confirmation)
    - [추가 확인이 필요한 결제](#추가-확인이-필요한-결제)
      - [Incomplete and Past Due State](#incomplete-and-past-due-state)
      - [불완전 및 연체 상태](#불완전-및-연체-상태)
    - [Off-Session Payment Notifications](#off-session-payment-notifications)
    - [오프 세션 결제 알림](#오프-세션-결제-알림)
  - [Stripe SDK](#stripe-sdk)
  - [Stripe SDK](#stripe-sdk-1)
  - [Testing](#testing)
  - [테스트](#테스트)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Cashier Stripe](https://github.com/laravel/cashier-stripe) provides an expressive, fluent interface to [Stripe's](https://stripe.com) subscription billing services. It handles almost all of the boilerplate subscription billing code you are dreading writing. In addition to basic subscription management, Cashier can handle coupons, swapping subscription, subscription "quantities", cancellation grace periods, and even generate invoice PDFs.

[Laravel Cashier Stripe](https://github.com/laravel/cashier-stripe) 는 [Stripe](https://stripe.com) 의 구독 결제 서비스를 표현적이고 플루언트한 인터페이스를 제공합니다. 당신이 쓰고 싶어하는 모든 보일러 플레이트 구독 결제 코드를 처리합니다. 기본 구독 관리뿐만 아니라 쿠폰, 구독 교환, 구독 "수량", 취소 유예 기간 및 청구서 PDF 생성까지 Cashier가 처리할 수 있습니다.

<a name="upgrading-cashier"></a>
## Upgrading Cashier
## Cashier 업그레이드

When upgrading to a new version of Cashier, it's important that you carefully review [the upgrade guide](https://github.com/laravel/cashier-stripe/blob/master/UPGRADE.md).

캐셔의 새 버전으로 업그레이드 할 때는 [업그레이드 가이드](https://github.com/laravel/cashier-stripe/blob/master/UPGRADE.md)를 주의 깊게 검토해야합니다.

> **Warning**  
> To prevent breaking changes, Cashier uses a fixed Stripe API version. Cashier 14 utilizes Stripe API version `2022-11-15`. The Stripe API version will be updated on minor releases in order to make use of new Stripe features and improvements.
>
> **경고**
> 변경을 방지하기 위해 캐셔는 고정 된 Stripe API 버전을 사용합니다. Cashier 14는 Stripe API 버전 `2022-11-15`을 사용합니다. Stripe API 버전은 새로운 Stripe 기능 및 개선을 사용하기 위해 마이너 릴리스에서 업데이트됩니다.

<a name="installation"></a>
## Installation
## 설치

First, install the Cashier package for Stripe using the Composer package manager:

먼저, Composer 패키지 관리자를 사용하여 Stripe를 위한 Cashier 패키지를 설치합니다.

```shell
composer require laravel/cashier
```

> **Warning**  
> To ensure Cashier properly handles all Stripe events, remember to [set up Cashier's webhook handling](#handling-stripe-webhooks).
>
> **경고**
> 캐셔가 모든 Stripe 이벤트를 올바르게 처리하도록하려면 [캐셔 웹훅 처리 설정](#handling-stripe-webhooks)을 기억하십시오.

<a name="database-migrations"></a>
### Database Migrations
### 데이터베이스 마이그레이션

Cashier's service provider registers its own database migration directory, so remember to migrate your database after installing the package. The Cashier migrations will add several columns to your `users` table as well as create a new `subscriptions` table to hold all of your customer's subscriptions:

캐셔의 서비스 제공자는 자체 데이터베이스 마이그레이션 디렉토리를 등록하므로 패키지를 설치 한 후 데이터베이스를 마이그레이션해야합니다. Cashier 마이그레이션은 `users` 테이블에 몇 가지 열을 추가하고 모든 고객의 구독을 보관 할 새로운 `subscriptions` 테이블을 만듭니다.

```shell
php artisan migrate
```

If you need to overwrite the migrations that ship with Cashier, you can publish them using the `vendor:publish` Artisan command:

Cashier와 함께 제공되는 마이그레이션을 덮어 쓰려면 `vendor:publish` Artisan 명령을 사용하여 게시 할 수 있습니다.

```shell
php artisan vendor:publish --tag="cashier-migrations"
```

If you would like to prevent Cashier's migrations from running entirely, you may use the `ignoreMigrations` method provided by Cashier. Typically, this method should be called in the `register` method of your `AppServiceProvider`:

Cashier의 마이그레이션을 전혀 실행하지 않도록하려면 Cashier가 제공하는 `ignoreMigrations` 메소드를 사용할 수 있습니다. 일반적으로 이 메소드는 `AppServiceProvider`의 `register` 메소드에서 호출해야합니다.

    use Laravel\Cashier\Cashier;

    /**
     * Register any application services.
     */
    public function register(): void
    {
        Cashier::ignoreMigrations();
    }

> **Warning**  
> Stripe recommends that any column used for storing Stripe identifiers should be case-sensitive. Therefore, you should ensure the column collation for the `stripe_id` column is set to `utf8_bin` when using MySQL. More information regarding this can be found in the [Stripe documentation](https://stripe.com/docs/upgrades#what-changes-does-stripe-consider-to-be-backwards-compatible).
>
> **경고**
> Stripe에서는 Stripe 식별자를 저장하는 데 사용되는 컬럼에 대해 대소 문자를 구분하는 것을 권장합니다. 따라서 MySQL을 사용할 때 `stripe_id` 컬럼 콜레이션을 `utf8_bin`으로 설정해야합니다. 이에 대한 자세한 내용은 [Stripe 문서](https://stripe.com/docs/upgrades#what-changes-does-stripe-consider-to-be-backwards-compatible)에서 찾을 수 있습니다.

<a name="configuration"></a>
## Configuration
## 구성

<a name="billable-model"></a>
### Billable Model
### 청구 가능 모델

Before using Cashier, add the `Billable` trait to your billable model definition. Typically, this will be the `App\Models\User` model. This trait provides various methods to allow you to perform common billing tasks, such as creating subscriptions, applying coupons, and updating payment method information:

Cashier를 사용하기 전에 청구 가능 모델 정의에 `Billable` 트레이트를 추가하십시오. 일반적으로이는 `App\Models\User` 모델입니다.이 트레이트는 구독을 만들고 쿠폰을 적용하고 결제 방법 정보를 업데이트하는 등 일반적인 청구 작업을 수행 할 수있는 다양한 메소드를 제공합니다:

    use Laravel\Cashier\Billable;

    class User extends Authenticatable
    {
        use Billable;
    }

Cashier assumes your billable model will be the `App\Models\User` class that ships with Laravel. If you wish to change this you may specify a different model via the `useCustomerModel` method. This method should typically be called in the `boot` method of your `AppServiceProvider` class:

Cashier는 청구 가능한 모델이 Laravel과 함께 제공되는 `App\Models\User` 클래스라고 가정합니다. 이를 변경하려면 `useCustomerModel` 메소드를 사용하여 다른 모델을 지정할 수 있습니다. 이 메소드는 일반적으로 `AppServiceProvider` 클래스의 `boot` 메소드에서 호출해야합니다:

    use App\Models\Cashier\User;
    use Laravel\Cashier\Cashier;

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Cashier::useCustomerModel(User::class);
    }

> **Warning**  
> If you're using a model other than Laravel's supplied `App\Models\User` model, you'll need to publish and alter the [Cashier migrations](#installation) provided to match your alternative model's table name.
>
> **경고**
> Laravel이 제공하는 `App\Models\User` 모델이 아닌 모델을 사용하는 경우 게시하고 대체 모델의 테이블 이름과 일치하도록 제공되는 [Cashier 마이그레이션](#installation)을 변경해야합니다.  

<a name="api-keys"></a>
### API Keys
### API 키

Next, you should configure your Stripe API keys in your application's `.env` file. You can retrieve your Stripe API keys from the Stripe control panel:

다음으로, 응용 프로그램의 `.env` 파일에서 Stripe API 키를 구성해야합니다. Stripe 제어판에서 Stripe API 키를 검색할 수 있습니다:

```ini

```ini
STRIPE_KEY=your-stripe-key
STRIPE_SECRET=your-stripe-secret
STRIPE_WEBHOOK_SECRET=your-stripe-webhook-secret
```

> **Warning**  
> You should ensure that the `STRIPE_WEBHOOK_SECRET` environment variable is defined in your application's `.env` file, as this variable is used to ensure that incoming webhooks are actually from Stripe.
>
> **경고**
> `STRIPE_WEBHOOK_SECRET` 환경 변수가 응용 프로그램의 `.env` 파일에 정의되어 있어야합니다. 이 변수는 수신 웹 훅이 실제로 Stripe에서 온 것인지 확인하는 데 사용됩니다.

<a name="currency-configuration"></a>
### Currency Configuration
### 통화 구성

The default Cashier currency is United States Dollars (USD). You can change the default currency by setting the `CASHIER_CURRENCY` environment variable within your application's `.env` file:

기본 Cashier 통화는 미국 달러 (USD)입니다. 응용 프로그램의 `.env` 파일에서 `CASHIER_CURRENCY` 환경 변수를 설정하여 기본 통화를 변경할 수 있습니다:

```ini
CASHIER_CURRENCY=eur
```

In addition to configuring Cashier's currency, you may also specify a locale to be used when formatting money values for display on invoices. Internally, Cashier utilizes [PHP's `NumberFormatter` class](https://www.php.net/manual/en/class.numberformatter.php) to set the currency locale:

Cashier의 통화를 구성하는 것 외에도 송장에 표시 할 금액을 형식화 할 때 사용할 로케일을 지정할 수도 있습니다. 내부적으로 Cashier는 [PHP의 `NumberFormatter` 클래스](https://www.php.net/manual/en/class.numberformatter.php)를 사용하여 통화 로케일을 설정합니다:

```ini
CASHIER_CURRENCY_LOCALE=nl_BE
```

> **Warning**  
> In order to use locales other than `en`, ensure the `ext-intl` PHP extension is installed and configured on your server.
>
> **경고**
> `en` 이외의 로케일을 사용하려면 서버에서 PHP 확장기능 `ext-intl`이 설치되어 있고 구성되어 있는지 확인하십시오.

<a name="tax-configuration"></a>
### Tax Configuration
### 세금 구성

Thanks to [Stripe Tax](https://stripe.com/tax), it's possible to automatically calculate taxes for all invoices generated by Stripe. You can enable automatic tax calculation by invoking the `calculateTaxes` method in the `boot` method of your application's `App\Providers\AppServiceProvider` class:

[Stripe Tax](https://stripe.com/tax) 덕분에 Stripe에 의해 생성 된 모든 송장에 대한 세금을 자동으로 계산할 수 있습니다. 응용 프로그램의 `App\Providers\AppServiceProvider` 클래스의 `boot` 메소드에서 `calculateTaxes` 메소드를 호출하여 자동 세금 계산을 활성화 할 수 있습니다:

    use Laravel\Cashier\Cashier;

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Cashier::calculateTaxes();
    }

Once tax calculation has been enabled, any new subscriptions and any one-off invoices that are generated will receive automatic tax calculation.

세금 계산이 활성화되면 새로운 구독과 생성 된 일회성 송장 모두 자동 세금 계산을 받게 됩니다.

For this feature to work properly, your customer's billing details, such as the customer's name, address, and tax ID, need to be synced to Stripe. You may use the [customer data synchronization](#syncing-customer-data-with-stripe) and [Tax ID](#tax-ids) methods offered by Cashier to accomplish this.

이 기능을 올바르게 작동하려면 고객의 이름, 주소 및 세금 ID와 같은 고객의 청구 정보가 Stripe에 동기화되어야합니다. Cashier가 제공하는 [고객 데이터 동기화](#syncing-customer-data-with-stripe) 및 [세금 ID](#tax-ids) 메소드를 사용하여 이를 수행할 수 있습니다.

> **Warning**  
> No tax is calculated for [single charges](#single-charges) or [single charge checkouts](#single-charge-checkouts).
>
> **경고**
> [단일 요금](#single-charges) 또는 [단일 요금 체크 아웃](#single-charge-checkouts)에 대해 세금이 계산되지 않습니다.

<a name="logging"></a>
### Logging
### 로깅

Cashier allows you to specify the log channel to be used when logging fatal Stripe errors. You may specify the log channel by defining the `CASHIER_LOGGER` environment variable within your application's `.env` file:

Cashier는 치명적인 Stripe 오류를 로깅 할 때 사용할 로그 채널을 지정할 수 있습니다. 응용 프로그램의 `.env` 파일에서 `CASHIER_LOGGER` 환경 변수를 정의하여 로그 채널을 지정할 수 있습니다:

```ini
CASHIER_LOGGER=stack
```

Exceptions that are generated by API calls to Stripe will be logged through your application's default log channel.

Stripe에 대한 API 호출로 생성 된 예외는 응용 프로그램의 기본 로그 채널을 통해 로그로 기록됩니다.

<a name="using-custom-models"></a>
### Using Custom Models
### 사용자 커스텀 모델 사용

You are free to extend the models used internally by Cashier by defining your own model and extending the corresponding Cashier model:

Cashier가 내부적으로 사용하는 모델을 확장하여 사용자 정의 모델을 정의하고 해당 Cashier 모델을 확장할 수 있습니다:

    use Laravel\Cashier\Subscription as CashierSubscription;

    class Subscription extends CashierSubscription
    {
        // ...
    }

After defining your model, you may instruct Cashier to use your custom model via the `Laravel\Cashier\Cashier` class. Typically, you should inform Cashier about your custom models in the `boot` method of your application's `App\Providers\AppServiceProvider` class:

모델을 정의 한 후 `Laravel\Cashier\Cashier` 클래스를 통해 Cashier에 사용자 커스텀 모델을 사용하도록 지시할 수 있습니다. 일반적으로 응용 프로그램의 `App\Providers\AppServiceProvider` 클래스의 `boot` 메소드에서 Cashier에 사용자 커스텀 모델에 대해 알려야 합니다:

    use App\Models\Cashier\Subscription;
    use App\Models\Cashier\SubscriptionItem;

    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Cashier::useSubscriptionModel(Subscription::class);
        Cashier::useSubscriptionItemModel(SubscriptionItem::class);
    }

<a name="customers"></a>
## Customers
## 고객

<a name="retrieving-customers"></a>
### Retrieving Customers
### 고객 검색

You can retrieve a customer by their Stripe ID using the `Cashier::findBillable` method. This method will return an instance of the billable model:

`Cashier::findBillable` 메소드를 사용하여 Stripe ID로 고객을 검색할 수 있습니다. 이 메소드는 청구 가능 모델의 인스턴스를 반환합니다:

    use Laravel\Cashier\Cashier;

    $user = Cashier::findBillable($stripeId);

<a name="creating-customers"></a>
### Creating Customers
### 고객 생성

Occasionally, you may wish to create a Stripe customer without beginning a subscription. You may accomplish this using the `createAsStripeCustomer` method:

가끔, 구독을 시작하지 않고 Stripe 고객을 생성하려는 경우가 있습니다. `createAsStripeCustomer` 메소드를 사용하여 이를 수행할 수 있습니다.

    $stripeCustomer = $user->createAsStripeCustomer();

Once the customer has been created in Stripe, you may begin a subscription at a later date. You may provide an optional `$options` array to pass in any additional [customer creation parameters that are supported by the Stripe API](https://stripe.com/docs/api/customers/create):

Stripe에 고객이 생성되면 나중에 구독을 시작할 수 있습니다. 선택적 `$options` 배열을 제공하여 Stripe API에서 지원하는 추가 [고객 생성 매개 변수](https://stripe.com/docs/api/customers/create)를 전달할 수 있습니다.

    $stripeCustomer = $user->createAsStripeCustomer($options);

You may use the `asStripeCustomer` method if you want to return the Stripe customer object for a billable model:

청구 가능한 모델 Stripe 고객 객체를 반환하려면 `asStripeCustomer` 메소드를 사용할 수 있습니다:

    $stripeCustomer = $user->asStripeCustomer();

The `createOrGetStripeCustomer` method may be used if you would like to retrieve the Stripe customer object for a given billable model but are not sure whether the billable model is already a customer within Stripe. This method will create a new customer in Stripe if one does not already exist:

주어진 청구 가능 모델의 Stripe 고객 객체를 검색하려는 경우 `createOrGetStripeCustomer` 메소드를 사용할 수 있지만 청구 가능 모델이 이미 Stripe 내의 고객인지 확실하지 않은 경우가 있습니다. 이 메소드는 이미 존재하지 않는 경우 Stripe에 새로운 고객을 생성합니다:

    $stripeCustomer = $user->createOrGetStripeCustomer();

<a name="updating-customers"></a>
### Updating Customers
### 고객 업데이트

Occasionally, you may wish to update the Stripe customer directly with additional information. You may accomplish this using the `updateStripeCustomer` method. This method accepts an array of [customer update options supported by the Stripe API](https://stripe.com/docs/api/customers/update):

가끔은, Stripe 고객을 추가 정보와 함께 직접 업데이트하려는 경우가 있습니다. `updateStripeCustomer` 메소드를 사용하여 이를 수행할 수 있습니다. 이 메소드는 Stripe API에서 지원하는 [Stripe API에서 지원하는 고객 업데이트 옵션](https://stripe.com/docs/api/customers/update):

    $stripeCustomer = $user->updateStripeCustomer($options);

<a name="balances"></a>
### Balances
### 잔액

Stripe allows you to credit or debit a customer's "balance". Later, this balance will be credited or debited on new invoices. To check the customer's total balance you may use the `balance` method that is available on your billable model. The `balance` method will return a formatted string representation of the balance in the customer's currency:

Stripe는 고객의 "잔액"을 크레딧 또는 디비트 할 수 있습니다. 나중에 이 잔액은 새로운 청구서에 크레딧 또는 디비트됩니다. 고객의 총 잔액을 확인하려면 청구 가능 모델에서 사용할 수 있는 `balance` 메소드를 사용할 수 있습니다. `balance` 메소드는 고객의 통화에서 잔액의 형식화된 문자열 표현을 반환합니다:

    $balance = $user->balance();

To credit a customer's balance, you may provide a value to the `creditBalance` method. If you wish, you may also provide a description:

고객의 잔액을 크레딧하려면 `creditBalance` 메소드에 값을 제공할 수 있습니다. 원한다면 설명도 제공할 수 있습니다:

    $user->creditBalance(500, 'Premium customer top-up.');

Providing a value to the `debitBalance` method will debit the customer's balance:

`debitBalance` 메소드에 값을 제공하면 고객의 잔액이 차감됩니다:

    $user->debitBalance(300, 'Bad usage penalty.');

The `applyBalance` method will create new customer balance transactions for the customer. You may retrieve these transaction records using the `balanceTransactions` method, which may be useful in order to provide a log of credits and debits for the customer to review:

`applyBalance` 메소드는 고객에 대해 새로운 고객 잔액 거래를 생성합니다. `balanceTransactions` 메소드를 사용하여 이러한 거래 기록을 검색할 수 있습니다. 이는 고객이 검토할 수있는 크레딧 및 디비트의 로그를 제공하기 위해 유용합니다:

    // Retrieve all transactions...
    $transactions = $user->balanceTransactions();

    foreach ($transactions as $transaction) {
        // Transaction amount...
        $amount = $transaction->amount(); // $2.31

        // Retrieve the related invoice when available...
        $invoice = $transaction->invoice();
    }

<a name="tax-ids"></a>
### Tax IDs
### 세금 ID

Cashier offers an easy way to manage a customer's tax IDs. For example, the `taxIds` method may be used to retrieve all of the [tax IDs](https://stripe.com/docs/api/customer_tax_ids/object) that are assigned to a customer as a collection:

Cashier는 고객의 세금 ID를 관리하는 간단한 방법을 제공합니다. 예를 들어, `taxIds` 메소드는 모든 [tax IDs](https://stripe.com/docs/api/customer_tax_ids/object)를 수집으로 할당된 고객에게 검색하는 데 사용할 수 있습니다:

    $taxIds = $user->taxIds();

You can also retrieve a specific tax ID for a customer by its identifier:

고객의 특정 세금 ID를 식별자로 검색할 수도 있습니다:

    $taxId = $user->findTaxId('txi_belgium');

You may create a new Tax ID by providing a valid [type](https://stripe.com/docs/api/customer_tax_ids/object#tax_id_object-type) and value to the `createTaxId` method:

유효한 [type](https://stripe.com/docs/api/customer_tax_ids/object#tax_id_object-type)과 값을 `createTaxId` 메소드에 제공하여 새로운 세금 ID를 생성할 수 있습니다:

    $taxId = $user->createTaxId('eu_vat', 'BE0123456789');

The `createTaxId` method will immediately add the VAT ID to the customer's account. [Verification of VAT IDs is also done by Stripe](https://stripe.com/docs/invoicing/customer/tax-ids#validation); however, this is an asynchronous process. You can be notified of verification updates by subscribing to the `customer.tax_id.updated` webhook event and inspecting [the VAT IDs `verification` parameter](https://stripe.com/docs/api/customer_tax_ids/object#tax_id_object-verification). For more information on handling webhooks, please consult the [documentation on defining webhook handlers](#handling-stripe-webhooks).

`createTaxId` 메소드는 즉시 VAT ID를 고객 계정에 추가합니다. [VAT ID의 확인은 Stripe에 의해 수행됩니다](https://stripe.com/docs/invoicing/customer/tax-ids#validation); 그러나 이것은 비동기 프로세스입니다. `customer.tax_id.updated` 웹훅 이벤트를 구독하여 확인 업데이트를 알릴 수 있으며 [VAT ID `verification` 매개변수](https://stripe.com/docs/api/customer_tax_ids/object#tax_id_object-verification). 웹훅을 처리하는 방법에 대한 자세한 내용은 [웹훅 핸들러 정의에 대한 문서](#handling-stripe-webhooks)를 참조하십시오.

You may delete a tax ID using the `deleteTaxId` method:

`deleteTaxId` 메소드를 사용하여 세금 ID를 삭제할 수 있습니다:

    $user->deleteTaxId('txi_belgium');

<a name="syncing-customer-data-with-stripe"></a>
### Syncing Customer Data With Stripe
### 고객 데이터를 Stripe와 동기화

Typically, when your application's users update their name, email address, or other information that is also stored by Stripe, you should inform Stripe of the updates. By doing so, Stripe's copy of the information will be in sync with your application's.

일반적으로, 어플리케이션의 사용자가 이름, 이메일 주소 또는 Stripe에 저장된 다른 정보를 업데이트 할 때 Stripe에 업데이트를 알려야 합니다. 이렇게 하면 Stripe의 정보는 어플리케이션의 정보와 동기화됩니다.

To automate this, you may define an event listener on your billable model that reacts to the model's `updated` event. Then, within your event listener, you may invoke the `syncStripeCustomerDetails` method on the model:

이를 자동화하려면, 청구 가능 모델에 이벤트 리스너를 정의하여 모델의 `updated` 이벤트에 반응하도록 할 수 있습니다. 그런 다음 이벤트 리스너 내에서 모델에서 `syncStripeCustomerDetails` 메소드를 호출할 수 있습니다:

    use App\Models\User;
    use function Illuminate\Events\queueable;

    /**
     * The "booted" method of the model.
     */
    protected static function booted(): void
    {
        static::updated(queueable(function (User $customer) {
            if ($customer->hasStripeId()) {
                $customer->syncStripeCustomerDetails();
            }
        }));
    }

Now, every time your customer model is updated, its information will be synced with Stripe. For convenience, Cashier will automatically sync your customer's information with Stripe on the initial creation of the customer.

이제, 고객 모델이 업데이트 될 때마다 정보가 Stripe와 동기화됩니다. 편의를 위해, Cashier는 고객을 최초로 생성할 때 고객의 정보를 Stripe와 자동으로 동기화합니다.

You may customize the columns used for syncing customer information to Stripe by overriding a variety of methods provided by Cashier. For example, you may override the `stripeName` method to customize the attribute that should be considered the customer's "name" when Cashier syncs customer information to Stripe:

Cashier가 고객 정보를 Stripe에 동기화 할 때 고객의 "이름"으로 고려해야 하는 속성을 사용자 정의하려면 다양한 메소드를 제공하는 메소드를 재정의할 수 있습니다. 예를 들어, 다음과 같이 `stripeName` 메소드를 재정의 할 수 있습니다:

    /**
     * Get the customer name that should be synced to Stripe.
     */
    public function stripeName(): string|null
    {
        return $this->company_name;
    }

Similarly, you may override the `stripeEmail`, `stripePhone`, `stripeAddress`, and `stripePreferredLocales` methods. These methods will sync information to their corresponding customer parameters when [updating the Stripe customer object](https://stripe.com/docs/api/customers/update). If you wish to take total control over the customer information sync process, you may override the `syncStripeCustomerDetails` method.

비슷하게, `stripeEmail`, `stripePhone`, `stripeAddress`, `stripePreferredLocales` 메소드를 재정의 할 수 있습니다. 이러한 메소드는 [Stripe 고객 개체 업데이트](https://stripe.com/docs/api/customers/update) 해당 고객 매개변수에 정보를 동기화합니다. 고객 정보 동기화 프로세스를 완전히 제어하려면 `syncStripeCustomerDetails` 메소드를 재정의 할 수 있습니다.

<a name="billing-portal"></a>
### Billing Portal
### 청구 포털

Stripe offers [an easy way to set up a billing portal](https://stripe.com/docs/billing/subscriptions/customer-portal) so that your customer can manage their subscription, payment methods, and view their billing history. You can redirect your users to the billing portal by invoking the `redirectToBillingPortal` method on the billable model from a controller or route:

Stripe는 [청구 포털을 간단히 설정하는 방법](https://stripe.com/docs/billing/subscriptions/customer-portal)을 제공하여 고객이 구독, 결제 방법 및 청구 내역을 관리할 수 있도록 합니다. 컨트롤러 또는 라우트에서 청구 가능 모델의 `redirectToBillingPortal` 메소드를 호출하여 사용자를 청구 포털로 리디렉션 할 수 있습니다:

    use Illuminate\Http\Request;

    Route::get('/billing-portal', function (Request $request) {
        return $request->user()->redirectToBillingPortal();
    });

By default, when the user is finished managing their subscription, they will be able to return to the `home` route of your application via a link within the Stripe billing portal. You may provide a custom URL that the user should return to by passing the URL as an argument to the `redirectToBillingPortal` method:

기본적으로, 사용자가 구독을 관리한 후에는 Stripe 청구 포털 내의 링크를 통해 어플리케이션의 `home` 라우트로 돌아갈 수 있습니다. `redirectToBillingPortal` 메소드에 URL을 인수로 전달하여 사용자가 돌아갈 사용자 커스텀 URL을 제공할 수 있습니다:

    use Illuminate\Http\Request;

    Route::get('/billing-portal', function (Request $request) {
        return $request->user()->redirectToBillingPortal(route('billing'));
    });

If you would like to generate the URL to the billing portal without generating an HTTP redirect response, you may invoke the `billingPortalUrl` method:

HTTP 리디렉션 응답을 생성하지 않고 청구 포털의 URL을 생성하려면, `billingPortalUrl` 메소드를 호출할 수 있습니다:

    $url = $request->user()->billingPortalUrl(route('billing'));

<a name="payment-methods"></a>
## Payment Methods
## 결제 방법

<a name="storing-payment-methods"></a>
### Storing Payment Methods
### 결제 방법 저장

In order to create subscriptions or perform "one-off" charges with Stripe, you will need to store a payment method and retrieve its identifier from Stripe. The approach used to accomplish this differs based on whether you plan to use the payment method for subscriptions or single charges, so we will examine both below.

Stripe로 구독을 생성하거나 "일회성" 결제를 수행하려면 결제 방법을 저장하고 Stripe에서 식별자를 검색해야 합니다. 이를 달성하기 위해 사용되는 접근 방식은 결제 방법을 구독 또는 단일 결제에 사용할 것인지 여부에 따라 다릅니다. 따라서 아래에서 두 가지 방법을 살펴보겠습니다.

<a name="payment-methods-for-subscriptions"></a>
#### Payment Methods For Subscriptions
#### 구독을 위한 결제 방법

When storing a customer's credit card information for future use by a subscription, the Stripe "Setup Intents" API must be used to securely gather the customer's payment method details. A "Setup Intent" indicates to Stripe the intention to charge a customer's payment method. Cashier's `Billable` trait includes the `createSetupIntent` method to easily create a new Setup Intent. You should invoke this method from the route or controller that will render the form which gathers your customer's payment method details:

구독에 의해 향후 사용을 위해 고객의 신용 카드 정보를 저장할 때 Stripe "Setup Intents" API를 사용하여 고객의 결제 방법 세부 정보를 안전하게 수집해야 합니다. "Setup Intent"는 Stripe에 고객의 결제 방법을 청구할 의도를 나타냅니다. Cashier의 `Billable` 트레잇은 새로운 Setup Intent를 쉽게 만들 수 있는 `createSetupIntent` 메소드를 포함합니다. 고객의 결제 방법 세부 정보를 수집하는 폼을 렌더링하는 라우트 또는 컨트롤러에서 이 메소드를 호출해야 합니다:

    return view('update-payment-method', [
        'intent' => $user->createSetupIntent()
    ]);

After you have created the Setup Intent and passed it to the view, you should attach its secret to the element that will gather the payment method. For example, consider this "update payment method" form:

Setup Intent를 생성하고 뷰로 전달한 후, 결제 방법을 수집할 요소에 비밀을 연결해야 합니다. 예를 들어, 다음 "결제 방법 업데이트" 폼을 고려해 보겠습니다:

```html
<input id="card-holder-name" type="text">

<!-- Stripe Elements Placeholder -->
<div id="card-element"></div>

<button id="card-button" data-secret="{{ $intent->client_secret }}">
    Update Payment Method
</button>
```

Next, the Stripe.js library may be used to attach a [Stripe Element](https://stripe.com/docs/stripe-js) to the form and securely gather the customer's payment details:

그런 다음, Stripe.js 라이브러리를 사용하여 [Stripe Element](https://stripe.com/docs/stripe-js)를 폼에 연결하고 고객의 결제 세부 정보를 안전하게 수집할 수 있습니다:

```html
<script src="https://js.stripe.com/v3/"></script>

<script>
    const stripe = Stripe('stripe-public-key');

    const elements = stripe.elements();
    const cardElement = elements.create('card');

    cardElement.mount('#card-element');
</script>
```

Next, the card can be verified and a secure "payment method identifier" can be retrieved from Stripe using [Stripe's `confirmCardSetup` method](https://stripe.com/docs/js/setup_intents/confirm_card_setup):

다음으로, 카드를 확인하고 Stripe에서 [Stripe의 `confirmCardSetup` 메소드](https://stripe.com/docs/js/setup_intents/confirm_card_setup)를 사용하여 안전한 "결제 방법 식별자"를 검색할 수 있습니다:

```js
const cardHolderName = document.getElementById('card-holder-name');
const cardButton = document.getElementById('card-button');
const clientSecret = cardButton.dataset.secret;

cardButton.addEventListener('click', async (e) => {
    const { setupIntent, error } = await stripe.confirmCardSetup(
        clientSecret, {
            payment_method: {
                card: cardElement,
                billing_details: { name: cardHolderName.value }
            }
        }
    );

    if (error) {
        // Display "error.message" to the user...
    } else {
        // The card has been verified successfully...
    }
});
```

After the card has been verified by Stripe, you may pass the resulting `setupIntent.payment_method` identifier to your Laravel application, where it can be attached to the customer. The payment method can either be [added as a new payment method](#adding-payment-methods) or [used to update the default payment method](#updating-the-default-payment-method). You can also immediately use the payment method identifier to [create a new subscription](#creating-subscriptions).

Stripe가 카드를 확인한 후, 결과 `setupIntent.payment_method` 식별자를 Laravel 어플리케이션으로 전달할 수 있습니다. 이 식별자는 고객에게 연결할 수 있습니다. 결제 방법은 [새로운 결제 방법으로 추가](#adding-payment-methods)하거나 [기본 결제 방법을 업데이트](#updating-the-default-payment-method)하는 데 사용할 수 있습니다. 또한 결제 방법 식별자를 즉시 [새 구독을 생성](#creating-subscriptions)하는 데 사용할 수도 있습니다.

> **Note**  
> If you would like more information about Setup Intents and gathering customer payment details please [review this overview provided by Stripe](https://stripe.com/docs/payments/save-and-reuse#php).
>
> **참고**
> 설정 의도와 고객 결제 세부 정보 수집에 대한 자세한 내용은 [Stripe에서 제공하는 개요](https://stripe.com/docs/payments/save-and-reuse#php)를 참조하십시오.

<a name="payment-methods-for-single-charges"></a>
#### Payment Methods For Single Charges
#### 단일 결제를 위한 결제 방법

Of course, when making a single charge against a customer's payment method, we will only need to use a payment method identifier once. Due to Stripe limitations, you may not use the stored default payment method of a customer for single charges. You must allow the customer to enter their payment method details using the Stripe.js library. For example, consider the following form:

물론, 고객의 결제 방법에 대해 단일 결제를 하는 경우, 결제 방법 식별자를 한 번만 사용하면 됩니다. Stripe 제한 사항으로 인해, 고객의 저장된 기본 결제 방법을 단일 결제에 사용할 수 없습니다. Stripe.js 라이브러리를 사용하여 고객이 결제 방법 세부 정보를 입력할 수 있도록 해야 합니다. 예를 들어, 다음 폼을 고려해 보겠습니다:

```html
<input id="card-holder-name" type="text">

<!-- Stripe Elements Placeholder -->
<div id="card-element"></div>

<button id="card-button">
    Process Payment
</button>
```

After defining such a form, the Stripe.js library may be used to attach a [Stripe Element](https://stripe.com/docs/stripe-js) to the form and securely gather the customer's payment details:

이러한 폼을 정의한 후, Stripe.js 라이브러리를 사용하여 [Stripe Element](https://stripe.com/docs/stripe-js)를 폼에 연결하고 고객의 결제 세부 정보를 안전하게 수집할 수 있습니다:

```html
<script src="https://js.stripe.com/v3/"></script>

<script>
    const stripe = Stripe('stripe-public-key');

    const elements = stripe.elements();
    const cardElement = elements.create('card');

    cardElement.mount('#card-element');
</script>
```

Next, the card can be verified and a secure "payment method identifier" can be retrieved from Stripe using [Stripe's `createPaymentMethod` method](https://stripe.com/docs/stripe-js/reference#stripe-create-payment-method):

다음으로, 카드를 확인하고 Stripe에서 [Stripe의 `createPaymentMethod` 메소드](https://stripe.com/docs/stripe-js/reference#stripe-create-payment-method)를 사용하여 안전한 "결제 방법 식별자"를 검색할 수 있습니다:

```js
const cardHolderName = document.getElementById('card-holder-name');
const cardButton = document.getElementById('card-button');

cardButton.addEventListener('click', async (e) => {
    const { paymentMethod, error } = await stripe.createPaymentMethod(
        'card', cardElement, {
            billing_details: { name: cardHolderName.value }
        }
    );

    if (error) {
        // Display "error.message" to the user...
    } else {
        // The card has been verified successfully...
    }
});
```

If the card is verified successfully, you may pass the `paymentMethod.id` to your Laravel application and process a [single charge](#simple-charge).

카드가 성공적으로 확인되면, `paymentMethod.id`를 Laravel 어플리케이션으로 전달하고 [단일 결제](#simple-charge)를 처리할 수 있습니다.

<a name="retrieving-payment-methods"></a>
### Retrieving Payment Methods
### 결제 방법 검색

The `paymentMethods` method on the billable model instance returns a collection of `Laravel\Cashier\PaymentMethod` instances:

청구 가능 모델 인스턴스인 `paymentMethods` 메소드는 `Laravel\Cashier\PaymentMethod` 인스턴스의 컬렉션을 반환합니다:

    $paymentMethods = $user->paymentMethods();

By default, this method will return payment methods of the `card` type. To retrieve payment methods of a different type, you may pass the `type` as an argument to the method:

기본적으로, 이 메소드는 `card` 유형의 결제 방법을 반환합니다. 다른 유형의 결제 방법을 검색하려면, 메소드에 `type`을 인수로 전달할 수 있습니다:

    $paymentMethods = $user->paymentMethods('sepa_debit');

To retrieve the customer's default payment method, the `defaultPaymentMethod` method may be used:

고객의 기본 결제 방법을 검색하려면, `defaultPaymentMethod` 메소드를 사용할 수 있습니다:

    $paymentMethod = $user->defaultPaymentMethod();

You can retrieve a specific payment method that is attached to the billable model using the `findPaymentMethod` method:

`findPaymentMethod` 메소드를 사용하여 청구 가능 모델에 첨부된 특정 결제 방법을 검색할 수 있습니다:

    $paymentMethod = $user->findPaymentMethod($paymentMethodId);

<a name="check-for-a-payment-method"></a>
### Determining If A User Has A Payment Method
### 사용자가 결제 방법을 가지고 있는지 확인

To determine if a billable model has a default payment method attached to their account, invoke the `hasDefaultPaymentMethod` method:

청구 가능 모델이 계정에 첨부된 기본 결제 방법이 있는지 확인하려면, `hasDefaultPaymentMethod` 메소드를 호출합니다:

    if ($user->hasDefaultPaymentMethod()) {
        // ...
    }

You may use the `hasPaymentMethod` method to determine if a billable model has at least one payment method attached to their account:

`hasPaymentMethod` 메소드를 사용하여 청구 가능 모델이 계정에 최소 한 개의 결제 방법을 첨부했는지 확인할 수 있습니다:

    if ($user->hasPaymentMethod()) {
        // ...
    }

This method will determine if the billable model has payment methods of the `card` type. To determine if a payment method of another type exists for the model, you may pass the `type` as an argument to the method:

이 메소드는 청구 가능 모델이 `card` 유형의 결제 방법을 가지고 있는지 확인합니다. 모델에 다른 유형의 결제 방법이 있는지 확인하려면, 메소드에 `type`을 인수로 전달할 수 있습니다:

    if ($user->hasPaymentMethod('sepa_debit')) {
        // ...
    }

<a name="updating-the-default-payment-method"></a>
### Updating The Default Payment Method
### 기본 결제 방법 업데이트

The `updateDefaultPaymentMethod` method may be used to update a customer's default payment method information. This method accepts a Stripe payment method identifier and will assign the new payment method as the default billing payment method:

`updateDefaultPaymentMethod` 메소드는 고객의 기본 결제 방법 정보를 업데이트하는 데 사용할 수 있습니다. 이 메소드는 Stripe 결제 방법 식별자를 받아 새 결제 방법을 기본 청구 결제 방법으로 할당합니다:

    $user->updateDefaultPaymentMethod($paymentMethod);

To sync your default payment method information with the customer's default payment method information in Stripe, you may use the `updateDefaultPaymentMethodFromStripe` method:

기본 결제 방법 정보를 Stripe의 고객의 기본 결제 방법 정보와 동기화하려면, `updateDefaultPaymentMethodFromStripe` 메소드를 사용할 수 있습니다:

    $user->updateDefaultPaymentMethodFromStripe();

> **Warning**  
> The default payment method on a customer can only be used for invoicing and creating new subscriptions. Due to limitations imposed by Stripe, it may not be used for single charges.
>
> **경고**
> 고객의 기본 결제 방법은 청구서 작성 및 새 구독 생성에만 사용할 수 있습니다. Stripe에 의해 부과된 제한으로 인해, 단일 결제에 사용할 수 없습니다.

<a name="adding-payment-methods"></a>
### Adding Payment Methods
### 결제 방법 추가

To add a new payment method, you may call the `addPaymentMethod` method on the billable model, passing the payment method identifier:

새 결제 방법을 추가하려면, 청구 가능 모델의 `addPaymentMethod` 메소드를 호출하고 결제 방법 식별자를 전달할 수 있습니다:

    $user->addPaymentMethod($paymentMethod);

> **Note**  
> To learn how to retrieve payment method identifiers please review the [payment method storage documentation](#storing-payment-methods).
>
> **참고**
> 결제 방법 식별자를 검색하는 방법에 대한 자세한 내용은 [결제 방법 저장 문서](#storing-payment-methods)를 참조하세요.

<a name="deleting-payment-methods"></a>
### Deleting Payment Methods
### 결제 방법 삭제

To delete a payment method, you may call the `delete` method on the `Laravel\Cashier\PaymentMethod` instance you wish to delete:

결제 방법을 삭제하려면, 삭제하려는 `Laravel\Cashier\PaymentMethod` 인스턴스의 `delete` 메소드를 호출할 수 있습니다:

    $paymentMethod->delete();

The `deletePaymentMethod` method will delete a specific payment method from the billable model:

`deletePaymentMethod` 메소드는 청구 가능 모델에서 특정 결제 방법을 삭제합니다:

    $user->deletePaymentMethod('pm_visa');

The `deletePaymentMethods` method will delete all of the payment method information for the billable model:

`deletePaymentMethods` 메소드는 청구 가능 모델의 모든 결제 방법 정보를 삭제합니다:

    $user->deletePaymentMethods();

By default, this method will delete payment methods of the `card` type. To delete payment methods of a different type you can pass the `type` as an argument to the method:

기본적으로, 이 메소드는 `card` 유형의 결제 방법을 삭제합니다. 다른 유형의 결제 방법을 삭제하려면, 메소드에 `type`을 인수로 전달할 수 있습니다:

    $user->deletePaymentMethods('sepa_debit');

> **Warning**  
> If a user has an active subscription, your application should not allow them to delete their default payment method.
>
> **경고**
> 사용자가 활성 구독을 가지고 있는 경우, 어플리케이션은 기본 결제 방법을 삭제하지 않도록 해야 합니다.

<a name="subscriptions"></a>
## Subscriptions
## 구독

Subscriptions provide a way to set up recurring payments for your customers. Stripe subscriptions managed by Cashier provide support for multiple subscription prices, subscription quantities, trials, and more.

구독은 고객에게 정기적인 결제를 설정하는 방법을 제공합니다. Cashier가 관리하는 Stripe 구독은 여러 구독 가격, 구독 수량, 시험 기간 등을 지원합니다.

<a name="creating-subscriptions"></a>
### Creating Subscriptions
### 구독 생성

To create a subscription, first retrieve an instance of your billable model, which typically will be an instance of `App\Models\User`. Once you have retrieved the model instance, you may use the `newSubscription` method to create the model's subscription:

구독을 생성하려면, 먼저 청구 가능 모델의 인스턴스를 검색해야 합니다. 일반적으로 `App\Models\User` 인스턴스가 됩니다. 모델 인스턴스를 검색한 후, `newSubscription` 메소드를 사용하여 모델의 구독을 생성할 수 있습니다:

    use Illuminate\Http\Request;

    Route::post('/user/subscribe', function (Request $request) {
        $request->user()->newSubscription(
            'default', 'price_monthly'
        )->create($request->paymentMethodId);

        // ...
    });

The first argument passed to the `newSubscription` method should be the internal name of the subscription. If your application only offers a single subscription, you might call this `default` or `primary`. This subscription name is only for internal application usage and is not meant to be shown to users. In addition, it should not contain spaces and it should never be changed after creating the subscription. The second argument is the specific price the user is subscribing to. This value should correspond to the price's identifier in Stripe.

`newSubscription` 메소드에 전달된 첫 번째 인수는 구독의 내부 이름이어야 합니다. 어플리케이션이 단일 구독만 제공하는 경우, `default` 또는 `primary`라고 부를 수 있습니다. 이 구독 이름은 어플리케이션 내부에서만 사용되며 사용자에게 표시되는 것이 아닙니다. 또한, 공백을 포함해서는 안 되며 구독을 생성한 후에는 절대로 변경해서는 안 됩니다. 두 번째 인수는 사용자가 구독하는 특정 가격입니다. 이 값은 Stripe의 가격 식별자와 일치해야 합니다.

The `create` method, which accepts [a Stripe payment method identifier](#storing-payment-methods) or Stripe `PaymentMethod` object, will begin the subscription as well as update your database with the billable model's Stripe customer ID and other relevant billing information.

`create` 메소드는 [Stripe 결제 방법 식별자](#storing-payment-methods) 또는 Stripe `PaymentMethod` 객체를 인수로 받아 구독을 시작하고 청구 가능 모델의 Stripe 고객 ID 및 기타 관련 청구 정보를 데이터베이스에 업데이트합니다.

> **Warning**  
> Passing a payment method identifier directly to the `create` subscription method will also automatically add it to the user's stored payment methods.
>
> **경고**
> 결제 방법 식별자를 `create` 구독 메소드에 직접 전달하면 사용자의 저장된 결제 방법에도 자동으로 추가됩니다.

<a name="collecting-recurring-payments-via-invoice-emails"></a>
#### Collecting Recurring Payments Via Invoice Emails
#### 인보이스 이메일을 통한 정기 결제 수집

Instead of collecting a customer's recurring payments automatically, you may instruct Stripe to email an invoice to the customer each time their recurring payment is due. Then, the customer may manually pay the invoice once they receive it. The customer does not need to provide a payment method up front when collecting recurring payments via invoices:

정기적인 결제를 자동으로 수집하는 대신, Stripe에게 정기적인 결제가 만료되는 때마다 고객에게 인보이스를 이메일로 보내도록 지시할 수 있습니다. 그런 다음 고객은 인보이스를 받은 후 수동으로 인보이스를 지불할 수 있습니다. 인보이스를 통해 정기적인 결제를 수집할 때, 고객은 결제 방법을 사전에 제공할 필요가 없습니다:

    $user->newSubscription('default', 'price_monthly')->createAndSendInvoice();

The amount of time a customer has to pay their invoice before their subscription is cancelled is determined by the `days_until_due` option. By default, this is 30 days; however, you may provide a specific value for this option if you wish:

구독이 취소되기 전에 고객이 인보이스를 지불할 수 있는 시간은 `days_until_due` 옵션에 의해 결정됩니다. 기본값은 30일이지만 원한다면 이 옵션에 특정 값을 제공할 수 있습니다:

    $user->newSubscription('default', 'price_monthly')->createAndSendInvoice([], [
        'days_until_due' => 30
    ]);

<a name="subscription-quantities"></a>
#### Quantities
#### 수량

If you would like to set a specific [quantity](https://stripe.com/docs/billing/subscriptions/quantities) for the price when creating the subscription, you should invoke the `quantity` method on the subscription builder before creating the subscription:

구독을 생성할 때 가격에 특정 [수량](https://stripe.com/docs/billing/subscriptions/quantities)을 설정하려면, 구독 빌더에서 `quantity` 메소드를 호출한 후 구독을 생성해야 합니다:

    $user->newSubscription('default', 'price_monthly')
         ->quantity(5)
         ->create($paymentMethod);

<a name="additional-details"></a>
#### Additional Details
#### 추가 정보

If you would like to specify additional [customer](https://stripe.com/docs/api/customers/create) or [subscription](https://stripe.com/docs/api/subscriptions/create) options supported by Stripe, you may do so by passing them as the second and third arguments to the `create` method:

Stripe에서 지원하는 추가 [고객](https://stripe.com/docs/api/customers/create) 또는 [구독](https://stripe.com/docs/api/subscriptions/create) 옵션을 지정하려면, `create` 메소드의 두 번째 및 세 번째 인수로 전달하면 됩니다:

    $user->newSubscription('default', 'price_monthly')->create($paymentMethod, [
        'email' => $email,
    ], [
        'metadata' => ['note' => 'Some extra information.'],
    ]);

<a name="coupons"></a>
#### Coupons
#### 쿠폰

If you would like to apply a coupon when creating the subscription, you may use the `withCoupon` method:

구독을 생성할 때 쿠폰을 적용하려면 `withCoupon` 메소드를 사용할 수 있습니다:

    $user->newSubscription('default', 'price_monthly')
         ->withCoupon('code')
         ->create($paymentMethod);

Or, if you would like to apply a [Stripe promotion code](https://stripe.com/docs/billing/subscriptions/discounts/codes), you may use the `withPromotionCode` method:

또는, [Stripe 프로모션 코드](https://stripe.com/docs/billing/subscriptions/discounts/codes)를 적용하려면 `withPromotionCode` 메소드를 사용할 수 있습니다:

    $user->newSubscription('default', 'price_monthly')
         ->withPromotionCode('promo_code_id')
         ->create($paymentMethod);

The given promotion code ID should be the Stripe API ID assigned to the promotion code and not the customer facing promotion code. If you need to find a promotion code ID based on a given customer facing promotion code, you may use the `findPromotionCode` method:

주어진 프로모션 코드 ID는 프로모션 코드에 할당된 Stripe API ID이어야 하며, 고객이 보는 프로모션 코드가 아닙니다. 주어진 고객이 보는 프로모션 코드에 기반하여 프로모션 코드 ID를 찾아야 하는 경우, `findPromotionCode` 메소드를 사용할 수 있습니다:

    // Find a promotion code ID by its customer facing code...
    $promotionCode = $user->findPromotionCode('SUMMERSALE');

    // Find an active promotion code ID by its customer facing code...
    $promotionCode = $user->findActivePromotionCode('SUMMERSALE');

In the example above, the returned `$promotionCode` object is an instance of `Laravel\Cashier\PromotionCode`. This class decorates an underlying `Stripe\PromotionCode` object. You can retrieve the coupon related to the promotion code by invoking the `coupon` method:

위의 예에서, 반환된 `$promotionCode` 객체는 `Laravel\Cashier\PromotionCode`의 인스턴스입니다. 이 클래스는 기본 `Stripe\PromotionCode` 객체를 장식합니다. 프로모션 코드와 관련된 쿠폰을 `coupon` 메소드를 호출하여 가져올 수 있습니다:

    $coupon = $user->findPromotionCode('SUMMERSALE')->coupon();

The coupon instance allows you to determine the discount amount and whether the coupon represents a fixed discount or percentage based discount:

쿠폰 인스턴스를 사용하면 할인 금액을 결정하고 쿠폰이 고정 할인인지 비율 기반 할인인지 여부를 확인할 수 있습니다:

    if ($coupon->isPercentage()) {
        return $coupon->percentOff().'%'; // 21.5%
    } else {
        return $coupon->amountOff(); // $5.99
    }

You can also retrieve the discounts that are currently applied to a customer or subscription:

또한, 현재 고객 또는 구독에 적용된 할인을 가져올 수 있습니다:

    $discount = $billable->discount();

    $discount = $subscription->discount();

The returned `Laravel\Cashier\Discount` instances decorate an underlying `Stripe\Discount` object instance. You may retrieve the coupon related to this discount by invoking the `coupon` method:

반환된 `Laravel\Cashier\Discount` 인스턴스는 기본 `Stripe\Discount` 객체 인스턴스를 장식합니다. `coupon` 메소드를 호출하여 이 할인과 관련된 쿠폰을 가져올 수 있습니다:

    $coupon = $subscription->discount()->coupon();

If you would like to apply a new coupon or promotion code to a customer or subscription, you may do so via the `applyCoupon` or `applyPromotionCode` methods:

새로운 쿠폰 또는 프로모션 코드를 고객 또는 구독에 적용하려면, `applyCoupon` 또는 `applyPromotionCode` 메소드를 통해 수행할 수 있습니다:

    $billable->applyCoupon('coupon_id');
    $billable->applyPromotionCode('promotion_code_id');

    $subscription->applyCoupon('coupon_id');
    $subscription->applyPromotionCode('promotion_code_id');

Remember, you should use the Stripe API ID assigned to the promotion code and not the customer facing promotion code. Only one coupon or promotion code can be applied to a customer or subscription at a given time.

명심하세요, Stripe API ID는 고객이 보는 프로모션 코드가 아닌 프로모션 코드에 할당된 Stripe API ID를 사용해야 합니다. 한 번에 고객 또는 구독에 하나의 쿠폰 또는 프로모션 코드만 적용할 수 있습니다.

For more info on this subject, please consult the Stripe documentation regarding [coupons](https://stripe.com/docs/billing/subscriptions/coupons) and [promotion codes](https://stripe.com/docs/billing/subscriptions/coupons/codes).

이 주제에 대한 자세한 내용은, [쿠폰](https://stripe.com/docs/billing/subscriptions/coupons) 및 [프로모션 코드](https://stripe.com/docs/billing/subscriptions/coupons/codes)에 대한 Stripe 문서를 참조하세요.

<a name="adding-subscriptions"></a>
#### Adding Subscriptions
#### 추가 구독

If you would like to add a subscription to a customer who already has a default payment method you may invoke the `add` method on the subscription builder:

기본 결제 방법이 있는 고객에게 구독을 추가하려면, 구독 빌더의 `add` 메소드를 호출할 수 있습니다:

    use App\Models\User;

    $user = User::find(1);

    $user->newSubscription('default', 'price_monthly')->add();

<a name="creating-subscriptions-from-the-stripe-dashboard"></a>
#### Creating Subscriptions From The Stripe Dashboard
#### Stripe 대시보드에서 구독 생성

You may also create subscriptions from the Stripe dashboard itself. When doing so, Cashier will sync newly added subscriptions and assign them a name of `default`. To customize the subscription name that is assigned to dashboard created subscriptions, [extend the `WebhookController`](#defining-webhook-event-handlers) and overwrite the `newSubscriptionName` method.

또한, Stripe 대시보드에서 구독을 생성할 수 있습니다. 이렇게 하면 Cashier는 새로 추가된 구독을 동기화하고 `default`라는 이름을 할당합니다. 대시보드에서 생성된 구독에 할당된 구독 이름을 사용자 정의하려면, [WebhookController 확장](#defining-webhook-event-handlers)하고 `newSubscriptionName` 메소드를 덮어쓰세요.

In addition, you may only create one type of subscription via the Stripe dashboard. If your application offers multiple subscriptions that use different names, only one type of subscription may be added through the Stripe dashboard.

추가로, Stripe 대시보드를 통해 하나의 유형의 구독만 생성할 수 있습니다. 어플리케이션이 다른 이름을 사용하는 여러 구독을 제공하는 경우, Stripe 대시보드를 통해 하나의 유형의 구독만 추가할 수 있습니다.

Finally, you should always make sure to only add one active subscription per type of subscription offered by your application. If a customer has two `default` subscriptions, only the most recently added subscription will be used by Cashier even though both would be synced with your application's database.

마지막으로, 어플리케이션이 제공하는 구독 유형별로 활성 구독을 하나만 추가하도록 항상 확인해야 합니다. 고객이 두 개의 `default` 구독을 가지고 있으면, 두 구독 모두 어플리케이션의 데이터베이스와 동기화되지만, Cashier에서는 가장 최근에 추가된 구독만 사용됩니다.

<a name="checking-subscription-status"></a>
### Checking Subscription Status
### 구독 상태 확인

Once a customer is subscribed to your application, you may easily check their subscription status using a variety of convenient methods. First, the `subscribed` method returns `true` if the customer has an active subscription, even if the subscription is currently within its trial period. The `subscribed` method accepts the name of the subscription as its first argument:

고객이 어플리케이션에 구독하면, 다양한 편리한 메소드를 사용하여 구독 상태를 쉽게 확인할 수 있습니다. 먼저, `subscribed` 메소드는 구독이 현재 시험 기간 내에 있더라도 고객이 활성 구독을 가지고 있으면 `true`를 반환합니다. `subscribed` 메소드는 첫 번째 인수로 구독의 이름을 받습니다:

    if ($user->subscribed('default')) {
        // ...
    }

The `subscribed` method also makes a great candidate for a [route middleware](/docs/{{version}}/middleware), allowing you to filter access to routes and controllers based on the user's subscription status:

`subscribed` 메소드는 [라우트 미들웨어](/docs/{{version}}/middleware)로도 사용할 수 있습니다. 이를 통해 사용자의 구독 상태에 따라 라우트와 컨트롤러에 대한 액세스를 필터링할 수 있습니다:

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class EnsureUserIsSubscribed
    {
        /**
         * Handle an incoming request.
         *
         * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
         */
        public function handle(Request $request, Closure $next): Response
        {
            if ($request->user() && ! $request->user()->subscribed('default')) {
                // This user is not a paying customer...
                return redirect('billing');
            }

            return $next($request);
        }
    }

If you would like to determine if a user is still within their trial period, you may use the `onTrial` method. This method can be useful for determining if you should display a warning to the user that they are still on their trial period:

사용자가 시험 기간 내에 있는지 확인하려면, `onTrial` 메소드를 사용할 수 있습니다. 이 메소드는 사용자가 시험 기간 내에 있음을 알리는 경고를 표시해야 하는지 여부를 결정하는 데 유용합니다:

    if ($user->subscription('default')->onTrial()) {
        // ...
    }

The `subscribedToProduct` method may be used to determine if the user is subscribed to a given product based on a given Stripe product's identifier. In Stripe, products are collections of prices. In this example, we will determine if the user's `default` subscription is actively subscribed to the application's "premium" product. The given Stripe product identifier should correspond to one of your product's identifiers in the Stripe dashboard:

`subscribedToProduct` 메소드는 주어진 Stripe 제품의 식별자를 기반으로 사용자가 주어진 제품에 구독되어 있는지 확인하는 데 사용할 수 있습니다. Stripe에서 제품은 가격의 모음입니다. 이 예에서는 사용자의 `default` 구독이 어플리케이션의 "premium" 제품에 구독되어 있는지 확인합니다. 주어진 Stripe 제품 식별자는 Stripe 대시보드의 제품 식별자 중 하나와 일치해야 합니다:

    if ($user->subscribedToProduct('prod_premium', 'default')) {
        // ...
    }

By passing an array to the `subscribedToProduct` method, you may determine if the user's `default` subscription is actively subscribed to the application's "basic" or "premium" product:

`subscribedToProduct` 메소드에 배열을 전달하면, 사용자의 `default` 구독이 어플리케이션의 "basic" 또는 "premium" 제품에 구독되어 있는지 확인할 수 있습니다:

    if ($user->subscribedToProduct(['prod_basic', 'prod_premium'], 'default')) {
        // ...
    }

The `subscribedToPrice` method may be used to determine if a customer's subscription corresponds to a given price ID:

`subscribedToPrice` 메소드는 고객의 구독이 주어진 가격 ID에 해당하는지 확인하는 데 사용할 수 있습니다:

    if ($user->subscribedToPrice('price_basic_monthly', 'default')) {
        // ...
    }

The `recurring` method may be used to determine if the user is currently subscribed and is no longer within their trial period:

`recurring` 메소드는 사용자가 현재 구독 중이고 시험 기간 내에 있지 않은지 확인하는 데 사용할 수 있습니다:

    if ($user->subscription('default')->recurring()) {
        // ...
    }

> **Warning**  
> If a user has two subscriptions with the same name, the most recent subscription will always be returned by the `subscription` method. For example, a user might have two subscription records named `default`; however, one of the subscriptions may be an old, expired subscription, while the other is the current, active subscription. The most recent subscription will always be returned while older subscriptions are kept in the database for historical review.
>
> **경고**
> 사용자에게 동일한 이름의 두 개의 구독이 있는 경우, `subscription` 메소드는 항상 가장 최근의 구독을 반환합니다. 예를 들어 사용자는 `default`라는 두 개의 구독 기록을 가질 수 있습니다. 그러나 구독 중 하나는 오래된 만료된 구독이고 다른 하나는 현재 활성화된 구독일 수 있습니다. 가장 최근의 구독은 항상 반환되고 오래된 구독은 데이터베이스에 보관되어 이력을 검토합니다.

<a name="cancelled-subscription-status"></a>
#### Canceled Subscription Status
#### 취소된 구독 상태

To determine if the user was once an active subscriber but has canceled their subscription, you may use the `canceled` method:

사용자가 한때 활성 구독자였지만 구독을 취소했는지 확인하려면, `canceled` 메소드를 사용할 수 있습니다:

    if ($user->subscription('default')->canceled()) {
        // ...
    }

You may also determine if a user has canceled their subscription but are still on their "grace period" until the subscription fully expires. For example, if a user cancels a subscription on March 5th that was originally scheduled to expire on March 10th, the user is on their "grace period" until March 10th. Note that the `subscribed` method still returns `true` during this time:

사용자가 구독을 취소했지만 구독이 완전히 만료되기 전까지 "grace period"에 있음을 확인할 수도 있습니다. 예를 들어 사용자가 3월 5일에 구독을 취소했고 원래 3월 10일에 만료되는 구독이었다면, 사용자는 3월 10일까지 "grace period"에 있습니다. 이 기간 동안 `subscribed` 메소드는 여전히 `true`를 반환한다는 점에 유의하십시오:

    if ($user->subscription('default')->onGracePeriod()) {
        // ...
    }

To determine if the user has canceled their subscription and is no longer within their "grace period", you may use the `ended` method:

사용자가 구독을 취소했고 "grace period" 내에 있지 않은지 확인하려면 `ended` 메소드를 사용할 수 있습니다:

    if ($user->subscription('default')->ended()) {
        // ...
    }

<a name="incomplete-and-past-due-status"></a>
#### Incomplete and Past Due Status
#### 미완료 및 연체 상태

If a subscription requires a secondary payment action after creation the subscription will be marked as `incomplete`. Subscription statuses are stored in the `stripe_status` column of Cashier's `subscriptions` database table.

구독 생성 후에 보조 결제 작업이 필요한 경우 구독은 `incomplete`로 표시됩니다. 구독 상태는 Cashier의 `subscriptions` 데이터베이스 테이블의 `stripe_status` 컬럼에 저장됩니다.

Similarly, if a secondary payment action is required when swapping prices the subscription will be marked as `past_due`. When your subscription is in either of these states it will not be active until the customer has confirmed their payment. Determining if a subscription has an incomplete payment may be accomplished using the `hasIncompletePayment` method on the billable model or a subscription instance:

비슷하게, 가격을 교체할 때 보조 결제 작업이 필요한 경우 구독은 `past_due`로 표시됩니다. 구독이 이러한 상태 중 하나에 있으면 고객이 결제를 확인할 때까지 활성화되지 않습니다. 구독에 미완료 결제가 있는지 확인하는 데 `billable` 모델 또는 구독 인스턴스의 `hasIncompletePayment` 메소드를 사용할 수 있습니다:

    if ($user->hasIncompletePayment('default')) {
        // ...
    }

    if ($user->subscription('default')->hasIncompletePayment()) {
        // ...
    }

When a subscription has an incomplete payment, you should direct the user to Cashier's payment confirmation page, passing the `latestPayment` identifier. You may use the `latestPayment` method available on subscription instance to retrieve this identifier:

구독에 미완료 결제가 있는 경우, 사용자를 Cashier의 결제 확인 페이지로 이동시키고 `latestPayment` 식별자를 전달해야 합니다. 구독 인스턴스에서 사용할 수 있는 `latestPayment` 메소드를 사용하여 이 식별자를 검색할 수 있습니다:

```html
<a href="{{ route('cashier.payment', $subscription->latestPayment()->id) }}">
    Please confirm your payment.
</a>
```

If you would like the subscription to still be considered active when it's in a `past_due` or `incomplete` state, you may use the `keepPastDueSubscriptionsActive` and `keepIncompleteSubscriptionsActive` methods provided by Cashier. Typically, these methods should be called in the `register` method of your `App\Providers\AppServiceProvider`:

`past_due` 또는 `incomplete` 상태인 경우 구독을 여전히 활성 상태로 유지하려면 Cashier가 제공하는 `keepPastDueSubscriptionsActive` 및 `keepIncompleteSubscriptionsActive` 메소드를 사용할 수 있습니다. 일반적으로 이러한 메소드는 `App\Providers\AppServiceProvider`의 `register` 메소드에서 호출해야 합니다:

    use Laravel\Cashier\Cashier;

    /**
     * Register any application services.
     */
    public function register(): void
    {
        Cashier::keepPastDueSubscriptionsActive();
        Cashier::keepIncompleteSubscriptionsActive();
    }

> **Warning**  
> When a subscription is in an `incomplete` state it cannot be changed until the payment is confirmed. Therefore, the `swap` and `updateQuantity` methods will throw an exception when the subscription is in an `incomplete` state.
>
> **경고**
> 구독이 `incomplete` 상태인 경우 결제가 확인되기 전까지 변경할 수 없습니다. 따라서 구독이 `incomplete` 상태인 경우 `swap` 및 `updateQuantity` 메소드는 예외를 발생시킵니다.

<a name="subscription-scopes"></a>
#### Subscription Scopes
#### 구독 범위

Most subscription states are also available as query scopes so that you may easily query your database for subscriptions that are in a given state:

대부분의 구독 상태는 쿼리 범위로도 사용할 수 있으므로 특정 상태의 구독을 쉽게 쿼리할 수 있습니다:

    // Get all active subscriptions...
    $subscriptions = Subscription::query()->active()->get();

    // Get all of the canceled subscriptions for a user...
    $subscriptions = $user->subscriptions()->canceled()->get();

A complete list of available scopes is available below:

사용 가능한 범위의 전체 목록은 다음과 같습니다:

    Subscription::query()->active();
    Subscription::query()->canceled();
    Subscription::query()->ended();
    Subscription::query()->incomplete();
    Subscription::query()->notCanceled();
    Subscription::query()->notOnGracePeriod();
    Subscription::query()->notOnTrial();
    Subscription::query()->onGracePeriod();
    Subscription::query()->onTrial();
    Subscription::query()->pastDue();
    Subscription::query()->recurring();

<a name="changing-prices"></a>
### Changing Prices
### 가격 변경

After a customer is subscribed to your application, they may occasionally want to change to a new subscription price. To swap a customer to a new price, pass the Stripe price's identifier to the `swap` method. When swapping prices, it is assumed that the user would like to re-activate their subscription if it was previously canceled. The given price identifier should correspond to a Stripe price identifier available in the Stripe dashboard:

고객이 응용 프로그램에 구독한 후에는 가끔 새 구독 가격으로 변경하려고 할 수 있습니다. 고객을 새 가격으로 교체하려면 Stripe 가격 식별자를 `swap` 메소드에 전달합니다. 가격을 교체할 때 이전에 취소된 경우 사용자가 구독을 다시 활성화하길 원한다고 가정합니다. 주어진 가격 식별자는 Stripe 대시 보드에서 사용 가능한 Stripe 가격 식별자와 일치해야 합니다:

    use App\Models\User;

    $user = App\Models\User::find(1);

    $user->subscription('default')->swap('price_yearly');

If the customer is on trial, the trial period will be maintained. Additionally, if a "quantity" exists for the subscription, that quantity will also be maintained.

고객이 체험판인 경우 체험판 기간이 유지됩니다. 또한 구독에 "수량"이 있는 경우 해당 수량도 유지됩니다.

If you would like to swap prices and cancel any trial period the customer is currently on, you may invoke the `skipTrial` method:

가격을 변경하고 현재 고객이 진행 중인 체험판 기간을 취소하려면 `skipTrial` 메소드를 호출할 수 있습니다:

    $user->subscription('default')
            ->skipTrial()
            ->swap('price_yearly');

If you would like to swap prices and immediately invoice the customer instead of waiting for their next billing cycle, you may use the `swapAndInvoice` method:

가격을 변경하고 다음 결제 주기를 기다리지 않고 즉시 고객에게 송장을 발행하려면 `swapAndInvoice` 메소드를 사용할 수 있습니다:

    $user = User::find(1);

    $user->subscription('default')->swapAndInvoice('price_yearly');

<a name="prorations"></a>
#### Prorations
#### 프로레이션

By default, Stripe prorates charges when swapping between prices. The `noProrate` method may be used to update the subscription's price without prorating the charges:

기본적으로, Stripe는 가격 간에 교체할 때 요금을 프로레이션합니다. `noProrate` 메소드를 사용하여 요금을 프로레이션하지 않고 구독의 가격을 업데이트할 수 있습니다:

    $user->subscription('default')->noProrate()->swap('price_yearly');

For more information on subscription proration, consult the [Stripe documentation](https://stripe.com/docs/billing/subscriptions/prorations).

구독 프로레이션에 대한 자세한 내용은 [Stripe 문서](https://stripe.com/docs/billing/subscriptions/prorations)를 참조하십시오.

> **Warning**  
> Executing the `noProrate` method before the `swapAndInvoice` method will have no effect on proration. An invoice will always be issued.
>
> **경고**
> `noProrate` 메소드를 `swapAndInvoice` 메소드 이전에 실행하면 프로레이션에 영향을 미치지 않습니다. 송장은 항상 발행됩니다.

<a name="subscription-quantity"></a>
### Subscription Quantity
### 구독 수량

Sometimes subscriptions are affected by "quantity". For example, a project management application might charge $10 per month per project. You may use the `incrementQuantity` and `decrementQuantity` methods to easily increment or decrement your subscription quantity:

때때로 구독은 "수량"에 영향을 받습니다. 예를 들어 프로젝트 관리 응용 프로그램은 프로젝트 당 월 $10을 청구할 수 있습니다. `incrementQuantity` 및 `decrementQuantity` 메소드를 사용하여 구독 수량을 쉽게 증가 또는 감소시킬 수 있습니다:

    use App\Models\User;

    $user = User::find(1);

    $user->subscription('default')->incrementQuantity();

    // Add five to the subscription's current quantity...
    $user->subscription('default')->incrementQuantity(5);

    $user->subscription('default')->decrementQuantity();

    // Subtract five from the subscription's current quantity...
    $user->subscription('default')->decrementQuantity(5);

Alternatively, you may set a specific quantity using the `updateQuantity` method:

또는, `updateQuantity` 메소드를 사용하여 특정 수량을 설정할 수 있습니다:

    $user->subscription('default')->updateQuantity(10);

The `noProrate` method may be used to update the subscription's quantity without prorating the charges:

`noProrate` 메소드를 사용하여 요금을 프로레이션하지 않고 구독의 수량을 업데이트할 수 있습니다:

    $user->subscription('default')->noProrate()->updateQuantity(10);

For more information on subscription quantities, consult the [Stripe documentation](https://stripe.com/docs/subscriptions/quantities).

구독 수량에 대한 자세한 내용은 [Stripe 문서](https://stripe.com/docs/subscriptions/quantities)를 참조하십시오.

<a name="quantities-for-subscription-with-multiple-products"></a>
#### Quantities For Subscriptions With Multiple Products
#### 여러 제품 구독의 수량

If your subscription is a [subscription with multiple products](#subscriptions-with-multiple-products), you should pass the ID of the price whose quantity you wish to increment or decrement as the second argument to the increment / decrement methods:

구독이 [여러 제품 구독](#subscriptions-with-multiple-products)인 경우 증가 또는 감소시킬 수량의 가격 ID를 증가 / 감소 메소드의 두 번째 인수로 전달해야 합니다:

    $user->subscription('default')->incrementQuantity(1, 'price_chat');

<a name="subscriptions-with-multiple-products"></a>
### Subscriptions With Multiple Products
### 여러 제품 구독

[Subscription with multiple products](https://stripe.com/docs/billing/subscriptions/multiple-products) allow you to assign multiple billing products to a single subscription. For example, imagine you are building a customer service "helpdesk" application that has a base subscription price of $10 per month but offers a live chat add-on product for an additional $15 per month. Information for subscriptions with multiple products is stored in Cashier's `subscription_items` database table.

[여러 제품 구독](https://stripe.com/docs/billing/subscriptions/multiple-products)을 사용하면 단일 구독에 여러 결제 제품을 할당할 수 있습니다. 예를 들어, $10 월 기본 구독 가격이 있지만 추가 $15 월의 실시간 채팅 애드온 제품을 제공하는 고객 서비스 "헬프데스크" 응용 프로그램을 구축하고 있다고 가정해 보십시오. 여러 제품 구독에 대한 정보는 Cashier의 `subscription_items` 데이터베이스 테이블에 저장됩니다.

You may specify multiple products for a given subscription by passing an array of prices as the second argument to the `newSubscription` method:

`newSubscription` 메소드의 두 번째 인수로 가격 배열을 전달하여 주어진 구독에 여러 제품을 지정할 수 있습니다:

    use Illuminate\Http\Request;

    Route::post('/user/subscribe', function (Request $request) {
        $request->user()->newSubscription('default', [
            'price_monthly',
            'price_chat',
        ])->create($request->paymentMethodId);

        // ...
    });

In the example above, the customer will have two prices attached to their `default` subscription. Both prices will be charged on their respective billing intervals. If necessary, you may use the `quantity` method to indicate a specific quantity for each price:

위의 예에서, 고객은 `default` 구독에 두 가격이 첨부됩니다. 각 가격은 각각의 청구 주기에 청구됩니다. 필요한 경우 `quantity` 메소드를 사용하여 각 가격에 대한 특정 수량을 표시할 수 있습니다:

    $user = User::find(1);

    $user->newSubscription('default', ['price_monthly', 'price_chat'])
        ->quantity(5, 'price_chat')
        ->create($paymentMethod);

If you would like to add another price to an existing subscription, you may invoke the subscription's `addPrice` method:

기존 구독에 또 다른 가격을 추가하려면 구독의 `addPrice` 메소드를 호출할 수 있습니다:

    $user = User::find(1);

    $user->subscription('default')->addPrice('price_chat');

The example above will add the new price and the customer will be billed for it on their next billing cycle. If you would like to bill the customer immediately you may use the `addPriceAndInvoice` method:

위의 예에서는 새 가격을 추가하고 고객이 다음 청구 주기에 청구됩니다. 고객을 즉시 청구하려면 `addPriceAndInvoice` 메소드를 사용할 수 있습니다:

    $user->subscription('default')->addPriceAndInvoice('price_chat');

If you would like to add a price with a specific quantity, you can pass the quantity as the second argument of the `addPrice` or `addPriceAndInvoice` methods:

특정 수량의 가격을 추가하려면 `addPrice` 또는 `addPriceAndInvoice` 메소드의 두 번째 인수로 수량을 전달할 수 있습니다:

    $user = User::find(1);

    $user->subscription('default')->addPrice('price_chat', 5);

You may remove prices from subscriptions using the `removePrice` method:

`removePrice` 메소드를 사용하여 구독에서 가격을 제거할 수 있습니다:

    $user->subscription('default')->removePrice('price_chat');

> **Warning**  
> You may not remove the last price on a subscription. Instead, you should simply cancel the subscription.
>
> **경고**
> 구독의 마지막 가격을 제거할 수 없습니다. 대신 구독을 취소하십시오.

<a name="swapping-prices"></a>
#### Swapping Prices
#### 가격 교환

You may also change the prices attached to a subscription with multiple products. For example, imagine a customer has a `price_basic` subscription with a `price_chat` add-on product and you want to upgrade the customer from the `price_basic` to the `price_pro` price:

또한 여러 제품이 있는 구독에 첨부된 가격을 변경할 수도 있습니다. 예를 들어, 고객이 `price_basic` 구독을 가지고 있고 `price_chat` 애드온 제품을 추가했고 고객을 `price_basic`에서 `price_pro` 가격으로 업그레이드하려고 한다고 가정해 보십시오:

    use App\Models\User;

    $user = User::find(1);

    $user->subscription('default')->swap(['price_pro', 'price_chat']);

When executing the example above, the underlying subscription item with the `price_basic` is deleted and the one with the `price_chat` is preserved. Additionally, a new subscription item for the `price_pro` is created.

위의 예를 실행하면 `price_basic` 가격을 가진 기본 구독 항목이 삭제되고 `price_chat` 가격을 가진 항목이 유지됩니다. 또한 `price_pro` 가격을 가진 새 구독 항목이 생성됩니다.

You can also specify subscription item options by passing an array of key / value pairs to the `swap` method. For example, you may need to specify the subscription price quantities:

`swap` 메소드에 키/값 쌍 배열을 전달하여 구독 항목 옵션을 지정할 수도 있습니다. 예를 들어, 구독 가격 수량을 지정해야 할 수 있습니다:

    $user = User::find(1);

    $user->subscription('default')->swap([
        'price_pro' => ['quantity' => 5],
        'price_chat'
    ]);

If you want to swap a single price on a subscription, you may do so using the `swap` method on the subscription item itself. This approach is particularly useful if you would like to preserve all of the existing metadata on the subscription's other prices:

구독의 단일 가격을 교환하려면 구독 항목 자체의 `swap` 메소드를 사용할 수 있습니다. 이 접근 방식은 구독의 다른 가격에 대한 기존 메타데이터를 모두 유지하려는 경우 특히 유용합니다:

    $user = User::find(1);

    $user->subscription('default')
            ->findItemOrFail('price_basic')
            ->swap('price_pro');

<a name="proration"></a>
#### Proration
#### 조정

By default, Stripe will prorate charges when adding or removing prices from a subscription with multiple products. If you would like to make a price adjustment without proration, you should chain the `noProrate` method onto your price operation:

기본적으로 Stripe는 여러 제품이 있는 구독에서 가격을 추가하거나 제거할 때 요금을 조정합니다. 조정 없이 가격을 조정하려면 가격 작업에 `noProrate` 메소드를 체인으로 연결해야 합니다:

    $user->subscription('default')->noProrate()->removePrice('price_chat');

<a name="swapping-quantities"></a>
#### Quantities
#### 수량

If you would like to update quantities on individual subscription prices, you may do so using the [existing quantity methods](#subscription-quantity) by passing the name of the price as an additional argument to the method:

개별 구독 가격의 수량을 업데이트하려면 [기존 수량 메소드](#subscription-quantity)를 사용하여 가격 이름을 메소드의 추가 인수로 전달하여 수량을 업데이트할 수 있습니다:

    $user = User::find(1);

    $user->subscription('default')->incrementQuantity(5, 'price_chat');

    $user->subscription('default')->decrementQuantity(3, 'price_chat');

    $user->subscription('default')->updateQuantity(10, 'price_chat');

> **Warning**  
> When a subscription has multiple prices the `stripe_price` and `quantity` attributes on the `Subscription` model will be `null`. To access the individual price attributes, you should use the `items` relationship available on the `Subscription` model.
>
> **경고**
> 구독에 여러 가격이 있는 경우 `Subscription` 모델의 `stripe_price` 및 `quantity` 속성은 `null`입니다. 개별 가격 속성에 액세스하려면 `Subscription` 모델에서 사용할 수 있는 `items` 관계를 사용해야 합니다.

<a name="subscription-items"></a>
#### Subscription Items
#### 구독 항목

When a subscription has multiple prices, it will have multiple subscription "items" stored in your database's `subscription_items` table. You may access these via the `items` relationship on the subscription:

구독에 여러 가격이 있는 경우 데이터베이스의 `subscription_items` 테이블에 여러 구독 "항목"이 저장됩니다. 구독에서 이러한 항목에 액세스하려면 `items` 관계를 사용합니다:

    use App\Models\User;

    $user = User::find(1);

    $subscriptionItem = $user->subscription('default')->items->first();

    // Retrieve the Stripe price and quantity for a specific item...
    $stripePrice = $subscriptionItem->stripe_price;
    $quantity = $subscriptionItem->quantity;

You can also retrieve a specific price using the `findItemOrFail` method:

`findItemOrFail` 메소드를 사용하여 특정 가격을 검색할 수도 있습니다:

    $user = User::find(1);

    $subscriptionItem = $user->subscription('default')->findItemOrFail('price_chat');

<a name="multiple-subscriptions"></a>
### Multiple Subscriptions
### 다중 구독

Stripe allows your customers to have multiple subscriptions simultaneously. For example, you may run a gym that offers a swimming subscription and a weight-lifting subscription, and each subscription may have different pricing. Of course, customers should be able to subscribe to either or both plans.

Stripe는 고객이 동시에 여러 구독을 가질 수 있도록 허용합니다. 예를 들어, 수영 구독과 무게 훈련 구독을 제공하는 헬스장을 운영하고 각 구독은 다른 가격을 가질 수 있습니다. 물론, 고객은 둘 중 하나 또는 둘 다의 플랜에 가입할 수 있어야 합니다.

When your application creates subscriptions, you may provide the name of the subscription to the `newSubscription` method. The name may be any string that represents the type of subscription the user is initiating:

어플리케이션이 구독을 생성할 때 `newSubscription` 메소드에 구독의 이름을 제공할 수 있습니다. 이름은 사용자가 시작하는 구독 유형을 나타내는 어떤 문자열이어도 됩니다:

    use Illuminate\Http\Request;

    Route::post('/swimming/subscribe', function (Request $request) {
        $request->user()->newSubscription('swimming')
            ->price('price_swimming_monthly')
            ->create($request->paymentMethodId);

        // ...
    });

In this example, we initiated a monthly swimming subscription for the customer. However, they may want to swap to a yearly subscription at a later time. When adjusting the customer's subscription, we can simply swap the price on the `swimming` subscription:

이 예에서는, 고객에게 월간 수영 구독을 시작했습니다. 그러나 나중에 연간 구독으로 교체하려고 할 수 있습니다. 고객의 구독을 조정할 때 `swimming` 구독의 가격을 간단히 교체할 수 있습니다:

    $user->subscription('swimming')->swap('price_swimming_yearly');

Of course, you may also cancel the subscription entirely:

물론, 구독을 완전히 취소할 수도 있습니다:

    $user->subscription('swimming')->cancel();

<a name="metered-billing"></a>
### Metered Billing
### 계량 요금

[Metered billing](https://stripe.com/docs/billing/subscriptions/metered-billing) allows you to charge customers based on their product usage during a billing cycle. For example, you may charge customers based on the number of text messages or emails they send per month.

[계량 요금](https://stripe.com/docs/billing/subscriptions/metered-billing)을 사용하면 고객이 요금 청구 주기 동안 제품 사용량에 따라 요금을 부과할 수 있습니다. 예를 들어, 월별로 보낸 문자 메시지 또는 이메일의 수에 따라 고객에게 요금을 부과할 수 있습니다.

To start using metered billing, you will first need to create a new product in your Stripe dashboard with a metered price. Then, use the `meteredPrice` to add the metered price ID to a customer subscription:

계량 요금을 사용하려면, 먼저 Stripe 대시보드에서 새 제품을 만들고 계량 가격을 추가해야 합니다. 그런 다음 `meteredPrice`를 사용하여 고객 구독에 계량 가격 ID를 추가합니다:

    use Illuminate\Http\Request;

    Route::post('/user/subscribe', function (Request $request) {
        $request->user()->newSubscription('default')
            ->meteredPrice('price_metered')
            ->create($request->paymentMethodId);

        // ...
    });

You may also start a metered subscription via [Stripe Checkout](#checkout):

[Stripe Checkout](#checkout)를 통해 계량 구독을 시작할 수도 있습니다:

    $checkout = Auth::user()
            ->newSubscription('default', [])
            ->meteredPrice('price_metered')
            ->checkout();

    return view('your-checkout-view', [
        'checkout' => $checkout,
    ]);

<a name="reporting-usage"></a>
#### Reporting Usage
#### 사용량 보고

As your customer uses your application, you will report their usage to Stripe so that they can be billed accurately. To increment the usage of a metered subscription, you may use the `reportUsage` method:

고객이 어플리케이션을 사용할 때, 사용량을 Stripe에 보고하여 정확하게 청구할 수 있도록 합니다. 계량 구독의 사용량을 증가시키려면 `reportUsage` 메소드를 사용할 수 있습니다:

    $user = User::find(1);

    $user->subscription('default')->reportUsage();

By default, a "usage quantity" of 1 is added to the billing period. Alternatively, you may pass a specific amount of "usage" to add to the customer's usage for the billing period:

기본적으로, "사용량 수량"이 1로 설정되어 있습니다. 또는 청구 기간 동안 고객의 사용량에 추가할 특정 "사용량"을 전달할 수 있습니다:

    $user = User::find(1);

    $user->subscription('default')->reportUsage(15);

If your application offers multiple prices on a single subscription, you will need to use the `reportUsageFor` method to specify the metered price you want to report usage for:

어플리케이션이 하나의 구독에 여러 가격을 제공하는 경우, `reportUsageFor` 메소드를 사용하여 사용량을 보고할 계량 가격을 지정해야 합니다:

    $user = User::find(1);

    $user->subscription('default')->reportUsageFor('price_metered', 15);

Sometimes, you may need to update usage which you have previously reported. To accomplish this, you may pass a timestamp or a `DateTimeInterface` instance as the second parameter to `reportUsage`. When doing so, Stripe will update the usage that was reported at that given time. You can continue to update previous usage records as the given date and time is still within the current billing period:

때때로, 이전에 보고한 사용량을 업데이트해야 할 수도 있습니다. 이를 수행하려면 `reportUsage`의 두 번째 매개변수로 타임스탬프 또는 `DateTimeInterface` 인스턴스를 전달할 수 있습니다. 이렇게 하면 Stripe가 해당 시간에 보고된 사용량을 업데이트합니다. 주어진 날짜와 시간이 현재 청구 기간 내에 있으므로 이전 사용 기록을 계속 업데이트할 수 있습니다:

    $user = User::find(1);

    $user->subscription('default')->reportUsage(5, $timestamp);

<a name="retrieving-usage-records"></a>
#### Retrieving Usage Records
#### 사용 기록 검색

To retrieve a customer's past usage, you may use a subscription instance's `usageRecords` method:

고객의 과거 사용 기록을 검색하려면, 구독 인스턴스의 `usageRecords` 메소드를 사용할 수 있습니다:

    $user = User::find(1);

    $usageRecords = $user->subscription('default')->usageRecords();

If your application offers multiple prices on a single subscription, you may use the `usageRecordsFor` method to specify the metered price that you wish to retrieve usage records for:

어플리케이션이 하나의 구독에 여러 가격을 제공하는 경우, `usageRecordsFor` 메소드를 사용하여 사용 기록을 검색할 계량 가격을 지정할 수 있습니다:

    $user = User::find(1);

    $usageRecords = $user->subscription('default')->usageRecordsFor('price_metered');

The `usageRecords` and `usageRecordsFor` methods return a Collection instance containing an associative array of usage records. You may iterate over this array to display a customer's total usage:

`usageRecords` 및 `usageRecordsFor` 메소드는 사용 기록의 연관 배열을 포함하는 Collection 인스턴스를 반환합니다. 이 배열을 반복하여 고객의 총 사용량을 표시할 수 있습니다:

    @foreach ($usageRecords as $usageRecord)
        - Period Starting: {{ $usageRecord['period']['start'] }}
        - Period Ending: {{ $usageRecord['period']['end'] }}
        - Total Usage: {{ $usageRecord['total_usage'] }}
    @endforeach

For a full reference of all usage data returned and how to use Stripe's cursor based pagination, please consult [the official Stripe API documentation](https://stripe.com/docs/api/usage_records/subscription_item_summary_list).

모든 사용 데이터를 반환하고 Stripe의 커서 기반 페이지네이션을 사용하는 방법에 대한 자세한 내용은 [공식 Stripe API 문서](https://stripe.com/docs/api/usage_records/subscription_item_summary_list)를 참조하십시오.

<a name="subscription-taxes"></a>
### Subscription Taxes
### 구독 세금

> **Warning**  
> Instead of calculating Tax Rates manually, you can [automatically calculate taxes using Stripe Tax](#tax-configuration)
>
> **경고**
> 세율을 수동으로 계산하는 대신, [Stripe Tax를 사용하여 자동으로 세금 계산](#tax-configuration)

To specify the tax rates a user pays on a subscription, you should implement the `taxRates` method on your billable model and return an array containing the Stripe tax rate IDs. You can define these tax rates in [your Stripe dashboard](https://dashboard.stripe.com/test/tax-rates):

사용자가 구독에 지불하는 세금 비율을 지정하려면, 청구 모델에 `taxRates` 메소드를 구현하고 Stripe 세금 비율 ID를 포함하는 배열을 반환해야 합니다. 이러한 세금 비율은 [Stripe 대시보드](https://dashboard.stripe.com/test/tax-rates)에서 정의할 수 있습니다:

    /**
     * The tax rates that should apply to the customer's subscriptions.
     *
     * @return array<int, string>
     */
    public function taxRates(): array
    {
        return ['txr_id'];
    }

The `taxRates` method enables you to apply a tax rate on a customer-by-customer basis, which may be helpful for a user base that spans multiple countries and tax rates.

`taxRates` 메소드를 사용하면 고객별로 세금 비율을 적용할 수 있으므로, 여러 국가 및 세금 비율을 포함하는 사용자 기반에 유용할 수 있습니다.

If you're offering subscriptions with multiple products, you may define different tax rates for each price by implementing a `priceTaxRates` method on your billable model:

여러 제품을 가진 구독을 제공하는 경우, 청구 모델에 `priceTaxRates` 메소드를 구현하여 각 가격에 대해 다른 세금 비율을 정의할 수 있습니다:

    /**
     * The tax rates that should apply to the customer's subscriptions.
     *
     * @return array<string, array<int, string>>
     */
    public function priceTaxRates(): array
    {
        return [
            'price_monthly' => ['txr_id'],
        ];
    }

> **Warning**  
> The `taxRates` method only applies to subscription charges. If you use Cashier to make "one-off" charges, you will need to manually specify the tax rate at that time.
>
> **경고**
> `taxRates` 메소드는 구독 요금에만 적용됩니다. Cashier를 사용하여 "일회성" 요금을 지불하는 경우, 해당 시점에서 수동으로 세금 비율을 지정해야 합니다.

<a name="syncing-tax-rates"></a>
#### Syncing Tax Rates
#### 세금 비율 동기화

When changing the hard-coded tax rate IDs returned by the `taxRates` method, the tax settings on any existing subscriptions for the user will remain the same. If you wish to update the tax value for existing subscriptions with the new `taxRates` values, you should call the `syncTaxRates` method on the user's subscription instance:

`taxRates` 메소드에서 반환하는 하드 코딩된 세금 비율 ID를 변경할 때, 사용자의 기존 구독에 대한 세금 설정은 그대로 유지됩니다. 기존 구독의 세금 값을 새로운 `taxRates` 값으로 업데이트하려면, 사용자의 구독 인스턴스에서 `syncTaxRates` 메소드를 호출해야 합니다:

    $user->subscription('default')->syncTaxRates();

This will also sync any item tax rates for a subscription with multiple products. If your application is offering subscriptions with multiple products, you should ensure that your billable model implements the `priceTaxRates` method [discussed above](#subscription-taxes).

이렇게 하면 여러 제품을 가진 구독의 모든 항목 세금 비율도 동기화됩니다. 여러 제품을 가진 구독을 제공하는 경우, 청구 모델이 [위에서 설명한](#subscription-taxes) `priceTaxRates` 메소드를 구현하는지 확인해야 합니다.

<a name="tax-exemption"></a>
#### Tax Exemption
#### 세금 면제

Cashier also offers the `isNotTaxExempt`, `isTaxExempt`, and `reverseChargeApplies` methods to determine if the customer is tax exempt. These methods will call the Stripe API to determine a customer's tax exemption status:

Cashier는 `isNotTaxExempt`, `isTaxExempt`, `reverseChargeApplies` 메소드를 제공하여 고객이 세금 면제 여부를 결정할 수 있습니다. 이러한 메소드는 Stripe API를 호출하여 고객의 세금 면제 상태를 결정합니다:

    use App\Models\User;

    $user = User::find(1);

    $user->isTaxExempt();
    $user->isNotTaxExempt();
    $user->reverseChargeApplies();

> **Warning**  
> These methods are also available on any `Laravel\Cashier\Invoice` object. However, when invoked on an `Invoice` object, the methods will determine the exemption status at the time the invoice was created.
>
> **경고**
> 이 메소드들은 `Laravel\Cashier\Invoice` 객체에서도 사용할 수 있습니다. 그러나 `Invoice` 객체에서 호출하면, 메소드는 인보이스가 생성된 시점의 면제 상태를 결정합니다.

<a name="subscription-anchor-date"></a>
### Subscription Anchor Date
### 구독 앵커 날짜

By default, the billing cycle anchor is the date the subscription was created or, if a trial period is used, the date that the trial ends. If you would like to modify the billing anchor date, you may use the `anchorBillingCycleOn` method:

기본적으로, 청구 주기 앵커는 구독이 생성된 날짜이거나, 시험 기간이 사용되는 경우 시험 기간이 끝나는 날짜입니다. 청구 앵커 날짜를 수정하려면, `anchorBillingCycleOn` 메소드를 사용할 수 있습니다:

    use Illuminate\Http\Request;

    Route::post('/user/subscribe', function (Request $request) {
        $anchor = Carbon::parse('first day of next month');

        $request->user()->newSubscription('default', 'price_monthly')
                    ->anchorBillingCycleOn($anchor->startOfDay())
                    ->create($request->paymentMethodId);

        // ...
    });

For more information on managing subscription billing cycles, consult the [Stripe billing cycle documentation](https://stripe.com/docs/billing/subscriptions/billing-cycle)

구독 청구 주기를 관리하는 더 자세한 정보는 [Stripe 청구 주기 문서](https://stripe.com/docs/billing/subscriptions/billing-cycle)를 참조하세요.

<a name="cancelling-subscriptions"></a>
### Cancelling Subscriptions
### 구독 취소

To cancel a subscription, call the `cancel` method on the user's subscription:

구독을 취소하려면, 사용자의 구독에서 `cancel` 메소드를 호출하세요:

    $user->subscription('default')->cancel();

When a subscription is canceled, Cashier will automatically set the `ends_at` column in your `subscriptions` database table. This column is used to know when the `subscribed` method should begin returning `false`.

구독이 취소되면, Cashier는 자동으로 `subscriptions` 데이터베이스 테이블의 `ends_at` 컬럼을 설정합니다. 이 컬럼은 `subscribed` 메소드가 `false`를 반환하기 시작해야 하는 시점을 알 수 있도록 사용됩니다.

For example, if a customer cancels a subscription on March 1st, but the subscription was not scheduled to end until March 5th, the `subscribed` method will continue to return `true` until March 5th. This is done because a user is typically allowed to continue using an application until the end of their billing cycle.

예를 들어, 고객이 구독을 3월 1일에 취소했지만, 구독이 3월 5일까지 끝나지 않도록 예약되어 있었다고 가정해 보겠습니다. 이 경우, `subscribed` 메소드는 3월 5일까지 `true`를 반환합니다. 이는 사용자가 보통 청구 주기가 끝날 때까지 어플리케이션을 계속 사용할 수 있기 때문입니다.

You may determine if a user has canceled their subscription but are still on their "grace period" using the `onGracePeriod` method:

`onGracePeriod` 메소드를 사용하여 사용자가 구독을 취소했지만 "유예 기간"에 있음을 확인할 수 있습니다.

    if ($user->subscription('default')->onGracePeriod()) {
        // ...
    }

If you wish to cancel a subscription immediately, call the `cancelNow` method on the user's subscription:

구독을 즉시 취소하려면, 사용자의 구독에서 `cancelNow` 메소드를 호출하세요:

    $user->subscription('default')->cancelNow();

If you wish to cancel a subscription immediately and invoice any remaining un-invoiced metered usage or new / pending proration invoice items, call the `cancelNowAndInvoice` method on the user's subscription:

구독을 즉시 취소하고, 미청구 된 사용량 또는 새로운 / 보류중인 조정 인보이스 항목에 대한 인보이스를 발행하려면, 사용자의 구독에서 `cancelNowAndInvoice` 메소드를 호출하세요:

    $user->subscription('default')->cancelNowAndInvoice();

You may also choose to cancel the subscription at a specific moment in time:

특정 시점에서 구독을 취소할 수도 있습니다:

    $user->subscription('default')->cancelAt(
        now()->addDays(10)
    );

<a name="resuming-subscriptions"></a>
### Resuming Subscriptions
### 구독 재개

If a customer has canceled their subscription and you wish to resume it, you may invoke the `resume` method on the subscription. The customer must still be within their "grace period" in order to resume a subscription:

고객이 구독을 취소했고, 구독을 재개하려면, 구독에서 `resume` 메소드를 호출할 수 있습니다. 고객은 구독을 재개하기 위해 "유예 기간"에 있어야 합니다:

    $user->subscription('default')->resume();

If the customer cancels a subscription and then resumes that subscription before the subscription has fully expired the customer will not be billed immediately. Instead, their subscription will be re-activated and they will be billed on the original billing cycle.

고객이 구독을 취소하고, 구독이 완전히 만료되기 전에 구독을 재개하면, 고객은 즉시 청구되지 않습니다. 대신, 구독이 재활성화되고, 원래의 청구 주기에 청구됩니다.

<a name="subscription-trials"></a>
## Subscription Trials
## 구독 시험판

<a name="with-payment-method-up-front"></a>
### With Payment Method Up Front
### 사전 결제 방법

If you would like to offer trial periods to your customers while still collecting payment method information up front, you should use the `trialDays` method when creating your subscriptions:

고객에게 시험판 기간을 제공하면서도 사전에 결제 방법 정보를 수집하려면, 구독을 생성할 때 `trialDays` 메소드를 사용해야 합니다:

    use Illuminate\Http\Request;

    Route::post('/user/subscribe', function (Request $request) {
        $request->user()->newSubscription('default', 'price_monthly')
                    ->trialDays(10)
                    ->create($request->paymentMethodId);

        // ...
    });

This method will set the trial period ending date on the subscription record within the database and instruct Stripe to not begin billing the customer until after this date. When using the `trialDays` method, Cashier will overwrite any default trial period configured for the price in Stripe.

이 메소드는 데이터베이스의 구독 레코드에 시험판 기간 종료 날짜를 설정하고, 이 날짜 이후에 고객을 청구하지 않도록 Stripe에 지시합니다. `trialDays` 메소드를 사용할 때, Cashier는 Stripe에서 가격에 대해 구성된 기본 시험판 기간을 덮어씁니다.

> **Warning**  
> If the customer's subscription is not canceled before the trial ending date they will be charged as soon as the trial expires, so you should be sure to notify your users of their trial ending date.
>
> **경고**
> 평가판 만료일 이전에 구독이 취소되지 않으면, 평가판이 만료되자마자 고객이 청구됩니다. 따라서 사용자에게 평가판 만료일을 알려야 합니다.

The `trialUntil` method allows you to provide a `DateTime` instance that specifies when the trial period should end:

`trialUntil` 메소드를 사용하면, 시험 기간이 끝나는 시점을 지정하는 `DateTime` 인스턴스를 제공할 수 있습니다:

    use Carbon\Carbon;

    $user->newSubscription('default', 'price_monthly')
                ->trialUntil(Carbon::now()->addDays(10))
                ->create($paymentMethod);

You may determine if a user is within their trial period using either the `onTrial` method of the user instance or the `onTrial` method of the subscription instance. The two examples below are equivalent:

사용자가 평가판 기간 내에 있는지 여부를 `onTrial` 메소드 또는 `onTrial` 메소드를 사용하여 확인할 수 있습니다. 아래의 두 예는 동일합니다:

    if ($user->onTrial('default')) {
        // ...
    }

    if ($user->subscription('default')->onTrial()) {
        // ...
    }

You may use the `endTrial` method to immediately end a subscription trial:

`endTrial` 메소드를 사용하여 구독 시험을 즉시 종료할 수 있습니다:

    $user->subscription('default')->endTrial();

To determine if an existing trial has expired, you may use the `hasExpiredTrial` methods:

기존 평가판이 만료되었는지 여부를 확인하려면, `hasExpiredTrial` 메소드를 사용할 수 있습니다:

    if ($user->hasExpiredTrial('default')) {
        // ...
    }

    if ($user->subscription('default')->hasExpiredTrial()) {
        // ...
    }

<a name="defining-trial-days-in-stripe-cashier"></a>
#### Defining Trial Days In Stripe / Cashier
#### Stripe / Cashier에서 시험판 일수 정의

You may choose to define how many trial days your price's receive in the Stripe dashboard or always pass them explicitly using Cashier. If you choose to define your price's trial days in Stripe you should be aware that new subscriptions, including new subscriptions for a customer that had a subscription in the past, will always receive a trial period unless you explicitly call the `skipTrial()` method.

Stripe 대시보드에서 가격의 시험판 일수를 정의하거나 Cashier를 사용하여 명시적으로 전달할 수 있습니다. Stripe에서 가격의 시험판 일수를 정의하려면, `skipTrial()` 메소드를 명시적으로 호출하지 않는 한, 과거에 구독을 가진 고객을 포함한 새 구독을 제외하고, 새 구독은 항상 시험 기간을 받습니다.

<a name="without-payment-method-up-front"></a>
### Without Payment Method Up Front
### 사전 결제 방법 없이

If you would like to offer trial periods without collecting the user's payment method information up front, you may set the `trial_ends_at` column on the user record to your desired trial ending date. This is typically done during user registration:

사용자의 결제 방법 정보를 사전에 수집하지 않고 시험판 기간을 제공하려면, 사용자 레코드의 `trial_ends_at` 컬럼을 원하는 시험 기간 종료 날짜로 설정할 수 있습니다. 이것은 일반적으로 사용자 등록 중에 수행됩니다:

    use App\Models\User;

    $user = User::create([
        // ...
        'trial_ends_at' => now()->addDays(10),
    ]);

> **Warning**  
> Be sure to add a [date cast](/docs/{{version}}/eloquent-mutators#date-casting) for the `trial_ends_at` attribute within your billable model's class definition.
>
> **경고**
> 청구 가능 모델의 클래스 정의 내에서 `trial_ends_at` 속성에 대한 [날짜 캐스트](/docs/{{version}}/eloquent-mutators#date-casting)를 추가하십시오.

Cashier refers to this type of trial as a "generic trial", since it is not attached to any existing subscription. The `onTrial` method on the billable model instance will return `true` if the current date is not past the value of `trial_ends_at`:

Cashier는 이러한 유형의 시험을 "일반 시험"이라고 부르는데, 이것은 어떤 기존 구독에도 연결되어 있지 않기 때문입니다. 청구 가능 모델 인스턴스의 `onTrial` 메소드는 현재 날짜가 `trial_ends_at`의 값을 지났다면 `true`를 반환합니다:

    if ($user->onTrial()) {
        // User is within their trial period...
    }

Once you are ready to create an actual subscription for the user, you may use the `newSubscription` method as usual:

사용자에 대한 실제 구독을 만들 준비가 되면, 일반적으로 `newSubscription` 메소드를 사용할 수 있습니다:

    $user = User::find(1);

    $user->newSubscription('default', 'price_monthly')->create($paymentMethod);

To retrieve the user's trial ending date, you may use the `trialEndsAt` method. This method will return a Carbon date instance if a user is on a trial or `null` if they aren't. You may also pass an optional subscription name parameter if you would like to get the trial ending date for a specific subscription other than the default one:

사용자의 시험 기간 종료 날짜를 검색하려면, `trialEndsAt` 메소드를 사용할 수 있습니다. 이 메소드는 사용자가 시험 중이면 Carbon 날짜 인스턴스를 반환하고, 그렇지 않으면 `null`을 반환합니다. 기본값이 아닌 특정 구독의 시험 기간 종료 날짜를 검색하려면 선택적으로 구독 이름 매개 변수를 전달할 수 있습니다:

    if ($user->onTrial()) {
        $trialEndsAt = $user->trialEndsAt('main');
    }

You may also use the `onGenericTrial` method if you wish to know specifically that the user is within their "generic" trial period and has not yet created an actual subscription:

사용자가 "일반" 시험 기간 내에 있고 아직 실제 구독을 생성하지 않았다는 것을 확인하려면 `onGenericTrial` 메소드를 사용할 수 있습니다:

    if ($user->onGenericTrial()) {
        // User is within their "generic" trial period...
    }

<a name="extending-trials"></a>
### Extending Trials
### 시험판 연장

The `extendTrial` method allows you to extend the trial period of a subscription after the subscription has been created. If the trial has already expired and the customer is already being billed for the subscription, you can still offer them an extended trial. The time spent within the trial period will be deducted from the customer's next invoice:

`extendTrial` 메소드를 사용하면 구독이 생성된 후 구독의 시험 기간을 연장할 수 있습니다. 시험 기간이 이미 만료되고 고객이 이미 구독에 대해 청구되고 있다면, 여전히 확장된 시험 기간을 제공할 수 있습니다. 시험 기간 내에서 사용된 시간은 고객의 다음 송장에서 차감됩니다:

    use App\Models\User;

    $subscription = User::find(1)->subscription('default');

    // End the trial 7 days from now...
    $subscription->extendTrial(
        now()->addDays(7)
    );

    // Add an additional 5 days to the trial...
    $subscription->extendTrial(
        $subscription->trial_ends_at->addDays(5)
    );

<a name="handling-stripe-webhooks"></a>
## Handling Stripe Webhooks
## Stripe 웹훅 처리

> **Note**  
> You may use [the Stripe CLI](https://stripe.com/docs/stripe-cli) to help test webhooks during local development.
>
> **참고**
> 로컬 개발 중 웹훅을 테스트하는 데 [Stripe CLI](https://stripe.com/docs/stripe-cli)를 사용할 수 있습니다.

Stripe can notify your application of a variety of events via webhooks. By default, a route that points to Cashier's webhook controller is automatically registered by the Cashier service provider. This controller will handle all incoming webhook requests.

Stripe는 웹훅을 통해 여러 이벤트에 대한 알림을 어플리케이션에 보낼 수 있습니다. 기본적으로 Cashier 서비스 제공자에 의해 Cashier 웹훅 컨트롤러를 가리키는 라우트가 자동으로 등록됩니다. 이 컨트롤러는 모든 수신 웹훅 요청을 처리합니다.

By default, the Cashier webhook controller will automatically handle cancelling subscriptions that have too many failed charges (as defined by your Stripe settings), customer updates, customer deletions, subscription updates, and payment method changes; however, as we'll soon discover, you can extend this controller to handle any Stripe webhook event you like.

기본적으로, Cashier 웹훅 컨트롤러는 너무 많은 실패한 청구(Stripe 설정에 의해 정의된 대로)를 취소하는 구독, 고객 업데이트, 고객 삭제, 구독 업데이트 및 결제 방법 변경을 자동으로 처리합니다. 그러나 곧 알게 될 것처럼, 이 컨트롤러를 확장하여 원하는 Stripe 웹훅 이벤트를 처리할 수 있습니다.

To ensure your application can handle Stripe webhooks, be sure to configure the webhook URL in the Stripe control panel. By default, Cashier's webhook controller responds to the `/stripe/webhook` URL path. The full list of all webhooks you should enable in the Stripe control panel are:

어플리케이션이 Stripe 웹훅을 처리할 수 있도록 하려면, Stripe 제어판에서 웹훅 URL을 구성해야 합니다. 기본적으로 Cashier의 웹훅 컨트롤러는 `/stripe/webhook` URL 경로에 응답합니다. Stripe 제어판에서 활성화해야 하는 모든 웹훅의 전체 목록은 다음과 같습니다:

- `customer.subscription.created`
- `customer.subscription.updated`
- `customer.subscription.deleted`
- `customer.updated`
- `customer.deleted`
- `invoice.payment_succeeded`
- `invoice.payment_action_required`

For convenience, Cashier includes a `cashier:webhook` Artisan command. This command will create a webhook in Stripe that listens to all of the events required by Cashier:

편의를 위해, Cashier에는 `cashier:webhook` 아티즌 명령이 포함되어 있습니다. 이 명령은 Cashier에 의해 필요한 모든 이벤트를 수신하는 Stripe에 웹훅을 생성합니다:

```shell
php artisan cashier:webhook
```

By default, the created webhook will point to the URL defined by the `APP_URL` environment variable and the `cashier.webhook` route that is included with Cashier. You may provide the `--url` option when invoking the command if you would like to use a different URL:

기본적으로, 생성된 웹훅은 `APP_URL` 환경 변수와 Cashier에 포함된 `cashier.webhook` 라우트에 의해 정의된 URL을 가리킵니다. 다른 URL을 사용하려면 명령을 호출할 때 `--url` 옵션을 제공할 수 있습니다:

```shell
php artisan cashier:webhook --url "https://example.com/stripe/webhook"
```

The webhook that is created will use the Stripe API version that your version of Cashier is compatible with. If you would like to use a different Stripe version, you may provide the `--api-version` option:

생성된 웹훅은 Cashier 버전과 호환되는 Stripe API 버전을 사용합니다. 다른 Stripe 버전을 사용하려면 `--api-version` 옵션을 제공할 수 있습니다:

```shell
php artisan cashier:webhook --api-version="2019-12-03"
```

After creation, the webhook will be immediately active. If you wish to create the webhook but have it disabled until you're ready, you may provide the `--disabled` option when invoking the command:

생성 후, 웹훅은 즉시 활성화됩니다. 웹훅을 생성하되 준비가 되지 않은 경우 웹훅을 비활성화하려면 명령을 호출할 때 `--disabled` 옵션을 제공할 수 있습니다:

```shell
php artisan cashier:webhook --disabled
```

> **Warning**  
> Make sure you protect incoming Stripe webhook requests with Cashier's included [webhook signature verification](#verifying-webhook-signatures) middleware.
>
> **경고**
> Cashier에 포함된 [웹훅 서명 검증](#verifying-webhook-signatures) 미들웨어로 Stripe 웹훅 요청을 보호하는지 확인하십시오.   

<a name="webhooks-csrf-protection"></a>
#### Webhooks & CSRF Protection
#### 웹훅 및 CSRF 보호

Since Stripe webhooks need to bypass Laravel's [CSRF protection](/docs/{{version}}/csrf), be sure to list the URI as an exception in your application's `App\Http\Middleware\VerifyCsrfToken` middleware or list the route outside of the `web` middleware group:

Stripe 웹훅은 Laravel의 [CSRF 보호](/docs/{{version}}/csrf)를 우회해야 하므로, 어플리케이션의 `App\Http\Middleware\VerifyCsrfToken` 미들웨어에서 URI를 예외로 지정하거나 라우트를 `web` 미들웨어 그룹 밖에 지정하십시오:

    protected $except = [
        'stripe/*',
    ];

<a name="defining-webhook-event-handlers"></a>
### Defining Webhook Event Handlers
### 웹훅 이벤트 핸들러 정의

Cashier automatically handles subscription cancellations for failed charges and other common Stripe webhook events. However, if you have additional webhook events you would like to handle, you may do so by listening to the following events that are dispatched by Cashier:

Cashier는 실패한 청구 및 기타 일반적인 Stripe 웹훅 이벤트에 대한 구독 취소를 자동으로 처리합니다. 그러나 추가 웹훅 이벤트를 처리하려면 Cashier에 의해 전달되는 다음 이벤트를 수신하여 처리할 수 있습니다:

- `Laravel\Cashier\Events\WebhookReceived`
- `Laravel\Cashier\Events\WebhookHandled`

Both events contain the full payload of the Stripe webhook. For example, if you wish to handle the `invoice.payment_succeeded` webhook, you may register a [listener](/docs/{{version}}/events#defining-listeners) that will handle the event:

두 이벤트 모두 Stripe 웹훅의 전체 페이로드를 포함합니다. 예를 들어, `invoice.payment_succeeded` 웹훅을 처리하려면 이벤트를 처리하는 [리스너](/docs/{{version}}/events#defining-listeners)를 등록할 수 있습니다:

    <?php

    namespace App\Listeners;

    use Laravel\Cashier\Events\WebhookReceived;

    class StripeEventListener
    {
        /**
         * Handle received Stripe webhooks.
         */
        public function handle(WebhookReceived $event): void
        {
            if ($event->payload['type'] === 'invoice.payment_succeeded') {
                // Handle the incoming event...
            }
        }
    }

Once your listener has been defined, you may register it within your application's `EventServiceProvider`:

리스너가 정의되면 어플리케이션의 `EventServiceProvider`에서 등록할 수 있습니다:

    <?php

    namespace App\Providers;

    use App\Listeners\StripeEventListener;
    use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;
    use Laravel\Cashier\Events\WebhookReceived;

    class EventServiceProvider extends ServiceProvider
    {
        protected $listen = [
            WebhookReceived::class => [
                StripeEventListener::class,
            ],
        ];
    }

<a name="verifying-webhook-signatures"></a>
### Verifying Webhook Signatures
### 웹훅 서명 확인

To secure your webhooks, you may use [Stripe's webhook signatures](https://stripe.com/docs/webhooks/signatures). For convenience, Cashier automatically includes a middleware which validates that the incoming Stripe webhook request is valid.

웹훅을 보호하려면, [Stripe의 웹훅 서명](https://stripe.com/docs/webhooks/signatures)을 사용할 수 있습니다. 편의를 위해 Cashier는 Stripe 웹훅 요청이 유효한지 확인하는 미들웨어를 자동으로 포함합니다.

To enable webhook verification, ensure that the `STRIPE_WEBHOOK_SECRET` environment variable is set in your application's `.env` file. The webhook `secret` may be retrieved from your Stripe account dashboard.

웹훅 확인을 활성화하려면, 어플리케이션의 `.env` 파일에서 `STRIPE_WEBHOOK_SECRET` 환경 변수가 설정되어 있는지 확인하십시오. 웹훅 `secret`은 Stripe 계정 대시보드에서 검색할 수 있습니다.

<a name="single-charges"></a>
## Single Charges
## 단일 요금

<a name="simple-charge"></a>
### Simple Charge
### 간편 충전

If you would like to make a one-time charge against a customer, you may use the `charge` method on a billable model instance. You will need to [provide a payment method identifier](#payment-methods-for-single-charges) as the second argument to the `charge` method:

고객에 대해 일회성 요금을 부과하려면, 청구 가능 모델 인스턴스의 `charge` 메소드를 사용할 수 있습니다. `charge` 메소드의 두 번째 인수로 [결제 방법 식별자 제공](#payment-methods-for-single-charges)해야 합니다:

    use Illuminate\Http\Request;

    Route::post('/purchase', function (Request $request) {
        $stripeCharge = $request->user()->charge(
            100, $request->paymentMethodId
        );

        // ...
    });

The `charge` method accepts an array as its third argument, allowing you to pass any options you wish to the underlying Stripe charge creation. More information regarding the options available to you when creating charges may be found in the [Stripe documentation](https://stripe.com/docs/api/charges/create):

`charge` 메소드는 세 번째 인수로 배열을 받으며, 기본 Stripe 청구서 생성에 전달할 옵션을 전달할 수 있습니다. 청구서를 생성할 때 사용할 수 있는 옵션에 대한 자세한 내용은 [Stripe 문서](https://stripe.com/docs/api/charges/create)에서 확인할 수 있습니다:

    $user->charge(100, $paymentMethod, [
        'custom_option' => $value,
    ]);

You may also use the `charge` method without an underlying customer or user. To accomplish this, invoke the `charge` method on a new instance of your application's billable model:

또한 기본 고객 또는 사용자 없이 `charge` 메소드를 사용할 수 있습니다. 이를 위해 어플리케이션의 청구 가능 모델의 새 인스턴스에서 `charge` 메소드를 호출하십시오:

    use App\Models\User;

    $stripeCharge = (new User)->charge(100, $paymentMethod);

The `charge` method will throw an exception if the charge fails. If the charge is successful, an instance of `Laravel\Cashier\Payment` will be returned from the method:

`charge` 메소드는 청구가 실패하면 예외를 발생시킵니다. 청구가 성공하면 `Laravel\Cashier\Payment`의 인스턴스가 메소드에서 반환됩니다:

    try {
        $payment = $user->charge(100, $paymentMethod);
    } catch (Exception $e) {
        // ...
    }

> **Warning**  
> The `charge` method accepts the payment amount in the lowest denominator of the currency used by your application. For example, if customers are paying in United States Dollars, amounts should be specified in pennies.
>
> **경고**
> `charge` 메소드는 어플리케이션에서 사용하는 통화의 가장 낮은 단위로 지불 금액을 받습니다. 예를 들어, 고객이 미국 달러로 지불하는 경우, 금액은 센트 단위로 지정해야 합니다.

<a name="charge-with-invoice"></a>
### Charge With Invoice
### 청구서와 함께 충전

Sometimes you may need to make a one-time charge and offer a PDF receipt to your customer. The `invoicePrice` method lets you do just that. For example, let's invoice a customer for five new shirts:

때때로 고객에게 PDF 영수증을 제공하면서 일회성 요금을 부과해야 할 수 있습니다. `invoicePrice` 메소드를 사용하면 이를 수행할 수 있습니다. 예를 들어, 고객에게 새로운 5개의 셔츠를 청구해 보겠습니다:

    $user->invoicePrice('price_tshirt', 5);

The invoice will be immediately charged against the user's default payment method. The `invoicePrice` method also accepts an array as its third argument. This array contains the billing options for the invoice item. The fourth argument accepted by the method is also an array which should contain the billing options for the invoice itself:

즉시 사용자의 기본 결제 방법에 청구서가 부과됩니다. `invoicePrice` 메소드는 또한 세 번째 인수로 배열을 받습니다. 이 배열에는 청구서 항목의 청구 옵션이 포함됩니다. 메소드가 받는 네 번째 인수도 배열이며, 청구서 자체의 청구 옵션을 포함해야 합니다:

    $user->invoicePrice('price_tshirt', 5, [
        'discounts' => [
            ['coupon' => 'SUMMER21SALE']
        ],
    ], [
        'default_tax_rates' => ['txr_id'],
    ]);

Similarly to `invoicePrice`, you may use the `tabPrice` method to create a one-time charge for multiple items (up to 250 items per invoice) by adding them to the customer's "tab" and then invoicing the customer. For example, we may invoice a customer for five shirts and two mugs:

`invoicePrice`와 마찬가지로, `tabPrice` 메소드를 사용하여 여러 항목(청구서당 250개 항목까지)에 대한 일회성 요금을 고객의 "탭"에 추가한 다음 고객에게 청구서를 작성할 수 있습니다. 예를 들어, 고객에게 5개의 셔츠와 2개의 컵을 청구할 수 있습니다:

    $user->tabPrice('price_tshirt', 5);
    $user->tabPrice('price_mug', 2);
    $user->invoice();

Alternatively, you may use the `invoiceFor` method to make a "one-off" charge against the customer's default payment method:

또는, `invoiceFor` 메소드를 사용하여 고객의 기본 결제 방법에 대해 "일회성" 요금을 부과할 수 있습니다:

    $user->invoiceFor('One Time Fee', 500);

Although the `invoiceFor` method is available for you to use, it is recommended that you use the `invoicePrice` and `tabPrice` methods with pre-defined prices. By doing so, you will have access to better analytics and data within your Stripe dashboard regarding your sales on a per-product basis.

비록 `invoiceFor` 메소드를 사용할 수 있지만, 미리 정의된 가격을 사용하여 `invoicePrice` 및 `tabPrice` 메소드를 사용하는 것이 좋습니다. 이렇게 하면, Stripe 대시보드에서 상품별 판매에 대한 더 나은 분석 및 데이터에 액세스할 수 있습니다.

> **Warning**  
> The `invoice`, `invoicePrice`, and `invoiceFor` methods will create a Stripe invoice which will retry failed billing attempts. If you do not want invoices to retry failed charges, you will need to close them using the Stripe API after the first failed charge.
>
> **경고**
> `invoice`, `invoicePrice`, `invoiceFor` 메소드는 실패한 청구 시도를 다시 시도하는 Stripe 청구서를 생성합니다. 청구서가 실패한 청구를 다시 시도하지 않도록 하려면, 첫 번째 실패한 청구 후 Stripe API를 사용하여 청구서를 닫아야 합니다.

<a name="creating-payment-intents"></a>
### Creating Payment Intents
### 결제 인텐트 생성

You can create a new Stripe payment intent by invoking the `pay` method on a billable model instance. Calling this method will create a payment intent that is wrapped in a `Laravel\Cashier\Payment` instance:

billable 모델 인스턴스에서 `pay` 메소드를 호출하여 새로운 Stripe 결제 인텐트를 생성할 수 있습니다. 이 메소드를 호출하면 `Laravel\Cashier\Payment` 인스턴스로 감싸진 결제 인텐트가 생성됩니다:

    use Illuminate\Http\Request;

    Route::post('/pay', function (Request $request) {
        $payment = $request->user()->pay(
            $request->get('amount')
        );

        return $payment->client_secret;
    });

After creating the payment intent, you can return the client secret to your application's frontend so that the user can complete the payment in their browser. To read more about building entire payment flows using Stripe payment intents, please consult the [Stripe documentation](https://stripe.com/docs/payments/accept-a-payment?platform=web).

결제 인텐트를 생성한 후, 클라이언트 시크릿을 어플리케이션의 프론트엔드로 반환하여 사용자가 브라우저에서 결제를 완료할 수 있습니다. Stripe 결제 인텐트를 사용하여 전체 결제 흐름을 구축하는 방법에 대해 자세히 알아보려면 [Stripe 문서](https://stripe.com/docs/payments/accept-a-payment?platform=web)를 참조하십시오.

When using the `pay` method, the default payment methods that are enabled within your Stripe dashboard will be available to the customer. Alternatively, if you only want to allow for some specific payment methods to be used, you may use the `payWith` method:

`pay` 메소드를 사용할 때, Stripe 대시보드에서 활성화된 기본 결제 방법이 고객에게 사용 가능합니다. 또는, 특정 결제 방법만 사용하도록 허용하려면 `payWith` 메소드를 사용할 수 있습니다:

    use Illuminate\Http\Request;

    Route::post('/pay', function (Request $request) {
        $payment = $request->user()->payWith(
            $request->get('amount'), ['card', 'bancontact']
        );

        return $payment->client_secret;
    });

> **Warning**  
> The `pay` and `payWith` methods accept the payment amount in the lowest denominator of the currency used by your application. For example, if customers are paying in United States Dollars, amounts should be specified in pennies.
>
> **경고**
> `pay` 및 `payWith` 메소드는 어플리케이션에서 사용하는 통화의 가장 낮은 단위로 결제 금액을 받습니다. 예를 들어, 고객이 미국 달러로 결제하는 경우, 금액은 센트 단위로 지정해야 합니다.

<a name="refunding-charges"></a>
### Refunding Charges
### 환불

If you need to refund a Stripe charge, you may use the `refund` method. This method accepts the Stripe [payment intent ID](#payment-methods-for-single-charges) as its first argument:

Stripe 청구서를 환불해야 하는 경우, `refund` 메소드를 사용할 수 있습니다. 이 메소드는 첫 번째 인수로 Stripe [결제 인텐트 ID](#payment-methods-for-single-charges)를 받습니다:

    $payment = $user->charge(100, $paymentMethodId);

    $user->refund($payment->id);

<a name="invoices"></a>
## Invoices
## 청구서

<a name="retrieving-invoices"></a>
### Retrieving Invoices
### 청구서 검색

You may easily retrieve an array of a billable model's invoices using the `invoices` method. The `invoices` method returns a collection of `Laravel\Cashier\Invoice` instances:

billable 모델의 청구서 배열을 `invoices` 메소드를 사용하여 쉽게 검색할 수 있습니다. `invoices` 메소드는 `Laravel\Cashier\Invoice` 인스턴스의 컬렉션을 반환합니다:

    $invoices = $user->invoices();

If you would like to include pending invoices in the results, you may use the `invoicesIncludingPending` method:

결과에 대기 중인 청구서를 포함하려면 `invoicesIncludingPending` 메소드를 사용할 수 있습니다:

    $invoices = $user->invoicesIncludingPending();

You may use the `findInvoice` method to retrieve a specific invoice by its ID:

`findInvoice` 메소드를 사용하여 ID로 특정 청구서를 검색할 수 있습니다:

    $invoice = $user->findInvoice($invoiceId);

<a name="displaying-invoice-information"></a>
#### Displaying Invoice Information
#### 청구서 정보 표시

When listing the invoices for the customer, you may use the invoice's methods to display the relevant invoice information. For example, you may wish to list every invoice in a table, allowing the user to easily download any of them:

고객의 청구서를 나열할 때, 청구서의 메소드를 사용하여 관련 청구서 정보를 표시할 수 있습니다. 예를 들어, 테이블에 모든 청구서를 나열하여 사용자가 쉽게 다운로드할 수 있도록 할 수 있습니다:

    <table>
        @foreach ($invoices as $invoice)
            <tr>
                <td>{{ $invoice->date()->toFormattedDateString() }}</td>
                <td>{{ $invoice->total() }}</td>
                <td><a href="/user/invoice/{{ $invoice->id }}">Download</a></td>
            </tr>
        @endforeach
    </table>

<a name="upcoming-invoices"></a>
### Upcoming Invoices
### 예정된 청구서

To retrieve the upcoming invoice for a customer, you may use the `upcomingInvoice` method:

고객의 예정된 청구서를 검색하려면 `upcomingInvoice` 메소드를 사용할 수 있습니다:

    $invoice = $user->upcomingInvoice();

Similarly, if the customer has multiple subscriptions, you can also retrieve the upcoming invoice for a specific subscription:

유사하게, 고객이 여러 구독을 가지고 있다면 특정 구독의 예정된 청구서를 검색할 수도 있습니다:

    $invoice = $user->subscription('default')->upcomingInvoice();

<a name="previewing-subscription-invoices"></a>
### Previewing Subscription Invoices
### 구독 청구서 미리보기

Using the `previewInvoice` method, you can preview an invoice before making price changes. This will allow you to determine what your customer's invoice will look like when a given price change is made:

`previewInvoice` 메소드를 사용하여 가격 변경 전에 청구서를 미리 볼 수 있습니다. 이를 통해 특정 가격 변경이 이루어졌을 때 고객의 청구서가 어떻게 보일지 확인할 수 있습니다:

    $invoice = $user->subscription('default')->previewInvoice('price_yearly');

You may pass an array of prices to the `previewInvoice` method in order to preview invoices with multiple new prices:

`previewInvoice` 메소드에 여러 가격을 포함하는 배열을 전달하여 여러 가격을 가진 청구서를 미리 볼 수 있습니다:

    $invoice = $user->subscription('default')->previewInvoice(['price_yearly', 'price_metered']);

<a name="generating-invoice-pdfs"></a>
### Generating Invoice PDFs
### 청구서 PDF 생성

Before generating invoice PDFs, you should use Composer to install the Dompdf library, which is the default invoice renderer for Cashier:

청구서 PDF를 생성하기 전에, Dompdf 라이브러리를 설치해야 합니다. 이 라이브러리는 Cashier의 기본 청구서 렌더러입니다:

```php
composer require dompdf/dompdf
```

From within a route or controller, you may use the `downloadInvoice` method to generate a PDF download of a given invoice. This method will automatically generate the proper HTTP response needed to download the invoice:

라우트 또는 컨트롤러 내에서, `downloadInvoice` 메소드를 사용하여 특정 청구서의 PDF를 다운로드할 수 있습니다. 이 메소드는 청구서를 다운로드하는 데 필요한 적절한 HTTP 응답을 자동으로 생성합니다:

    use Illuminate\Http\Request;

    Route::get('/user/invoice/{invoice}', function (Request $request, string $invoiceId) {
        return $request->user()->downloadInvoice($invoiceId);
    });

By default, all data on the invoice is derived from the customer and invoice data stored in Stripe. The filename is based on your `app.name` config value. However, you can customize some of this data by providing an array as the second argument to the `downloadInvoice` method. This array allows you to customize information such as your company and product details:

기본적으로, 청구서의 모든 데이터는 Stripe에 저장된 고객 및 청구서 데이터에서 파생됩니다. 파일 이름은 `app.name` 구성 값에 기반합니다. 그러나 `downloadInvoice` 메소드의 두 번째 인수로 배열을 제공하여 일부 데이터를 사용자 정의할 수 있습니다. 이 배열을 사용하여 회사 및 제품 세부 정보와 같은 정보를 사용자 정의할 수 있습니다:

    return $request->user()->downloadInvoice($invoiceId, [
        'vendor' => 'Your Company',
        'product' => 'Your Product',
        'street' => 'Main Str. 1',
        'location' => '2000 Antwerp, Belgium',
        'phone' => '+32 499 00 00 00',
        'email' => 'info@example.com',
        'url' => 'https://example.com',
        'vendorVat' => 'BE123456789',
    ]);

The `downloadInvoice` method also allows for a custom filename via its third argument. This filename will automatically be suffixed with `.pdf`:

`downloadInvoice` 메소드는 세 번째 인수를 통해 사용자 정의 파일 이름을 허용합니다. 이 파일 이름은 자동으로 `.pdf`로 끝납니다:

    return $request->user()->downloadInvoice($invoiceId, [], 'my-invoice');

<a name="custom-invoice-render"></a>
#### Custom Invoice Renderer
#### 커스텀 청구서 렌더러

Cashier also makes it possible to use a custom invoice renderer. By default, Cashier uses the `DompdfInvoiceRenderer` implementation, which utilizes the [dompdf](https://github.com/dompdf/dompdf) PHP library to generate Cashier's invoices. However, you may use any renderer you wish by implementing the `Laravel\Cashier\Contracts\InvoiceRenderer` interface. For example, you may wish to render an invoice PDF using an API call to a third-party PDF rendering service:

Cashier는 커스텀 청구서 렌더러를 사용할 수 있도록 합니다. 기본적으로 Cashier는 `DompdfInvoiceRenderer` 구현을 사용합니다. 이 구현은 [dompdf](https://github.com/dompdf/dompdf) PHP 라이브러리를 사용하여 Cashier의 청구서를 생성합니다. 그러나 `Laravel\Cashier\Contracts\InvoiceRenderer` 인터페이스를 구현하여 원하는 렌더러를 사용할 수 있습니다. 예를 들어, 서드 파티 PDF 렌더링 서비스의 API 호출을 사용하여 청구서 PDF를 렌더링할 수 있습니다:

    use Illuminate\Support\Facades\Http;
    use Laravel\Cashier\Contracts\InvoiceRenderer;
    use Laravel\Cashier\Invoice;

    class ApiInvoiceRenderer implements InvoiceRenderer
    {
        /**
         * Render the given invoice and return the raw PDF bytes.
         */
        public function render(Invoice $invoice, array $data = [], array $options = []): string
        {
            $html = $invoice->view($data)->render();

            return Http::get('https://example.com/html-to-pdf', ['html' => $html])->get()->body();
        }
    }

Once you have implemented the invoice renderer contract, you should update the `cashier.invoices.renderer` configuration value in your application's `config/cashier.php` configuration file. This configuration value should be set to the class name of your custom renderer implementation.

청구서 렌더러 계약을 구현한 후, 응용 프로그램의 `config/cashier.php` 구성 파일의 `cashier.invoices.renderer` 구성 값을 업데이트해야 합니다. 이 구성 값은 커스텀 렌더러 구현의 클래스 이름으로 설정해야 합니다.

<a name="checkout"></a>
## Checkout
## 체크아웃

Cashier Stripe also provides support for [Stripe Checkout](https://stripe.com/payments/checkout). Stripe Checkout takes the pain out of implementing custom pages to accept payments by providing a pre-built, hosted payment page.

Cashier Stripe는 [Stripe Checkout](https://stripe.com/payments/checkout)에 대한 지원도 제공합니다. Stripe Checkout은 사전 구축된 호스팅된 결제 페이지를 제공하여 커스텀 페이지를 구현하는 데 드는 불편함을 줄여줍니다.

The following documentation contains information on how to get started using Stripe Checkout with Cashier. To learn more about Stripe Checkout, you should also consider reviewing [Stripe's own documentation on Checkout](https://stripe.com/docs/payments/checkout).

다음 문서는 Cashier와 함께 Stripe Checkout을 사용하는 방법에 대한 정보를 포함하고 있습니다. Stripe Checkout에 대해 더 자세히 알아보려면 [Stripe의 Checkout에 대한 자체 문서](https://stripe.com/docs/payments/checkout)를 검토하는 것이 좋습니다.

<a name="product-checkouts"></a>
### Product Checkouts
### 제품 체크아웃

You may perform a checkout for an existing product that has been created within your Stripe dashboard using the `checkout` method on a billable model. The `checkout` method will initiate a new Stripe Checkout session. By default, you're required to pass a Stripe Price ID:

billable 모델의 `checkout` 메소드를 사용하여 Stripe 대시보드에서 생성된 기존 제품에 대한 체크아웃을 수행할 수 있습니다. `checkout` 메소드는 새로운 Stripe Checkout 세션을 시작합니다. 기본적으로 Stripe Price ID를 전달해야 합니다:

    use Illuminate\Http\Request;

    Route::get('/product-checkout', function (Request $request) {
        return $request->user()->checkout('price_tshirt');
    });

If needed, you may also specify a product quantity:

필요한 경우, 제품 수량을 지정할 수도 있습니다:

    use Illuminate\Http\Request;

    Route::get('/product-checkout', function (Request $request) {
        return $request->user()->checkout(['price_tshirt' => 15]);
    });

When a customer visits this route they will be redirected to Stripe's Checkout page. By default, when a user successfully completes or cancels a purchase they will be redirected to your `home` route location, but you may specify custom callback URLs using the `success_url` and `cancel_url` options:

고객이 이 라우트를 방문하면 Stripe의 Checkout 페이지로 리디렉션됩니다. 기본적으로 사용자가 구매를 성공적으로 완료하거나 취소하면 `home` 라우트 위치로 리디렉션되지만, `success_url` 및 `cancel_url` 옵션을 사용하여 커스텀 콜백 URL을 지정할 수 있습니다:

    use Illuminate\Http\Request;

    Route::get('/product-checkout', function (Request $request) {
        return $request->user()->checkout(['price_tshirt' => 1], [
            'success_url' => route('your-success-route'),
            'cancel_url' => route('your-cancel-route'),
        ]);
    });

When defining your `success_url` checkout option, you may instruct Stripe to add the checkout session ID as a query string parameter when invoking your URL. To do so, add the literal string `{CHECKOUT_SESSION_ID}` to your `success_url` query string. Stripe will replace this placeholder with the actual checkout session ID:

`success_url` 체크아웃 옵션을 정의할 때, URL을 호출할 때 체크아웃 세션 ID를 쿼리 문자열 매개 변수로 추가하도록 Stripe에 지시할 수 있습니다. 이를 수행하려면 `success_url` 쿼리 문자열에 리터럴 문자열 `{CHECKOUT_SESSION_ID}`을 추가합니다. Stripe는 이 플레이스 홀더를 실제 체크아웃 세션 ID로 대체합니다:

    use Illuminate\Http\Request;
    use Stripe\Checkout\Session;
    use Stripe\Customer;

    Route::get('/product-checkout', function (Request $request) {
        return $request->user()->checkout(['price_tshirt' => 1], [
            'success_url' => route('checkout-success').'?session_id={CHECKOUT_SESSION_ID}',
            'cancel_url' => route('checkout-cancel'),
        ]);
    });

    Route::get('/checkout-success', function (Request $request) {
        $checkoutSession = $request->user()->stripe()->checkout->sessions->retrieve($request->get('session_id'));

        return view('checkout.success', ['checkoutSession' => $checkoutSession]);
    })->name('checkout-success');

<a name="checkout-promotion-codes"></a>
#### Promotion Codes
#### 프로모션 코드

By default, Stripe Checkout does not allow [user redeemable promotion codes](https://stripe.com/docs/billing/subscriptions/discounts/codes). Luckily, there's an easy way to enable these for your Checkout page. To do so, you may invoke the `allowPromotionCodes` method:

기본적으로, Stripe Checkout은 [사용자가 사용 가능한 프로모션 코드](https://stripe.com/docs/billing/subscriptions/discounts/codes)를 허용하지 않습니다. 다행히도, Checkout 페이지에 이를 활성화하는 간단한 방법이 있습니다. 이를 수행하려면 `allowPromotionCodes` 메소드를 호출할 수 있습니다:

    use Illuminate\Http\Request;

    Route::get('/product-checkout', function (Request $request) {
        return $request->user()
            ->allowPromotionCodes()
            ->checkout('price_tshirt');
    });

<a name="single-charge-checkouts"></a>
### Single Charge Checkouts
### 단일 요금 체크아웃

You can also perform a simple charge for an ad-hoc product that has not been created in your Stripe dashboard. To do so you may use the `checkoutCharge` method on a billable model and pass it a chargeable amount, a product name, and an optional quantity. When a customer visits this route they will be redirected to Stripe's Checkout page:

또한 Stripe 대시보드에서 생성되지 않은 임시 제품에 대한 단순한 요금을 지불할 수도 있습니다. 이를 수행하려면 billable 모델의 `checkoutCharge` 메소드를 사용하고 요금을 지불할 금액, 제품 이름 및 선택적 수량을 전달할 수 있습니다. 고객이 이 라우트를 방문하면 Stripe의 Checkout 페이지로 리디렉션됩니다:

    use Illuminate\Http\Request;

    Route::get('/charge-checkout', function (Request $request) {
        return $request->user()->checkoutCharge(1200, 'T-Shirt', 5);
    });

> **Warning**  
> When using the `checkoutCharge` method, Stripe will always create a new product and price in your Stripe dashboard. Therefore, we recommend that you create the products up front in your Stripe dashboard and use the `checkout` method instead.
>
> **경고**
> 결제 체크아웃 메소드를 사용할 때, Stripe는 항상 Stripe 대시보드에서 새로운 제품과 가격을 생성합니다. 따라서, Stripe 대시보드에서 제품을 미리 생성하고 `checkout` 메소드를 사용하는 것이 좋습니다.

<a name="subscription-checkouts"></a>
### Subscription Checkouts
### 구독 체크아웃

> **Warning**  
> Using Stripe Checkout for subscriptions requires you to enable the `customer.subscription.created` webhook in your Stripe dashboard. This webhook will create the subscription record in your database and store all of the relevant subscription items.
>
> **경고**
> 구독에 대한 Stripe Checkout를 사용하려면 Stripe 대시보드에서 `customer.subscription.created` 웹훅을 활성화해야 합니다. 이 웹훅은 데이터베이스에 구독 레코드를 생성하고 관련 구독 항목을 모두 저장합니다.

You may also use Stripe Checkout to initiate subscriptions. After defining your subscription with Cashier's subscription builder methods, you may call the `checkout `method. When a customer visits this route they will be redirected to Stripe's Checkout page:

Stripe Checkout를 사용하여 구독을 시작할 수도 있습니다. Cashier의 구독 빌더 메소드로 구독을 정의한 후 `checkout` 메소드를 호출할 수 있습니다. 고객이 이 라우트를 방문하면 Stripe의 Checkout 페이지로 리디렉션됩니다:

    use Illuminate\Http\Request;

    Route::get('/subscription-checkout', function (Request $request) {
        return $request->user()
            ->newSubscription('default', 'price_monthly')
            ->checkout();
    });

Just as with product checkouts, you may customize the success and cancellation URLs:

제품 체크아웃과 마찬가지로, 성공 및 취소 URL을 사용자 정의할 수 있습니다:

    use Illuminate\Http\Request;

    Route::get('/subscription-checkout', function (Request $request) {
        return $request->user()
            ->newSubscription('default', 'price_monthly')
            ->checkout([
                'success_url' => route('your-success-route'),
                'cancel_url' => route('your-cancel-route'),
            ]);
    });

Of course, you can also enable promotion codes for subscription checkouts:

물론, 구독 체크아웃에 프로모션 코드를 활성화할 수도 있습니다:

    use Illuminate\Http\Request;

    Route::get('/subscription-checkout', function (Request $request) {
        return $request->user()
            ->newSubscription('default', 'price_monthly')
            ->allowPromotionCodes()
            ->checkout();
    });

> **Warning**  
> Unfortunately Stripe Checkout does not support all subscription billing options when starting subscriptions. Using the `anchorBillingCycleOn` method on the subscription builder, setting proration behavior, or setting payment behavior will not have any effect during Stripe Checkout sessions. Please consult [the Stripe Checkout Session API documentation](https://stripe.com/docs/api/checkout/sessions/create) to review which parameters are available.
>
> **경고**
> 안타깝게도 Stripe Checkout는 구독을 시작할 때 모든 구독 결제 옵션을 지원하지 않습니다. 구독 빌더의 `anchorBillingCycleOn` 메소드, 조정 행동 또는 결제 행동을 설정하는 것은 Stripe Checkout 세션 동안에는 아무런 영향을 미치지 않습니다. 사용 가능한 매개 변수를 검토하려면 [Stripe Checkout 세션 API 문서](https://stripe.com/docs/api/checkout/sessions/create)를 참조하십시오.

<a name="stripe-checkout-trial-periods"></a>
#### Stripe Checkout & Trial Periods
#### Stripe Checkout & 체험 기간

Of course, you can define a trial period when building a subscription that will be completed using Stripe Checkout:

물론, Stripe Checkout를 사용하여 완료될 구독을 구축할 때 체험 기간을 정의할 수 있습니다:

    $checkout = Auth::user()->newSubscription('default', 'price_monthly')
        ->trialDays(3)
        ->checkout();

However, the trial period must be at least 48 hours, which is the minimum amount of trial time supported by Stripe Checkout.

그러나, 체험 기간은 최소 48 시간이어야 합니다. 이것은 Stripe Checkout에서 지원되는 최소 체험 시간입니다.

<a name="stripe-checkout-subscriptions-and-webhooks"></a>
#### Subscriptions & Webhooks
#### 구독 & 웹훅

Remember, Stripe and Cashier update subscription statuses via webhooks, so there's a possibility a subscription might not yet be active when the customer returns to the application after entering their payment information. To handle this scenario, you may wish to display a message informing the user that their payment or subscription is pending.

명심하세요, Stripe와 Cashier는 웹훅을 통해 구독 상태를 업데이트하므로, 고객이 결제 정보를 입력한 후 어플리케이션으로 돌아왔을 때 구독이 아직 활성화되지 않은 상태일 수 있습니다. 이러한 시나리오를 처리하려면, 사용자에게 결제 또는 구독이 보류 중임을 알리는 메시지를 표시하도록 할 수 있습니다.

<a name="collecting-tax-ids"></a>
### Collecting Tax IDs
### 세금 ID 수집

Checkout also supports collecting a customer's Tax ID. To enable this on a checkout session, invoke the `collectTaxIds` method when creating the session:

Checkout은 고객의 세금 ID를 수집하는 것도 지원합니다. 체크아웃 세션에서 이를 활성화하려면 세션을 생성할 때 `collectTaxIds` 메소드를 호출하십시오:

    $checkout = $user->collectTaxIds()->checkout('price_tshirt');

When this method is invoked, a new checkbox will be available to the customer that allows them to indicate if they're purchasing as a company. If so, they will have the opportunity to provide their Tax ID number.

이 메소드가 호출되면, 고객이 회사로 구매하는지 여부를 표시할 수 있는 새로운 체크박스가 사용 가능해집니다. 그렇다면, 세금 ID 번호를 제공할 기회가 주어집니다.

> **Warning**  
> If you have already configured [automatic tax collection](#tax-configuration) in your application's service provider then this feature will be enabled automatically and there is no need to invoke the `collectTaxIds` method.
>
> **경고**
> 어플리케이션의 서비스 제공자에서 [자동 세금 수집](#tax-configuration)을 구성했다면 이 기능은 자동으로 활성화되며 `collectTaxIds` 메소드를 호출할 필요가 없습니다.

<a name="guest-checkouts"></a>
### Guest Checkouts
### 게스트 체크아웃

Using the `Checkout::guest` method, you may initiate checkout sessions for guests of your application that do not have an "account":

`Checkout::guest` 메소드를 사용하여 어플리케이션의 게스트에 대한 체크아웃 세션을 시작할 수 있습니다. 이들은 "계정"이 없습니다:

    use Illuminate\Http\Request;
    use Laravel\Cashier\Checkout;

    Route::get('/product-checkout', function (Request $request) {
        return Checkout::guest()->create('price_tshirt', [
            'success_url' => route('your-success-route'),
            'cancel_url' => route('your-cancel-route'),
        ]);
    });

Similarly to when creating checkout sessions for existing users, you may utilize additional methods available on the `Laravel\Cashier\CheckoutBuilder` instance to customize the guest checkout session:

기존 사용자를 위해 체크아웃 세션을 생성할 때와 마찬가지로, `Laravel\Cashier\CheckoutBuilder` 인스턴스에서 사용 가능한 추가 메소드를 사용하여 게스트 체크아웃 세션을 사용자 정의할 수 있습니다:

    use Illuminate\Http\Request;
    use Laravel\Cashier\Checkout;

    Route::get('/product-checkout', function (Request $request) {
        return Checkout::guest()
            ->withPromotionCode('promo-code')
            ->create('price_tshirt', [
                'success_url' => route('your-success-route'),
                'cancel_url' => route('your-cancel-route'),
            ]);
    });

After a guest checkout has been completed, Stripe can dispatch a `checkout.session.completed` webhook event, so make sure to [configure your Stripe webhook](https://dashboard.stripe.com/webhooks) to actually send this event to your application. Once the webhook has been enabled within the Stripe dashboard, you may [handle the webhook with Cashier](#handling-stripe-webhooks). The object contained in the webhook payload will be a [`checkout` object](https://stripe.com/docs/api/checkout/sessions/object) that you may inspect in order to fulfill your customer's order.

게스트 체크아웃이 완료된 후, Stripe는 `checkout.session.completed` 웹훅 이벤트를 전송할 수 있으므로, [Stripe 웹훅 구성](https://dashboard.stripe.com/webhooks)하여 이 이벤트를 실제로 어플리케이션으로 보내도록 합니다. Stripe 대시보드에서 웹훅이 활성화된 후, [Cashier로 웹훅 처리](#handling-stripe-webhooks)할 수 있습니다. 웹훅 페이로드에 포함된 객체는 [`checkout` 객체](https://stripe.com/docs/api/checkout/sessions/object)이며, 이를 검사하여 고객의 주문을 완료할 수 있습니다.

<a name="handling-failed-payments"></a>
## Handling Failed Payments
## 실패한 결제 처리

Sometimes, payments for subscriptions or single charges can fail. When this happens, Cashier will throw an `Laravel\Cashier\Exceptions\IncompletePayment` exception that informs you that this happened. After catching this exception, you have two options on how to proceed.

가끔, 구독 또는 단일 결제에 대한 결제가 실패할 수 있습니다. 이러한 경우, Cashier는 이러한 사실을 알리는 `Laravel\Cashier\Exceptions\IncompletePayment` 예외를 발생시킵니다. 이 예외를 잡은 후, 진행 방법에 대해 두 가지 옵션이 있습니다.

First, you could redirect your customer to the dedicated payment confirmation page which is included with Cashier. This page already has an associated named route that is registered via Cashier's service provider. So, you may catch the `IncompletePayment` exception and redirect the user to the payment confirmation page:

먼저, Cashier에 포함된 전용 결제 확인 페이지로 고객을 리디렉션할 수 있습니다. 이 페이지에는 이미 Cashier의 서비스 제공자를 통해 등록된 연관된 이름있는 라우트가 있습니다. 따라서, `IncompletePayment` 예외를 잡고 사용자를 결제 확인 페이지로 리디렉션할 수 있습니다:

    use Laravel\Cashier\Exceptions\IncompletePayment;

    try {
        $subscription = $user->newSubscription('default', 'price_monthly')
                                ->create($paymentMethod);
    } catch (IncompletePayment $exception) {
        return redirect()->route(
            'cashier.payment',
            [$exception->payment->id, 'redirect' => route('home')]
        );
    }

On the payment confirmation page, the customer will be prompted to enter their credit card information again and perform any additional actions required by Stripe, such as "3D Secure" confirmation. After confirming their payment, the user will be redirected to the URL provided by the `redirect` parameter specified above. Upon redirection, `message` (string) and `success` (integer) query string variables will be added to the URL. The payment page currently supports the following payment method types:

결제 확인 페이지에서 고객은 신용 카드 정보를 다시 입력하고 Stripe에서 필요로 하는 추가 작업을 수행하도록 요청됩니다. 예를 들어 "3D Secure" 확인. 결제를 확인한 후, 사용자는 위에서 지정한 `redirect` 매개 변수에 의해 제공된 URL로 리디렉션됩니다. 리디렉션 시, `message` (문자열) 및 `success` (정수) 쿼리 문자열 변수가 URL에 추가됩니다. 결제 페이지는 현재 다음 결제 방법 유형을 지원합니다:

<div class="content-list" markdown="1">

- Credit Cards
- Alipay
- Bancontact
- BECS Direct Debit
- EPS
- Giropay
- iDEAL
- SEPA Direct Debit

</div>

Alternatively, you could allow Stripe to handle the payment confirmation for you. In this case, instead of redirecting to the payment confirmation page, you may [setup Stripe's automatic billing emails](https://dashboard.stripe.com/account/billing/automatic) in your Stripe dashboard. However, if an `IncompletePayment` exception is caught, you should still inform the user they will receive an email with further payment confirmation instructions.

또는, Stripe가 결제 확인을 처리하도록 허용할 수 있습니다. 이 경우, 결제 확인 페이지로 리디렉션하는 대신, Stripe 대시보드에서 [Stripe의 자동 청구 이메일 설정](https://dashboard.stripe.com/account/billing/automatic)할 수 있습니다. 그러나, `IncompletePayment` 예외가 잡히면, 사용자에게 결제 확인 지침을 포함한 이메일을 받을 것이라고 알려야 합니다.

Payment exceptions may be thrown for the following methods: `charge`, `invoiceFor`, and `invoice` on models using the `Billable` trait. When interacting with subscriptions, the `create` method on the `SubscriptionBuilder`, and the `incrementAndInvoice` and `swapAndInvoice` methods on the `Subscription` and `SubscriptionItem` models may throw incomplete payment exceptions.

결제 예외는 `Billable` 트레잇을 사용하는 모델의 `charge`, `invoiceFor`, 및 `invoice` 메소드에 대해 발생할 수 있습니다. 구독과 상호 작용할 때, `SubscriptionBuilder`의 `create` 메소드와 `Subscription` 및 `SubscriptionItem` 모델의 `incrementAndInvoice` 및 `swapAndInvoice` 메소드는 불완전한 결제 예외를 발생시킬 수 있습니다.

Determining if an existing subscription has an incomplete payment may be accomplished using the `hasIncompletePayment` method on the billable model or a subscription instance:

기존 구독에 불완전한 결제가 있는지 여부를 결정하는 데는, 청구 가능 모델 또는 구독 인스턴스의 `hasIncompletePayment` 메소드를 사용할 수 있습니다.

    if ($user->hasIncompletePayment('default')) {
        // ...
    }

    if ($user->subscription('default')->hasIncompletePayment()) {
        // ...
    }

You can derive the specific status of an incomplete payment by inspecting the `payment` property on the exception instance:

불완전한 결제의 특정 상태를 파악하려면, 예외 인스턴스의 `payment` 프로퍼티를 검사할 수 있습니다.

    use Laravel\Cashier\Exceptions\IncompletePayment;

    try {
        $user->charge(1000, 'pm_card_threeDSecure2Required');
    } catch (IncompletePayment $exception) {
        // Get the payment intent status...
        $exception->payment->status;

        // Check specific conditions...
        if ($exception->payment->requiresPaymentMethod()) {
            // ...
        } elseif ($exception->payment->requiresConfirmation()) {
            // ...
        }
    }

<a name="strong-customer-authentication"></a>
## Strong Customer Authentication
## 강력한 고객 인증

If your business or one of your customers is based in Europe you will need to abide by the EU's Strong Customer Authentication (SCA) regulations. These regulations were imposed in September 2019 by the European Union to prevent payment fraud. Luckily, Stripe and Cashier are prepared for building SCA compliant applications.

만약 당신의 비즈니스 또는 고객 중 한 명이 유럽에 있다면, EU의 강력한 고객 인증 (SCA) 규정을 준수해야 합니다. 이 규정은 유럽 연합에 의해 2019년 9월에 결제 사기를 방지하기 위해 시행되었습니다. 다행히도, Stripe와 Cashier는 SCA 준수 어플리케이션을 구축하기 위해 준비되어 있습니다.

> **Warning**  
> Before getting started, review [Stripe's guide on PSD2 and SCA](https://stripe.com/guides/strong-customer-authentication) as well as their [documentation on the new SCA APIs](https://stripe.com/docs/strong-customer-authentication).
>
> **경고**
> 시작하기 전에, [Stripe의 PSD2 및 SCA 가이드](https://stripe.com/guides/strong-customer-authentication)와 [새로운 SCA API에 대한 문서](https://stripe.com/docs/strong-customer-authentication)를 검토하십시오.

<a name="payments-requiring-additional-confirmation"></a>
### Payments Requiring Additional Confirmation
### 추가 확인이 필요한 결제

SCA regulations often require extra verification in order to confirm and process a payment. When this happens, Cashier will throw a `Laravel\Cashier\Exceptions\IncompletePayment` exception that informs you that extra verification is needed. More information on how to handle these exceptions be found can be found in the documentation on [handling failed payments](#handling-failed-payments).

SCA 규정은 종종 결제 확인 및 처리를 위해 추가 확인이 필요합니다. 이러한 경우, Cashier는 추가 확인이 필요하다는 것을 알려주는 `Laravel\Cashier\Exceptions\IncompletePayment` 예외를 발생시킵니다. 이러한 예외를 처리하는 방법에 대한 자세한 내용은 [결제 실패 처리](#handling-failed-payments) 문서에서 찾을 수 있습니다.

Payment confirmation screens presented by Stripe or Cashier may be tailored to a specific bank or card issuer's payment flow and can include additional card confirmation, a temporary small charge, separate device authentication, or other forms of verification.

Stripe 또는 Cashier에 의해 제공되는 결제 확인 화면은 특정 은행 또는 카드 발행사의 결제 흐름에 맞게 조정할 수 있으며, 추가 카드 확인, 일시적인 소액 결제, 별도의 장치 인증 또는 기타 확인 형식을 포함할 수 있습니다.

<a name="incomplete-and-past-due-state"></a>
#### Incomplete and Past Due State
#### 불완전 및 연체 상태

When a payment needs additional confirmation, the subscription will remain in an `incomplete` or `past_due` state as indicated by its `stripe_status` database column. Cashier will automatically activate the customer's subscription as soon as payment confirmation is complete and your application is notified by Stripe via webhook of its completion.

결제에 추가 확인이 필요한 경우, 구독은 `stripe_status` 데이터베이스 컬럼에 의해 나타내는 대로 `incomplete` 또는 `past_due` 상태로 유지됩니다. Cashier는 결제 확인이 완료되고 Stripe 웹훅을 통해 어플리케이션이 완료되었음을 알림을 받은 즉시 고객의 구독을 자동으로 활성화합니다.

For more information on `incomplete` and `past_due` states, please refer to [our additional documentation on these states](#incomplete-and-past-due-status).

`incomplete` 및 `past_due` 상태에 대한 자세한 내용은 [이러한 상태에 대한 추가 문서](#incomplete-and-past-due-status)를 참조하십시오.

<a name="off-session-payment-notifications"></a>
### Off-Session Payment Notifications
### 오프 세션 결제 알림

Since SCA regulations require customers to occasionally verify their payment details even while their subscription is active, Cashier can send a notification to the customer when off-session payment confirmation is required. For example, this may occur when a subscription is renewing. Cashier's payment notification can be enabled by setting the `CASHIER_PAYMENT_NOTIFICATION` environment variable to a notification class. By default, this notification is disabled. Of course, Cashier includes a notification class you may use for this purpose, but you are free to provide your own notification class if desired:

SCA 규정은 구독이 활성화된 상태에서도 고객이 결제 세부 정보를 확인해야 하므로, Cashier는 오프 세션 결제 확인이 필요할 때 고객에게 알림을 보낼 수 있습니다. 예를 들어, 이는 구독이 갱신될 때 발생할 수 있습니다. Cashier의 결제 알림은 `CASHIER_PAYMENT_NOTIFICATION` 환경 변수를 알림 클래스로 설정하여 활성화할 수 있습니다. 기본적으로 이 알림은 비활성화되어 있습니다. 물론, Cashier에는 이 목적으로 사용할 수 있는 알림 클래스가 포함되어 있지만 필요에 따라 고유한 알림 클래스를 제공할 수 있습니다.

```ini
CASHIER_PAYMENT_NOTIFICATION=Laravel\Cashier\Notifications\ConfirmPayment
```

To ensure that off-session payment confirmation notifications are delivered, verify that [Stripe webhooks are configured](#handling-stripe-webhooks) for your application and the `invoice.payment_action_required` webhook is enabled in your Stripe dashboard. In addition, your `Billable` model should also use Laravel's `Illuminate\Notifications\Notifiable` trait.

오프 세션 결제 확인 알림이 전달되도록 하려면, [Stripe 웹훅이 어플리케이션에 구성](#handling-stripe-webhooks)되었는지 확인하고 Stripe 대시보드에서 `invoice.payment_action_required` 웹훅이 활성화되어 있는지 확인하십시오. 또한, `Billable` 모델은 Laravel의 `Illuminate\Notifications\Notifiable` 트레이트를 사용해야 합니다.

> **Warning**  
> Notifications will be sent even when customers are manually making a payment that requires additional confirmation. Unfortunately, there is no way for Stripe to know that the payment was done manually or "off-session". But, a customer will simply see a "Payment Successful" message if they visit the payment page after already confirming their payment. The customer will not be allowed to accidentally confirm the same payment twice and incur an accidental second charge.
>
> **경고**
> 고객이 추가 확인이 필요한 결제를 수동으로 수행할 때도 알림이 전송됩니다. 불행하게도, Stripe가 결제가 수동 또는 "오프 세션"으로 수행되었는지 알 수 있는 방법은 없습니다. 그러나, 고객이 결제를 이미 확인한 후 결제 페이지를 방문하면 "결제 성공" 메시지를 볼 수 있습니다. 고객은 실수로 동일한 결제를 두 번 확인하고 실수로 두 번째 요금을 부과하는 것을 방지할 수 없습니다.

<a name="stripe-sdk"></a>
## Stripe SDK
## Stripe SDK

Many of Cashier's objects are wrappers around Stripe SDK objects. If you would like to interact with the Stripe objects directly, you may conveniently retrieve them using the `asStripe` method:

Cashier의 많은 객체는 Stripe SDK 객체를 래핑합니다. Stripe 객체와 직접 상호 작용하려면 `asStripe` 메소드를 사용하여 간편하게 검색할 수 있습니다.

    $stripeSubscription = $subscription->asStripeSubscription();

    $stripeSubscription->application_fee_percent = 5;

    $stripeSubscription->save();

You may also use the `updateStripeSubscription` method to update a Stripe subscription directly:

또한 `updateStripeSubscription` 메소드를 사용하여 Stripe 구독을 직접 업데이트할 수 있습니다.

    $subscription->updateStripeSubscription(['application_fee_percent' => 5]);

You may invoke the `stripe` method on the `Cashier` class if you would like to use the `Stripe\StripeClient` client directly. For example, you could use this method to access the `StripeClient` instance and retrieve a list of prices from your Stripe account:

`Cashier` 클래스의 `stripe` 메소드를 사용하여 `Stripe\StripeClient` 클라이언트를 직접 사용하려는 경우가 있습니다. 예를 들어, 이 메소드를 사용하여 `StripeClient` 인스턴스에 액세스하고 Stripe 계정에서 가격 목록을 검색할 수 있습니다.

    use Laravel\Cashier\Cashier;

    $prices = Cashier::stripe()->prices->all();

<a name="testing"></a>
## Testing
## 테스트

When testing an application that uses Cashier, you may mock the actual HTTP requests to the Stripe API; however, this requires you to partially re-implement Cashier's own behavior. Therefore, we recommend allowing your tests to hit the actual Stripe API. While this is slower, it provides more confidence that your application is working as expected and any slow tests may be placed within their own PHPUnit testing group.

Cashier를 사용하는 어플리케이션을 테스트할 때는 Stripe API에 대한 실제 HTTP 요청을 모의할 수 있지만, 이렇게 하려면 Cashier 자체의 동작을 부분적으로 다시 구현해야 합니다. 따라서 테스트를 실제 Stripe API에 대한 요청으로 허용하는 것을 권장합니다. 이것이 느리긴 하지만, 어플리케이션이 예상대로 작동하고 있는지에 대한 더 많은 신뢰를 제공하며 느린 테스트는 자체 PHPUnit 테스트 그룹에 포함할 수 있습니다.

When testing, remember that Cashier itself already has a great test suite, so you should only focus on testing the subscription and payment flow of your own application and not every underlying Cashier behavior.

테스트할 때, Cashier 자체가 이미 훌륭한 테스트 스위트를 가지고 있으므로 어플리케이션의 구독 및 결제 흐름만 테스트하고 Cashier의 모든 기본 동작을 테스트하지 않아야 합니다.

To get started, add the **testing** version of your Stripe secret to your `phpunit.xml` file:

시작하려면, `phpunit.xml` 파일에 Stripe 비밀 키의 **testing** 버전을 추가합니다.

    <env name="STRIPE_SECRET" value="sk_test_<your-key>"/>

Now, whenever you interact with Cashier while testing, it will send actual API requests to your Stripe testing environment. For convenience, you should pre-fill your Stripe testing account with subscriptions / prices that you may use during testing.

이제, 테스트 중에 Cashier와 상호 작용할 때마다 실제 API 요청이 Stripe 테스트 환경으로 전송됩니다. 편의를 위해 Stripe 테스트 계정을 테스트 중 사용할 수 있는 구독 / 가격으로 미리 채워야 합니다.

> **Note**  
> In order to test a variety of billing scenarios, such as credit card denials and failures, you may use the vast range of [testing card numbers and tokens](https://stripe.com/docs/testing) provided by Stripe.
>
> **참고**
> 신용카드 거부 및 실패와 같은 다양한 결제 시나리오를 테스트하려면 Stripe에서 제공하는 [테스트 카드 번호 및 토큰](https://stripe.com/docs/testing)의 광범위한 범위를 사용할 수 있습니다.
