# KnowLedge

标签（空格分隔）： 实践

---
## 1.浏览器
### 1.浏览器兼容问题
- 浏览器内外补丁区别：*{margin:0;padding:0}
- 使用条件注释，在不同浏览器情况下使用不同的js包
- 对于内置对象的调用可先判断其是否存在再进行调用，否则使用对应的浏览器的调用方法
- CSS设计上不同属性


### 2.浏览器内核
- 使用Trident内核的浏览器：IE、Maxthon、TT； -ms-
- 使用Gecko内核的浏览器：Netcape6及以上版本、FireFox； -moz-
- 使用Presto内核的浏览器：Opera7及以上版本； -o-
- 使用Webkit内核的浏览器：Safari、Chrome。 -webkit-



### 3.浏览器渲染原理
 1. 浏览器将获取的HTML文档并解析成DOM树。
    - display:none也会出现在DOM树
    - link加载资源，css加载不会阻塞DOM解析
    - img加载不会阻塞html解析，加载后不渲染，需要与渲染树一起渲染
    - script加载会阻塞DOM树的解析，可通过设置defer和async属性进行异步加载延迟执行
    - defer属性：html解析且脚本加载完成后执行，然后触发DOMContentLoaded事件
    - async属性：脚本加载完成后执行，可能在DOMContentLoaded事件前后，但在load之前执行
    - 多个脚本加载时，async无顺序加载，defer有顺序加载
    - DOMContentLoaded事件发生在文档解析完毕之后，不需要等待图片等其他资源加载完成
    - load事件发生在页面所有资源(图片、音频、视频等)加载后触发
 2. 处理CSS标记，构成层叠样式表模型CSSOM(CSS Object Model)
    - CSS执行会与JavaScript互斥， 可以DOM树解析同时进行
    - 阻塞过程中会对脚本后面进行预加载但并不执行
    - DOM树和CSSOM边解析边渲染(节点边解析边生成)
 3. 将DOM和CSSOM合并为渲染树(rendering tree)将会被创建，代表一系列将被渲染的对象。
    - display:none不在树内;visibility:hidden在树内
    - CSS的加载会阻塞DOM的渲染
 4. 渲染树计算元素的布局layout
    - 浏览器使用一种流式处理的方法，通过递归调用一次pass绘制操作布局所有元素
    - float、absolute、fixed会发生位置偏移，脱离文档流即脱离渲染树
    - reflow重排发生在这个阶段：页面首次渲染、窗口大小变化、元素尺寸位置内容字体大小变化、增加或删除可见元素、激活伪类
 5. 将渲染树的各个节点绘制到屏幕上painting
    - repainting重绘发生在这个阶段：改变元素的背景、文字边框颜色、visibility、outline等不影响周围与内部布局的属性
    - 浏览器firstPaint能够对部分内容进行解析并显示，因此js放在底部可以减少firstPaint的时间，但不会减少DOMContentLoaded事件的时间

    
## 2.MVVM模型
- Model：数据模型，可以在Model中定义数据修改和操作的业务逻辑
- view：视图，也就是UI组件，负责将数据模型转化为UI展示
- view-model：监听数据模型中数据的变化，控制视图行为，处理用户交互，简单来说就是一个同步view和model的对象，用来连接view和model
- view和model之间没有直接的关联，他们通过view-model进行交互，通过数据绑定，view和model之间是能相互影响的。


## 3.性能优化
- 实现按需加载，例如vue框架中路由组件的利用required/import实现按需加载，避免打包过大加载过久，或者图片过多时进行图片的懒加载
- 优化DOM操作，例如将大量的DOM结点添加工作添加进一个DocumentFragment结点，再将该节点添加到DOM
- 减少属性的查找，例如将在一个函数中使用多次的全局对象存储为局部对象/将对象属性存储在局部对象中，避免不必要的属性查找
- 页面闪屏原因：DOM操作频繁导致页面重绘与回流增多(减少重绘和回流)
- 页面白屏原因：js操作阻塞，等待异步调用(将js操作放到页面后面)
- 图片的懒加载：
  - 预加载loading图片，src="loding"，data-src="true"
  - 判断哪些图片需要加载：当图片的可视化界面innerHeight+滚动界面scrollTop>=图片top-height
  - 通过temp.load进行图片的隐形加载
  - 加载成功后，img.src替换成data-src中的图片链接
- 减少重排与重绘（重排必定重绘、重绘不一定重排）
  - 尽量只改变子元素
  - 尽量使用class设计元素格式
  - 对于经常需要回流的组件，position设为fixed或absolute
  - 权衡速度的平滑，动画改变从1像素单位改为3像素单位
  - 对于元素结点的修改可以先从dom结点中抽离操作完毕再display到页面上
  - 减少不必要的dom层级以及复杂的css选择器
- 函数防抖：触发事件后在n秒内函数只能执行一次，如果在n秒内又触发了事件，则会重新计算函数执行时间
  - 原理：执行时清除计时器再设置定时器
  - 应用场景：搜索框输入，验证信息输入检测，窗口resize
- 函数节流：一定时间间隔内只执行一次
  - 原理：执行时若定时器存在则直接return
  - 应用场景：滚动加载、搜索联想、重复提交
- CSS性能优化 
  - 使用CDN
  - 合并CSS文件
  - 减少CSS嵌套
  - 建立公共css样式类或者公共css文件
  - 运用css的继承机制
  - css文件压缩



## 4.前端的SEO优化
- 重要内容优先加载，搜索引擎爬行抓取页面的顺序从上到下，从左到右
- `<title>`、`<h1>`、`<strong>`
- `<meta>`标签下的name：description/key
- 图片使用alt属性标注图片内容
- 动态设置document.title为页面相关标题
- 网站结构扁平状有利于搜索引擎抓取
- ajax动态加载的内容搜索引擎无法识别


## 5.事件循环机制Eventloop
- 特点：单线程、非阻塞
- 执行上下文：方法的私有作用域，上层作用域的指向，方法的参数，这个作用域中定义的变量以及这个作用域的this对象。
- 执行顺序
  - 将所有同步代码放入执行栈中，执行代码中的方法向其中加入执行上下文
  - 异步事件挂起，继续执行执行栈内任务，异步事件执行完毕后将回调函数放入事件队列
  - 执行栈任务执行完毕，主线程闲置情况下执行事件队列内事件，将事件放入执行栈中执行同步代码
- 宏任务与微任务
  - 首先执行主线程，主线程内的宏任务与微任务放入对应的队列当中
  - 当前执行栈执行完毕后，先执行微任务，再执行宏任务，均按照队列顺序执行
  - 宏任务主要是setTimeout（在固定的延迟时间后放入宏任务队列中）
  - 微任务主要是Promise对象
- 优先级：
  - V8老版本：promise.Trick()>promise的回调>async>setTimeout>setImmediate
  - V8新版本：promise.Trick()>promise的回调>setTimeout>setImmediate，async中等价于resolve()


## 6.模块化开发
- 初步：将模块写成对象，所有成员写在对象里面，可以改变里面的属性和方法不安全
- 改进：利用立即执行函数，返回方法进行调用，变量成为私有变量
- 利用构造函数：变量为私有变量，形成闭包，耗费内存，违背构造函数与实例对象数据分离的原则
- 使用原型对象：外部可以修改方法和属性，不安全
- 输入全局变量：自调用函数中写入全局变量参数即可调用，例如引入JQuery库


### 1.服务端模块：同步加载
- CommonJS：运行时加载，输出的是值的缓冲，不存在动态更新
  - 模块定义：模块设置为一个构造函数，module.exports = 函数名 / exports.变量名
  - 模块调用：var a = require('模块名')  实例对象 = new var()
  - 好处：避免全局污染、明确依赖项目、语法清晰
  - 缺点：同步加载模块，阻止浏览器运行其他内容


### 2.客户端模块：异步加载
- AMD：依赖前置原则(必须加载完依赖)
  - define([模块]，callback(模块参数名))：将依赖模块全部加载后再执行回调函数
- CMD：依赖就近原则(按需加载)
  - define(function(require, exports, module) {}）：内部在需要时进行加载
- UMD：同时支持AMD与CommonJS


### 3.ES6
- 编译时加载或静态加载，只加载对应的方法而不会有对象
- ES6模块自动采用严格模式
- 变量动态绑定：模块内设置setTimeout改变内部值，外部调用值也会改变
- 动态导入：CommonJS可动态导入js文件的地址，后者不支持
- 变量只读：对它进行重新赋值会报错，可以对对象添加属性但是不能重新赋值
- 模块定义：export，可同时存在接口与默认接口
  - export命令可以出现在模块任何位置，只要在顶层，如果出现在块级作用域会报错 
  - export {变量名}，定义模块接口：
     - 报错：export 1;/ var m = 1;export m;/function f() {};export f;
     - 正确：export var m = 1;/var m = 1;export {m};/var n = 1;export {n as m};/export function f() {};/function f() {};export {f};
  - export default 变量名，定义模块默认接口，输出一个default变量，仅一个默认输出
     - 报错：export default var a = 1;
     - 正确：export var a = 1;/var a = 1;export default a;/function f();export default f;/export default 1；
- 模块加载：import，可同时加载接口与默认接口
  - import {方法名} from '模块名' 调用模块
  - import 自定义名字 from '模块名' 调用采用默认接口的模块 
  - import '模块名' 立即执行模块
  - import命令会提升到模块的头部并首先执行
  - import命令不能使用表达式、变量和if语句
  - 整体加载：import * as 整体加载名 from 模块，通过整体加载名.方法名()进行调用
  - 同时加载：import _,{变量} from 模块名，同时加载默认方法和其他变量
- 模块继承：复合写法继承模块，在通过模块定义定义新模块
  - export与import复合写法：
    - export {变量名} form 模块名 = import {变量名} from 模块名 + exprot {变量名}
    - 默认接口写法：export {default} from 模块名；/ export { es6 as default } from 模块名
- 浏览器的模块加载：
  - < script type="module" src="foo.js" >type标志ES6，异步加载脚本
  - 脚本内顶层变量仅在内部有效，this关键字返回undefined，而不是window
- 循环加载：
  - CommonJS遇到循环加载，返回当前已经执行的部分的值，当调用未定义的变量会产生错误
  - ES6加载的变量都是动态其所在引用模块，只要引用存在，代码就能执行


## 7.内存泄漏
### 1.原因
- 全局变量：定义在全局对象window上的变量，在页面关闭前不会回收
```
function fn() {
  a = 'global variable'
}
fn()
```
- 闭包变量：闭包中的变量一直存在在内存中，未手动清除则不会回收
```
function fn () {
  var a = "I'm a";
  return function () {
    console.log(a);
  };
}
```
- 未清理的DOM元素引用：变量值为DOM元素，未手动清除该变量则不会回收
```
// 在对象中引用DOM
var elements = {
  btn: document.getElementById('btn'),
}
function doSomeThing() {
  elements.btn.click()
}

function removeBtn() {
  // 将body中的btn移除, 也就是移除 DOM树中的btn
  document.body.removeChild(document.getElementById('btn'))
  // 但是此时全局变量elements还是保留了对btn的引用, btn还是存在于内存中,不能被GC回收
}
```
- `定时器或回调`：回调或定时器引用DOM，未手动删除回调或定时器则DOM仍存在
```
// 定时器中引用DOM，需手动删除定时器与DOM
var serverData = loadData()
setInterval(function () {
  var renderer = document.getElementById('renderer')
  if (renderer) {
    renderer.innerHTML = JSON.stringify(serverData)
  }
}, 5000)

// DOM绑定事件监听回调，需removeEventListener
var btn = document.getElementById('btn')
function onClick(element) {
  element.innerHTMl = "I'm innerHTML"
}
btn.addEventListener('click', onClick)
```
- echart未清除：页面切换清除图例，但未清除内存中的实例
```
beforeDestroy () {
  this.chart.dispose()
}
```
- `插件生成DOM未回收`：手动销毁插件生成的DOM
```
// select组件使用Choices.js初始化生成DOM插入到父组件中，DOM未释放
<div id="app">
  <button v-if="showChoices" @click="hide">Hide</button>
  <button v-if="!showChoices" @click="show">Show</button>
  <div v-if="showChoices">
    <select id="choices-single-default"></select>
  </div>
</div>
```


### 2.预防
- weakset/weakmap：对值的引用为弱引用，不计入垃圾回收机制
```
// 对element的引用为弱引用，不计入垃圾回收机制
const wm = new WeakMap()
const element = document.getElementById('example')
vm.set(element, 'something')
vm.get(element)

//
const listener = new WeakMap()
listener.set(ele, handler)
ele.addEventListener('click', listener.get(ele), false)
```


### 3.排查
- 性能（performance）
  - 启动录制：录制操作过程中的内存变化
  - JS Heap：内存变化，垃圾回收时断崖式下降，持续上升大概率内存泄漏
- 内存（memory）
  - 生成快照：生成不同操作前后的内存快照，对比排查内存情况
  - 构造函数（Constructor） — 占用内存的资源类型
  - 距离（Distance） — 当前对象到根的引用层级距离
  - 卷影大小（Shallow Size） — 对象所占内存（不包含内部引用的其它对象所占的内存）
  - 保留大小（Retained Size） — 对象所占总内存（包含内部引用的其它对象所占的内存）
  - 新增（New）— 新增内存量
  - 已删除（Deleted）— 已删除内存量
  - 增量（Delta）— 正值代表内存增加
  - (closure) — 代表闭包，若为增量则说明内存溢出
- 性能检测器（Performance monitor）：实时检测内存变化


## 10.路由设计思路
- 全局路由：
  - 访问路径缓存：不符合权限需跳转到登录界面，先缓存路由，登录后获取缓存路由进行跳转
  - 跨域代理设置：开发环境下在vue项目未开启CORS服务的情况下，使用proxytable设置跨域时的代理服务器
  - 相同路由权限判定：点击相同路由，判断localStorage中的权限值进行重定向，分别返回不同的页面
  - 路由设置：vue-router模式下子路径不需要/，/表示根目录，router-link中应该写全路径
- 菜单路由：
  - 菜单绑定：el-menu设置router属性，在各导航栏即可使用index指定对应路由
  - 父子路由影响：子路由切换影响父路由导航栏active：将active绑定为父路径


## 11.项目构建思路
- 页面划分准则
  - 拆分为不同子组件，划清子组件之间数据的交互并在父组件对该类数据进行操作与流动
  - 不同功能弹窗设置为不同子组件，父组件控制子组件的显隐以及数据来源
- api：作为ts类型定义的同时形成接口文档
- 父子传值
  - prop：规划父组件传值，明确prop
  - emit：规划子组件触发事件
- state数据划分
  - 数据列表：接口调用函数进行赋值
  - 接口参数对象：便于参数修改以及接口调用管理
  - 传值对象：作为子组件所需的传值属性的集合
  - 显隐属性：用于控制功能弹窗的显隐
- 组件函数封装准则
  - 接口调用函数get：接口调用根据返回值进行state赋值
  - 组件触发函数handle：绑定click、change等操作函数
  - 功能封装函数：非上述函数的功能函数
