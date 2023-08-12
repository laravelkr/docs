# Laravel Mix
# 라라벨 Mix

- [Introduction](#introduction)
- [시작하기](#introduction)

<a name="introduction"></a>
## Introduction
## 시작하기

[Laravel Mix](https://github.com/laravel-mix/laravel-mix), a package developed by [Laracasts](https://laracasts.com) creator Jeffrey Way, provides a fluent API for defining [webpack](https://webpack.js.org) build steps for your Laravel application using several common CSS and JavaScript pre-processors.

[라라벨 믹스](https://github.com/laravel-mix/laravel-mix)는 [Laracasts](https://laracasts.com)를 만든 제프리 웨이(Jeffrey Way)에 의해 개발된 패키지로 웹펙을 정의하고 빌드하는데 빼어난 API를 제공합니다. 라라벨 믹스를 통해 애플리케이션에서 common CSS와 자바스크립트 전처리기(pre-processors)를 사용할 수 있습니다.

In other words, Mix makes it a cinch to compile and minify your application's CSS and JavaScript files. Through simple method chaining, you can fluently define your asset pipeline. For example:

이 말은 라라벨 믹스를 사용하면 라라벨 애플리케이션의 CSS와 자바스크립트 파일을 손쉽게 컴파일하고 압축할 수 있다는 말입니다. 간단한 메서드 체이닝을 통해서 에셋(asset) 파이프라인을 전문가처럼(fluently) 정의할 수 있습니다. 다음의 예를 보세요.

```js
mix.js('resources/js/app.js', 'public/js')
    .postCss('resources/css/app.css', 'public/css');
```

If you've ever been confused and overwhelmed about getting started with Webpack and asset compilation, you will love Laravel Mix. However, you are not required to use it while developing your application; you are free to use any asset pipeline tool you wish, or even none at all.

혹시라도 웹펙 과 asset 컴파일을 혼란스럽고 부담을 느끼고 있다면, 라라벨 Mix를 좋아하게 될 것입니다. 하지만 애플리케이션을 개발할 때 라라벨 Mix가 꼭 필요한 건 아닙니다. 여러분이 원하는 어떠한 asset 파이프라인 툴을 사용해됩니다. 또는 이러한 기능을 사용하지 않아도 괜찮습니다.

> **Note**  
> Vite has replaced Laravel Mix in new Laravel installations. For Mix documentation, please visit the [official Laravel Mix](https://laravel-mix.com/) website. If you would like to switch to Vite, please see our [Vite migration guide](https://github.com/laravel/vite-plugin/blob/main/UPGRADE.md#migrating-from-laravel-mix-to-vite).

> **Note**  
> Vite가 라라벨 Mix를 대체했습니다. Mix 문서는 [공식 라라벨 Mix](https://laravel-mix.com/) 웹사이트를 참고해주세요. Vite로 전환하고 싶으시다면 [Vite 마이그레이션](https://github.com/laravel/vite-plugin/blob/main/UPGRADE.md#migrating-from-laravel-mix-to-vite)을 참고해주세요.
