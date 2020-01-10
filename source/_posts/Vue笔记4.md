---
layout: w
title: Vue笔记4
date: 2018-05-02 16:27:16
tags:
    - 学习笔记
categories: 
    - vue
---
### 范例
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
    <div id="app">
        <!-- moustache  小胡子语法 表达式 可以放赋值 取值 三元-->
        {{msg}} 
    </div>
    <script src="./node_modules/vue/dist/vue.js"></script>
    <script>
        //引入vue后会白给你一个Vue构造函数
        let vm = new Vue({//vm == viewModel
            el:'#app', //告诉vue能管理哪个部分,querySelector
            data:{ //data中的数据会被vm所代理
                msg:'hello,zfpx' //可以通过vm.msg取到对应的内容
            }
        });//Object.defineProperty
    </script>
</body>
</html>
```