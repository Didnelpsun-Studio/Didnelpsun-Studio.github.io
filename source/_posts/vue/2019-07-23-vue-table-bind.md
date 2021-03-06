---
layout: post
title:  "表单绑定"
date:   2019-07-23 17:39:07 +0800
categories: notes vue base
tags: Vue 基础 v-model v-bind v-for lazy number trim
excerpt: "表单绑定与双向传输"
---

这也是Vue最重要的概念，表单绑定更科学地来说应该是双向绑定。这也是Vue不同于React的一个方面，它首先从后端取出数据放到前端中展示，然后根据前端的更改再把数据返回到后端。在React中需要使用setData来更改数据，因为React默认是单向数据流，而Vue则不用，因为它是双向数据流。

你可以用v-model指令在表单\<input>、\<textarea>及\<select>元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。尽管有些神奇，但v-model本质上不过是语法糖。它负责监听用户的输入事件以更新数据，并对一些极端场景进行一些特殊处理。

<span style="color:orange">注意：</span>v-model会忽略所有表单元素的value、checked、selected、attribute的初始值而总是将Vue实例的数据作为数据来源。你应该通过JavaScript在组件的data选项中声明初始值。也就是说如果你在元素的value值中设置对应的值，那么这个值是无效的。

v-model在内部为不同的输入元素使用不同的属性并抛出不同的事件：

+ text和textarea元素使用value属性和input事件；
+ checkbox和radio使用checked属性和change事件；
+ select字段将value作为prop并将change作为事件。

## 基础用法

### &emsp;单文本

我们可以将文本框输入的值写到数据中也可以重新返回到前端页面，实现输入同步。

{% raw %}

```vue
<template>
    <div id="hello">
        <input v-model="message" placeholder="编辑">
        <p>{{message}}</p>
    </div>
</template>

<script>
export default {
    name: 'HelloWorld',
    data () {
        return {
            message: ''
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
</style>
```

{% endraw %}

首先input输入框的默认值是由v-model来绑定的，它将data中message值绑定到input元素，并且下面的message直接展示data属性中的message值，随着input输入的改变，data属性message值也在改变，与此同时页面上也在改变。

如之前说的，你在input元素的value值上设置是没有用的。

### &emsp;多文本

与input元素一样，也是直接用v-model指令绑定就可以了：`<textarea v-model="message" placeholder="编辑"></textarea>`

### &emsp;复选框

我们可以根据复选框的是否选择来获取对应的布尔值：`<input type="checkbox" id="chose" v-model="choose"><label for="chose">{{choose}}</label>`，其中choose是data中的属性，默认为false，如果你点击了这个复选框，那么它会变成true。当然你也可以默认为true，那么复选框默认就是被选择的状态。

正常的复选框，是使用v-model绑定一个数组，data的names为一个空数组：

{% raw %}

```html
<div id='test'>
    <input type="checkbox" id="id1" value="红" v-model="names">
    <label for="id1">红</label>
    <input type="checkbox" id="id2" value="黄" v-model="names">
    <label for="id2">黄</label>
    <input type="checkbox" id="id3" value="蓝" v-model="names">
    <label for="id3">蓝</label>
    <br>
    <span>已选择: {{ names }}</span>
</div>
```

{% endraw %}

使用复选框，如果对应的复选框为true，将对应的value值传入names这个数组。

### &emsp;单选框

同理，单选框与复选框类似，直接将type属性变为radio，然后将绑定的属性变为字符串类型的name。

### &emsp;下拉框

#### &emsp;&emsp;单选

{% raw %}

```html
<div id="test">
    <select v-model="name">
        <option disabled value="">请选择</option>
        <option>A</option>
        <option>B</option>
        <option>C</option>
    </select>
    <span>已选: {{ name }}</span>
</div>
```

{% endraw %}

其中name为data中一个字符串类型属性。将v-model绑定到name，然后下拉框中选择对应的option元素，选中的option元素中的数值将传入对应的name属性中。

<span style="color:red">警告：</span>如果v-model表达式的初始值未能匹配任何选项，\<select>元素将被渲染为"未选中"状态。在iOS中，这会使用户无法选择第一个选项。因为这样的情况下，iOS不会触发change事件。因此，更推荐像上面这样提供一个值为空的禁用选项。

#### &emsp;&emsp;多选

{% raw %}

```html
<select v-model="names" multiple >
    <option>A</option>
    <option>B</option>
    <option>C</option>
</select>
<br>
<span>已选: {{ names }}</span>
```

{% endraw %}

单选和多选差不多，只是加上multiple属性，然后将data属性换成names就可以了。还要注意一点，你单纯的点击选项是不能实现多选的，你需要在选了一项后按住ctrl按键再点击一项才可以多选，或者按住shift，再点击一项，将这次点击和上次点击的选项间的所有选项都选中。

&emsp;

## 值绑定

对于单选按钮，复选框及选择框的选项，v-model绑定的值通常是静态字符串(对于复选框也可以是布尔值)，但是有时我们可能想把值绑定到Vue实例的一个动态属性上，这时可以用v-bind实现，并且这个属性的值可以不是字符串。也就是说你可以使用其他指令来配合使用。

### &emsp;true-value和false-value

```html
<input
  type="checkbox"
  v-model="chose"
  true-value="yes"
  false-value="no"
>
```

和原来的复选框不同，它会将选中选框的true值变为yes值，而false值变为no值。

这里的true-value和false-valueattribute并不会影响输入控件的value值，因为浏览器在提交表单时并不会包含未被选中的复选框。如果要确保表单中这两个值中的一个能够被提交，(即"yes"或"no")，请换用单选按钮。

### &emsp;v-bind指令配合

{% raw %}

```vue
<template>
    <div id="hello">
        <input type="radio" v-model="chose" v-bind:value="a">
        <input type="radio" v-model="chose" v-bind:value="b">
        <p>{{chose}}</p>
    </div>
</template>

<script>
export default {
    name: 'HelloWorld',
    data () {
        return {
            chose: '',
            a: 'A',
            b: 'B'
        }
    }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
</style>
```

{% endraw %}

### &emsp;与v-bind、v-for指令配合

在多选时，你也可以使用v-bind和v-for指令，来循环给元素赋值：

{% raw %}

```vue
<template>
    <div id="hello">
        <div id="test">
    <select v-model="names" multiple >
        <option v-for="values in ovalue" v-bind:key="values.word" v-bind:value="values.value">
            {{values.word}}
        </option>
  </select>
  <br>
    <span>已选: {{names}}</span>
    </div>
    </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      names: [],
      name: '',
      ovalue: [
        { word: '红', value: 'red' },
        { word: '黄', value: 'yellow' },
        { word: '蓝', value: 'blue' }
      ]
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
</style>
```

{% endraw %}

&emsp;

## 表单修饰符

### &emsp;lazy修饰符

在默认情况下，v-model在每次input事件触发后将输入框的值与数据进行同步(除了上述输入法组合文字时)。你可以添加lazy修饰符，从而转为在change事件之后进行同步，在"change"时而非"input"时更新：\<input v-model.lazy="msg">

### &emsp;number修饰符

如果想自动将用户的输入值转为数值类型，可以给v-model添加number修饰符：\<input v-model.number="age" type="number">

这通常很有用，因为即使在type="number"时，HTML输入元素的值也总会返回字符串。如果这个值无法被parseFloat()解析，则会返回原始的值。

### &emsp;trim修饰符

如果要自动过滤用户输入的首尾空白字符，可以给v-model添加trim修饰符：\<input v-model.trim="msg">
