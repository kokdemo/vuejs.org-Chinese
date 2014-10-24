title: 我在学过滤器的道路上迷失了方向
type: guide
order: 4
---

## 开宗明义

一个 Vue.js 的过滤器从本质上来说，是获得一个数据，操作这个数据，然后再返回结果。语法上，过滤器需要一个简单的竖杠(`|`)进行标记，同时它可以输入一个或者多个参数：

``` html
<element directive="expression | filterId [args...]"></element>
```

## 例子

过滤器必须放置在一个指令的值的后面：

``` html
<span v-text="message | capitalize"></span>
```

你也可以使用mustache风格（译者注：就是这个模板引擎的那个风格）的绑定：

``` html
<span>&#123;&#123;message | uppercase&#125;&#125;</span>
```

多重绑定必须链接在一起：

``` html
<span>&#123;&#123;message | lowercase | reverse&#125;&#125;</span>
```

## 参数

一些过滤器有可选的参数，可以通过添加空格将这些参数简单的添加进去：

``` html
<span>&#123;&#123;order | pluralize st nd rd th&#125;&#125;</span>
```

``` html
<input v-on="keyup: submitForm | key enter">
```

想要看到特殊用法的例子，请见 [full list of built-in filters](/api/filters.html).

## 写一个自定义过滤器

你可以使用全局方法 `Vue.filter()` 来注册一个过滤器，这需要输入一个 **filterID** 和一个 **filter function** 。过滤器的函数获取一个值作为参数，并且返回一个加工过的值：

``` js
Vue.filter('reverse', function (value) {
    return value.split('').reverse().join('')
})
```

``` html
<!--
    'abc' => 'cba'
-->
<span v-text="message | reverse"></span>
```

过滤器函数也接受任何行内的参数:

``` js
Vue.filter('wrap', function (value, begin, end) {
    return begin + value + end
})
```

``` html
<!--
    'hello' => 'before hello after'
-->
<span v-text="message | wrap before after"></span>
```

## 计算过滤器

当一个过滤器被声明，他的 `this` 关键字被设置成被声明的视图模型实例。这允许输出基于它自己的视图模型状态的动态结果。
在这种情况下，我们需要追踪这些被访问的属性以便当他们改变时，使用该过滤器的指令会被重置。（译者注：这里重置来自于re-evaluated这个单词，我确实不知道怎么翻译较好。）

举个例子：

``` js
Vue.filter('concat', function (value, key) {
    // `this` points to the VM invoking the filter
    return value + this[key]
})
```
``` html
<input v-model="userInput">
<span>{&#123;msg | concat userInput&#125;}</span>
```

依赖它所访问视图模型状态的过滤器被叫做 **计算过滤器computed filters**。对于这个简单的示例，你可以仅仅使用一个表达式达到相同的结果，但是对于一些多余一句的复杂程序，你需要把他们放在一起，当到一个可计算属性或者是计算过滤器当中。

举个栗子，内置的`filterBy`以及`orderBy`过滤器都是计算过滤器，他们执行一些比较重要的数组传递工作。对于自定义过滤器，Vue.js 通过寻找一个过滤器的函数体中引用`this`关键字来检查计算过滤器。任何使用计算过滤器的指令将会自动的编译成一个表达式，这样它的过滤器就被包括在依赖收集程序中了。

如果你现在觉得计算过滤器的概念还很是纠结，别担心，金鸡胶……。他会被Vue.js自动的处理，你并不需要知道他的内在工作原理。在你了解了更多的相关概念知识之后，你将会豁然开朗。

现在你已经对指令和过滤器有一个完整的认识，是时候聊聊怎么[显示一个列表](/guide/list.html)了。