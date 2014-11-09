title: 使用Vue.js
type: guide
order: 1
vue_version: 0.10.6
dev_size: 124.76
min_size: 41.80
gz_size: 14.29
---

> **兼容性提示：** Vue.js 不支持IE8及以下的浏览器。

## 单独引入

你可以通过一个script标签引入 Vue.js 。`Vue` 将会被注册成一个全局变量。

<a class="button" href="https://raw.github.com/yyx990803/vue/v{{vue_version}}/dist/vue.js" download>开发 版本</a><br><span class="light">{{dev_size}}kb, 有一些注释以及 debug/警告信息。</span>

<a class="button" href="https://raw.github.com/yyx990803/vue/v{{vue_version}}/dist/vue.min.js" download>生产版本</a><br><span class="light">{{min_size}}kb minified / {{gz_size}}kb gzipped</span>

也可以使用 [cdnjs](//cdnjs.cloudflare.com/ajax/libs/vue/{{vue_version}}/vue.min.js) (需要一些时间同步，所以最新版本可能不可用)。

## Component

``` bash
$ component install yyx990803/vue
```
```js
var Vue = require('vue')
```

对于最新版本（不稳定的分支，使用后果自负……）

``` bash
$ component install yyx990803/vue@dev
```

## Browserify

``` bash
$ npm install vue
```
```js
var Vue = require('vue')
```

对于最新版本：

``` bash
$ npm install yyx990803/vue#dev
```

<p class="tip">`dist/` 中的构建版本在包管理器中不可用，因为它假定它在全局中加载，通过它自己的 `require` 机制。当使用包管理器的时候，直接使用源码版本。</p>

## Bower

``` bash
$ bower install vue
```

``` html
<script src="bower_components/vue/dist/vue.js">
```

## AMD Module Loaders

e.g. RequireJS, SeaJS: Built versions in `/dist` or installed via Bower is wrapped with UMD so it can be used directly as an AMD module.

## Ready?

[嘿喂狗！开始吧](/guide/).