title: 燃烧小宇宙吧！计算属性！
type: guide
order: 8
---

Vue.js' 的行内表达式十分的方便，但是使用其最好的场景是一些简单的布尔操作，或者是字符串的连接。对于复杂的的逻辑，你应该使用 **计算属性computed properties**。

在 Vue.js 中， 你可以使用 `computed` 选项来定义计算属性：

``` js
var demo = new Vue({
    data: {
        firstName: 'Foo',
        lastName: 'Bar'
    },
    computed: {
        fullName: {
            // the getter should return the desired value
            $get: function () {
                return this.firstName + ' ' + this.lastName
            },
            // the setter is optional
            $set: function (newValue) {
                var names = newValue.split(' ')
                this.firstName = names[0]
                this.lastName = names[names.length - 1]
            }
        }
    }
})
demo.fullName // 'Foo Bar'
```

如果你仅仅只需要 get 方法，你可以只提供一个简单函数代替前面的对象：

``` js
// ...
computed: {
    fullName: function () {
        return this.firstName + ' ' + this.lastName 
    }    
}
// ...
```

一个计算属性本质上是一个定义了 getter和setter 函数的属性（译者注：这里getter和setter我没有想到比较好的翻译，就这么着吧……单词也简单）。你可以像一个普通的属性来使用一个计算属性，当你访问它的时候，你会获得 getter 函数的返回值；当你改变它的值的时候，你会将新的值作为参数传进触发的的 setter 函数中。

## 依赖收集陷阱

就像行内表达式一样，Vue.js 会自动的收集计算属性的依赖，并在依赖变化时自动刷新这些值。这里有一个你需要记住的东西：当 Vue.js 通过一个 getter 来监视他们的属性从而收集依赖的时候，你需要小心的对待有 getter 中的条件逻辑的部分。考虑下面这个例子：

``` js
// ...
status: function () {
    return this.validated
        ? this.okMsg
        : this.errMsg
}
// ...
```

如果 `validated` 在开始的时候是真，那么 `errMsg` 将不会访问，因此将不会收集到依赖。`okMsg` 反之亦然。为了绕开这个问题，你可以在你的getter中，简单手动操作访问那些可能无法访问的属性：

``` js
// ...
status: function () {
    // access dependencies explicitly
    this.okMsg
    this.errMsg
    return this.validated
        ? this.okMsg
        : this.errMsg
}
// ...
```

<p class="tip">不要担心这些问题会在行内表达式中，因为 Vue.js 的表达式解析器会为你处理这些问题。</p>

下一章节： [增加动画效果Adding Transition Effects](/guide/transitions.html)