title: 不是假发，是列表
type: guide
order: 5
---

通过访问一个视图模型中的数组，你可以使用 `v-repeat` 指令循环生成模型元素。对于这个数组中的每个对象，指令都会使用这个对象生成一个子视图模型作为它的数据对象。通过这个模板元素，你可以访问到子视图模型和他的父视图模型中的属性。此外，你也可以访问当前子视图元素的 `$index` 属性，这是数组中他的数据对象的索引。

**又是一个栗子：**

``` html
<ul id="demo">
    <li v-repeat="items" class="item-{&#123;$index&#125;}">
        {&#123;$index&#125;} - {&#123;parentMsg&#125;} {&#123;childMsg&#125;}
    </li>
</ul>
```

``` js
var demo = new Vue({
    el: '#demo',
    data: {
        parentMsg: 'Hello',
        items: [
            { childMsg: 'Foo' },
            { childMsg: 'Bar' }
        ]
    }
})
```

**结果：**

<ul id="demo"><li v-repeat="items" class="item-{&#123;$index&#125;}">{&#123;$index&#125;} - {&#123;parentMsg&#125;} {&#123;childMsg&#125;}</li></ul>
<script>
var demo = new Vue({
    el: '#demo',
    data: {
        parentMsg: 'Hello',
        items: [
            { childMsg: 'Foo' },
            { childMsg: 'Bar' }
        ]
    }
})
</script>

## 数组的初始值

对于数组包含的初始值，你可以通过 `$value` 来简单的访问：

``` html
<ul id="tags">
    <li v-repeat="tags">
        {&#123;$value&#125;}
    </li>
</ul>
```

``` js
new Vue({
    el: '#tags',
    data: {
        tags: ['JavaScript', 'MVVM', 'Vue.js']
    }
})
```

**结果：**
<ul id="tags" class="demo"><li v-repeat="tags">{&#123;$value&#125;}</li></ul>
<script>
new Vue({
    el: '#tags',
    data: {
        tags: ['JavaScript', 'MVVM', 'Vue.js']
    }
})
</script>

## 使用一个标识符

有的时候我们可能需要更多的显式变量访问而不是隐式的回退到父作用域。你可以通过提供一个参数给 `v-repeat` 指令，当个体被重复声明的时候使用它作为一个标识符来达到访问更多显示变量的目的：

``` html
<ul id="users">
    <!-- think of this as "for each user in users" -->
    <li v-repeat="user: users">
        {&#123;user.name&#125;} {&#123;user.email&#125;}
    </li>
</ul>
```

## 变异方法

在内部机制中，Vue.js 会拦截一个被监听数组的变异方法（`push()`, `pop()`, `shift()`, `unshift()`, `splice()`, `sort()` 以及`reverse()`），这样他们也会触发视图更新。

``` js
// the DOM will be updated accordingly
demo.items.unshift({ childMsg: 'Baz' })
demo.items.pop()
```

你应该避免直接与索引设置数据绑定数组的元素，因为这些将不会被 Vue.js 检测到。因此，使用 `$set()` 方法替代之。Vue.js 通过两个方便的函数来增强监听数组：`$set()` 和 `$remove()` ：

### $set( index, value )

在数组中设置一个值：

``` js
// same as `demo.items[0] = ...` but triggers view update
demo.items.$set(0, { childMsg: 'Changed!'})
```

### $remove( index | value )

`$remove()` 仅仅是一个 `splice()` 的语法糖。它会根据所给的索引移除相应的元素。当参数不是一个数字的时候，`$remove()` 将会在数组中搜索这个值，并且移除掉对应的第一个匹配值。

``` js
// remove the item at index 0
demo.items.$remove(0)
```

## 设置一个新数组

当你使用一些非变异的方法的时候，比如`filter()`, `concat()` 或者 `slice()`，返回的数组将会是一个不同的实例。在这种情况下，你只能给新的数组中设置值：

``` js
demo.items = demo.items.filter(function (item) {
    return item.childMsg.match(/Hello/)
})
```
你可能认为这将会影响已有的DOM并重建所有的东西。但是不要担心，Vue.js使用了某些黑魔法（一些内在算法），会重新利用已经存在的视图模型和DOM节点，这会将DOM操作尽可能的减少到最小。同时，因为批量的更新，对一个事件循环中的数组的多重改变也将只会触发一次比对。

## 数组过滤器

有的时候我们仅仅需要显示一个过滤或者分类过的数组，并不需要对原始数据做改变或者重置。Vue 提供了两种内置的过滤器来达到该目的：`filterBy` 和 `orderBy` 。你可以围观文档 [documentations](/api/filters.html#filterBy) 获得更多信息。

## 通过一个对象进行循环

从 Vue.js 的 v0.8.8版本开始，你可以通过一个对象的属性来循环使用 `v-repeat` 。每一个循环实例都有一个特殊的属性 `$key` 。对于原始值，你可以使用 `$value` 来过的与数组中初始值相同的数值。

``` html
<ul id="repeat-object">
    <li v-repeat="primitiveValues">{&#123;$key&#125;} : {&#123;$value&#125;}</li>
    <li>===</li>
    <li v-repeat="objectValues">{&#123;$key&#125;} : {&#123;msg&#125;}</li>
</ul>
```

``` js
new Vue({
    el: '#repeat-object',
    data: {
        primitiveValues: {
            FirstName: 'John',
            LastName: 'Doe',
            Age: 30
        },
        objectValues: {
            one: {
                msg: 'Hello'
            },
            two: {
                msg: 'Bye'
            }
        }
    }
})
```

**结果：**
<ul id="repeat-object" class="demo"><li v-repeat="primitiveValues">{&#123;$key&#125;} : {&#123;$value&#125;}</li><li>===</li><li v-repeat="objectValues">{&#123;$key&#125;} : {&#123;msg&#125;}</li></ul>
<script>
new Vue({
    el: '#repeat-object',
    data: {
        primitiveValues: {
            FirstName: 'John',
            LastName: 'Doe',
            Age: 30
        },
        objectValues: {
            one: {
                msg: 'Hello'
            },
            two: {
                msg: 'Bye'
            }
        }
    }
})
</script>

## $add() 和 $delete()

在 ECMAScript 5 中是没有办法检测出一个新的属性被添加到一个对象中的，抑或是一个属性从对象中被删除。为了解决这一问题，被监测的对象将会通过两个方法进行扩展：`$add(key, value)` 和 `$delete(key)`。这些方法用于在对应的视图模型更新被触发时，从监测对象中增加或者删除这些属性。

下一章节： [事件的监听Listening for Events](/guide/events.html).
