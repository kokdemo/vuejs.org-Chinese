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

你可能会纠结为什么整个事件监听的方法违背了“关系分离（译者注：估计就是逻辑表现分离这个意思吧）”的老规则。请放心，所有的的Vue.js的处理函数和表达式都是严格绑定在处理当前视图的视图模型上，它不会导致任何的维护困难。实际上，使用 `v-on` 还有这么一些好处：

1. 它使得你可以更快的通过略读HTML模板来定位处理函数在 JS 代码中的位置。

2. 由于你不需要手动的在 JS 中绑定监听器到，你的视图模型代码将会是纯粹的逻辑，并且与DOM完全无关。这也减轻了测试的压力。

3. 当一个视图模型被摧毁时，所有的监听器都会被自动的移除掉。你不需要担心需要自己来清理这些东西。

下一章节： [处理表单Handling Forms](./forms.html).