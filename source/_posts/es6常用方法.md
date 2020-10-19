---
title: es6常用方法
date: 2020-10-16 09:29:44
categories: web
tags:
     - es
description: 总结了es6的常用方法。
---

## 简介

慢慢成为es6的熟练小能手。

## 数组的方法
### map
遍历原数组所有item，通过返回修改原来新item，组成新数组。
```
// 3参数
let arr1 = [1,2,3];
let arr2 = arr1.map((value,key,arr) => {
    console.log(value)    // 1，2，3
    console.log(key)      // 0，1，2
    console.log(arr)      //[1,2,3] [1,2,3] [1,2,3]  有若干个值 就会遍历若干个自身的数组 
    return value * value;
})
console.log(arr1); // [ 1, 2, 3 ]
console.log(arr2); // [ 1, 4, 9 ]

// 简易版
let arr = [1,2,3]
let newArr = arr.map(item => value * value)  
console.log(newArr)// [ 1, 4, 9 ]
```

### filter
遍历原数组所有item，通过返回true或者发false，过滤原数组的item组成新数组。
```
// 3参数
let arr1 = [1,2,3];
let arr2 = arr1.filter((value,key,arr) => {
    console.log(value)    // 1，2，3
    console.log(key)      // 0，1，2
    console.log(arr)      // [1,2,3]
    return value >= 3 ? false : true;     
})
console.log(arr1); // [ 1, 2, 3 ]
console.log(arr2); // [ 1, 2 ]

// 简易版
let arr = [1,2,3]
let newArr = arr.filter(item => item>=3)  
console.log(newArr)// [3]
```

### forEach
遍历原数组，不能break中途退出，可以continue，return只支持退出循环（无视return的值）。
```
let arr = [1,2,3];
arr.forEach(item){
    console.log(item);
}
```

### some
遍历数组，当有一真时即为真。（相当于判断数组中是具有否符合要求item）
```
let arr = [1,2,3]
let temp = arr.some(item => item>=3)  
console.log(temp)// [true]
```

### every
遍历数组，当有一假时即为假。（相当于判断数组中的所有item是否符合要求）
```
let arr = [1,2,3]
let temp = arr.every(item => item>=3)  
console.log(temp)// [false]
```


