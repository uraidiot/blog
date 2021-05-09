---
title: JavaScript原型
date: 2021-05-07 16:57:14
tags:
---

> JavaScript 常被描述为一种基于原型的语言 (prototype-based language)——每个对象拥有一个原型对象，对象以其原型为模板、从原型继承方法和属性。原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推



#### 理解原型

*重要* : 区别对象原型和`prototype`属性

1. 对象原型

   可以通过`Object.getPrototypeof`方法或者`__proto__`(已弃用), 获得对象的原型

2. `prototype`属性

   该属性是在`constructor`构造函数上的一个**属性** , 在具体的`instance`实例上, `prototype`为`undefined`

   *注:* `{}`对象字面量是没有构造函数的. 定义的函数`function`是有构造函数的

```
let Fn = function () {}

let fnInstance = new Fn()

fnInstance.prototype // undefined

Object.getPrototypeOf(fnInstance) == Fn.prototype // true
```

*注:* **没有官方的方法直接访问对象的原型** 

**对象定义模式中的编程技巧:**  在`constructor`构造函数中定义属性, 在`prototype`中定义方法,可以让代码更具有可读性.(同时也可以提高部分性能?待考证, 在<<JavaScript编程模式>>中有提到过)

```
let Fn = function (name, age) {
/**
* 定义属性
*/
this.name = name
this.age = age
}

/**
* 在prototype中定义方法
*/
Fn.prototype.hello = function () { console.log('hello, my name is '+this.name)}
```



#### 参考资料

[对象原型-MDN](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object_prototypes)

[JavaScript中的继承-MDN](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Inheritance)

*注:* 着重查阅文档中的注意事项