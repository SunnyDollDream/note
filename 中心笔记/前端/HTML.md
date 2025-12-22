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
超链接==默认是在当前窗口跳转页面==，添加 **`target="_blank"`** 实现**新窗口**打开页面。
>target属性还可以跟iframe的name,这样点击超链接后的新窗口会在iframe元素中打开(target就是打开这个链接的地方)

**常用属性**
1. target
常见取值：

| 值         | 说明                |
| --------- | ----------------- |
| `_self`   | 当前窗口（默认）          |
| `_blank`  | 新窗口 / 新标签         |
| `_parent` | 父 frame           |
| `_top`    | 顶级窗口              |
| 自定义 name  | 指向 frame / iframe |
2. `rel`（安全与语义非常重要）
```html
<a href="..." target="_blank" rel="noopener noreferrer">
```
- 为什么需要 `rel`？
	当使用 `_blank` 时：
	- 新页面可以通过 `window.opener` 操作原页面
	- 存在安全风险（tabnabbing）
- 常见值

| 值            | 作用          |
| ------------ | ----------- |
| `noopener`   | 阻断 opener   |
| `noreferrer` | 不发送 Referer |
| `nofollow`   | SEO：不传递权重   |
| `external`   | 外部链接        |
| `ugc`        | 用户生成内容      |
3. download
```html
<a href="/file.zip" download>下载文件</a>
<a href="/file.zip" download="demo.zip">重命名</a>
```
- 强制下载而非打开
- 受同源策略限制
 4. `title`
`<a title="查看详情">详情</a>`
- 悬浮提示
- 不建议承载核心信息（可访问性差）
拓展：开发初期，不确定跳转地址，则 href 属性值写为 **#**，表示**空链接**，页面不会跳转，在当前页面刷新一次。
```html
<a href="https://www.baidu.com/">跳转到百度</a>
<a href="/user/list">站内链接</a>
<!-- 跳转到本地文件：相对路径查找 --> 
<!-- target="_blank" 新窗口跳转页面 --> 
<a href="./01-标签的写法.html" target="_blank">跳转到01-标签的写法</a>
<a href="/files/report.pdf">下载</a>
<!-- 开发初期，不知道超链接的跳转地址，href属性值写#，表示空链接，不会跳转 -->
<a href="#">空链接</a>
```
>`#`和`""`的区别
>#	跳到顶部（hash 变化）
>""	重新请求当前 URL

href标签还可以带邮箱地址和锚点链接
```html
<a href="mailto:19374292610@163.com">给我写信</a>
<!-- 需要设备上有邮箱的客户端才能打开 --> 
<a href="tel:10086">拨号</a>

<a href="#section1">跳转到某一节</a>

<h2 id="section1">第一节</h2>
```
>锚点的本质: 修改 URL 的 hash,不刷新页面

`<a>`标签还可以内嵌元素,此时内嵌元素的整个占用区域都会被认为是`<a>`的交互区域
```html
  <a href="/detail">
    <div class="card">
      <h3>标题</h3>
      <p>描述</p>
    </div>
  </a>
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

| 标签名   | 说明    |
| ----- | ----- |
| table | 表格    |
| tr    | 行     |
| th    | 表头单元格 |
| td    | 内容单元格 |
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
```html
<form action="/login" method="post">
  <!-- 表单控件 -->
</form>
```
功能:
1. **定义一次表单提交的边界**（哪些控件属于同一次提交）
2. **收集表单控件的值**（name → value）
3. **按照指定方式向服务器发起请求**
可以理解为：
> `<form>` 是一个“请求生成器”，而不是一个普通容器。

使用场景：
- 登录页面
- 注册页面
- 搜索区域
常见属性:
1. `action`
	- **含义**：表单提交的目标 URL
	- 若省略：
	    - 提交到 **当前页面地址**
2. `method`
	 - **GET**
	    - 数据拼接在 URL 查询串中
	    - 有长度限制
	    - 可被缓存、收藏
	    - 适合：查询、筛选
	- **POST**
	    - 数据放在请求体中
	    - 理论上无限制
	    - 不直接暴露在 URL
	    - 适合：登录、提交表单、修改数据
3. `enctype`（提交数据的编码方式）
	- 常见值：

| 值                                   | 用途           |
| ----------------------------------- | ------------ |
| `application/x-www-form-urlencoded` | 默认，普通表单      |
| `multipart/form-data`               | **文件上传必须使用** |
| `text/plain`                        | 调试用，很少使用     |

> **只有具备 `name` 属性的表单控件，才会被提交**
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
常用属性：

| 属性            | 作用            |
| ------------- | ------------- |
| `name`        | 提交字段名         |
| `value`       | 默认值           |
| `placeholder` | 占位提示          |
| `required`    | 必填            |
| `readonly`    | 只读            |
| `disabled`    | 禁用（**不会被提交**） |
>input默认就一个框,前面的文字需要自己手动加
```html
文本框: <input type="text">
```
###### 单选框
```html
<input type="radio" name="gender" value="male" checked> 男
<input type="radio" name="gender" value="female"> 女
```
>单选框和文本框一样,要带提示信息都得额外写

常用属性

| 属性名     | 作用   | 特殊说明                                   |
| ------- | ---- | -------------------------------------- |
| name    | 控件名称 | 控件==分组==,同组中只能选中一个(单选功能)(不是显示这个选项代表什么) |
| checked | 默认选中 | 属性名和属性值一样,简写为一个单词                      |

> 提示：name 属性值自定义。
###### 上传文件
默认情况下，文件上传表单控件只能上传一个文件，添加 multiple 属性可以实现文件多选功能。
```html
<input type="file" name="avatar" multiple>
```
多选要自己在上传文件时按Shift选中多个文件,并不是可以多次点击传不同的文件
###### 多选框
多选框也叫复选框，默认选中：checked。
```html
<input type="checkbox" checked> 敲前端代码
```
###### 隐藏字段（hidden）
`<input type="hidden" name="userId" value="123">`
- 不显示
- 常用于：
    - 传递状态
    - CSRF Token
    - 业务上下文信息
###### 提交与重置
```html
<input type="submit" value="提交">
<input type="reset" value="重置">
```
##### 按钮
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
##### 文本域
作用：多行输入文本的表单控件。
![[Pasted image 20250423210957.png]]
```html
<textarea name="content" rows="5" cols="30">默认提示文字</textarea>
```
> 注意点：
> - 实际开发中，使用 CSS 设置 文本域的尺寸
> - 实际开发中，一般禁用右下角的拖拽功能
> - 这个标签中间的提示文字不是虚文字,是预置在文本框里的,双标签之间不应该换行,否则会多好几个空格
##### 下拉菜单
标签：select 嵌套 option，select 是下拉菜单整体，option是下拉菜单的每一项。
```html
<select name="city">
  <option value="bj">北京</option>
  <option value="sh">上海</option>
  <option value="gz">广州</option>
  <option value="sz">深圳</option>
  <option value="wh" selected>武汉</option>
</select>
```
> 默认显示第一项，**selected** 属性实现**默认选中**功能。
> **multiple**属性实现多选

##### label 标签
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
##### 表单提交的默认行为
**浏览器会做什么？**
当点击提交按钮时：

1. 触发表单校验（required、pattern 等）
2. 收集所有 **未 disabled 且有 name 的控件**
3. 按 `method + action + enctype` 发起请求
4. 页面跳转（刷新）
**如何阻止默认提交？**
```html
<form onsubmit="return false;">
```
或 JS：
```js
form.addEventListener('submit', e => {
  e.preventDefault();
})
```
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
- **语义化标签**指的是 HTML 中那些 _有意义_ 的元素（如 `<header>`、`<nav>`、`<article>`、`<section>`、`<footer>` 等），它们不仅仅用于样式布局，而是表达内容的结构和语义。 
- 相比之下，用大量 `<div>` 或 `<span>`（无语义标签）来构建页面结构，只是从表现层组织，而不表达内容含义。
##### 语义化标签对SEO的作用
1. **帮助搜索引擎理解内容结构**
    - 搜索引擎（比如 Google）的爬虫在抓取网页时，会尝试解析页面结构。语义化标签给出了明确的结构提示（这是导航、这是主内容、这是文章、这是次要内容等），让爬虫更容易理解哪些内容是重要的。
    - 例如，`<main>` 标签可以明确告诉搜索引擎 “这是页面的主要内容” 。
    - 用 `<article>` 标识一个自成体系的内容（比如一篇博客文章），有助于搜索引擎把它当作独立、有价值的内容来处理。
2. **清晰的层次结构 / 关键词信号**
    - 使用语义标签 + 合理的标题标签（`<h1>`、`<h2>` 等）可以构建内容的层级结构。搜索引擎通过这些标签判断主题与子主题之间的关系。
    - 语义标签也帮助表达关键词的重要性。例如，把标题文本放在 `<h1>` 或放在 `<strong>`，比简单用 `<div>` + CSS 强调，对搜索引擎更友好。
3. **改进爬取与索引效率**
    - 语义化结构让爬虫更快、更准确地发现关键内容。因为 “重要内容区域” 可通过语义标签直观表达。
    - 这样做有助于提高索引质量，搜索引擎更容易决定哪些内容值得优先抓取和存储。
4. **增强结构化数据 / 富摘要**
    - 语义标签与结构化数据（如 Schema.org）结合得很好。使用 `<article>`、`<time>`、`<figure>` 等语义元素，有助于搜索引擎识别特定类型内容（文章、时间、图片说明等），进而生成富摘要 (rich snippets)。
    - 富摘要能提升搜索结果中的展示（例如评分、日期、事件等），进而提高点击率 (CTR) 。
5.  **提升可访问性 (Accessibility)**
    - 语义标签也对无障碍 (accessibility) 十分重要。屏幕阅读器 (screen reader) 能够通过语义元素更好地识别页面结构（例如导航区、主要内容、页脚等）。 
    - 更好的可访问性通常也意味着更好的用户体验 (UX)。而搜索引擎非常重视用户体验，因此间接也有 SEO 收益。
## 特殊标签
### meta标签
`<meta>` 是 HTML `<head>` 内用于提供页面元数据（metadata）的标签。元数据不是页面内容本身，而是描述页面的信息，供浏览器、搜索引擎、社交平台或其他用户代理使用。它没有可见渲染（通常不会在页面中直接显示）。
**必须放在 `<head>` 内（放在其他地方浏览器可能忽略或行为不确定）。**
#### 常见属性说明
1. `charset`
定义文档字符编码。**应尽可能放在 `<head>` 的最前面**（早于 CSS/JS/其他 meta），以便浏览器正确解析HTML。
`<meta charset="utf-8">`
>注意：在老浏览器或某些环境下，浏览器通常要求在文档开头的前 1024 字节内出现编码声明，否则可能无法正确识别。
2. `name` + `content`
最常见的形式，用于提供多种元信息。
- `viewport`：响应式移动设备所必需。
    `<meta name="viewport" content="width=device-width, initial-scale=1">`
    常见扩展：`maximum-scale=1, user-scalable=no`（但一般建议允许缩放以利无障碍）。
- `description`：页面摘要，搜索引擎常在搜索结果中显示（长度通常在 150–160 字左右）。
    `<meta name="description" content="这是页面的简短描述...">`
- `keywords`：关键词列表（现在搜索引擎多数忽略，但某些系统仍可能使用）。
    `<meta name="keywords" content="HTML, meta, 教程">`
- `robots`：指示搜索引擎爬虫如何索引页面与跟踪链接。
    `<meta name="robots" content="index, follow">  `
    `<meta name="robots" content="noindex, nofollow">`
    也可针对特定爬虫：`googlebot`、`bingbot` 等。
- `referrer`：控制通过 Referer 头发送的引用信息，例如 `no-referrer`, `origin`, `strict-origin-when-cross-origin`。
- `theme-color`：用于移动浏览器（如 Chrome）设置地址栏/工具栏颜色。
    `<meta name="theme-color" content="#ffffff">`
- `author`、`application-name` 等元信息也常见，主要供工具或平台使用。
3. `http-equiv`
模拟 HTTP 响应头的元信息，浏览器会把它当作来自服务器的头来处理（注意并非所有头都可用或建议使用）。
- `Content-Type`（已被 `charset` 取代）：
    `<meta http-equiv="Content-Type" content="text/html; charset=utf-8">`
- `X-UA-Compatible`（IE 兼容模式）：
    `<meta http-equiv="X-UA-Compatible" content="IE=edge">`
- `refresh`（页面刷新/跳转——通常**不推荐**用于 SEO/可访问性）：
    `<meta http-equiv="refresh" content="5;url=https://example.com/">`
- `Content-Security-Policy`（可以通过 `<meta>` 设置简单的 CSP，但更建议通过 HTTP 头）：
    `<meta http-equiv="Content-Security-Policy" content="default-src 'self'">`
4. 社交平台与开放图（Open Graph）/Twitter Card
社交平台抓取页面时常用 meta（注意：这些用 `property` 或自定义 `name`，不是标准 `name`
```html
<meta property="og:title" content="页面标题">
<meta property="og:description" content="页面描述">
<meta property="og:image" content="https://example.com/image.jpg">
<meta name="twitter:card" content="summary_large_image">
```
这些能控制分享到 Facebook、Twitter、微信等平台时显示的标题、摘要和缩略图。
#### 使用顺序与最佳实践
1. **把 `<meta charset>` 放最前面**（或至少在任何外部资源之前），以避免编码错误。
2. 使用 `viewport` 来保证移动设备显示正常。
3. 使用简洁且有针对性的 `description`（对 SEO 与点击率有帮助）。
4. 避免用 `<meta http-equiv="refresh">` 做强制跳转；使用服务器端重定向更合适。
5. 对安全策略（CSP）优先通过服务器头发送，只有在无法配置服务器时才使用 `<meta http-equiv="Content-Security-Policy">`。
6. 为社交分享准备 Open Graph / Twitter Card 元数据（视目标平台而定）。
7. 不要把隐私敏感信息放在 meta 中（它们通常可被爬虫读取）。
## 字符实体

| 显示结果 | 描述  | 实体名称    |
| ---- | --- | ------- |
|      | 空格  | \&nbsp; |
| <    | 小于号 | \&lt;   |
| >    | 大于号 | \&gt    |
>在代码中敲空格不论输入几个显示时都只会识别成一个

## `<frameset>/<frame>`(已废弃)
`<frameset>` 和 `<frame>` 是 **HTML 4 时代用于把浏览器窗口切分为多个独立文档区域的机制**。
它们的设计目标是：
- 一个浏览器窗口
- 同时显示多个 **完全独立的 HTML 文档**
- 每个区域都可以单独加载、刷新、跳转
这是**早期 Web 对“多视图布局”的解决方案**。
### `<frameset>`：页面结构的替代者
1. `<frameset>` 的本质
`<frameset>` **不是普通容器**，它是：
- 用来**替代 `<body>` 的**
- 定义页面的“分割方式”
⚠ 一个 HTML 文档中：
- **要么使用 `<body>`**
- **要么使用 `<frameset>`**
- 二者不能共存
2. 基本结构示例
```html
<!DOCTYPE html>
<html>
  <head>
    <title>Frameset 示例</title>
  </head>

  <frameset cols="30%,70%">
    <frame src="menu.html">
    <frame src="content.html">
  </frameset>
</html>
```
含义：
- 浏览器窗口被 **纵向切成两列**
- 左边 30%，右边 70%
- 每一列加载一个独立 HTML 文档
>切分时使用`*`占位符可以使对应的部分占据剩余空间
3. 嵌套 frameset
```html
<frameset rows="20%,80%">
  <frame src="header.html">
  <frameset cols="30%,70%">
    <frame src="menu.html">
    <frame src="content.html">
  </frameset>
</frameset>
```
### `<frame>`：真正加载内容的单元
1. `<frame>` 的作用
`<frame>` 表示 **frameset 切出来的一个“窗口”，用于加载一个 HTML 文档**
它本身 **不包含内容**，只负责加载外部页面。
2. 常见属性
```html
<frame src="menu.html" name="menuFrame">
```
- `src`
	- 加载的页面地址
- `name`（非常重要）
	- 用于 **链接的 target 指向**
	- 实现“点击左边，右边刷新”
- `scrolling`
	`<frame scrolling="yes | no | auto">`
	- 是否显示滚动条
- noresize
	- 禁止用户拖拽调整 frame 大小
- `frameborder`（旧属性）
	`<frame frameborder="0">`
	- 控制边框显示
	- 非 CSS 时代的产物
### 与 `<iframe>` 的本质区别（非常重要）

|对比点|frame / frameset|iframe|
|---|---|---|
|是否废弃|是|否|
|是否替代 body|是|否|
|布局方式|切分窗口|普通元素|
|CSS 控制|极弱|完全支持|
|SEO|极差|可控|
|使用场景|已淘汰|嵌入第三方内容|
> frameset 是“页面级切割”，iframe 是“页面内嵌”。