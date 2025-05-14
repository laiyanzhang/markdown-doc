# CSS&组件库

标签（空格分隔）： 实践

---

## 1.CSS异常
- 子元素为空时显示空白
  - 解决：父元素font-size：0
- 页面多余空白
  - 解决：index.html设置margin：0
  - 解决：两个行内元素设置vertical-align：top
- 外层div没有设置padding，会将内层margin-top的值赋值给外层的margin-top
- 控件未对齐
  - 解决：两个inline-block设置vertical-align:top时文字对齐
- 组件未渲染
  - 解决：属性中绑定无法识别属性造成组件未渲染
- transform失效
  - 解决：元素必须为盒模型，设置display:inline-block/block
- font-size失效
  - 解决：浏览器限制最小为12px，可使用transform:scale(0.8)进行缩小
- 绝对定位图标被遮挡
  - 解决：在父级元素设置overflow:visible
- flex子元素超出盒子
  - 问题：父元素flex:1但不设置宽度，子元素内容过长时会将元素扩大至超出盒子
  - 解决：父元素的width/height设置为0
- 图片无法渲染
  - 问题：img的src属性绑定为模板字符串，渲染时无法转换成图片实际地址
  - 解决：img的src属性绑定为require函数，渲染时函数返回图片实际地址


## 2.element-ui异常
- button无法居中：
  - 问题：设置两个按钮时第二个按钮无法居中
  - 解决：需将按钮作为div子元素
- popover适应Vue3：
  - 问题：slot属性废弃，原slot="reference"用于绑定控件
  - 解决：`<template #reference> <i class="el-icon-question"></i> </template>`
- popover设置气泡属性：
  - 问题：popper-class写入class无效
  - 解决：气泡单独作为body子节点，class应在全局定义

## 3.antd异常
- a-select：可选项异常
  - 问题：清空选中项却未清空搜索值，造成可选项未及时更新
  - 解决：在清空选中项处理函数中手动更新可选项
- a-tree：无法正常折叠
  - 问题：设置auto-expand-parent="true"时，点击子节点的情况下无法折叠父节点
  - 解决：true时折叠父节点的同时需要同时清空子节点，false时则只需清除父节点
- a-tree：渲染为空
  - 问题：当数据来源为异步加载时，树开始渲染为空
  - 解决：树v-if绑定数据长度，当有数据的时候才渲染
- a-tree：default-expand-all失效
  - 问题：异步获取数据，初始化时为空导致无法展开
  - 解决：树v-if绑定数据长度，当有数据的时候才渲染
- a-select/a-tree-select：placeholder失效
  - 问题：v-model绑定的value设置‘’
  - 解决：v-model绑定的value设置为undefined
- a-popover：设置弹窗样式
  - 问题：直接赋值class不生效
  - 解决：overlayClassName="myPopClass"绑定对应的class，需要在根作用域定义样式
- a-tooltip：设置文本提示框样式
  - 问题：直接赋值class不生效
  - 解决：overlayClassName="myTooltipClass"绑定对应的class，需要在根作用域定义样式
- a-modal：关闭按钮不奏效
  - 问题：v-model绑定的值为prop时，触发事件直接更新prop导致报错
  - 解决：v-model改为v-bind
- a-select/a-tree-select：未按中文搜索
  - 问题：在设置自定义字段的情况下，未按照正确值进行搜索
  - 解决：optionFilterProp/treeNodeFilterProp绑定对应的自定义字段
- a-select-option: 警告未引入
  - 问题：按需引入失败
  - 解决：`VueApp.component('a-select-option', Select.Option)`
- a-modal：弹窗内嵌表单，再次打开恢复原始值（destroyOnClose + resetFields）
  - destroyOnClose：关闭弹窗时销毁子元素
  1、需要消除的值在modal层做处理（修改值），关闭弹窗时不会消除当前的值
  2、需要消除的值在modal下的子组件中做处理（修改值），关闭弹窗时会消除当前的值
  - formRef.value.resetFields()：重置表单内容以及校验规则
  1、重置的内容是表单mounted周期结束的值，第一次编辑时传入值后再执行mounted
  2、再次编辑时子元素是销毁后新建的，因此重新执行mounted周期，关闭弹窗时重置内容重置的是再次编辑时传入值
- a-divider：设置边距
  - 问题：单独使用margin、padding无法设置分割线距离两边的边距
  - 解决：`width: auto; margin: 0 12px; min-width: 0;`
- a-drawer：自定义挂载父节点不成功
  - 问题：drawer与父节点样式不正确
  - 解决：drawer：`position: absolute`；父节点：`position: relative`


