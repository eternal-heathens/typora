# JSON 简介

[JSON](https://developer.mozilla.org/zh-CN/docs/Glossary/JSON) 是一种按照JavaScript对象语法的数据格式，这是 [Douglas Crockford](https://en.wikipedia.org/wiki/Douglas\_Crockford) 推广的。虽然它是基于 JavaScript 语法，但它独立于JavaScript，这也是为什么许多程序环境能够读取（解读）和生成 JSON。

JSON可以作为一个对象或者字符串存在，前者用于解读 JSON 中的数据，后者用于通过网络传输 JSON 数据。

一个 JSON 对象可以被储存在它自己的文件中，这基本上就是一个文本文件，扩展名为 `.json`， 还有 [MIME type](https://developer.mozilla.org/zh-CN/docs/Glossary/MIME\_type) 用于 `application/json`.

* JSON 指的是 JavaScript 对象表示法（_J_ava_S_cript _O_bject _N_otation）
* JSON 是轻量级的文本数据交换格式
* JSON 独立于语言
* JSON 具有自我描述性，更易理解

## 类似XML的不同之处

* JSON 是纯文本
* JSON 具有“自我描述性”（人类可读）
* JSON 具有层级结构（值中存在值）
* JSON 可通过 JavaScript 进行解析
* JSON 数据可使用 AJAX 进行传输

## 相比 XML 的不同之处

* 没有结束标签
* 更短
* 读写的速度更快
* 能够使用内建的 JavaScript eval() 方法进行解析
* 使用数组
* 不使用保留字

```json
{
    "title": "Design Patterns",
    "subtitle": "Elements of Reusable Object-Oriented Software",
    "author": [
        "Erich Gamma",
        "Richard Helm",
        "Ralph Johnson",
        "John Vlissides"
    ],
    "year": 2009,
    "weight": 1.8,
    "hardcover": true,
    "publisher": {
        "Company": "Pearson Education",
        "Country": "India"
    },
    "website": null
}
```

从此例子可看出，JSON 是树状结构，而 JSON 只包含 6 种数据类型：

* null: 表示为 null
* boolean: 表示为 true 或 false
* number: 一般的浮点数表示方式，在下一单元详细说明
* string: 表示为 "..."
* array: 表示为 \[ ... ]
* object: 表示为 { ... }

1. 把 JSON 文本解析为一个树状数据结构（parse）。
2. 提供接口访问该数据结构（access）。
3. 把数据结构转换成 JSON 文本（stringify）。

![img](https://pic2.zhimg.com/80/75eecb0312e129d64dd3b028e1479e3d\_720w.png)

* `parse()`: 以文本字符串形式接受JSON对象作为参数，并返回相应的对象。。
* `stringify()`: 接收一个对象作为参数，返回一个对应的JSON字符串。
