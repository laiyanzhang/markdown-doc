# uni-app

标签（空格分隔）： 技术栈

---
## 1.跨端相关

### 1.css

- 元素选择器：非H5端不支持*选择器，元素选择器修改：body => page、div/ul/li => view、 span/font => text、a => navigator、img => image
- 样式影响：H5端默认开启scoped，其他则需要在style标签加上scoped属性
- webview浏览器兼容性：低版本的app端不支持新css
- 顶部与底部距离：APP和小程序的导航栏和tabbar均是原生控件，元素区域坐标是不包含原生导航栏和tabbar的；而 H5 里导航栏和tabbar是div模拟实现的，元素坐标会包含导航栏和tabbar的高度。uni-app 新增了2个css变量：--window-top 和 --window-bottom，这代表了页面的内容区域距离顶部和底部的距离。

### 2.js

- js对象：非H5端没有浏览器专用的js对象，比如document、xmlhttp、cookie、window、location、navigator、localstorage、websql、indexdb、webgl等对象
- app端兼容：HBuilderX 2.6起，App端新增了renderjs，这是一种运行在视图层的js，vue页面通过renderjs可以操作浏览器对象，进而可以让基于浏览器的库直接在uni-app的App端运行
- api调用：wx的api在app里也可以直接运行，比如写wx.request和uni.request是一样的
- 生命周期：组件内不支持onLoad、onShow等页面生命周期

### 3.模板语法

- v-html：在h5和app-vue均支持，但小程序不支持
- 元素标签：div => view、span => text、a => navigator，div、span也可以运行在app和小程序上，因为uni-app编译器会把这些HTML标签编译为小程序标签




