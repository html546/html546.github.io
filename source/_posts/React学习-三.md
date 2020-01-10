---
title: React学习(三)
date: 2019-03-29 16:00:47
tags:
    - React
categories: 
    - react
---

#### 动态路由传参
 - 路径匹配规则

1. ?代表参数可选。例如:

```javascript
    <Route path="/detail/:id?" component={Detail} />
    // 下面两种情况都符合跳转条件
    // /detail
    // /detail/10086
```

2. 使用正则表达式对路径参数的类型进行限制
```javascript
// 限制id只能是数字
<Route path="/detail/:id(\d+)" component={Detail} />
// 路径匹配结果:
// /detail/10086 OK
// /detail/abc NG无法匹配
```

#### 重定向

##### Redirect标签

1. from属性里面是匹配规则,当匹配的时候跳转到to中的路径
2. 当需要使用from属性的时候,外面要配合Switch标签使用

```javascript
<Switch>
    <Route exact path="/" component={Home} />
    <Route path="/discover" component={Discover}/>
    <Redirect from="/*" to="/" />
</Switch>
```
