# 라라벨 캐셔 (Stripe)

- [시작하기](#introduction)
- [캐셔 업그레이드하기](#upgrading-cashier)
- [설치하기](#installation)
    - [데이터베이스 마이그레이션](#database-migrations)
- [설정하기](#configuration)
    - [Billable 모델](#billable-model)
    - [API Key](#api-keys)
    - [통화 설정하기](#currency-configuration)
    - [세금 설정](#tax-configuration)
    - [로깅](#logging)
    - [사용자 정의 모델 사용](#using-custom-models)
- [고객](#customers)
    - [고객 조회](#retrieving-customers)
    - [고객 생성](#creating-customers)
    - [고객 업데이트](#updating-customers)
    - [잔액](#balances)
    - [Tax ID](#tax-ids)
    - [Stripe와 고객 데이터 동기화](#syncing-customer-data-with-stripe)
    - [빌링 포탈](#billing-portal)
- [결제 수단](#payment-methods)
    - [결제 수단 저장](#storing-payment-methods)
    - [결제 수단 조회](#retrieving-payment-methods)
    - [사용자에게 결제 수단이 있는지 확인](#check-for-a-payment-method)
    - [기본 결제 수단 업데이트](#updating-the-default-payment-method)
    - [결제 수단 추가](#adding-payment-methods)
    - [결제 수단 삭제](#deleting-payment-methods)
- [정기 구독 모델](#subscriptions)
    - [새로운 정기 구독 생성하기](#creating-subscriptions)
    - [정기 구독 상태 확인하기](#checking-subscription-status)
    - [가격 변경하기](#changing-prices)
    - [정기 구독 수량 변경하기](#subscription-quantity)
    - [다중 가격 구독](#multiprice-subscriptions)
    - [사용량 계산 청구](#metered-billing)
    - [정기 구독의 세금계산](#subscription-taxes)
    - [정기 구독의 고정일](#subscription-anchor-date)
    - [정기 구독 취소하기](#cancelling-subscriptions)
    - [정기 구독 재개](#resuming-subscriptions)
- [구독 평가기간-trial](#subscription-trials)
    - [결제 수단 사전 등록](#with-payment-method-up-front)
    - [결제 수단없이 사전 등록](#without-payment-method-up-front)
    - [평가기간 연장](#extending-trials)
- [Stripe webook 처리하기](#handling-stripe-webhooks)
    - [webook 이벤트 핸들러 정의하기](#defining-webhook-event-handlers)
    - [Webhook의 서명 검증](#verifying-webhook-signatures)
- [한번만 결제하기](#single-charges)
    - [기본 결제](#simple-charge)
    - [청구서와 같이 결제](#charge-with-invoice)
    - [환불 수수료](#refunding-charges)
- [결제하기](#checkout)
    - [제품 결제](#product-checkouts)
    - [한번만 결제하기](#single-charge-checkouts)
    - [구독 결제하기](#subscription-checkouts)
    - [세금 ID 수집](#collecting-tax-ids)
- [청구서](#invoices)
    - [인보이스 검색](#retrieving-invoices)
    - [예정 인보이스](#upcoming-invoices)
    - [구독 인보이스 미리보기](#previewing-subscription-invoices)
    - [인보이스 PDF 생성](#generating-invoice-pdfs)
- [결제 실패 처리](#handling-failed-payments)
- [강력한 고객 인증 (SCA)](#strong-customer-authentication)
    - [추가 확인이 필요한 결제](#payments-requiring-additional-confirmation)
    - [세션 외 결제 알림](#off-session-payment-notifications)
- [Stripe SDK](#stripe-sdk)
- [테스팅](#testing)

<a name="introduction"></a>
## 시작하기

[라라벨 캐셔 Stripe](https://github.com/laravel/cashier-stripe) 는 [Stripe](https://stripe.com)에 의해서 제공되는 손쉽고 편리한 구독(정기 과금) 서비스를 위한 인터페이스를 제공합니다. 라라벨 캐셔는 여러분이 작성하는데 어려움을 겪는 구독(정기과금 서비스)를 위한 청구서에서 필요한 거의 모든 템플릿(boilerplate)들을 다룹니다. 기본적인 구독 관리 외에도, 캐셔를 통해서 쿠폰 관리, 구독 변경, 구매 수량 변경, 취소 유예 기간, 그리고 청구서를 PDF로 생성할 수도 있습니다.

<a name="upgrading-cashier"></a>
## 캐셔 업그레이드하기

Cashier의 새 버전으로 업그레이드 할 때는 [업그레이드 가이드](https://github.com/laravel/cashier-stripe/blob/master/UPGRADE.md) 를 주의 깊게 검토해야합니다.

> {note} 변경을 방지하기 위해 캐셔는 Stripe API 버전을 고정으로 사용합니다. Cashier 12은 Stripe API 버전 `2020-08-27`를 사용합니다. Stripe API 버전은 새로운 Stripe 기능과 개선 사항을 사용하기 위해 마이너 릴리스에서 업데이트됩니다.

<a name="installation"></a>
## 설치하기

먼저 Composer 패키지 관리자를 사용하여 Stripe용 Cashier 패키지를 설치합니다.

    composer require laravel/cashier

> {note} 캐셔가 모든 스트라이프 이벤트를 올바르게 처리하도록하려면 [Cashier의 웹훅 처리](#handling-stripe-webhooks)를 설정해야합니다.

<a name="database-migrations"></a>
### 데이터베이스 마이그레이션

Cashier 서비스 프로바이더는 자체 데이터베이스 마이그레이션 디렉토리를 등록하므로, 반드시 패키지를 설치 한 후 데이터베이스를 마이그레이션해야합니다. Cashier 마이그레이션은 `users`테이블에 여러 컬럼을 추가하고 모든 고객의 구독을 저장 할 `subscriptions`테이블을 생성합니다.

    php artisan migrate

캐셔와 함께 제공된 마이그레이션을 덮어 써야하는 경우 `vendor:publish` 아티잔 명령어을 사용하여 마이그레이션 파일을 내보낼 수 있습니다.

    php artisan vendor:publish --tag="cashier-migrations"

Cashier의 마이그레이션이 실행되지 않게하려면 캐셔가 제공하는 `ignoreMigrations` 메소드를 사용하면 됩니다. 일반적으로 이 메소드는 `AppServiceProvider`의 `register` 메소드에서 호출합니다.

    use Laravel\Cashier\Cashier;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        Cashier::ignoreMigrations();
    }

> {note} Stripe는 Stripe 식별자를 저장하는 데 사용되는 모든 열을 대소문자를 구분할 것을 권장합니다. 따라서 MySQL을 사용할 때 `stripe_id` 열에 대한 열 데이터 정렬이 `utf8_bin`으로 설정되어 있는지 확인해야 합니다. 이에 대한 자세한 내용은 [Stripe 문서](https://stripe.com/docs/upgrades#what-changes-does-stripe-consider-to-be-backwards-compatible) 에서 확인할 수 있습니다.

<a name="configuration"></a>
## 설정하기

<a name="billable-model"></a>
### Billable 모델

Cashier를 사용하기 전에 청구 가능한 모델 정의에 `Billable` 특성-trait을 추가하십시오. 일반적으로 `App\Models\User` 모델이 됩니다. 이 특성은 구독 생성, 쿠폰 적용 및 결제 방법 정보 업데이트와 같은 일반적인 청구 작업을 수행할 수 있는 다양한 메서드를 제공합니다.

    use Laravel\Cashier\Billable;

    class User extends Authenticatable
    {
        use Billable;
    }

캐셔는 Billable 모델이 Laravel과 함께 제공되는 `App\Models\User` 클래스라고 가정합니다. 이것을 바꾸고 싶다면 `useCustomerModel` 메소드를 통해서 변경 할 수 있습니다. 이 메소드는 `AppServiceProvider` 클래스에서 `boot` 메소드에서 호출 합니다.

    use App\Models\Cashier\User;
    use Laravel\Cashier\Cashier;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Cashier::useCustomerModel(User::class);
    }

> {note} Laravel에서 제공 한 `App\Models\User` 모델 이외의 모델을 사용하는 경우, 대체 모델의 테이블 이름과 일치하도록 제공된 [Cashier migrations](#installation)을 퍼블리싱하고 변경해야합니다.

<a name="api-keys"></a>
### API Key

다음으로 애플리케이션의 `.env` 파일에서 Stripe API 키를 설정해야 합니다. Stripe 제어판에서 Stripe API 키를 찾을 수 있습니다.

    STRIPE_KEY=your-stripe-key
    STRIPE_SECRET=your-stripe-secret

<a name="currency-configuration"></a>
### 통화 설정하기

캐셔의 기본 통화는 미국 달러(USD)입니다. 애플리케이션의 `.env` 파일에서 `CASHIER_CURRENCY` 환경 변수를 설정하여 기본 통화를 변경할 수 있습니다.

    CASHIER_CURRENCY=eur

Cashier의 통화 구성 외에도 송장에 표시 할 돈의 값을 포매팅-formatting 할 때 사용할 로케일-locale을 지정할 수도 있습니다. 내부적으로 캐셔는 [PHP의 `NumberFormatter` 클래스](https://www.php.net/manual/en/class.numberformatter.php) 를 사용하여 통화 로케일-locale을 설정합니다.

    CASHIER_CURRENCY_LOCALE=nl_BE

> {note} `en` 이외의 로케일을 사용하려면 서버에 `ext-intl` PHP 확장모듈이 설치 및 설정되어 있는지 확인하십시오.

<a name="tax-configuration"></a>
### 세금 설정

[Stripe Tax](https://stripe.com/tax) 덕분에, 모든 세금계산, 계산서 발행이 자동으로 이루어집니다. `App\Providers\AppServiceProvider` 클래스에 `boot` 메소드에서 `calculateTaxes` 메소드를 호출 하면 자동으로 세금을 계산해줍니다.

    use Laravel\Cashier\Cashier;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Cashier::calculateTaxes();
    }

모든 세금계산이 활성화 된다면, 모든 구독, 일회성 청구서가 생성되며, 자동으로 세금 계산된 계산서를 받게 될 것입니다.

해당 기능이 올바르기 동작하기 위해서는 고객의 이름, 주소 그리고 tax ID와 같은 고객의 청구 상세 정보가 Stripe와 동기화 되도록 필요로 합니다. 동기화가 되도록 [customer data synchronization](#syncing-customer-data-with-stripe) 그리고 [Tax ID](#tax-ids) 메소드 들을 제공할 것 입니다.

> {note} [single charges](#single-charges) 또는 [single charge checkouts](#single-charge-checkouts) 을 참고 바랍니다. 그리고, Stripe Tax는 현재 베타기간 동안 초대 받은 유저만 사용할 수 있습니다. 사용을 하고자 하신다면 [Stripe Tax website](https://stripe.com/tax#request-access) 에서 요청 하실 수 있습니다.


<a name="logging"></a>
### 로깅-Logging

캐셔를 사용하면 치명적인 Stripe 오류를 기록할 때 사용할 로그 채널을 지정할 수 있습니다. 애플리케이션의 `.env` 파일 내에서 `CASHIER_LOGGER` 환경 변수를 정의하여 로그 채널을 지정할 수 있습니다.

    CASHIER_LOGGER=stack

Stripe에 대한 API 호출에 의해 생성된 예외는 애플리케이션의 기본 로그 채널을 통해 기록됩니다.

<a name="using-custom-models"></a>
### 커스텀 모델 사용하기

내가 모델을 만들어서 정의하고 그 모델은 Cashier를 상속받고, Cashier 내부적으로 사용하는 모델을 자유롭게 확장 할 수 있습니다.

    use Laravel\Cashier\Subscription as CashierSubscription;

    class Subscription extends CashierSubscription
    {
        // ...
    }

모델을 정의 한 후 Cashier에 내가 정의한 커스텀 모델을 `Laravel\Cashier\Cashier` 클래스에 정의 해야합니다. 일반적으로, 어플리케이션의 `App\Providers\AppServiceProvider` 클래스에 `boot` 메소드에 커스텀 모델을 Casher 한테 알려줍니다.

    use App\Models\Cashier\Subscription;
    use App\Models\Cashier\SubscriptionItem;

    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Cashier::useSubscriptionModel(Subscription::class);
        Cashier::useSubscriptionItemModel(SubscriptionItem::class);
    }

<a name="customers"></a>
## 고객

<a name="retrieving-customers"></a>
### 고객 조회

`Cashier::findBillable` 메서드를 사용하여 스트라이프 ID로 고객을 조회 할 수 있습니다. 그러면 메소드에서 Billable 모델의 인스턴스가 반환됩니다.

    use Laravel\Cashier\Cashier;

    $user = Cashier::findBillable($stripeId);

<a name="creating-customers"></a>
### 고객 생성하기

경우에 따라 구독을 시작하지 않고 Stripe 고객을 만들 수도 있습니다. 이 경우 `createAsStripeCustomer` 메소드를 사용하면 됩니다.

    $stripeCustomer = $user->createAsStripeCustomer();

고객이 Stripe에서 생성되고나면 구독을 시작할 수 있습니다. 추가 [Stripe API에서 지원하는 고객 생성 매개변수](https://stripe.com/docs/api/customers/create) 를 전달할 선택적 `$options` 배열을 제공할 수 있습니다.

    $stripeCustomer = $user->createAsStripeCustomer($options);

청구 가능한 모델에 대해 Stripe 고객 객체를 반환하려면 `asStripeCustomer` 메서드를 사용할 수 있습니다.

    $stripeCustomer = $user->asStripeCustomer();

`createOrGetStripeCustomer` 메소드는 주어진 청구 가능 모델에 대한 Stripe 고객 객체를 검색하고 싶지만, 청구 가능 모델이 이미 Stripe 내 고객인지 확실하지 않은 경우 사용할 수 있습니다. 이 메서드는 Stripe에 새로운 고객이 없는 경우 새로 생성합니다.

    $stripeCustomer = $user->createOrGetStripeCustomer();

<a name="updating-customers"></a>
### 고객 업데이트

경우에 따라 Stripe 고객에게 추가 정보를 직접 업데이트할 수 있습니다. `updateStripeCustomer` 메소드를 사용하여 이를 수행할 수 있습니다. 이 메서드는 [Stripe API에서 지원하는 고객 업데이트 옵션](https://stripe.com/docs/api/customers/update) 의 배열을 입력받습니다.

    $stripeCustomer = $user->updateStripeCustomer($options);

<a name="balances"></a>
### 잔액

Stripe를 통하여 고객의 credit 또는 잔고를 인출 할 수 있습니다. 이후, 잔고는 새로운 청구서에 입금되거나 차감될 것입니다. 고객의 총 잔액을 체크하기 위해서는 billable 모델에서 `balance` 메소드를 사용할 수 있습니다. `balance` 메소드는 고객의 잔액을 통화에 맞추어 형태를 갖춘 문자열을 반환 할 것 입니다.

    $balance = $user->balance();

고객의 잔액을 credit으로 전환하기 위해서 `applyBalance` 메소드에 음수를 사용할 수 있습니다. 또 원하는 경우 설명도 같이 제공할 수 있습니다.

    $user->applyBalance(-500, 'Premium customer top-up.');

`applyBalance` 메소드에 양수를 제공하고, 고객의 잔고에서 금액을 인출합니다.

    $user->applyBalance(300, 'Bad usage penalty.');

`applyBalance` 메소드는 고객을 위하여 새로운 고객의 거래를 만듭니다. `balanceTransactions` 메소드를 통하여 해당 거래를 조회 할 수 있습니다. 이것은 고객이 검토 할 시 creadit 및 금액 인출 기록을 제공하는 데 유용할 수 있습니다.

    // Retrieve all transactions...
    $transactions = $user->balanceTransactions();

    foreach ($transactions as $transaction) {
        // Transaction amount...
        $amount = $transaction->amount(); // $2.31

        // Retrieve the related invoice when available...
        $invoice = $transaction->invoice();
    }

<a name="tax-ids"></a>
### Tax ID

캐셔는 고객의 tax IDs를 쉽게 관리하도록 제공합니다. 예를들어, `taxIds` 메소드는 모든 [tax IDs](https://stripe.com/docs/api/customer_tax_ids/object) 를 조회하고, 각 고객에게 컬렉션으로 할당합니다. 

    $taxIds = $user->taxIds();

또한 고객을 위하여 특정한 tax ID를 조회 할 수 있습니다.

    $taxId = $user->findTaxId('txi_belgium');

검증된 [타입](https://stripe.com/docs/api/customer_tax_ids/object#tax_id_object-type) 그리고 새로운 값을 가지고 `createTaxId` 메소드를 사용하요 새로운 Tax ID 생성 할 수 있습니다.

    $taxId = $user->createTaxId('eu_vat', 'BE0123456789');

`createTaxId` 메소드는 즉시 고객의 계정에 VAT ID를 추가합니다. [VAT ID 역시 Stripe에 의해 검증 되어있습니다.](https://stripe.com/docs/invoicing/customer/tax-ids#validation); 그러나, 해당 작업은 동기적인 작업입니다. `customer.tax_id.updated` webhook 이벤트를 통하여 [VAT ID의 `인증`된 파라미터] 여부를 검증 할 수 있습니다. webhooks에 대한 더 자세한 정보는 [webhook handler 정의 문서](#handling-stripe-webhooks) 를 확인 바랍니다.

`deleteTaxId` 메소드를 사용하여 tax ID를 지울 수 있습니다.

    $user->deleteTaxId('txi_belgium');

<a name="syncing-customer-data-with-stripe"></a>
### Stripe와 고객 데이터 동기화

일반적으로 어플리케이션에서 유저가 이메일이나 이름 과 같은 다른 정보를 업데이트 할 시 Stripe에 도 동일하게 업데이트 해야합니다. 이렇게 할 시 Stripe에도 어플리케이션의 정보가 저장되어 동기화가 됩니다.

이를 자동화 하기 위해서 billable 모델에 `updated` 이벤트에 반응하는 이벤트 리스너를 정의 할 수 있습니다. 그런 다음, 이벤트 리스너에서 `syncStripeCustomerDetails`메소드를 모델에서 호출 합니다.

    use function Illuminate\Events\queueable;

    /**
     * The "booted" method of the model.
     *
     * @return void
     */
    protected static function booted()
    {
        static::updated(queueable(function ($customer) {
            if ($customer->hasStripeId()) {
                $customer->syncStripeCustomerDetails();
            }
        }));
    }

지금, 매 시간 고객 모델이 업데이트 될 때, 그 정보들이 Stripe와 동기화 될 것 입니다. 편의상, 최초 고객 생성시 캐셔는 자동으로 고객의 정보를 Stripe와 동기화 합니다. 

Cashier에서 제공하는 다양한 메소드를 재정의 하여 Stripe로 고객 정보를 동기화 하는데 사용하는 컬럼명을 재정의 할 수 있습니다. 예를 들어 `stripeName` 메서드를 재정의하여 Cashier 에서 고객 정보를 Stripe 와 동기화할 때 고객의 "이름" 으로 간주해야 하는 속성을 사용자 정의할 수 있습니다.

    /**
     * Get the customer name that should be synced to Stripe.
     *
     * @return string|null
     */
    public function stripeName()
    {
        return $this->company_name;
    }

이와 비슷하게, `stripeEmail`, `stripePhone`, 그리고 `stripeAddress` 메소드를 재정의 할 수 있습니다. 해당 메소드들은 Stripe 고객정보를 업데이트할 때 해당 고객 파라미터와 정보를 동기화합니다. 만약 고객 정보 동기화 절차를 완벽히 통제하고 싶으면, `syncStripeCustomerDetails` 메소드를 재정의 하여 사용하시면 됩니다.

<a name="billing-portal"></a>
### 빌링 포탈

Stripe는 [결제 포탈을 설정하는 쉬운 방법](https://stripe.com/docs/billing/subscriptions/customer-portal) 을 제공하므로 고객이 구독, 결제 수단을 관리하고 결제 내역을 볼 수 있습니다. 컨트롤러 또는 라우트에서 `redirectToBillingPortal` 메소드를 사용하여 컨트롤러 또는 라우터에서 사용자를 결제 포탈로 리디렉션 할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/billing-portal', function (Request $request) {
        return $request->user()->redirectToBillingPortal();
    });

기본적으로 사용자가 구독 관리를 마치면 Stripe 청구 포털 내의 링크를 통해 애플리케이션의 `home` 경로로 돌아갈 수 있습니다. URL을 `redirectToBillingPortal` 메소드에 대한 인수로 전달하여 사용자가 반환해야 하는 사용자 정의 URL을 제공할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/billing-portal', function (Request $request) {
        return $request->user()->redirectToBillingPortal(route('billing'));
    });

HTTP 리디렉션 응답을 생성하지 않고 청구 포털에 대한 URL을 생성하려면 `billingPortalUrl` 메소드를 호출할 수 있습니다.

    $url = $request->user()->billingPortalUrl(route('billing'));

<a name="payment-methods"></a>
## 결제 수단

<a name="storing-payment-methods"></a>
### 결제 수단 저장

Stripe로 구독을 생성하거나 "일회성" 청구를 수행하려면 결제 수단을 저장하고 Stripe에서 식별자를 검색해야 합니다. 이를 수행하는 데 사용되는 접근 방식은 결제 방법을 구독에 사용할 것인지 단일 청구에 사용할 것인지에 따라 다르므로 아래에서 둘 다 살펴보겠습니다.

<a name="payment-methods-for-subscriptions"></a>
#### 구독을 위한 결제 수단

구독에서 나중에 사용할 수 있도록 고객의 신용 카드 정보를 저장할 때 Stripe "Setup Intents" API를 사용하여 고객의 결제 방법 세부 정보를 안전하게 수집해야 합니다. "설정 의도-Setup Intent"는 Stripe가 고객의 결제 수단에 요금을 청구할 의사가 있음을 나타냅니다. Cashier의 `Billable` 특성-trait에는 새 Setup Intent를 쉽게 생성할 수 있는 `createSetupIntent` 메서드가 포함되어 있습니다. 고객의 지불 방법 세부 정보를 수집하는 양식을 렌더링할 경로 또는 컨트롤러에서 이 메서드를 호출해야 합니다.

    return view('update-payment-method', [
        'intent' => $user->createSetupIntent()
    ]);

"Setup Intent"를 생성 한 후 데이터를 view로 전달하고, 비밀번호 및 지불방식 데이터를 element에 바인딩 할 수 있습니다. 예를 들어 다음 "결제 방법 업데이트" 폼을 참고하십시오:

```html
<input id="card-holder-name" type="text">

<!-- Stripe Elements Placeholder -->
<div id="card-element"></div>

<button id="card-button" data-secret="{{ $intent->client_secret }}">
    Update Payment Method
</button>
```

다음으로 Stripe.js 라이브러리를 사용하고 [Stripe Element](https://stripe.com/docs/stripe-js) 를 폼에 추가하여 고객의 결제 세부 사항을 안전하게 가져 올 수 있습니다.

```html
<script src="https://js.stripe.com/v3/"></script>

<script>
    const stripe = Stripe('stripe-public-key');

    const elements = stripe.elements();
    const cardElement = elements.create('card');

    cardElement.mount('#card-element');
</script>
```

다음으로, [Stripe의 `confirmCardSetup` 메소드](https://stripe.com/docs/stripe-js/reference#stripe-handle-card) 를 사용하여 카드를 확인하고 안전한 "결제 방법 식별자"를 Stripe에서 조회 할 수 있습니다.

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

Stripe에서 카드인증 후 결과값을 `setupIntent.payment_method` 식별자를 라라벨 애플리케이션에 전달하여 고객에게 추가 할 수 있습니다. 결제 수단은 [결제 수단 추가](#adding-payment-methods) 또는 [기본 결제 수단 업데이트](#updating-the-default-payment-method) 일 수 있습니다. 결제 수단 식별자를 즉시 ​​사용하여 [새로운 정기 구독 생성하기](#creating-subscriptions)를 할 수도 있습니다.

> {tip} 설정 의도 및 고객 지불 정보 수집에 대한 자세한 내용을 보려면 [Stripe에서 제공하는 개요](https://stripe.com/docs/payments/save-and-reuse#php) 를 참고하십시오.

<a name="payment-methods-for-single-charges"></a>
#### 단일 청구에 대한 결제 수단

물론 고객의 결제 수단에 대해 단일 청구를 할 때 결제 수단 식별자는 한 번만 사용하면 됩니다. Stripe 제한으로 인해 단일 청구에 대해 고객의 저장된 기본 결제 수단을 사용할 수 없습니다. 고객이 Stripe.js 라이브러리를 사용하여 결제 방법 세부 정보를 입력하도록 등록해야 합니다. 예를 들어 다음 양식을 보십시오.

```html
<input id="card-holder-name" type="text">

<!-- Stripe Elements Placeholder -->
<div id="card-element"></div>

<button id="card-button">
    Process Payment
</button>
```

이러한 양식을 정의한 후 Stripe.js 라이브러리를 사용하여 [Stripe Element](https://stripe.com/docs/stripe-js) 를 양식에 첨부하고 고객의 결제 세부 정보를 안전하게 수집할 수 있습니다.

```html
<script src="https://js.stripe.com/v3/"></script>

<script>
    const stripe = Stripe('stripe-public-key');

    const elements = stripe.elements();
    const cardElement = elements.create('card');

    cardElement.mount('#card-element');
</script>
```

다음으로, [Stripe의 `createPaymentMethod` 메소드](https://stripe.com/docs/stripe-js/reference#stripe-create-payment) 를 사용하여 카드를 확인하고 안전한 "지불 방법 식별자"를 Stripe에서 조회 할 수 있습니다.

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

카드가 성공적으로 확인되면 `paymentMethod.id`를 라라벨 애플리케이션에 전달하고 [단일 청구](#simple-charge)를 처리 할 수 있습니다.

<a name="retrieving-payment-methods"></a>
### 결제 수단 검색

Billable 모델 인스턴스의 `paymentMethods` 메소드는 `Laravel\Cashier\PaymentMethod` 인스턴스의 콜렉션을 리턴합니다.

    $paymentMethods = $user->paymentMethods();

기본적으로 이 메서드는 `card` 유형의 결제 방법을 반환합니다. 다른 유형의 지불 방법을 검색하려면 `type`을 해당 메서드에 대한 인수로 전달할 수 있습니다.

    $paymentMethods = $user->paymentMethods('sepa_debit');

고객의 기본 결제 수단을 검색하려면 `defaultPaymentMethod` 메서드를 사용할 수 있습니다.

    $paymentMethod = $user->defaultPaymentMethod();

`findPaymentMethod` 메서드를 사용하여 청구 가능 모델에 연결된 특정 결제 수단을 검색할 수 있습니다.

    $paymentMethod = $user->findPaymentMethod($paymentMethodId);

<a name="check-for-a-payment-method"></a>
### 사용자에게 결제 수단이 있는지 확인

청구 가능 모델에 계정에 연결된 기본 결제 수단이 있는지 확인하려면 `hasDefaultPaymentMethod` 메서드를 호출하세요.

    if ($user->hasDefaultPaymentMethod()) {
        //
    }

`hasPaymentMethod` 메서드를 사용하여 청구 가능한 모델에 계정에 연결된 결제 방법이 하나 이상 있는지 확인할 수 있습니다.

    if ($user->hasPaymentMethod()) {
        //
    }

해당 메소드는 billable 모델이, 카드형태를 결제 수단을 가지고 있는지 판단합니다. 다른 유형의 지급방법이 있는지 여부를 확인하기 위해서 `type` 파라미터를 메소드에 전달할 수 있습니다.

    if ($user->hasPaymentMethod('sepa_debit')) {
        //
    }

<a name="updating-the-default-payment-method"></a>
### 기본 결제 수단 업데이트

`updateDefaultPaymentMethod` 메소드는 고객의 기본 결제 수단 정보를 업데이트하는 데 사용할 수 있습니다. 이 방법은 Stripe에서 제공하는 지불방법만 허용하고, 새 지불 방법을 기본 청구 지불 방법으로 지정합니다.

    $user->updateDefaultPaymentMethod($paymentMethod);

기본 결제 수단 정보를 Stripe의 고객의 기본 결제 수단 정보와 동기화하려면 `updateDefaultPaymentMethodFromStripe` 메소드를 사용할 수 있습니다.

    $user->updateDefaultPaymentMethodFromStripe();

> {note} 고객의 기본 결제 수단은 인보이스 발행 및 새 구독 생성에만 사용할 수 있습니다. Stripe에서 부과하는 제한 사항으로 인해 1회 충전에 사용할 수 없습니다.

<a name="adding-payment-methods"></a>
### 결제 수단 추가

새로운 결제 수단을 추가하려면 billable 모델의 `addPaymentMethod` 메소드를 호출하여 결제 수단 식별자를 전달하면됩니다.

    $user->addPaymentMethod($paymentMethod);

> {tip} 결제 수단 식별자를 조회하는 방법을 알아 보려면 [결제 수단 저장](#storing-payment-methods)을 확인하십시오.

<a name="deleting-payment-methods"></a>
### 결제 수단 삭제

결제 수단을 삭제하려면 삭제하려는 `Laravel\Cashier\PaymentMethod` 인스턴스에서 `delete` 메소드를 호출하면됩니다.

    $paymentMethod->delete();

`deletePaymentMethods` 메소드는 Billable 모델에 대한 모든 지불 수단 정보를 삭제합니다.

    $user->deletePaymentMethods();

기본적으로 이 방법은 `card` 유형의 결제 수단을 삭제합니다. 다른 유형의 결제 수단을 삭제하려면 `type`을 메서드에 대한 인수로 전달할 수 있습니다.

    $user->deletePaymentMethods('sepa_debit');

> {note} 사용자가 현재 정기 구독중인 경우, 애플리케이션에서 기본 결제 수단을 삭제하지 못하게 해야합니다.

<a name="subscriptions"></a>
## 정기 구독 모델

구독은 고객에 대한 반복 지불을 설정하는 방법을 제공합니다. Cashier에서 관리하는 Stripe 구독은 여러 구독 가격, 구독 수량, 평가판 등을 지원합니다.

<a name="creating-subscriptions"></a>
### 새로운 정기 구독 생성하기

새로운 정기 구독을 생성하려면, 먼저 청구가 가능한 (일반적으로 `App\Models\User`가 되는 ) 모델 인스턴스를 조회해야 합니다. 모델 인스턴스를 조회하면, 모델에 대한 정기 구독을 생성하기 위해 `newSubscription` 메소드를 사용할 수 있습니다.

    use Illuminate\Http\Request;

    Route::post('/user/subscribe', function (Request $request) {
        $request->user()->newSubscription(
            'default', 'price_monthly'
        )->create($request->paymentMethodId);

        // ...
    });

`newSubscription` 메소드에 전달된 첫 번째 인수는 구독의 내부 이름이어야 합니다. 애플리케이션에서 단일 구독만 제공하는 경우 이를 `default` 또는 `primary`이라고 부를 수 있습니다. 이 구독 이름은 내부 애플리케이션 용도로만 사용되며 사용자에게 표시되지 않습니다. 또한 공백을 포함해서는 안되며 구독을 생성한 후 변경해서는 안 됩니다. 두 번째 인수는 사용자가 구독하는 특정 가격입니다. 이 값은 Stripe의 가격 식별자와 일치해야 합니다.

[Stripe 지불 방법 식별자](#storing-payment-methods) 또는 Stripe의 `PaymentMethod` 객체를 허용하는 `create` 메소드는 구독을 시작하고 청구 가능한 모델의 Stripe 고객 ID 및 기타 관련 청구정보로 데이터베이스를 업데이트합니다.

> {note} 결제 수단 식별자를 `create` 구독 수단에 직접 전달하면 사용자의 저장된 결제 수단에도 자동으로 추가됩니다.

<a name="collecting-recurring-payments-via-invoice-emails"></a>
#### 인보이스 이메일을 통한 반복 결제 수집

고객의 반복 지불을 자동으로 수집하는 대신, 반복 지불이 만기될 때마다 고객에게 청구서를 이메일로 보내도록 Stripe에 지시할 수 있습니다. 그런 다음 고객은 송장을 받으면 수동으로 지불할 수 있습니다. 고객은 인보이스를 통해 반복 결제를 수집할 때 결제 방법을 미리 제공할 필요가 없습니다.

    $user->newSubscription('default', 'price_monthly')->createAndSendInvoice();

구독이 취소되기 전에 고객이 청구서를 발송해야 하는 기간은 [Stripe 대시보드](https://dashboard.stripe.com/settings/billing/automatic) 내의 구독 및 청구서 설정에 따라 결정됩니다.

<a name="subscription-quantities"></a>
#### 수량

구독을 생성할 때 가격에 대해 특정 [수량](https://stripe.com/docs/billing/subscriptions/quantities) 을 설정하려면 구독을 만들기 전에 구독 빌더에서 `quantity` 메서드를 호출해야 합니다.

    $user->newSubscription('default', 'price_monthly')
         ->quantity(5)
         ->create($paymentMethod);

<a name="additional-details"></a>
#### 추가정보

추가 [고객](https://stripe.com/docs/api/customers/create) 또는 [구독](https://stripe.com/docs/api/subscriptions/create) 옵션을 지정하려는 경우 지원됨 Stripe에서 `create` 메소드에 두 번째 및 세 번째 인수로 전달하면 됩니다.

    $user->newSubscription('default', 'price_monthly')->create($paymentMethod, [
        'email' => $email,
    ], [
        'metadata' => ['note' => 'Some extra information.'],
    ]);

<a name="coupons"></a>
#### 쿠폰

새로운 구독을 생성 할 때 쿠폰을 적용할 수 있게 하려면 `withCoupon` 메소드를 사용하면 됩니다.

    $user->newSubscription('default', 'price_monthly')
         ->withCoupon('code')
         ->create($paymentMethod);

또는 [Stripe 프로모션 코드](https://stripe.com/docs/billing/subscriptions/discounts/codes) 를 적용하고 싶다면 `withPromotionCode` 메소드를 사용할 수 있습니다. 지정된 프로모션 코드 ID는 프로모션 코드에 할당된 Stripe API ID여야 하며 고객 대상 프로모션 코드는 아닙니다.

    $user->newSubscription('default', 'price_monthly')
         ->withPromotionCode('promo_code')
         ->create($paymentMethod);

<a name="adding-subscriptions"></a>
#### 구독 추가

이미 기본 결제 수단이 있는 고객에게 구독을 추가하려면 구독 작성기에서 `add` 메서드를 호출하면 됩니다.

    use App\Models\User;

    $user = User::find(1);

    $user->newSubscription('default', 'price_monthly')->add();

<a name="creating-subscriptions-from-the-stripe-dashboard"></a>
#### Stripe 대시보드에서 구독 생성

Stripe 대시보드 자체에서 구독을 생성할 수도 있습니다. 그렇게 하면 캐셔는 새로 추가된 구독을 동기화하고 `default`이라는 이름을 할당합니다. 대시보드 생성 구독에 할당된 구독 이름을 사용자 지정하려면 [`WebhookController` 확장](/docs/{{version}}/billing#defining-webhook-event-handlers) 및 `newSubscriptionName` 메서드를 덮어씁니다.

또한 Stripe 대시보드를 통해 한 가지 유형의 구독만 생성할 수 있습니다. 애플리케이션이 다른 이름을 사용하는 여러 구독을 제공하는 경우 Stripe 대시보드를 통해 한 가지 유형의 구독만 추가할 수 있습니다.

마지막으로 애플리케이션에서 제공하는 구독 유형별로 활성 구독을 하나만 추가해야 합니다. 고객에게 2개의 `default` 구독이 있는 경우 둘 다 애플리케이션의 데이터베이스와 동기화되더라도 가장 최근에 추가된 구독만 Cashier에서 사용됩니다.

<a name="checking-subscription-status"></a>
### 정기 구독 가입 상태 확인하기

고객이 당신의 애플리케이션에 가입하면 다양한 편리한 방법을 사용하여 쉽게 가입 상태를 확인할 수 있습니다. 첫째, `subscribed` 메소드는 구독이 현재 평가판 기간 내에 있더라도 활성 구독이 있는 경우 `true`를 반환합니다. `subscribed` 메서드는 구독 이름을 첫 번째 인수로 받아들입니다.

    if ($user->subscribed('default')) {
        //
    }

`subscribed` 메소드는 [라우트 미들웨어](/docs/{{version}}/middleware)에 사용될수 있는 좋은 메소드 중 하나입니다. 사용자의 구독 상태에 따라서, 라우트 및 컨트롤러에 대한 액세스를 제한할 할 수 있습니다.

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

사용자가 현재 무료 평가기간(trial) 을 통해서 이용중인지 아닌지 확인하고자 한다면, `onTrial` 메소드를 사용하면 됩니다. 이 메소드는 사용자들에게 그들이 현재 무료 평가기간을 이용중이라는 정보를 표시하는데 유용하게 사용될 수 있습니다.

    if ($user->subscription('default')->onTrial()) {
        //
    }

`subscribedToProduct` 메서드는 사용자가 주어진 Stripe 제품의 식별자를 기반으로 주어진 제품에 가입했는지 확인하는 데 사용할 수 있습니다. Stripe에서 제품은 가격의 집합체입니다. 이 예에서는 사용자의 `default` 구독이 애플리케이션의 "프리미엄" 제품에 적극적으로 구독되어 있는지 확인합니다. 주어진 Stripe 제품 식별자는 Stripe 대시보드의 제품 식별자 중 하나와 일치해야 합니다.

    if ($user->subscribedToProduct('prod_premium', 'default')) {
        //
    }

`subscribedToProduct` 메소드에 배열을 전달하면 사용자의 `default` 구독이 애플리케이션의 "기본" 또는 "프리미엄" 제품에 적극적으로 구독되는지 확인할 수 있습니다.

    if ($user->subscribedToProduct(['prod_basic', 'prod_premium'], 'default')) {
        //
    }

`subscribedToPrice` 메서드는 고객의 구독이 주어진 가격 ID에 해당하는지 확인하는 데 사용할 수 있습니다.

    if ($user->subscribedToPrice('price_basic_monthly', 'default')) {
        //
    }

`recurring` 메소드는 사용자가 현재 구독 중이며 더이상 평가기간에 속해 있지 않은지를 확인할 때 사용 할 수 있습니다. 

    if ($user->subscription('default')->recurring()) {
        //
    }

> {note} 같은 이름의 구독이 2개 있는 사용자의 경우 항상 `subscription` 메서드를 통해 가장 최근 구독을 반환합니다. 예를 들어, 사용자는 `default`라는 두 개의 구독 레코드를 가질 수 있습니다. 그러나 구독 중 하나는 만료된 오래된 구독이고 다른 하나는 현재 활성 구독일 수 있습니다. 가장 최근의 구독은 항상 반환되며 이전 구독은 기록 검토를 위해 데이터베이스에 보관됩니다.

<a name="cancelled-subscription-status"></a>
#### 취소된 구독 상태

사용자가 한때 활성 구독자였으나 구독을 취소했는지 확인하려면 `canceled` 메서드를 사용할 수 있습니다.

    if ($user->subscription('default')->canceled()) {
        //
    }

또한 사용자가 구독을 취소했지만 구독이 완전히 만료될 때까지 "유예 기간"에 있는지 확인할 수도 있습니다. 예를 들어 사용자가 원래 3월 10일에 만료될 예정이었던 구독을 3월 5일에 취소하면 사용자는 3월 10일까지 "유예 기간"이 됩니다. 이 시간 동안 `subscribed` 메소드는 여전히 `true`를 반환합니다.

    if ($user->subscription('default')->onGracePeriod()) {
        //
    }

사용자가 구독을 취소했고 더 이상 "유예 기간"이 아닌지 확인하려면 `ended` 메서드를 사용할 수 있습니다.

    if ($user->subscription('default')->ended()) {
        //
    }

<a name="incomplete-and-past-due-status"></a>
#### 미완료 및 기한 만료 상태

구독 후 생성 된 보조 결제 수단이 필요한 경우 구독이 `incomplete`로 표시됩니다. 구독 상태는 Cashier의 `subscriptions` 데이터베이스 테이블의 `stripe_status` 컬럼에 저장됩니다.

마찬가지로 가격을 교환할 때 2차 결제 조치가 필요한 경우 구독은 `past_due`으로 표시됩니다. 구독이 이러한 상태 중 하나에 있으면 고객이 결제를 확인할 때까지 활성화되지 않습니다. 구독에 불완전한 결제가 있는지 확인하는 것은 청구 가능한 모델 또는 구독 인스턴스에서 `hasIncompletePayment` 메서드를 사용하여 수행할 수 있습니다.

    if ($user->hasIncompletePayment('default')) {
        //
    }

    if ($user->subscription('default')->hasIncompletePayment()) {
        //
    }

구독에 결제가 완료되지 않았다면 `latestPayment`식별자를 전달하여 사용자를 Cashier의 결제 확인 페이지로 안내해야합니다. 구독 인스턴스에서 사용 가능한 `latestPayment` 메소드를 사용하여 이 식별자를 조회 할 수 있습니다.

```html
<a href="{{ route('cashier.payment', $subscription->latestPayment()->id) }}">
    Please confirm your payment.
</a>
```

구독이 `past_due` 상태에 있을 때 여전히 활성 상태로 간주되도록 하려면 Cashier에서 제공하는 `keepPastDueSubscriptionsActive` 메서드를 사용할 수 있습니다. 일반적으로 이 메소드는 `App\Providers\AppServiceProvider`의 `register` 메소드에서 호출해야 합니다.

    use Laravel\Cashier\Cashier;

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        Cashier::keepPastDueSubscriptionsActive();
    }

> {note} 구독이 `incomplete`상태 인 경우 결제가 확인 될 때까지 구독을 변경할 수 없습니다. 따라서 `swap`및 `updateQuantity`메소드는 구독이 `incomplete`상태 인 경우 예외를 발생시킵니다.

<a name="subscription-scopes"></a>
#### 구독 Scope

대부분의 구독 상태는 쿼리 scope로도 사용할 수 있으므로 데이터베이스에서 주어진 상태의 구독을 쉽게 쿼리할 수 있습니다.

    // Get all active subscriptions...
    $subscriptions = Subscription::query()->active()->get();

    // Get all of the canceled subscriptions for a user...
    $subscriptions = $user->subscriptions()->canceled()->get();

사용 가능한 scope의 전체 목록은 아래에서 확인할 수 있습니다.

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
### 가격 변경

고객이 당신의 애플리케이션을 구독한 후 때때로 새로운 구독 가격으로 변경하기를 원할 수 있습니다. 고객을 새 가격으로 바꾸려면 Stripe 가격의 식별자를 `swap` 메서드에 전달하세요. 가격을 바꿀 때 사용자가 이전에 취소한 구독을 다시 활성화하기를 원한다고 가정합니다. 주어진 가격 식별자는 Stripe 대시보드에서 사용 가능한 Stripe 가격 식별자와 일치해야 합니다.

    use App\Models\User;

    $user = App\Models\User::find(1);

    $user->subscription('default')->swap('price_yearly');

고객이 평가판을 사용 중인 경우 평가판 기간이 유지됩니다. 또한 구독에 "수량"이 있는 경우 해당 수량도 유지됩니다.

가격을 바꾸고 고객이 현재 사용 중인 평가판 기간을 취소하려면 `skipTrial` 메서드를 호출할 수 있습니다.

    $user->subscription('default')
            ->skipTrial()
            ->swap('price_yearly');

가격을 교환하고 고객의 다음 청구 주기를 기다리는 대신 즉시 청구서를 작성하려면 `swapAndInvoice` 메소드를 사용할 수 있습니다.

    $user = User::find(1);

    $user->subscription('default')->swapAndInvoice('price_yearly');

<a name="prorations"></a>
#### 일할계산

기본적으로 Stripe는 가격을 바꿀 때 요금을 일할 계산합니다. `noProrate` 메서드는 요금을 일할계산하지 않고 구독 가격을 변경하는 데 사용할 수 있습니다.

    $user->subscription('default')->noProrate()->swap('price_yearly');

구독의 일할계산에 대한 자세한 내용은 [스트라이프 문서](https://stripe.com/docs/billing/subscriptions/prorations) 를 참조하십시오.

> {note} `swapAndInvoice` 메소드보다 먼저 `noProrate` 메소드를 실행해도 일할계산에 영향을 미치지 않습니다. 청구서는 항상 발행됩니다.

<a name="subscription-quantity"></a>
### 정기 구독 수량 변경하기

때때로 구독은 "수량"의 영향을 받습니다. 예를 들어 프로젝트 관리 애플리케이션은 프로젝트당 월 $10을 청구할 수 있습니다. `incrementQuantity` 및 `decrementQuantity` 메서드를 사용하여 구독 수량을 쉽게 늘리거나 줄일 수 있습니다.

    use App\Models\User;

    $user = User::find(1);

    $user->subscription('default')->incrementQuantity();

    // Add five to the subscription's current quantity...
    $user->subscription('default')->incrementQuantity(5);

    $user->subscription('default')->decrementQuantity();

    // Subtract five from the subscription's current quantity...
    $user->subscription('default')->decrementQuantity(5);

이 대신에, 특정 수량을 지정하고자 한다면, `updateQuantity` 메소드를 사용하면 됩니다.

    $user->subscription('default')->updateQuantity(10);

`noProrate` 메소드를 사용하면 요금에 영향을 주지 않으면서 구독의 수량을 수정 할 수 있습니다.

    $user->subscription('default')->noProrate()->updateQuantity(10);

구독 수량에 대한 보다 자세한 내용은 [Stripe 문서](https://stripe.com/docs/subscriptions/quantities) 를 참고하십시오.

<a name="multiprice-subscription-quantities"></a>
#### 다중 가격 구독 수량

구독이 [multiprice subscription](#multiprice-subscriptions) 인 경우 증가 또는 감소하려는 수량의 가격 이름을 증분 감소 메소드의 두 번째 인수로 전달해야 합니다.

    $user->subscription('default')->incrementQuantity(1, 'price_chat');

<a name="multiprice-subscriptions"></a>
### 다중 가격 구독

[Multiprice subscriptions](https://stripe.com/docs/billing/subscriptions/multiple-products) 을 사용하면 단일 구독에 여러 청구 가격을 지정할 수 있습니다. 예를 들어 기본 구독 가격이 월 $10이지만 라이브 채팅 추가 기능 가격을 월 $15로 추가하는 고객 서비스 "헬프데스크" 애플리케이션을 구축한다고 가정해 보겠습니다. 다중 가격 구독 정보는 Cashier의 `subscription_items` 데이터베이스 테이블에 저장됩니다.

가격 배열을 `newSubscription` 메소드에 두 번째 인수로 전달하여 주어진 구독에 대해 여러 가격을 지정할 수 있습니다.

    use Illuminate\Http\Request;

    Route::post('/user/subscribe', function (Request $request) {
        $request->user()->newSubscription('default', [
            'price_monthly',
            'price_chat',
        ])->create($request->paymentMethodId);

        // ...
    });

위의 예에서 고객은 `default` 구독에 두 개의 가격이 첨부됩니다. 두 가격 모두 각각의 청구 간격에 따라 청구됩니다. 필요한 경우 `quantity` 메서드를 사용하여 각 가격에 대한 특정 수량을 표시할 수 있습니다.

    $user = User::find(1);

    $user->newSubscription('default', ['price_monthly', 'price_chat'])
        ->quantity(5, 'price_chat')
        ->create($paymentMethod);

기존 구독에 다른 가격을 추가하려면 구독의 `addPrice` 메서드를 호출하면 됩니다.

    $user = User::find(1);

    $user->subscription('default')->addPrice('price_chat');

위의 예는 새 가격을 추가하고 고객은 다음 청구 주기에 이에 대해 청구됩니다. 고객에게 즉시 청구하려면 `addPriceAndInvoice` 메소드를 사용할 수 있습니다.

    $user->subscription('default')->addPriceAndInvoice('price_chat');

특정 수량의 가격을 추가하려면 `addPrice` 또는 `addPriceAndInvoice` 메소드의 두 번째 인수로 수량을 전달할 수 있습니다.

    $user = User::find(1);

    $user->subscription('default')->addPrice('price_chat', 5);

`removePrice` 메소드를 사용하여 구독에서 가격을 제거할 수 있습니다.

    $user->subscription('default')->removePrice('price_chat');

> {note} 구독의 마지막 가격을 삭제할 수 없습니다. 대신 구독을 취소하기만 하면 됩니다.

<a name="swapping-prices"></a>
#### 가격 교환

다중 가격 구독에 첨부된 가격을 변경할 수도 있습니다. 예를 들어 고객이 `price_chat` 추가 기능 가격이 포함된 `price_basic` 구독을 갖고 있고 고객을 `price_basic`에서 `price_pro` 가격으로 업그레이드하려고 한다고 가정해 보겠습니다.

    use App\Models\User;

    $user = User::find(1);

    $user->subscription('default')->swap(['price_pro', 'price_chat']);

위의 예를 실행하면 `price_basic`이 있는 기본 구독 항목이 삭제되고 `price_chat`이 있는 구독 항목이 유지됩니다. 또한 `price_pro`에 대한 새 구독 항목이 생성됩니다.

키 값 쌍의 배열을 `swap` 메서드에 전달하여 구독 항목 옵션을 지정할 수도 있습니다. 예를 들어 구독 가격 수량을 지정해야 할 수 있습니다.

    $user = User::find(1);

    $user->subscription('default')->swap([
        'price_pro' => ['quantity' => 5],
        'price_chat'
    ]);

구독에서 단일 가격을 바꾸려면 구독 항목 자체에 `swap` 메서드를 사용하면 됩니다. 이 접근 방식은 구독의 다른 가격에 대한 기존 메타데이터를 모두 유지하려는 경우에 특히 유용합니다.

    $user = User::find(1);

    $user->subscription('default')
            ->findItemOrFail('price_basic')
            ->swap('price_pro');

<a name="proration"></a>
#### 일할계산

기본적으로 Stripe은 다중 가격 구독에서 가격을 추가하거나 제거할 때 요금을 일할계산합니다. 일할계산 없이 가격을 조정하려면 `noProrate` 메서드를 가격 작업에 연결해야 합니다.

    $user->subscription('default')->noProrate()->removePrice('price_chat');

<a name="swapping-quantities"></a>
#### 수량

개별 구독 가격의 수량을 업데이트하려면 가격 이름을 메서드에 대한 추가 인수로 전달하여 [기존 수량 메서드](#subscription-quantity)을 사용하여 업데이트할 수 있습니다.

    $user = User::find(1);

    $user->subscription('default')->incrementQuantity(5, 'price_chat');

    $user->subscription('default')->decrementQuantity(3, 'price_chat');

    $user->subscription('default')->updateQuantity(10, 'price_chat');

> {note} 구독에 여러 가격이 있는 경우 `Subscription` 모델의 `stripe_price` 및 `수량` 속성은 `null`이 됩니다. 개별 가격 속성에 액세스하려면 `Subscription` 모델에서 사용 가능한 `items` 관계를 사용해야 합니다.

<a name="subscription-items"></a>
#### 구독 항목

구독에 여러 가격이 있는 경우 데이터베이스의 `subscription_items` 테이블에 여러 구독 "항목"이 저장됩니다. 구독의 `items` 관계를 통해 액세스할 수 있습니다.

    use App\Models\User;

    $user = User::find(1);

    $subscriptionItem = $user->subscription('default')->items->first();

    // Retrieve the Stripe price and quantity for a specific item...
    $stripePrice = $subscriptionItem->stripe_price;
    $quantity = $subscriptionItem->quantity;

`findItemOrFail` 메서드를 사용하여 특정 가격을 검색할 수도 있습니다.

    $user = User::find(1);

    $subscriptionItem = $user->subscription('default')->findItemOrFail('price_chat');

<a name="metered-billing"></a>
### 사용량 계산 청구

[사용량 계산 청구](https://stripe.com/docs/billing/subscriptions/metered-billing) 를 사용하면 청구 주기 동안 제품 사용량을 기준으로 고객에게 청구할 수 있습니다. 예를 들어, 한 달에 보내는 문자 메시지나 이메일 수를 기준으로 고객에게 요금을 청구할 수 있습니다.

사용량 계산 청구를 사용하려면 먼저 Stripe 대시보드에서 metered price로 새 제품을 생성해야 합니다. 그런 다음 `meteredPrice`를 사용하여 측정된 가격 ID를 고객 구독에 추가합니다.

    use Illuminate\Http\Request;

    Route::post('/user/subscribe', function (Request $request) {
        $request->user()->newSubscription('default')
            ->meteredPrice('price_metered')
            ->create($request->paymentMethodId);

        // ...
    });

[Stripe Checkout](#checkout)을 통해 사용량 구독을 시작할 수도 있습니다.

    $checkout = Auth::user()
            ->newSubscription('default', [])
            ->meteredPrice('price_metered')
            ->checkout();

    return view('your-checkout-view', [
        'checkout' => $checkout,
    ]);

<a name="reporting-usage"></a>
#### 사용량 보고

고객이 당신의 애플리케이션을 사용하면, 정확하게 청구될 수 있도록 당신의 사용량을 Stripe에 보고해야 합니다. 사용량 구독의 사용량을 늘리려면 `reportUsage` 메서드를 사용할 수 있습니다.

    $user = User::find(1);

    $user->subscription('default')->reportUsage();

기본적으로 "사용 수량" 1이 청구 기간에 추가됩니다. 또는 청구 기간 동안 고객의 사용량에 추가하기 위해 특정 양의 "사용량"을 전달할 수 있습니다.

    $user = User::find(1);

    $user->subscription('default')->reportUsage(15);

애플리케이션이 단일 구독에 대해 여러 가격을 제공하는 경우 `reportUsageFor` 메서드를 사용하여 사용량을 보고하려는 사용량 가격을 지정해야 합니다.

    $user = User::find(1);

    $user->subscription('default')->reportUsageFor('price_metered', 15);

때로는 이전에 보고한 사용량을 업데이트해야 할 수도 있습니다. 이를 수행하려면 타임스탬프 또는 `DateTimeInterface` 인스턴스를 `reportUsage`의 두 번째 매개변수로 전달할 수 있습니다. 그렇게 하면 Stripe은 주어진 시간에 보고된 사용량을 업데이트합니다. 주어진 날짜와 시간이 여전히 현재 청구 기간 내에 있으므로 이전 사용 기록을 계속 업데이트할 수 있습니다.

    $user = User::find(1);

    $user->subscription('default')->reportUsage(5, $timestamp);

<a name="retrieving-usage-records"></a>
#### 사용 기록 검색

고객의 과거 사용량을 검색하려면 구독 인스턴스의 `usageRecords` 메서드를 사용할 수 있습니다.

    $user = User::find(1);

    $usageRecords = $user->subscription('default')->usageRecords();

애플리케이션이 단일 구독에 대해 여러 가격을 제공하는 경우 `usageRecordsFor` 메서드를 사용하여 사용 기록을 검색하려는 측정 가격을 지정할 수 있습니다.

    $user = User::find(1);

    $usageRecords = $user->subscription('default')->usageRecordsFor('price_metered');

`usageRecords` 및 `usageRecordsFor` 메서드는 사용 레코드의 연관 배열을 포함하는 Collection 인스턴스를 반환합니다. 이 배열을 반복하여 고객의 총 사용량을 표시할 수 있습니다.

    @foreach ($usageRecords as $usageRecord)
        - Period Starting: {{ $usageRecord['period']['start'] }}
        - Period Ending: {{ $usageRecord['period']['end'] }}
        - Total Usage: {{ $usageRecord['total_usage'] }}
    @endforeach

반환된 모든 사용 데이터와 Stripe의 커서 기반 페이지 매김 사용 방법에 대한 전체 참조는 [공식 Stripe API 문서](https://stripe.com/docs/api/usage_records/subscription_item_summary_list) 를 참조하세요.

<a name="subscription-taxes"></a>
### 구독 세금

> {note} 수동으로 세율을 계산하는 대신 [Stripe Tax를 사용하여 세금 자동 계산](#tax-configuration)을 할 수 있습니다.

사용자가 구독에 대해 지불하는 세율을 지정하려면 청구 가능한 모델에 `taxRates` 메서드를 구현하고 Stripe 세율 ID를 포함하는 배열을 반환해야 합니다. [내 Stripe 대시보드](https://dashboard.stripe.com/test/tax-rates) 에서 다음 세율을 정의할 수 있습니다.

    /**
     * The tax rates that should apply to the customer's subscriptions.
     *
     * @return array
     */
    public function taxRates()
    {
        return ['txr_id'];
    }

`taxRates` 메서드를 사용하면 고객별로 세율을 적용할 수 있으므로 여러 국가와 세율에 걸쳐 있는 사용자 기반에 도움이 될 수 있습니다.

다중 가격 구독을 제공하는 경우, 청구 가능 모델에 `priceTaxRates` 메서드를 구현하여 각 가격에 대해 서로 다른 세율을 정의할 수 있습니다.

    /**
     * The tax rates that should apply to the customer's subscriptions.
     *
     * @return array
     */
    public function priceTaxRates()
    {
        return [
            'price_monthly' => ['txr_id'],
        ];
    }

> {note} `taxRates` 메소드는 정기구독의 결제 시에만 적용됩니다. "한번 결제"에서 캐셔를 사용하는 경우 세율을 직접 적용해야합니다.

<a name="syncing-tax-rates"></a>
#### 세율 동기화

`taxRates` 메서드에서 반환된 하드 코딩된 세율 ID를 변경할 때 사용자의 기존 구독에 대한 세금 설정은 동일하게 유지됩니다. 기존 구독에 대한 세금 값을 새 `taxRates` 값으로 업데이트하려면 사용자의 구독 인스턴스에서 `syncTaxRates` 메서드를 호출해야 합니다.

    $user->subscription('default')->syncTaxRates();

이것은 또한 모든 다중 가격 구독 항목 세율을 동기화합니다. 애플리케이션이 다중 가격 구독을 제공하는 경우 청구 가능 모델이 [위에서 논의된](#subscription-taxes) `priceTaxRates` 메소드를 구현하는지 확인해야 합니다.

<a name="tax-exemption"></a>
#### 면세

캐셔는 또한 `isNotTaxExempt`, `isTaxExempt` 및 `reverseChargeApplies` 메서드를 제공하여 고객이 면세 대상인지 확인합니다. 이러한 메서드는 Stripe API를 호출하여 고객의 면세 상태를 확인합니다.

    use App\Models\User;

    $user = User::find(1);

    $user->isTaxExempt();
    $user->isNotTaxExempt();
    $user->reverseChargeApplies();

> {note} 이 메소드는 모든 `Laravel\Cashier\Invoice` 객체에서도 사용할 수 있습니다. 그러나 `Invoice` 객체에서 호출되는 경우 메서드는 인보이스가 생성된 시점의 면제 상태를 결정합니다.

<a name="subscription-anchor-date"></a>
### 정기 구독의 고정일

기본적으로 청구 주기 기준은 구독이 생성된 날짜이거나 평가판 기간이 사용된 경우 평가판이 종료되는 날짜입니다. 청구 기준일을 수정하려면 `anchorBillingCycleOn` 메소드를 사용할 수 있습니다.

    use Illuminate\Http\Request;

    Route::post('/user/subscribe', function (Request $request) {
        $anchor = Carbon::parse('first day of next month');

        $request->user()->newSubscription('default', 'price_monthly')
                    ->anchorBillingCycleOn($anchor->startOfDay())
                    ->create($request->paymentMethodId);

        // ...
    });

정기 구독의 결제주기 관리에 대한 자세한 내용은 [Stripe billing cycle documentation](https://stripe.com/docs/billing/subscriptions/billing-cycle) 에서 확인이 가능합니다

<a name="cancelling-subscriptions"></a>
### 정기구독 취소하기

정기구독을 취소하기 위해서는, 사용자의 정기구독에 대해서 `cancle` 메소드를 호출하면 됩니다.

    $user->subscription('default')->cancel();

구독이 취소되면 캐셔는 자동으로 `subscriptions` 데이터베이스 테이블의 `ends_at` 열을 설정합니다. 이 열은 `subscribed` 메소드가 `false`를 반환하기 시작해야 할 때를 아는 데 사용됩니다.

예를 들어 고객이 3월 1일에 구독을 취소했지만 구독이 3월 5일까지 종료되도록 예약되지 않은 경우 `subscribed` 메서드는 3월 5일까지 계속 `true`를 반환합니다. 이는 일반적으로 사용자가 청구 주기가 끝날 때까지 애플리케이션을 계속 사용할 수 있기 때문에 수행됩니다.

사용자가 구독을 취소했지만 `onGracePeriod` 메서드를 사용하여 아직 "유예 기간"에 있는지 확인할 수 있습니다.

    if ($user->subscription('default')->onGracePeriod()) {
        //
    }

정기구독을 즉시 취소하고자 한다면, 사용자의 정기구독 에서 `cancelNow` 메소드를 호출하면 됩니다.

    $user->subscription('default')->cancelNow();

구독을 즉시 취소하고 청구되지 않은 나머지 측정된 사용량 또는 새로운 보류 중인 비례 청구 항목에 대해 청구하려면 사용자 구독에서 `cancelNowAndInvoice` 메서드를 호출하세요.

    $user->subscription('default')->cancelNowAndInvoice();

특정 시점에 구독을 취소하도록 선택할 수도 있습니다.

    $user->subscription('default')->cancelAt(
        now()->addDays(10)
    );

<a name="resuming-subscriptions"></a>
### 정기 구독 재개하기

고객이 구독을 취소했는데 다시 시작하려는 경우 구독에서 `resume` 메서드를 호출할 수 있습니다. 구독을 재개하려면 고객이 여전히 "유예 기간" 내에 있어야 합니다.

    $user->subscription('default')->resume();

고객이 구독을 취소한 다음 구독이 완전히 만료되기 전에 해당 구독을 재개하는 경우 고객에게 즉시 요금이 청구되지 않습니다. 대신 구독이 다시 활성화되고 원래 청구 주기에 따라 청구됩니다.

<a name="subscription-trials"></a>
## 구독 평가기간

<a name="with-payment-method-up-front"></a>
### 결제 수단 사전 등록

고객에게 평가기간을 제공하고, 결제 정보를 사전에 등록 해달라고 요청하고자 한다면, 구독을 생성할 때 `trialDays` 메소드를 해야합니다.

    use Illuminate\Http\Request;

    Route::post('/user/subscribe', function (Request $request) {
        $request->user()->newSubscription('default', 'price_monthly')
                    ->trialDays(10)
                    ->create($request->paymentMethodId);

        // ...
    });

이 메서드는 데이터베이스 내의 구독 레코드에 평가판 기간 종료 날짜를 설정하고, 이 날짜 이후까지 고객에게 청구를 시작하지 않도록 Stripe에 지시합니다. `trialDays` 방법을 사용할 때 캐셔는 Stripe의 가격에 대해 구성된 모든 기본 평가 기간을 덮어씁니다.

> {참고} 평가판 종료일 이전에 고객의 구독을 취소하지 않으면 평가판 만료 즉시 요금이 청구되므로 사용자에게 평가판 종료일을 알려야 합니다.

`trialUntil` 메소드를 사용하면 평가판 기간이 종료되어야 하는 시기를 지정하는 `DateTime` 인스턴스를 전달할 수 있습니다.

    use Carbon\Carbon;

    $user->newSubscription('default', 'price_monthly')
                ->trialUntil(Carbon::now()->addDays(10))
                ->create($paymentMethod);

사용자 인스턴스의 `onTrial` 메서드 또는 구독 인스턴스의 `onTrial` 메서드를 사용하여 사용자가 평가판 기간 내에 있는지 확인할 수 있습니다. 아래 두 가지 예는 동일합니다.

    if ($user->onTrial('default')) {
        //
    }

    if ($user->subscription('default')->onTrial()) {
        //
    }

`endTrial` 메서드를 사용하여 구독 평가판을 즉시 종료할 수 있습니다.

    $user->subscription('default')->endTrial();

<a name="defining-trial-days-in-stripe-cashier"></a>
#### Stripe / 캐셔에서 평가기간 정의

Stripe 대시보드에서 가격이 받는 평가판 일수를 정의하거나 항상 Cashier를 사용하여 명시적으로 전달할 수 있습니다. Stripe에서 가격의 평가판 날짜를 정의하기로 선택한 경우 과거에 구독이 있었던 고객의 새 구독을 포함하여 새 구독은 `skipTrial()` 메서드를 명시적으로 호출하지 않는 한 항상 평가판 기간을 받게 됩니다.

<a name="without-payment-method-up-front"></a>
### 결제 수단없이 사전 등록

고객에게 신용카드에 대한 결제 정보의 사전등록 없이 평가기간을 부여하고자 한다면, 사용자의 `trial_ends_at` 컬럼에 트리이얼 종료기간을 설정하면 됩니다. 이는 일반적으로 사용자를 등록할 때 설정하게 됩니다.

    use App\Models\User;

    $user = User::create([
        // ...
        'trial_ends_at' => now()->addDays(10),
    ]);

> {note} 청구 가능한 모델의 클래스 정의 내에서 `trial_ends_at` 속성에 대해 [날짜 캐스트](/docs/{{version}}/eloquent-mutators##date-casting)를 추가해야 합니다.

캐셔는 이 유형의 평가판을 기존 구독에 연결되지 않기 때문에 "일반 평가판"이라고 합니다. 청구 가능한 모델 인스턴스의 `onTrial` 메서드는 현재 날짜가 `trial_ends_at` 값을 초과하지 않은 경우 `true`를 반환합니다.

    if ($user->onTrial()) {
        // User is within their trial period...
    }

사용자에게 실제 구독을 생성할 준비가 되면, 평소대로 `newSubscription` 메소드를 사용하면됩니다.

    $user = User::find(1);

    $user->newSubscription('default', 'price_monthly')->create($paymentMethod);

사용자의 평가판 종료 날짜를 검색하려면 `trialEndsAt` 메서드를 사용할 수 있습니다. 이 메서드는 사용자가 평가판을 사용 중이면 Carbon 날짜 인스턴스를 반환하고 그렇지 않은 경우 `null`을 반환합니다. 기본 구독이 아닌 특정 구독에 대한 평가판 종료 날짜를 얻으려면 선택적 구독 이름 매개변수를 전달할 수도 있습니다.

    if ($user->onTrial()) {
        $trialEndsAt = $user->trialEndsAt('main');
    }

사용자가 "일반" 평가판 기간 내에 있고 아직 실제 구독을 생성하지 않았는지 구체적으로 알고 싶다면 `onGenericTrial` 방법을 사용할 수도 있습니다.

    if ($user->onGenericTrial()) {
        // User is within their "generic" trial period...
    }

<a name="extending-trials"></a>
### 평가기간 연장

`extendTrial` 메서드를 사용하면 구독이 생성된 후 구독의 평가판 기간을 연장할 수 있습니다. 평가판이 이미 만료되었고 고객에게 이미 구독 요금이 청구되고 있는 경우에도 연장된 평가판을 제공할 수 있습니다. 평가판 기간 내에 소요된 시간은 고객의 다음 청구서에서 차감됩니다.

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
## Stripe webhook 처리하기

> {tip} [the Stripe CLI](https://stripe.com/docs/stripe-cli) 을 사용하여 로컬 개발 도중에도 웹훅을 테스트 할 수 있습니다.

Stripe는 웹후크를 통해 다양한 이벤트를 애플리케이션에 알릴 수 있습니다. 기본적으로 Cashier의 webhook 컨트롤러를 가리키는 경로는 Cashier 서비스 제공자가 자동으로 등록합니다. 이 컨트롤러는 들어오는 모든 웹훅 요청을 처리합니다.

기본적으로 Cashier 웹훅 컨트롤러는 실패한 요금이 너무 많은 구독 취소(Stripe 설정에 정의됨), 고객 업데이트, 고객 삭제, 구독 업데이트 및 결제 방법 변경을 자동으로 처리합니다. 그러나 곧 알게 되겠지만 이 컨트롤러를 확장하여 원하는 Stripe webhook 이벤트를 처리할 수 있습니다.

애플리케이션이 Stripe 웹훅을 처리할 수 있도록 하려면 Stripe 제어판에서 웹훅 URL을 구성해야 합니다. 기본적으로 Cashier의 웹훅 컨트롤러는 `/stripe/webhook` URL 경로에 응답합니다. Stripe 제어판에서 활성화해야 하는 모든 웹훅의 전체 목록은 다음과 같습니다.

- `customer.subscription.created`
- `customer.subscription.updated`
- `customer.subscription.deleted`
- `customer.updated`
- `customer.deleted`
- `invoice.payment_action_required`

편의를 위해 캐셔에는 `cashier:webhook` Artisan 명령이 포함되어 있습니다. 이 명령은 Cashier에 필요한 모든 이벤트를 수신하는 Stripe에서 웹훅을 생성합니다.

    php artisan cashier:webhook

기본적으로 생성된 웹훅은 `APP_URL` 환경 변수로 정의된 URL과 Cashier에 포함된 `cashier.webhook` 경로를 가리킵니다. 다른 URL을 사용하려는 경우 명령을 호출할 때 `--url` 옵션을 제공할 수 있습니다.

    php artisan cashier:webhook --url "https://example.com/stripe/webhook"

생성된 웹훅은 귀하의 Cashier 버전과 호환되는 Stripe API 버전을 사용합니다. 다른 Stripe 버전을 사용하려면 `--api-version` 옵션을 제공할 수 있습니다.

    php artisan cashier:webhook --api-version="2019-12-03"

생성 후 웹훅은 즉시 활성화됩니다. 웹훅을 만들고 싶지만 준비가 될 때까지 비활성화된 상태로 유지하려면 다음 명령을 호출할 때 `--disabled` 옵션을 제공할 수 있습니다.

    php artisan cashier:webhook --disabled

> {note} Cashier에 포함된 [webhook 서명 확인](#verifying-webhook-signatures) 미들웨어를 사용하여 들어오는 Stripe 웹훅 요청을 보호해야 합니다.

<a name="webhooks-csrf-protection"></a>
#### Webhook & CSRF 보호

Stripe 웹훅은 라라벨의 [CSRF 보호](/docs/{{version}}/csrf)를 우회해야 하므로 애플리케이션의 `App\Http\Middleware\VerifyCsrfToken` 미들웨어에 URI를 예외로 추가하거나 `web` 미들웨어 그룹의 외부로 경로를 지정해야 합니다.

    protected $except = [
        'stripe/*',
    ];

<a name="defining-webhook-event-handlers"></a>
### Webhook 이벤트 핸들러 정의하기

캐셔는 실패한 청구 및 기타 일반적인 Stripe 웹훅 이벤트에 대한 구독 취소를 자동으로 처리합니다. 그러나 처리하려는 추가 webhook 이벤트가 있는 경우 캐셔에서 전달하는 다음 이벤트를 수신하여 처리할 수 있습니다.

- `Laravel\Cashier\Events\WebhookReceived`
- `Laravel\Cashier\Events\WebhookHandled`

두 이벤트 모두 Stripe 웹훅의 전체 페이로드를 포함합니다. 예를 들어 `invoice.payment_succeeded` 웹훅을 처리하려는 경우 이벤트를 처리할 [listener](/docs/{{version}}/events#defining-listeners)를 등록할 수 있습니다.

    <?php

    namespace App\Listeners;

    use Laravel\Cashier\Events\WebhookReceived;

    class StripeEventListener
    {
        /**
         * Handle received Stripe webhooks.
         *
         * @param  \Laravel\Cashier\Events\WebhookReceived  $event
         * @return void
         */
        public function handle(WebhookReceived $event)
        {
            if ($event->payload['type'] === 'invoice.payment_succeeded') {
                // Handle the incoming event...
            }
        }
    }

리스너가 정의되면 애플리케이션의 `EventServiceProvider` 내에 리스너를 등록할 수 있습니다.

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
### Webhook의 서명 검증

안전한 webhook을 사용하기 위해 [Stripe's webhook signatures](https://stripe.com/docs/webhooks/signatures)를 사용할 수 있습니다. 캐셔에는 유입되는 Stripe webhook 요청이 유효한지 자동으로 확인하는 미들웨어를 포함하고 있습니다.

웹훅 확인을 활성화하려면 `STRIPE_WEBHOOK_SECRET` 환경 변수가 애플리케이션의 `.env` 파일에 설정되어 있는지 확인하세요. 웹훅 `secret`은 Stripe 계정 대시보드에서 조회할 수 있습니다.

<a name="single-charges"></a>
## 한번만 결제하기

<a name="simple-charge"></a>
### 간단한 결제

>> {note} `charge` 메소드는 사용하는 통화 중 가장 낮은 청구하고자 하는 가장 낮은 금액을 허용합니다. 예를 들어, 미국 달러를 사용할 때, 금액은 센트로 명시되어야 합니다.

고객에 대해 일회성 청구를 하려면 청구 가능한 모델 인스턴스에서 `charge` 메서드를 사용할 수 있습니다. `charge` 메서드에 대한 두 번째 인수로 [결제 수단 식별자 제공](#payment-methods-for-single-charges)이 필요합니다.

    use Illuminate\Http\Request;

    Route::post('/purchase', function (Request $request) {
        $stripeCharge = $request->user()->charge(
            100, $request->paymentMethodId
        );

        // ...
    });

`charge` 메서드는 배열을 세 번째 인수로 받아 기본 Stripe 요금 생성에 원하는 옵션을 전달할 수 있습니다. 요금 생성 시 사용할 수 있는 옵션에 대한 자세한 내용은 [Stripe 문서](https://stripe.com/docs/api/charges/create) 에서 확인할 수 있습니다.

    $user->charge(100, $paymentMethod, [
        'custom_option' => $value,
    ]);

기본 고객이나 사용자 없이 `charge` 메서드를 사용할 수도 있습니다. 이를 수행하려면 애플리케이션 청구 가능 모델의 새 인스턴스에서 `charge` 메서드를 호출합니다.

    use App\Models\User;

    $stripeCharge = (new User)->charge(100, $paymentMethod);

충전이 실패하면 `charge` 메소드에서 예외가 발생합니다. 청구가 성공하면 `Laravel\Cashier\Payment` 인스턴스가 메소드에서 리턴됩니다.

    try {
        $payment = $user->charge(100, $paymentMethod);
    } catch (Exception $e) {
        //
    }

<a name="charge-with-invoice"></a>
### 결제와 청구서

때로는 일회성 비용을 지불하고 고객에게 PDF 영수증을 제공해야 할 수도 있습니다. `invoicePrice` 메서드를 사용하면 그렇게 할 수 있습니다. 예를 들어 고객에게 새 셔츠 5벌에 대해 송장을 발행한다고 가정해 보겠습니다.

    $user->invoicePrice('price_tshirt', 5);

청구서는 사용자의 기본 결제 방법으로 즉시 청구됩니다. `invoicePrice` 메서드는 세 번째 인수로 배열도 입력받습니다. 이 배열에는 송장 항목에 대한 청구 옵션이 포함됩니다. 메서드에서 허용하는 네 번째 인수는 송장 자체에 대한 청구 옵션을 포함해야 하는 배열이기도 합니다.

    $user->invoicePrice('price_tshirt', 5, [
        'discounts' => [
            ['coupon' => 'SUMMER21SALE']
        ],
    ], [
        'default_tax_rates' => ['txr_id'],
    ]);

또는 `invoiceFor` 메소드를 사용하여 고객의 기본 결제 수단에 대해 "일회성" 요금을 청구할 수 있습니다.

    $user->invoiceFor('One Time Fee', 500);

`invoiceFor` 메서드를 사용할 수 있지만 미리 정의된 가격과 함께 `invoicePrice` 메서드를 사용하는 것이 좋습니다. 이렇게 하면 제품별로 판매와 관련하여 Stripe 대시보드 내에서 더 나은 분석 및 데이터에 액세스할 수 있습니다.

> {note} `invoicePrice` 및 `invoiceFor` 메소드는 실패한 청구 시도를 재시도하는 Stripe 청구서를 생성합니다. 청구서가 실패한 청구를 재시도하는 것을 원하지 않으면 첫 번째 실패한 청구 후 Stripe API를 사용하여 청구서를 마감해야 합니다.

<a name="refunding-charges"></a>
### 환불 수수료

Stripe 요금을 환불해야 하는 경우 `refund` 메서드를 사용할 수 있습니다. 이 메서드는 첫 번째 인수로 Stripe [결제 의도 ID](#payment-methods-for-single-charges)를 입력받습니다.

    $payment = $user->charge(100, $paymentMethodId);

    $user->refund($payment->id);

<a name="invoices"></a>
## 청구서

<a name="retrieving-invoices"></a>
### 청구서 조회

`invoices` 메소드를 사용하여 청구가 가능한 모델의 청구서를 손쉽게 배열 형태로 조회 할 수 있습니다 해당 메소드는 `Laravel\Cashier\Invoice` 인스턴스를 반환 합니다.

    $invoices = $user->invoices();

결과에 보류 중인 인보이스를 포함하려면 `invoicesInducingPending` 메서드를 사용할 수 있습니다.

    $invoices = $user->invoicesIncludingPending();

`findInvoice` 메소드를 사용하여 ID로 특정 인보이스를 검색할 수 있습니다.

    $invoice = $user->findInvoice($invoiceId);

<a name="displaying-invoice-information"></a>
#### 송장 정보 표시

고객에 대한 송장을 나열할 때 송장의 방법을 사용하여 관련 송장 정보를 표시할 수 있습니다. 예를 들어, 사용자가 쉽게 다운로드할 수 있도록 테이블에 모든 인보이스를 나열할 수 있습니다.

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
### 예정된 청구서들

`upcomingInvoice` 메소드를 사용하여 고객에게 예정된 청구서를 조회 할 수 있습니다.

    $invoice = $user->upcomingInvoice();

유사하게, 고객이 다양한 구독을 하고 있다면, 지정한 구독에 대해 예정된 청구서를 조회 할 수 있습니다.

    $invoice = $user->subscription('default')->upcomingInvoice();

<a name="previewing-subscription-invoices"></a>
### 구독 청구서 미리보기

`previewInvoice` 메소드를 사용하여, 가격이 변동되기 전의 청구서를 미리 볼 수 있습니다. 이것은 가격의 변동이 발생 시 고객의 청구서가 어떻게 보여지는지 확인하고 결정 할 수 있습니다.

    $invoice = $user->subscription('default')->previewInvoice('price_yearly');

`previewInvoice` 메소드에 가격 배열을 전달하여 새롭게 만들어진 가격들이 포함된 청구서를 미리 볼 수 있습니디:

    $invoice = $user->subscription('default')->previewInvoice(['price_yearly', 'price_metered']);

<a name="generating-invoice-pdfs"></a>
### 청구서 PDF 로 생성하기

경로 또는 컨트롤러 내에서 `downloadInvoice` 메소드를 사용하여 주어진 송장의 PDF 다운로드를 생성할 수 있습니다. 이 방법은 인보이스를 다운로드하는 데 필요한 적절한 HTTP 응답을 자동으로 생성합니다.

    use Illuminate\Http\Request;

    Route::get('/user/invoice/{invoice}', function (Request $request, $invoiceId) {
        return $request->user()->downloadInvoice($invoiceId, [
            'vendor' => 'Your Company',
            'product' => 'Your Product',
        ]);
    });

기본적으로 송장의 모든 데이터는 Stripe에 저장된 고객 및 송장 데이터에서 파생됩니다. 그러나 `downloadInvoice` 메소드에 대한 두 번째 인수로 배열을 제공하여 이 데이터 중 일부를 사용자 정의할 수 있습니다. 이 어레이를 사용하면 회사 및 제품 세부 정보와 같은 정보를 사용자 정의할 수 있습니다.

    return $request->user()->downloadInvoice($invoiceId, [
        'vendor' => 'Your Company',
        'product' => 'Your Product',
        'street' => 'Main Str. 1',
        'location' => '2000 Antwerp, Belgium',
        'phone' => '+32 499 00 00 00',
        'email' => 'info@example.com',
        'url' => 'https://example.com',
        'vendorVat' => 'BE123456789',
    ], 'my-invoice');

`downloadInvoice` 메소드는 또한 세 번째 인수를 통해 사용자 정의 파일 이름을 입력받습니다. 이 파일 이름에는 자동으로 `.pdf`가 붙습니다.

    return $request->user()->downloadInvoice($invoiceId, [], 'my-invoice');

<a name="checkout"></a>
## 결제

Cashier Stripe 또한 [Stripe Checkout](https://stripe.com/payments/checkout) 를 지원합니다. Stripe Checkout 은 사전 구축된 호스팅된 결제 페이지를 제공하여 결제를 수락하기 위한 사용자 지정 페이지 구현 난이도를 줄여줍니다.

다음 문서에는 Stripe Checkout 과 Cashier를 함께 사용하기 위한 방법 및 정보가 포함되어있습니다. Stripe Checkout에 대한 더 많은 정보를 얻기 위해서는 [Stripe의 결제 문서](https://stripe.com/docs/payments/checkout) 를 확인 해야 합니다.

<a name="product-checkouts"></a>
### 상품 결제

billable 모델의 `checkout` 메소드를 사용하여 Stripe 대시보드에 있는 제품의 결제가 가능합니다. `checkout` 메소드는 새로운 Stripe 결제 세션을 초기화 합니다. 기본적으로, Stripe Price ID를 필요로 합니다.

    use Illuminate\Http\Request;

    Route::get('/product-checkout', function (Request $request) {
        return $request->user()->checkout('price_tshirt');
    });

상품 개수를 지정하고싶다면 하단과 같이 지정 할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/product-checkout', function (Request $request) {
        return $request->user()->checkout(['price_tshirt' => 15]);
    });

고객이 해당 경로로 접근 시 Stripe의 결제 페이지로 이동될껍니다. 기본적으로, 유저가 "구매"를 성공적으로 취소 또는 성공 시 라우터의 `home` 으로 이동 될 것 입니다, 그러나 사용자가 지정한 callback URL이 있다면, `success_url` 그리고 `cancel_url` 옵션을 지정해주어야 합니다.

    use Illuminate\Http\Request;

    Route::get('/product-checkout', function (Request $request) {
        return $request->user()->checkout(['price_tshirt' => 1], [
            'success_url' => route('your-success-route'),
            'cancel_url' => route('your-cancel-route'),
        ]);
    });

`success_url` 결제 옵션을 지정핬을 시, URL에 요청 시 Stripe에 checkout session ID를 쿼리스트링 파라미터를 추가하도록 요청 할 수 잇습니다. `{CHECKOUT_SESSION_ID}` 문자열을 `success_url`의 쿼리스트링으로 추가합니다. Stripe는 플레이스 홀더를 실제 checkout session ID로 치환해줍니다.

    use Illuminate\Http\Request;
    use Stripe\Checkout\Session;
    use Stripe\Customer;

    Route::get('/product-checkout', function (Request $request) {
        return $request->user()->checkout(['price_tshirt' => 1], [
            'success_url' => route('checkout-success') . '?session_id={CHECKOUT_SESSION_ID}',
            'cancel_url' => route('checkout-cancel'),
        ]);
    });

    Route::get('/checkout-success', function (Request $request) {
        $checkoutSession = $request->user()->stripe()->checkout->sessions->retrieve($request->get('session_id'));

        return view('checkout.success', ['checkoutSession' => $checkoutSession]);
    })->name('checkout-success');

<a name="checkout-promotion-codes"></a>
#### 프로모션 코드들

기본적으로, Stripe Checkout [유저가 무작위로 지정하는 프로모션 코드](https://stripe.com/docs/billing/subscriptions/discounts/codes) 를 허가하지 않습니다. 다행이도, Checkout 페이지 에서 쉽게 허용할 수 있습니다. 그렇게 하기 위해서 `allowPromotionCodes` 메소드를 호출하여 사용해보세요:

    use Illuminate\Http\Request;

    Route::get('/product-checkout', function (Request $request) {
        return $request->user()
            ->allowPromotionCodes()
            ->checkout('price_tshirt');
    });

<a name="single-charge-checkouts"></a>
### 단일 결제

스트라이프 대시보드에서 생성되지 않은 애드혹 제품에 대해 간단한 충전을 수행할 수도 있습니다. billable 모델에서 `checkoutCharge` 메소드를 통하여 충전 가능금액, 제품이름, 그리고 제품 수량을 전달 할 수 있습니다. 손님이 해당 경로를 접근 시 will be redirected to Stripe의 결제 페이지로 이동 할 것 입니다.

    use Illuminate\Http\Request;

    Route::get('/charge-checkout', function (Request $request) {
        return $request->user()->checkoutCharge(1200, 'T-Shirt', 5);
    });

> {note} `checkoutCharge` 메소드 사용시, Stripe는 항상 Stripe 대시보드에서 새로운 상품을 생성합니다. 그러므로, 우리는 Stripe dashboard 프론트에서 상품을 생성하고 `checkout` 메소드를 대신 사용하기를 추천합니다.

<a name="subscription-checkouts"></a>
### 구독 결제

> {note} 구독에 스트라이프 체크아웃을 사용하려면 `customer.subscription.created` webhook을 Stripe dashboard에서 활성화 해야합니다. 이 webhook은 구독을 만들고, 데이터베이스에 모든 구독 관련 항목을 다 저장합니다.

Stripe Checkout을 사용하여 구독등록을 할 수 있습니다. Cashier의 구독 메소드를 정의 한 이후 `checkout` 메소드를 호출 할 것입니다. 고객이 해당 경로 방문 시 Stripe의 결제페이지로 이동 할 것 입니다.

    use Illuminate\Http\Request;

    Route::get('/subscription-checkout', function (Request $request) {
        return $request->user()
            ->newSubscription('default', 'price_monthly')
            ->checkout();
    });

제품 checkouts과 마찬가지로, 성공, 취소 URL을 지정하여 사용할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/subscription-checkout', function (Request $request) {
        return $request->user()
            ->newSubscription('default', 'price_monthly')
            ->checkout([
                'success_url' => route('your-success-route'),
                'cancel_url' => route('your-cancel-route'),
            ]);
    });

물론, 구독결제에 대한 프로모션 코드도 설정 할 수 있습니다.

    use Illuminate\Http\Request;

    Route::get('/subscription-checkout', function (Request $request) {
        return $request->user()
            ->newSubscription('default', 'price_monthly')
            ->allowPromotionCodes()
            ->checkout();
    });

> {note} 아쉽게도 Stripe Checkout은 구독 시작 시 모든 구독결제 옵션을 지원하지 않습니다. 구독 빌더에서 `anchorBillingCycleOn` 메소드 사용 시, Stripe Checkout 세션에서 정렬 설정 또는 결제 동작 중 영향을 미치지 않습니다. [Stripe Checkout 세션 API 문서](https://stripe.com/docs/api/checkout/sessions/create)문서중 어떤 파라미터 가능한지 확인 바랍니다.

<a name="stripe-checkout-trial-periods"></a>
#### Stripe 결제 & 평가판 기간

물론 Stripe Checkout을 사용하여 완료할 구독을 작성할 때 평가판 기간을 정의할 수 있습니다.

    $checkout = Auth::user()->newSubscription('default', 'price_monthly')
        ->trialDays(3)
        ->checkout();

그러나 평가판 사용 기간은 Stripe Checkout 에서 지원하는 최소 평가판 사용 시간인 48시간 이상이어야 합니다.

<a name="stripe-checkout-subscriptions-and-webhooks"></a>
#### 구독 & Webhooks

Remember, Stripe 와 캐셔는 webhook을 통해 구독 상태를 업데이트 하므로, 고객이 결제 정보를 입력한 후 어플리케이션으로 돌아갈 때 구독이 아직 활성화되지 않았을 수 있습니다. 이 시나리오를 처리하기 위해서, 사용자에게 지불 또는 구독이 보류 중임을 알리는 메시지를 표시할 수 있습니다.

<a name="collecting-tax-ids"></a>
### Tax ID 수집

체크아웃은 또한 고객의 세금 ID를 수집할 수 있도록 지원합니다. Checkout 세션에서 이 옵션을 사용하려면 세션을 만들 때 `collectTaxIds` 메서드를 호출합니다.

    $checkout = $user->collectTaxIds()->checkout('price_tshirt');

이 메서드가 실행되면 고객이 회사로서 구매하는지 여부를 표시할 수 있는 새 체크박스가 고객에게 제공됩니다, 그리고 Tax ID를 제공할 수 있는 기회가 주어집니다.

> {note} 이미 [자동 세금 수집](#tax-configuration) 세팅을 어플리케이션에서 설정했다면 해당 기능은 자동으로 활성화 되며, `TaxIds` 메소드를 호출 할 필요가 없습니다.

<a name="handling-failed-payments"></a>
## 결제 실패 처리

때때로 구독 또는 단일 청구에 대한 결제가 실패할 수 있습니다. 이런 일이 발생하면 캐셔는 `Laravel\Cashier\Exceptions\IncompletePayment` 예외를 발생시켜 이러한 일이 발생했음을 알려줍니다. 이 예외를 포착한 후 진행 방법에 대한 두 가지 옵션이 있습니다.

먼저, 캐셔에 포함된 전용 결제 확인 페이지로 고객을 리디렉션할 수 있습니다. 이 페이지에는 캐셔의 서비스 프로바이더를 통해 등록된 연결된 이름이 지정된 경로가 이미 있습니다. 따라서 `IncompletePayment` 예외를 포착하고 사용자를 결제 확인 페이지로 리디렉션할 수 있습니다.

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

결제 확인 페이지에서 고객은 신용 카드 정보를 다시 입력하고 "3D 보안" 확인과 같이 Stripe에서 요구하는 추가 작업을 수행하라는 메시지가 표시됩니다. 지불을 확인한 후 사용자는 위에 지정된 `redirect` 매개변수가 제공한 URL로 리디렉션됩니다. 리디렉션 시 `message`(문자열) 및 `success`(정수) 쿼리 문자열 변수가 URL에 추가됩니다. 결제 페이지는 현재 다음 결제 수단 유형을 지원합니다.

- Credit Cards
- Alipay
- Bancontact
- BECS Direct Debit
- EPS
- Giropay
- iDEAL
- SEPA Direct Debit

또는 Stripe에서 결제 확인을 처리하도록 허용할 수 있습니다. 이 경우 결제 확인 페이지로 리디렉션하는 대신 Stripe 대시보드에서 [Stripe의 자동 청구 이메일 설정](https://dashboard.stripe.com/account/billing/automatic) 을 할 수 있습니다. 그러나 `IncompletePayment` 예외가 발생하는 경우에도 사용자에게 추가 결제 확인 지침이 포함된 이메일을 받을 것임을 알려야 합니다.

`Billable` 특성-trait을 사용하는 모델의 경우 `charge`, `invoiceFor` 및 `invoice` 메서드에 대해 결제 예외가 발생할 수 있습니다. 구독과 상호 작용할 때 `SubscriptionBuilder`의 `create` 메서드와 `Subscription` 및 `SubscriptionItem` 모델의 `incrementAndInvoice` 및 `swapAndInvoice` 메서드에서 불완전한 결제 예외가 발생할 수 있습니다.

기존 구독에 불완전한 결제가 있는지 확인하는 것은 청구 가능한 모델 또는 구독 인스턴스에서 `hasIncompletePayment` 메서드를 사용하여 수행할 수 있습니다.

    if ($user->hasIncompletePayment('default')) {
        //
    }

    if ($user->subscription('default')->hasIncompletePayment()) {
        //
    }

예외 인스턴스의 `payment` 속성을 검사하여 불완전한 결제의 특정 상태를 확인할 수 있습니다.

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
## 강력한 고객 인증

If your business or one of your customers is based in Europe you will need to abide by the EU's Strong Customer Authentication (SCA) regulations. These regulations were imposed in September 2019 by the European Union to prevent payment fraud. Luckily, Stripe and Cashier are prepared for building SCA compliant applications.

귀하의 비즈니스 또는 고객 중 하나가 유럽에 기반을 두고 있는 경우 EU의 강력한 고객 인증(SCA) 규정을 준수해야 합니다. 이러한 규정은 결제 사기를 방지하기 위해 유럽 연합에서 2019년 9월에 부과되었습니다. 다행히 Stripe와 Cashier는 SCA 호환 응용 프로그램을 구축할 준비가 되어 있습니다.

> {note} 시작하기 전에 [PS2 및 SCA에 대한 Stripe 안내서](https://stripe.com/guides/strong-customer-authentication) 와 [새로운 SCA API에 대한 문서](https://stripe.com/docs/strong-customer-authentication) 를 검토하십시오.

<a name="payments-requiring-additional-confirmation"></a>
### 추가 확인이 필요한 결제

SCA 규정은 종종 지불을 확인하고 처리하기 위해 추가 확인을 요구합니다. 이런 일이 발생하면 Cashier는 추가 확인이 필요함을 알리는 `Laravel\Cashier\Exceptions\IncompletePayment` 예외를 발생시킵니다. 이러한 예외를 처리하는 방법에 대한 자세한 내용은 [결제 실패 처리](#handling-failed-payments) 문서에서 찾을 수 있습니다.

Stripe 또는 캐셔가 제공하는 결제 확인 화면은 특정 은행 또는 카드 발급사의 결제 흐름에 맞게 조정될 수 있으며 추가 카드 확인, 일시적인 소액 청구, 별도의 장치 인증 또는 기타 형식의 확인이 포함될 수 있습니다.

<a name="incomplete-and-past-due-state"></a>
#### 미완료 및 기한 만료 상태

결제에 추가 확인이 필요한 경우 구독은 `stripe_status` 데이터베이스 열에 표시된 대로 `미완료` 또는 `지난 기한` 상태로 유지됩니다. 지불 확인이 완료되고 귀하의 신청서가 웹훅을 통해 Stripe에 의해 완료되었다는 알림을 받는 즉시 Cashier는 고객의 구독을 자동으로 활성화합니다.

`incomplete` 및 `past_due` 상태에 대한 자세한 내용은 [이 상태에 대한 추가 문서](#incomplete-and-past-due-status)를 참조하세요.

<a name="off-session-payment-notifications"></a>
### 세션 외 결제 알림

SCA 규정에 따라 고객은 구독이 활성화되어 있는 동안에도 때때로 지불 세부 정보를 확인해야 하므로, 캐셔는 세션 외 지불 확인이 필요할 때 고객에게 알림을 보낼 수 있습니다. 예를 들어, 구독이 갱신될 때 발생할 수 있습니다. 캐셔의 지불 알림은 `CASHIER_PAYMENT_NOTIFICATION` 환경 변수를 알림 클래스로 설정하여 활성화할 수 있습니다. 기본적으로 이 알림은 비활성화되어 있습니다. 물론 캐셔에는 이 목적으로 사용할 수 있는 알림 클래스가 포함되어 있지만 원하는 경우 고유한 알림 클래스를 자유롭게 제공할 수 있습니다.

    CASHIER_PAYMENT_NOTIFICATION=Laravel\Cashier\Notifications\ConfirmPayment

세션 외 결제 확인 알림이 전달되도록하려면 애플리케이션에 대한 [Stripe webhooks 구성](#handling-stripe-webhooks) 및 Stripe 대시 보드에서 `invoice.payment_action_required` webhook이 활성화되어 있는지 확인하십시오. 또한 `Billable` 모델은 Laravel의 `Illuminate\Notifications\Notifiable` trait도 사용해야합니다.

> {note} 고객이 추가 확인이 필요한 수동 결제를 하는 경우에도 알림이 전송됩니다. 불행히도 Stripe이 지불이 수동 또는 "세션 외"로 완료되었음을 알 수있는 방법은 없습니다. 그러나 고객은 이미 결제를 확인한 후 결제 페이지를 방문하면 "결제 성공"메시지를 보게됩니다. 고객은 실수로 동일한 결제를 두 번 확인하고 실수로 두 번째 청구를 할 수 없습니다.

<a name="stripe-sdk"></a>
## Stripe SDK

Cashier의 많은 객체는 Stripe SDK 객체를 감싸는 래퍼입니다. Stripe 객체와 직접 상호 작용하려면 `asStripe` 메소드를 사용하여 편리하게 조회 할 수 있습니다.

    $stripeSubscription = $subscription->asStripeSubscription();

    $stripeSubscription->application_fee_percent = 5;

    $stripeSubscription->save();

`updateStripeSubscription` 메서드를 사용하여 Stripe 구독을 직접 업데이트 할 수도 있습니다.

    $subscription->updateStripeSubscription(['application_fee_percent' => 5]);

`Stripe\StripeClient` 클라이언트를 직접 호출 하는 경우 `Cashier` 클래스에 `stripe` 메소드를 호출합니다. 예를 들어 Stripe 계정에 기격들을 `StripeClient` 인스턴스를 이용하여 조회 해야 하는 경우 해당 메소드를 사용합니다.

    use Laravel\Cashier\Cashier;

    $prices = Cashier::stripe()->prices->all();

<a name="testing"></a>
## 테스팅

캐셔를 사용하는 애플리케이션을 테스트 할 때 실제 HTTP 요청을 Stripe API로 모킹 할 수 있습니다. 그러나 이를 위해서는 캐셔 자신의 행동을 부분적으로 다시 구현해야합니다. 따라서 테스트가 실제 Stripe API에 도달하도록 허용하는 것이 좋습니다. 속도는 느리지만 애플리케이션이 예상대로 작동하고 있으며 느린 테스트는 자체 PHPUnit 테스트 그룹 내에 배치 될 수 있습니다.

테스트할 때 캐셔 자체에는 이미 훌륭한 테스트 제품군이 있으므로, 모든 기본 캐셔 동작이 아니라 자체 애플리케이션의 구독 및 지불 흐름을 테스트하는 데 집중해야 합니다.

시작하려면, **testing** 버전의 Stripe secret을 `phpunit.xml` 파일에 추가하십시오.

    <env name="STRIPE_SECRET" value="sk_test_<your-key>"/>

이제 테스트하는 동안 캐셔와 상호 작용할 때마다 실제 API 요청을 Stripe 테스트 환경으로 보냅니다. 편의를 위해 테스트 중에 사용할 수 있는 구독 가격으로 Stripe 테스트 계정을 미리 등록해둬야 합니다.

> {tip} 신용 카드 거부 및 실패와 같은 다양한 결제 시나리오를 테스트하기 위해 Stripe가 제공하는 광범위한 [테스트 카드 번호 및 토큰](https://stripe.com/docs/testing) 을 사용할 수 있습니다.
