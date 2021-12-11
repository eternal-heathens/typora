# html

* HTML 文档*包含 HTML 标签*和纯文本
* HTML 标签对大小写不敏感

## html 常用标签

HTML 标题（Heading）是通过 <h1> - <h6> 等标签进行定义的。

HTML 段落是通过 <p> 标签进行定义的。

HTML 链接是通过 <a> 标签进行定义的。

有两种使用 <a> 标签的方式：

1. 通过使用 href 属性 - 创建指向另一个文档的链接
2. 通过使用 name 属性 - 创建文档内的书签

HTML 图像是通过 <img> 标签进行定义的。

要在页面上显示图像，你需要使用源属性（src）。src 指 "source"。源属性的值是图像的 URL 地址。

<br/>换行符

<hr/> 为水平分割线

<!--这是一段注释。注释不会在浏览器中显示。-->

当显示页面时，浏览器会移除*源代码中*多余的空格和空行。所有连续的空格或空行都会被算作一个空格。

浏览器通常会为 <q> 元素包围*引号*。

浏览器通常会对 <blockquote> 元素进行*缩进*处理。

条件注释：

```
<!--[if IE 8]>
    .... some HTML here ....
<![endif]-->
```

<meta> 标签提供关于 HTML 文档的元数据。元数据不会显示在页面上，但是对于机器是可读的。

典型的情况是，meta 元素被用于规定页面的描述、关键词、文档的作者、最后修改时间以及其他元数据。
<meta> 元素可提供有关页面的元信息（meta-information），比如针对搜索引擎和更新频度的描述和关键词。
<meta> 标签位于文档的头部，不包含任何内容。<meta> 标签的属性定义了与文档相关联的名称/值对。
content 属性始终要和 name 属性或 http-equiv 属性一起使用

表格由 <table> 标签来定义。每个表格均有若干行（由 <tr> 标签定义），每行被分割为若干单元格（由 <td> 标签定义）。字母 td 指表格数据（table data）

* 表格的表头使用 <th> 标签进行定义。
* 如果某个单元格是空的（没有内容），在空单元格中添加一个空格占位符（&nbsp;），就可以将边框显示出来

无序列表是一个项目的列表，此列项目使用粗体圆点（典型的小黑圆圈）进行标记。

1. 无序列表始于 <ul> 标签。每个列表项始于 <li>。

2. 有序列表始于 <ol> 标签。每个列表项始于 <li> 标签。

Frame 标签定义了放置在每个框架中的 HTML 文档。

iframe 用于在网页内显示网页。

frame 可用作链接的目标（target）。

链接的 target 属性必须引用 iframe 的 name 属性：

<base> 标签为页面上的所有链接规定默认地址或默认目标（target）：
<form> 标签用于为用户输入创建 HTML 表单。
表单元素：

```
<form>
 .
form elements
 .
</form>
```

表单能够包含 [input 元素](http://www.w3school.com.cn/tags/tag_input.asp)，比如文本字段、复选框、单选框、提交按钮等等。

如需引用一个以上的表单，请使用空格分隔的表单 id 列表。

formaction 属性：

formaction 属性规定当提交表单时处理该输入控件的文件的 URL。

formaction 属性覆盖 <form> 元素的 action 属性。

formaction 属性适用于 type="submit" 以及 type="image"。

placeholder 属性

placeholder 属性规定用以描述输入字段预期值的提示（样本值或有关格式的简短描述）。

该提示会在用户输入值之前显示在输入字段中。

placeholder 属性适用于以下输入类型：text、search、url、tel、email 以及 password。

required 属性：

required 属性是布尔属性。

如果设置，则规定在提交表单之前必须填写输入字段。

required 属性适用于以下输入类型：text、search、url、tel、email、password、date pickers、number、checkbox、radio、and file.

step 属性：

step 属性规定 <input> 元素的合法数字间隔。

## html属性

使用style来给html元素改变属性和标签

属性总是以名称/值对的形式出现

属性总是在 HTML 元素的*开始标签*中规定。

属性值本身就含有双引号，那么您必须使用单引号

text-align 属性规定了元素中文本的水平对齐方式：

font-family、color 以及 font-size 属性分别定义元素中文本的字体系列、颜色和字体尺寸：

## HTML布局

使用的<div>元素进行布局CSS 中<style>的类要加#或.

```
<style>
#header {
    background-color:black;
    color:white;
    text-align:center;
    padding:5px;
}
```

用html5进行布局

```
<style>
header {
    background-color:black;
    color:white;
    text-align:center;
    padding:5px; 
}
```

## 响应式Web设计

- RWD 指的是响应式 Web 设计（Responsive Web Design）

- RWD 能够以可变尺寸传递网页

- RWD 对于平板和移动设备是必需的

  1.  创建响应式设计的一个方法，是自己来创建它：

  2. 另一个创建响应式设计的方法，是使用现成的 CSS 框架。

     Bootstrap 是最流行的开发响应式 web 的 HTML, CSS, 和 JS 框架。

<script> 标签用于定义客户端脚本，比如 JavaScript。

script 元素既可包含脚本语句，也可通过 src 属性指向外部脚本文件。

必需的 type 属性规定脚本的 MIME 类型。

JavaScript 最常用于图片操作、表单验证以及内容动态更新。

<noscript> 标签提供无法使用脚本时的替代内容
    如果浏览器压根没法识别 <script> 标签你应该将脚本隐藏在注释标签当中。那些老的浏览器（无法识别 <script> 标签的浏览器）将忽略这些注释，所以不会将标签的内容显示到页面上。而那些新的浏览器将读懂这些脚本并执行它们，即使代码被嵌套在注释标签内。

## HTML 实体

在 HTML 中，某些字符是预留的。

在 HTML 中不能使用小于号（<）和大于号（>），这是因为浏览器会误认为它们是标签。

如果希望正确地显示预留字符，我们必须在 HTML 源代码中使用字符实体（character entities）。
增加空格的数量，您需要使用 &nbsp; 字符实体。

## URL 

统一资源定位器（URL）用于定位万维网上的文档（或其他数据）。

遵守以下的语法规则：

```
scheme://host.domain:port/path/filename
```

- scheme - 定义因特网服务的类型。最常见的类型是 http
- host - 定义域主机（http 的默认主机是 www）
- domain - 定义因特网域名，比如 w3school.com.cn
- :port - 定义主机上的端口号（http 的默认端口号是 80）
- path - 定义服务器上的路径（如果省略，则文档必须位于网站的根目录中）。
- filename - 定义文档/资源的名称

URL 只能使用 [ASCII 字符集](http://www.w3school.com.cn/tags/html_ref_ascii.asp)来通过因特网进行发送。URL 编码通常使用 + 来替换空格。URL 编码使用 "%" 其后跟随两位的十六进制数来替换非 ASCII 字符。

# XHTML

- XHTML 是更严格更纯净的 HTML 版本

- XML 是一种必须正确标记且格式良好的标记语言。

- XHTML 是以 XML 应用的方式定义的 HTML

  ### 文档结构

  - XHTML DOCTYPE 是*强制性的*
  - <html> 中的 XML namespace 属性是*强制性的*
  - <html>、<head>、<title> 以及 <body> 也是*强制性的*

  ### 元素语法

  - XHTML 元素必须*正确嵌套*
  - XHTML 元素必须始终*关闭*
  - XHTML 元素必须*小写*
  - XHTML 文档必须有*一个根元素*

  ### 属性语法

  - XHTML 属性必须使用*小写*
  - XHTML 属性值必须用*引号包围*
  - XHTML 属性最小化也是*禁止的*

#     css

css是Cascading Style Sheets 的缩写,即层叠式样式表单，它是由W3C协会制定并发布的一个网页排版式标准,是对HTML语言功能的补充。

html则是用于文本内容，包括头部（Head）、主体（Body）两大部分，其中头部描述浏览器所需的信息，而主体则包含所要说明的具体内容。

css是多用于样式，主要的用途是对网页中字体、颜色、背景、图像及其他各种元素的控制,使网页能够完全按照设计者的要求来显示。

CSS 规则由两个主要的部分构成：选择器，以及一条或多条声明。

```
selector {declaration1; declaration2; ... declarationN }
```

你可以对选择器进行分组，这样，被分组的选择器就可以分享相同的声明。用逗号将需要分组的选择器分开。

```
h1,h2,h3,h4,h5,h6 {
  color: green;
  }
```

通过 CSS 继承，子元素将继承最高级元素（在本例中是 body）所拥有的属性（这些子元素诸如 p, td, ul, ol, ul, li, dl, dt,和 dd）。不需要另外的规则，所有 body 的子元素都应该显示 Verdana 字体，子元素的子元素也一样。

子元素需要特殊的就单独制定子元素的属性。

**id 选择器可以为标有特定 id 的 HTML 元素指定特定的样式。**

**id 选择器以 "#" 来定义。**

下面的两个 id 选择器，第一个可以定义元素的颜色为红色，第二个定义元素的颜色为绿色：

```
#red {color:red;}
#green {color:green;}
```



**即使被标注为 sidebar 的元素只能在文档中出现一次，这个 id 选择器作为派生选择器也可以被使用很多次：**

```
#sidebar p {
	font-style: italic;
	text-align: right;
	margin-top: 0.5em;
	}

#sidebar h2 {
	font-size: 1em;
	font-weight: normal;
	font-style: italic;
	margin: 0;
	line-height: 1.5;
	text-align: right;
	}
```

**在 CSS 中，类选择器以一个点号显示：**

```
.center {text-align: center}
```

在上面的例子中，所有拥有 center 类的 HTML 元素均为居中。

在下面的 HTML 代码中，h1 和 p 元素都有 center 类。这意味着两者都将遵守 ".center" 选择器中的规则。

```
<h1 class="center">
This heading will be center-aligned
</h1>

<p class="center">
This paragraph will also be center-aligned.
</p>
```

## 属性和值选择器 - 多个值

下面的例子为包含指定值的 title 属性的所有元素设置样式。适用于由空格分隔的属性值：

```
[title~=hello] { color:red; }
```

## 设置表单的样式

属性选择器在为不带有 class 或 id 的表单设置样式时特别有用：

```
input[type="text"]
{
  width:150px;
  display:block;
  margin-bottom:10px;
  background-color:yellow;
  font-family: Verdana, Arial;
}

input[type="button"]
{
  width:120px;
  margin-left:35px;
  display:block;
  font-family: Verdana, Arial;
}
```

# Canvas 与 SVG

## Canvas

HTML5 的 canvas 元素使用 JavaScript 在网页上绘制图像。\

什么是SVG？

- SVG 指可伸缩矢量图形 (Scalable Vector Graphics)
- SVG 用于定义用于网络的基于矢量的图形
- SVG 使用 XML 格式定义图形
- SVG 图像在放大或改变尺寸的情况下其图形质量不会有损失
- SVG 是万维网联盟的标准

## SVG 的优势

与其他图像格式相比（比如 JPEG 和 GIF），使用 SVG 的优势在于：

- SVG 图像可通过文本编辑器来创建和修改
- SVG 图像可被搜索、索引、脚本化或压缩
- SVG 是可伸缩的
- SVG 图像可在任何的分辨率下被高质量地打印
- SVG 可在图像质量不下降的情况下被放大

### Canvas

- 依赖分辨率
- 不支持事件处理器
- 弱的文本渲染能力
- 能够以 .png 或 .jpg 格式保存结果图像
- 最适合图像密集型的游戏，其中的许多对象会被频繁重绘

### SVG

- 不依赖分辨率
- 支持事件处理器
- 最适合带有大型渲染区域的应用程序（比如谷歌地图）
- 复杂度高会减慢渲染速度（任何过度使用 DOM 的应用都不快）
- 不适合游戏应用

## manifest

manifest 文件是简单的文本文件，它告知浏览器被缓存的内容（以及不缓存的内容）。

manifest 文件有三个部分：

- CACHE MANIFEST - 在此标题下列出的文件将在首次下载后进行缓存
- NETWORK - 在此标题下列出的文件需要与服务器的连接，且不会被缓存
- FALLBACK - 在此标题下列出的文件规定当页面无法访问时的回退页面（比如 404 页面）

# DOM

DOM 定义了访问 HTML 和 XML 文档的标准：

> “W3C 文档对象模型 （DOM） 是中立于平台和语言的接口，它允许程序和脚本动态地访问和更新文档的内容、结构和样式。”

可通过 JavaScript （以及其他编程语言）对 HTML DOM 进行访问。HTML DOM 将 HTML 文档视作**树结构**。

1. 当网页被加载时，浏览器会创建页面的文档对象模型（Document Object Model）。

2. 通过 HTML DOM，树中的所有节点均可通过 JavaScript 进行访问。所有 HTML 元素（节点）均可被修改，也可以创建或删除节点。

W3C DOM 标准被分为 3 个不同的部分：

- 核心 DOM - 针对任何结构化文档的标准模型
- XML DOM - 针对 XML 文档的标准模型
- HTML DOM - 针对 HTML 文档的标准模型

## DOM 节点

- 整个文档是一个文档节点
- 每个 HTML 元素是元素节点
- HTML 元素内的文本是文本节点
- 每个 HTML 属性是属性节点
- 注释是注释节点



