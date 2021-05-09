---
title: JavaScript相等性判断
date: 2021-05-09 18:12:25
tags:
---

#### 相等性判断

##### 非严格相等 ==

- 同类型

`Object`类型之间比较指向对象的地址, 其他进行**值**的比较

- 非同类型

```javascript
/**
* 以下比较均为 true
*/
[] == 0
"0" == 0
"\t" == 0

[] != "0"
[] != "\t"
"0" != "\t"
```

*解析上述比较过程*

1. `[]` 是`Object`类型, 调用`toString`方法, 返回`""`(空字符), 与`0`进行比较, 需要再将`""`转换成`Number`类型, 即`Number("")`返回`0`, 最后比较`0 == 0` 返回`true`
2. `"0"`是`String`类型, 与`0`比较, 需要进行类型转换`Number('0')`返回`0`, 最后比较`0 == 0` 返回`true`
3. `"\t"`(`\t`为空格符, 即实际内容为`"  "`)是`String`类型, 与`0`比较, 需要进行类型转换`Number('  ')`返回`0`, 最后比较`0 == 0` 返回`true`
4. `[]`调用`toString`后为`""`, 与`"0"`同为`String`类型, 直接比较`"" == "0"`返回`false`
5. `[]`调用`toString`后为`""`, 与`"  "`同为`String`类型, 直接比较`"" == "  "`返回`false`
6. `"0"`与`"\t"`同为`String`类型, 直接比较`"0" == "  "`返回`false`



在非严格相等`==`比较时, 出现类型不同时, 会先进行类型转换. `Object`类型会转换成原始值后进行比较, 会尝试调用`Object`的`toString`或者`valueOf`函数, 此时转换后是`String`类型, 再根据比较的内容或进行第二次类型转换. 除此之外, 不同类型的比较大都会转换成`Number`进行比较



#### 参考资料

[JavaScript 中的相等性判断-MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Equality_comparisons_and_sameness)