---
title: React学习(二)
date: 2019-03-29 14:17:32
tags:
	- React
categories: 
    - react
---
#### Redux三大原则

1. 单一数据源


   - 整个应用的state被储存在一棵object tree中，并且这个object tree只存在于唯一一个store中。


2. State是只读的
   
   - 唯一改变state的方法就是触发action，action是一个用于描述已发生事件的普通对象。

3. 使用纯函数来执行修改

   - 为了描述action如何改变state tree ，你需要编写reducers。

#### Redux生命周期
- 生命周期整体流程

1. 实例化

```javascript
    getDefaultProps 取得默认属性(ES6的写法中被删除)
    getInitialState 初始化状态(ES6的写法中被删除)
    componentWillMount 即将进入dom
    Render 组件渲染
    componentDidMount 已经进入dom
```

2. 存在期(参照数据更新过程)
    - 数据更新过程
      - 触发时机: this.setState更新状态
      1. componentWillReceiveProps 父组件发生render的时候子组件就会调用
      2. showldComponentUpdate 判断是否需要重新渲染组件
      3. componentWillUpdate 组件即将重新渲染
      4. Render 组件渲染
      5. componentDidUpdate 组件重新渲染完成

3. 销毁期
```javascript
    componentWillUnmount
```

#### 动画

##### ReactCSSTransitionGroup使用方法:

1. 引入组件
```javascript
    import ReactCSSTransitionGroup from 'react-addons-css-transition-group'
```
2. 使用ReactCSSTransitionGroup标签包裹动画标签
```javascript
        <ReactCSSTransitionGroup
            transitionName="example"
            transitionEnterTimeout={500}
            transitionLeaveTimeout={300}
            >
            {items}
            </ReactCSSTransitionGroup>
```
3. 写动画用的css(红色的字是上一页中的transitionName)

```javascript
    .example-enter 进入动画的七点
    .example-enter-active 进入动画的终点

    .example-leave 离开动画的起点
    .example-leave-active 离开动画的终点
```