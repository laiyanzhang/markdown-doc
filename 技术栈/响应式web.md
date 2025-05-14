# 响应式web

标签（空格分隔）： 技术栈

---

## 1.媒体查询
- 视口viewport：呈现网页的区域
- 根据设备宽度渲染内容：`<meta name="viewport" content="width=device-width">`
- 媒体查询：以项目设计划分断点而并非设备不同划分断点
- 根据设备类型/设备朝向引入不同css文件：
`<link rel="stylesheet" media="screen and (orientation: portrait)" href="portrait-screen.css" />`
- 样式文件引入：`@import url("phone.css") screen and (max-width:360px);`(增加http请求)
- 样式文件判断：`@media screen and (max-device-width: 400px) { }`
- 阻塞渲染：优先加载当前媒体的css样式文件，其他css文件为非阻塞渲染
- meta标签的content属性值
  - initial-scale=2.0：把内容放大为实际大小的两倍
  - maximum-scale=3: 内容最大为设备宽度三倍
  - minimum-scale=0.5"：内容最小为设备宽度的二分之一
  - user-scalable=no：禁止缩放
- 媒体查询4级
  - 可编程特性scripting：none/enable/initial-only(是否支持JavaScript)
  - 交互媒体特性pointer：none/coarse/fine(鼠标等指标设备是否存在)
  - 悬停媒体特性hover
  - 环境媒体特性light-level：normal/dim/washed(环境光线强度)



## 2.弹性布局
- 场景1：弹性元素左边开始排列，最后一个弹性元素在最右边
- 实现1：最后一个元素样式margin-left:auto即可用上该侧所有外边距 
- display:inline-flex：
  - 元素为父元素：根据子元素宽高决定自身宽高
  - 元素为子元素：作为父元素的弹性元素且自带子元素间间距，弹性布局中则消除间距
- align-items：弹性盒子交叉轴上的弹性元素对齐方式
- align-self：单独的弹性元素设置对齐方式（没有配置align-items的情况）
- justif-content：弹性盒子主轴上的弹性元素对齐方式
- flex: flex-grow flex-shrik flex-basis (扩展、收缩、基准值)
- order：决定弹性元素的顺序，集合媒体查询调整布局


## 3.分辨率图片
- 根据不同分辨率适用不同图片：srcset属性设置不同分辨率下图片
```
<img 
src="scones_small.jpg" 
srcset="scones_medium.jpg 1.5x, scones_ large.jpg 2x" 
alt="Scones taste amazing">
```
- 根据不同屏幕适用不同宽度图片：sizes属性决定不同屏幕大小下的图片宽度
```
<img 
srcset="scones-small.jpg 450w, scones-medium.jpg 900w" 
sizes="(min-width: 17em) 100vw, (min-width: 40em) 50vw" 
src="sconessmall. jpg" 
alt="Scones">
```
- 根据不同屏幕适用不同图片：picture中支持不同屏幕下的图片来源
```
<picture> 
 <source media="(min-width: 30em)" srcset="cake-table.jpg"> 
 <source media="(min-width: 60em)" srcset="cake-shop.jpg"> 
 <img src="scones.jpg" alt="One way or another, you WILL get 
cake."> 
</picture>
```


## 4.HTML5
- a标签：可以把多个元素放入a标签内
- 新语义标签：
  - `<main>`：页面主体内容，唯一且不作为其他其他新语义标签的子元素
  - `<section>`：定义文档或者某个通用区块
  - `<nav>`：导航
  - `<article>`：常用于文章内容区块
  - `<aside>`：与主内容无直接关系的内容
  - `<header>`：头部信息
  - `<footer>`：尾部信息
  - `<address>`：地址信息
- `<detail>`与`<summary>`：详细内容与缩略内容，浏览器内置样式
```
<details> 
     <summary>I ate 15 scones in one day</summary> 
     <p>Of course I didn't. It would probably kill me if I did. What a 
    way to go. Mmmmmm, scones!</p> 
</details>
```


## 5.CSS3
- 根据视口宽度将文字划分为若干区块
  - column-width: 区块宽度
  - column-count: 区块个数
  - column-gap: 区块间隔 
  - column-rule: 区块分割线
- 文本截断
  - word-wrap: break-word 允许长单词/长数字等换行
  - text-overflow: ellipsis 文字省略号 
  - white-space: no-wrap 不允许换行
- 隐藏滚动条：.element::-webkit-scrollbar { display: none }
- 特性查询：@supports (display: flex) 查询是否支持弹性布局
- CSS分支：`<head>`中引入Modernizr库，库生成特性类写在顶部，在特性类后添加样式
- 属性选择器：
  - [attr] { }：选中有该attr属性的元素
  - [attr="value"]：选中attr属性值为value的元素
  - [attr^="value"]：选中attr属性值**开头**为value的元素
  - [attr\*="value"]：选中attr属性值包含value的元素
  - [attr\$="value"]：选中attr属性值**结尾**为value的元素
- 特殊选择器
  - 连续选择器`.Item:nth-child(4n+1):nth-last-child(-n+4)`：这个元素既是每4个元素中的第1个，也是最后4个元素中的其中1个
  - 空选择器：`:empty`没有任何子元素的元素
  - 非选择器：`:not(选择器)`并非选择器元素的元素
  - 首行选择器：`:first-line`元素的第一行
- CSS4：`:has()`包含选择器子元素的父元素
- 视口百分比单位：vw：视口宽度、vh：视口高度
- 定义不同字体：`@font-face { } `
- 颜色：
  - rgba：三原色、透明度
  - hsla：色相、饱和度、亮度、透明度


## 6.CSS3高级技术
- 阴影
  - 文本：`text-shadow: 0px 1px #fff,4px 4px 0px #dad7d7;`
  - 盒子：`box-shadow:inset 0 0 30px hsl(0, 0%, 0%),0 10px 10px -10px hsl(0, 97%, 53%);`
- 渐变
  - 渐变方向：`background: linear-gradient(to top right, red, blue);`
  - 径向渐变：`background: radial-gradient(12rem circle at bottom, yellow, red);`
  - 重复渐变：`background: repeating-radial-gradient(black 0px, orange 5px, red 10px);`
  - 色标：`background: linear-gradient(#f90 0, #eee 50%, #f90 100%);`
- 背景
```
background: url('rosetta.png'), url('moon.png'), url('stars.jpg'); // 背景图片顺序
background-size: 75vmax, 50vw, cover;                              // 背景图片尺寸
background-position: top 50px right 80px, 40px 40px, top center;   // 背景图片位置
background-repeat: no-repeat;                                      // 背景重复
```
- 滤镜：`filter: drop-shadow(8px 8px 6px #333);`
 

 
## 7.SVG
- 插入SVG
  - img：`<img src="mySconeVector.svg" alt="Amazing line art of a scone" />`
  - object：`<object data="img/svgfile.svg" type="image/svg+xml" />`
  - 背景图片：`background-image: url('image.svg');`
  - 内联：`<svg width="198px" height="188px" viewBox="0 0 99 94" />`
  - 符号复用：先定义svg图形，后根据需要进行调用



## 8.CSS3过渡、变形和动画
### 1.过渡transition
- 例子：`transition: box-shadow 1s ease 0s;`在box-shadow属性上经过1s进行过渡
- `transition-property` ：要过渡的 CSS 属性的名字
- `transition-duration`：定义过渡效果持续的时长
- `transition-timing-function`：定义过渡期间的速度变化
- `transition-delay`：可选，用于定义过渡开始前的延迟时间


### 2.2D变形transform
- `scale(1.4)`：放大缩小
- `translate(-20px, -20px)`：平面移动
- `rotate(30deg)`：平面旋转
- `skew(40deg, 12deg)`：斜切
- `transform-origin: 270px 20px`：设置变形中心


### 3.3D变形
```javascript
<html>
    <div class="flipper"> 
        <span class="flipper-object flipper-vertical"> 
            <span class="panel front">The Front</span> 
            <span class="panel back">The Back</span> 
        </span> 
    </div>
</html>


<style>
.flipper { 
    perspective: 400px;  // 视点到3D控件的距离，越近效果越明显 
    position: relative; 
    .flipper-object { 
         position: absolute; 
         transition: transform 1s; 
         transform-style: preserve-3d; // 子元素保持3D效果
         panel { 
             top: 0; 
             position: absolute; 
             backface-visibility: hidden; // 翻转时隐藏元素背面
        }
    }
    .flipper-vertical .back { 
        transform: rotateX(180deg); // 使背面面板默认是翻转的
    } 
    .flipper-horizontal .back { 
        transform: rotateY(180deg); 
    }
}
.flipper:hover .flipper-vertical { 
     // rotateX悬停时触发翻转，translate3dX轴上的偏移、Y轴上的偏移、Z轴上的偏移
     transform: rotateX(180deg) translate3d(0, 0, -120px); 
     animation: pulse 1s 1s infinite alternate both; 
} 
.flipper:hover .flipper-horizontal { 
     transform: rotateY(180deg) translate3d(0, 0, 120px); 
     animation: pulse 1s 1s infinite alternate both; 
} 
</style>
```


### 4.动画
```
// 定义动画关键值，可定义百分比时间轴上的状态
@keyframes pulse { 
     100% { 
         text-shadow: 0 0 5px #bbb; 
         box-shadow: 0 0 3px 4px #bbb; 
     } 
}
// 翻转之后的动画展示
.flipper:hover .flipper-horizontal { 
     transform: rotateY(180deg); 
     animation: pulse 1s 1s infinite alternate both; 
}
关键帧名字：pulse
动画持续时长：1s
动画开始延迟：1s
动画运行的次数：infinite
动画播放方向：alternate
保留动画状态：both（动画结束后会回归原始状态，该值可使元素保留动画执行后的状态）
```


## 9.表单
- input元素HTML5新增属性
  - 占位符号placeholder：`:placeholder-shown`选择器设置占位字符样式
  - 必填校验required：校验必填
  - 自动聚焦autofocus
  - 自动补全autocomplete：属性值为off时关闭
- list与datelist
```
// list属性值与datalist的id值对应，用作输入框的备选值，显示输入的匹配项
<input id="awardWon" name="awardWon" type="text" list="awards"> 
<datalist id="awards"> 
    <select>
        <option value="Best Picture"></option> 
        <option value="Best Director"></option> 
        <option value="Best Adapted Screenplay"></option> 
        <option value="Best Original Screenplay"></option> 
    </select> 
</datalist>
```
- input输入类型校验
  - `type="email"`：提交时校验输入值为邮箱形式
  - `type="url"`：提交时校验输入值为url形式
  - `type="tel"`：触摸屏中出现数字键盘
  - `type="color"`：颜色选择器
  - `type="search"`：触摸屏特殊处理
  - `patter=""`：符合正则表达式的输入
  - `type="range" min="1" max="10" value="5"`：范围选择但无数值显示
  - `type="number" min="1929" max="2015" step="10"`：提交时校验为数字，提供上下箭头调整数字，每次调整的变化幅度
- 时间选择器
  - `type="date"`：选择日期
  - `type="month"`：选择月份
  - `type="week"`：选择周
  - `type="time"`：选择时间点
- 必填项样式
```
// 使用flex布局的倒序选择兄弟节点
input:required + label:after { 
     content: "*"; 
     font-size: 2.1em; 
     position: relative; 
     top: 6px; 
     display: inline-flex; 
     margin-left: .2ch; 
     transition: color, 1s; 
} 

// 在必填项中输入正确的值时星号变绿
input:required:invalid + label:after { 
    color: red; 
} 
input:required:valid + label:after { 
    color: green; 
}
```


## 10.响应式设计
- 设计不同视口宽度下的断点
- 真实设备中观察和使用
- 渐进增强：先适应低支持的版本再渐进增强，优化HTML5代码减少CSS与JS需要的老式兼容
- 确定支持的浏览器：等价功能无非外观
- 分层体验：新式浏览器具备更佳的使用体验
- 避免使用CSS框架
- 根据视口隐藏与展示内容



