title: 我要替天行道，增加动画效果
type: guide
order: 9
---

通过 Vus.js 的动画钩子，当一个元素被插入或者移除出一个DOM的时候，你可以申请自动动画效果。Vue.js中有三个实现动画的选项：CSS 变化，CSS动画，Javascript函数。

<p class="tip">所有的 Vue.js 动画都只会被 Vue.js所申请的DOM操作所触发，也包括内置的指令，举例来说，`v-if` 或者是通过试图模型内置的函数，举例来说，`vm.$appendTo()`。</p>

## CSS 变形

为了使一个元素中的CSS变形可用，只需要简单给它一个空的 `v-transition` 指令：

``` html
<p class="msg" v-if="show" v-transition>Hello!</p>
```

你也同时需要为 `v-enter` 和 `v-leave` 类提供CSS（这个类名可以通过 `Vue.config()` 来设置）：

``` css
.msg {
    transition: all .3s ease;
    height: 30px;
    padding: 10px;
    background-color: #eee;
    overflow: hidden;
}
.msg.v-enter, .msg.v-leave {
    height: 0;
    padding: 0 10px;
    opacity: 0;
}
```

<div id="demo"><p class="msg" v-if="show" v-transition>Hello!</p><button v-on="click: show = !show">Toggle</button></div>

<style>
.msg {
    transition: all .5s ease;
    height: 30px;
    background-color: #eee;
    overflow: hidden;
    padding: 10px;
    margin: 0 !important;
}
.msg.v-enter, .msg.v-leave {
    height: 0;
    padding: 0 10px;
    opacity: 0;
}
</style>

<script>
new Vue({
    el: '#demo',
    data: { show: true }
})
</script>

现在当 `show` 属性变化的时候，Vue.js将会插入或者相应的 `<p>` 元素，并且按照如下规则添加变化的类：

- 当 `show` 为假时，Vue.js将会：
    1. 为这个元素添加 `v-leave` 类，并触发动画；
    2. 等待动画完成；（监听 `transitionend` 的事件）
    3. 移除掉DOM上的合格元素和对应的`v-leave` 类。

- 当 `show` 为真时，Vus.js 将会：
    1. 为元素添加 `v-enter` 类。
    2. 插入 DOM 中。
    3. `v-enter`实际的发挥效果，CSS布局开始变化。
    4. 移除 `v-enter` 类，触发一个动画回归元素的原始状态。（译者注：这里的动画方法也是最普遍实用的方法了。）

<p class="tip">要确保目标元素的CSS动画样式正确的设置了，并且能够产生一个 `transitionend` 事件，这是很重要的。不然Vue.js 没办法区分啥时候动画结束。</p>

## CSS 动画

（译者注：这里有一个CSS变形，有一个CSS动画，两者并不是一套系统，但是我统一的把整个过程称之为动画，请不要打我……）

CSS 动画的使用和CSS 变形有一些相似，但是使用 `v-animation` 指令。不同的地方在于， `v-enter` 并不会在元素插入之后立马就被移除掉，而是需要使用一个 `animationend` 回调。

**栗子：** (这里省略了CSS的前缀（译者注：动画和变形的前缀简直就是噩梦……）)

``` html
<p class="animated" v-if="show" v-animation>Look at me!</p>
```

``` css
.animated {
    display: inline-block;
}

.animated.v-enter {
    animation: fadein .5s;
}

.animated.v-leave {
    animation: fadeout .5s;
}

@keyframes fadein {
    0% {
        transform: scale(0);
    }
    50% {
        transform: scale(1.5);
    }
    100% {
        transform: scale(1);
    }
}

@keyframes fadeout {
    0% {
        transform: scale(1);
    }
    50% {
        transform: scale(1.5);
    }
    100% {
        transform: scale(0);
    }
}
```

<div id="anim" class="demo"><span class="animated" v-if="show" v-animation>Look at me!</span><br><button v-on="click: show = !show">Toggle</button></div>

<style>
    .animated {
        display: inline-block;
    }
    .animated.v-enter {
        -webkit-animation: fadein .5s;
        animation: fadein .5s;
    }
    .animated.v-leave {
        -webkit-animation: fadeout .5s;
        animation: fadeout .5s;
    }
    @keyframes fadein {
        0% {
            transform: scale(0);
            -webkit-transform: scale(0);
        }
        50% {
            transform: scale(1.5);
            -webkit-transform: scale(1.5);
        }
        100% {
            transform: scale(1);
            -webkit-transform: scale(1);
        }
    }
    @keyframes fadeout {
        0% {
            transform: scale(1);
            -webkit-transform: scale(1);
        }
        50% {
            transform: scale(1.5);
            -webkit-transform: scale(1.5);
        }
        100% {
            transform: scale(0);
            -webkit-transform: scale(0);
        }
    }
    @-webkit-keyframes fadein {
        0% {
            -webkit-transform: scale(0);
        }
        50% {
            -webkit-transform: scale(1.5);
        }
        100% {
            -webkit-transform: scale(1);
        }
    }
    @-webkit-keyframes fadeout {
        0% {
            -webkit-transform: scale(1);
        }
        50% {
            -webkit-transform: scale(1.5);
        }
        100% {
            -webkit-transform: scale(0);
        }
    }
</style>

<script>
new Vue({
    el: '#anim',
    data: { show: true }
})
</script>

## JavaScript 函数

Vue.js 提供一个方法，可以再元素的插入或者是移除中来调用任意的JS函数。为了使用这个魔法，你需要首先声明你的效果函数：

``` js
Vue.effect('my-effect', {
    enter: function (el, insert, timeout) {
        // insert() will actually insert the element
    },
    leave: function (el, remove, timeout) {
        // remove() will actually remove the element
    }
})
```

第三个参数，`timeout`，是一个 `setTimeout` 的封装版本。你应该使用它来代替 `setTimeout`，以便当一个效果被取消时，清楚相关联的计时器。

然后你可以通过给 `v-effect` 提供id来使用它了：

``` html
<p v-effect="my-effect"></p>
```

下一章节： [组装视图模型](/guide/composition.html).