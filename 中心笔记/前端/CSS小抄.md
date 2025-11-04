## transition
`transition: [属性名] [持续时间] [时间函数] [延迟时间]`
**各参数详细说明**

1. 属性名 (transition-property)
- 作用 ：指定哪些CSS属性需要应用过渡效果
- 取值 ：
  - all ：对所有可过渡的属性应用过渡效果
  - 具体属性名：如 width 、 height 、 color 、 background-color 、 filter 等
  - none ：不应用任何过渡效果
- 可过渡属性 ：大多数数值型和颜色相关的CSS属性都支持过渡，如尺寸、位置、颜色、透明度、边框等

2. 持续时间 (transition-duration)
- 作用 ：设置从一种状态过渡到另一种状态所需的时间
- 取值 ：以秒(s)或毫秒(ms)为单位的数值，如 0.3s 、 300ms
- 默认值 ： 0s （即无过渡效果，瞬时变化）
- 说明 ：数值越大，过渡效果越慢；数值越小，过渡效果越快 

3. 时间函数 (transition-timing-function)
- 作用 ：定义过渡动画的速度曲线，控制动画在不同时间点的速度
- 常用取值 ：
  - ease ：默认值，慢速开始，中间加速，结束时减速
  - linear ：匀速运动，速度保持不变
  - ease-in ：加速运动，开始慢，逐渐变快
  - ease-out ：减速运动，开始快，逐渐变慢
  - ease-in-out ：先加速后减速，结合了 ease-in 和 ease-out 的特点
  - cubic-bezier(n,n,n,n) ：自定义贝塞尔曲线，可以创建更复杂的速度变化效果 
  
4. 延迟时间 (transition-delay)
- 作用 ：设置过渡效果开始前的延迟时间
- 取值 ：以秒(s)或毫秒(ms)为单位的数值，如 0.2s 、 200ms
- 默认值 ： 0s （即不延迟，立即开始过渡）
## 伪类选择器
伪类选择器是CSS中一种特殊的选择器，用于选择处于特定状态的元素，而这些状态无法仅通过HTML结构来表示。伪类选择器通常以冒号(`:`)开头。

**伪类选择器的主要分类

1. **用户行为伪类**
这类伪类用于响应用户与页面的交互：

| 伪类              | 描述             | 示例                                                        |
| --------------- | -------------- | --------------------------------------------------------- |
| `:hover`        | 当鼠标悬停在元素上时     | `.item:hover { filter: brightness(0.9); }`                |
| `:active`       | 当元素被点击（激活）时    | `.item:active { filter: brightness(0.7); }`               |
| `:focus`        | 当元素获得焦点时       | `input:focus { border-color: blue; }`                     |
| `:focus-within` | 当元素或其内部元素获得焦点时 | `.form-group:focus-within { background-color: #f9f9f9; }` |

 2. **结构伪类**
基于HTML文档结构选择元素：

| 伪类 | 描述 | 示例 |
|------|------|------|
| `:first-child` | 选择父元素的第一个子元素 | `ul li:first-child { font-weight: bold; }` |
| `:last-child` | 选择父元素的最后一个子元素 | `ul li:last-child { margin-bottom: 0; }` |
| `:nth-child(n)` | 选择父元素的第n个子元素 | `ul li:nth-child(2) { color: red; }` |
| `:nth-child(odd/even)` | 选择奇数/偶数位置的子元素 | `ul li:nth-child(odd) { background-color: #f5f5f5; }` |
| `:only-child` | 选择父元素的唯一子元素 | `div p:only-child { font-style: italic; }` |
| `:first-of-type` | 选择同类型元素中的第一个 | `div p:first-of-type { font-size: 1.2em; }` |
| `:last-of-type` | 选择同类型元素中的最后一个 | `div p:last-of-type { margin-bottom: 0; }` |

 3. **表单伪类**
用于选择表单元素的特定状态：

| 伪类 | 描述 | 示例 |
|------|------|------|
| `:checked` | 选择被选中的单选按钮或复选框 | `input[type="checkbox"]:checked { transform: scale(1.2); }` |
| `:disabled` | 选择被禁用的元素 | `input:disabled { opacity: 0.5; cursor: not-allowed; }` |
| `:enabled` | 选择启用的元素 | `input:enabled { border-color: #ccc; }` |
| `:required` | 选择必填表单元素 | `input:required { border-left: 3px solid red; }` |
| `:optional` | 选择可选表单元素 | `input:optional { border-left: 3px solid green; }` |
| `:valid` | 选择验证通过的表单元素 | `input:valid { border-color: green; }` |
| `:invalid` | 选择验证失败的表单元素 | `input:invalid { border-color: red; }` |

4. **否定伪类**
用于排除特定元素：

| 伪类 | 描述 | 示例 |
|------|------|------|
| `:not(selector)` | 选择不匹配给定选择器的元素 | `div:not(.special) { color: gray; }` |

5. **链接伪类**
用于选择链接的不同状态：

| 伪类 | 描述 | 示例 |
|------|------|------|
| `:link` | 选择未访问的链接 | `a:link { color: blue; }` |
| `:visited` | 选择已访问的链接 | `a:visited { color: purple; }` |

6. **动态伪类**
用于选择当前处于特定状态的元素：

| 伪类 | 描述 | 示例 |
|------|------|------|
| `:target` | 选择当前锚点指向的元素 | `section:target { background-color: #f0f0f0; }` |
| `:empty` | 选择没有子元素的元素 | `div:empty { display: none; }` |
## aspect-ratio
aspect-ratio 属性允许您为元素定义一个固定的宽高比，使元素在缩放时始终保持这个比例关系。这在响应式设计中特别有用，尤其是对于图片、视频、卡片等元素，确保它们在不同屏幕尺寸下不会变形。
```css
aspect-ratio: <ratio> | auto;
```
- `<ratio>` : 宽高比，格式为 width/height
- 例如： 1/1 （正方形）、 16/9 （宽屏视频）、 4/3 （标准视频）、 3/4 （竖屏）
- 也可以使用小数形式，如 1 等同于 1/1 ， 1.777 等同于 16/9
- auto : 默认值，元素的宽高比由其内容决定
## list-style-position: inside;
这个属性可以让list的前边的序号或者点也在盒子内,(这个元素要放在ul或者ol里,而不是li)
