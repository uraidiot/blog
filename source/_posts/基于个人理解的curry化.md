---
title: 基于个人理解的curry化
date: 2020-08-16 15:48:31
tags:
---

### 函数curry化

- 定义

> 柯里化（英語：Currying），又译为卡瑞化或加里化，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

> 大白话: 用闭包把参数保存起来，当参数的数量足够执行函数了，就开始执行函数

- 涉及知识

    1. 闭包
    2. 递归

- 代码

```
let judge;

let curry = (fn) => 
    judge = (...args) =>
         args.length === fn.length ? // 判断传入参数个数与原函数形参个数是否相同
             fn.apply(null, args) :  // 相同则执行原函数
             (...arg) => judge(...args, ...arg) // 不相同则再次传入judge函数

// 所有的 args 都通过闭包得到了保存


let add = (a, b) => a+b

let curriedAdd = curry(add);

console.log(curriedAdd(1, 2)); // 3
console.log(curriedAdd(4)(5)); // 9
```

- 执行过程

声明`curry`函数,接受一个原函数`function`作为参数,返回一个`judge`判断函数,判断传入`judge`函数的参数个数与原函数的形参个数是否相同,如果相同则将所有参数传入原函数并执行,如果不同,则将之前的参数与后面的参数再次传入`judge`函数进行判读

- 参考资料

[JavaScript专题之函数柯里化](https://github.com/mqyqingfeng/Blog/issues/42)
