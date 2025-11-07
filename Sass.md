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