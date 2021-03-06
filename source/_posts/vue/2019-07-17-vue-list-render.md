---
layout: post
title:  "列表渲染"
date:   2019-07-17 08:47:22 +0800
categories: notes vue base
tags: Vue 基础 v-for key index data of v-if
excerpt: "列表渲染"
---

虽然Vue教程中叫v-for为列表渲染，但是我更愿意叫它循环渲染，因为听起来更熟悉。

## 循环数组

### &emsp;绑定key

v-for指令基于一个数组来渲染一个列表。v-for指令需要使用item in items形式的特殊语法，其中items是源数据数组，而item则是被迭代的数组元素的别名。这种用法十分常见，一些编程语言以及一些框架都使用这种表示循环的用法。但是你仅是这样是不行的，你还需要给循环的数组绑定一个key。如data中有一个数组：items: [1, 2, 3]，而模板需要`<div v-for="(names,index) in items" v-bind:key="index">`这样使用，names对应别名数组，而index对应当前的遍历到的索引。

是key绑定的原因是当Vue正在更新使用v-for渲染的元素列表时，它默认使用"就地更新"的策略。如果数据项的顺序被改变，Vue将不会移动DOM元素来匹配数据项的顺序，而是就地更新每个元素，并且确保它们在每个索引位置正确渲染。这个类似Vue1.x的track-by="$index"。

这个默认的模式是高效的，但是只适用于不依赖子组件状态或临时DOM状态(例如：表单输入值)的列表渲染输出。

为了给Vue一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一key属性。同时，不要使用对象或数组之类的非基本类型值作为v-for的key。请用字符串或数值类型的值。

### &emsp;与index和data属性

同样在这里也可以与index索引值以及data属性值同时使用计算。如`<div>{{ names }}+{{ index }}+{{show}}</div>`，show就是一个false的布尔值，将这些值都连在一起了，并请注意，迭代出来的值默认是<span style="color:orange">字符串</span>。

### &emsp;使用of

你也可以使用of来代替in作为分隔符，效果是一样的。

&emsp;

## 循环对象

你可以循环数组也可以循环对象，首先我们先写一个对象：ob: { 'last-name': 'King', 'name': 'Didnelpsun', 'password': '123' }。然后我们模板中写入：

```html
<div v-for="(p1,p2,p3) in ob" v-bind:key="p2">
    <div>{{p1}},{{p2}},{{p3}}</div>
</div>
```

v-for循环数组会提供三个参数，第一个参数代表对象值，第二个参数对应对象名，第三个参数对应循环到的索引。我们可以将key绑定到对象名上，也可以绑定在索引值上。

在遍历对象时，会按Object.keys()的结果遍历，但是不能保证它的结果在不同的JavaScript引擎下都一致。

&emsp;

## 数组更新

一般只有以下一些方法会触发数组的更新，从而让Vue对于元素重新渲染。

### &emsp;变异方法

会改变被这些方法调用的原始数组的方法，所以它们也将会触发视图更新。

+ push()
+ pop()
+ shift()
+ unshift()
+ splice()
+ sort()
+ reverse()

### &emsp;非变异方法

非变异方法就是返回一个新值，而不是改变原值的方法，而对于这样的方法，Vue将不会重新渲染元素，而如果我们要重新渲染元素，就必须将这些新返回的元素替换原来的元素。如filter()、concat()和slice()。

```vue
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```

Vue为了使得DOM元素得到最大范围的重用而实现了一些智能的启发式方法，所以用一个含有相同元素的数组去替换原来的数组是非常高效的操作。所以Vue不会将更改的元素全部销毁而重新渲染，而是会采取智能的方式来重构。

### &emsp;注意

因为是非响应式的，以下两种情况不会触发的Vue的更新：

+ 当你利用索引直接设置一个数组项时，例如：vm.items[indexOfItem] = newValue
+ 当你修改数组的长度时，例如：vm.items.length = newLength

对于设置值的解决方法：

+ Vue.set(vm.items, indexOfItem, newValue)
+ vm.$set(vm.items, indexOfItem, newValue) 这个方法和上一个方法是等效的，只是写的方式不同罢了。
+ vm.items.splice(indexOfItem, 1, newValue)

对于第二种情况，你可以使用splice方法：`vm.items.splice(newLength)`。如果组件化的使用方法，你会发现Vue会失效，那么你需要使用$set。

&emsp;

## 对象更新

还是由于JavaScript的限制，Vue不能检测对象属性的添加或删除，也就是说同数组一样，你也不可以通过直接赋值的方式来对对象值发生更改。对于已经创建的实例，Vue不允许动态添加根级别的响应式属性。但是，可以使用`Vue.set(object, propertyName, value)` / `vm.$set(object, propertyName, value)`方法向嵌套对象添加响应式属性。

有时你可能需要为已有对象赋值多个新属性，比如使用 Object.assign() 或 _.extend()。在这种情况下，你应该用两个对象的属性创建一个新的对象。所以，如果你想添加新的响应式属性，不要像这样：

```vue
Object.assign(vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

你应该这样做：

```vue
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```

也就是同样的重新对属性进行赋值。

&emsp;

## 显示处理结果

如果我们要对于数组或者对象进行排序或者计算再输出出来，而不改变它原有的数据，那么我们应该如何做呢？

### &emsp;使用方法

我们首先想到的方式就是利用一个方法来处理对应的数据，然后在模板中调用这个函数。

```vue
<li v-for="n in even(numbers)">{{ n }}</li>
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
methods: {
  even: function (numbers) {
    return numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

### &emsp;计算属性

或者我们也可以使用计算属性，先将对应的值处理好，使用的时候直接调用就可以了。

```vue
<li v-for="n in evenNumbers">{{ n }}</li>
data: {
  numbers: [ 1, 2, 3, 4, 5 ]
},
computed: {
  evenNumbers: function () {
    return this.numbers.filter(function (number) {
      return number % 2 === 0
    })
  }
}
```

&emsp;

## 值范围与元素循环

我们也可以直接在循环中按照值来循环，就比如v-for="n in 10"，就是循环十次。

同时我们也可以在\<template>元素中使用循环，来让这个元素中的元素循环多次。

&emsp;

## v-if与v-for

v-for指令的执行顺序是高于v-if的。意味着v-if将分别重复运行于每个v-for循环中。当你只想为部分项渲染节点时，这种优先级的机制会十分有用，如下：

```html
<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

上面的代码将只渲染未完成的 todo。而如果你的目的是有条件地跳过循环的执行，那么可以将v-if置于外层元素（或\<template>）上。如：

```html
<ul v-if="todos.length">
  <li v-for="todo in todos">
    {{ todo }}
  </li>
</ul>
<p v-else>No todos left!</p>
```
