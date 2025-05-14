# Mobile

标签（空格分隔）： 实践

---

## 1.APP开发模式
- Native APP：原生app开发模式，IOS与Android各自语言开发各自app
  - 优点：性能和体验最好
  - 缺点：开发和发布成本高
- WAP：传统智能手机，适用于手机键盘操作
- Web APP：移动端网站，H5应用
  - 优点：开发和发布成本低
  - 缺点：性能和体验较差，受浏览器处理能力的限制
- Hybrid APP：混合模式移动开发
  - 兼具Native APP良好交互体验的优势和Web APP跨平台开发的优势。
  - 利用web技术开发，调用API调用原生的功能
  - WebView：Android中用于展示一个web页面，显示和渲染网页并可与页面JavaScript交互实现混合开发
  - JSBridge：
    - Native调用JavaScript：直接执行拼接好的JavaScript代码
    - JavaScript调用Native：注入API直接执行Native逻辑/native拦截url scheme进行操作
- React Native APP：JSX写原生界面，js通过JSBridge调用原生API渲染UI互相通信
- Weex APP：支持vue2.0


## 2.不同平台展示不同内容
- navigator：useAgent属性，检测关键字是否存在indexof>-1
  - 移动端浏览器，返回字符串内带有Mobile关键字
  - pc端打开，返回字符串内带有Windows关键字
  - 微信，返回字符串内带有MicroMessenger关键字
  - Android机型，返回字符串Android关键字或者Linux关键字
  - iPhone机型，返回字符串iPhone关键字
  - iPad机型，返回字符串iPad关键字
- navigator：language属性/browserLanguage属性，不同国家语言显示不同页面
- 根据window.location.href改变不同的url展示不同内容



## 3.移动端适配
- 开发移动设备的网站：
```
<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=0">
```
- 辅助功能：
``` javascript
<meta content="telephone=no,email=no" name="format-detection" />
//禁止自动识别电话号码和邮箱
<meta content="yes" name="apple-mobile-web-app-capable" />
//苹果手机：会删除默认的工具栏和菜单栏，网站开启对web app程序的支持
<meta name="apple-mobile-web-app-status-bar-style" content="black" />
//苹果手机：在web app应用下状态条（屏幕顶部条）的颜色,默认值为default（白色），可以定为black（黑色）和black-translucent（灰色半透明）。
<meta name="apple-touch-fullscreen" content="yes" />
//苹果手机：如果把一个web app添加到了主屏幕中，那么从主屏幕中打开这个web app则全屏显示
<link rel="apple-touch-icon" href="/static/images/identity/HTML5_Badge_64.png" />
//苹果手机：将应用添加到手机主屏幕，会有一个icon可以直接进入
```
- 目前而言解决移动端开发的方案有两种
  - 响应式布局：适用于以信息发布为主，访问或交互比较少的网站，只需要维护一套代码，针对桌面和移动的代码都会被同等加载不利于网络优化(可通过模块化开发针对性加载)，JS交互操作难以做出差异(特性检测分开不同逻辑)
  - 两种设计：适用于访问或交互比较多的网站，需要维护两套代码，可同时兼容键鼠和触摸的JS操作
- 目前移动端浏览器内核：大多数移动端是-webkit-内核，使用chrome调试
- 响应式布局：
  - 多媒体查询：@media 适用不同的css代码
  - 流式布局：百分比布局，基于父元素的布局
  - 液态图片：img{max-width:100%;}
  - flex布局：弹性盒子
- 样式大小单位：
  - em单位：相对于父元素文本的尺寸为1em，根据文本尺寸设置其他css样式的大小
  - rem单位：相对于根元素文本的尺寸为1rem，根据文本尺寸设置其他css样式的大小
  - 视区宽度/高度(vw/vh)：总宽度/总高度为100，视区为window.innerWidth/Height，类似于百分比的布局
- 像素
  - 物理像素：分辨率，设备屏幕实际拥有的像素点
  - 逻辑像素：CSS像素px
  - dpr：物理像素与逻辑像素之间的比例
  - PPI：像素密度
- viewport：默认为layout viewport
  - devicePixelRatio(dpr) = 物理像素 / 独立像素
  - scale = visual viewport / layout viewport
  - layout viewport：窗口全部内容宽度，通过 document.documentElement.clientWidth 来获取
  - visual viewport：浏览器可视区域宽度，通过window.innerWidth来获取
  - ideal viewport：移动设备的屏幕宽度
  - viewport设为ideal viewport：`width=device-width`与`initial-scale=1.0`，前者解决IE的问题，后者解决iPhone、iPad的问题，同时设置发生冲突时宽度适配最大值
  - visual viewport宽度 = ideal viewport宽度  / 当前缩放值
  - 在iphone和ipad上，无论你给viewport设的宽的是多少，如果没有指定默认的缩放值，则iphone和ipad会自动计算这个缩放值，以达到当前页面不会出现横向滚动条(或者说viewport的宽度就是屏幕的宽度)的目的。
  - 动态改变meta标签：
     - document.write动态输出meta viewport标签
     - element.setAttribute()改变meta标签的content属性
- 解决方案：
  - 设计稿为750px，手机设备为375px
  - 设置根元素fontsize = 屏幕宽度/设计稿宽度*基本宽度(基本宽度建议100px)
  - 计算每个元素的rem值进行开发
  - js获取设备宽度并重置fontsize
  - 多媒体查询设置fontsize(少数分辨率适用)