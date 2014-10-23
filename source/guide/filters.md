title: Filters in Depth
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

Filters that relies on the state of the ViewModel that is calling it are referred to as **computed filters**. For this simple example above, you can achieve the same result with just an expression, but for more complicated procedures that need more than one statements, you need to put them either in a computed property or a computed filter.

For example, the built-in `filterBy` and `orderBy` filters are both computed filters that perform non-trivial work on the Array being passed in. For custom filters, Vue.js checks for computed filters by looking for references to `this` in a filter's function body. Any directive that uses a computed filter will be automatically compiled as an expression so its filters are included in the dependency collection process.

If you find the concept of computed filters confusing at the moment, don't worry. It is handled automatically by Vue.js and you don't really need to know how it works to leverage it. As you get familiar with more related concepts, it will all make sense.

Now that you know everything about directives and filters, let's talk about how to [display a list of items](/guide/list.html).