title: Getting Started
type: guide
order: 2
---

## 介绍

Vue.js 是一个用于构建交互web界面的库。


从技术角度上来讲，Vue.js 专注于 MVVM 结构中视图模型 [ViewModel](#ViewModel) 的部分。它将视图 [View](#View) 与模型 [Model](#Model) 通过双向数据绑定连接在一起。 实际上DOM操作和输出格式通过指示 [Directives](#Directives) 和过滤器 [Filters](#Filters) 抽象出来。

从哲学上来说，它的目的是尽可能简单的为 MVVM 的数据绑定提供一个 API 。模块化和可组合都是在设计当中被特别注意的。它并不是一个成熟的框架 - 他被设计的十分简单和灵活。（你这是在吐槽angularJS么……）你可以用它快速的完成原型，或者和其他的库混合使用来开发一个传统前端项目。（英文这里用了stack这个词。）它也可以很自如的与一些无后端服务相契合，比如Firebase （Firebase 是一个数据同步存储的服务，正在努力的替代现有的后端存储功能）。

Vue.js 的 API 深受 [AngularJS], [KnockoutJS], [Ractive.js] 和 [Rivets.js] 的影响。尽管有相似之处，相比现有的库我深信 Vue.js 通过找到简洁与功能的平衡点，能提供一个有价值的替代

即使你已经熟悉其中的一些内容，建议你阅读以下概念的概述,因为这些术语在 Vue.js 中的概念可能有所不同。

## 概念概述

### ViewModel

这是一个同步模型层和视图层的对象。在 Vue.js 中，ViewModels 层通过 `Vue` 的构造函数或者它的子类实例化：

```js
var vm = new Vue({ /* options */ })
```

这是你在使用 Vue.js 时最主要的对象。更多信息请访问 [Class: Vue](/api/)。

### View

实际上用户看到的 HTML/DOM 。

```js
vm.$el // The View
```

当使用 Vue.js 的时候， 除了你自己的指令，很少会接触DOM。 View 会在数据改变的时候自动的变化。 这些视图的改变通过精确的修改文字节点达到十分细腻的粒度。他们也会批量和异步执行以获得更好的性能。

### Model

一个稍微修改的简单的 JavaScript 对象.

```js
vm.$data // The Model
```

在 Vue.js中，模型层是一个简单的JavaScript对象，或者  **data objects** 。你可以操作他们的属性，ViewModels 会收到这些改变。 Vue.js 将数据对象的属性变为了 ES5 getter/setters，可以直接的操作而不需要脏检查（来检测数据有没有被修改）。

数据对象被放在一个地方，因此修改它和引用它有同样的效果。这同样让多重 ViewModel 实体关联同一块数据。

更多的细节见 [Instantiation Options: data](/api/instantiation-options.html#data).

### Directives

用一个加了前缀的 HTML 属性来告诉 Vue.js 做一些有关 DOM 元素的事情。

```html
<div v-text="message"></div>
```

这里 div 元素有一个 `v-text` 指令，对应的值是 `message` 。这告诉 Vue.js 保持这个 div 的文字内容与 ViewModel 层中的 `message` 值同步。

指令可以压缩任意的 DOM 操作。比如 `v-attr` 操作一个元素的属性，`v-repeat` 根据一个数组复制一个元素， `v-on` 绑定事件监听……我们之后会提到这些东西。

### Mustache Bindings

你也可以使用 Mustache 风格（Mustache是一个模板引擎的名字）来绑定文字和属性。他们会在引擎中转义成 `v-text` 和 `v-attr` 指令。 举个“栗子”:

```html
<div id="person-&#123;&#123;id&#125;&#125;">Hello &#123;&#123;name&#125;&#125;!</div>
```

尽管他很方便，这里还有一些你需要注意的点：

<p class="tip">`<image>` 图片标签的 `src` 属性在设置时会发起一个HTTP请求，如果使用模板中第一次访问将会404（这里模板中填的是'{{src}}'），这种情况下 `v-attr` 更好用。</p>

<p class="tip">Internet Explorer 在解析 HTML 的时候会移除掉无效的行内 `style` 属性，因此如果你需要支持 IE ，请使用 `v-style` 来绑定行内CSS。</p>

<p class="tip">你可以使用三重花括号 &#123;&#123;&#123; 比如 &#125;&#125;&#125; 通过翻译成 `v-html` 来使用非转义的 HTML。然而这却给XSS 注入带来了机会，因此建议你只在那些你觉得对数据绝对安全的地方才使用三重花括号，或者通过一个过滤器来过滤掉那些有问题的 HTML。</p>

### Filters

过滤器用于在展现之前处理原始数据。他们由一个包括指令和绑定在内的“竖杠”组成（译者注：原谅笔者一直不到知道“|”大名叫什么……）：

```html
<div>&#123;&#123;message | capitalize&#125;&#125;</div>
```

现在每当 div 的文字内容更新之前， `message` 值将会先通过 `capitalize` 函数。更多的信息见[Filters in Depth](/guide/filters.html).

### Components

在 Vue.js 中，通过使用 `Vue.component(ID, constructor)` ，一个组件简单到只需要一个 ViewModel 的构造器，然后用 ID 实例出来。通过一个相关联的ID，组件通过 `v-component` 的指令嵌入到另外的 ViewModel 的模板中。 这种简单的机制使声明重用，视图模型组成与 [Web Components](http://www.w3.org/TR/components-intro/)类似，不需要最新的浏览器或者复杂的填充物。通过将一个应用分解为更小的组件，将会使代码高度解耦并增强可维护性。更多信息，见[Composing ViewModels](/guide/composition.html).

## 一个栗子

``` html
<div id="demo">
    <h1>&#123;&#123;title | uppercase&#125;&#125;</h1>
    <ul>
        <li
            v-repeat="todos"
            v-on="click: done = !done"
            class="&#123;&#123;done ? 'done' : ''&#125;&#125;">
            &#123;&#123;content&#125;&#125;
        </li>
    </ul>
</div>
```

``` js
var demo = new Vue({
    el: '#demo',
    data: {
        title: 'todos',
        todos: [
            {
                done: true,
                content: 'Learn JavaScript'
            },
            {
                done: false,
                content: 'Learn Vue.js'
            }
        ]
    }
})
```

**Result**

<div id="demo"><h1>&#123;&#123;title | uppercase&#125;&#125;</h1><ul><li v-repeat="todos" v-on="click: done = !done" class="&#123;&#123;done ? 'done' : ''&#125;&#125;">&#123;&#123;content&#125;&#125;</li></ul></div>
<script>
var demo = new Vue({
    el: '#demo',
    data: {
        title: 'todos',
        todos: [
            {
                done: true,
                content: 'Learn JavaScript'
            },
            {
                done: false,
                content: 'Learn Vue.js'
            }
        ]
    }
})
</script>

也可以在这里看 [jsfiddle](http://jsfiddle.net/yyx990803/yMv7y/).

你可以点击一个todo项去切换它，或者你可以打开你的浏览器命令行运行 `demo` 对象，举个例子，你修改 `demo.title` ，给`demo.todos` 增加一个新对象，或者切换todo 的`done` 状态。 

看到这里，你脑海中应该有一些问题了，别担心，我们会接下来一一解答的。下一课：[Directives in Depth](/guide/directives.html).

[AngularJS]: http://angularjs.org
[KnockoutJS]: http://knockoutjs.com
[Ractive.js]: http://ractivejs.org
[Rivets.js]: http://www.rivetsjs.com