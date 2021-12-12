# Javascript 

(像面向对象的语言)

您只能在 HTML 输出中使用 document.write。如果您在文档加载后使用该方法，会覆盖整个文档。

**HTML 中的脚本必须位于 <script> 与 </script> 标签之间。**

**脚本可被放置在 HTML 页面的 <body> 和 <head> 部分中。**

> 脚本可位于 HTML 的 <body> 或 <head> 部分中，或者同时存在于两个部分中。通常的做法是把函数放入 <head> 部分中，或者放在页面底部。这样就可以把它们安置到同一处位置，不会干扰页面的内容。

> 我们把 JavaScript 放 到了页面代码的底部，这样就可以确保在 <p> 元素创建之后再执行脚本。

也可以把脚本保存到外部文件中。外部文件通常包含被多个网页使用的代码。

外部 JavaScript 文件的文件扩展名是 .js。

如需使用外部文件，请在 <script> 标签的 "src" 属性中设置该 .js 文件：

------







## 语句

**JavaScript 中的所有事物都是对象：字符串、数字、数组、日期，等等。**

**在 JavaScript 中，对象是拥有属性和方法的数据。**

- document.write() 仅仅向文档输出写内容。

  > 如果在文档已完成加载后执行 document.write，整个 HTML 页面将被覆盖 

  

- JavaScript 对大小写是敏感的。

- JavaScript 会忽略多余的空格。

- 可以在文本字符串中使用反斜杠对代码行进行换行。

- 单行注释以 // 开头。多行注释以 /* 开始，以 */ 结尾。

- > 变量必须以字母开头
  > 变量也能以 $ 和 _ 符号开头（不过我们不推荐这么做）
  > 变量名称对大小写敏感（y 和 Y 是不同的变量）

- 当您向变量分配文本值时，应该用双引号或单引号包围这个值。

- 当您向变量赋的值是数值时，不要使用引号。 如果您用引号包围数值，该值会被作为文本来处理。

- var 关键词来声明变量未使用值来声明的变量，其值实际上是 undefined。只有var 一个声明变量的因此是动态类型。

- 数组

> 下面的代码创建名为 cars 的数组：

> var cars=new Array();
> cars[0]="Audi";
> cars[1]="BMW";
> cars[2]="Volvo";
> 或者 (condensed array):

> var cars=new Array("Audi","BMW","Volvo");
> 或者 (literal array):

> 实例
> var cars=["Audi","BMW","Volvo"];

- 当您声明新变量时，可以使用关键词 "new" 来声明其类型,而不是undefined
- 函数：

> 关键词 function 必须是小写的，并且必须以与函数名称相同的大小写来调用函数。
> 把值赋给尚未声明的变量，该变量将被自动作为全局变量声明。

“+” 运算符用于把文本值或字符串变量加起来（连接起来）。如果把数字与字符串相加，结果将成为字符串

> 运算符和逻辑语法和JAVA 一样
> continue 语句（带有或不带标签引用）只能用在循环中
> break 语句（不带标签引用），只能用在循环或 switch 中。 通过标签引用，break 语句可用于跳出任何 JavaScript 代码块：

- 错误的处理

  > try 语句测试代码块的错误。
  > catch 语句处理错误
  > throw* 语句创建自定义错误。
  >
  > 和java 一致

- 通常，通过 JavaScript，您需要操作 HTML 元素。

  为了做到这件事情，您必须首先找到该元素。有三种方法来做这件事：

  - 通过 id 找到 HTML 元素
  - 通过标签名找到 HTML 元素
  - 通过类名找到 HTML 元素

- HTML DOM 允许您通过使用 JavaScript 来向 HTML 元素分配事件：

- 实例：
  向 button 元素分配 onclick 事件：
  
  ```
  <script>
  document.getElementById("myBtn").onclick=function(){displayDate()};
  </script>
  ```
  
- > onload 和 onunload 事件会在用户进入或离开页面时被触发。
  >  onchange 事件常结合对输入字段的验证来使用。
  >  onmouseover 和 onmouseout 事件可用于在用户的鼠标移至 HTML 元素上方或移出元素时触发函数。
  >  onmousedown, onmouseup 以及 onclick 构成了鼠标点击事件的所有部分。首先 当点击鼠标按钮时，会触发 onmousedown 事件，当释放鼠标按钮时，会触发 onmouseup 事件，最后，当完成鼠标点击时，会触发 onclick 事件。

- 如需向 HTML DOM 添加新元素，您必须首先创建该元素（元素节点），然后向一个已存在的元素追加该元素。

- 实例：

  ```
  <div id="div1">
  ```
<p id="p1">这是一个段落</p>
<p id="p2">这是另一个段落</p>
</div>

<script>
var para=document.createElement("p");
var node=document.createTextNode("这是新段落。");
para.appendChild(node);

var element=document.getElementById("div1");
element.appendChild(para);
</script>
```

```



------



## 对象

- 在 JavaScript 中，不会创建类，也不会通过类来创建对象（就像在其他面向对象的语言中那样）。JavaScript 基于 prototype，而不是基于类的。
- for...in 语句循环遍历对象的属性。

JavaScript 中的所有数字都存储为根为 10 的 64 位（8 比特），浮点数。

> 如果前缀为 0，则 JavaScript 会把数值常量解释为八进制数，如果前缀为 0 和 "x"，则解释为十六进制数

- 使用关键词 new 来定义 Boolean 对象。下面的代码定义了一个名为 myBoolean 的逻辑对象：

> 如果逻辑对象无初始值或者其值为 0、-0、null、""、false、undefined 或者 NaN，那么对象的值为 false。否则，其值为 true（即使当自变量为字符串 "false" 时）！

* 正则表达式（RegExp 是正则表达式的缩写。）

> RegExp 对象用于存储检索模式。
>
> test() 方法检索字符串中的指定值。返回值是 true 或 false。
>
> exec() 方法检索字符串中的指定值。返回值是被找到的值。如果没有发现匹配，则返回 null。
>
> compile() 方法用于改变 RegExp。
>
> compile() 既可以改变检索模式，也可以添加或删除第二个参数。



------

## Windows

（**浏览器对象模型 (BOM) 使 JavaScript 有能力与浏览器“对话”**）

- 所有 JavaScript 全局对象、函数以及变量均自动成为 window 对象的成员。

​      全局变量是 window 对象的属性。

​      全局函数是 window 对象的方法。

- window.location 对象用于获得当前页面的地址 (URL)，并把浏览器重定向到新的页面。

  > - location.href 属性返回当前页面的 URL。
  > - location.hostname 返回 web 主机的域名
  > - location.pathname 返回当前页面的路径和文件名
  > - location.port 返回 web 主机的端口 （80 或 443）
  > - location.protocol 返回所使用的 web 协议（http:// 或 https://）

- window History

  > - history.back() - 与在浏览器点击后退按钮相同
  > - history.forward() - 与在浏览器中点击按钮向前相同

- window.navigator 对象包含有关访问者浏览器的信息。

  > 来自 navigator 对象的信息具有误导性，不应该被用于检测浏览器版本，这是因为：
  >
  > - navigator 数据可被浏览器使用者更改
  > - 浏览器 无法报告晚于浏览器发布的新操作系统

  ​      

* 可以在 JavaScript 中创建三种消息框：警告框、确认框、提示框。

> 警告框  alert("文本")
>
> 确认框 confirm("文本")
>
> 提示框  prompt("文本","默认值")

* JavaScript 计时事件

> setTimeout("javascript语句",毫秒)
>
> 未来的某时执行代码
>
> clearTimeout(setTimeout_variable)
>
> 取消setTimeout()

------
## 框架

- jQuery

  > 它使用 CSS 选择器来访问和操作网页上的 HTML 元素（DOM 对象）。
  >
  > jQuery 同时提供 companion UI（用户界面）和插件。

- Prototype

  > *API* 是应用程序编程接口（Application Programming Interface）的缩写。它是包含属性和方法的库，用于操作 HTML DOM。

- MooTools

  > *MooTools* 也是一个框架，提供了可使常见的 JavaScript 编程更为简单的 API

- CDN  - 内容分发网络

  > CDN 是包含可分享代码库的服务器网络。
  >
  > 若不同的网页使用同一个框架且是同一个CDN的其他某一个网站访问过，就可以直接读取而不用再加载一次

  
  







