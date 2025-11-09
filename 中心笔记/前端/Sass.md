## 预处理器
CSS预处理器用一种专门的编程语言，进行Web页面样式设计，然后再编译成正常的CSS文件，以供项目使用。CSS预处理器为CSS增加一些编程的特性，无需考虑浏览器的兼容性问题。 比如说：Sass（SCSS）、LESS、Stylus、Turbine、Swithch CSS、CSS Cacheer、DT CSS等。都属于css预处理器。其中比较优秀的： Sass、LESS，Stylus

Sass是一种动态样式语言，由 Ruby 开发者设计和开发，Sass语法属于缩排语法，比css多出好些功能(如变量、嵌套、运算、混入(Mixin)、继承、指令、颜色处理、函数等)，更容易阅读。

Sass的工作方式是，在Sass源文件中写代码，然后由Sass程序（Sass编译器/转译器）将其转换为最终浏览器能认识的CSS文件。
## 使用
### VS插件
![[Pasted image 20251106215648.png]]
去配置项中配置
```json
{
	...
	"liveSassCompile.settings.formats": [
		{
			/*
				:expanded – 展开格式
				:compressed – 压缩格式
			*/
			"format": "expanded", // 可定制的出口CSS样式（expanded，compact，compressed，nested）
			"extensionName": ".css",
			"savePath": "~/../css" // 为null 表示当前目录
		}
	],
	/* 排除目录 */
	"liveSassCompile.settings.excludeList": [
		"/**/node_modules/**",
		"/.vscode/**"
	],
	/* 是否生成对应的map */
	"liveSassCompile.settings.generateMap": true,
	/* 是否添加兼容前缀 例如：-webkit- -moz- ...等 */
	"liveSassCompile.settings.autoprefix": [
		"> 1%",
		"last 2 versions"
	],
	"explorer.confirmDelete": false
	...
}
```
可以使用:style选项或使用style命令行参数设置输出格式。

命令标准格式：
`sass  --watch   (scss文件):(要生成的目标css文件)  --style  (输出格式)`
### 嵌套
例如有这么一段css，正常CSS的写法
```css
.container{width:1200px; margin: 0 auto;}
.container .header{height: 90px; line-height: 90px;}
.container .header .log{width:100px; height:60px;}
.container .center{height: 600px; background-color: #F00;}
.container .footer{font-size: 16px;text-align: center;} 
```
改成写SASS的方法
```scss
.container{
    width: 1200px;
    margin: 0 auto;
    .header{
        height: 90px; line-height: 90px;
        .log{
            width:100px; height:60px;
        }
    }
    .center{
        height: 600px; background-color: #F00;
    }
    .footer{
        font-size: 16px;text-align: center;
    }
}
```
避免了重复输入父选择器，复杂的 CSS 结构更易于管理
### 父选择器
在嵌套 CSS 规则时，有时也需要直接使用嵌套外层的父选择器，例如，当给某个元素设定 hover 样式时，或者当 body 元素有某个 classname 时，可以用 & 代表嵌套规则外层的父选择器。

例如有这么一段样式：
```css
.container{width: 1200px;margin: 0 auto;}
.container a{color: #333;}
.container a:hover{text-decoration: underline;color: #F00;}
.container .top{border:1px #f2f2f2 solid;}
.container .top-left{float: left; width: 200px;} 
```
用sass编写
```scss
.container{
    width: 1200px;
    margin: 0 auto;
    a{
        color: #333;
        &:hover{
            text-decoration: underline;color: #F00;
        }
    }
    .top{
        border:1px #f2f2f2 solid; 
        &-left{
            float: left; width: 200px;
        }
    }
}
```
>这里的父选择器&实际上是父类的名字的字面量的代写,也就是说不一定就是必须要在后边跟其他选择器,直接写后半部分的类名(如buf,bufend),会直接以类似字符串拼接的方式连起来
### 属性嵌套
有些 CSS 属性遵循相同的命名空间 (namespace)，比如 font-family, font-size, font-weight 都以 font 作为属性的命名空间。为了便于管理这样的属性，同时也为了避免了重复输入，Sass 允许将属性嵌套在命名空间中
例如：
```css
.container a {
  color: #333;
  font-size: 14px;
  font-family: sans-serif;
  font-weight: bold;
}
```
用SASS的方式写
```scss
.container{
    a{
        color: #333;
        font: {
            size: 14px;
            family:sans-serif;
            weight:bold;
        }
    }
}
```
>注意font：后面要加一个空格,因为他是css属性而不是类名
### 占位符选择器
有时有很多样式中都有通用的部分样式,此时可以通过占位符选择器将通用部分提取出来,在需要使用的地方直接使用,并且这样提取出来的样式如果没有被使用到,在编译时会被删除而不会编译出来
如在scss中
```scss
.button%base{
	...
}
.btn-default{
	@extend %base
	...
}
```
编译后
```css
.button.btn-default {
	...
}
.btn-default{
	...
}
```
>如果有多个类使用了这个公共类,会编译为并集选择器`.button.btn-default,.button.btn-success`,如果没有用到则会被删除
### 注释
Sass支持两种注释

1. 标准的css多行注释 `/* ... */` 会编译到.css文件中
2. 单行注释 // 不会编译到.css文件
### 变量
#### 定义
在原生css中定义变量的方式如下
```css
:root{
    --font-size:18px;
}
.container{
    font-size: var(--font-size);
}
```
>:root中的变量名可以在所有地方使用,其他的如.header{}中定义的变量就只能在header以及他的子类中使用,在其他地方使用无效(body{}中定义变量与:root效果一致)

在sass中
```scss
$font-size:14px;
.container{font-size: $font-size;}
```
编译后
```css
.container { font-size: 14px; }
```
>重复声明的变量会被后声明的变量覆盖掉,并且所有变量都需要先定义再使用,定义前使用会导致编译出错

定义规则：

1. 变量以美元符号($)开头，后面跟变量名；
2. 变量名是不以数字开头的,可包含字母、数字、下划线、横线（连接符）；
3. 写法同css，即变量名和值之间用冒号(:)分隔；
4. 变量一定要先定义，后使用；
5. ==通过连接符与下划线 定义的同名变量为同一变量，建议使用连接符==
```scss
$font-size:14px;
$font_size:16px;
.container{font-size: $font-size;}
```
编译后
```css
.container { font-size: 16px; }
```
#### 局部变量
在选择器内容定义的变量，只能在选择器范围内使用
```scss
.container{
    $font-size:14px;
    font-size: $font-size;
}
```
如果这样用就不行
```scss
.container{
    $font-size:14px;
    font-size: $font-size;
}

.footer{
    font-size: $font-size;
}
```
#### 全局变量
定义后能全局使用的变量

第一种：在选择器外面的最前面定义的变量
```scss
$font-size:16px;
.container{
    font-size: $font-size;
}
.footer{
    font-size: $font-size;
}
```
第二种：使用 !global 标志定义全局变量
```scss
.container{
    $font-size:16px !global;
    font-size: $font-size;
}
.footer{
    font-size: $font-size;
}
```
#### 变量值类型
变量值的类型可以有很多种

支持 6 种主要的数据类型
- 数字，1, 2, 13, 10px
- 字符串，有引号字符串与无引号字符串，"foo", 'bar', baz
- 颜色，blue, `#04a3f9`, rgba(255,0,0,0.5)
- 布尔型，true, false
- 空值，null
- 数组 (list)，用空格或逗号作分隔符，1.5em 1em 0 2em, Helvetica, Arial, sans-serif
- maps, 相当于 JavaScript 的 object，(key1: value1, key2: value2)
```scss
$layer-index: 10;
$border-width: 3px;
$font-base-family: 'Open Sans', Helvetica, Sans-Serif;
$top-bg-color: rgba(255, 147, 29, 0.6);
$block-base-padding: 6px 10px 6px 10px;
$blank-mode: true;
$var: null; // 值null是其类型的唯一值。它表示缺少值，通常由函数返回以指示缺少结果。
$color-map: (
  color1: #fa0000,
  color2: #fbe200,
  color3: #95d7eb,
);

$fonts: (
  serif: 'Helvetica Neue',
  monospace: 'Consolas',
);

.container {
  $font-size: 16px !global;
  font-size: $font-size;
  @if $blank-mode {
    background-color: #000;
  } @else {
    background-color: #fff;
  }
  content: type-of($var);
  content: length($var);
  color: map-get($color-map, color2);
}
.footer {
  font-size: $font-size;
}
// 如果列表中包含空值，则生成的CSS中将忽略该空值。
.wrap {
  font: 18px bold map-get($fonts, 'sans');
}
```
#### 默认值
在定义变量时,可以使用!default来为变量设置默认值,实质上也可以认为是如果有值则保留原值,没有再重新赋值
```scss
$color:#333;
// 如果$color之前没定义就使用如下的默认值
$color:#666 !default;
.container{
    border-color: $color;
}
```
## @import
### 使用
在原始css中,导入其他css文件的方式如下
```css
@import url(<文件路径>)
```
Sass 拓展了 @import 的功能，允许其导入 SCSS 或 Sass 文件。被导入的文件将合并编译到同一个 CSS 文件中，另外，被导入的文件中所包含的变量或者混合指令 (mixin) 都可以在导入的文件中使用。
```scss
@import "public"; // .scss后缀可以省略
$color:#666;
.container{
    border-color: $color;
    color: $font-base-color; 
}
```
如下的几种方式，都将==作为普通的 CSS 语句==，不会导入任何 Sass 文件
- 文件拓展名是 .css；
- 文件名以 http:// 开头；
- 文件名是 url()；
- @import 包含 media queries。
```scss
@import "public.css";
@import url(public);
@import "http://xxx.com/xxx";
@import 'landscape' screen and (orientation:landscape)
```
### 局部文件
Sass源文件中可以通过@import指令导入其他Sass源文件，被导入的文件就是**局部文件**，局部文件让Sass模块化编写更加容易。
如果一个目录正在被Sass程序监测，目录下的所有scss/sass源文件都会被编译，但通常==不希望局部文件被编译==，因为局部文件是用来被导入到其他文件的。如果不想局部文件被编译，文件名可以以下划线 （`_`）开头
@import 引入的_theme.scss，可以没有_，这是允许的，这也就意味着，同一个目录下不能同时出现两个相关名的sass文件（一个不带_，一个带_），==添加下划线的文件将会被忽略==。
### 嵌套@import
大多数情况下，一般在文件的最外层（不在嵌套规则内）使用 @import，其实，也可以将 @import 嵌套进 CSS 样式或者 @media 中，与平时的用法效果相同，只是这样导入的样式只能出现在嵌套的层中。
## Mixin
混合指令（Mixin）用于定义可重复使用的样式，避免了使用无语意的 class，比如 .float-left。混合指令可以包含所有的 CSS 规则，绝大部分 Sass 规则，甚至通过参数功能引入变量，输出多样化的样式。

mixin是可以重用的一组CSS声明。mixin有助于减少重复代码，只需声明一次，就可在文件中引用。

可以看出，mixin类似变量，不同的是变量存储值，mixin存储一组css声明。mixin可以传入参数。
### 使用
使用@include<混入名>就可以把混入导入到指定的位置
```scss
@mixin mixin-name() {
/* css 声明 */
}
```
1. 标准形式
```scss
// 定义页面一个区块基本的样式
@mixin block {
    width: 96%;
    margin-left: 2%;
    border-radius: 8px;
    border:1px #f6f6f6 solid;
}

// 使用混入
.container{
    .block{
        @include block;
	}
}
```
生成结果
```css
.container .block {
  width: 96%;
  margin-left: 2%;
  border-radius: 8px;
  border: 1px #f6f6f6 solid;
}
```
2. 嵌套选择器
```scss
// 定义警告字体样式,下划线（_）与横线（-）是一样的
@mixin warning-text {
  .warn-text {
    font-size: 12px;
    color: rgb(255, 253, 123);
    line-height: 180%;
  }
}

// 使用混入
.container {
  @include warning-text;
}
```
生成结果
```css
.container .warn-text {
  font-size: 12px;
  color: rgb(255, 253, 123);
  line-height: 180%;
}
```
3. 使用变量
```scss
// 定义flex布局元素纵轴的排列方式
@mixin flex-align($aitem) {
  -webkit-box-align: $aitem;
  -webkit-align-items: $aitem;
  -ms-flex-align: $aitem;
  align-items: $aitem;
}

// 只有一个参数，直接传递参数
.container {
  @include flex-align(center);
}
// 给指定参数指定值
.footer {
  @include flex-align($aitem: center);
}
```
生成结果
```css
.container {
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
}

.footer {
  -webkit-box-align: center;
  -ms-flex-align: center;
  align-items: center;
}
```
>如果使用了指定位置传参,那么其他的最好也是使用指定位置传参,因为按参数索引传参的实参必须在按位置传参的实参前,并不会依次补位到未分配的变量上

变量也可以设置默认值,这样在按位置传参的时候没有传参的位置就不需要再指定后写一个值了
>使用参数时建议都加上默认值 
```scss
// 定义块元素内边距，参数指定默认值
@mixin block-padding($top: 0, $right: 0, $bottom: 0, $left: 0) {
  padding-top: $top;
  padding-right: $right;
  padding-bottom: $bottom;
  padding-left: $left;
}

// 可指定参数赋值
.container {
  // 不带参数
  //@include block-padding;

  //按顺序指定参数值
  //@include block-padding(10px,20px);

  //给指定参数指定值
  @include block-padding($left: 10px, $top: 20px);
}
```
生成结果
```css
.container {
  padding-top: 20px;
  padding-right: 0;
  padding-bottom: 0;
  padding-left: 10px;
}
```
也支持可变参数,与ES6一样会被装入一个数组中,直接使用的话会被展开,如果想要访问特定的下标可以使用`nth(<array>,<index>)`来访问
```scss
/** 
    *定义线性渐变
    *@param $direction  方向
    *@param $gradients  颜色过度的值列表
 */
@mixin linear-gradient($direction, $gradients...) {
    background-color: nth($gradients,1);
    background-image: linear-gradient($direction, $gradients);
}

.table-data{
    @include linear-gradient(to right,#F00, orange, yellow);
}
```
生成结果
```css
@charset "UTF-8";
/** 
    *定义线性渐变
    *@param $direction  方向
    *@param $gradients  颜色过度的值列表
 */
.table-data {
  background-color: #f00;
  background-image: -webkit-gradient(linear, left top, right top, from(#f00), color-stop(orange), to(yellow)); /* 兼容性属性 */
  background-image: linear-gradient(to right, #f00, orange, yellow);
}
```
## @extend
### 使用
在设计网页的时候常常遇到这种情况：一个元素使用的样式与另一个元素完全相同，但又添加了额外的样式。通常会在 HTML 中给元素定义两个 class，一个通用样式，一个特殊样式。
在sass中,对于这种情况,可以在特殊样式类中使用@extend来继承共有部分,这样在html里就可以只写一个类名了
```scss
.alert {
  padding: 15px;
  margin-bottom: 20px;
  border: 1px solid transparent;
  border-radius: 4px;
  font-size: 12px;
}

.alert-info {
  @extend .alert;
  color: #31708f;
  background-color: #d9edf7;
  border-color: #bce8f1;
}

.alert-success {
  @extend .alert;
  color: #3c763d;
  background-color: #dff0d8;
  border-color: #d6e9c6;
}

.alert-warning {
  @extend .alert;
  color: #8a6d3b;
  background-color: #fcf8e3;
  border-color: #faebcc;
}

.alert-danger {
  @extend .alert;
  color: #a94442;
  background-color: #f2dede;
  border-color: #ebccd1;
}
```
生成结果
```css
.alert, .alert-danger, .alert-warning, .alert-success, .alert-info {
  padding: 15px;
  margin-bottom: 20px;
  border: 1px solid transparent;
  border-radius: 4px;
  font-size: 12px;
}

.alert-info {
  color: #31708f;
  background-color: #d9edf7;
  border-color: #bce8f1;
}

.alert-success {
  color: #3c763d;
  background-color: #dff0d8;
  border-color: #d6e9c6;
}

.alert-warning {
  color: #8a6d3b;
  background-color: #fcf8e3;
  border-color: #faebcc;
}

.alert-danger {
  color: #a94442;
  background-color: #f2dede;
  border-color: #ebccd1;
}
```
>与混入不同,不会在每个类中生成一份通用样式,而是使用并集选择器,减小css大小
>支持多继承和多级继承
### 占位符
有时被继承的样式并没有真正在页面中被用到,但是在继承时还是会被编译到结果中,此时就可以使用占位符选择器

占位符选择器类似于类选择器，但是，它们不是以句点(.)开头，而是以百分号(%)开头。

当在Sass文件中使用占位符选择器时，它可以用于扩展其他选择器，但不会被编译成最终的CSS。
>在继承时也要写%而不是.
```scss
%alert {
  padding: 15px;
  margin-bottom: 20px;
  border: 1px solid transparent;
  border-radius: 4px;
  font-size: 12px;
}
.alert-info {
  @extend %alert;
  color: #31708f;
  background-color: #d9edf7;
  border-color: #bce8f1;
}

.alert-success {
  @extend %alert;
  color: #3c763d;
  background-color: #dff0d8;
  border-color: #d6e9c6;
}

.alert-warning {
  @extend %alert;
  color: #8a6d3b;
  background-color: #fcf8e3;
  border-color: #faebcc;
}

.alert-danger {
  @extend %alert;
  color: #a94442;
  background-color: #f2dede;
  border-color: #ebccd1;
}
```
>其实就是被继承的类的类名不会在出现在css里了,并集选择器只会有继承的特殊选择器
## 运算符
### 等号操作符

所有数据类型都支持等号运算符:

|符号|说明|
|---|---|
|==|等于|
|!=|不等于|

例1数字比较：
```scss
$theme: 1;
.container {
  @if $theme==1 {
    background-color: red;
  } @else {
    background-color: blue;
  }
}
```
例2字符串比较：
```scss
$theme: 'blue';
.container {
  @if $theme != 'blue' {
    background-color: red;
  } @else {
    background-color: blue;
  }
}
```
所有数据类型均支持相等运算 `==` 或 `!=`，此外，每种数据类型也有其各自支持的运算方式。
### 关系（比较）运行符
>这些比较运算符只能比较整数

| 符号  | 说明   |
| --- | ---- |
| <   | 小于   |
| >   | 大于   |
| <=  | 小于等于 |
| >=  | 大于等于 |

例
```scss
$theme: 3;
.container {
  @if $theme >= 5 {
    background-color: red;
  } @else {
    background-color: blue;
  }
}
```
### 逻辑运行符

|符号|说明|
|---|---|
|and|逻辑与|
|or|逻辑或|
|not|逻辑非|

例
```scss
$width: 100;
$height: 200;
$last: false;
div {
  @if $width>50 and $height<300 {
    font-size: 16px;
  } @else {
    font-size: 14px;
  }
  @if not $last {
    border-color: red;
  } @else {
    border-color: blue;
  }
}
```
### 数字操作符
>可以运算的数包括带单位的数字,如果纯数字和带单位的数字进行运算,会把纯数字自动也带上单位,其他的不同单位之间运算,如果可以互相转化(如px和pt),就会先统一单位再进行计算,不能转化则会直接报错
>对于乘法,整数和其他单位之间的运算会符合直觉的直接计算而不会换算单位,而且两个带单位的数字进行乘法运算会直接报错
>对于除法,因为单纯的2/1在css中本来就是合法的运算符,所以不会被sass运算,想要进行除法运算,就必须加上()包裹,或者算子中有任意一个是表达式(一个变量也算)或方法的返回值(如果是表达式的话,并不会无视运算优先级先算表达式再算除法,而是正常计算,可以认为是出现表达式让他变成了在css中不合法的语法所以sass可以识别到),而且与乘法不同除法也会对整数转化为带单位的形式(并不会像标准单位制那样2px/1px=2,而是2px)

| 符号  | 说明  |
| --- | --- |
| +   | 加   |
| -   | 减   |
| *   | 乘   |
| /   | 除   |
| %   | 取模  |

例
```scss
/* 
    +、-、*、/、%
    线数字、百分号、css部分单位（px、pt、in...）
    +
    线数字与百分号或单位运算时会自动转化成相应的百分比与单位值
*/
.container {
  /* ==================+ 运算===================== */
  width: 50 + 20;
  width: 50 + 20%;
  width: 50% + 20%;
  width: 10px + 20px;
  width: 10pt + 20px;
  width: 10pt + 20;
  width: 10px + 10;
  /* ==================- 运算===================== */
  height: 50 - 30;
  height: 10 - 30%;
  height: 60% - 30%;
  height: 50px - 20px;
  height: 50pt - 20px;
  height: 50pt - 40;
  /* ==================* 运算===================== */
  height: 50 * 30;
  height: 10 * 30%;
  /* height: 60% * 30%; 出现了两个百分号*/
  /* height: 50px * 20px; 出现了两个单位*/
  height: 50 * 2px;
  height: 50pt * 4;
  /* ==================/运算 (除完后最多只能保留一种单位)===================== */
  $width: 100px;
  width: 10 / 5;
  width: 10px / 5px;
  width: 10px / 10 * 2;
  width: 20px / 2px * 5%;
  width: ($width/2); // 使用变量与括号
  z-index: round(10) / 2; // 使用了函数
  height: (500px/2); // 使用了括号
  /* ==================% 运算===================== */
  width: 10 % 3;
  width: 50 % 3px;
  width: 50px % 4px;
  width: 50px % 7;
  width: 50% % 7;
  width: 50% % 9%;
  width: 50px % 10pt; // 50px % 13.33333px
  width: 50px % 13.33333px;
  width: 50px + 10pt;
  /* width: 50px % 5%; 单位不统一*/
}
```
编译结果
```css
@charset "UTF-8";
/* 
    +、-、*、/、%
    线数字、百分号、css部分单位（px、pt、in...）
    +
    线数字与百分号或单位运算时会自动转化成相应的百分比与单位值
*/
.container {
  /* ==================+ 运算===================== */
  width: 70;
  width: 70%;
  width: 70%;
  width: 30px;
  width: 25pt;
  width: 30pt;
  width: 20px;
  /* ==================- 运算===================== */
  height: 20;
  height: -20%;
  height: 30%;
  height: 30px;
  height: 35pt;
  height: 10pt;
  /* ==================* 运算===================== */
  height: 1500;
  height: 300%;
  /* height: 60% * 30%; 出现了两个百分号*/
  /* height: 50px * 20px; 出现了两个单位*/
  height: 100px;
  height: 200pt;
  /* ==================/运算 (除完后最多只能保留一种单位)===================== */
  width: 10/5;
  width: 10px/5px;
  width: 2px;
  width: 50%;
  width: 50px;
  z-index: 5;
  height: 250px;
  /* ==================% 运算===================== */
  width: 1;
  width: 2px;
  width: 2px;
  width: 1px;
  width: 1%;
  width: 5%;
  width: 10px;
  width: 10.00001px;
  width: 63.3333333333px;
  /* width: 50px % 5%; 单位不统一*/
}
```
加法运算也可以用于字符串拼接

**注意**：如果有引号字符串（位于 + 左侧）连接无引号字符串，运算结果是有引号的，相反，无引号字符串（位于 + 左侧）连接有引号字符串，运算结果则没有引号。
```scss
.container {
  content: 'Foo ' + Bar;
  font-family: sans- + 'serif';
}
```
编译结果
```css
.container {
  content: "Foo Bar";
  font-family: sans-serif;
}
```
### 三元运算符函数
sass中的三元运算符是一个函数,使用方法如下
```scss
if($condition,$if-true,$if-false);
```
判断`$condition`，如果条件成立，则返回`$if-true`的结果，如果条件不成立，则返回`$if-false`的结果。
```scss
$theme: 'light';
.container {
  color: if($theme== 'light', #000, #fff);
}
```
编译结果
```css
.container {
  color: #000;
}
```
## 插值语句 #{ }
我们知道使用/符号时,只要有一个算子是变量就会被认为是除法表达式,但是如果就是要使用变量和分隔符的话,就需要使用插值语句了
```scss
p {
  $font-size: 12px;
  $line-height: 30px;
  font:
    #{$font-size}/#{$line-height} Helvetica,
    sans-serif;
}
```
编译结果
```css
p {
  font: 12px/30px Helvetica, sans-serif;
}
```
通过 `#{}` 插值语句可以在选择器、属性名、注释中使用变量：
```scss
$class-name: danger;
$attr: color;
$author: '老姚';

/* 
   * 这是文件的说明部分
    @author: #{$author}
 */

a.#{$class-name} {
  border-#{$attr}: #f00;
}
```
编译结果
```css
@charset "UTF-8";
/* 
   * 这是文件的说明部分
    @author: 老姚
 */
a.danger {
  border-color: #f00;
}
```
>插值表达式比较像C中的宏定义,会直接把对应的部分替换为目标值
## 常见函数
### Color(颜色函数)

sass包含很多操作颜色的函数。例如：lighten() 与 darken()函数可用于调亮或调暗颜色，opacify()函数使颜色透明度减少，transparent()函数使颜色透明度增加，mix()函数可用来混合两种颜色。
```scss
p {
  height: 30px;
}

.p0 {
  background-color: #5c7a29;
}

.p1 {
  /* 
        让颜色变亮
        lighten($color, $amount)
        $amount 的取值在0% - 100% 之间
     */
  background-color: lighten(#5c7a29, 30%);
}

.p2 {
  // 让颜色变暗  通常使用color.scale()代替该方案
  background-color: darken(#5c7a29, 15%);
}

.p3 {
  // 降低颜色透明度  通常使用color.scale()代替该方案
  // background-color: opacify(#5c7a29,0.5);
  background-color: opacify(rgba(#5c7a29, 0.1), 0.5);
}
```
编译结果
```css
@charset "UTF-8";
p {
  height: 30px;
}

.p0 {
  background-color: #5c7a29;
}

.p1 {
  /* 
        让颜色变亮
        lighten($color, $amount)
        $amount 的取值在0% - 100% 之间
     */
  background-color: #aace6e;
}

.p2 {
  background-color: #314116;
}

.p3 {
  background-color: rgba(92, 122, 41, 0.6);
}
```
### String（字符串函数）

Sass有许多处理字符串的函数，比如向字符串添加引号的quote()、取消引号的unquote(),获取字符串长度的string-length()和将内容插入字符串给定位置的string-insert()。
```scss
p {
  &:after {
    content: quote(这是里面的内容);
  }
  background-color: unquote($string: '#F00');
  z-index: str-length('sass学习');
}

```
编译结果
```css
@charset "UTF-8";
p {
  background-color: #F00;
  z-index: 6;
}
p:after {
  content: "这是里面的内容";
}
```
### Math(数值函数)

数值函数处理数值计算，例如：percentage()将无单元的数值转换为百分比，round()将数字四舍五入为最接近的整数，min()和max()获取几个数字中的最小值或最大值，random()返回一个随机数。
```scss
p {
  z-index: abs($number: -15); // 15
  z-index: ceil(5.8); //6
  z-index: max(5, 1, 6, 8, 3); //8
  opacity: random(); // 随机 0-1
}
```
编译结果
```css
p {
  z-index: 15;
  z-index: 6;
  z-index: 8;
  opacity: 0.9577446364;
}
```
### List函数

List函数操作List，length()返回列表长度，nth()返回列表中的特定项，join()将两个列表连接在一起，append()在列表末尾添加一个值。
```scss
p {
  z-index: length(12px); //1
  z-index: length(12px 5px 8px); //3
  z-index: index(a b c d, c); //3
  padding: append(10px 20px, 30px); // 10px 20px 30px
  color: nth($list: red blue green, $n: 2); // blue
}
```
编译结果
```css
p {
  z-index: 1;
  z-index: 3;
  z-index: 3;
  padding: 10px 20px 30px;
  color: blue;
}
```
### Map函数

Map函数操作Map，map-get()根据键值获取map中的对应值，map-merge()来将两个map合并成一个新的map，map-values()映射中的所有值。
```scss
$font-sizes: (
  'small': 12px,
  'normal': 18px,
  'large': 24px,
);
$padding: (
  top: 10px,
  right: 20px,
  bottom: 10px,
  left: 30px,
);
p {
  font-size: map-get($font-sizes, 'normal'); //18px
  @if map-has-key($padding, 'right') {
    padding-right: map-get($padding, 'right');
  }
  &:after {
    content: map-keys($font-sizes) + ' ' + map-values($padding) + '';
  }
}
```
编译结果
```css
p {
  font-size: 18px;
  padding-right: 20px;
}
p:after {
  content: '"small", "normal", "large" 10px, 20px, 10px, 30px';
}
```
### selector选择器函数

选择符相关函数可对CSS选择进行一些相应的操作，例如：selector-append()可以把一个选择符附加到另一个选择符，selector-unify()将两组选择器合成一个复合选择器。
```scss
.header {
  background-color: #000;
  content: selector-append('.a', '.b', '.c') + '';
  content: selector-unify('a', '.disabled') + '';
}
```
编译结果
```css
.header {
  background-color: #000;
  content: ".a.b.c";
  content: "a.disabled";
}
```
### 自检函数

自检相关函数，例如：feature-exists()检查当前Sass版本是否存在某个特性，variable-exists()检查当前作用域中是否存在某个变量，mixin-exists()检查某个mixin是否存在。
```scss
$color: #f00;
@mixin padding($left: 0, $top: 0, $right: 0, $bottom: 0) {
  padding: $top $right $bottom $left;
}

.container {
  @if variable-exists(color) {
    color: $color;
  } @else {
    content: '$color不存在';
  }
  @if mixin-exists(padding) {
    @include padding($left: 10px, $right: 10px);
  }
}
```
编译结果
```css
.container {
  color: #f00;
  padding: 0 10px 0 10px;
}
```
>不存在的话会直接报错,一般用于代码调试
## 流程控制指令
### @if控制指令

@if()函数允许您根据条件进行分支，并仅返回两种可能结果中的一种。
```scss
$theme: 'green';
.container {
  @if $theme== 'red' {
    color: red;
  } @else if $theme== 'blue' {
    color: blue;
  } @else if $theme== 'green' {
    color: green;
  } @else {
    color: darkgray;
  }
}
```
编译结果
```css
.container {
  color: green;
}
```
### @for指令

@for 指令可以在==限制的范围内==重复输出格式，每次按要求（变量的值）对输出结果做出变动。这个指令包含两种格式：@for $var from `<start>` through `<end>` ，或者 @for $var from `<start>` to `<end>`

区别在于 through 与 to 的含义：

- 当使用`through`时，条件范围包含`<start>`与`<end>`的值。(闭区间)
- 而使用`to` 时条件范围只包含`<start>`的值不包含`<end>`的值。(左闭右开)
- 另外，$var 可以是任何变量，比如 $i；`<start>`和`<end`必须是整数值。
```scss
@for $i from 1 to 4 {
  .p#{$i} {
    width: 10px * $i;
    height: 30px;
    background-color: red;
  }
}

@for $i from 1 through 3 {
  .p#{$i} {
    width: 10px * $i;
    height: 30px;
    background-color: red;
  }
}
```
编译结果
```css
.p1 {
  width: 10px;
  height: 30px;
  background-color: red;
}

.p2 {
  width: 20px;
  height: 30px;
  background-color: red;
}

.p3 {
  width: 30px;
  height: 30px;
  background-color: red;
}

.p1 {
  width: 10px;
  height: 30px;
  background-color: red;
}

.p2 {
  width: 20px;
  height: 30px;
  background-color: red;
}

.p3 {
  width: 30px;
  height: 30px;
  background-color: red;
}
```
### @each指令

@each 指令的格式是 $var in , $var 可以是任何变量名，比如 $length 或者 $name，而 是一连串的值，也就是值列表。
```scss
$color-list: red green blue turquoise darkmagenta;
@each $color in $color-list {
  $index: index($color-list, $color);
  .p#{$index - 1} {
    background-color: $color;
  }
}
```
编译结果
```css
.p0 {
  background-color: red;
}

.p1 {
  background-color: green;
}

.p2 {
  background-color: blue;
}

.p3 {
  background-color: turquoise;
}

.p4 {
  background-color: darkmagenta;
}
```
### @while 指令

@while 指令重复输出格式直到表达式返回结果为 false。这样可以实现比 @for 更复杂的循环。
```scss
$column: 12;
@while $column>0 {
  .col-sm-#{$column} {
    width: $column / 12 * 100%;
    width: unquote($string: $column / 12 * 100 + '%');
  }
  $column: $column - 1;
}
```
编译结果
```css
.col-sm-12 {
  width: 100%;
  width: 100%;
}

.col-sm-11 {
  width: 91.6666666667%;
  width: 91.6666666667%;
}

.col-sm-10 {
  width: 83.3333333333%;
  width: 83.3333333333%;
}

.col-sm-9 {
  width: 75%;
  width: 75%;
}

.col-sm-8 {
  width: 66.6666666667%;
  width: 66.6666666667%;
}

.col-sm-7 {
  width: 58.3333333333%;
  width: 58.3333333333%;
}

.col-sm-6 {
  width: 50%;
  width: 50%;
}

.col-sm-5 {
  width: 41.6666666667%;
  width: 41.6666666667%;
}

.col-sm-4 {
  width: 33.3333333333%;
  width: 33.3333333333%;
}

.col-sm-3 {
  width: 25%;
  width: 25%;
}

.col-sm-2 {
  width: 16.6666666667%;
  width: 16.6666666667%;
}

.col-sm-1 {
  width: 8.3333333333%;
  width: 8.3333333333%;
}
```
## @function的使用
### 函数作用
把一些比较复杂或经常用些的内容进行抽离（封装），以便重复使用
### 函数的定义与使用
#### 定义
```scss
@function function-name([$param1,$param2,...]){
    ...
    @return $value;
}
```
>提示：函数名function-name 与function_name 是相同的

**@return**
它只允许在@函数体中使用，并且每个@function必须以@return结束。当遇到@return时，它会立即结束函数并返回其结果。
#### 使用
>函数的参数和混入的参数一样,可以有默认值,可以按位传参和指定传参,支持可变参数
```scss
@function row-cols-width($column) {
  @return percentage(1 / $column);
}

@for $i from 1 through 6 {
  .row-cols-#{$i} > * {
    width: row-cols-width($i);
  }
}
```
编译结果
```css
.row-cols-1 > * {
  width: 100%;
}

.row-cols-2 > * {
  width: 50%;
}

.row-cols-3 > * {
  width: 33.3333333333%;
}

.row-cols-4 > * {
  width: 25%;
}

.row-cols-5 > * {
  width: 20%;
}

.row-cols-6 > * {
  width: 16.6666666667%;
}
```
**混入mixin和函数function的区别**
- 混入mixin主要是通过传递参数的方式输出多样化的样式，为了可以现实代码**复用**。
- 函数的功能主要是通过传递参数后，经过函数内部的计算，最后@return输出一个值。
## @use
从其他Sass样式表加载mixin，function和变量，并将来自多个样式表的CSS组合在一起，@use加载的样式表被称为“模块”，多次引入只包含一次。
>@use也可以看作是对@import的增强
### 使用
>注意要使用/而不是\
```scss
@use '<url>' [as alias|namespace]
// 例
@use 'use/common';
@use 'use/about';
```
### 与import的区别
使用import导入外部样式时,不会对之前是否导入过进行检查,编译结果中导入过几次就会出现几次,而use导入时首先会对命名空间进行检查.如果之前没有使用as命名,重复导入会直接报错,而使用了别名后不会报错,但是也不会出现导入多次的情况,相同的样式只会出现一次
**使用@use导入**
```scss
@use '_test' as g1;
@use '_test' as g2;
```
编译结果
```css
.gclass {
  background-color: #f00;
}
```
**使用@import导入**
```scss
@import '_test';
@import '_test';
```
编译结果
```css
.gclass {
  background-color: #f00;
}

.gclass {
  background-color: #f00;
}
```
>可以大致理解为@import导入是导入的字面量,直接复制粘贴,这样导入的变量,混入和函数都可以直接使用,而@use导入的是一个模块,需要使用模块名.的方式来引用出来其中的变量等,否则会报错找不到变量
### @use取消别名
使用@use导入时,默认的模块名就是文件名(会自动去掉_,使用的时候不需要带着_),也可以使用as来重命名,当模块名为`*`时,可以直接使用其中的变量,不需要使用模块名.,但是这样容易导致冲突所以不建议使用
```scss
@use '_test' as *;

p {
  font-size: $font-size;
}
```
>此时使用test.反而会报错
### 定义私有成员
如果加载的模块内部有变量只想在模块内使用，可使用-或_定义在变量头即可,此时这个变量就只能在当前scss中可以使用,外部导入时不可以使用
```scss
$-font-size:14px;
* {
    margin: 0;
    padding: 0;
    font-size: $-font-size;
    color: #333;
}
```
### 定义默认值
 通过！default能变量定义默认值
 ```scss
 $font-size:14px !default;
* {
    margin: 0;
    padding: 0;
    font-size: $font-size;
    color: #333;
}
 ```
@use引入时可通过with(...)修改默认值
```scss
@use 'use/common' with ( $font-size:16px, );
@use 'use/global' as *;
@use 'use/global' as g2;
common.$font-size:28px; // 也可能通过这种方式覆盖
body {
    font-size: common.$font-size;
    @include base('#FFF');
    @include g2.base('#000');
}
```
>这个修改的默认值不止对导入的scss起效,原来的被导入的scss中的默认值也会被修改
### 默认加载index.scss
直接使用@use '文件夹' 会导入其中的index.scss,通过这一点我们可以像store模块的单一出口一样,把需要导入到其他scss的模块都导入到index中,再在别的scss中导入这个index.scss
>但是这样二次导入会导致原scss中的变量无法使用
### @use使用总结
- @use引入同一个文件多次，不会重复引入，而@import会重复引入
- @use引入的文件都是一个模块，默认以文件名作为模块名，可通过as alias取别名
- @use引入多个文件时，每个文件都是单独的模块，相同变量名不会覆盖，通过模块名访问，而@import变量会被覆盖
- @use方式可通过 @use 'xxx' as `*`来取消命名空间，建议不要这么做
- @use模块内可通过`$-` 或`$_`来定义私有成员，也就是说_或者-开头的Variables mixins functions 不会被引入
- @use模块内变量可通过！default 定义默认值，引入时可通用with（...）的方式修改
- 可定义-index.scss或_index.scss来合并多个scss文件，它@use默认加载文件
## @forward
通过 `@forward`加载一个模块的成员，并将这些成员当作自己的成员对外暴露出去，类似于类似于 es6 的 export ...，通常用于跨多个文件组织 Sass 库
### 转发
创建`forward/_common.scss`
```scss
$font-size:14px !default;
* {
    margin: 0;
    padding: 0;
    font-size: $font-size;
    color: #333;
}

@function column-width($col, $total) {
    @return percentage($col/$total);
}

@mixin bgColor($bg-color:#f2f2f2) {
    background-color: $bg-color;
}
```
创建启动合并bootstrap.scss
```scss
@forward 'uses/common';
```
使用
```scss
@use 'bootstrap';
.body {
    font-size: bootstrap.$font-size;
    width: bootstrap.column-width(3, 12);
    @include bootstrap.bgColor('#F00');
}
```
>当同时转发多个文件时其中有同名的变量时会报错
### 选择性转发
默认情况下，`@forward` 会将一个模块中所有成员都转发，如果只想转发某些成员，当你不想转发所有变量、函数、混入时，可使用
- `@forward "module" hide $var, mixinName, fnName` 禁止转发某些成员
- `@forward "module" show $var, mixinName, fnName` 只转发某些成员
各个成员通过逗号 `,` 分隔开，如果成员是变量，不能省略 `$` 符号。
```scss
@forward 'uses/common' as com-* hide com-bgColor,$com-font-size;
@forward 'uses/global' as glob-* show base;
```
### 转发时定义前缀
如果一定要转发多个模块中的同名变量,可以使用`@forward "" as -*`语法来为每个模块的变量名前添加前缀,这样就不会冲突了
```scss
@forward 'uses/common' as com-*;
@forward 'uses/global' as glob-*;
```
使用
```scss
@use 'bootstrap';
.body {
    font-size: bootstrap.$com-font-size;
    width: bootstrap.com-column-width(3, 12);
    @include bootstrap.com-bgColor('#F00');
    @include bootstrap.glob-base('#000');
}
```
>如果使用了前缀,那么在==之后==使用时都要带着这个前缀,在==选择转发中也需要带上前缀==
### 转发时配置模块的成员
类似@use在导入时可以配置模块内的一些默认值,@forward导出也可以定义默认值
```scss
@forward 'uses/common' as com-* with ( $font-size:30px !default);
@forward 'uses/global' as glob-* show glob-base;
```
使用
```scss
@use 'bootstrap' with ($com-font-size:50px);
.body {
    font-size: bootstrap.$com-font-size;
    width: bootstrap.com-column-width(3, 12);
    @include bootstrap.com-bgColor('#000');
    @include bootstrap.glob-base('#000');
}
```
### @use与@forward一起使用的情况
当一个模块里面须要同时使用@use与@forward时，建议先使用@forwar后再使用@use,因为在一个模块链上,如果某一个模块在中间模块被@use过,那之后就不能再配置默认值了,也就是说只有第一次@use和中间的@forward可以修改默认值,其他的都不能
## @at-root
@at-root可以使被嵌套的选择器或属性跳出嵌套
### 使用
```scss
@at-root <selector>{
    ...
}
```
**作用某个选择器使其跳出嵌套**
```scss
.parent {
  font-size: 12px;
  @at-root .child {
    font-size: 14px;
    @at-root .son {
      font-size: 16px;
    }
  }
}
```
编译结果
```css
.parent {
  font-size: 12px;
}
.child {
  font-size: 14px;
}
.son {
  font-size: 16px;
}
```
**作用某些选择器使其跳出嵌套**
```scss
.parent {
  font-size: 12px;
  @at-root {
    .child-1 {
      font-size: 14px;
    }
    .child-2 {
      font-size: 16px;
    }
  }
}
```
编译结果
```css
.parent {
  font-size: 12px;
}
.child-1 {
  font-size: 14px;
}

.child-2 {
  font-size: 16px;
}
```
### 使用@at-root结合#{&}实现BEM效果
>BEM就是给css类起名的一种规范

官网学习：[https://en.bem.info/methodology/quick-start/](https://en.bem.info/methodology/quick-start/)

BEM完整命名规则：block-name__element-name--modifier-name (也可以换成驼峰式命名)

官方网站最新推出：block-name__element-name_modifier-name
例
```css
.block{width: 1000px;}
.block__element{font-size: 12px;}
.block--modifier{font-size: 14px;}
.block__element--modifier{font-size: 16px;} 
```
**三种实现方式**
1. 
```scss
.block {
  width: 1000px;
  @at-root #{&}__element {
    font-size: 12px;
    @at-root #{&}--modifier {
      font-size: 16px;
    }
  }
  @at-root #{&}--modifier {
    font-size: 14px;
  }
}
```
2. 
```scss
.block {
  width: 1000px;
  @at-root {
    #{&}__element {
      font-size: 12px;
      @at-root #{&}--modifier {
        font-size: 16px;
      }
    }
    #{&}--modifier {
      font-size: 14px;
    }
  }
}
```
3. 
```scss
.block {
  width: 1000px;
  &__element {
    font-size: 12px;
    &--modifier {
      font-size: 16px;
    }
  }
  &--modifier {
    font-size: 14px;
  }
}
```
编译后的结果都是
```css
.block {
  width: 1000px;
}
.block__element {
  font-size: 12px;
}
.block__element--modifier {
  font-size: 16px;
}
.block--modifier {
  font-size: 14px;
}
```
### @at-root (without: …)和@at-root (with: …)的使用
```scss
@media screen {
  .parent {
    font-size: 12px;
    @at-root {
      .child {
        font-size: 14px;
        .son {
          font-size: 16px;
        }
      }
    }
  }
}
```
编译结果
```css
@media screen {
  .parent {
    font-size: 12px;
  }
  .child {
    font-size: 14px;
  }
  .child .son {
    font-size: 16px;
  }
}
```
默认@at-root只会跳出选择器嵌套，而不能跳出@media或@support，如果要跳出这两种，则需使用@at-root (without: media)，@at-root (without: support)。这个语法的关键词有四个：
1. all（表示所有） 
2. rule（表示常规css） 
3. media（表示media） 
4. supports（表示supports）
```scss
@media screen {
  .parent {
    font-size: 12px;
    @at-root (without: media) {
      .child {
        font-size: 14px;
        .son {
          font-size: 16px;
        }
      }
    }
  }
}
```
编译结果
```css
@media screen {
  .parent {
    font-size: 12px;
  }
}
.parent .child {
  font-size: 14px;
}
.parent .child .son {
  font-size: 16px;
}
```