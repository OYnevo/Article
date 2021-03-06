# 基础
- [字面量语法VS动态语法](#字面量语法vs动态语法)
- [单向数据流](#单向数据流)
- [编写可复用组件](#编写可复用组件)
- [内联模版](#内联模版)

![img](https://cn.vuejs.org/images/props-events.png)

## 字面量语法VS动态语法
##### [回到索引](#基础)
初学者常犯的一个错误是使用字面量语法传递数值：
``` html
<!-- 传递了一个字符串 "1" -->
<comp some-prop="1"></comp>
```

因为它是一个字面 prop ，它的值是字符串 ``"1"`` 而不是number。如果想传递一个实际的number，需要使用 ``v-bind`` ，从而让它的值被当作 JavaScript 表达式计算：
``` html
<!-- 传递实际的 number -->
<comp v-bind:some-prop="1"></comp>
```
> 对于 布尔值 也是如此。``<comp v-bind:some-prop="true"></comp>``

---

## 单向数据流
##### [回到索引](#基础)

prop 是单向绑定的：当父组件的属性变化时，将传导给子组件，但是不会反过来。这是为了防止子组件无意修改了父组件的状态——这会让应用的数据流难以理解。

另外，每次父组件更新时，子组件的所有 prop 都会更新为最新值。这意味着你不应该在子组件内部改变 prop 。如果你这么做了，Vue 会在控制台给出警告。

为什么我们会有修改prop中数据的冲动呢？通常是这两种原因：
- prop 作为初始值传入后，子组件想把它当作局部数据来用；
- prop 作为初始值传入，由子组件处理成其它数据输出。

对这两种原因，正确的应对方式是：

1.定义一个局部变量，并用 prop 的值初始化它：
``` javascript
props: ['initialCounter'],
data: function () {
  return { counter: this.initialCounter }
}
```

2.定义一个计算属性，处理 prop 的值并返回。
``` javascript
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

> ⚠️ 注意在 JavaScript 中对象和数组是引用类型，指向同一个内存空间，如果 prop 是一个对象或数组，在子组件内部改变它会影响父组件的状态。

## 编写可复用组件
##### [回到索引](#基础)

在编写组件时，记住是否要复用组件有好处。

一次性组件跟其它组件紧密耦合没关系，但是可复用组件应当定义一个清晰的公开接口。

> 什么是耦合？\
组件与父组件之间相互依赖，叫做 ``紧密耦合``；\
可复用的组件通常api开放，不需要依赖父组件，只需要父组件传递相关的参数，就可以运行，这个时候叫做 ``解耦``

Vue 组件的 API 来自三部分 - props, events 和 slots ：
- **Props** 允许外部环境传递数据给组件
- **Events** 允许组件触发外部环境的副作用
- **Slots** 允许外部环境将额外的内容组合在组件中。

## 内联模版
##### [回到索引](#基础)

如果子组件有 *inline-template* 特性，组件将把它的内容当作它的模板，而不是把它当作分发内容。这让模板更灵活。

``` html
<my-component inline-template>
  <div>
    <p>These are compiled as the component's own template.</p>
    <p>Not parent's transclusion content.</p>
  </div>
</my-component>
```
但是 inline-template 让模板的作用域难以理解。最佳实践是使用 template 选项在组件内定义模板或者在 ``.vue`` 文件中使用 ``template`` 元素。

## x-Template
##### [回到索引](#基础)

另一种定义模版的方式是在 JavaScript 标签里使用 ``text/x-template`` 类型，并且指定一个id。例如：

``` html 
<script type="text/x-template" id="hello-world-template">
  <p>Hello hello hello</p>
</script>
```

``` javascript
Vue.component('hello-world', {
  template: '#hello-world-template'
})
```
这在有很多模版或者小的应用中有用，否则应该避免使用，因为它将模版和组件的其他定义隔离了。