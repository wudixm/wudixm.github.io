[原文](https://learning.dcloud.io/#/?vid=4)

### 第3节

#### obj.freeze()

用freeze() 方法，会阻止修改现有的属性，也意味着响应系统不再追踪变化。

#### 暴露实例属性和方法

都使用`$` 做为前缀，以便与用户定义的属性区分。

```js
var vm = new Vue({
  el: "#app",
  data : data
});
vm.$data.a = "test";// 用vm.$ 符号可以访问Vue 全局变量里的值
vm.$el === document.getElementById("app");  //true
```

#### watch 方法

```js
var data = {a:1};
var vm = new Vue({
  el: "#app",
  data : data
});
vm.$watch('a', function(newVal, oldVal){
  console.log(newVal, oldVal);
})

vm.$data.a = "test";// 用vm.$ 符号可以访问Vue 全局变量里的值

```

### 第4节

#### 实例生命周期钩子

每个 Vue 实例在被创建时都要经过一系列的初始化过程——例如，需要设置数据监听、编译模板、将实例挂载到 DOM 并在数据变化时更新 DOM 等。同时在这个过程中也会运行一些叫做**生命周期钩子**的函数，这给了用户在不同阶段添加自己的代码的机会。

比如 [`created`](https://cn.vuejs.org/v2/api/#created) 钩子可以用来在一个实例被创建之后执行代码：

```js
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
})
// => "a is: 1"
```

也有一些其它的钩子，在实例生命周期的不同阶段被调用，如 [`mounted`](https://cn.vuejs.org/v2/api/#mounted)、[`updated`](https://cn.vuejs.org/v2/api/#updated) 和 [`destroyed`](https://cn.vuejs.org/v2/api/#destroyed)。生命周期钩子的 `this` 上下文指向调用它的 Vue 实例。

```js
new Vue({
  data: {
    a: 1
  },
  created: function () {
    // `this` 指向 vm 实例
    console.log('a is: ' + this.a)
  }
  // 有beforeCreate, create, beforeMount, mounted, beforeUpdate, updated 这些生命周期方法
})

```

### 第5节 模板语法-插值

#### v-once 只能改动值一次

```html
<div id="app-6" v-once>
  <p>{{ message }}</p>
</div>
<script>
var app5 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue.js!'
  },
})

app5.message = 'fuck';
</script>
```

#### v-html 让变量变成html元素而不是普通文本

```html
<div id="app-7">
  <p>{{ rawHtml }}</p>
  <p v-html="rawHtml2"></p>
</div>
<script>
var app7 = new Vue({
  el: '#app-7',
  data: {
      rawHtml: '<span style="color:red">inside_span1</span>',
      rawHtml2: '<span style="color:red">inside_span2</span>'
  },
})
</script>
```

#### v-bind 动态给html标签绑定属性

```html
<div id="app-8">
    <div v-bind:class="color">test_v_bind</div>
</div>
<script>
var app7 = new Vue({
  el: '#app-8',
  data: {
      color: "red",
  },
})
</script>
<style type="text/css">
.red{ color:red }
.blue{ color:blue;font-size:100px; }
</style>
```

#### 在vue中使用js表达式

```js
{{ number+1 }}
{{ ok ? "YES" : "NO"}}
{{ message.split('').reverse().join('')}}
```

