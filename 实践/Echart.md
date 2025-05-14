# Echart

标签（空格分隔）： 实践

---

## 1.关键函数
- `init`
  - 根据元素生成echart实例，同时元素绑定_echarts_instance_属性
  - 页面挂载时，必须执行该函数使元素绑定_echarts_instance_属性
- `clear`
  - 清空实例中的组件、数据等，用于确保重新渲染时的准确无误
  - 清空后再执行setOptions
- `dispose`
  - 销毁实例，用于确保不产生内存泄漏
  - 页面销毁时，必须执行该函数销毁实例


## 2.图标容器与大小
- 直接定义容器大小
```html
<div id="main" style="width: 600px;height:400px;"></div>
```
- 指定容器大小（容器无宽高或图表宽高不等于容器宽高）
```javascript
var myChart = echarts.init(document.getElementById('main'), null, {
    width: 600,
    height: 400
});
```
- 响应容器大小（容器大小变化时变更图表大小）
```javscript
var myChart = echarts.init(document.getElementById('main'));
window.addEventListener('resize', function() {
    myChart.resize();
});
```
- 为图表设置特定的大小
```javascript
myChart.resize({
  width: 800,
  height: 400
});
```

## 3.样式
- 设置主题
```javascript
// 除一贯主题外，只内置‘dark’主题
var chart = echarts.init(dom, 'dark');

// 官网主题编辑器下载对应主题json文件时
fetch('theme/vintage.json')
  .then(r => r.json())
  .then(theme => {
    echarts.registerTheme('vintage', theme);
    var chart = echarts.init(dom, 'vintage');
  })
  
// 官网主题编辑器下载对应主题js文件时
// HTML 引入 vintage.js 文件后（假设主题名称是 "vintage"）
var chart = echarts.init(dom, 'vintage');
```
- 设置调色盘，图形、系列自动在其中选择颜色
```javascript
option = {
  // 全局调色盘。
  color: [
    '#c23531',
    '#2f4554',
  ],

  series: [
    {
      type: 'bar',
      // 此系列自己的调色盘。
      color: [
        '#dd6b66',
        '#759aa0',
      ]
      // ...
    },
    {
      type: 'pie',
      // 此系列自己的调色盘。
      color: [
        '#37A2DA',
        '#32C5E9',
      ]
      // ...
    }
  ]
};
```
- 直接样式设置：itemStyle、lineStyle、areaStyle、label
- 高亮样式：emphasis



## 4.数据集
- （1）提供数据（2）指定数据到视觉的映射
- seriesLayoutBy：数据集的行或列映射为系列
  - column：默认值，系列安放到dataset的列上面
  - row：系列安放到dataset的行上面
- 维度：
  - 系列映射为列时，每一列为一个维度，每一行为数据项
  - `dataset.source`第一行可定义为维度名，由echarts自动检测
  - `dataset.sourceHeader: true`可指定第一行为维度名
  - `dataset.dimensions`/`series.dimensions`：同时指定维度名以及维度类型
- 数据到图形的映射：`series.encode`设置坐标映射数据集的列，从0开始


## 5.数据转换
- `dataset.transform`：将指定的dataset的数据根据指定转换规则转换为新的数据集
- `datasetIndex`：系列中指定引用的dataset
- 链式transform：transform数组，后一个输入基于前一个输出，最终生成一个dataset
- 一个transform多个data
```
{
      // 这个 dataset 的 index 为 `1`。
      transform: {
        type: 'boxplot'
      }
      // 这个 "boxplot" transform 生成了两个数据：
      // result[0]: boxplot series 所需的数据。
      // result[1]: 离群点数据。
      // 当其他 series 或者 dataset 引用这个 dataset 时，他们默认只能得到
      // result[0] 。
      // 如果想要他们得到 result[1] ，需要额外声明如下这样一个 dataset ：
},
{
      // 这个 dataset 的 index 为 `2`。
      // 这个额外的 dataset 指定了数据来源于 index 为 `1` 的 dataset。
      fromDatasetIndex: 1,
      // 并且指定了获取 transform result[1] 。
      fromTransformResult: 1
}
```
- `transform.print`：开发环境中将转换的dataset结果打印出来


### 1.数据转换器filter/sort
```
transform: {
    type: 'filter',
    config: { dimension: 'Year', '=': 2011 }
    // 这个筛选条件表示，遍历数据，筛选出维度（ dimension ）
    // 'Year' 上值为 2011 的所有数据项。
}
```
- 关于维度dimension：设定为维度名或者维度index
- 关于关系比较操作符
  - >（gt）、>=（gte）、<（lt）、<=（lte）、=（eq）、!=（ne、<>）、reg
  - 多个关系操作符声明： `{ dimension: 'Price', '>=': 20, '<': 30 }`
  - 类数值字符串：自动转换为数值进行比较
  - 日期对象/日期字符串：`{ dimension: 3, lt: '2012-05-12', parser: 'time' }`
  - 纯字符串：只能用在 = 或 != 上
- 关于逻辑比较：支持or/and/not以及嵌套形式
```
config: {
    or: [
        {
          and: [{
            dimension: 'Price', '>=': 10, '<': 20
          }, {
            dimension: 'Sales', '<': 100
          }, {
            not: { dimension: 'Product', '=': 'Tofu' }
          }]
        }, 
        {
          and: [{
            dimension: 'Price', '>=': 10, '<': 20
          }, {
            dimension: 'Sales', '<': 100
          }, {
            not: { dimension: 'Product', '=': 'Cake' }
          }]
        }
    ]
}
```
- 关于解析器parser
  - `parser: 'time'`：原始值解析为时间戳进行比较
  - `parser: 'trim'`：原始值为字符串时去除空格与换行符
  - `parser: 'number'`：默认情况下不需要指定，带后缀的数值需指定
- 按需引入
```
import {
  DatasetComponent,
  TransformComponent
} from 'echarts/components';

echarts.use([
  DatasetComponent,
  TransformComponent
]);
```



## 6.事件与行为
- 常规的鼠标事件类型，包括 `click`、 `dblclick`、 `mousedown`、 `mousemove`、 `mouseup`、 `mouseover`、 `mouseout`、 `globalout`、 `contextmenu`
- `chart.on(eventName, query, handler)`对指定query的组件的图形元素触发回调
```
// 监听click事件，params包含点击图形的数据信息
myChart.on('click', function(params) {
  if (params.componentType === 'markPoint') {
    // 点击到了 markPoint 上
    if (params.seriesIndex === 5) {
      // 点击到了 index 为 5 的 series 的 markPoint 上。
    }
  } else if (params.componentType === 'series') {
    if (params.seriesType === 'graph') {
      if (params.dataType === 'edge') {
        // 点击到了 graph 的 edge（边）上。
      } else {
        // 点击到了 graph 的 node（节点）上。
      }
    }
  }
});
```
- `myChart.dispatchAction({ type: '' })`手动触发图标行为
```
// 轮流高亮饼图中的每一个扇形
setInterval(function() {
  var dataLen = option.series[0].data.length;
  // 取消之前高亮的图形
  myChart.dispatchAction({
    type: 'downplay',
    seriesIndex: 0,
    dataIndex: currentIndex
  });
  currentIndex = (currentIndex + 1) % dataLen;
  // 高亮当前图形
  myChart.dispatchAction({
    type: 'highlight',
    seriesIndex: 0,
    dataIndex: currentIndex
  });
  // 显示 tooltip
  myChart.dispatchAction({
    type: 'showTip',
    seriesIndex: 0,
    dataIndex: currentIndex
  });
}, 1000);
```
- 监听空白处事件
```
myChart.getZr().on('click', function(event) {
  // 没有 target 意味着鼠标/指针不在任何一个图形元素上，它是从“空白处”触发的。
  if (!event.target) {
    // 点击在了空白处，做些什么。
  }
});
```



## 7.柱状图

