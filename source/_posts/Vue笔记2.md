---
layout: w
title: Vue笔记2
date: 2018-05-02 16:25:21
tags:
    - 学习笔记
categories: 
    - vue
---
## 箭头函数、闭包和foreach
1. arrow fn 不具备this,arguments   
2. 自己家没有this就找上一级的this
3. 如何更改this指向
- call apply bind 
- var that = this;
-  =>
4. 如何确定this是谁 看谁调用的 .前面是谁this就是谁

```
function a(b){
    return b+1;
}
let a = b=> b+1;//去掉function关键字 参数有一个可以省略小括号 小括号和大括号之间有一个箭头
//如果没有大括号则直接是返回值 有大括号必须写return
/* function a(b){
    return function (c){
        return b+c;
    }
}
a(1)(2); */

let a = b => c =>b + c; // 高阶函数 (>=两箭头的)
console.log(a(1)(2)); //3
let a = function (b){
    return function (c){
        return b+c;
    }
}();
//闭包:函数执行的一瞬间叫闭包，（不销毁的作用域）,当执行后返回的结果必须是引用数据类型，被外界变量接收 此时这个函数不会销毁(模块化)
//在vue中，很多时候不能用箭头函数
[1,2,3].forEach(item=>console.log(item)); //1,2,3
```
