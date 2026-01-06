## ECharts的介绍
[Apache ECharts](https://echarts.apache.org/zh/index.html)是百度公司开源的一个使用 JavaScript 实现的开源可视化库，兼容性强，底层依赖矢量图形库 ZRender ，提供直观，交互丰富，可**高度个性化定制**的数据可视化图表。
- 开源免费
	它是开源免费的，也就是我们可以免费的使用 ECharts ，不需要缴纳任何的费用
- 功能丰富
	它的功能非常的丰富，提供了各种各样的图表，支持各种各样的定制, 满足各种需求，比如折线图、柱状图、饼图、K线图等. 在他的官方示例中, 提供了上百种图表, 可以用 只有你想不到, 没有她做不到 这句话来形容
- 社区活跃
	ECharts 的社区非常活跃，意味着你可以和很多开发者讨论，遇到了 ECharts 中不会的问题，也很容易找到解决办法
- 多种数据的支持
	可视化的含义就是将数据通过更加直观的图表的方式来呈现。图表只是一种呈现方式。最核心的其实是数据。 ECharts 对数据格式的支持也是非常友好的。 ECharts 能够支持常见的 key-value 数据格式，还能支持二维表，或者 TypedArray 格式的数据
- 流数据的支持
	对于超大的数据量而言， 数据本身的体量可能就非常消耗资源, 而 ECharts 可以支持对流数据的动态渲染，加载多少数据就渲染多少数据，省去了漫长的数据加载的等待时间, 他还提供了增量渲染的技术, 只渲染变化的数据, 提高系统的资源利用.
- 移动端的优化
- 跨平台
- 酷炫的特效,
- 数据的三维可视化
- ......
## 快速上手
### 原始导入
ECharts 的入门使用特别简单, 5分钟就能够上手. 他大体分为这几个步骤
- 步骤1：引入 echarts.js 文件
	echarts是一个 js 的库，当然得先引入这个库文件
```html
<script src="js/echarts.min.js"></script>
```
- 步骤2：准备一个呈现图表的盒子
	这个盒子通常来说就是我们熟悉的 div ，这个 div 决定了图表显示在哪里
```html
<div id="main" style="width: 600px;height:400px;"></div>
```
- 步骤3：初始化 echarts 实例对象
	在这个步骤中, 需要指明图表最终显示在哪里的DOM元素
```js
var myChart = echarts.init(document.getElementById('main'))
```
- 步骤4：准备配置项
	这步很关键，我们最终的效果，到底是显示饼图还是折线图，基本上都是由配置项决定的
```js
var option = {
  xAxis: {
    type: 'category',
    data: ['小明', '小红', '小王'],
  },
  yAxis: {
    type: 'value',
  },
  series: [
    {
      name: '语文',
      type: 'bar',
      data: [70, 92, 87],
    },
  ],
}
```
- 步骤5：将配置项设置给 echarts 实例对象
```js
myChart.setOption(option)
```
通过简单的5个步骤, 就能够把一个简单的柱状图给显示在网页中了.这几个步骤中, 步骤4最重要,
一个图表最终呈现什么样子,完全取决于这个配置项.所以对于不同的图表, 除了**配置项会发生改变**之外,其他的代码 都是**固定不变**的.
### Vue整合
你可以使用如下命令通过 npm 安装 ECharts
```bash
npm install echarts --save
```
之后就可以直接使用了
>推荐封装为组件使用,注意需要在dom渲染之后再去init
```vue
<!-- components/EChartsWrapper.vue -->
<template>
  <div>
    <div style="width: 600px; height: 400px" ref="div"></div>
  </div>
</template>

<script setup lang="ts">
import * as echarts from 'echarts'
import { onMounted, ref } from 'vue'

const div = ref()

onMounted(() => {
  const myChart = echarts.init(div.value)
  myChart.setOption({
    xAxis: {
      type: 'category',
      data: ['小明', '小红', '小王'],
    },
    yAxis: {
      type: 'value',
    },
    series: [
      {
        name: '语文',
        type: 'bar',
        data: [70, 92, 87],
      },
    ],
  })
})
</script>

<style lang="scss" scoped></style>
```
## 通用配置
>配置项都是以键值对的形式存在, 并且配置项有很多, ECharts 的学习大多是针对于这些配置项的, 对于配置项的学习, 大家可以不用死记硬背, 需要的时候查一查官方文档即可.[Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#title)
### title
![[Pasted image 20251122195403.png]]
```js
const option = {
  title: {
    text: '成绩', // 标题文字
    textStyle: {
      color: 'red', // 文字颜色
    },
    borderWidth: 5, // 标题边框
    borderColor: 'green', // 标题边框颜色
    borderRadius: 5, // 标题边框圆角
    left: 20, // 标题的位置
    top: 20, // 标题的位置
  },
}
```
### series
系列列表。每个系列通过 type 决定自己的图表类型, data 来设置每个系列的数据
### tooltip
![[Pasted image 20251122200348.png]]
```js
const option = {
  tooltip: {
    trigger: 'item',
    triggerOn: 'click',
    formatter: '{b}:{c}',
  },
}
```
tooltip 指的是当鼠标移入到图表或者点击图表时, 展示出的提示框(对之前的标记,平均值之类的都有效)
1. 触发范围: trigger,即鼠标在那个范围内可以触发,可选值有item(在对应的图块内)\axis(在对应的类别的轴线上)
2. 触发时机: triggerOn,可选值有 mousemove(鼠标移入)\click(点击)
3. 格式化显示: formatter,可以写一个字面量,一个模版字符串或者一个返回字符串(需要进行html转义)的回调函数,反正就是要接收一个字符串,然后显示的时候就会显示那个字符串
**字符串模板**
模板变量有 `{a}`, `{b}`，`{c}`，`{d}`，`{e}`，分别表示系列名，数据名，数据值等。 在 [trigger](https://echarts.apache.org/zh/option.html#tooltip.trigger) 为 `'axis'` 的时候，会有多个系列的数据，此时可以通过 `{a0}`, `{a1}`, `{a2}` 这种后面加索引的方式表示系列的索引。 不同图表类型下的 `{a}`，`{b}`，`{c}`，`{d}` 含义不一样。 其中变量`{a}`, `{b}`, `{c}`, `{d}`在不同图表类型下代表数据含义为：
- 折线（区域）图、柱状（条形）图、K线图 : `{a}`（系列名称），`{b}`（类目值），`{c}`（数值）, `{d}`（无）
- 散点图（气泡）图 : `{a}`（系列名称），`{b}`（数据名称），`{c}`（数值数组）, `{d}`（无）
- 地图 : `{a}`（系列名称），`{b}`（区域名称），`{c}`（合并数值）, `{d}`（无）
- 饼图、仪表盘、漏斗图: `{a}`（系列名称），`{b}`（数据项名称），`{c}`（数值）, `{d}`（百分比）
>更多其它图表模板变量的含义可以见相应的图表的 label.formatter 配置项。

示例：
```
formatter: '{b0}: {c0}<br />{b1}: {c1}'
```
**回调函数**
```js
const option = {
  tooltip: {
    trigger: 'item',
    triggerOn: 'click',
    formatter: function (arg) {
      return arg.name + ':' + arg.data
    },
  },
}
```
### toolbox
toolbox 是 ECharts 提供的工具栏, 内置有 导出图片，数据视图, 重置, 数据区域缩放, 动态类型切换五个工具
工具栏的按钮是配置在 feature 的节点之下
![[Pasted image 20251122211407.png]]
```js
const option = {
  toolbox: {
    feature: {
      saveAsImage: {}, // 将图表保存为图片
      dataView: {}, // 是否显示出原始数据
      restore: {}, // 还原图表
      dataZoom: {}, // 数据缩放
      magicType: {
        // 将图表在不同类型之间切换,图表的转换需要数据的支持
        type: ['bar', 'line'],
      },
    },
  },
}
```
### legend
legend 是图例,用于筛选类别,可以点击下方的类别来选择是否展示某一个数据,需要和 series 配合使用
- legend 中的 data 是一个数组
- legend 中的 data 的值需要和 series 数组中某组数据的 name 值一致
![[成绩.png]]
```js
const option = {
  legend: {
    data: ['语文', '数学'],
  },
  xAxis: {
    type: 'category',
    data: ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强'],
  },
  yAxis: {
    type: 'value',
  },
  series: [
    {
      name: '语文',
      type: 'bar',
      data: [88, 92, 63, 77, 94, 80, 72, 86],
    },
    {
      name: '数学',
      type: 'bar',
      data: [93, 60, 61, 82, 95, 70, 71, 86],
    },
  ],
}
```
## 直角坐标系配置
直角坐标系的图表指的是带有x轴和y轴的图表, 常见的直角坐标系的图表有: 柱状图 折线图 散点图
针对于直角坐标系的图表, 有一些通用的配置
### 网格 grid
grid是用来控制直角坐标系的布局和大小, x轴和y轴就是在grid的基础上进行绘制的
- 显示 grid
	show: true
- grid 的边框
	borderWidth : 10
- grid 的位置和大小
	left top right bottom
	width height
```js
const option = {
  grid: {
    show: true, // 显示grid
    borderWidth: 10, // grid的边框宽度
    borderColor: 'red', // grid的边框颜色
    left: 100, // grid的位置
    top: 100,
    width: 300, // grid的大小
    height: 150,
  },
}
```
![[Pasted image 20251123175725.png]]
>就是外边那个红框,有点类似盒子模型的border和margin的合体,show不显示的话就类似margin使用
### 坐标轴 axis
坐标轴分为x轴和y轴, 一个 grid 中最多有两种位置的 x 轴和 y 轴
- 坐标轴类型 type
	value : 数值轴, 自动会从目标数据中读取数据
	category : 类目轴, 该类型必须通过 data 设置类目数据
- 坐标轴位置
	xAxis : 可取值为 top 或者 bottom
	yAxis : 可取值为 left 或者 right
```js
const option = {
  xAxis: {
    type: 'category',
    data: xDataArr,
    position: 'top',
  },
  yAxis: {
    type: 'value',
    position: 'right',
  },
}
```
### 区域缩放 dataZoom
>实际效果类似限制数据展示的范围,只有在选中范围内的数据会被显示并参与计算,如最值或平均值

![[Pasted image 20251123180344.png]]
dataZoom 用于区域缩放, 对数据范围过滤, x轴和y轴都可以拥有, dataZoom 是一个数组, 意味着可以配置多个区域缩放器
- 区域缩放类型 type
	slider : 滑块
	inside : 内置, 依靠鼠标滚轮或者双指缩放
- 产生作用的轴
	xAxisIndex :设置缩放组件控制的是哪个 x 轴, 一般写0即可
	yAxisIndex :设置缩放组件控制的是哪个 y 轴, 一般写0即可
- 指明初始状态的缩放情况
	start : 数据窗口范围的起始百分比
	end : 数据窗口范围的结束百分比
```js
const option = {
  xAxis: {
    type: 'category',
    data: xDataArr,
  },
  yAxis: {
    type: 'value',
  },
  dataZoom: [
    {
      type: 'slider',
      xAxisIndex: 0,
    },
    {
      type: 'slider',
      yAxisIndex: 0,
      start: 0,
      end: 80,
    },
  ],
}
```
## 常用图表
### 柱状图--bar
柱状图描述的是分类数据，呈现的是每一个分类中『有多少？』, 图表所表达出来的含义在于不同类别数据的排名\对比情况
```vue
<template>
  <div>
    <div style="width: 600px; height: 400px" ref="div"></div>
  </div>
</template>

<script setup lang="ts">
import { init } from 'echarts'
import { onMounted, ref } from 'vue'

const div = ref()

onMounted(() => {
  const myChart = init(div.value)
  myChart.setOption({
    xAxis: {
      type: 'category',
      data: ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强'],
    },
    yAxis: {
      type: 'value',
    },
    series: [
      {
        name: '语文',
        type: 'bar',
        data: [88, 92, 63, 77, 94, 80, 72, 86],
      },
    ],
  })
})
</script>

<style lang="scss" scoped></style>
```
1. 设置x轴类型为"类别",此时需要配置data说明有那些类别
2. 设置y轴类型为"值",此时会从series中提供的数据获取具体的值无需配置data
3. 设置series,其中每一个类都是一系列数据,name为系列名,可以为不同系列自动分配不同颜色(不写也会分配不同的颜色,但是如果name一致则颜色也一致),type为图表类型,data为数据
#### 标记
**最大值\最小值 markPoint**
![[Pasted image 20251122194002.png]]
```js
  myChart.setOption({
    xAxis: {
      type: 'category',
      data: ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强'],
    },
    yAxis: {
      type: 'value',
    },
    series: [
      {
        name: '语文',
        type: 'bar',
        data: [88, 92, 63, 77, 94, 80, 72, 86],
        markPoint: {
          data: [
            {
              type: 'max',
              name: '最大值',
            },
            {
              type: 'min',
              name: '最小值',
            },
          ],
        },
      },
    ],
  })
```
**平均值 markLine**
![[Pasted image 20251122194249.png]]
```js
    series: [
      {
        name: '语文',
        type: 'bar',
        data: [88, 92, 63, 77, 94, 80, 72, 86],
        markPoint: {
          data: [
            {
              type: 'max',
              name: '最大值',
            },
            {
              type: 'min',
              name: '最小值',
            },
          ],
        },
        markLine: {
          data: [
            {
              type: 'average',
              name: '平均值',
            },
          ],
        },
      },
    ],
```
#### 显示
**数值显示 label**
![[Pasted image 20251122194541.png]]
```js
    series: [
      {
         ......
        label: {
          show: true, // 是否可见
          rotate: 60, // 旋转角度
          position: 'inside', // 标签的位置
        },
      },
    ],
```
>标签位置默认为inside,其他的还有top等等

**柱宽度 barWidth**
![[Pasted image 20251122195012.png]]
```js
    series: [
      {
        // ......
        barWidth: '30%', // 柱的宽度
      },
    ],
```
### 折线图
![[Pasted image 20251122212108.png]]
只需要把series的type改为'line'即可
#### 标记
**最大值\最小值 markPoint**
![[Pasted image 20251122212438.png]]
```js
const option = {
  series: [
    {
      // ......
      markPoint: {
        data: [
          {
            type: 'max',
            name: '最大值',
          },
          {
            type: 'min',
            name: '最小值',
          },
        ],
      },
    },
  ],
}
```
**平均值 markLine**
![[Pasted image 20251122212605.png]]
```js
const option = {
  series: [
    {
      // ......
      markLine: {
        data: [
          {
            type: 'average',
            name: '平均值',
          },
        ],
      },
    },
  ],
}
```
**标注区间 markArea**
![[Pasted image 20251122212920.png]]
```js
const option = {
  series: [
    {
      // ......
      markArea: {
        data: [
          [
            {
              xAxis: '1月',
            },
            {
              xAxis: '2月',
            },
          ],
          [
            {
              xAxis: '6月',
            },
            {
              xAxis: '8月',
            },
          ],
        ],
      },
    },
  ],
}
```
#### 线条控制
**平滑线条 smooth**
![[Pasted image 20251122213029.png]]
```js
const option = {
  series: [
    {
      // ......
      smooth: true,
    },
  ],
}
```
**线条样式 lineStyle**
![[Pasted image 20251122213146.png]]
```js
const option = {
  series: [
    {
      // ......
      smooth: true,
      lineStyle: {
        color: 'green',
        type: 'dashed', // 可选值还有 dotted solid
      },
    },
  ],
}
```
#### 填充风格 areaStyle
![[Pasted image 20251122213258.png]]
```js
const option = {
  series: [
    {
      type: 'line',
      data: yDataArr,
      areaStyle: {
        color: 'pink',
      },
    },
  ],
}
```
#### 紧挨边缘 boundaryGap
>boundaryGap 是设置给 x 轴的, 让起点从 x 轴的0坐标开始

![[Pasted image 20251122213501.png]]
```js
const option = {
  xAxis: {
    type: 'category',
    data: xDataArr,
    boundaryGap: false,
  },
}
```
#### 缩放, 脱离0值比例
如果每一组数据之间相差较少, 且都比0大很多, 那么有可能会出现这种情况
![[Pasted image 20251122213609.png]]
因此可以配置上 scale , 让其摆脱0值比例
**scale 配置**
>scale 应该配置给 y 轴
![[Pasted image 20251122213734.png]]
```js
const option = {
  yAxis: {
    type: 'value',
    scale: true,
  },
}
```
#### 堆叠图
堆叠图指的是, 同个类目轴上系列配置相同的 stack 值后，**后一个系列的值会在前一个系列的值上相加**
如果在一个图表中有两个或者多个折线图, 在没有使用堆叠配置的时候, 效果如下:
![[Pasted image 20251122214217.png]]
使用了堆叠图之后:
![[Pasted image 20251122214433.png]]
```js
const option = {
  series: [
    {
      type: 'line',
      data: yDataArr1,
      stack: 'all', // series中的每一个对象配置相同的stack值, 这个all可以任意写
    },
    {
      type: 'line',
      data: yDataArr2,
      stack: 'all', // series中的每一个对象配置相同的stack值, 这个all可以任意写
    },
  ],
}
```
绿色这条线的y轴起点, 不再是y轴, 而是蓝色这条线对应的点. 所以相当于绿色是在蓝色这条线的基础之上进行绘制. 基于前一个图表进行堆叠
### 散点图
![[Pasted image 20251123174014.png]]
```js
const data = [
  { gender: 'female', height: 161.2, weight: 51.6 },
  {
    gender: 'female',
    height: 167.5,
    weight: 59,
  },
  { gender: 'female', height: 159.5, weight: 49.2 },
  { gender: 'female', height: 157, weight: 63 },
  { gender: 'female', height: 155.8, weight: 53.6 },
  {
    gender: 'female',
    height: 170,
    weight: 59,
  },
  { gender: 'female', height: 159.1, weight: 47.6 },
  { gender: 'female', height: 166, weight: 69.8 },
  { gender: 'female', height: 176.2, weight: 66.8 },
  {
    gender: 'female',
    height: 160.2,
    weight: 75.2,
  },
  { gender: 'female', height: 172.5, weight: 55.2 },
  { gender: 'female', height: 170.9, weight: 54.2 },
  { gender: 'female', height: 172.9, weight: 62.5 },
  {
    gender: 'female',
    height: 153.4,
    weight: 42,
  },
  { gender: 'female', height: 160, weight: 50 },
  { gender: 'female', height: 147.2, weight: 49.8 },
]

const axisData = []
for (let i = 0; i < data.length; i++) {
  const height = data[i].height
  const weight = data[i].weight
  const itemArr = [height, weight]
  axisData.push(itemArr)
}

onMounted(() => {
  const myChart = init(div.value)
  myChart.setOption({
    xAxis: {
      type: 'value',
    },
    yAxis: {
      type: 'value',
    },
    series: [
      {
        name: '语文',
        type: 'scatter',
        data: axisData,
      },
    ],
  })
})
```
散点图的x轴和y轴的类型都为值,并且数据要求为一个二维数组,其中每一个元素代表一个点(即`[[x1,y1],[x2,y2]...]`)
#### 气泡图效果
要能够达到气泡图的效果, 其实就是让每一个散点的大小不同, 让每一个散点的颜色不同
- symbolSize 控制散点的大小
- itemStyle.color 控制散点的颜色
这两个配置项都支持固定值的写法, 也支持回调函数的写法
固定值的写法如下
```js
const option = {
  series: [
    {
      type: 'scatter',
      data: axisData,
      symbolSize: 25,
      itemStyle: {
        color: 'green',
      },
    },
  ],
}
```
![[Pasted image 20251123174613.png]]
回调函数的写法如下
```js
const option = {
  series: [
    {
      type: 'scatter',
      data: axisData,
      symbolSize: function (arg) {
        const weight = arg[1]
        const height = arg[0] / 100
        // BMI > 28 则代表肥胖, 肥胖的人用大的散点标识, 正常的人用小散点标识
        // BMI: 体重/ 身高*身高 kg m
        const bmi = weight / (height * height)
        if (bmi > 28) {
          return 20
        }
        return 5
      },
      itemStyle: {
        color: function (arg) {
          const weight = arg.data[1]
          const height = arg.data[0] / 100
          const bmi = weight / (height * height)
          if (bmi > 28) {
            return 'red'
          }
          return 'green'
        },
      },
    },
  ],
}
```
![[Pasted image 20251123174731.png]]
#### 涟漪动画效果
![[Pasted image 20251123174922.png]]
>图中的每一个点都有涟漪动画
- type:effectScatter
	将 type 的值从 scatter 设置为 effectScatter 就能够产生涟漪动画的效果
- rippleEffect
	rippleEffect 可以配置涟漪动画的大小
- showEffectOn
	showEffectOn 可以控制涟漪动画在什么时候产生, 它的可选值有两个: render 和 emphasis
	- render 代表界面渲染完成就开始涟漪动画
	- emphasis 代表鼠标移过某个散点的时候, 该散点开始涟漪动画
```js
const option = {
  series: [
    {
      type: 'effectScatter',
      showEffectOn: 'emphasis',
      rippleEffect: {
        scale: 3,
      },
    },
  ],
}
```
### 饼图
![[Pasted image 20251123180838.png]]
```vue
<template>
  <div>
    <div style="width: 600px; height: 400px" ref="div"></div>
  </div>
</template>

<script setup lang="ts">
import { init } from 'echarts'
import { onMounted, ref } from 'vue'

const div = ref()

const pieData = [
  {
    value: 11231,
    name: '淘宝',
  },
  {
    value: 22673,
    name: '京东',
  },
  {
    value: 6123,
    name: '唯品会',
  },
  {
    value: 8989,
    name: '1号店',
  },
  {
    value: 6700,
    name: '聚美优品',
  },
]

onMounted(() => {
  const myChart = init(div.value)
  myChart.setOption({
    series: [
      {
        type: 'pie',
        data: pieData,
      },
    ],
  })
})
</script>

<style lang="scss" scoped></style>
```
>饼图的数据是由 name 和 value 组成的字典所形成的数组
>饼图无须配置 xAxis 和 yAxis
#### 显示数值
![[Pasted image 20251123182107.png]]
- label.show : 显示文字
- label.formatter : 格式化文字
```JS
const option = {
  series: [
    {
      type: 'pie',
      data: pieData,
      label: {
        show: true,
        formatter: function (arg) {
          return arg.data.name + '平台' + arg.data.value + '元\n' + arg.percent + '%'
        },
      },
    },
  ],
}
```
#### 南丁格尔图
>南丁格尔图指的是每一个扇形的半径随着数据的大小而不同, 数值占比越大, 扇形的半径也就越大
![[Pasted image 20251123182235.png]]
- roseType:'radius
```js
const option = {
  series: [
    {
      type: 'pie',
      data: pieData,
      label: {
        show: true,
        formatter: function (arg) {
          return arg.data.name + '平台' + arg.data.value + '元\n' + arg.percent + '%'
        },
      },
      roseType: 'radius',
    },
  ],
}
```
#### 选中效果
![[Pasted image 20251123182759.png]]
- selectedMode: 'multiple'
	选中模式，表示是否支持多个选中，默认关闭，支持布尔值和字符串，字符串取值可选 'single' ， 'multiple' ，分别表示单选还是多选
- selectedOffset: 30
	选中扇区的偏移距离
```js
const option = {
  series: [
    {
      type: 'pie',
      data: pieData,
      selectedMode: 'multiple',
      selectedOffset: 30,
    },
  ],
}
```
#### 圆环
![[Pasted image 20251123183110.png]]
- radius
	饼图的半径。可以为如下类型：
	- number ：直接指定外半径值。 
	- string ：例如， '20%' ，表示外半径为可视区尺寸（容器高宽中较小一项）的 20% 长度。
	- Array：数组的第一项是内半径，第二项是外半径, 通过 Array , 可以将饼图设置为**圆环图**
```js
const option = {
  series: [
    {
      type: 'pie',
      data: pieData,
      radius: ['50%', '70%'],
    },
  ],
}
```
### 地图
地图图表的使用方式
- 百度地图API : 使用百度地图的 api , 它能够在线联网展示地图, 百度地图需要申请 ak
- 矢量地图 : 可以离线展示地图, 需要开发者准备矢量地图数据(本地)
#### 矢量地图
![[Pasted image 20251123212902.png]]
```vue
<template>
  <div>
    <div style="width: 600px; height: 400px" ref="div"></div>
  </div>
</template>

<script setup lang="ts">
import { init, registerMap } from 'echarts'
import { onMounted, ref } from 'vue'
import chinaJson from '@/assets/china.json'
import type { GeoJSONSourceInput } from 'echarts/types/src/coord/geo/geoTypes.js'

const div = ref(null)
onMounted(() => {
  const myChart = init(div.value)
  registerMap('china',chinaJson as GeoJSONSourceInput)
  const option = {
    geo: {
      type: 'map', // map是一个固定的值
      map: 'china', //chinaMap需要和registerMap中的第一个参数保持一致
    },
  }
  myChart.setOption(option)
})
</script>

<style lang="scss" scoped></style>
```
在线获取[DataV.GeoAtlas地理小工具系列](https://datav.aliyun.com/portal/school/atlas/area_selector)
```ts
onMounted(() => {
  const myChart = init(div.value)
  axios.get('https://geo.datav.aliyun.com/areas_v3/bound/100000_full.json').then((res) => {
    registerMap('china', res.data)
    const option = {
      geo: {
        type: 'map', // map是一个固定的值
        map: 'china', //chinaMap需要和registerMap中的第一个参数保持一致
      },
    }
    myChart.setOption(option)
  })
})
```
#### 缩放拖动: roam
![[Pasted image 20251123213350.png]]
```ts
const option = {
  geo: {
    type: 'map', // map是一个固定的值
    map: 'chinaMap', //chinaMap需要和registerMap中的第一个参数保持一致,
    roam: true, // 运行使用鼠标进行拖动和缩放
  },
}
```
#### 名称显示: label
![[Pasted image 20251123213537.png]]
```ts
const option = {
  geo: {
    type: 'map', // map是一个固定的值
    map: 'chinaMap', //chinaMap需要和registerMap中的第一个参数保持一致,
    roam: true,
    label: {
      show: true,
    },
  },
}
```
#### 初始缩放比例: zoom 地图中心点: center
![[Pasted image 20251123213700.png]]
```ts
const option = {
  geo: {
    type: 'map', // map是一个固定的值
    map: 'chinaMap', //chinaMap需要和registerMap中的第一个参数保持一致,
    roam: true,
    label: {
      show: true,
    },
    zoom: 0.8, // 地图的缩放比例, 大于1代表放大, 小于1代表缩小
    center: [87.617733, 43.792818], // 当前视角的中心点，用经纬度表示
  },
}
```
#### 不同城市颜色不同
![[Pasted image 20251123214456.png]]
```ts
const airData = [
  { name: '北京', value: 39.92 },
  { name: '天津', value: 39.13 },
  { name: '上海', value: 31.22 },
  { name: '重庆', value: 66 },
  { name: '河北', value: 147 },
  { name: '河南', value: 113 },
  { name: '云南', value: 25.04 },
  { name: '辽宁', value: 50 },
  { name: '黑龙江', value: 114 },
  { name: '湖南', value: 175 },
  { name: '安徽', value: 117 },
  { name: '山东', value: 92 },
  { name: '新疆', value: 84 },
  { name: '江苏', value: 67 },
  { name: '浙江', value: 84 },
  { name: '江西', value: 96 },
  { name: '湖北', value: 273 },
  { name: '广西', value: 59 },
  { name: '甘肃', value: 99 },
  { name: '山西', value: 39 },
  { name: '内蒙古', value: 58 },
  { name: '陕西', value: 61 },
  { name: '吉林', value: 51 },
  { name: '福建', value: 29 },
  { name: '贵州', value: 71 },
  { name: '广东', value: 38 },
  { name: '青海', value: 57 },
  { name: '西藏', value: 24 },
  { name: '四川', value: 58 },
  { name: '宁夏', value: 52 },
  { name: '海南', value: 54 },
  { name: '台湾', value: 88 },
  { name: '香港', value: 66 },
  { name: '澳门', value: 77 },
  { name: '南海诸岛', value: 55 },
]
const option = {
  geo: {
    type: 'map',
    map: 'chinaMap',
    roam: true,
    label: {
      show: true,
    },
  },
  series: [
    {
      data: airData,
      geoIndex: 0,
      type: 'map',
    },
  ],
  visualMap: {
    min: 0, // 最小值
    max: 300, // 最大值
    inRange: {
      color: ['white', 'red'], // 颜色的范围
    },
    calculable: true, // 是否显示拖拽用的手柄（手柄能拖拽调整选中范围）
  },
}
```
1. 首先使用series为地图配置数据
2. 将 series 下的数据和 geo 关联起来(geoIndex: 0,type: 'map',)
3. 结合 visualMap 配合使用
	visualMap 是视觉映射组件, 和之前区域缩放 dataZoom 很类似, 可以做数据的过滤. 只不过dataZoom 主要使用在直角坐标系的图表, 而 visualMap 主要使用在地图或者饼图中
>注意数据中的name要和地图json中的省份name完全一致,北京匹配不到北京市
#### 地图和散点图结合
1. 给 series 这个数组下增加新的对象
2. 准备好散点数据,设置给新对象的 data
```ts
const option = {
  series: [
    {
      data: airData,
      geoIndex: 0,
      type: 'map',
    },
    {
      data: [
        {
          value: [117.283042, 31.86119], // 散点的坐标, 使用的是经纬度
        },
      ],
      type: 'effectScatter',
      coordinateSystem: 'geo',
      rippleEffect: {
        scale: 10,
      },
    },
  ],
}
```
### 雷达图
![[Pasted image 20251123215654.png]]
```vue
<template>
  <div>
    <div style="width: 600px; height: 400px" ref="div"></div>
  </div>
</template>

<script setup lang="ts">
import { init } from 'echarts'
import { onMounted, ref } from 'vue'

const div = ref()

const dataMax = [
  {
    name: '易用性',
    max: 100,
  },
  {
    name: '功能',
    max: 100,
  },
  {
    name: '拍照',
    max: 100,
  },
  {
    name: '跑分',
    max: 100,
  },
  {
    name: '续航',
    max: 100,
  },
]
const hwScore = [80, 90, 80, 82, 90]
const zxScore = [70, 82, 75, 70, 78]
onMounted(() => {
  const myChart = init(div.value)
  const option = {
    radar: {
      indicator: dataMax, // 各个维度的最大值
    },
    series: [
      {
        type: 'radar',
        data: [
          {
            name: '华为手机1',
            value: hwScore,
          },
          {
            name: '中兴手机1',
            value: zxScore,
          },
        ],
      },
    ],
  }
  myChart.setOption(option)
})
</script>

<style lang="scss" scoped></style>
```
#### 显示数值 label
![[Pasted image 20251123215910.png]]
```ts
const option = {
  series: [
    {
      type: 'radar',
      label: {
        show: true,
      },
      data: [
        {
          name: '华为手机1',
          value: hwScore,
        },
        {
          name: '中兴手机1',
          value: zxScore,
        },
      ],
    },
  ],
}
```
#### 区域面积 areaStyle
![[Pasted image 20251123220159.png]]
```ts
const option = {
  series: [
    {
      type: 'radar',
      label: {
        show: true,
      },
      areaStyle: {},
      data: [
        {
          name: '华为手机1',
          value: hwScore,
        },
        {
          name: '中兴手机1',
          value: zxScore,
        },
      ],
    },
  ],
}
```
#### 绘制类型 shape
雷达图绘制类型，支持 'polygon' 和 'circle'
- 'polygon' : 多边形
- 'circle' 圆形
![[Pasted image 20251123224342.png]]
```ts
const option = {
  radar: {
    indicator: dataMax,
    shape: 'circle',
  },
  series: [
    {
      type: 'radar',
      label: {
        show: true,
      },
      data: [
        {
          name: '华为手机1',
          value: hwScore,
        },
        {
          name: '中兴手机1',
          value: zxScore,
        },
      ],
    },
  ],
}
```
### 仪表盘图
![[Pasted image 20251123224533.png]]
```vue
<template>
  <div>
    <div style="width: 600px; height: 400px" ref="div"></div>
  </div>
</template>

<script setup lang="ts">
import { init } from 'echarts'
import { onMounted, ref } from 'vue'

const div = ref()
onMounted(() => {
  const myChart = init(div.value)
  const option = {
    series: [
      {
        type: 'gauge',
        data: [
          {
            value: 97,
          },
        ],
      },
    ],
  }
  myChart.setOption(option)
})
</script>

<style lang="scss" scoped></style>
```
#### 常见效果
![[Pasted image 20251123224848.png]]
- 数值范围: max min
- 多个指针: 增加data中数组的元素
- 多个指针颜色的差异: itemStyle
```ts
const option = {
  series: [
    {
      type: 'gauge',
      data: [
        {
          value: 97,
          itemStyle: {
            color: 'pink',
          },
        },
        {
          value: 85,
          itemStyle: {
            color: 'green',
          },
        },
      ],
      min: 50,
    },
  ],
}
```
![[Pasted image 20251123230537.png]]
# ECharts高级
## 显示相关
### 主题
- 默认主题
	ECharts 中默认内置了两套主题: light(默认) dark
	在初始化对象方法 init 中可以指明
```js
var chart = echarts.init(dom, 'light')
var chart = echarts.init(dom, 'dark')
```
>暗色模式(感觉怎么就只是把div背景改成黑的了)
![[Pasted image 20251124185313.png]]
### 自定义主题
1. 在主题编辑器中编辑主题
	主题编辑器的地址为: [主题编辑器 - Apache ECharts](https://echarts.apache.org/zh/theme-builder.html)
	在该地址中, 你可以定义一个主题的很多方面的内容:
	![[Pasted image 20251124185613.png]]
2. 下载主题, 可以选择json格式和js格式
	在线编辑完主题之后, 可以点击下载主题按钮, 下载主题的js文件
#### json使用方法
1. 将下载的 `essos.json` 文件保存到项目中
2. 读取 JSON 文件并解析：  
    `import essos from '@/assets/essos.json'`
3. 注册主题：  
    `echarts.registerTheme('essos', essos)`
4. 创建图表时使用主题：  
    `echarts.init(dom, 'essos')`
![[Pasted image 20251124191147.png]]
### 调色盘
>它是一组颜色，图形、系列会自动从其中选择颜色, 不断的循环从头取到尾, 再从头取到尾, 如此往复.
- 主题调色盘
```js
echarts.registerTheme('itcast', {
  color: ['#893448', '#d95850', '#eb8146', '#ffb248', '#f2d643', '#ebdba4'],
  backgroundColor: 'rgba(242,234,191,0.15)',
  // ......
})
```
![[Pasted image 20251124192651.png]]
- 全局调色盘(相对于所有系列数据的全局)
	全局调色盘是在 option 下增加一个 color 的数组,优先级大于当前的主题调色盘
```ts
const option = {
  // 全局调色盘
  color: ['red', 'green', 'blue'],
  // ......
}
mCharts.setOption(option)
```
![[Pasted image 20251124192304.png]]
- 局部调色盘
	局部调色盘就是在 series 下增加一个 color 的数组,对当前系列数据生效
```ts
const option = {
  // 全局调色盘
  color: ['red', 'green', 'blue'],
  series: [
    {
      type: 'pie',
      data: pieData,
      // 局部调色盘
      color: ['pink', 'yellow', 'black'],
    },
  ],
}
mCharts.setOption(option)
```
![[Pasted image 20251124193111.png]]
>需要注意一点的是, 如果全局的调色盘和局部的调色盘都设置了, 局部调色盘会产生效果, 这里面遵循的是就近原则
### 渐变颜色的实现
在 ECharts 中, 支持线性渐变和径向渐变两种颜色渐变的方式
- 线性渐变
	线性渐变的类型为 linear , 他需要配置线性的方向, 通过 x, y, x2, y2 即可进行配置
	x , y , x2 , y2 , 范围从 0 - 1，相当于在图形包围盒中的百分比，如果 global 为 true ，则该四个值是绝对的像素位置
	在下述代码中的 0 0 0 1 意味着从上往下进行渐变(垂直向上)
```ts
const option = {
  xAxis: {
    type: 'category',
    data: xDataArr,
  },
  yAxis: {
    type: 'value',
  },
  series: [
    {
      type: 'bar',
      data: yDataArr,
      itemStyle: {
        color: {
          type: 'linear',
          x: 0,
          y: 0,
          x2: 0,
          y2: 1,
          colorStops: [
            {
              offset: 0,
              color: 'red', // 0% 处的颜色
            },
            {
              offset: 1,
              color: 'blue', // 100% 处的颜色
            },
          ],
          globalCoord: false, // 缺省为 false
        },
      },
    },
  ],
}
```
![[Pasted image 20251124193544.png]]
- 径向渐变
	线性渐变的类型为 radial , 他需要配置径向的方向, 通过 x , y , r 即可进行配置,r是相对于宽度的倍数
	前三个参数分别是圆心 x , y 和半径，取值同线性渐变
	在下述代码中的 0.5 0.5 0.5 意味着从柱的中点, 向外径向扩散半径为宽度一半的圆
```js
series: [
  {
    itemStyle: {
      color: {
        type: 'radial',
        x: 0.5,
        y: 0.5,
        r: 0.5,
        colorStops: [
          {
            offset: 0,
            color: 'red', // 0% 处的颜色
          },
          {
            offset: 1,
            color: 'blue', // 100% 处的颜色
          },
        ],
        global: false, // 缺省为 false
      },
    },
  },
]
```
![[Pasted image 20251124193857.png]]
### 样式
- 直接样式
	这些样式一般都可以设置颜色或者背景或者字体等样式, 他们会**覆盖主题中的样式**
	- itemStyle
	- textStyle
	- lineStyle
	- areaStyle
	- label
```js
data: [
  {
    value: 11231,
    name: '淘宝',
    itemStyle: {
      color: 'black',
    },
  },
]
title: {
  text: '我是标题'
  textStyle: {
    color: 'red'
  }
}
label: {
  color: 'green'
}
```
- 高亮样式
图表中, 其实有很多元素都是有两种状态的, 一种是默认状态, 另外一种就是鼠标滑过或者点击形成的高亮状态. 而高亮样式是针对于元素的高亮状态设定的样式
那它的使用也非常简单,在 emphasis 中包裹原先的 itemStyle 等等就可以了,类似css的hover:
```js
series: [
  {
    type: 'pie',
    label: {
      color: 'green',
    },
    emphasis: {
      label: {
        color: 'yellow',
      },
    },
    data: [
      {
        value: 11231,
        name: '淘宝',
        itemStyle: {
          color: 'black',
        },
        emphasis: {
          itemStyle: {
            color: 'blue',
          },
        },
      },
    ],
  },
]
```
![[Pasted image 20251124203944.png]]
>柱状图上的数值标记在鼠标移入后变为了黄色
### 自适应
步骤1: 监听窗口大小变化事件
步骤2: 在事件处理函数中调用 ECharts 实例对象的 resize 即可
>其实就是ECharts的图标默认不会跟着包裹其的容器的大小变化而变化,需要在容器大小变化时手动调用重绘才会更改大小
```js
// 监听window大小变化的事件
window.onresize = function () {
  // 调用echarts示例对象的resize方法
  myChart.resize()
}
// window.onresize = mCharts.resize
```
## 动画的使用
### 加载动画
ECharts 已经内置好了加载数据的动画, 我们只需要在合适的时机显示或者隐藏即可显示加载动画
>实际用起来类似el的加载动画,显示时无法操作后边的图表
- 显示加载动画
	`mCharts.showLoading()`
![[Pasted image 20251124210120.png]]
- 隐藏加载动画
	`mCharts.hideLoading()`
### 增量动画
所有数据的更新都通过 setOption 实现, 我们不用考虑数据到底产生了那些变化, ECharts 会找到两组数据之间的差异然后通过合适的动画去表现数据的变化。
>配置新option时只需要配置更新的部分即可,但是需要能精确匹配到你要修改的配置项(如series中的数据,如果不写name就会默认匹配第一个数据列,写上name就会匹配到对应的数据项)
```js
const btn = document.querySelector('button')
btn.onclick = function () {
  const newArr = [68, 62, 93, 67, 64, 90, 52, 36]
  // setOption的方法可以被调用多次
  // 新的option 和旧的option配置
  // 新旧option配置项他们之间不是替换的关系,是相互整合的关系
  // 我们在设置新的option的时候,只需要考虑到将变化的配置项配置就可以了
  const option = {
    series: [
      {
        data: newArr,
      },
    ],
  }
  mCharts.setOption(option)
}
const btnAdd = document.querySelector('#btnAdd')
btnAdd.onclick = function () {
  setInterval(function () {
    //增加数据
    xDataArr.push('小明')
    yDataArr.push(parseInt(50 + Math.random() * 10))
    const option = {
      xAxis: {
        data: xDataArr,
      },
      series: [
        {
          data: yDataArr,
        },
      ],
    }
    mCharts.setOption(option)
  }, 1000)
}
```
![[Pasted image 20251124211125.png]]
### 动画的配置
- 开启动画
	animation: true
- 动画时长
	animationDuration: 5000
- 缓动动画
	animationEasing : 'bounceOut'
	linear ,线性变化, 这样动画效果会很均匀
	bounceOut ,这样动画效果会有一个回弹效果
	缓动动画的可选值如下图:
	![[Pasted image 20251124211643.png]]
- 动画阈值
	animationThreshold: 8
	单种形式的元素数量大于这个阈值时会关闭动画
## 交互API
### 全局echarts对象
全局 echarts 对象是引入 echarts.js 文件之后就可以直接使用的
- echarts.init
	初始化ECharts实例对象
	使用主题
- echarts.registerTheme
	注册主题
	只有注册过的主题,才能在init方法中使用该主题
- echarts.registerMap
	注册地图数据
```js
$.get('json/map/china.json', function (chinaJson) {
	echarts.registerMap('china', chinaJson);
});
```
	geo组件使用地图数据
```js
var option = {
	geo: {
		type: 'map',
		map: 'china',
	},
})
```
- echarts.connect
	一个页面中可以有多个独立的图表
	每一个图表对应一个 ECharts 实例对象
	connect 可以实现**多图关联**，传入联动目标为 EChart 实例，支持数组
```vue
<template>
  <div>
    <div style="width: 600px; height: 400px" ref="div"></div>
    <div style="width: 600px; height: 400px" ref="map"></div>
  </div>
</template>

<script setup lang="ts">
import * as echarts from 'echarts'
import { onMounted, ref } from 'vue'
import chinaJson from '@/assets/china.json'
import type { GeoJSONSourceInput } from 'echarts/types/src/coord/geo/geoTypes.js'

const xDataArr = ['张三', '李四', '王五', '闰土', '小明', '茅台', '二妞', '大强']
const yDataArr = [88, 92, 63, 77, 94, 80, 72, 86]

const div = ref()
const map = ref()

onMounted(() => {
  const mCharts = echarts.init(div.value, 'itcast')
  const option = {
    xAxis: {
      type: 'category',
      data: xDataArr,
    },
    toolbox: {
      feature: {
        saveAsImage: {},
      },
    },
    yAxis: {
      type: 'value',
    },
    series: [
      {
        type: 'bar',
        data: yDataArr,
      },
    ],
  }
  mCharts.setOption(option)
  echarts.registerMap('china', chinaJson as GeoJSONSourceInput)
  const mCharts2 = echarts.init(map.value)
  const option2 = {
    geo: {
      type: 'map',
      map: 'china',
    },
  }
  mCharts2.setOption(option2)
  echarts.connect([mCharts, mCharts2])
})
</script>

<style lang="scss" scoped></style>
```
![[Pasted image 20251124213113.png]]
这样, 由于我们打开了toolbox中的saveAsImage, 所以会出现下载图片的按钮. `而通过echarts.connect([mCharts, mCharts2])` , 此时点击下载图片按钮, 保存下来的图片就是两个图表的图片了.
### echartsInstance 对象
eChartsInstance 对象是通过 echarts.init 方法调用之后得到的
- echartsInstance.setOption
	设置或修改图表实例的配置项以及数据
	多次调用setOption方法
	合并新的配置和旧的配置
	增量动画
- echartsInstance.resize
	重新计算和绘制图表
	一般和window对象的resize事件结合使用
```js
window.onresize = function(){
	myChart.resize();
}
```
- echartsInstance.on / echartsInstance.off
	- 鼠标事件
		绑定或者解绑事件处理函数
		常见事件: 'click'、'dblclick'、'mousedown'、'mousemove'、'mouseup' 等
		事件参数 arg: 和事件相关的数据信息
```js
mCharts.on('click', function (arg) {
	// console.log(arg)
	console.log('饼图被点击了')
})
```
		解绑事件:
```js
mCharts.off('click')
```
	- ECharts 事件
		常见事件:legendselectchanged、'datazoom'、'pieselectchanged'、'mapselectchanged'等
		事件参数 arg: 和事件相关的数据信息
```js
mCharts.on('legendselectchanged', function (arg) {
console.log(arg)
console.log('图例选择发生了改变...')
})
```
- echartsInstance.dispatchAction
	主动触发某些行为, 使用代码模拟用户的行为
```js
// 触发高亮的行为
mCharts.dispatchAction({
	type: "highlight",
	seriesIndex: 0,
	dataIndex: 1
})
// 触发显示提示框的行为
mCharts.dispatchAction({
	type: "showTip",
	seriesIndex: 0,
	dataIndex: 3
})
```
- echartsInstance.clear
	清空当前实例，会移除实例中所有的组件和图表
	清空之后可以再次 setOption
- echartsInstance.dispose
	销毁实例
	销毁后实例无法再被使用