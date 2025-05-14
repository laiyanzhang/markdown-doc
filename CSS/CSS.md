# CSS

标签（空格分隔）： CSS

---


## 1.各类主要选择器
- 常规选择器
  - `p`：元素选择器
  - `.class`：类选择器
  - `#id`：id选择器
  - `input[type="text"]`：属性选择器
  - `div p`：后代选择器
- 常用选择器
  - 父级是div元素的p元素：`div>p`
  - div元素之后的第一个p元素（兄弟元素）：`div+p`
  - p元素之后的每一个ul元素：`p~ul`
  - p元素是其父级的第一个子级：`p:first-child`
  - p元素是其父级的第二个子级：`p:nth-child(2)`
  - p元素是其父级的第一个p元素：`p:first-of-type`
  - p元素是其父级的第二个p元素：`p:nth-of-type(2)`
  - p元素前后插入内容：`p:before / p:after`
  - 并非p元素的元素：`:not(p)`
- 特殊选择器
  - 具有焦点的input元素：`input:focus`
  - p元素的第一个字母：`p:first-letter`
  - p元素的第一行：`p:first-line`


## 2.各类非常规样式属性
- background
  - 图像在对应方向上平铺：`background-repeat: repeat-x/repeart-y/no-repeat`
  - 图像在背景中的位置：`backgrounf-position: right top`
  - 图像在背景中是否固定或随页面滚动：`backgrounf-attachment: fixed`
  - 设置背景模糊度：`backdrop-filter: blur(10px)`
- text
  - 元素内文本左右两端对齐：`text-align: justify`
  - 设置文本的装饰：`text-decoration: none/underline/line-through`
  - 文本转换大小写：`text-transform: uppercase/lowercase/lowercase`
  - 文本第一行缩进：`text-indent: 50px`
  - 文本换行：`white-space: nowrap`
  - 文本垂直对齐：`vertical-align: middle`
- position
  - 跨越阈值前相对定位，跨越后固定定位：`position: sticky`
- @media
  - 根据媒体类型选择不同样式：`@media screen/print`
  - 根据屏幕大小选择不同样式：`@media (max-width:600px)`
- 选中没有子元素的元素：`:empty`
- 文本禁止光标选中：`user-select：none`
- 设置自身/后代元素获取焦点时伪类：`:focus-within`


## 3.对齐
- 水平对齐：
  - `text-align: center`
  - `width: 50%; margin: auto;`
- 垂直对齐：
  - `vertical-align: middle`
  - `padding: 70px 0`
  - `line-height: 40px; height: 40px;`
  - `position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);`
  - `display: flex; align-items: center; justify-content: center;`
- 左右对齐
  - `position: absolute; right: 0;`
  - `float: right/left`


## 4.盒子模型
- 由外到内：margin、outline(不占用weight与height)、border、padding、content
- 属性顺序：上右下左、上（左右）下、（上下）（左右）
- box-sizing:
  - `content-box`：width仅包括content（默认）
  - `border-box`：width包括content、padding、border
- BFC触发特性
  - body 根元素
  - 浮动元素：float 除 none 以外的值
  - 绝对定位元素：position (absolute、fixed)
  - display 为 inline-block、table-cell、flex
  - overflow 除了 visible 以外的值 (hidden、auto、scroll)
- BFC布局规则
  - 内部的盒子垂直方向上排列
  - BFC内两个相邻box的margin会发生重叠(margin塌陷)
  - 每个盒的左外边界挨着包含块的左外边界，即使存在浮动
  - BFC的区域不会与float元素重叠
  - BFC是独立的容器，容器内的子元素不会影响到外面的元素，反之亦然。
  - 计算BFC的高度时，浮动元素会撑起父元素的高度。
- BFC应用
  - 自适应两栏布局：利用BFC与float元素不会重叠的特性
  - 解决margin塌陷：将两个盒子放在不同的BFC中
  - 父元素内部高度为0：子元素浮动导致父元素高度为0，父元素设置为BFC



## 5.CSS3新增特性
- 边框：
  - 边框图片：`border-image: url(border.png) 30 30 round`
  - 阴影：`box-shadow: 10px 10px 5px #888888;`
  - 圆角：`border-radius: 25px;`
- 背景：
  - 背景图片：`background-image: url(img_flwr.gif)`
  - 背景图片尺寸：`background-size: 80px 60px;`
  - 背景图片的位置区域：`background-origin: content-box/padding-box/border-box`
  - 背景的绘制区域：`background-clip: content-box/padding-box/border-box`
- 渐变
  - 线性渐变：`background-image: linear-gradient(to right, red , yellow);`
  - 径向渐变：`background-image: radial-gradient(red, yellow, green);`
- 文本效果
  - 文本阴影：`text-shadow: 5px 5px 5px #FF0000;`
  - 盒子阴影：`box-shadow: 10px 10px grey;`
  - 文本溢出：`text-overflow: ellipsis;`
  - 文本换行：`word-wrap: break-word;`
  - 单词拆分：`word-break: break-all;`
- 转换
  - 平移：`transform: translate(50px,100px);`
  - 2d旋转：`transform: rotate(30deg);`
  - 宽度高度放大/缩小：`transform: scale(2,3);`
  - 倾斜：`transform: scale(2,3);`
  - 3d旋转：`transform: rotateX(30deg) / transform: rotateY(30deg);`
- 过渡
  - 过渡：`transition: width 2s, height 2s, transform 2s;`
  - 属性：`transition-property: width;`
  - 持续时间：`transition-duration: 1s;`
  - 时间曲线：`transition-timing-function: linear;`
  - 延迟时间：`transition-delay: 2s;`
- 动画
  - 定义动画：`@keyframes myfirst {from {background: red;} to {background: yellow;}}`
  - 使用动画：`animation: myfirst 5s;`
- 弹性盒子
  - 定义：`display: flex`
  - 元素排列顺序：`direction: rtl`
  - 元素排列方式：`flex-direction: row/colunmn`
  - 元素主轴对齐方式：`justify-content: center`
  - 元素侧轴对齐方式：`align-items: center`
  - 元素换行方式：`flex-wrap: wrap`
  - 各行侧轴对齐方式：`align-content: center`
  - 元素顺序：`order: 1`
  - 元素居中：`margin: auto`
  - 元素扩大缩小比率：`flex: 1 1 auto`



## 7.grid布局
- 定义：设置行列的二维布局
- 网格结构
  - 行（Row）与列（Column）
  - 轨道（Track）：行或列的网格线
  - 单元格（Cell）：行和列的交叉区域
  - 区域（Area）：合并多个单元格形成的矩形区域


### 1.容器属性
- 布局：`display`
  - 块级元素：`grid`
  - 行内元素：`inline-grid`
- 设置列宽：`grid-template-columns`
  - 分别设置三列固定宽：`200px 100px 200px`
  - 简化重复的值`repeat`：`repeat(3, 100px)`
  - 列数量不确定，设置列宽尽可能容纳多的列`auto-fill`：`repeat(auto-fill, 200px)`
  - 剩余空间等分占比`fr`，第二列占1/3，第三列占2/3，：`200px 1fr 2fr`
  - 设置长度范围`minmax`：`1fr 1fr minmax(300px, 2fr)`
  - 浏览器决定长度`auto`：`100px auto 100px`
- 设置行高：`grid-template-rows`类似设置列宽
- 设置间距
  - 行间距`row-gap`
  - 列间距`column-gap`
  - 行列间距`gap`：先行后列
- 布局排列方式：`grid-auto-flow：row | column | row dense`
  - 出现空白时，让排列后面长度合适的单元格填满空白：`grid-auto-flow: row dense`
- 单元格内容的水平位置：`justify-items: start | end | center | stretch`
  - 占满宽度：`justify-items: stretch`（默认）
- 单元格内容的垂直位置：`align-items: start | end | center | stretch`
- 整个内容区域在容器里面的水平位置：`justify-content: start | end | center | stretch | space-around | space-between | space-evenly;`
- 整个内容区域在容器里面的垂直位置：`align-content: start | end | center | stretch | space-around | space-between | space-evenly;`
- 显式网格：包含了在`grid-template-columns`和`grid-template-rows`属性中定义的行和列
- 隐式网格：超出定义的单元格数量而隐式创建的行和列
- 设置隐式网格的列宽与行高：`grid-auto-columns`和`grid-auto-rows`


### 2.项目属性
- 可以指定网格项目所在的四个边框，分别定位在哪个轨道，从而圈定项目的位置
  - grid-column-start 属性：左边框所在的列轨道
  - grid-column-end 属性：右边框所在的列轨道
  - grid-row-start 属性：上边框所在的行轨道
  - grid-row-end 属性：下边框所在的行轨道
- 单项目设置单元格内容的水平位置：`justify-self`
- 单项目设置单元格内容的垂直位置：`align-self`
  

### 3.结合使用
- 区域划分：
  - grid-template-areas：划分为6个单元格的区域划分，.代表空的单元格。若有相同名称的则将单元格合并为一个区域，元素直接占满整个区域
  - grid-area：代表项目所在区域名称

```css
.wrapper {
  display: grid;
  grid-gap: 10px;
  grid-template-rows: 120px  120px  120px;
  grid-template-areas:
    ". header  content"
    "sidebar header content";
  background-color: #fff;
}

.sidebar {
  grid-area: sidebar;
}
.content {
  grid-area: content;
}
.header {
  grid-area: header;
}

```


### 4.应用场景
1. 单元格设置固定宽度A，行宽足够时尽可能放入足够多单元格。不足够一个单元格时，其他单元格通过拉伸的方式填补多余的空白空间：`grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));`
2. 基于第1种场景，每个网格元素的长度可能不相同：设置该网格项目跨度为3个单元格`grid-column-start: span 3`
3. 基于第2种场景，希望其他单元格填补多余的空白空间：`grid-auto-flow: row dense`


### 5.变量区分
- `auto-fill`和`auto-fit`
| auto-fill | auto-fit |
| -- | -- | -- |
| 需要保持严格的网格列数一致性 |希望内容最大化利用可用空间 |
| 未来可能动态添加更多项目 |  项目数量固定且需要视觉填充 |
| 配合绝对定位元素使用	| 创建自适应拉伸效果 |


## 8.各类实现
### 1.文字超过显示省略号
```css
// 显示单行
white-space: nowrap; // 文本强制不换行；
text-overflow: ellipsis; // 文本溢出显示省略号；
overflow: hidden; // 溢出的部分隐藏；
```
```css
// 显示双行
overflow: hidden;
-webkit-line-clamp: 2;
text-overflow: ellipsis;
display: -webkit-box;
-webkit-box-orient: vertical;
```

### 2.文字颜色渐变

```css
background: linear-gradient(
  to right,
  rgb(65, 85, 240),
  rgb(74, 213, 255)
);
background-clip: text;
-webkit-text-fill-color: transparent;
```

### 3.图片自适应横竖屏展示

```css
.left {
  width: 1000px;
  height: 100%;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
  background: rgba(250, 250, 250, 1);
  .image {
    width: 100%;
    height: 100%;
    object-fit: contain; // 保持宽高比完整显示，不裁剪内容但可能留白
  }
}
```


## 9.瀑布流布局
### 1.多列布局（纯CSS）
- 优点：无需JS计算，代码简洁的瀑布流效果
- 缺点：仅支持从上到下的排列，无法支持从左到右排列

```javascript
<template>
    <div class="masonry_1">
      <div class="item" v-for="(item, index) in 20" :key="index">
        {{ index }}
      </div>
    </div>
</template>

<style scoped lang="less">
.masonry_1 {
  width: 100%;
  columns: 4;
  column-gap: 8px;
  .item {
    width: 100%;
    break-inside: avoid;
    margin-bottom: 8px;
    background-color: #ccc;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 20px;

    &:nth-child(n) {
      height: 200px;
    }
    &:nth-child(2n) {
      height: 300px;
    }
    &:nth-child(3n) {
      height: 400px;
    }
  }
}
</style>
```

### 2.grid布局（CSS + JS计算）
- 优点：支持从左到右排列
- 缺点：需要进行JS计算，元素多时会卡顿

```javascript
<template>
    <div class="masonry_2">
      <div class="item" v-for="(item, index) in 20" :key="index">
        {{ index }}
      </div>
    </div>
</template>

<script>
import { defineComponent, ref, onMounted } from "vue";

export default defineComponent({
  name: "MasonryComponent",
  setup() {
    onMounted(() => {
      // grid布局
      const masonry = document.querySelector(".masonry_2");
      const items = masonry.querySelectorAll(".masonry_2 .item");
      items.forEach((item) => {
        // 模拟高度获取，若为图片可获取图片高度
        const height = Math.floor(Math.random() * 200 + 50);
        // 根据元素高度设置元素的需占行数
        const rows = Math.ceil(height / 10);
        item.style.gridRowStart = `span ${rows}`;
      });
    });

    return {};
  },
});
</script>

<style scoped lang="less">
.masonry_2 {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px;
  grid-auto-rows: 10px;
  .item {
    background: #ccc;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
</style>
```


### 3.flex布局（CSS + JS划分）
- 优点：支持从左到右排列，无需复杂计算
- 缺点：按照数量划分各列图片，缺少高度智能排列，可能存在各列高度差距较大的情况

```javascript
<template>
    <div class="masonry_3">
      <div class="column column_1">
        <div class="item" v-for="item in column1" :key="item">
          {{ item }}
        </div>
      </div>
      <div class="column column_2">
        <div class="item" v-for="item in column2" :key="item">
          {{ item }}
        </div>
      </div>
      <div class="column column_3">
        <div class="item" v-for="item in column3" :key="item">
          {{ item }}
        </div>
      </div>
    </div>
</template>

<script>
import { defineComponent, ref } from "vue";

export default defineComponent({
  name: "MasonryComponent",
  setup() {
    // felx布局
    const data = ref([
      1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20,
    ]);
    let column1 = ref([]), //第一列
      column2 = ref([]), //第二列
      column3 = ref([]); //第三列
    let i = 0;
    while (i < data.value.length) {
      column1.value.push(data.value[i++]);
      if (i < data.value.length) {
        column2.value.push(data.value[i++]);
      }
      if (i < data.value.length) {
        column3.value.push(data.value[i++]);
      }
    }

    return {
      column1,
      column2,
      column3,
    };
  },
});
</script>

<style lang="less" scoped>
.masonry_3 {
  display: flex;
  gap: 8px;
  .column {
    display: flex;
    flex-direction: column;
    flex: 1;
    gap: 8px;
    .item {
      width: 100%;
      background-color: #ccc;
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 20px;
    }
  }
  .column_1 {
    .item {
      &:nth-child(n) {
        height: 280px;
      }
      &:nth-child(2n) {
        height: 100px;
      }
      &:nth-child(3n) {
        height: 320px;
      }
    }
  }
  .column_2 {
    .item {
      &:nth-child(n) {
        height: 200px;
      }
      &:nth-child(2n) {
        height: 320px;
      }
      &:nth-child(3n) {
        height: 120px;
      }
    }
  }
  .column_3 {
    .item {
      &:nth-child(n) {
        height: 230px;
      }
      &:nth-child(2n) {
        height: 340px;
      }
      &:nth-child(3n) {
        height: 400px;
      }
    }
  }
}
</style>
```


### 4.第三方库masonry-layout
- 优点：封装代码，使用简单
- 缺点：封装内部本质上依旧是JS计算

```javascript
<template>
    <div class="masonry_4" v-show="way === 4">
      <div class="grid-sizer"></div>
      <div class="gutter-sizer"></div>
      <div class="grid-item">1</div>
      <div class="grid-item grid-item-width2">2</div>
      <div class="grid-item">3</div>
      <div class="grid-item">4</div>
      <div class="grid-item">5</div>
      <div class="grid-item">6</div>
      <div class="grid-item">7</div>
      <div class="grid-item">8</div>
      <div class="grid-item">9</div>
    </div>
</template>

<script>
import { defineComponent, reative, onMounted } from "vue";
import Masonry from "masonry-layout";

export default defineComponent({
  name: "MasonryComponent",
  setup() {
    let masonry = reative({})
    onMounted(() => {
      // 第三方库
      masonry = new Masonry(".masonry_4", {
        columnWidth: ".grid-sizer", // 列宽度，未设置时将第一个项的宽度作为列宽度，也支持设置固定宽度
        gutter: ".gutter-sizer", // 添加项元素间的横向间隔，也支持设置固定宽度
        itemSelector: ".grid-item", // 指定将在布局中作为项的子元素
        percentPosition: true, // 将项的位置设为百分比优于设为像素值
        transitionDuration: "0.2s", // 过渡效果持续时间
        stagger: 30, // 项转换位置时为其提供交错效果，用以毫秒为单位的数字
      });
    });
  },
});
</script>

<style scoped lang="less">
.masonry_4 {
  width: 100%;
  height: 100%;
  position: relative;
  .grid-sizer {
    width: calc(25% - 12px);
  }
  .gutter-sizer {
    width: 8px;
  }
  .grid-item {
    width: calc(25% - 12px);
    margin-bottom: 8px;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 20px;
    background-color: #ccc;
    &:nth-child(n) {
      height: 200px;
    }
    &:nth-child(2n) {
      height: 300px;
    }
    &:nth-child(3n) {
      height: 400px;
    }
  }
  .grid-item-width2 {
    width: calc(50% - 16px);
    height: 300px;
  }
}
</style>
```
- 当渲染的是图片时

```javascript
import imagesLoaded from 'imagesLoaded'

// 当内部存在图片时，等待图片加载完全再进行排列，否则可能出现项堆叠的情况
imagesLoaded('.masonry_4', () => {
    new Masonry('.masonry_4', {
      columnWidth: '.grid-sizer', // 列宽度，未设置时将第一个项的宽度作为列宽度，也支持设置固定宽度
      gutter: '.gutter-sizer', // 添加项元素间的横向间隔，也支持设置固定宽度
      itemSelector: '.grid-item', // 指定将在布局中作为项的子元素
      percentPosition: true, // 将项的位置设为百分比优于设为像素值
      transitionDuration: '0.2s', // 过渡效果持续时间
      stagger: 30 // 项转换位置时为其提供交错效果，用以毫秒为单位的数字
    })
})
```

- 触底加载更多时

```javascript
// 加载更多元素后重载各元素并重新布局
masonry.reloadItems()
masonry.layout()
```