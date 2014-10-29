title: 处理表单
type: guide
order: 7
---

你可以使用 `v-model` 指令来创建表单输入元素或是带有 `contenteditable`（译者注：也就是内容可编辑）属性的元素中的双向数据绑定。它会自动的选择正确的方法，基于输入的方式去更新元素。

**例子：**

``` html
<form id="demo">
    <!-- text -->
    <p><input type="text" v-model="msg"> {&#123;msg&#125;}</p>
    <!-- checkbox -->
    <p><input type="checkbox" v-model="checked"> {&#123;checked ? &quot;yes&quot; : &quot;no&quot;&#125;}</p>
    <!-- radio buttons -->
    <p>
        <input type="radio" name="picked" value="one" v-model="picked">
        <input type="radio" name="picked" value="two" v-model="picked">
        {&#123;picked&#125;}
    </p>
    <!-- select -->
    <p>
        <select v-model="selected">
            <option>one</option>
            <option>two</option>
        </select>
        {&#123;selected&#125;}
    </p>
    <!-- multiple select -->
    <p>
        <select v-model="multiSelect" multiple>
            <option>one</option>
            <option>two</option>
            <option>three</option>
        </select>
        {&#123;multiSelect&#125;}
    </p>
    <p>data: {&#123;$data&#125;}</p>
</form>
```

``` js
new Vue({
    el: '#demo',
    data: {
        msg      : 'hi!',
        checked  : true,
        picked   : 'one',
        selected : 'two',
        multiSelect: ['one', 'three']
    }
})
```

**结果**

<form id="demo"><p><input type="text" v-model="msg"> {&#123;msg&#125;}</p><p><input type="checkbox" v-model="checked"> {&#123;checked ? &quot;yes&quot; : &quot;no&quot;&#125;}</p><p><input type="radio" v-model="picked" name="picked" value="one"><input type="radio" v-model="picked" name="picked" value="two"> {&#123;picked&#125;}</p><p><select v-model="selected"><option>one</option><option>two</option></select> {&#123;selected&#125;}</p><p><select v-model="multiSelect" multiple><option>one</option><option>two</option><option>three</option></select>{&#123;multiSelect&#125;}</p><p>data: {&#123;$data&#125;}</p></form>
<script>
new Vue({
    el: '#demo',
    data: {
        msg      : 'hi!',
        checked  : true,
        picked   : 'one',
        selected : 'two',
        multiSelect: ['one', 'three']
    }
})
</script>

（译者注：这一章节好简单……）
下一章节： [计算属性Computed Properties](/guide/computed.html).