title: 事件的监听
type: guide
order: 6
---

你可以使用 `v-on` 指令来给DOM事件绑定事件监听器。它既可以与事件处理函数（没有调用的括号）也可以与一个行内表达式相绑定。如果给定的是一个处理函数，它将会将原始的DOM事件作为参数。事件还带来了一个额外的属性：`targetVM`，当事件被触发时，会指向其对应的视图模型：

``` html
<div id="demo">
    <a v-on="click: onClick">Trigger a handler</a>
    <a v-on="click: n++">Trigger an expression</a>
</div>
```

``` js
new Vue({
    el: '#demo',
    data: {
        n: 0
    },
    methods: {
        onClick: function (e) {
            console.log(e.target.tagName) // "A"
            console.log(e.targetVM === this) // true
        }
    }
})
```

## 通过表达式调用处理函数

当 `v-on` 和 `v-repeat` 同时使用时，`targetVM` 会很有用处，因为后者（`v-repeat`）会创建一系列的子视图模型。然而，通常使用 `this` 来进行更简单的表达式调用，这个关键字对应当前的视图模型内容：

``` html
<ul id="list">
    <li v-repeat="items" v-on="click: toggle(this)">{{text}}</li>
</ul>
```

``` js
new Vue({
    el: '#list',
    data: {
        items: [
            { text: 'one', done: true },
            { text: 'two', done: false }
        ]
    },
    methods: {
        toggle: function (item) {
            item.done = !item.done
        }
    }
})
```

当你想要在一个表达式处理函数中访问原始DOM事件，你可以通过 `$event` 来访问它：

``` html
<button v-on="click: submit('hello!', $event)">Submit</button>
```

``` js
/* ... */
{
    methods: {
        submit: function (msg, e) {
            e.stopPropagation()
        }
    }
}
/* ... */
```

## 特殊的 `key` 过滤器

当监听键盘事件的时候，我们常常需要关注一些通用的键盘按键。Vue.js 提供一个特殊的 `key` 过滤器，它仅能用于 `v-on` 指令。 它会把所关注的键盘按键当作自己的参数。（译者注：这里属于一个语法糖。合理的使用即可，用多了会蛀牙……）：

```
<!-- only call vm.submit() when the keyCode is 13 -->
<input v-on="keyup:submit | key 13">
```

它有一些预设好的通用值：

```
<!-- same as above -->
<input v-on="keyup:submit | key enter">
```

更多详情请访问 [full list of key filter presets](/api/filters.html#key).

## 为什么把事件监听写到HTML中？

You might be concerned about this whole event listening approach violates the good old rules about "separation of concern". Rest assured - since all Vue.js handler functions and expressions are strictly bound to the ViewModel that's handling the current View, it won't cause any maintainance difficulty. In fact, there are several benefits in using `v-on`:

1. It makes it easier to locate the handler function implementations within your JS code by simply skimming the HTML template.
2. Since you don't have to manually attach event listeners in JS, your ViewModel code can be pure logic and DOM-free. This makes it easier to test.
3. When a ViewModel is destroyed, all event listeners are automatically removed. You don't need to worry about cleaning it up yourself.

Next up: [Handling Forms](/guide/forms.html).