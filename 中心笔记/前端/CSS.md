层叠样式表 (Cascading Style Sheets，缩写为 CSS），是一种 **样式表** 语言，用来**描述 HTML 文档的呈现**（**美化内容**）。

书写位置：**title 标签下方添加 style 双标签，style 标签里面书写 CSS 代码**。

```html
<title>CSS 初体验</title>
<style>
  /* 选择器 { } */
  p {
    /* CSS 属性 */
    color: red;
  }
</style>

<p>体验 CSS</p>
```
## 引入方式
- **内部**样式表：学习使用
    - CSS 代码写在 style 标签里面
- **外部**样式表：开发使用
    - CSS 代码写在单独的 CSS 文件中（**.css**）
    - 在 HTML 使用 link 标签引入(还是加在title下边)

```html
<link rel="stylesheet" href="./my.css">
```

- **行内**样式：配合 JavaScript 使用
    - CSS 写在标签的 style 属性值里

```html
<div style="color: red; font-size:20px;">这是 div 标签</div>
```
## 选择器
作用：**查找标签**，设置样式。

### 标签选择器

标签选择器：使用**标签名**作为选择器 → 选中**同名标签设置相同的样式**。

例如：p, h1, div, a, img......

```html
<style>
  p {
    color: red;
  }
</style>
```

> 注意：标签选择器**无法差异化**同名标签的显示效果。

### 类选择器

作用：查找标签，**差异化**设置标签的显示效果。

步骤：

- 定义类选择器 → **.类名**
- 使用类选择器 → 标签添加 **class="类名"**

```html
<style>
  /* 定义类选择器 */
  .red {
    color: red;
  }
</style>

<!-- 使用类选择器 -->
<div class="red">这是 div 标签</div>
<div class="red size">div 标签</div>
```

注意：

- 类名**自定义**，不要用纯数字或中文，尽量用英文命名
- 一个类选择器**可以供多个标签使用**
- **一个标签可以使用多个类名**，类名之间用**空格**隔开

> 开发习惯：类名见名知意，多个单词可以用 - 连接，例如：news-hd。

### id选择器

作用：查找标签，差异化设置标签的显示效果。

场景：id 选择器一般**配合 JavaScript** 使用，很少用来设置 CSS 样式

步骤：

- 定义 id 选择器 → \#id名
- 使用 id 选择器 → 标签添加 id= "id名"

```html
<style>
  /* 定义 id 选择器 */
  #red {
    color: red;
  }
</style>

<!-- 使用 id 选择器 -->
<div id="red">这是 div 标签</div>
```

> 规则：同一个 id 选择器在一个页面只能使用一次。

### 通配符选择器

作用：查找页面**所有**标签，设置相同样式。

通配符选择器： ***，不需要调用**，浏览器自动查找页面所有标签，设置相同的样式

```css
* {
  color: red;
}
```

> 经验：通配符选择器可以用于**清除标签的默认样式**，例如：标签默认的外边距、内边距。
``` css
* {
	margin:0;
	padding:0;
}
```
### 结构伪类选择器

**基本使用**

作用：根据元素的**结构关系**查找元素。
>==查找的是第一个E元素,不是E元素的第一个子标签,只是写作first-child(其他的同理)==
![[Pasted image 20250506165823.png]]

```css
li:first-child {
  background-color: green;
}
```

**:nth-child(公式)**

![[Pasted image 20250506165843.png]]

> 提示：公式中的n取值从 **0** 开始。

### 伪元素选择器

作用：创建**虚拟元素**（伪元素），用来**摆放装饰性的内容**。

![[Pasted image 20250506165915.png]]
![[Pasted image 20250506165927.png]]

```css
div::before {
  content: "before 伪元素";
}
div::after {
  content: "after 伪元素";
}
```

注意点：

- 必须设置 **content: ””属性**，用来 设置伪元素的内容，如果没有内容，则**引号留空**即可
- 伪元素默认是**行内**显示模式
- **权重和标签选择器相同**
## 复合选择器

定义：由两个或多个基础选择器，通过不同的方式组合而成。

作用：更准确、更高效的选择目标元素（标签）。

### 后代选择器

后代选择器：**选中某元素的后代元素**。

选择器写法：父选择器 子选择器 { CSS 属性}，父子选择器之间用**空格**隔开。(即选中父选择器==嵌套==的子选择器,包括多级嵌套)

```html
<style>
  div span {
    color: red;
  }
</style>
<span> span 标签</span>
<div>
  <span>这是 div 的儿子 span</span >
</div>
```

### 子代选择器

子代选择器：选中某元素的子代元素（**最近的子级**）。

选择器写法：父选择器 > 子选择器 { CSS 属性}，父子选择器之间用 **>** 隔开。

```html
<style>
  div > span {
    color: red;
  }
</style>

<div>
  <span>这是 div 里面的 span</span>
  <p>
    <span>这是 div 里面的 p 里面的 span</span>
  </p>
</div>

```

### 并集选择器

并集选择器：选中**多组标签**设置**相同**的样式。

选择器写法：选择器1, 选择器2, …, 选择器N { CSS 属性}，选择器之间用 **,** 隔开。

```html
<style>
  div,
  p,
  span {
    color: red;
  }
</style>

<div> div 标签</div>
<p>p 标签</p>
<span>span 标签</span>
```

### 交集选择器

交集选择器：选中**同时满足多个条件**的元素。

选择器写法：选择器1选择器2 { CSS 属性}，选择器之间连写，没有任何符号。

```html
<style>
  p.box {
  color: red;
}
</style>

<p class="box">p 标签，使用了类选择器 box</p>
<p>p 标签</p>
<div class="box">div 标签，使用了类选择器 box</div>
```

> 注意：如果交集选择器中有标签选择器，标签选择器必须书写在最前面。(感觉有点类似限制class生效范围)

### 伪类选择器
伪类选择器：伪类表示元素**状态**，选中元素的某个状态设置样式。

鼠标悬停状态：**选择器:hover { CSS 属性 }**

```html
<style>
  a:hover {
    color: red;
  }
  .box:hover {
    color: green;
  }
</style>

<a href="#">a 标签</a>
<div class="box">div 标签</div>
```

#### 超链接伪类

| 选择器      | 作用      |
| -------- | ------- |
| :link    | 访问前     |
| :visited | 访问后     |
| :hover   | 鼠标悬停    |
| :active  | 点击时(激活) |

> 提示：如果要给超链接设置以上四个状态，需要按 LVHA 的顺序书写。
> 
> 经验：工作中，一个 a 标签选择器设置超链接的样式， hover状态特殊设置

```css
a {
  color: red;
}

a:hover {
  color: green;
}
```
## 盒子尺寸和背景色

| 属性名              | 作用  |
| ---------------- | --- |
| width            | 宽度  |
| height           | 高度  |
| background-color | 背景色 |
>作为css修饰div标签,就可以做出盒子效果

## 盒子模型

作用：布局网页，摆放盒子和内容。

### 盒子模型-组成

- 内容区域 – width & height
- 内边距 – padding（出现在内容与盒子边缘之间）
- 边框线 – border
- 外边距 – margin（出现在盒子外面）

```css
div {
  margin: 50px;
  border: 5px solid brown;
  padding: 20px;
  width: 200px;
  height: 200px;
  background-color: pink;
}
```
![[Pasted image 20250506185124.png]]
![[Pasted image 20250506185130.png]]
### 边框线

#### 四个方向

属性名：**border**（bd）

属性值：边框线粗细 线条样式 颜色（不区分顺序）
![[Pasted image 20250506185210.png]]
```css
div {
  border: 5px solid brown;
  width: 200px;
  height: 200px;
  background-color: pink;
}
```

#### 单方向边框线

属性名：**border-方位名词**（bd+方位名词首字母，例如，bdl）

属性值：边框线粗细 线条样式 颜色（不区分顺序）

```css
div {
  border-top: 2px solid red;
  border-right: 3px dashed green;
  border-bottom: 4px dotted blue;
  border-left: 5px solid orange;
  width: 200px;
  height: 200px;
  background-color: pink;
}
```

### 内边距

作用：设置 内容 与 盒子边缘 之间的距离。

- 属性名：padding / padding-方位名词

```css
div {
  /* 四个方向 内边距相同 */
  padding: 30px;
  /* 单独设置一个方向内边距 */
  padding-top: 10px;
  padding-right: 20px;
  padding-bottom: 40px;
  padding-left: 80px;
  width: 200px;
  height: 200px;
  background-color: pink;
}
```

> 提示：添加 padding 会撑大盒子。

- padding 多值写法

![[Pasted image 20250506185225.png]]

> 技巧：从**上**开始**顺时针**赋值，当前方向没有数值则与**对面取值相同**。

### 尺寸计算

![[Pasted image 20250506185233.png]]

默认情况：盒子尺寸 = 内容尺寸 + border 尺寸 + 内边距尺寸

结论：给盒子加 border / padding 会撑大盒子

解决：

- 手动做减法，减掉 border / padding 的尺寸
- 內减模式：**box-sizing: border-box**(内外边距和边框都不再撑大盒子)

### 外边距

作用：拉开两个盒子之间的距离

属性名：**margin**

提示：与 padding 属性值写法、含义相同

### 版心居中

左右 margin 值 为 auto（盒子要有宽度）

```css
div {
  margin: 0 auto;
  width: 1000px;
  height: 200px;
  background-color: pink;
}
```

### 清除默认样式

清除标签默认的样式，比如：默认的内外边距。

```css
/* 清除默认内外边距 */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
/* 清除列表项目符号 */
li {
  list-style: none;
}
```

### 元素溢出

作用：控制溢出元素的内容的显示方式。

属性名：**overflow**

![[Pasted image 20250506185339.png]]

### 外边距问题

#### 合并现象

场景：**垂直**排列的兄弟元素，上下 **margin** 会**合并**

现象：取两个 margin 中的==**较大值==生效**

![[Pasted image 20250506185351.png]]

```css
.one {
  margin-bottom: 50px;
}
.two {
  margin-top: 20px;
}
```

#### 外边距塌陷

场景：父子级的标签，子级的添加 **上外边距** 会产生**塌陷**问题

现象：**导致父级一起向下移动**

```css
.son {
  margin-top: 50px;
  width: 100px;
  height: 100px;
  background-color: orange;
}
```

![[Pasted image 20250506185404.png]]

解决方法：

- 取消子级margin，父级设置padding
- 父级设置 overflow: hidden
- 父级设置 border-top

### 行内元素 – 内外边距问题

场景：行内元素添加 margin 和 padding，无法改变元素垂直位置

解决方法：给行内元素添加 **line-height** 可以改变垂直位置

```css
span {
  /* margin 和 padding 属性，无法改变垂直位置 */
  margin: 50px;
  padding: 20px;
  /* 行高可以改变垂直位置 */
  line-height: 100px;
}
```

### 圆角

作用：设置元素的外边框为圆角。

属性名：**border-radius**

属性值：数字+px / 百分比

提示：属性值是圆角半径

![[Pasted image 20250506185417.png]]

- 多值写法

![[Pasted image 20250506185423.png]]

> 技巧：从左上角开始顺时针赋值，当前角没有数值则与对角取值相同。

- 正圆形状：给正方形盒子设置圆角属性值为 **宽高的一半 / 50%**

```css
img {
  width: 200px;
  height: 200px;
  
  border-radius: 100px;
  border-radius: 50%;
}
```

![[Pasted image 20250506185443.png]]

- 胶囊形状：给长方形盒子设置圆角属性值为 盒子高度的一半

```css
div {
  width: 200px;
  height: 80px;
  background-color: orange;
  border-radius: 40px;
}
```

![[Pasted image 20250506185451.png]]

### 盒子阴影（拓展）

作用：给元素设置阴影效果

属性名：**box-shadow**

属性值：X 轴偏移量 Y 轴偏移量 模糊半径 扩散半径 颜色 内外阴影

注意：

- X 轴偏移量 和 Y 轴偏移量 必须书写
- 默认是外阴影，内阴影需要添加 inset

```css
div {
  width: 200px;
  height: 80px;
  background-color: orange;
  box-shadow: 2px 5px 10px 0 rgba(0, 0, 0, 0.5) inset;
}
```
## 文字控制属性
![[Pasted image 20250427164631.png]]
### 字体大小

- 属性名：**font-size**
- 属性值：文字尺寸，PC 端网页最常用的单位 **px**

```css
p {
  font-size: 30px;
}
```

> 经验：谷歌浏览器默认字号是16px。
> px不写该css会不生效

### 字体粗细

属性名: font-weight
属性值:

| 正常  | 400/normal |
| --- | ---------- |
| 加粗  | 700/bold   |

### 字体样式（是否倾斜）

作用：清除文字默认的倾斜效果

属性名：**font-style**

属性值

- 正常（不倾斜）：**normal**
- 倾斜：**italic**

### 行高

作用：设置多行文本的间距

属性名：line-height

属性值

- 数字 + px
- 数字（当前标签font-size属性值的倍数）

```css
line-height: 30px;


/* 当前标签字体大小为16px */
line-height: 2;
```

![[Pasted image 20250424223136.png]]

> 行高的测量方法：从一行文字的最顶端（最底端）量到下一行文字的最顶端（最底端）。

#### 单行文字垂直居中

垂直居中技巧：**行高属性值等于盒子高度属性值**

注意：该技巧适用于单行文字垂直居中效果

```css
div {
  height: 100px;
  background-color: skyblue;

  /* 注意：只能是单行文字垂直居中 */
  line-height: 100px;
}
```

### 字体族

属性名：**font-family**

属性值：字体名

```css
font-family: 楷体;
```

> 拓展（了解）：font-family属性值可以书写多个字体名，各个字体名用逗号隔开，执行顺序是从左向右依次查找
> 
> - font-family 属性最后设置一个字体族名，网页开发建议使用无衬线字体

![[Pasted image 20250424223157.png]]

```css
font-family: Microsoft YaHei, Heiti SC, tahoma, arial, Hiragino Sans GB, "\5B8B\4F53", sans-serif;
```

### font复合属性

使用场景：设置网页文字公共样式

![[Pasted image 20250424223241.png]]

复合属性：属性的简写方式，**一个属性对应多个值的写法**，各个属性值之间用**空格**隔开。

**font: 是否倾斜 是否加粗 字号/行高 字体（必须按顺序书写）**

```css
div {
  font: italic 700 30px/2 楷体;
}
```

> 注意：字号和字体值必须书写，否则 font 属性不生效 。

### 文本缩进

属性名：**text-indent**

属性值：

- 数字 + px
- **数字 + em**（推荐：**1em = 当前标签的字号大小**）

```css
p {
  text-indent: 2em;
}
```

### 文本对齐方式

作用：控制内容水平对齐方式

属性名：**text-align**

| 属性值        | 效果      |
| ---------- | ------- |
| left       | 左对齐(默认) |
| **center** | 居中对齐    |
| right      | 右对齐     |


```css
text-align: center;
```

> text-align本质是控制内容的对齐方式，==属性要设置给内容的父级==。

```html
<style>
  div {
    text-align: center;
  }
</style>

<div>
  <img src="./images/1.jpg" alt="">
</div>
```

### 文本修饰线

属性名： **text-decoration**

| 属性值           | 效果  |
| ------------- | --- |
| **none**      | 无   |
| **underline** | 下划线 |
| line_through  | 删除线 |
| overline      | 上划线 |

### color 文字颜色

| 颜色表示方式  | 属性值           | 说明                               | 使用场景           |
| ------- | ------------- | -------------------------------- | -------------- |
| 颜色关键字   | 颜色英文单词        | red,green,blue                   | 学习测试           |
| rgb表示法  | rgb(r,g,b)    | r,g,b表示红绿蓝三原色,取值:0-255           | 了解             |
| rgba表示法 | rgba(r,g,b,a) | a表示**透明度**,取值0-1                 | 开发使用,**实现透明色** |
| 十六进制表示法 | \#RRGGBB      | \#000000,\#ffcc00,简写:\#000,\#fc0 | 开发使用(从设计稿复制)   |


> 提示：只要属性值为颜色，都可以使用上述四种颜色表示方式，例如：背景色。
## CSS三大特性
CSS特性：化简代码 / 定位问题，并解决问题

- 继承性
- 层叠性
- 优先级

### 继承性

继承性：子级默认继承父级的**文字控制属性**。(即嵌套标签中修饰了父级标签子类也会一块修改)

> 注意：如果标签有默认文字样式会继承失败。 例如：a 标签的颜色、标题的字体大小。(可以认为继承来的文字属性优先级较低,有默认样式的部分会被覆盖,但是没有的任然会继承)

### 层叠性

特点：

- 相同的属性会覆盖：**后面的 CSS 属性覆盖前面的 CSS 属性**
- 不同的属性会叠加：**不同的 CSS 属性都生效**

```html
<style>
  div {
    color: red;
    font-weight: 700;
  }
  div {
    color: green;
    font-size: 30px;
  }
</style>

<div>div 标签</div>
```

> 注意：选择器类型相同则遵循层叠性，否则按==选择器优先级==判断。

### 优先级

优先级：也叫权重，当一个标签**使用了多种选择器时**，基于不同种类的选择器的**匹配规则**。

```html
<style>
  div {
    color: red;
  }
  .box {
    color: green;
  }
</style>

<div class="box">div 标签</div>
```

#### 基础选择器

规则：选择器**优先级高的样式生效**。

公式：**通配符选择器 < 标签选择器 < 类选择器 < id选择器 < 行内样式 < !important**

​ **（选中标签的范围越大，优先级越低）**

>!important是加在属性后边的,可以把这一个属性优先级提升到最高,但是如果继承了一个important属性那么他的优先级还是最低

```css
* {
	color:red !important;
}
```
#### 复合选择器-叠加

叠加计算：如果是复合选择器，则需要**权重叠加**计算。

公式：（每一级之间不存在进位）

![[Pasted image 20250427200032.png]]

规则：

- 从左向右依次比较选个数，同一级个数多的优先级高，如果个数相同，则向后比较
- **!important 权重最高**
- 继承权重最低

## Emmet写法

Emmet写法：代码的**简写**方式，输入缩写 VS Code 会自动生成对应的代码。

- HTML标签
![[Pasted image 20250427205607.png]]
- CSS：大多数简写方式为属性单词的**首字母**
![[Pasted image 20250427205619.png]]
## 背景属性
### 背景图

网页中，使用背景图实现装饰性的图片效果。

- 属性名：**background-image**（bgi）
- 属性值：url(背景图 URL)

```css
div {
  width: 400px;
  height: 400px;

  background-image: url(./images/1.png);
}
```

> 提示：背景图默认有**平铺（复制）效果**。

### 平铺方式

平铺就是图片按原大小拼接直到铺满整个标签范围
属性名：**background-repeat**（bgr）
![[Pasted image 20250504191627.png]]
```css
div {
  width: 400px;
  height: 400px;
  background-color: pink;
  background-image: url(./images/1.png);

  background-repeat: no-repeat;
}
```

### 背景图位置
可以认为这是定义第一张图片在哪个位置,其他的图片以这个位置平铺

属性名：**background-position**（bgp）

属性值：<水平方向位置> <垂直方向位置>

- 关键字
![[Pasted image 20250504191649.png]]

- 坐标
    - 水平：正数向右；负数向左
    - 垂直：正数向下；负数向上

```css
div {
  width: 400px;
  height: 400px;
  background-color: pink;
  background-image: url(./images/1.png);
  background-repeat: no-repeat;

  background-position: center bottom;
  background-position: 50px -100px;
  background-position: 50px center;
}
```

> 提示：
> 
> - 关键字取值方式写法，可以颠倒取值顺序
> - 可以只写一个关键字，另一个方向默认为居中；数字只写一个值表示水平方向，垂直方向为居中

### 背景图缩放

作用：设置背景图大小

属性名：**background-size**（bgz）

常用属性值：

- 关键字
    - cover：等比例缩放背景图片以完全覆盖背景区，可能背景图片部分看不见
    - contain：等比例缩放背景图片以完全装入背景区，可能背景区部分空白
- 百分比：根据盒子尺寸计算图片大小(等比例缩放)
    
- 数字 + 单位（例如：px）(指定的分别为长和宽,只写一个指定长,宽度等比例缩放 )

```css
div {
  width: 500px;
  height: 400px;
  background-color: pink;
  background-image: url(./images/1.png);
  background-repeat: no-repeat;
  
  background-size: cover;
  background-size: contain;
}
```

> 提示：工作中，**图片比例与盒子比例相同**，使用 cover 或 contain 缩放背景图效果相同。

### 背景图固定

作用：背景不会随着元素的内容滚动。

属性名：**background-attachment**（bga）

属性值：**fixed**

```css
body {
  background-image: url(./images/bg.jpg);
  background-repeat: no-repeat;
  background-attachment: fixed;
}
```

### 背景复合属性

属性名：**background**（bg）

属性值：背景色 背景图 背景图平铺方式 背景图位置/背景图缩放 背景图固定（**空格隔开各个属性值，不区分顺序**）

```css
div {
  width: 400px;
  height: 400px;

  background: pink url(./images/1.png) no-repeat right center/cover;
}
```
## 显示模式

显示模式：标签（元素）的显示方式。


作用：布局网页的时候，根据标签的显示模式选择合适的标签摆放内容。

### 块级元素

特点：

- 独占一行
- 宽度默认是父级的100%
- 添加宽高属性生效
![[Pasted image 20250504230033.png]]
### 行内元素

特点：

- 一行可以显示多个
- 设置宽高属性不生效
- 宽高尺寸由内容撑开

![[Pasted image 20250504230057.png]]
### 行内块元素

特点：

- 一行可以显示多个
- 设置宽高属性生效
- 宽高尺寸也可以由内容撑开
![[Pasted image 20250504230106.png]]
### 转换显示模式

属性：**display**
![[Pasted image 20250504230114.png]]

## 标准流

标准流也叫文档流，指的是标签在页面中**默认的排布规则**，例如：块元素独占一行，行内元素可以一行显示多个。
## 浮动

### 基本使用

作用：让块元素水平排列。

属性名：**float**

属性值

- **left**：左对齐
- **right**：右对齐
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>test</title>
		<style>
			/* 特点：顶对齐；具备行内块显示模式特点；浮动的盒子会脱标 */
			.one {
				width: 100px;
				height: 100px;
				background-color: brown;

				float: left;
			}

			.two {
				width: 200px;
				height: 200px;
				background-color: orange;

				/* float: left; */

				float: right;
			}
		</style>
	</head>
	<body>
		<div class="one">one</div>
		<div class="two">two</div>
	</body>
</html>
```
特点：

- 浮动后的盒子**顶对齐**
- 浮动后的盒子具备**行内块**特点
- 浮动后的盒子**脱标**，**不占用标准流的位置**(即脱标的盒子不会占用标准流盒子的位置,两者可以重叠显示)
### 清除浮动

场景：浮动元素会脱标，如果**父级没有高度**，**子级无法撑开父级高度**（可能导致页面布局错乱）
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<style>
			.top {
				margin: 10px auto;
				width: 1200px;
				/* height: 300px; */
				background-color: pink;
			}

			.left {
				float: left;
				width: 200px;
				height: 300px;
				background-color: skyblue;
			}

			.right {
				float: right;
				width: 950px;
				height: 300px;
				background-color: orange;
			}

			.bottom {
				height: 100px;
				background-color: brown;
			}
		</style>
	</head>
	<body>
		<div class="top">
			<div class="left"></div>
			<div class="right"></div>
		</div>
		<div class="bottom"></div>
	</body>
</html>
```
解决方法：**清除浮动**（清除浮动带来的影响）
#### 额外标签法

在**父元素内容的最后**添加一个**块级**元素，设置 CSS 属性 **clear: both**

clear CSS 属性指定一个元素是否必须移动 (清除浮动后) 到在它之前的浮动元素下面。clear 属性适用于浮动和非浮动元素。
```css
<style>
.clearfix {
  clear: both;
}
</style>

<div class="father">
  <div class="left"></div>
  <div class="right"></div>
  <div class="clearfix"></div>
</div>
```
>这个属性和直接删掉浮动属性效果不同,使用这个属性之后类似于把第三个div跟在了前两个浮动标签上,然后用第三个div撑开了父级div
#### 单伪元素法

1. 准备 after 伪元素
```css
.clearfix::after {
  content: "";
  display: block;
  clear: both;
}
```
2. 父级使用 clearfix 类
```html
<div class="father clearfix"></div>
```
#### 双伪元素法

1. 准备 after 和 before 伪元素
```css
/* before 解决外边距塌陷问题 */  
/* 双伪元素法 */  
.clearfix::before,  
.clearfix::after {  
  content: "";  
  display: table;  
}  
​  
/* after 清除浮动 */  
.clearfix::after {  
  clear: both;  
}
```

2. 父级使用 clearfix 类
```html
<div class="father clearfix"></div>
```
#### overfow法
```css
.father {  
  margin: 10px auto;  
  width: 1200px;  
  /* height: 300px; */  
  background-color: pink;  
​  
  overflow: hidden;  
}
```
## Flex布局

Flex 布局也叫**弹性布局**，是浏览器**提倡的布局模型**，非常适合**结构化**布局，提供了强大的空间分布和对齐能力。

Flex 模型不会产生浮动布局中脱标现象，布局网页更简单、更灵活。
### Flex组成

设置方式：给**父**元素设置 **display: flex**，==子元素==可以自动挤压或拉伸

组成部分：

- 弹性容器
- 弹性盒子
- 主轴：默认在**水平**方向
- 侧轴 / 交叉轴：默认在**垂直**方向
 ![[Pasted image 20250507184937.png]]
> 如果一行排列的元素装不下,flex布局会自动压缩每个元素的宽度,如果flex盒子没有高或宽.则会自动与flex容器填充对齐
> flex盒子会沿着主轴方向排列

### 主轴对齐方式

属性名：**justify-content**
![[Pasted image 20250507185010.png]]
>在分配间距时按的是父级的剩余尺寸
### 侧轴对齐方式

- align-items：当前弹性容器内**所有**弹性盒子的侧轴对齐方式（给**弹性容器**设置）
- align-self：单独控制**某个弹性盒子**的侧轴对齐方式（给**弹性盒子**设置）
![[Pasted image 20250507185031.png]]
### 修改主轴方向

**主轴默认在水平方向，侧轴默认在垂直方向**

属性名：**flex-direction**
![[Pasted image 20250507185056.png]]
### 弹性伸缩比

作用：控制弹性盒子的主轴方向的尺寸。

属性名：**flex**

属性值：整数数字，表示占用**父级剩余尺寸的份数**(即可以让flex盒子拉伸时长度各不相同,如果有的flex设置了flex属性,有的没设置,那么这个盒子就会占用全部的剩余空间)。

### 弹性盒子换行

弹性盒子可以自动挤压或拉伸，默认情况下，所有弹性盒子都在一行显示。

属性名：**flex-wrap**

属性值

- wrap：换行
- nowrap：不换行（默认）

### 行内对齐方式

属性名：**align-content**
![[Pasted image 20250507185124.png]]
>注意：该属性对**单行**弹性盒子模型**无效**。
## 定位

作用：灵活的改变盒子在网页中的位置

实现：

1.定位模式：position

2.边偏移：设置盒子的位置

- left
- right
- top
- bottom

CSS position **属性**用于指定一个元素在文档中的定位方式。top，right，bottom 和 left **属性**则决定了该元素的最终位置。
### 相对定位

**position: relative**

特点：

- 不脱标，占用自己原来位置
- 显示模式特点保持不变
- 设置边偏移则相对**自己原来位置**移动
```css
div {  
  position: relative;  
  top: 100px;  
  left: 200px;  
}   
```
### 绝对定位

**position: absolute**

使用场景：子级绝对定位，父级相对定位（**子绝父相**）

特点：

- 脱标，不占位
- 显示模式具备**行内块**特点
- 设置边偏移则相对**最近的已经定位的祖先元素**改变位置
- 如果祖先元素都未定位，则相对浏览器可视区改变位置
```css
.father {  
  position: relative;  
}  
​  
.father span {  
  position: absolute;  
  top: 0;  
  right: 0;  
}
```
#### 定位居中
![[Pasted image 20250508085421.png]]
实现步骤：

1. 绝对定位
2. 水平、垂直边偏移为 50%
3. 子级向左、上移动自身尺寸的一半

- 左、上的外边距为 –尺寸的一半
- transform: translate(-50%, -50%)
```css
img {  
  position: absolute;  
  left: 50%;  
  top: 50%;  
​  
  /* margin-left: -265px;  
  margin-top: -127px; */  
​  
  /* 方便： 50% 就是自己宽高的一半 */  
  transform: translate(-50%, -50%);  
}
```
### 固定定位

**position: fixed**

场景：元素的位置在网页滚动时不会改变

特点：

- 脱标，不占位
- 显示模式具备**行内块**特点
- 设置边偏移相对**浏览器窗口**改变位置
```css
div {  
  position: fixed;  
  top: 0;  
  right: 0;  
​  
  width: 500px;  
}
```
### 堆叠层级z-index
![[Pasted image 20250508085652.png]]
默认效果：按照标签书写顺序，后来者居上

作用：设置定位元素的层级顺序，改变定位元素的显示顺序

属性名：**z-index**

属性值：**整数数字**（默认值为0，取值越大，层级越高）
```css
.box1 {  
  background-color: pink;  
  /* 取值是整数，默认是0，取值越大显示顺序越靠上 */  
  z-index: 1;  
}  
​  
.box2 {  
  background-color: skyblue;  
  left: 100px;  
  top: 100px;  
​  
  z-index: 2;  
}
```
## 高级技巧

### CSS精灵

CSS 精灵，也叫 **CSS Sprites**，是一种网页**图片应用处理方式**。把网页中**一些背景图片**整合到**一张图片**文件中，再**background-position** 精确的定位出背景图片的位置。(即把所有小图标整合为一张大图,在通过改变图片位置让小盒子正好卡在小图标的位置,其他部分被覆盖)
![[Pasted image 20250508085725.png]]
优点：减少服务器被请求次数，减轻服务器的压力，提高页面加载速度

实现步骤：

1. 创建盒子，**盒子尺寸**与**小图**尺寸**相同**
2. 设置盒子**背景图**为精灵图
3. 添加 **background-position** 属性，改变**背景图位置**

3.1 使用 PxCook 测量小图片**左上角坐标**

3.2 取**负数**坐标为 background-position 属性值（向左上移动图片位置）
### 字体图标
![[Pasted image 20250508085834.png]]字体图标：**展示的是图标，本质是字体**

作用：在网页中添加**简单的、颜色单一**的小图标

优点

- **灵活性**：灵活地修改样式，例如：尺寸、颜色等
- **轻量级**：体积小、渲染快、降低服务器请求次数
- **兼容性**：几乎兼容所有主流浏览器
- **使用方便**：先下载再使用
#### 下载字体
iconfont 图标库：[https://www.iconfont.cn/](https://www.iconfont.cn/)

登录 → 素材库 → 官方图标库 → 进入图标库 → 选图标，加入购物车 → 购物车，添加至项目，确定 → 下载至本地
#### 使用字体
1. 引入字体样式表（iconfont.css）
![[Pasted image 20250508085925.png]]
2. 标签使用字体图标类名
- iconfont：字体图标基本样式（字体名，字体大小等等）
- icon-xxx：图标对应的类名
![[Pasted image 20250508090013.png]]
#### 上传矢量图
作用：项目特有的图标上传到 iconfont 图标库，生成字体

上传步骤：上传 → 上传图标 → 选择 svg 矢量图，打开 → 提交 → 系统审核
## CSS修饰属性
### 垂直对齐方式
![[Pasted image 20250508090059.png]]
属性名：vertical-align
CSS vertical-align 属性用来指定行内（inline）、行内区块（inline-block）、表格单元格（table-cell）盒子的垂直对齐方式。
![[Pasted image 20250508090108.png]]
vertical-align 属性可被用于两种上下文：
1. 使行内元素盒模型与其行内元素容器垂直对齐。例如，用于垂直对齐行文本内的图片
2. 垂直对齐表格单元格的内容
>注意 vertical-align 只对行内元素、行内块元素和表格单元格元素生效：不能用它垂直对齐块级元素。
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
		<style>
			#* {
				box-sizing: border-box;
			}

			img {
				margin-right: 0.5em;
			}

			p {
				height: 3em;
				padding: 0 0.5em;
				font-family: monospace;
				text-decoration: underline overline;
				margin-left: auto;
				margin-right: auto;
				width: 80%;
			}
		</style>
	</head>
	<body>
		<p>
			top: <img style="vertical-align: top" src="star.png" alt="star" /> middle:
			<img style="vertical-align: middle" src="star.png" alt="star" /> bottom:
			<img style="vertical-align: bottom" src="star.png" alt="star" /> super:
			<img style="vertical-align: super" src="star.png" alt="star" /> sub:
			<img style="vertical-align: sub" src="star.png" alt="star" />
		</p>

		<p>
			text-top:
			<img style="vertical-align: text-top" src="star.png" alt="star" />
			text-bottom:
			<img style="vertical-align: text-bottom" src="star.png" alt="star" /> 0.2em:
			<img style="vertical-align: 0.2em" src="star.png" alt="star" /> -1em:
			<img style="vertical-align: -1em" src="star.png" alt="star" /> 20%:
			<img style="vertical-align: 20%" src="star.png" alt="star" /> -100%:
			<img style="vertical-align: -100%" src="star.png" alt="star" />
		</p>
	</body>
</html>
```
![[Pasted image 20250508103928.png]]
### 透明度opacity

作用：设置**整个元素的透明度**（包含背景和内容）

属性名：opacity

属性值：0 – 1

- 0：完全透明（元素不可见）
- 1：不透明
- 0-1之间小数：半透明
### 光标类型cursor

作用：鼠标悬停在元素上时指针显示样式

属性名：cursor
![[Pasted image 20250508090157.png]]
## 平面转换

### 简介

作用：为元素添加动态效果，一般与过渡配合使用

概念：改变盒子在平面内的形态（位移、旋转、缩放、倾斜）
![[Pasted image 20250508143203.png]]
平面转换也叫 2D 转换，属性是 **transform**
### 过渡
transition CSS 属性是 transition-property、transition-duration、transition-timing-function 和 transition-delay 的一个简写属性。

作用：过渡可以为一个元素在不同状态之间切换的时候定义不同的过渡效果。比如在不同的伪元素之间切换，像是 :hover，:active 或者通过 JavaScript 实现的状态变化。

属性名：**transition（复合属性）**

属性值：**过渡的属性 花费时间 (s)**

提示：

- 过渡的属性可以是具体的 CSS 属性
- 也可以为 all（两个状态属性值不同的所有属性，都产生过渡效果）
- transition 设置给元素本身
```css
img {  
  width: 200px;  
  height: 200px;  
  transition: all 1s;  
}  
​  
img:hover {  
  width: 500px;  
  height: 500px;  
}
```
### transform

CSS transform 属性允许你旋转、缩放、倾斜或平移给定元素。这是通过修改 CSS 视觉格式化模型的坐标空间实现的。
如果属性的值不是 none，则将创建一个层叠上下文。在这种情况下，该元素将作为**所有** 其包含的 带有 position: fixed; 或 position: absolute; 属性的 **元素**的包含块。

>只能 transform 由盒模型定位的元素，也就是所有布局由 CSS 盒模型管理的元素，除了：非替换的行内盒子、表格列和表格列组。

可以取的属性值:
1. `<transform-function>`
要应用的一个或多个 CSS 变换函数。变换函数按从左到右的顺序相乘，也就是说复合变换按从右到左的顺序有效地应用。
2. none
不应用任何变换。
### 平移

transform: translate(X轴移动距离, Y轴移动距离);

- 取值
    - 像素单位数值
    - 百分比（参照**盒子自身尺寸**计算结果）
    - **正负**均可
- 技巧
    - translate() **只写一个值**，表示沿着 **X** 轴移动
    - 单独设置 X 或 Y 轴移动距离：translateX() 或 translateY()
### 定位居中

- 方法一：margin
![[Pasted image 20250508143256.png]]
- 方法二：平移 → 百分比参照盒子自身尺寸计算结果
![[Pasted image 20250508143314.png]]
### 旋转

transform: rotate(旋转角度);

- 取值：角度单位是 **deg**
- 技巧
    - 取值正负均可
    - 取值为正，顺时针旋转
    - 取值为负，逆时针旋转
### 转换原点

> 默认情况下，转换原点是盒子中心点

transform-origin: 水平原点位置 垂直原点位置;

取值：

- **方位名词**（left、top、right、bottom、center）
- 像素单位数值
- 百分比
### 多重转换

多重转换技巧：先平移再旋转

transform: translate() rotate();

- 多重转换原理：以第一种转换方式坐标轴为准转换形态
    - 旋转会改变网页元素的坐标轴向
    - 先写旋转，则后面的转换效果的轴向以旋转后的轴向为准，会影响转换结果
### 缩放

transform: scale(缩放倍数);  
transform: scale(X轴缩放倍数, Y轴缩放倍数);

- 技巧
    - 通常，只为 scale() 设置一个值，表示 X 轴和 Y 轴等比例缩放
    - 取值大于1表示放大，取值小于1表示缩小
### 倾斜

transform: skew();

取值：角度度数 deg
## 渐变

渐变是多个颜色逐渐变化的效果，一般用于设置盒子背景

分类：

- 线性渐变
![[Pasted image 20250508143501.png]]
- 径向渐变
![[Pasted image 20250508143516.png]]
### radial-gradient()

radial-gradient() CSS 函数创建一个图像，该图像由从原点辐射的两种或多种颜色之间的渐进过渡组成，其形状可以是圆形或椭圆形。函数的结果是 `<gradient>` 数据类型的对象，此对象是一种特殊的 `<image>` 类型。
```css
/* 在容器中心的渐变，从红色开始，变成蓝色，最后变成绿色 */
radial-gradient(circle at center, red 0, blue, green 100%)
```
径向渐变通过指定渐变的中心（0% 椭圆所在的位置）和_结束形状_（100% 椭圆）的大小和形状来指定。
#### 参数
```
<position>
渐变的位置，和解析 background-position 或者 transform-origin 的方式相同。如果没有指定，默认为 center。

<ending-shape>
渐变的结束形状。值可以是 circle（圆形，渐变的形状是一个半径不变的正圆）或 ellipse（椭圆，渐变形状是轴对称椭圆）。如果没有指定，默认为 ellipse。

<size>
确定渐变结束形状的大小。如果省略，则默认为最远角（farthest-corner）。它可以显式给出，也可以通过关键字给出。就关键字定义而言，渐变框边缘应视为向两个方向无限延伸，而不是有限线段。

对于它们的 <size>，圆形和椭圆渐变都接受以下关键字：

关键字    描述
closest-side    渐变结束形状如果是圆形，与容器距离渐变中心点最近的一边相切，如果是椭圆，则与距离渐变中心点最近的垂直和水平边相切。
closest-corner    渐变结束形状与容器距离渐变中心点最近的一个角相交。
farthest-side    类似于 closest-side，但是结束形状与容器距离渐变中心点最远的一边（或最远的垂直和水平边）相切。
farthest-corner    默认值，渐变的结束形状与容器距离渐变中心点最远的一个角相交。
如果 <ending-shape> 被指定为 circle，则可以为大小指定一个 <length> 值，它明确了圆的半径。负值是无效的。

如果 <ending-shape> 被指定为 ellipse，则可以使用带有两个值的 <length-percentage> 来指定椭圆的大小。第一个值表示水平半径，第二个值表示垂直半径。百分比值是相对于渐变框在相应维度上的大小的。负值是无效的。

如果 <ending-shape> 关键词被省略，渐变形状会由给定的大小决定。一个 <length> 产生圆，两个 <length-percentage> 单位的值产生椭圆。一个 <percentage> 值是无效的。

<linear-color-stop>
色标（color stop）的 <color> 值，然后是一个或两个可选的色标位置（沿渐变轴的 <percentage> 或 <length>）。百分比值 0%，或者长度值 0，代表渐变的中心；值 100% 表示结束形状与虚拟渐变射线的交点。两者之间的百分比值线性定位在渐变射线上。包含两个色标位置相当于在两个位置上声明了两个颜色相同的色标。

<color-hint>
颜色提示（color-hint）是一个插值提示，定义了渐变色在相邻色标之间的渐变方式。这个长度定义了渐变色应在两个色标之间的哪个点到达颜色过渡的中点。如果省略该值，颜色过渡的中点就是两个色标之间的中点。
```
### 线性渐变
```css
background-image: linear-gradient(  
  渐变方向,  
  颜色1 终点位置,  
  颜色2 终点位置,  
  ......  
);
```
取值：
- 渐变方向：可选
    - to 方位名词
    - 角度度数
- 终点位置：可选
    - 百分比
### 径向渐变
```css
background-image: radial-gradient(  
  半径 at 圆心位置,  
  颜色1 终点位置,  
  颜色2 终点位置,  
  ......  
);
```
取值：
- 半径可以是2条，则为椭圆
- 圆心位置取值：像素单位数值 / 百分比 / 方位名词
## 空间转换

### 空间转换简介

- 空间：是从坐标轴角度定义的 X 、Y 和 Z 三条坐标轴构成了一个立体空间，Z 轴位置与视线方向相同。
- 空间转换也叫 3D转换
- 属性：transform
![[Pasted image 20250508152222.png]]
### 平移
```css
transform: translate3d(x, y, z);  
transform: translateX();  
transform: translateY();  
transform: translateZ();
```
> 取值与平面转换相同
> 默认情况下，Z 轴平移没有效果，原因：电脑屏幕默认是平面，无法显示 Z 轴平移效果
### 视距

作用：指定了观察者与 Z=0 平面的距离，为元素添加透视效果

透视效果：近大远小、近实远虚

属性：(添加给父级，取值范围 800-1200)
```css
perspective: 视距;
```

![[Pasted image 20250508152317.png]]
### 旋转

Z 轴：rotateZ()
X 轴：rotateX()
Y 轴：rotateY()
### 左手法则

作用：根据旋转方向确定取值正负

使用：左手握住旋转轴, 拇指指向正值方向, 其他四个手指弯曲方向为旋转正值方向
![[Pasted image 20250508152521.png]]
### rotate3d-了解

- rotate3d(x, y, z, 角度度数) ：用来设置自定义旋转轴的位置及旋转的角度
- x，y，z 取值为0-1之间的数字
### 立体呈现

作用：设置元素的子元素是位于 3D 空间中还是平面中

属性名：transform-style

属性值：

- flat：子级处于平面中
- preserve-3d：子级处于 3D 空间
## 动画

- 过渡：实现两个状态间的变化过程
- 动画：实现多个状态间的变化过程，动画过程可控（重复播放、最终画面、是否暂停）
### 动画实现步骤

1. 定义动画
```css
/* 方式一 */  
@keyframes 动画名称 {  
  from {}  
  to {}  
}  
​  
/* 方式二 */  
@keyframes 动画名称 {  
  0% {}  
  10% {}  
  ......  
  100% {}  
}
```
2. 使用动画
```css
animation: 动画名称 动画花费时长;
```
### animation复合属性
![[Pasted image 20250508152659.png]]
提示：

- 动画名称和动画时长必须赋值
- 取值不分先后顺序
- 如果有两个时间值，第一个时间表示动画时长，第二个时间表示延迟时间
### animation拆分写法
![[Pasted image 20250508152718.png]]
### 精灵动画

- 核心
![[Pasted image 20250508152753.png]]
- 制作步骤
    1.准备显示区域
    盒子尺寸与一张精灵小图尺寸相同
    2.定义动画
    移动背景图（移动距离 = 精灵图宽度）
    3.使用动画
    steps(N)，N 与精灵小图个数相同
    
```css
div {  
  width: 140px;  
  height: 140px;  
  border: 1px solid #000;  
  background-image: url(./images/bg.png);  
  animation: run 1s steps(12) infinite;  
}  
​  
@keyframes run {  
  from {  
    background-position: 0 0;  
  }  
  to {  
    background-position: -1680px 0;  
  }  
}
```
### 多组动画
```css
animation:   
  动画一,  
  动画二,  
  ... ...  
;
```
## 移动 Web 基础
### 屏幕分辨率

分类：

- 物理分辨率：硬件分辨率（出厂设置）
- 逻辑分辨率：软件 / 驱动设置

结论：**制作网页参考 逻辑分辨率**
### 视口

作用：显示 HTML 网页的区域，用来约束 HTML 的尺寸
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">

  <!– 视口标签 -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Document</title>
</head>
<body>
  
</body>
</html>
```
- width=device-width：视口宽度 = 设备宽度
- initial-scale=1.0：缩放1倍（不缩放）
## rem

### 简介

- rem单位，是相对单位
- rem单位是相对于HTML标签的字号计算结果
- 1rem = 1HTML字号大小
### rem 布局

目前rem布局方案中，将网页等分成10份， HTML标签的字号为视口宽度的 1/10。
![[Pasted image 20250508154859.png]]
### flexible.js

flexible.js 是手淘开发出的一个用来适配移动端的 js 库。

核心原理就是根据不同的视口宽度给网页中 html 根节点设置不同的 font-size。
```html
<body>  
  ......  
  <script src="./js/flexible.js"></script>  
</body>
```
### rem 移动适配

rem单位尺寸
1. 确定基准根字号
- 查看设计稿宽度 → 确定参考设备宽度(视口宽度) → 确定基准根字号（1/10视口宽度）
1. rem单位的尺寸
- **rem单位的尺寸 = px单位数值 / 基准根字号**
## vw适配方案

### vw和vh基本使用

vw和vh是相对单位，相对视口尺寸计算结果

- vw：viewport width（1vw = 1/100视口宽度 ）
- vh：lviewport height ( 1vh = 1/100视口高度 )

### vw布局

vw单位的尺寸 = px 单位数值 / ( 1/100 视口宽度 )

### vh问题

vh是1/100视口高度，全面屏视口高度尺寸大，如果混用可能会导致盒子变形
## 媒体查询

### 基本写法
![[Pasted image 20250508160050.png]]
- max-width：最大宽度（小于等于）
- min-width：最小宽度（大于等于）
### 书写顺序

- min-width（从小到大）
- max-width（从大到小）
### 媒体查询-完整写法
![[Pasted image 20250508160231.png]]
#### 关键词 / 逻辑操作符

- and
- only
- not
#### 媒体类型

媒体类型用来区分设备类型

- screen：屏幕设备
- 打印预览：print
- 阅读器：speech
- 不区分类型：all
#### 媒体特性

- 视口宽高：width / height
- 视口最大宽高：max-width ；max-height
- 视口最小宽高：min-width；min-height
- 屏幕方向：orientation
    - protrait：竖屏
    - landscape：横屏
### 媒体查询-外部CSS
![[Pasted image 20250508160320.png]]
