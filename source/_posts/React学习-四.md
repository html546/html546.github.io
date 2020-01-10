---
title: React学习(四)
date: 2019-03-29 16:42:36
tags:
	- React
categories: 
	- react
---

#### Flux架构

#### Flux流程

1. 用户访问View
2. View发出用户的Action
3. Dispatcher收到Action,要求Store进行响应的更新
4. Store更新后,发出一个"change"事件
5. View收到"change"事件后,更新页面

#### 四个组成部分

1. View: 视图层
2. Action(动作):视图层发出的消息(比如click)
3. Dispatcher(派发器):用来接受Actions、执行回调函数
4. Store(数据层):用来存放应用的状态,一旦发生变动,就提醒Views要更新页面

#### controller-view

##### controller-view可以理解成MVC模型中的controller.
> 它一般由应用的顶层容器充当,负责从store中获取数据并将数据传递到子组件中。简单的应用一般只有一个controller-view,复杂应用中也可以有多个。controller-view是应用中唯一可以操作state的地方(setState())

##### dispatcher与action
>dispatcher是事件调度中心,flux模型的中心枢纽,管理着Flux应用中的所有数据流。它本质上是Store的回调注册.每个Store注册它自己并提供一个回调函数。当Dispather响应Action时,通过已注册的回调函数,将Action提供的数据负载发送给应用中的所有Store.应用层级单例

##### Store
>负责封装应用的业务逻辑跟数据的交互.
1. Store中包含应用所有的数据
2. Store是应用中唯一的数据发生变更的地方
3. Store中没有赋值接口---所有数据变更都是由dispatcher发送store,新的数据随着Store触发的change事件传回view.Store对外只暴露getter,不允许提供setter!!禁止在任何地方直接操作Store.

##### view
>view(UI组件) ui-component职责单一只允许调用action触发事件,数据从由上层容器通过属性传递过来。

##### actionCreator
>actionCreators是相对独立的,它作为语法上的辅助函数以action的形式使得dispatcher传递数据更为便利。