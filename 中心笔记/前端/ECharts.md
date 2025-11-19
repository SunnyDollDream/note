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
import { init } from 'echarts'
import { onMounted, ref } from 'vue'

const div = ref()

onMounted(() => {
  const myChart = init(div.value)
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
## 相关配置
- xAxis
	直角坐标系 中的 x 轴, 如果 type 属性的值为 category ,那么需要配置 data 数据, 代表在 x 轴的呈现
- yAxis
	直角坐标系 中的 y 轴, 如果 type 属性配置为 value , 那么无需配置 data , 此时 y 轴会自动去 series 下找数据进行图表的绘制
- series
	系列列表。每个系列通过 type 决定自己的图表类型, data 来设置每个系列的数据
>配置项都是以键值对的形式存在, 并且配置项有很多, ECharts 的学习大多是针对于这些配置项的, 对于配置项的学习, 大家可以不用死记硬背, 需要的时候查一查官方文档即可.[Documentation - Apache ECharts](https://echarts.apache.org/zh/option.html#title)