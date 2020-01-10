---
title: Vue笔记1
date: 2018-04-27 08:42:38
tags:
    - 学习笔记
categories: 
    - vue
---
## 数组的方法:
- pop、push、unshift、shift、splice、slice、concat、indexOf、lastIndexOf、reserve、sort
- slice不改变原数组，其他改变原数组

## foreach、filter(过滤)、map(映射)、some、every、reduce、(includes find es6里的)

## node>8.5

## 关于for系列循环的区别
```
let arr = [1,2,3,4,5];
arr.b = '100';
for(let i = 0;i<arr.length;i++){ //编程式
    console.log(arr[i]);
} 
//面试: forEach for...in for for...of 的区别
//foreach不支持return 
 arr.forEach(function(item){ //声明式,不关心如何实现
    console.log(item);
}) 

for(let key in arr){ //key会变成字符串类型,包括数组的私有属性也可以打印出来
    console.log(typeof key); //1、2、3、4、5、b
}

for(let val of arr){//支持return,只能是值of数组(不能遍历对象)
    console.log(val);
}
//如果用for...of遍历对象
let obj = {school:'zfpx',age:8};
// ['school','age']
for(let val of Object.keys(obj)){//Object.keys 将对象的key作为新的数组
    console.log(obj[val]); 
}
```
## filter、map函数
```
//2)filter 是否操作原数组: 不  返回结果: 过滤后的新数组 回调函数的返回结果:如果返回true,表示这一项放到新数组中         (删除)
let newArray = [1,2,3,4,5].filter(function(item,index){
    return item>2&&item<5;
})
console.log(newArray); //[3,4]

//3)map 映射 将原有的数组映射成一个新数组
//不操作原数组  返回新数组  回调函数中返回什么这一项就是什么        (更新)
//[1,2,3]=><li>1</li><li>2</li><li>3</li>
let arr1 = [1,2,3].map(function(item){
    return `<li>${item}</li>`; //``是es6中的模板字符串，遇到变量用${}取值
})
console.log(arr1.join(','));
//4)includes 返回的是boolean
let arr3 = [1,2,3,4,55];
console.log(arr3.includes(5));
//5)find 返回找到的那一项 不会改变数组 回调函数中返回true表示找到了，找到后停止循环,找不到返回的是undefined
let result = arr3.find(function(item,index){ //找到具体的某一项用find
    return item.toString().indexOf(5)>-1;
});
console.log(result);//55
//6)some 找true 找到true后停止,返回true，找不到返回false
let arr3 = [1,2,3,4];

let result = arr3.some(function(item,index){
    return item.toString().indexOf(5)>-1;
})
console.log(result);//false
//7)every找false,找到false后停止,返回false
//8)reduce 收敛 4个参数  返回的是叠加后的结果 原数组不发生变化，回调函数返回的结果:
//prev代表的是数组的第一项,next是数组的第二项
//第二次prev是undefined,next是数组的第三项
let sum = [1,2,3,4,5].reduce(function(prev,next,index,item){
    // console.log(arguments);
    // console.log(prev,next);
    //return 100;//本次的返回值, 会作为下一次的prev
    return prev+next;
})
console.log(sum);

let sum2 = [{price:30,count:2},{price:30,count:3},{price:30,count:4}].reduce(function(prev,next){
    // return prev.price*prev.count+next.price*next.count;  
    // console.log(prev,next);
    //0+60
    //60+90
    //150+120
    return prev+next.price*next.count;
},0) //默认指定第一次的prev
console.log(sum2); //270

//数组扁平化，二维数组转一维数组
let flat = [[1,2,3],[4,5,6],[7,8,9]].reduce(function(prev,next){
    return prev.concat(next);
})
console.log(flat);
```