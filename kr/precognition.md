# Precognition

- [Precognition](#precognition)
  - [Introduction](#introduction)
  - [소개](#소개)
  - [Live Validation](#live-validation)
  - [실시간 유효성 검사](#실시간-유효성-검사)
    - [Using Vue](#using-vue)
    - [Vue 사용](#vue-사용)
    - [Using Vue \& Inertia](#using-vue--inertia)
    - [Vue \& Inertia 사용](#vue--inertia-사용)
    - [Using React](#using-react)
    - [리액트 사용](#리액트-사용)
    - [Using React \& Inertia](#using-react--inertia)
    - [리액트와 Inertia 사용](#리액트와-inertia-사용)
    - [Using Alpine \& Blade](#using-alpine--blade)
    - [Alpine \& Blade 사용](#alpine--blade-사용)
      - [Repopulating Old Form Data](#repopulating-old-form-data)
      - [이전 양식 데이터 다시 채우기](#이전-양식-데이터-다시-채우기)
    - [Configuring Axios](#configuring-axios)
    - [Axios 구성](#axios-구성)
  - [Customizing Validation Rules](#customizing-validation-rules)
  - [유효성 검사 규칙 사용자 정의](#유효성-검사-규칙-사용자-정의)
  - [Handling File Uploads](#handling-file-uploads)
  - [파일 업로드 처리](#파일-업로드-처리)
  - [Managing Side-Effects](#managing-side-effects)
  - [부작용 관리](#부작용-관리)

<a name="introduction"></a>
## Introduction
## 소개

Laravel Precognition allows you to anticipate the outcome of a future HTTP request. One of the primary use cases of Precognition is the ability to provide "live" validation for your frontend JavaScript application without having to duplicate your application's backend validation rules. Precognition pairs especially well with Laravel's Inertia-based [starter kits](/docs/{{version}}/starter-kits).

라라벨 Precognition은 미래의 HTTP 요청 결과를 예측할 수 있게 해줍니다. Precognition의 주요 사용 사례 중 하나는 애플리케이션의 백엔드 유효성 검사 규칙을 복제하지 않고 프론트엔드 JavaScript 애플리케이션에 "실시간" 유효성 검사를 제공할 수 있습니다. Precognition은 특히 라라벨의 Inertia 기반 [스타터 키트](/docs/{{version}}/starter-kits)와 잘 어울립니다.

When Laravel receives a "precognitive request", it will execute all of the route's middleware and resolve the route's controller dependencies, including validating [form requests](/docs/{{version}}/validation#form-request-validation) - but it will not actually execute the route's controller method.

라라벨이 "예지 요청"을 받으면 라우트의 모든 미들웨어를 실행하고 라우트의 컨트롤러 종속성을 해결합니다. 이때 [form requests](/docs/{{version}}/validation#form-request-validation)를 유효성 검사하지만 실제로 라우트의 컨트롤러 메서드를 실행하지는 않습니다.

<a name="live-validation"></a>
## Live Validation
## 실시간 유효성 검사

<a name="using-vue"></a>
### Using Vue
### Vue 사용

Using Laravel Precognition, you can offer live validation experiences to your users without having to duplicate your validation rules in your frontend Vue application. To illustrate how it works, let's build a form for creating new users within our application.

라라벨 Precognition을 사용하면 프론트엔드 Vue 애플리케이션에서 유효성 검사 규칙을 복제하지 않고 사용자에게 실시간 유효성 검사 환경을 제공할 수 있습니다. 작동 방식을 설명하기 위해 애플리케이션 내에서 새 사용자를 만드는 양식을 만들어 보겠습니다.

First, to enable Precognition for a route, the `HandlePrecognitiveRequests` middleware should be added to the route definition. You should also create a [form request](/docs/{{version}}/validation#form-request-validation) to house the route's validation rules:

먼저 라우트에 Precognition을 활성화하려면 `HandlePrecognitiveRequests` 미들웨어를 라우트 정의에 추가해야 합니다. 또한 라우트의 유효성 검사 규칙을 담을 [form request](/docs/{{version}}/validation#form-request-validation)를 만들어야 합니다.

```php
use App\Http\Requests\StoreUserRequest;
use Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests;

Route::post('/users', function (StoreUserRequest $request) {
    // ...
})->middleware([HandlePrecognitiveRequests::class]);
```

Next, you should install the Laravel Precognition frontend helpers for Vue via NPM:

다음으로 NPM을 통해 Vue용 라라벨 Precognition 프론트엔드 헬퍼를 설치해야 합니다.

```shell
npm install laravel-precognition-vue
```

With the Laravel Precognition package installed, you can now create a form object using Precognition's `useForm` function, providing the HTTP method (`post`), the target URL (`/users`), and the initial form data.

라라벨 Precognition 패키지를 설치하면 Precognition의 `useForm` 함수를 사용하여 폼 객체를 만들 수 있습니다. HTTP 메서드 (`post`), 대상 URL (`/users`) 및 초기 폼 데이터를 제공합니다.

Then, to enable live validation, invoke the form's `validate` method on each input's `change` event, providing the input's name:

그런 다음 실시간 유효성 검사를 활성화하려면 각 입력의 `change` 이벤트에서 입력의 이름을 제공하여 폼의 `validate` 메서드를 호출합니다:

```vue
<script setup>
import { useForm } from 'laravel-precognition-vue';

const form = useForm('post', '/users', {
    name: '',
    email: '',
});

const submit = () => form.submit();
</script>

<template>
    <form @submit.prevent="submit">
        <label for="name">Name</label>
        <input
            id="name"
            v-model="form.name"
            @change="form.validate('name')"
        />
        <div v-if="form.invalid('name')">
            {{ form.errors.name }}
        </div>

        <label for="email">Email</label>
        <input
            id="email"
            type="email"
            v-model="form.email"
            @change="form.validate('email')"
        />
        <div v-if="form.invalid('email')">
            {{ form.errors.email }}
        </div>

        <button>Create User</button>
    </form>
</template>
```

Now, as the form is filled by the user, Precognition will provide live validation output powered by the validation rules in the route's form request. When the form's inputs are changed, a debounced "precognitive" validation request will be sent to your Laravel application. You may configure the debounce timeout by calling the form's `setValidationTimeout` function:

이제 사용자가 양식을 작성하면 Precognition은 라우트의 form request의 유효성 검사 규칙을 기반으로 하는 실시간 유효성 검사 출력을 제공합니다. 양식의 입력이 변경되면 디바운스된 "예지" 유효성 검사 요청이 라라벨 애플리케이션으로 전송됩니다. 폼의 `setValidationTimeout` 함수를 호출하여 디바운스 시간 초과를 구성할 수 있습니다:

```js
form.setValidationTimeout(3000);
```

When a validation request is in-flight, the form's `validating` property will be `true`:

유효성 검사 요청이 전송되면 폼의 `validating` 속성이 `true`가 됩니다:

```html
<div v-if="form.validating">
    Validating...
</div>
```

Any validation errors returned during a validation request or a form submission will automatically populate the form's `errors` object:

유효성 검사 요청 또는 양식 제출 중에 반환된 모든 유효성 검사 오류는 자동으로 폼의 `errors` 객체에 채워집니다:

```html
<div v-if="form.invalid('email')">
    {{ form.errors.email }}
</div>
```

You can determine if the form has any errors using the form's `hasErrors` property:

폼에 오류가 있는지 여부는 폼의 `hasErrors` 속성을 사용하여 확인할 수 있습니다:

```html
<div v-if="form.hasErrors">
    <!-- ... -->
</div>
```

You may also determine if an input has passed or failed validation by passing the input's name to the form's `valid` and `invalid` functions, respectively:

또한 각각 입력의 이름을 폼의 `valid` 및 `invalid` 함수에 전달하여 입력이 유효성 검사를 통과했는지 실패했는지 여부를 확인할 수도 있습니다:

```html
<span v-if="form.valid('email')">
    ✅
</span>

<span v-else-if="form.invalid('email')">
    ❌
</span>
```

> **Warning**
> A form input will only appear as valid or invalid once it has changed and a validation response has been received.

> **주의**
> 폼 입력은 변경되고 유효성 검사 응답이 수신된 경우에만 유효하거나 유효하지 않은 것으로 표시됩니다.

If you are validating a subset of a form's inputs with Precognition, it can be useful to manually clear errors. You may use the form's `forgetError` function to achieve this:

Precognition을 사용하여 양식의 일부 입력을 유효성 검사하는 경우 오류를 수동으로 지우는 것이 유용할 수 있습니다. 폼의 `forgetError` 함수를 사용하여 이를 수행할 수 있습니다:

```html
<input
    id="avatar"
    type="file"
    @change="(e) => {
        form.avatar = e.target.files[0]

        form.forgetError('avatar')
    }"
>
```

Of course, you may also execute code in reaction to the response to the form submission. The form's `submit` function returns an Axios request promise. This provides a convenient way to access the response payload, reset the form inputs on successful submission, or handle a failed request:

물론 폼 제출에 대한 응답에 반응하여 코드를 실행할 수도 있습니다. 폼의 `submit` 함수는 Axios 요청 프로미스를 반환합니다. 이를 통해 응답 페이로드에 액세스하거나 성공적인 폼 제출에 대한 폼의 입력을 재설정하거나 실패한 요청을 처리하는 편리한 방법을 제공합니다:

```js
const submit = () => form.submit()
    .then(response => {
        form.reset();

        alert('User created.');
    })
    .catch(error => {
        alert('An error occurred.');
    });
```

<a name="using-vue-and-inertia"></a>
### Using Vue & Inertia
### Vue & Inertia 사용

> **Note**
> If you would like a head start when developing your Laravel application with Vue and Inertia, consider using one of our [starter kits](/docs/{{version}}/starter-kits). Laravel's starter kits provide backend and frontend authentication scaffolding for your new Laravel application.

> **참고**
> Vue와 Inertia를 사용하여 라라벨 애플리케이션을 개발할 때 미리 시작하고 싶다면 [스타터 키트](/docs/{{version}}/starter-kits) 중 하나를 사용하는 것을 고려하세요. 라라벨의 스타터 키트는 새로운 라라벨 애플리케이션을 위한 백엔드 및 프론트엔드 인증 스캐폴딩을 제공합니다.

Before using Precognition with Vue and Inertia, be sure to review our general documentation on [using Precognition with Vue](#using-vue). When using Vue with Inertia, you will need to install the Inertia compatible Precognition library via NPM:

Vue와 Inertia를 사용하기 전에 [Vue와 함께 Precognition 사용](#using-vue)에 대한 일반적인 문서를 반드시 검토하세요. Inertia를 사용할 때는 NPM을 통해 Inertia 호환 Precognition 라이브러리를 설치해야 합니다:

```shell
npm install laravel-precognition-vue-inertia
```

Once installed, Precognition's `useForm` function will return an Inertia [form helper](https://inertiajs.com/forms#form-helper) augmented with the validation features discussed above.

설치가 완료되면 Precognition의 '사용 양식' 함수는 위에서 설명한 유효성 검사 기능으로 보강된 Inertia [양식 헬퍼](https://inertiajs.com/forms#form-helper)를 반환합니다.

The form helper's `submit` method has been streamlined, removing the need to specify the HTTP method or URL. Instead, you may pass Inertia's [visit options](https://inertiajs.com/manual-visits) as the first and only argument. In addition, the `submit` method does not return a Promise as seen in the Vue example above. Instead, you may provide any of Inertia's supported [event callbacks](https://inertiajs.com/manual-visits#event-callbacks) in the visit options given to the `submit` method:

양식 헬퍼의 `submit` 메서드는 HTTP 메서드 또는 URL을 지정할 필요가 없도록 단순화되었습니다. 대신 첫 번째 인수로 Inertia의 [방문 옵션](https://inertiajs.com/manual-visits)을 전달할 수 있습니다. 또한 `submit` 메서드는 Vue 예제에서 본 것처럼 프로미스를 반환하지 않습니다. 대신 `submit` 메서드에 제공된 방문 옵션에 Inertia가 지원하는 [이벤트 콜백](https://inertiajs.com/manual-visits#event-callbacks) 중 하나를 제공할 수 있습니다:

```vue
<script setup>
import { useForm } from 'laravel-precognition-vue-inertia';

const form = useForm('post', '/users', {
    name: '',
    email: '',
});

const submit = () => form.submit({
    preserveScroll: true,
    onSuccess: () => form.reset(),
});
</script>
```

<a name="using-react"></a>
### Using React
### 리액트 사용

Using Laravel Precognition, you can offer live validation experiences to your users without having to duplicate your validation rules in your frontend React application. To illustrate how it works, let's build a form for creating new users within our application.

라라벨 Precognition을 사용하면 프론트엔드 React 애플리케이션에서 유효성 검사 규칙을 복제하지 않고 사용자에게 실시간 유효성 검사 환경을 제공할 수 있습니다. 작동 방식을 설명하기 위해 애플리케이션 내에서 새 사용자를 만드는 양식을 만들어 보겠습니다.

First, to enable Precognition for a route, the `HandlePrecognitiveRequests` middleware should be added to the route definition. You should also create a [form request](/docs/{{version}}/validation#form-request-validation) to house the route's validation rules:

먼저 라우트에 Precognition을 활성화하려면 `HandlePrecognitiveRequests` 미들웨어를 라우트 정의에 추가해야 합니다. 또한 라우트의 유효성 검사 규칙을 담을 [form request](/docs/{{version}}/validation#form-request-validation)를 만들어야 합니다:

```php
use App\Http\Requests\StoreUserRequest;
use Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests;

Route::post('/users', function (StoreUserRequest $request) {
    // ...
})->middleware([HandlePrecognitiveRequests::class]);
```

Next, you should install the Laravel Precognition frontend helpers for React via NPM:

다음으로 NPM을 통해 React용 라라벨 Precognition 프론트엔드 헬퍼를 설치해야 합니다:

```shell
npm install laravel-precognition-react
```

With the Laravel Precognition package installed, you can now create a form object using Precognition's `useForm` function, providing the HTTP method (`post`), the target URL (`/users`), and the initial form data.

라라벨 Precognition 패키지를 설치하면 Precognition의 `useForm` 함수를 사용하여 폼 객체를 만들 수 있습니다. HTTP 메서드 (`post`), 대상 URL (`/users`) 및 초기 폼 데이터를 제공합니다.

To enable live validation, you should listen to each input's `change` and `blur` event. In the `change` event handler, you should set the form's data with the `setData` function, passing the input's name and new value. Then, in the `blur` event handler invoke the form's `validate` method, providing the input's name:

실시간 유효성 검사를 활성화하려면 각 입력의 `change` 및 `blur` 이벤트를 수신해야 합니다. `change` 이벤트 핸들러에서는 입력의 이름과 새 값이 전달된 `setData` 함수를 사용하여 폼의 데이터를 설정해야 합니다. 그런 다음 `blur` 이벤트 핸들러에서 입력의 이름을 제공하여 폼의 `validate` 메서드를 호출합니다:

```jsx
import { useForm } from 'laravel-precognition-react';

export default function Form() {
    const form = useForm('post', '/users', {
        name: '',
        email: '',
    });

    const submit = (e) => {
        e.preventDefault();

        form.submit();
    };

    return (
        <form onSubmit={submit}>
            <label for="name">Name</label>
            <input
                id="name"
                value={form.data.name}
                onChange={(e) => form.setData('name', e.target.value)}
                onBlur={() => form.validate('name')}
            />
            {form.invalid('name') && <div>{form.errors.name}</div>}

            <label for="email">Email</label>
            <input
                id="email"
                value={form.data.email}
                onChange={(e) => form.setData('email', e.target.value)}
                onBlur={() => form.validate('email')}
            />
            {form.invalid('email') && <div>{form.errors.email}</div>}

            <button>Create User</button>
        </form>
    );
};
```

Now, as the form is filled by the user, Precognition will provide live validation output powered by the validation rules in the route's form request. When the form's inputs are changed, a debounced "precognitive" validation request will be sent to your Laravel application. You may configure the debounce timeout by calling the form's `setValidationTimeout` function:

이제 사용자가 양식을 작성하면 Precognition은 라우트의 form request의 유효성 검사 규칙을 기반으로 하는 실시간 유효성 검사 출력을 제공합니다. 양식의 입력이 변경되면 디바운스된 "예지" 유효성 검사 요청이 라라벨 애플리케이션으로 전송됩니다. 폼의 `setValidationTimeout` 함수를 호출하여 디바운스 시간 초과를 구성할 수 있습니다:

```js
form.setValidationTimeout(3000);
```

When a validation request is in-flight, the form's `validating` property will be `true`:

유효성 검사 요청이 전송되면 폼의 `validating` 속성이 `true`가 됩니다:

```jsx
{form.validating && <div>Validating...</div>}
```

Any validation errors returned during a validation request or a form submission will automatically populate the form's `errors` object:

유효성 검사 요청 또는 양식 제출 중에 반환된 모든 유효성 검사 오류는 자동으로 폼의 `errors` 객체에 채워집니다:

```jsx
{form.invalid('email') && <div>{form.errors.email}</div>}
```

You can determine if the form has any errors using the form's `hasErrors` property:

폼에 오류가 있는지 여부는 폼의 `hasErrors` 속성을 사용하여 확인할 수 있습니다:

```jsx
{form.hasErrors && <div><!-- ... --></div>}
```

You may also determine if an input has passed or failed validation by passing the input's name to the form's `valid` and `invalid` functions, respectively:

또한 각각 입력의 이름을 폼의 `valid` 및 `invalid` 함수에 전달하여 입력이 유효성 검사를 통과했는지 실패했는지 여부를 확인할 수도 있습니다:

```jsx
{form.valid('email') && <span>✅</span>}

{form.invalid('email') && <span>❌</span>}
```

> **Warning**
> A form input will only appear as valid or invalid once it has changed and a validation response has been received.

> **주의**
> 양식 입력은 변경되고 유효성 검사 응답이 수신된 경우에만 유효하거나 유효하지 않은 것으로 표시됩니다.

If you are validating a subset of a form's inputs with Precognition, it can be useful to manually clear errors. You may use the form's `forgetError` function to achieve this:

Precognition을 사용하여 양식의 일부 입력을 유효성 검사하는 경우 오류를 수동으로 지우는 것이 유용할 수 있습니다. 폼의 `forgetError` 함수를 사용하여 이를 수행할 수 있습니다:

```jsx
<input
    id="avatar"
    type="file"
    onChange={(e) => 
        form.setData('avatar', e.target.value);

        form.forgetError('avatar');
    }
>
```

Of course, you may also execute code in reaction to the response to the form submission. The form's `submit` function returns an Axios request promise. This provides a convenient way to access the response payload, reset the form's inputs on a successful form submission, or handle a failed request:

물론 폼 제출에 대한 응답에 반응하여 코드를 실행할 수도 있습니다. 폼의 `submit` 함수는 Axios 요청 프로미스를 반환합니다. 이를 통해 응답 페이로드에 액세스하거나 성공적인 폼 제출에 대한 폼의 입력을 재설정하거나 실패한 요청을 처리하는 편리한 방법을 제공합니다:

```js
const submit = (e) => {
    e.preventDefault();

    form.submit()
        .then(response => {
            form.reset();

            alert('User created.');
        })
        .catch(error => {
            alert('An error occurred.');
        });
};
```

<a name="using-react-and-inertia"></a>
### Using React & Inertia
### 리액트와 Inertia 사용

> **Note**
> If you would like a head start when developing your Laravel application with React and Inertia, consider using one of our [starter kits](/docs/{{version}}/starter-kits). Laravel's starter kits provide backend and frontend authentication scaffolding for your new Laravel application.

> **참고**
> 리액트와 Inertia로 라라벨 애플리케이션을 개발할 때 미리 시작하고 싶다면 [스타터 키트](/docs/{{버전}}/starter-kits) 중 하나를 사용하는 것을 고려하세요. 라라벨의 스타터 키트는 새로운 라라벨 애플리케이션을 위한 백엔드 및 프론트엔드 인증 스캐폴딩을 제공합니다.

Before using Precognition with React and Inertia, be sure to review our general documentation on [using Precognition with React](#using-react). When using React with Inertia, you will need to install the Inertia compatible Precognition library via NPM:

리액트와 Inertia를 사용하기 전에 [리액트와 함께 Precognition 사용](#using-react)에 대한 일반적인 문서를 반드시 검토하세요. Inertia를 사용할 때는 NPM을 통해 Inertia 호환 Precognition 라이브러리를 설치해야 합니다.

```shell
npm install laravel-precognition-react-inertia
```

Once installed, Precognition's `useForm` function will return an Inertia [form helper](https://inertiajs.com/forms#form-helper) augmented with the validation features discussed above.

설치가 완료되면 Precognition의 '사용 양식' 함수는 위에서 설명한 유효성 검사 기능으로 보강된 Inertia [양식 헬퍼](https://inertiajs.com/forms#form-helper)를 반환합니다.

The form helper's `submit` method has been streamlined, removing the need to specify the HTTP method or URL. Instead, you may pass Inertia's [visit options](https://inertiajs.com/manual-visits) as the first and only argument. In addition, the `submit` method does not return a Promise as seen in the React example above. Instead, you may provide any of Inertia's supported [event callbacks](https://inertiajs.com/manual-visits#event-callbacks) in the visit options given to the `submit` method:

양식 헬퍼의 `submit` 메서드는 HTTP 메서드 또는 URL을 지정할 필요가 없도록 단순화되었습니다. 대신 첫 번째 인수로 Inertia의 [방문 옵션](https://inertiajs.com/manual-visits)을 전달할 수 있습니다. 또한 `submit` 메서드는 리액트 예제에서 본 것처럼 프로미스를 반환하지 않습니다. 대신 `submit` 메서드에 제공된 방문 옵션에 Inertia가 지원하는 [이벤트 콜백](https://inertiajs.com/manual-visits#event-callbacks) 중 하나를 제공할 수 있습니다:

```js
import { useForm } from 'laravel-precognition-react-inertia';

const form = useForm('post', '/users', {
    name: '',
    email: '',
});

const submit = (e) => {
    e.preventDefault();

    form.submit({
        preserveScroll: true,
        onSuccess: () => form.reset(),
    });
};
```

<a name="using-alpine"></a>
### Using Alpine & Blade
### Alpine & Blade 사용

Using Laravel Precognition, you can offer live validation experiences to your users without having to duplicate your validation rules in your frontend Alpine application. To illustrate how it works, let's build a form for creating new users within our application.

라라벨 Precognition을 사용하면 프론트엔드 Alpine 애플리케이션에서 유효성 검사 규칙을 복제하지 않고 사용자에게 실시간 유효성 검사 환경을 제공할 수 있습니다. 작동 방식을 설명하기 위해 애플리케이션 내에서 새 사용자를 만드는 양식을 만들어 보겠습니다.

First, to enable Precognition for a route, the `HandlePrecognitiveRequests` middleware should be added to the route definition. You should also create a [form request](/docs/{{version}}/validation#form-request-validation) to house the route's validation rules:

먼저 라우트에 Precognition을 활성화하려면 `HandlePrecognitiveRequests` 미들웨어를 라우트 정의에 추가해야 합니다. 또한 라우트의 유효성 검사 규칙을 담을 [form request](/docs/{{version}}/validation#form-request-validation)를 만들어야 합니다:

```php
use App\Http\Requests\CreateUserRequest;
use Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests;

Route::post('/users', function (CreateUserRequest $request) {
    // ...
})->middleware([HandlePrecognitiveRequests::class]);
```

Next, you should install the Laravel Precognition frontend helpers for Alpine via NPM:

다음으로 NPM을 통해 Alpine용 라라벨 Precognition 프론트엔드 헬퍼를 설치해야 합니다:

```shell
npm install laravel-precognition-alpine
```

Then, register the Precognition plugin with Alpine in your `resources/js/app.js` file:

그런 다음 `resources/js/app.js` 파일에서 Alpine에 Precognition 플러그인을 등록합니다:

```js
import Alpine from 'alpinejs';
import Precognition from 'laravel-precognition-alpine';

window.Alpine = Alpine;

Alpine.plugin(Precognition);
Alpine.start();
```

With the Laravel Precognition package installed and registered, you can now create a form object using Precognition's `$form` "magic", providing the HTTP method (`post`), the target URL (`/users`), and the initial form data.

라라벨 Precognition 패키지가 설치되고 등록되었으므로 Precognition의 `$form` "magic"을 사용하여 HTTP 메서드 (`post`), 대상 URL (`/users`) 및 초기 폼 데이터를 제공하여 폼 객체를 만들 수 있습니다.

To enable live validation, you should bind the form's data to it's relevant input and then listen to each input's `change` event. In the `change` event handler, you should invoke the form's `validate` method, providing the input's name:

실시간 유효성 검사를 활성화하려면 폼의 데이터를 해당 입력에 바인딩한 다음 각 입력의 `change` 이벤트를 수신해야 합니다. `change` 이벤트 핸들러에서 입력의 이름을 제공하여 폼의 `validate` 메서드를 호출해야 합니다:

```html
<form x-data="{
    form: $form('post', '/register', {
        name: '',
        email: '',
    }),
}">
    @csrf
    <label for="name">Name</label>
    <input
        id="name"
        name="name"
        x-model="form.name"
        @change="form.validate('name')"
    />
    <template x-if="form.invalid('name')">
        <div x-text="form.errors.name"></div>
    </template>

    <label for="email">Email</label>
    <input
        id="email"
        name="email"
        x-model="form.email"
        @change="form.validate('email')"
    />
    <template x-if="form.invalid('email')">
        <div x-text="form.errors.email"></div>
    </template>

    <button>Create User</button>
</form>
```

Now, as the form is filled by the user, Precognition will provide live validation output powered by the validation rules in the route's form request. When the form's inputs are changed, a debounced "precognitive" validation request will be sent to your Laravel application. You may configure the debounce timeout by calling the form's `setValidationTimeout` function:

이제 사용자가 양식을 작성하면 Precognition은 라우트의 form request의 유효성 검사 규칙을 기반으로 하는 실시간 유효성 검사 출력을 제공합니다. 양식의 입력이 변경되면 디바운스된 "예지" 유효성 검사 요청이 라라벨 애플리케이션으로 전송됩니다. 폼의 `setValidationTimeout` 함수를 호출하여 디바운스 시간 초과를 구성할 수 있습니다:

```js
form.setValidationTimeout(3000);
```

When a validation request is in-flight, the form's `validating` property will be `true`:

유효성 검사 요청이 전송되면 폼의 `validating` 속성이 `true`가 됩니다:

```html
<template x-if="form.validating">
    <div>Validating...</div>
</template>
```

Any validation errors returned during a validation request or a form submission will automatically populate the form's `errors` object:

유효성 검사 요청 또는 양식 제출 중에 반환된 모든 유효성 검사 오류는 자동으로 폼의 `errors` 객체에 채워집니다:

```html
<template x-if="form.invalid('email')">
    <div x-text="form.errors.email"></div>
</template>
```

You can determine if the form has any errors using the form's `hasErrors` property:

폼에 오류가 있는지 여부는 폼의 `hasErrors` 속성을 사용하여 확인할 수 있습니다:

```html
<template x-if="form.hasErrors">
    <div><!-- ... --></div>
</template>
```

You may also determine if an input has passed or failed validation by passing the input's name to the form's `valid` and `invalid` functions, respectively:

또한 각각 입력의 이름을 폼의 `valid` 및 `invalid` 함수에 전달하여 입력이 유효성 검사를 통과했는지 실패했는지 여부를 확인할 수도 있습니다:

```html
<template x-if="form.valid('email')">
    <span>✅</span>
</template>

<template x-if="form.invalid('email')">
    <span>❌</span>
</template>
```

> **Warning**
> A form input will only appear as valid or invalid once it has changed and a validation response has been received.

> **주의**
> 양식 입력은 변경되고 유효성 검사 응답이 수신된 경우에만 유효하거나 유효하지 않은 것으로 표시됩니다.

<a name="repopulating-old-form-data"></a>
#### Repopulating Old Form Data
#### 이전 양식 데이터 다시 채우기

In the user creation example discussed above, we are using Precognition to perform live validation; however, we are performing a traditional server-side form submission to submit the form. So, the form should be populated with any "old" input and validation errors returned from the server-side form submission:

위에서 설명한 사용자 생성 예제에서는 실시간 유효성 검사를 수행하기 위해 Precognition을 사용하지만, 서버 측 양식 제출을 사용하여 양식을 제출합니다. 따라서 서버 측 양식 제출에서 반환된 모든 "이전" 입력 및 유효성 검사 오류로 양식을 채워야 합니다:

```html
<form x-data="{
    form: $form('post', '/register', {
        name: '{{ old('name') }}',
        email: '{{ old('email') }}',
    }).setErrors({{ Js::from($errors->messages()) }}),
}">
```

Alternatively, if you would like to submit the form via XHR you may use the form's `submit` function, which returns an Axios request promise:

또는 XHR을 통해 양식을 제출하려면 Axios 요청 프로미스를 반환하는 폼의 `submit` 함수를 사용할 수 있습니다:

```html
<form 
    x-data="{
        form: $form('post', '/register', {
            name: '',
            email: '',
        }),
        submit() {
            this.form.submit()
                .then(response => {
                    form.reset();

                    alert('User created.')
                })
                .catch(error => {
                    alert('An error occurred.');
                });
        },
    }"
    @submit.prevent="submit"
>
```

<a name="configuring-axios"></a>
### Configuring Axios
### Axios 구성

The Precognition validation libraries use the [Axios](https://github.com/axios/axios) HTTP client to send requests to your application's backend. For convenience, the Axios instance may be customized if required by your application. For example, when using the `laravel-precognition-vue` library, you may add additional request headers to each outgoing request in your application's `resources/js/app.js` file:

Precognition 유효성 검사 라이브러리는 [Axios](https://github.com/axios/axios) HTTP 클라이언트를 사용하여 애플리케이션의 백엔드로 요청을 보냅니다. 편의를 위해 Axios 인스턴스는 애플리케이션이 요구하는 경우에만 사용자 정의할 수 있습니다. 예를 들어 `laravel-precognition-vue` 라이브러리를 사용할 때 애플리케이션의 `resources/js/app.js` 파일에서 모든 발신 요청에 추가 요청 헤더를 추가할 수 있습니다:

```js
import { client } from 'laravel-precognition-vue';

client.axios().defaults.headers.common['Authorization'] = authToken;
```

Or, if you already have a configured Axios instance for your application, you may tell Precognition to use that instance instead:

또는 애플리케이션에 이미 구성된 Axios 인스턴스가 있는 경우 Precognition에게 해당 인스턴스를 사용하도록 지시할 수 있습니다:

```js
import Axios from 'axios';
import { client } from 'laravel-precognition-vue';

window.axios = Axios.create()
window.axios.defaults.headers.common['Authorization'] = authToken;

client.use(window.axios)
```

> **Warning**
> The Inertia flavored Precognition libraries will only use the configured Axios instance for validation requests. Form submissions will always be sent by Inertia.

> **주의**
> Inertia flavored Precognition 라이브러리는 유효성 검사 요청에만 구성된 Axios 인스턴스를 사용합니다. 양식 제출은 항상 Inertia에 의해 전송됩니다.

<a name="customizing-validation-rules"></a>
## Customizing Validation Rules
## 유효성 검사 규칙 사용자 정의

It is possible to customize the validation rules executed during a precognitive request by using the request's `isPrecognitive` method.

`isPrecognitive` 메서드를 사용하여 예지 요청 중에 실행되는 유효성 검사 규칙을 사용자 정의할 수 있습니다.

For example, on a user creation form, we may want to validate that a password is "uncompromised" only on the final form submission. For precognitive validation requests, we will simply validate that the password is required and has a minimum of 8 characters. Using the `isPrecognitive` method, we can customize the rules defined by our form request:

예를 들어 사용자 생성 양식에서 비밀번호가 최종 양식 제출 시에만 "compromised"되지 않았는지 유효성을 검사하려고 합니다. 예지 유효성 검사 요청의 경우 비밀번호가 필요하고 최소 8자인지만 유효성을 검사합니다. `isPrecognitive` 메서드를 사용하여 양식 요청에서 정의한 규칙을 사용자 정의할 수 있습니다:

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rules\Password;

class StoreUserRequest extends FormRequest
{
    /**
     * Get the validation rules that apply to the request.
     *
     * @return array
     */
    protected function rules()
    {
        return [
            'password' => [
                'required',
                $this->isPrecognitive()
                    ? Password::min(8)
                    : Password::min(8)->uncompromised(),
            ],
            // ...
        ];
    }
}
```

<a name="handling-file-uploads"></a>
## Handling File Uploads
## 파일 업로드 처리

By default, Laravel Precognition does not upload or validate files during a precognitive validation request. This ensure that large files are not unnecessarily uploaded multiple times.

기본적으로 라라벨 Precognition은 예지 유효성 검사 요청 중에 파일을 업로드하거나 유효성을 검사하지 않습니다. 이렇게 하면 큰 파일이 불필요하게 여러 번 업로드되지 않습니다.

Because of this behavior, you should ensure that your application [customizes the corresponding form request's validation rules](#customizing-validation-rules) to specify the field is only required for full form submissions:

이 동작으로 인해 애플리케이션 [해당 양식 요청의 유효성 검사 규칙을 사용자 정의](#customizing-validation-rules)하여 해당 필드가 전체 양식 제출에만 필요하도록 지정해야 합니다:

```php
/**
 * Get the validation rules that apply to the request.
 *
 * @return array
 */
protected function rules()
{
    return [
        'avatar' => [
            ...$this->isPrecognitive() ? [] : ['required'],
            'image',
            'mimes:jpg,png'
            'dimensions:ratio=3/2',
        ],
        // ...
    ];
}
```

If you would like to include files in every validation request, you may invoke the `validateFiles` function on your client-side form instance:

모든 유효성 검사 요청에 파일을 포함하려면 클라이언트 측 폼 인스턴스에서 `validateFiles` 함수를 호출할 수 있습니다:

```js
form.validateFiles();
```

<a name="managing-side-effects"></a>
## Managing Side-Effects
## 부작용 관리

When adding the `HandlePrecognitiveRequests` middleware to a route, you should consider if there are any side-effects in _other_ middleware that should be skipped during a precognitive request.

라우트에 `HandlePrecognitiveRequests` 미들웨어를 추가할 때 예지 요청 중에 건너뛸 _다른_ 미들웨어에 부작용이 있는지 고려해야 합니다.

For example, you may have a middleware that increments the total number of "interactions" each user has with your application, but you may not want precognitive requests to be counted as an interaction. To accomplish this, we may check the request's `isPrecognitive` method before incrementing the interaction count:

예를 들어 사용자가 애플리케이션과 상호 작용한 총 횟수를 증가시키는 미들웨어가 있을 수 있지만 예지 요청을 상호 작용으로 계산하고 싶지 않을 수 있습니다. 이를 위해 상호 작용 수를 증가시키기 전에 요청의 `isPrecognitive` 메서드를 확인할 수 있습니다:

```php
<?php

namespace App\Http\Middleware;

use App\Facades\Interaction;
use Closure;
use Illuminate\Http\Request;

class InteractionMiddleware
{
    /**
     * Handle an incoming request.
     */
    public function handle(Request $request, Closure $next): mixed
    {
        if (! $request->isPrecognitive()) {
            Interaction::incrementFor($request->user());
        }

        return $next($request);
    }
}
```
