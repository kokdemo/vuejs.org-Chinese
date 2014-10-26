title: 显示列表
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

When you are using non-mutating methods, e.g. `filter()`, `concat()` or `slice()`, the returned Array will be a different instance. In that case, you can just set the value to the new Array:

``` js
demo.items = demo.items.filter(function (item) {
    return item.childMsg.match(/Hello/)
})
```

You might think this will blow away the existing DOM and re-build everything. But worry not - Vue.js uses some smart algorithms internally that reuse existing ViewModels and DOM nodes, and incurs the least number of DOM manipulations possible. Also, because of update batching, multiple changes to an Array in the same event loop will only trigger one comparison.

## Array Filters

Sometimes we only need to display a filtered or sorted version of the Array without actually mutating or resetting the original data. Vue provides two built-in filters to simplify such usage: `filterBy` and `orderBy`. Check out their [documentations](/api/filters.html#filterBy) for more details.

## Iterating Through An Object

Starting in Vue.js v0.8.8, you can also use `v-repeat` to iterate through the properties of an Object. Each repeated instance will have a special property `$key`. For primitive values, you also get `$value` which is similar to primitive values in Arrays.

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

**Result:**
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

## $add() and $delete()

In ECMAScript 5 there is no way to detect when a new property is added to an Object, or when a property is deleted from an Object. To counter for that, observed objects will be augmented with two methods: `$add(key, value)` and `$delete(key)`. These methods can be used to add / delete properties from observed objects while triggering the desired View updates.

Next up: [Listening for Events](/guide/events.html).
