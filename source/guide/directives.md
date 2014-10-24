title: 教练，我想学指令
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

这里 `"my-component"` 不是一个数据属性，而是一个字符串ID，而Vue.js用它来寻找相应组件的构造函数。

从 Vue.js 0.10开始，你也可以在文字指令中使用 mustache 表达式了。这允许你动态的的生成你想要看到的组件：

``` html
<div v-component="{&#123; isOwner ? 'owner-panel' : 'guest-panel' &#125;}"></div>
```

然而，记住指令中的 mustache 表达式只能转化 **一次**。在指令被编译后，他就不再对数值变化起作用了。为了动态的在运行中实例化不同的组件，使用 [v-view](/api/directives.html#v-view) 指令。

你可以在这个列表中找到所有的文字指令 [API reference](/api/directives.html#Literal_Directives)。

## 空指令

有的指令不需要任何的属性值，他们只是简单的对元素运行一次。 举个例子 `v-pre` 指令:

``` html
<div v-pre>
    <!-- markup in here will not be compiled -->
</div>
```

你可以在这个列表中找到所有的空指令 [API reference](/api/directives.html#Empty_Directives).

## 写一个自定义指令

使用 `Vue.directive()` 方法，你可以注册一个全局的自定义指令，通过一个与指令id **directiveID** 关联的定义对象 **definition object** 。一个定义对象可以提供几个拦截函数（全都是自定义）：

- **bind**: 当指令第一次绑定在元素上时访问一次。
- **update**: 当绑定数值变化时访问。更新值会以变量的形式提供。
- **unbind**: 当指令与元素解绑时访问。

**例子**

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

一旦注册，你可以这么使用 Vue.js 模板（你需要加一个 Vue.js 的前缀）：

``` html
<div v-my-directive="someValue"></div>
```

当你只需要 `update` 功能，你可以通过简单的定义对象代替之：

``` js
Vue.directive('my-directive', function (value) {
    // this function will be used as update()
})
```

所有的拦截函数都会复制到实际的定义对象中 **directive object**，你可以通过这些函数访问他们的 `this` 环境。这些指令对象会提供一些有用的属性：

- **el**: 指令绑定的元素。
- **key**: 绑定的键值，不包括参数和过滤器。
- **arg**: 可能提供的参数。
- **expression**: 没有压缩过的原生表达式。
- **vm**: 拥有这些指令的相关视图模型。
- **value**: 当前绑定的数值。

<p class="tip">你应该将所有的的属性作为只读并尽可能少的更改它们。你也可以让自定义指令和这些指令对象连接，但是要小心不要偶然的覆盖掉内部已有的那些。</p>

一个使用了一些属性的自定义指令的例子：

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

**结果**

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

### 生成文字和空指令

如果你在生成自定义指令的时候使用 `isLiteral: true` 或者 `isEmpty: true` ，所有的数据绑定工作将会跳过这个指令，此时只有 `bind()` 和 `unbind()` 才会调用。在文字指令中，表达式一直是解析过的，所以你可以访问一些信息比如 `this.expression`， `this.key` 和 `this.arg`。 对于空指令来说，即使表达式存在，Vue.js 也不会解析表达式。

例子：

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

### 生成一个函数指令

Vue.js 鼓励开发者将数据与行为分离，因此实例方法应该写在 `methods` 中而不是写在数据对象里。不然
，数据对象中的函数将会被无视，而普通指令将无法与他们绑定。

为了在你的自定义指令中通过 `methods` 获取函数，你需要设置 `isFn` 选项：

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

通过设置 `isFn:true` 也可以使你的自定义指令接受行内表达式，就像 `v-on` 那样。

想要看到更多的综合性的例子，请自行阅读源码中的 `src/directives/` 部分。（译者注：作者你够懒……）

下一章节: [深入理解过滤器 Filters in Depth](/guide/filters.html).
