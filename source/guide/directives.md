title: Directives In Depth
type: guide
order: 3
---

## 概要

如果你之前没有用过 AngularJS ，你可能不清楚什么是 指令directive 。本质上，指令是一些特殊的记号来告知库对一个DOM元素做一些操作。在Vue.js 中，指令的概念比Angular中的更简单。一个Vue.js的指令只能以HTML属性前缀的形式出现，格式如下：

``` html
<element prefix-directiveId="[arg:] ( keypath | expression ) [filters...]"></element>
```

## 一个简单的例子

``` html
<div v-text="message"></div>
```

这里前缀v是默认的。指令是 `text` ，对应的值则是`message`。当视图模型变更的时候，这个指令控制 Vue.js 按照 `message` 来更新div的文字内容 `textContent` 。

## 行内表达式

``` html
<div v-text="'hello ' + user.firstName + ' ' + user.lastName"></div>
```

这里我们使用了一个计算表达式代替了一个单纯的属性键值。Vue.js 会自动的追踪表达式当中的属性，当变化时自动刷新指令。感谢同步批量更新，当多个属性变化时，一个表达式只会在每个时间周期更新一次。

你应该聪明的使用表达式，避免把太多逻辑写到你的模板当中，特别是有副作用的语句（事件监听表达式是例外）。为了阻止过量的模板当中加入逻辑，Vue.js 的行内表达式限制为 **单行语句** （译者注：这其实是懒得实现多行吧喂……）。如果想要绑定复杂的操作，使用计算属性代替之 [Computed Properties](/guide/computed.html) 。

<p class="tip">因为安全性的原因，在行内表达式中，你只能访问它的父元素和附近的视图模型中的属性和方法。</p>

## 参数

``` html
<div v-on="click : clickHandler"></div>
```

一些指令需要在键值或者表达式前放置一个参数。在这个例子中 `click` 参数显示我们想要用 `v-on` 指令监听一个点击事件绑定视图模型实例中的 `clickHandler` 方法。

## 过滤器

过滤器可以添加在指令键值或者表达式的后面，用于在 DOM 更新之前继续处理数据。过滤器通过一个简单竖杠(`|`)来声明。更多的细节请见 [Filters in Depth](/guide/filters.html).

## 多重语句

你可以把使用相同指令的多个绑定合而为一到一个属性中，用逗号分离（译者注：类似json格式）：

``` html
<div v-on="
    click   : onClick,
    keyup   : onKeyup,
    keydown : onKeydown
">
</div>
```

## 文字指令Literal Directives

一些指令不会产生数据绑定，他们只是简单的把属性设置为文字字符串。举个例子 `v-component` 指令：

``` html
<div v-component="my-component"></div>
```

Here `"my-component"` is not a data property - it's a string ID that Vue.js uses to lookup the corresponding Component constructor.

Since Vue.js 0.10, you can also use mustache expressions inside literal directives. This allows you to dynamically resolve the type of component you want to use:

``` html
<div v-component="{&#123; isOwner ? 'owner-panel' : 'guest-panel' &#125;}"></div>
```

However, note that mustache expressions inside literal directives are evaluated **only once**. After the directive has been compiled, it will no longer react to value changes. To dynamically instantiate different components at run time, use the [v-view](/api/directives.html#v-view) directive.

A full list of literal directives can be found in the [API reference](/api/directives.html#Literal_Directives).

## Empty Directives

Some directives don't even expect an attribute value - they simply do something to the element once and only once. For example the `v-pre` directive:

``` html
<div v-pre>
    <!-- markup in here will not be compiled -->
</div>
```

A full list of empty directives can be found in the [API reference](/api/directives.html#Empty_Directives).

## Writing a Custom Directive

You can register a global custom directive with the `Vue.directive()` method, passing in a **directiveID** followed by a **definition object**. A definition object can provide several hook functions (all optional):

- **bind**: called only once, when the directive is first bound to the element.
- **update**: called when the binding value changes. The new value is provided as the argument.
- **unbind**: called only once, when the directive is unbound from the element.

**Example**

``` js
Vue.directive('my-directive', {
    bind: function (value) {
        // do preparation work
        // e.g. add event listeners or expensive stuff
        // that needs to be run only once
        // `value` is the initial value
    },
    update: function (value) {
        // do something based on the updated value
        // this will also be called for the initial value
    },
    unbind: function () {
        // do clean up work
        // e.g. remove event listeners added in bind()
    }
})
```

Once registered, you can use it in Vue.js templates like this (you need to add the Vue.js prefix to it):

``` html
<div v-my-directive="someValue"></div>
```

When you only need the `update` function, you can pass in a single function instead of the definition object:

``` js
Vue.directive('my-directive', function (value) {
    // this function will be used as update()
})
```

All the hook functions will be copied into the actual **directive object**, which you can access inside these functions as their `this` context. The directive object exposes some useful properties:

- **el**: the element the directive is bound to.
- **key**: the keypath of the binding, excluding arguments and filters.
- **arg**: the argument, if present.
- **expression**: the raw, unparsed expression.
- **vm**: the context ViewModel that owns this directive.
- **value**: the current binding value.

<p class="tip">You should treat all these properties as read-only and refrain from changing them. You can attach custom properties to the directive object too, but be careful not to accidentally overwrite existing internal ones.</p>

An example of a custom directive using some of these properties:

``` html
<div id="demo" v-demo="LightSlateGray : msg"></div>
```

``` js
Vue.directive('demo', {
    bind: function () {
        this.el.style.color = '#fff'
        this.el.style.backgroundColor = this.arg
    },
    update: function (value) {
        this.el.innerHTML =
            'argument - ' + this.arg + '<br>' +
            'key - ' + this.key + '<br>' +
            'value - ' + value
    }
})
var demo = new Vue({
    el: '#demo',
    data: {
        msg: 'hello!'
    }
})
```

**Result**

<div id="demo" v-demo="LightSlateGray : msg"></div>
<script>
Vue.directive('demo', {
    bind: function () {
        this.el.style.color = '#fff'
        this.el.style.backgroundColor = this.arg
    },
    update: function (value) {
        this.el.innerHTML =
            'argument - ' + this.arg + '<br>' +
            'key - ' + this.key + '<br>' +
            'value - ' + value
    }
})
var demo = new Vue({
    el: '#demo',
    data: {
        msg: 'hello!'
    }
})
</script>

### Creating Literal &amp; Empty Directives

If you pass in `isLiteral: true` or `isEmpty: true` when creating a custom directive, all data-binding work will be skipped for that directive, and only `bind()` and `unbind()` will be called. In literal directives the expression will still be parsed, so you can still access information such as `this.expression`, `this.key` and `this.arg`. For empty directives, Vue.js will never parse the expression even if it exists.

Example:

``` html
<div v-literal-dir="foo"></div>
```

``` js
Vue.directive('literal-dir', {
    isLiteral: true,
    bind: function () {
        console.log(this.expression) // 'foo'
    }
})
```

### Creating a Function Directive

Vue.js encourages the developer to separate data from behavior, so instance methods are expected to be contained in the `methods` option and not inside data objects. As a result, functions inside data objects are ignored and normal directives will not be able to bind to them.

To gain access to functions inside `methods` in your custom directive, you need to pass in the `isFn` option:

``` js
Vue.directive('my-handler', {
    isFn: true, // important!
    bind: function () {
        // ...
    },
    update: function (handler) {
        // the passed in value is a function
    },
    unbind: function () {
        // ...
    }
})
```

Passing in `isFn:true` also enables your custom directive to accept inline expressions like `v-on` does. For more comprehensive examples, check out `src/directives/` in the source code.

Next: [Filters in Depth](/guide/filters.html).
