---
layout: w
title: Vue笔记3
date: 2018-05-02 16:26:03
tags:
    - 学习笔记
categories: 
    - vue
---
## 框架和库
- 框架 vue 拥有完整的解决方案 我们写好人家调用我
- 库 jquery underscore zpeto animate.css 我们调用他
## 渐进式 （渐进增强）
- vue全家桶 vuejs + vue-router + vuex + axios
- 通过组合 完成一个完整的框架
## MVC(backbone) 单向的
- model 数据
- view 视图
- controller 控制器

##MVVM(angular,vue) 双向的
- model 数据
- view 视图
- viewModel 视图模型

## Object.defineProperty(es5)的没有替代方案
- 不支持ie8以下(<=)
## 安装vue
- cdn的方式
- npm 安装 node package manager 
```
npm init -y
npm install vue
```
>初始化会产生一个package.json的文件这个文件用来描述项目的依赖，不能有大写 特殊字符 中文，而且不要和安装的包的名字相同