---
layout: w
title: Vue笔记5
date: 2018-05-02 16:28:27
tags:
    - 学习笔记
categories: 
    - vue
---
###### Vue双向绑定的简单实现原理解释

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <input type="text" id="input">
    <script>
       let obj = {};
       let temp= {};
       Object.defineProperty(obj,'name',{
           get(){//取obj的name属性会触发
                return temp["name"];
           },
           set(val){//给obj赋值会触发set方法
                temp["name"] = val;//改变temp的结果
                input.value = val;//将值赋予输入框
           }
       })
       input.value = obj.name;//页面一加载时，会调用get方法
       input.addEventListener('input',function(){//等待输入框的变化
           obj.name = this.value;//当值变化时会调用set方法
       })
       /* Object.defineProperty(obj,'name',{
           value:1,
           writable:true,//是否可重新赋值
           enumerable:false,//是否可枚举
           configurable:false//是否可删除
       }) */
    //    delete obj.name
    // obj.name = 1000;
    for (let key in obj){
        console.log(key);
    }
    </script>
</body>
</html>
```
