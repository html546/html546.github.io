---
title: react学习
date: 2019-03-29 10:32:32
tags:
    - react
categories: 
    - react
---
#### Redux核心API

1. 引入必要组件
```javascript
    import {createStore,combineReducers} from 'redux';
```

2. 生成store:
```javascript
    const store = createStore(reducer,state初始状态[可选]);
```

3. 取得当前时刻的state:
```javascript
    const state = store.getState();
```

4. 发出action
```javascript
    store.dispatch({
        type:'ADD_TODO',
        payload:'learn Redux'
    })
```

5. 设置监听函数(当store改变时会自动调用回调函数)
```javascript
    store.subscribe(callback)
```

#### Reducer
##### 纯函数:同样的输入,必定得到同样的输出。

##### 约束条件

1. 不得改写参数
2. 不能调用系统I/O的API
3. 不能调用Date.now()或者Math.random()等不纯的方法,因为每次会得到不一样的结果

#### redux reducer操作

1. Reducer函数里面不能改变state,必须返回一个全新的对象

```javascript
// State是一个对象
function reducer(state,action){
    return Object.assign({},state,{thingToChange});
    // 或者
    return {...state,...newState};
}
// State是一个数组
function reducer(state,action){
    return [...state,newItem];
}
```

#### Reducer写法一:

```javascript
const chatReducer = (state=defaultState,action={})=>{
    const {type,payload} = action;
    switch(type){
        case "ADD_CHAT":
            return Object.assign({},state,{
                chatLog:state.chatLog.concat(payload)
            });
        default:
            return state;
    }
}
```

#### Reducer写法二
```javascript
const reducer = combineReducers({
    a:functionA,
    b:functionB,
    c:functionC
})
```

#### Redux异步流

1. redux-thunk:
    1. store.dispatch参数可以是一个function

1. 使用方法
   1. 引入:
```
  javascript
  import thunk from 'redux-thunk'
```
   2. 加入中间件:
```
  javascript
  const store = createStore(fetchReducer,applyMiddleware(thunk))
``` 
