XML 指可扩展标记语言（e**X**tensible **M**arkup **L**anguage）。
XML 被设计用来传输和存储数据，不用于表现和展示数据，用来表现数据的是HTML。
## 实例
```xml
<!-- 第一行是 XML 声明。它定义 XML 的版本（1.0）和所使用的编码（UTF-8 : 万国码, 可显示各种语言） -->
<?xml version="1.0" encoding="UTF-8"?>
<!-- 下一行描述文档的根元素（像在说："本文档是一个便签"）： -->
<note>
  <!-- 接下来 4 行描述根的 4 个子元素（to, from, heading 以及 body）： -->
	<to>Tove</to>
	<from>Jani</from>
	<heading>Reminder</heading>
	<body>Don't forget me this weekend!</body>
  <!-- 最后一行定义根元素的结尾： -->
</note>
```
## 规则
>与HTML不同,XML 标签==没有被预定义==。您需要自行定义标签。

标签必须==成对出现==，有开始标签就需要有结束标签，例如：
- 开始标签：`<name>`
- 结束标签：`</name>`
元素之间必须正确的嵌套（**不能在双标签里出现不完整的其他标签**）
- `<x><y>hello</x></y>`是错误的
XML 文档必须包含**根元素**。该元素是所有其他元素的父元素。
所有的元素都可以有子元素
标签可以有任意属性,属性值在引号内
- `<title lang="en">Everyday Italian</title>`
大小写敏感
XML 元素必须遵循以下命名规则：
- 名称可以包含字母、数字以及其他的字符
- 名称不能以数字或者标点符号开始
- 名称不能以字母 xml（或者 XML、Xml 等等）开始
- 名称不能包含空格
- 可使用任何名称，没有保留的字词。
HTML 会把多个连续的空格字符裁减（合并）为一个,但在 XML 中，文档中的**空格不会被删减**
## XML树结构
```xml
<bookstore>
    <book category="COOKING">
        <title lang="en">Everyday Italian</title>
        <author>Giada De Laurentiis</author>
        <year>2005</year>
        <price>30.00</price>
    </book>
    <book category="CHILDREN">
        <title lang="en">Harry Potter</title>
        <author>J K. Rowling</author>
        <year>2005</year>
        <price>29.99</price>
    </book>
    <book category="WEB">
        <title lang="en">Learning XML</title>
        <author>Erik T. Ray</author>
        <year>2003</year>
        <price>39.95</price>
    </book>
</bookstore>
```
![[nodetree.gif]]
## 语法
### XML声明
XML 声明文件的**可选部分**，如果存在需要放在文档的第一行，如下所示：
```xml
<?xml version="1.0" encoding="utf-8"?>
```
### 根元素
XML 必须包含根元素，它是所有其他元素的父元素，比如以下实例中 root 就是根元素：
```xml
<root>
  <child>
    <subchild>.....</subchild>
  </child>
</root>
```
### 属性
元素可以包含属性，属性提供有关元素的附加信息。
在 XML 中，XML 的任何属性值**必须加引号**,单引号和双引号均可使用。
属性位于开始标签中，例如：
```xml
<person age="30" gender="male">John Doe</person>
```
### XML 单标签
所有的 XML 元素一般都有一个关闭标签，但也**允许单标签**的使用的。
单标签是指在一个标签中同时包含了开始和结束标签
在XML中，你可以使用以下两种方式表示单标签：
```xml
<!-- 使用空元素标签： -->
<exampleTag />
<!-- 使用开始和结束标签，但是不包含任何内容： -->
<exampleTag></exampleTag>
```
这两种表示方式是等效的，你可以根据个人或项目的约定选择使用其中之一。
在许多XML解析器中，它们都会将这两种形式解释为相同的结构。
### 实体引用
在 XML 中，一些字符拥有特殊的意义。如果您把字符 "<" 放在 XML 元素中，会发生错误，这是因为解析器会把它当作新元素的开始。这样会产生 XML 错误,为了避免这个错误，请用**实体引用**来代替 "<" 字符：
在 XML 中，有 5 个预定义的实体引用：

| `&lt;`   | <   | less than          |
| -------- | --- | ------------------ |
| `&gt;`   | >   | greater than       |
| `&amp;`  | &   | ampersand(和)       |
| `&apos;` | '   | apostrophe(撇号)     |
| `&quot;` | "   | quotation mark(引号) |
>在 XML 中，只有字符 "<" 和 "&" 确实是非法的。大于号是合法的，但是用实体引用来代替它是一个好习惯。
### XML 中的注释
在 XML 中编写注释的语法与 HTML 的语法很相似。
```xml
<!-- This is a comment -->
```
## 在XML中使用CSS
对于如下XML
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<?xml-stylesheet type="text/css" href="cd_catalog.css"?>
<CATALOG>
  <CD>
    <TITLE>Unchain my heart</TITLE>
    <ARTIST>Joe Cocker</ARTIST>
    <COUNTRY>USA</COUNTRY>
    <COMPANY>EMI</COMPANY>
    <PRICE>8.20</PRICE>
    <YEAR>1987</YEAR>
  </CD>
</CATALOG>
```
我们可以使用CSS修改其在浏览器中的展示形式
```css
    CATALOG {
      background-color: #ffffff;
      width: 100%;
    }

    CD {
      display: block;
      margin-bottom: 30pt;
      margin-left: 0;
    }

    TITLE {
      color: #FF0000;
      font-size: 20pt;
    }

    ARTIST {
      color: #0000FF;
      font-size: 20pt;
    }

    COUNTRY,
    PRICE,
    YEAR,
    COMPANY {
      display: block;
      color: #000000;
      margin-left: 20pt;
    }
```
xml中的`<?xml-stylesheet type="text/css" href="cd_catalog.css"?>`指定了CSS
## XSL
通过使用 XSLT，您可以把 XML 文档转换成 HTML 格式。
```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<breakfast_menu>
  <food>
    <name>Belgian Waffles</name>
    <price>$5.95</price>
    <description>Two of our famous Belgian Waffles with plenty of real maple syrup</description>
    <calories>650</calories>
  </food>
  <food>
    <name>Strawberry Belgian Waffles</name>
    <price>$7.95</price>
    <description>Light Belgian waffles covered with strawberries and whipped cream</description>
    <calories>900</calories>
  </food>
</breakfast_menu>
```
对应的xsl
```xsl
<?xml version="1.0" encoding="ISO-8859-1"?>
<!-- Edited by XMLSpy® -->
<html xsl:version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns="http://www.w3.org/1999/xhtml">
  <body style="font-family:Arial;font-size:12pt;background-color:#EEEEEE">
    <xsl:for-each select="breakfast_menu/food">
      <div style="background-color:teal;color:white;padding:4px">
        <span style="font-weight:bold"><xsl:value-of select="name"/></span>
        - <xsl:value-of select="price"/>
      </div>
      <div style="margin-left:20px;margin-bottom:1em;font-size:10pt">
        <p><xsl:value-of select="description"/>.
        <span style="font-style:italic">
          <xsl:value-of select="calories"/> (calories per serving)
        </span>.</p>
      </div>
    </xsl:for-each>
  </body>
</html>
```