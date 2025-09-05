HTML即HyperText Markup Language——超文本标记语言
- 超文本：链接
- 标记：标签，带尖括号的文本
### 标签结构
- 标签要成对出现，中间包裹内容
- <>里面放英文字母（标签名）
- 结束标签比开始标签多 /
- 标签分类
	- 双标签:成对出现的标签(如strong加粗)
	- 单标签:只有开始标签,没有结束标签(如br换行,hr水平线)
```html
<strong>需要加粗的文字<strong>
<br>
<hr>
```
### HTML骨架
- html：整个网页
- head：网页头部，用来存放给==浏览器==看的信息，例如 CSS
    - title：网页标题
- body：网页主体，用来存放给==用户==看的信息，例如图片、文字
```html
<!DOCTYPE html>------描述文档的定义类型为html，此处为html5的定义方式
<html>----根元素，声明当前文档为html文档结构的根元素
	<head>----头部节点元素，用于描述整个页面的属性信息，例如：描述页面使用编码格式
		<meta charset="utf-8" />--页面编码标准
		<title>首页</title>---标题
	</head>----结束头部信息
	<body>---体内容,html标签一般写在体内容中
		你好，这是我的第一个界面！
	</body>--体内容结束
</html>---html文档结束
```
### 标签的关系
作用：明确标签的书写位置，让代码格式更整齐
- 父子关系（嵌套关系）：子级标签换行且缩进（Tab键）
```html
<html>
	<head></head>
</html>	
```
- 兄弟关系（并列关系）：兄弟标签换行要对齐
```html
<head></head>
<body></body>
```
### 注释
概念：注释是对代码的解释和说明，能够提高程序的可读性，方便理解、查找代码。
注释不会在浏览器中显示。
在 VS Code 中，**添加 / 删除**注释的快捷键：**Ctrl + /**
```html
<!-- 我是 HTML 注释 -->
```
### 标签
#### 标题
一般用在新闻标题、文章标题、网页区域名称、产品名称等等。
```html
<h1>一级标题</h1>
<h2>二级标题</h2>
<h3>三级标题</h3>
<h4>四级标题</h4>
<h5>五级标题</h5>
<h6>六级标题</h6>
```
显示特点：
- 文字加粗
- 字号逐渐减小
- 独占一行（换行）
用法习惯
1. h1 标签在一个网页中只能用一次，用来放新闻标题或网页的 logo
2. h2 ~ h6 没有使用次数的限制
>h和p标签的效果是冲突的,不能把h嵌套在p中再对p进行修改来影响h
#### 段落
一般用在新闻段落、文章段落、产品描述信息等等。
```html
<p>段落</p>
```
显示特点：
- 独占一行
- 段落之间存在间隙
- 会自动换行,适配窗口大小
#### 文本

显示一段文本(直接写一段文本的效果相同)
```html
<label>text</label>
```
#### 换行与水平线标签
- 换行：br(浏览器不识别直接写在html里的换行,会变成一个空格)
- 水平线：hr
```html
<br>
<hr>
```
#### 文本格式化标签
作用：为文本添加特殊格式，以突出重点。常见的文本格式：加粗、倾斜、下划线、删除线等。
```html
	<!-- 加粗,两个效果一致 -->
    <strong>
        这是一个测试HTML
    </strong>
    <b>
        这是一个测试HTML
    </b>
	<!-- 倾斜 -->
    <em>
        这是一个测试HTML
    </em>
    <i>
        这是一个测试HTML
    </i>
	<!-- 下划线 -->
    <ins>
	    ceshi
    </ins>
    <u>
        这是一个测试HTML
    </u>
	<!-- 删除线 -->
    <del>
        这是一个测试HTML
    </del>
    <s>
        这是一个测试HTML
    </s>
```
> 提示：strong、em、ins、del 标签自带强调含义（语义）,也就是一般使用第一种写法
#### 图像标签
作用：在网页中插入图片
```html
<img  src="图片的 URL">
```
src用于指定图像的位置和名称，是<img>的必须属性。
##### 图像属性

| 属性     | 作用    | 说明                |
| ------ | ----- | ----------------- |
| alt    | 替换文本  | 图片无法显示的时候显示的文字    |
| title  | 提示文本  | 鼠标悬停在图片上面的时候显示的文字 |
| width  | 图片的宽度 | 值为数字,没有单位         |
| height | 图片的高度 | 值为数字,没有单位         |
宽和高如果只修改了一个,浏览器会默认等比例缩放另一个,即锁定长宽比
##### 属性语法
- 属性名="属性值"
- 属性写在尖括号里面，标签名后面，标签名和属性之间用空格隔开，不区分先后顺序
![[Pasted image 20250421213516.png]]
#### 超链接标签
作用：点击跳转到其他页面。
```html
<a href="https://www.baidu.com">跳转到百度</a>
```
**href 属性值是跳转地址，是超链接的必须属性。**(也可以填本地的文件,比如html和图片)
超链接==默认是在当前窗口跳转页面==，添加 **target="(下划线)blank"** 实现**新窗口**打开页面。
>target属性还可以跟iframe的name,这样点击超链接后的新窗口会在iframe元素中打开(target就是打开这个链接的地方)

拓展：开发初期，不确定跳转地址，则 href 属性值写为 **#**，表示**空链接**，页面不会跳转，在当前页面刷新一次。
```html
<a href="https://www.baidu.com/">跳转到百度</a>

<!-- 跳转到本地文件：相对路径查找 --> 
<!-- target="_blank" 新窗口跳转页面 --> 
<a href="./01-标签的写法.html" target="_blank">跳转到01-标签的写法</a>

<!-- 开发初期，不知道超链接的跳转地址，href属性值写#，表示空链接，不会跳转 -->
<a href="#">空链接</a>
```
href标签还可以带邮箱地址和锚点链接
```html
<a href="mailto:19374292610@163.com">给我写信</a>
<!-- 需要设备上有邮箱的客户端才能打开 --> 
<a href="#">空链接</a>

<a href="#">空链接</a>
```
#### 内联框架元素
**`<iframe>`** **HTML**元素代表嵌套的**浏览上下文** ，将另一个 HTML 页面嵌入到当前页面中。
```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title>iframe</title>
		<style>
			iframe {
				border: 1px solid black;
				width: 100%;
				/* takes precedence over the width set with the HTML width attribute */
			}
		</style>
	</head>
	<body>
		<iframe id="inlineFrameExample" title="Inline Frame Example" width="300" height="200"
			src="https://www.openstreetmap.org/export/embed.html?bbox=-0.004017949104309083%2C51.47612752641776%2C0.00030577182769775396%2C51.478569861898606&amp;layer=mapnik">
		</iframe>
	</body>
</html>
```
**属性:**
```
1. height: 框架的高度（以 CSS 像素为单位）。默认值为 150 。
2. width: 框架的宽度（以 CSS 像素为单位）。默认值为 300 。
3. 嵌入式浏览上下文的可定位名称。此名称可用于 <a> 、 <form> 或 <base> 元素的 target 属性； <input> 或 <button> 元素的 formtarget 属性；或 window.open() 方法中的 windowName 参数。
4. src: 要嵌入页面的 URL。使用 about:blank 值可嵌入符合同源策略的空页面。
```
#### 音频标签
```html
<audio src="音频的 URL"></audio>
```
常用属性

| 属性            | 作用       | 特殊说明                    |
| ------------- | -------- | ----------------------- |
| **src(必须属性)** | 音频URL    | 支持格式:MP3,Ogg,Wav        |
| controls      | 显示音频控制面板 |                         |
| loop          | 循环播放     |                         |
| autoplay      | 自动播放     | 为了提升用户体验,浏览器一般会禁用自动播放功能 |

> 拓展：书写 HTML5 属性时，如果属性名和属性值相同，可以简写为一个单词。
```html
<!-- 在 HTML5 里面，如果属性名和属性值完全一样，可以简写为一个单词 -->
<audio src="./media/music.mp3" controls loop autoplay></audio>
```
#### 视频标签
```html
<video src="视频的 URL"></video>
```
常用属性

| 属性            | 作用       | 特殊说明                    |
| ------------- | -------- | ----------------------- |
| **src(必须属性)** | 视频URL    | 支持格式:MP4,WebM,Ogg       |
| controls      | 显示视频控制面板 |                         |
| loop          | 循环播放     |                         |
| muted         | 静音播放     |                         |
| autoplay      | 自动播放     | 为了提升用户体验,浏览器支持在静音状态自动播放 |

```html
<!-- 在浏览器中，想要自动播放，必须有 muted 属性 -->
<video src="./media/vue.mp4" controls loop muted autoplay></video>
```
#### 列表
作用：布局内容排列整齐的区域。
列表分类：无序列表、有序列表、定义列表。
##### 无序列表
作用：布局排列整齐的**不需要规定顺序**的区域。
标签：ul 嵌套 li，ul 是无序列表，li 是列表条目。
```html
<ul>
  <li>第一项</li>
  <li>第二项</li>
  <li>第三项</li>
  ……
</ul>
```
> 注意事项：
> - ul 标签里面只能包裹 li 标签
> - li 标签里面可以包裹任何内容
##### 有序列表
作用：布局排列整齐的**需要规定顺序**的区域。
标签：ol 嵌套 li，ol 是有序列表，li 是列表条目。
```html
<ol>
  <li>第一项</li>
  <li>第二项</li>
  <li>第三项</li>
  ……
</ol>
```
> 注意事项：
> - ol 标签里面只能包裹 li 标签
> - li 标签里面可以包裹任何内容
##### 定义列表
标签：dl 嵌套 dt 和 dd，dl 是定义列表，dt 是定义列表的标题，dd 是定义列表的描述 / 详情。
```html
<dl>
  <dt>列表标题</dt>
  <dd>列表描述 / 详情</dd>
   ……
</dl>
```

> 注意事项：
> - dl 里面只能包含dt 和 dd
> - dt 和 dd 里面可以包含任何内容
#### 表格
网页中的表格与 Excel 表格类似，用来展示数据。
##### 基本使用
标签：table 嵌套 tr，tr 嵌套 td / th。

|标签名|说明|
|---|---|
|table|表格|
|tr|行|
|th|表头单元格|
|td|内容单元格|
> 提示：在网页中，**表格默认没有边框线**，使用 **border 属性**(加在table标签里,值为边框粗细)可以为表格添加边框线。

```html
<table border="1">
  <tr>
    <th>姓名</th>
    <th>语文</th>
    <th>数学</th>
    <th>总分</th>
  </tr>
  <tr>
    <td>张三</td>
    <td>99</td>
    <td>100</td>
    <td>199</td>
  </tr>
  <tr>
    <td>李四</td>
    <td>98</td>
    <td>100</td>
    <td>198</td>
  </tr>
  <tr>
    <td>总结</td>
    <td>全市第一</td>
    <td>全市第一</td>
    <td>全市第一</td>
  </tr>
</table>
```
##### table属性
表格相关的主要属性：
（table）:table中的属性设置的是整个表格的属性
1. **border**属性：设置表格的边框宽度（同时设置表格外边框，以及每个单元格的边框）
2. **cellspacing**属性：设置单元格空隙
3. **cellpadding**属性：设置当前表格中单元格的内边距
4. **width、height**属性：设置当前表格的宽度、⾼度
5. **align**属性：设置当前表格在⽹⻚中的⽔平位置（left|center|right）
6. **bgcolor**属性：设置表格行的的背景颜⾊
7. **background**属性：设置当前表格的背景图⽚
```html
<table align="center" border="1" width="850" cellpadding="1" cellspacing="1"></table>
```
##### 表格结构标签-了解
作用：用表格结构标签把内容划分区域，让表格结构更清晰，语义更清晰。

| 标签名    | 含义   | 特殊说明   |
| ------ | ---- | ------ |
| thread | 表格头部 | 表格头部内容 |
| tbody  | 表格主体 | 主要内容区域 |
| tfoot  | 表格底部 | 汇总信息区域 |

> 提示：表格结构标签可以省略,因为视觉上并没有变化,只是让浏览器更好识别内容是什么
##### 合并单元格
作用：将多个单元格合并成一个单元格，以合并同类信息。
![[Pasted image 20250422013708.png]]
合并单元格的步骤：
1. 明确合并的目标
2. 保留**最左最上**的单元格，为td添加属性（取值是**数字**，表示需要**合并的单元格数量**）
    - **跨行合并**，保留最上单元格，添加属性 **rowspan**
    - **跨列合并**，保留最左单元格，添加属性 **colspan**
3. 删除其他单元格
```html
<table border="1">
  <thead>
    <tr>
      <th>姓名</th>
      <th>语文</th>
      <th>数学</th>
      <th>总分</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>张三</td>
      <td>99</td>
      <td rowspan="2">100</td>
      <td>199</td>
    </tr>
    <tr>
      <td>李四</td>
      <td>98</td>
      <!-- <td>100</td> -->
      <td>198</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <td>总结</td>
      <td colspan="3">全市第一</td>
      <!-- <td>全市第一</td>
      <td>全市第一</td> -->
    </tr>
  </tfoot>
</table>
```
> 注意：不能跨表格结构标签合并单元格（thead、tbody、tfoot）。
#### 表单
作用：收集用户信息。
使用场景：
- 登录页面
- 注册页面
- 搜索区域
##### input 标签
input 标签 type 属性值不同，则功能不同。
```html
<input type="..." >
```

| type属性值  | 说明           |
| -------- | ------------ |
| text     | 文本框,用于输入单行文本 |
| password | 密码框          |
| radio    | 单选框          |
| checkbox | 多选框          |
| file     | 上传文件         |
| email    | 会判断输入内容是否为邮箱 |
| date     | 日期           |
>input默认就一个框,前面的文字需要自己手动加
```html
文本框: <input type="text">
```
###### input 标签占位文本
占位文本：提示信息，文本框和密码框都可以使用(就是那个没有内容的时候的虚内容)。
```html
<input type="..." placeholder="提示信息">
```
##### 单选框
```html
<input type="radio" name="gender" checked> 男
<input type="radio" name="gender"> 女
```
>单选框和文本框一样,要带提示信息都得额外写

常用属性

| 属性名     | 作用   | 特殊说明                                  |
| ------- | ---- | ------------------------------------- |
| name    | 控件名称 | 控件==分组==,同组中只能选中一(单选功能)(不是显示这个选项代表什么) |
| checked | 默认选中 | 属性名和属性值一样,简写为一个单词                     |

> 提示：name 属性值自定义。
##### 上传文件
默认情况下，文件上传表单控件只能上传一个文件，添加 multiple 属性可以实现文件多选功能。
```html
<input type="file" multiple>
```
多选要自己在上传文件时按Shift选中多个文件,并不是可以多次点击传不同的文件
##### 多选框
多选框也叫复选框，默认选中：checked。
```html
<input type="checkbox" checked> 敲前端代码
```
#### 下拉菜单
标签：select 嵌套 option，select 是下拉菜单整体，option是下拉菜单的每一项。
```html
<select>
  <option>北京</option>
  <option>上海</option>
  <option>广州</option>
  <option>深圳</option>
  <option selected>武汉</option>
</select>
```
> 默认显示第一项，**selected** 属性实现**默认选中**功能。
#### 文本域
作用：多行输入文本的表单控件。
![[Pasted image 20250423210957.png]]
```html
<textarea>默认提示文字</textarea>
```
> 注意点：
> - 实际开发中，使用 CSS 设置 文本域的尺寸
> - 实际开发中，一般禁用右下角的拖拽功能
> - 这个标签中间的提示文字不是虚文字,是预置在文本框里的,双标签之间不应该换行,否则会多好几个空格
#### label 标签
作用：网页中，某个标签的说明文本。
![[Pasted image 20250423211030.png]]
经验：用 label 标签绑定文字和表单控件的关系，增大表单控件的点击范围。
![[Pasted image 20250423211038.png]]
- 写法一
    - label 标签只包裹内容，==不包裹表单控件==
    - 设置 label 标签的 for 属性值 和表单控件的 ==id== 属性值相同
```html
<input type="radio" id="man">
<label for="man">男</label>
```
- 写法二：使用 label 标签包裹文字和表单控件，不需要属性
```html
<label><input type="radio"> 女</label>
```
> 提示：支持 label 标签增大点击范围的表单控件：文本框、密码框、上传文件、单选框、多选框、下拉菜单、文本域等等。
#### 按钮
```html
<button type="">按钮</button>
```

| type属性值 | 说明                           |
| ------- | ---------------------------- |
| submit  | 提交按钮,点击后可以提交数据到后台(默认功能)      |
| reset   | 重置按钮,点击后将表单控件恢复默认值           |
| button  | 普通按钮,默认没有功能,一般配合javaScript使用 |

```html
<!-- form 表单区域 -->
<!-- action="" 发送数据的地址 -->
<form action="">
  用户名：<input type="text">
  <br><br>
  密码：<input type="password">
  <br><br>

  <!-- 如果省略 type 属性，功能是 提交 -->
  <button type="submit">提交</button>
  <button type="reset">重置</button>
  <button type="button">普通按钮</button>
</form>
```
> 提示：按钮需配合 ==form 标签==（表单区域）才能实现对应的功能。(需要把button和表单放在一个form里才行)
#### 语义化
##### 无语义的布局标签
作用：布局网页（划分网页区域，摆放内容）
- div：独占一行(大盒子)
- span：不换行(小盒子)
```html
<div>div 标签，独占一行</div>
<span>span 标签，不换行</span>
```
>div包裹效果类似于br
##### 有语义的布局标签

| 标签名     | 语义    |
| ------- | ----- |
| header  | 网页头部  |
| nav     | 网页导航  |
| footer  | 网页底部  |
| aside   | 网页侧边栏 |
| section | 网页区块  |
| article | 网页文章  |

## 字符实体

| 显示结果 | 描述  | 实体名称    |
| ---- | --- | ------- |
|      | 空格  | \&nbsp; |
| <    | 小于号 | \&lt;   |
| >    | 大于号 | \&gt    |
>在代码中敲空格不论输入几个显示时都只会识别成一个