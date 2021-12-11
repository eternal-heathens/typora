## XML基础
### XML简介
* XML 指可扩展标记语言（EXtensible Markup Language）。
* XML 是一种标记语言，很类似 HTML。
* XML 的设计宗旨是传输数据，而非显示数据。
* XML 标签没有被预定义。您需要自行定义标签。
* XML 被设计为具有自我描述性。
* XML 是 W3C 的推荐标准。
* XML 仅仅是纯文本。
* 在没有任何有关如何显示数据的信息的情况下，大多数的浏览器都会仅仅把 XML 文档显示为源代码。

### XML的用途
* XML 把数据从HTML分离。
* XML 数据以纯文本格式进行存储，因此提供了一种独立于软件和硬件的数据存储方法。
* XML 应用于 web 开发的许多方面，常用于简化数据的存储和共享。

### XML树结构

![image-20200214195922639](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200214195922639.png)

### XML语法结构

1. 所有 XML 元素都须有关闭标签
2. XML标签对大小写敏感
3. XML必须正确嵌套
4. XML文档必须有根元素

XML 元素指的是从（且包括）开始标签直到（且包括）结束标签的部分。

元素可包含其他元素、文本或者两者的混合物。元素也可以拥有属性。

**XML 元素必须遵循以下命名规则：**

名称可以含字母、数字以及其他的字符
名称不能以数字或者标点符号开始
名称不能以字符 “xml”（或者 XML、Xml）开始
名称不能包含空格
可使用任何名称，没有保留的字词。



### DTD简介
1. 文档类型定义（DTD）可定义合法的XML文档构建模块。它使用一系列合法的元素来定义文档的结构。
* 所有的 XML 文档（以及 HTML 文档）均由以下简单的构建模块构成： 

* 元素
```
<body>body text in between</body>
<message>some message in between</message>
```
* 属性
```
<img src="computer.gif" />
元素的名称是 "img"。属性的名称是 "src"。属性的值是 "computer.gif"。由于元素本身为空，它被一个 " /" 关闭。
```
* 实体(实体是用来定义普通文本的变量。实体引用是对实体的引用。)
* PCDATA( 是会被解析器解析的文本。这些文本将被解析器检查实体以及标记。)
> 文本中的标签会被当作标记来处理，而实体会被展开。 不过，被解析的字符数据不应当包含任何 &、< 或者 > 字符；需要使用 &amp;、&lt; 以及 &gt; 实体来分别替换它们。

* CDATA(CDATA是不会被解析器解析的文本)

2. DTD 可被成行地声明于 XML 文档中，也可作为一个外部引用。

**内部的 DOCTYPE 声明 **

```
<!DOCTYPE 根元素 [元素声明]>
```
**外部的 DOCTYPE 声明 **

```
<!DOCTYPE 根元素 SYSTEM "文件名">
```
3. DTD-元素

**声明一个元素**

```
 <!ELEMENT 元素名称 类别>/<!ELEMENT 元素名称 (元素内容)>
```
**空元素**

```
<!ELEMENT 元素名称 EMPTY>   --->   <!ELEMENT 元素名称 类别>
```
**只有 PCDATA 的元素**

```
<!ELEMENT 元素名称 (#PCDATA)>
```
**带有任何内容的元素**

```
<!ELEMENT 元素名称 ANY>
```
**带有子元素（序列）的元素**
带有一个或多个子元素的元素通过圆括号中的子元素名进行声明：
```
<!ELEMENT 元素名称 (子元素名称 1)>
<!ELEMENT 元素名称 (子元素名称 1,子元素名称 2,.....)>
```

**声明只出现一次的元素**
```
<!ELEMENT 元素名称 (子元素名称)>
```
**声明最少出现一次的元素**
```
<!ELEMENT 元素名称 (子元素名称+)>
```
**声明出现零次或多次的元素**
```
<!ELEMENT 元素名称 (子元素名称*)>
```
**声明出现零次或一次的元素**

```
<!ELEMENT 元素名称 (子元素名称?)>
```
**声明“非.../既...”类型的内容**
```
<!ELEMENT note (to,from,header,(message|body))>
```

### DTD属性
* 在 DTD 中，属性通过 ATTLIST 声明来进行声明。
```
<!ATTLIST 元素名称 属性名称 属性类型 默认值>
```

![image-20200215234330742](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20200215234330742.png)

### DTD实体
* 实体是用于定义引用普通文本或特殊字符的快捷方式的变量。
* 实体引用是对实体的引用。
* 实体可在内部或外部进行声明。
1. 一个内部实体声明
```
<!ENTITY 实体名称 "实体的值">
```
DTD 例子:
```
<!ENTITY writer "Bill Gates">
<!ENTITY copyright "Copyright W3School.com.cn">
```
XML 例子:
```
<author>&writer;&copyright;</author>
```
2. 一个外部实体声明
```
<!ENTITY 实体名称 SYSTEM "URI/URL">
```
DTD 例子:
```
<!ENTITY writer SYSTEM "http://www.w3school.com.cn/dtd/entities.dtd">
<!ENTITY copyright SYSTEM "http://www.w3school.com.cn/dtd/entities.dtd">
```
XML 例子:
```
<author>&writer;&copyright;</author>
```

### DTD 验证
* 通过 XML 解析器进行验证
当您试图打开某个 XML 文档时，XML 解析器有可能会产生错误。通过访问 parseError 对象，就可以取回引起错误的确切代码、文本甚至所在的行。
```
var xmlDoc = new ActiveXObject("Microsoft.XMLDOM")
xmlDoc.async="false"
xmlDoc.validateOnParse="true"
xmlDoc.load("note_dtd_error.xml")

document.write("<br>Error Code: ")
document.write(xmlDoc.parseError.errorCode)
document.write("<br>Error Reason: ")
document.write(xmlDoc.parseError.reason)
document.write("<br>Error Line: ")
document.write(xmlDoc.parseError.line)
```

* 关闭验证
```
var xmlDoc = new ActiveXObject("Microsoft.XMLDOM")
xmlDoc.async="false"
xmlDoc.validateOnParse="false"
xmlDoc.load("note_dtd_error.xml")

document.write("<br>Error Code: ")
document.write(xmlDoc.parseError.errorCode)
document.write("<br>Error Reason: ")
document.write(xmlDoc.parseError.reason)
document.write("<br>Error Line: ")
document.write(xmlDoc.parseError.line)
```
## XML JavaScript
### XMLHttpRequest
* XMLHttpRequest 对象用于在后台与服务器交换数据。
1. 在不重新加载页面的情况下更新网页
2. 在页面已加载后从服务器请求数据
3. 在页面已加载后从服务器接收数据
4. 在后台向服务器发送数据
### XML 解析器

#### 解析 XML 文档

下面的代码片段把 XML 文档解析到 XML DOM 对象中：

```
if (window.XMLHttpRequest)
  {// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
  }
else
  {// code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }

xmlhttp.open("GET","books.xml",false);
xmlhttp.send();
xmlDoc=xmlhttp.responseXML; 
```

#### 其他方法 1

* 通过微软的 XML 解析器来加载 XML

微软的 XML 解析器内建于 Internet Explorer 5 以及更高的版本中。

下面的 JavaScript 片段把一个 XML 文档载入解析器中：

```
var xmlDoc=new ActiveXObject("Microsoft.XMLDOM");
xmlDoc.async="false";
xmlDoc.load("note.xml");
```

1. 上面代码的第一个行创建一个空的微软 XML 文档对象。
2. 第二行关闭异步加载，这样确保在文档完全加载之前解析器不会继续脚本的执行。
3. 第三行告知解析器加载名为 "note.xml" 的 XML 文档。

#### 其他方法 2
* 在 Firefox 及其他浏览器中的 XML 解析器

下面的 JavaScript 片段把 XML 文档 ("note.xml") 载入解析器：

```
var xmlDoc=document.implementation.createDocument("","",null);
xmlDoc.async="false";
xmlDoc.load("note.xml");
```

1. 上面代码的第一个行创建一个空的 XML 文档对象。
2. 第二行关闭异步加载，这样确保在文档完全加载之前解析器不会继续脚本的执行。
3. 第三行告知解析器加载名为 "note.xml" 的 XML 文档。

#### 解析 XML 字符串

下面的 JavaScript 代码片段把 XML 字符串解析到 XML DOM 对象中（把字符串 txt 载入解析器）：

```
txt="<bookstore><book>";
txt=txt+"<title>Everyday Italian</title>";
txt=txt+"<author>Giada De Laurentiis</author>";
txt=txt+"<year>2005</year>";
txt=txt+"</book></bookstore>";

if (window.DOMParser)
  {
  parser=new DOMParser();
  xmlDoc=parser.parseFromString(txt,"text/xml");
  }
else // Internet Explorer
  {
  xmlDoc=new ActiveXObject("Microsoft.XMLDOM");
  xmlDoc.async="false";
  xmlDoc.loadXML(txt);
  }
```

### XML DOM

XML DOM (XML Document Object Model) 定义了访问和操作 XML 文档的标准方法。

DOM 把 XML 文档作为树结构来查看。能够通过 DOM 树来访问所有元素。可以修改或删除它们的内容，并创建新的元素。元素，它们的文本，以及它们的属性，都被认为是节点。

在下面的例子中，我们使用 DOM 引用从 <to> 元素中获取文本：

```
xmlDoc.getElementsByTagName("to")[0].childNodes[0].nodeValue
```

- *xmlDoc* -由解析器创建的 XML 文档
- *getElementsByTagName("to")[0]* - 第一个 <to> 元素
- *childNodes[0]* - <to> 元素的第一个子元素（文本节点）
- *nodeValue* - 节点的值（文本本身）

## XML 高级
### 使用命名空间

* 统一资源标识符（Uniform Resource Identifier (URI)，是一串可以标识因特网资源的字符。

此 XML 文档携带着有关一件家具的信息：

```
<f:table xmlns:f="http://www.w3school.com.cn/furniture">
   <f:name>African Coffee Table</f:name>
   <f:width>80</f:width>
   <f:length>120</f:length>
</f:table>
```

* 与仅仅使用前缀不同，我们为 <table> 标签添加了一个 xmlns 属性，这样就为前缀赋予了一个与某个命名空间相关联的限定名称。

* 用于标示命名空间的地址不会被解析器用于查找信息。其惟一的作用是赋予命名空间一个惟一的名称。不过，很多公司常常会作为指针来使用命名空间指向实际存在的网页，这个网页包含关于命名空间的信息。

* 默认的命名空间（Default Namespaces）

为元素定义默认的命名空间可以让我们省去在所有的子元素中使用前缀的工作。

请使用下面的语法：

```
xmlns="namespaceURI"
```

这个 XML 文档携带着某个表格中的信息：

```
<table xmlns="http://www.w3.org/TR/html4/">
   <tr>
   <td>Apples</td>
   <td>Bananas</td>
   </tr>
</table>
```