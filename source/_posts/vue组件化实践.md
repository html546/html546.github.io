---
title: vue组件化实践
date: 2020-03-16 09:23:51
tags:
    - 学习笔记
---


### 组件化

组件化是vue的核心思想，主要目的是为了代码重用。
![avatar](https://cn.vuejs.org/images/components.png)

### 组件通信
#### 父组件=>子组件

* 属性props

```javascript
    // child
    props:{msg:String}
    // parent
    <Helloworld msg="Welcome to Your Vue.js App">
```

* 引用refs

```javascript
    // parent
    <Helloworld ref="hw" />>

    this.$refs.hw.xx = 'xxx'
```

* 子元素$children

```javascript
// parent
this.$children[0].xx = 'xxx'
```

> 子元素不保证顺序

#### 自组件=>父组件:自定义事件

```javascript
// child
this.$emit('add',good)

// parent
<Cart @add="cartAdd($event)"></Cart>
```

#### 兄弟组件：通过共同祖辈组件
通过共同的祖辈组件搭桥,$parent或$root

```javascript
// brother1
this.$parent.$on('foo',handle)
// brother2
this.$parent.$emit('foo')
```

#### 祖先和后代之间
由于嵌套层数过多,传递props不切实际,vue提供了`provide/inject` Api完成该任务

- provide/inject:能够实现祖先给后代传值

```javascript
// ancestor
provide(){
    return {
        foo:'foo'
    }
}
// descendant
inject:['foo']
```

> 注意：`provide`和`inject`主要为高阶插件/组件库提供用例.并**不推荐直接用于应用程序代码**中。我们更多会在开源组件库中见到。
> 但是，反过来想要后代给祖先传值这种方案就不行了

#### 任意两个组件之间:事件总线或vuex
- 事件总线:创建一个Bus类负责事件派发、监听和回调管理

```javascript
// Bus:事件派发、监听和回调管理
class Bus{
    constructor(){
        // {
        //     eventName1:[fn1,fn2],
        //     eventName2:[fn3,fn4]
        // }
        this.callbacks = {}
    }
    $on(name,fn){
        this.callbacks[name] = this.callback[name]||[]
        this.callbacks[name].push(fn);
    }
    $emit(name,args){
        if(this.callbacks[name]){
            this.callbacks[name].forEach(cb=>cb(args))
        }
    }
}

// main.js
Vue.prototype.$bus = new Bus()

// child1
this.$bus.$on('foo',handle);
// child2
this.$bus.$emit('foo');
```

> 实践中可以用Vuex代替Bus,因为它已经实现了相应功能

- vuex:创建唯一的全局数据管理者store,通过它管理数据并通知组件状态变更

### 插槽
插槽语法是Vue实现的内容分发API,用于复合组件开发。该技术在通用组件库开发中有大量应用。

![插槽](https://ss1.bdstatic.com/70cFuXSh_Q1YnxGkpoWK1HF6hhy/it/u=398760744,2190852689&fm=26&gp=0.jpg0)

> Vue2.6.0之后采用全新v-slot语法取代之前的slot、slot-scope

#### 匿名插槽

```javascript
// comp1
<div>
    <slot></slot>
</div>

// parent
<comp>hello</comp>
```

#### 具名插槽
将内容分发到子组件指定位置

```html
// comp2
<div>
    <slot></slot>
    <slot name="content"></slot>
</div>

// parent
<Comp2>
    <!-- 默认插槽用default做参数 -->
    <template v-slot:default>具名插槽</template>
    <!-- 具名插槽用插槽名做参数 -->
    <template v-slot:content>内容...</template>
</Comp2>
```

#### 作用域插槽
分发内容要用到子组件中的数据

```html
<!-- comp3 -->
<div>
    <slot :foo="foo"></slot>    
</div>

<!-- parent -->
<Comp3>
    <!-- 把v-slot的值指定为作用域上下文对象 -->
    <template v-slot:default="slotProps">
        来自子组件数据:{{slotProps.foo}
    </template>
</Comp3>
```

### 组件化实战

### 实现Form、FormItem、Input

#### 最终效果：[Element表单](https://element.eleme.cn/#/zh-CN/component/form)

**Form** 管理数据模型-model、校验规则-rules、全局校验方法-validate
**FormItem**显示标签-label、执行校验-props和显示校验结果
**Input**绑定数据模型-v-model、通知FormItem执行校验

需要思考的几个问题?
1. Input是自定义组件,它是怎么实现数据绑定的?
2. FormItem怎么知道何时执行校验,校验的数据和规则怎么得到?
3. Form怎么进行全局校验?它用什么办法把数据模型和校验规则传递给内部组件?

#### 实现KInput
- 任务1：实现自定义组件双向绑定功能
  > v-model是语法糖，实现自定义组件双向绑定需要制定:value和@input即可
- 任务2：值发生变化能够通知FormItem组件
- 任务3：input各种特性设定,如type,placeholder等

创建**components/form/KInput.vue**

```html
<template>
  <div>
    <!-- 自定义组件要实现v-model必须实现:value,@input -->
    <!-- $attrs存储的是props之外的部分 -->
    <input :value="value" @input="onInput" v-bind="$attrs">
  </div>
</template>


<script>
export default {
  inheritAttrs:false, //避免顶层容器继承属性
  props: {
    value: {
      type: String,
      default: ""
    }
  },
  data() {
    return {};
  },
  methods: {
    // 通知父组件数值变化
    onInput(e) {
      this.$emit("input", e.target.value);
    }
  }
};
</script>

<style lang="less" scoped>
</style>
```

#### 使用KInput
创建components/form/index.vue,添加如下代码:
```html
<template>
  <div>
    <KInput v-model="model.username"></KInput>
    <KInput v-model="model.password" type="password"></KInput>
  </div>
</template>


<script>
import KInput from "./KInput.vue";
export default {
  data() {
    return {
      model: {
        username: "tom",
        password: ""
      }
    };
  },
  components: {
    KInput
  }
};
</script>

<style lang="less" scoped>
</style>
```

#### 实现KFormItem
- 任务1：给Input预留插槽-slot
- 任务2：能够展示label和校验信息
- 任务3：能够进行校验

创建components/form/KFormItem.vue

```html
<template>
  <div>
      <label v-if="label">
        {{label}}
      </label>
      <slot></slot>
      <!-- 校验信息 -->
      <p v-if="errorMessage">
        {{errorMessage}}
      </p>
  </div>
</template>


<script>
export default {
  props: {
    label: {
      type: String,
      default: ""
    },
    errorMessage: {
      type: String,
      default: ""
    }
  },
  data() {
    return {};
  }
};
</script>

<style lang="less" scoped>
</style>
```

#### 数据校验
- 思路：校验发生在FormItem,它需要知道合适校验(让Input通知它),还需要知道怎么校验(注入校验规则)
- 任务1：Input通知校验
```javascript
onInput(e){
    //...
    //$parent指FormItem
    this.$parent.$emit('validate');
}
```
- 任务2：FormItem监听校验通知,获取规则并执行校验

```javascript
inject: ['form'], // 注入
 mounted() {
    // 监听校验时间、并执行校验
    this.$on("validate", () => {
      this.validate();
    });
  },
  methods: {
    validate() {
      // 执行组件校验
      // 1.获取校验规则
      const rule = this.form.rules[this.prop];

      // 2.获取数据
      const value = this.form.model[this.prop];
```

- 安装async-validator: npm i async-validator -S

```javascript
import Schema from 'async-validator'
validate() {
      // 执行组件校验
      // 1.获取校验规则
      const rule = this.form.rules[this.prop];

      // 2.获取数据
      const value = this.form.model[this.prop];

      // 3.执行校验
      const desc = {
        [this.prop]:rule
      };
      const schema = new Schema(desc);
      // 参数1是值,参数2是校验错误对象数组
      // 返回的Promis<resolve>
      return schema.validate({[this.prop]:value},(errors)=>{
          if(errors){
            // 有错
            this.errorMessage = errors[0].message;
          }else{
            // 没错,清除错误信息
            this.errorMessage = '';
          }
        })
    }
```
