title: 增加动画效果
type: guide
order: 9
---

通过 Vus.js 的动画钩子，当一个元素被插入或者移除出一个DOM的时候，你可以申请自动动画效果。Vue.js中有三个实现动画的选项：CSS 变化，CSS动画，Javascript函数。

<p class="tip">所有的 Vue.js 动画都只会被 Vue.js所申请的DOM操作所触发，也包括内置的指令，举例来说，`v-if` 或者是通过试图模型内置的函数，举例来说，`vm.$appendTo()`。</p>

## CSS 变化

为了使一个元素中的CSS变化可用，只需要简单给它一个空的 `v-transition` 指令：

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

现在当 `show` 属性变化的时候，Vue.js将会插入或者相应的 `<p>` 元素，并且按照如下规则申请变化的类：

- 当 `show` 为假时，Vue.js将会：
    1. 为这个元素申请 `v-leave` 类，并触发动画；
    2. 等待动画完成；（监听 `transitionend` 的事件）
    3. 移除掉DOM上的合格元素和对应的`v-leave` 类。

- When `show` becomes true, Vue.js will:
    1. Apply `v-enter` class to the element;
    2. Insert it into the DOM;
    3. Force a CSS layout so `v-enter` is actually applied;
    4. Remove the `v-enter` class to trigger a transition back to the element's original state.


<p class="tip">It is important to ensure that the target element's CSS transition rules are properly set and it will fire a `transitionend` event. Otherwise Vue.js will not be able to determine when the transition is finished.</p>

## CSS Animations

CSS animations are applied in a similar fashion with transitions, but using the `v-animation` directive. The difference is that `v-enter` is not removed immediately after the element is inserted, but on an `animationend` callback.

**Example:** (omitting prefixed CSS rules here)

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

## JavaScript Functions

Vue.js provides a way to call arbitrary JavaScript functions during element insertion/removal. To do that you first need to register your effect functions:

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

The third argument, `timeout`, is a wrapped version of `setTimeout`. You should use it in place of `setTimeout` so that when an effect is cancelled, its associated timers can be cleared.

Then you can use it by providing the effect id to `v-effect`:

``` html
<p v-effect="my-effect"></p>
```

Next: [Composing ViewModels](/guide/composition.html).