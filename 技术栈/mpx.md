# mpx

标签（空格分隔）： 技术栈

---
## 1.快速开始
- 安装：`npm i`
- 运行：`npm run watch`
- 项目：微信开发工具打开dist下对应项目目录
- 调试：微信开发工具点击编译调试结果


## 2.基础
- src/app.mpx：
  - js调用creatApp注册小程序
  - style区块定义全局格式
  - json区块：对应app.json
- src/pages/index.mpx：
  - js调用createPage注册页面
  - json区块：`"usingComponents": { "list": "../components/list" } // 引入组件`
- src/components/component.mpx：
  - js调用createComponent注册组件
  - json区块：`"component": true`
- 单文件开发：类似于vue的开发模式，对应原生小程序的四个区块
- 与vue的区别：一切绑定均需{{ 选项 }}
- CSS处理：src复用/import复用
- 类名样式绑定：
  - 类名绑定wx:class：
     - 对象语法：传入对象，key代表类名、value代表是否生效
     - 数组语法：传入数组，字符串代表类名
  - 样式绑定wx:style：
     - 对象语法：横杠样式名改用驼峰写法
     - 数组语法：引入对象数组
- 条件渲染：wx:if、elif、else;wx:show
- 列表渲染：wx:for，item默认为当前项；wx:key="id"；computed二次处理列表
- 事件处理：bindtap/bindchange绑定相应事件，参数个数类型没有限制，可传类型：
  - 字面量
  - 组件数据
  - for中的item与index
  - 传递\$event作为当前事件对象
- 双向绑定：wx:model
  - 更改双向绑定监听事件及数据属性
     - wx:model-event：更改监听事件
     - wx:model-prop：更改监听属性
  - 更改双向绑定事件数据路径wx:model-value-path：双向绑定默认使用event.detail.value作为用户输入来更新组件数据；某些组件数据存储在event.detail中时，通过wx:model-value-path设定数据获取路径，以event.detail为根路径，设置为空时代表在根路径获取数据
  - 双向绑定过滤：wx:model-filter="trim"内置过滤器对用户输入过滤
- 自定义组件
  - 动态组件：<component is="{{current}}"></component>
  - slot：
     - 组件内部定义：<slot name="slot1"></slot>
     - 组件外部定义：<view slot="slot1">我是slot1中的内容</view>
- 获取组件节点
  - 绑定元素：wx:ref="content"
  - 获取节点：this.$refs.content
  - 若绑定元素为wx:for组件时，返回的是一个组件实例数组
- store：
  - 组件外定义：createStore创建类似于vue的store，export default store
  - 组件内引用：import store
  - 组件内调用：store.state、store.commit


