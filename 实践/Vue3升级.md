# Vue3升级

标签（空格分隔）： 实践

---
## 1.gogocode
### 1.简介及使用
1. 简介：阿里妈妈开发的一个vue2代码转vue3代码的工具
2. 项目地址: https://github.com/thx/gogocode
3. 使用文档：https://gogocode.io/zh/docs/vue/vue2-to-vue3
4. 项目版本：v1.0.45
5. 使用流程：
```
npm install gogocode-cli -g  // 安装工具

gogocode -s ./src -t gogocode-plugin-vue -o ./src-out  // 升级代码

gogocode -s package.json -t gogocode-plugin-vue -o package.json  // 升级配置文件

npm install  // 安装升级
```
> *1.可能存在**转化停顿情况**，需要排查转换卡住的文件并暂时删除从而转化成功；
2.代码转换将根据gogocode内置的风格进行转换，可能**改变原有代码的代码风格**；
3.同时因为gogocode仍处于迭代过程中，**不同版本会造成升级的效果有差异**；*



### 2.主要语法转换
<table>
    <tr><th>语法转换</th><th style="text-align:center">优点</th><th style="text-align:center">缺陷</th></tr>
    <tr><td>v-model</td>
        <td style="text-align:center">父组件绑定:<br>v-model => v-model:value<br>子组件触发:<br>input => update:value</td>
        <td style="text-align:center">-</td></tr>
    <tr><td>生命周期</td>
        <td style="text-align:center">beforeDestroy => beforeUnmount<br>destroyed  => unmounted</td>
        <td style="text-align:center">-</td></tr>
    <tr><td>\$listener</td>
        <td style="text-align:center">v-bind="\$listener" => v-bind="\$attrs"</td>
        <td style="text-align:center">-</td></tr>
    <tr><td>事件API</td>
        <td style="text-align:center">生成代理文件代理事件API并引入<br>Bus.\$emit('') => \$emit(Bus,'')</td>
        <td style="text-align:center">子组件触发事件错误转换<br>this.\$emit('') => \$emit(this,'')</td></tr>
    <tr><td>emit选项</td>
        <td style="text-align:center">子组件触发事件写入选项中</td>
        <td style="text-align:center">事件API事件也写入选项中</td></tr>
    <tr><td>异步组件</td>
        <td style="text-align:center">() => import('')  转化<br> Vue.defineAsyncComponent(() => import(''))</td>
        <td style="text-align:center">仍需手动引入defineAsyncComponent<br>并去掉Vue
</td></tr>
    <tr><td>slot插槽</td>
        <td style="text-align:center">slot="name" => v-slot:name</td>
        <td style="text-align:center">slot-scope未转换</td></tr>

</table>


### 3.其他配置转换
- 启动文件index.js: 结合gogocode以及手动修改
``` 
// 创建Vue实例移动到头部
window.$vueApp = Vue.createApp(App)
window.$vueApp.use(router) // router在挂载之前，否则无法渲染
window.$vueApp.mount('#app')
window.$vueApp.use(store)

// 兼容就this.$http调用方法
// Vue2语法转化：Vue.use(v => { v.prototype.$http = axios })
window.$vueApp.config.globalProperties.$http = axios


window.$vueApp.use(less)
window.$vueApp.use(Antd)
window.$vueApp.use(Contextmenu)
window.$vueApp.component('draggable', draggable)

window.moment = moment
```
- router：结合gogocode以及手动修改
```
// 404页面路径调整
path: '*' => path: '/:catchAll(.*)'
// 与 2.x 不同，loader 函数不再接收 resolve 和 reject 参数
component: (resolve) => {
    require.ensure([], () => {
        return resolve(require('@views/login/Index.vue'))
    }, 'login')
}
=> 
component: () => import('@views/login/Index.vue'),
```
> 总结：
1.使用该工具能够减少一定的升级工作量，但是同时会产生其他一些未知的隐患，使用过程中需要对转换后的代码进行排查。目前针对大型项目还是优先手动升级
2.若不采用该工具进行一键转换，该工具有单独可取之处，比如：自动生成的事件API代理文件以及package.json变更等（该文件执行命令转换后仍有问题，将在vue3变更中进一步说明）




## 2.第三方库
- vue-contextmenu更换
1. 新插件：@imengyu/vue3-context-menu
2. 项目地址：https://github.com/imengyu/vue3-context-menu
3. 个性化调整：调整样式
4. 替换方法：
```
package.json: vue-contextmenujs => @imengyu/vue3-context-menu
index.js: import Contextmenu from '@imengyu/vue3-context-menu'
``` 
> 可能问题：右键菜单事件失效
可能原因：右键菜单事件原来绑定在a-tabs。antd版本升级导致失效，需更换绑定对象为div


- vuedraggable升级
1. 新特性：模板中只允许存在一个根节点
2. 适配操作：控件渲染需将空值情况下的渲染移出draggable，将空值渲染设置为绝对定位
3. 特殊：tabboard、panel、functionbar、card组件需添加最小高度样式以撑开控件
4. 多根节点报错：control-render外部包一层div，规避多节点错误
5. 警告：draggable中需要绑定item-key
```
<draggable v-model="myArray">
    <transition-group name="fade">
        <div v-for="element in myArray" :key="element.id">
            {{element.name}}
        </div>
    </transition-group>
</draggable>
=>
<draggable v-model="myArray" tag="transition-group" :component-data="{name:'fade'}">
  <template #item="{element}">
    <div>{{element.name}}</div>
  </template>
</draggable>
```
- monaco第三方库
1. 根源问题：monaco不支持Vue3数据，monaco获取数据过程导致堆栈溢出，UI卡死
2. 其他问题：a-form-item的name将作为组件根节点的id属性：将id赋值给name属性
3. 解决：将editor由响应式改为局部变量
4. 隐患：需去除settimeout优先赋值editor
- 拖拽目录树
1. 节点获取vue实例：_ _ vue _ _ => __vueParentComponent
2. 节点获取vue实例的属性：vue实例获取属性 => vue实例属性data、prop分开获取
3. 获取节点：lastChild => lastElementChild，前者获取文本节点


## 3.vue3变更
> 在上篇gogocode中的package.json的基础上的进一步修改
"vue-loader": "^16.0.0"

- vue-loader升级
```
// 切换获取vue-loader操作
- const VueLoaderPlugin = require('vue-loader/lib/plugin')
+ const { VueLoaderPlugin } = require('vue-loader')
```
- 编译警告：antd样式修改 /deep/ => :deep()
- 插槽语法改变：
  - slot、slot-scope => v-slot
  - scopedSlots替换为slots
- DynamicOptionsConfig：特殊slot结构改造
- components\Relationship.vue报错：Cannot read property 'created' of undefined
  - 解决：v-loading指令不存在
  - 备注：vue2中同样报错，不存在该自定义指令
- View.vue
```
// 无法获取到components选项
this.$options.components.ContentView = deepCopy(this.view)
// 采用component组件
this.component = deepCopy(this.view)
<component :is="component"></component> 
```
- ActionPropTools.vue
```
curComponent() {
    let type = this.actiontype || 'return'
    let getSrcFn = actionComponentMap[type]  //获取异步组件
    /* let targetCmp = () => {  return getSrcFn() } */
    let ref = type + 'Ref'
    return [targetCmp, ref] => return [getSrcFn, ref]
},
```
- 对象属性监听：
```
// 父组件
beforeUnmount() {
   this.$store.commit('conui/clearTabCache', this.tabName)
}
// 子组件
tabCache() {  
    let tabCache = this.$store.getters['conui/getTabCache']
    return tabCache[this.tabName]
},
beforeUnmount() {  // tabCache未更新，仍可访问
    $off(Bus, this.tabCache.eventBusNames.updateUiConfigData)
}

// store设置
['conui/setTabCache'](state, data) {  // 非响应式
    if (!state.tabCache[data.tabName]) {
      state.tabCache[data.tabName] = {}
    }
}
['conui/clearTabCache'](state, data) {  // 清空对象属性
    if (data && state.tabCache[data]) {
      state.tabCache[data] = null
    }
}
['conui/getTabCache']: (state) => state.tabCache  //  属性更新不响应式触发

// 解决方法，父组件清空函数在销毁后生命周期执行
unmounted() {
   this.$store.commit('conui/clearTabCache', this.tabName)
}
```
- sotherTools\databaseQuery\components\View.vue
```
// Flycode调试器新建实例
<div style="height: 100%">
</div>
mounted(){
	if (this.view) {
        let comp = this.view
        let model = new Vue({
            store,
			render: h => h(comp)
        })
        if (model.$el) {
		    this.$el.append(model.$el)
        } else {
			let vm = model.$mount()
			this.$el.append(vm.$el)
        }
	}
}

=>

<div style="height: 100%">
    <component :is="view"></component>
</div>
```

- ui-preview\index.js：UI预览新建实例并挂载，隐患：vue2中不同Vue实例共用全局定义，vue3不同Vue实例需分别定义


## 4.antd用法变化
- v-model升级
  - v-model:checked: CheckableTag、Checkbox、Switch
  - v-model:value: Radio、Mentions、CheckboxGroup、Rate、DatePicker、Select
  - v-model:visible: Tag、Popconfirm、Popove、Tooltip、Moda、Dropdown
  - v-model:activeKey: Collaps、Tabs
  - v-model:current: Steps
  - v-model:selectedKeys: Menu
- 版本迁移文档未写明变更：
  - a-select：optionFilterProp="children" => optionFilterProp="label"
  - a-select：`onSearch` `showSearch` common\IdeSelectMul.vue中showsearch默认值为true
  - a-form-item：prop => name
  - a-modal：dialogClass => class
  - a-tree：v-model:value => v-model:expandedKeys、
  - a-tree: 'e.selectedNodes[0].data.props.dataRef' => 'e.selectedNodes[0].props.dataRef'
  - a-table：slot中指定为record，不能为row
- a-form报错：
  - a-form-item要在a-form标签内部
  - a-form-model更换为a-form
  - a-form的validate事件重构，callback => Promise
- a-form自定义校验规则
  - 在不修改的情况下功能正常运行，修改情况下多处需变更
  - 修改：callback => Promise
  - 原有功能隐患：同时自定义规则与message存在情况下，message优先级更高
```
let verifyFilekey = async (rule, value) => {  // 加上async，否则依旧warning
      let isRepeat = true
      if (!value) {
        return Promise.reject(new Error('请输入文件key！'))
      } else {
        await this.$http                     // 使用await，否则无法return reject
          .post(`${API_ROOT}/uiserv/protocol/file/isExistKey`, {
            filekey: value,
            fileid: this.configData.fileid,
          })
          .then((res) => {
          })
          .catch((error) => {
            isRepeat = false
            errorMessage(error, this)
          })
        if(isRepeat) return Promise.resolve()
        else return Promise.reject(new Error('文件key重复！'))
      }
    }
```
- a-form的校验空值无法判断数字
  - 显示绑定数字为两处，隐式绑定数字不明，存在隐患
  - 两种方案：对值进行转换(改动位置多，不利)，自定义校验规则（改动位置少，有利）
```
let verifyFileType = async (rule, value) => {
      if (!value)  return Promise.reject(new Error('文件类型不能为空'))
      else return Promise.resolve()
}
```
- 图标升级
  - 整体：设置全局组件a-icon，在原有基础上仅需要批量修改type即可实现绝大部分icon迁移
  - 自定义组件select-menu、tools-button：icon=''
  - 导航栏、事件图标icon设置：icon:''
  - a-button：icon设置改为内置template，需手动修改
     - controlAttr\settingSpecial\AttrExtraparams.vue
     - controlAttr\settingTemplates\DynamicOptionsConfig.vue
     - controlConfiguration\view\SectionList.vue
  - question-circle-o => QuestionCircleOutlined  与下一个图标相同，存疑
  - controlConfiguration\description\textbutton.js: icon:'search'
  - src\views\home\menu\Index.vue：icon:''



## 5.antd样式修改
- a-table
  - 问题：背景颜色由透明转为白色
  - 解决：设置背景颜色为透明
  - 问题：固定单元格背景由透明转为白色
  - 解决：设置背景颜色为透明
- a-input
  - 举例：登录账号密码样式
  - 问题1：padding由内部转为外部，但主题颜色设置在内部，将导致外部padding范围无主题色
  - 问题2：内部设置高度且为border-box，padding转为外部后，padding使整个控件变大
  - 解决：调整内部height并将主题色设置在外部
```
// 自定义样式48px，实际效果58px
// 屏蔽内部padding，内部为border-box模式
.ant-input-affix-wrapper > input.ant-input {
    padding: 0;
    border: none;
    outline: none;
}
// 转化为外部padding，综合导致整个控件高度增加
.ant-input-affix-wrapper {
    padding: 4px 11px;
}
```
- a-select
  - 举例：登录环境选择样式
  - 问题：新版antd中样式选择器变更
  - 解决：修改自定义样式中的样式选择器
```
.ant-select-selection => ant-select-selector
```
- a-row
  - 问题：布局样式变化，a-form-item由上下布局 => 左右布局,如实体基本信息
  - 解决：a-form-item加上样式style="display:block"
  - 原来：a-row：display：block，a-col：float：left
  - 现在：a-row：display：flex，~~a-col：float：left~~
- a-textarea
  - 问题：没有拖拽标识，如实体实体属性
  - 解决：选择：设置auto-size则无标识，不设置则有标识但无法限制行数


## 6.遗留问题
- 数据分析：跨域报错
- 警告：a-select的mode不支持'default'以及''，划分多选与非多选选择框
- 警告：draggable中需要绑定item-key，根据具体绑定元素的属性值作为key
- 警告：reactive包含组件造成性能消耗，shallowRef创建一个 ref ，将会追踪它的 .value 更改操作，但是并不会对变更后的 .value 做响应式代理转换


## 7.图标详解
  - plus-circle => PlusCircleOutlined
  - delete => DeleteOutlined
  - edit => EditOutlined
  - check => CheckOutlined
  - notification => NotificationOutlined
  - unlock => UnlockOutlined
  - lock => LockOutlined
  - close-circle => CloseCircleOutlined
  - drag => DragOutlined
  - save => SaveOutlined 
  - upload => UploadOutlined 
  - question-circle-o => QuestionCircleOutlined  与下一个图标相同，存疑
  - question-circle => QuestionCircleOutlined
  - desktop => DesktopOutlined 
  - mobile => MobileOutlined 
  - wechat => WechatOutlined 
  - check-circle => CheckCircleOutlined 
  - plus => PlusOutlined 
  - down => DownOutlined 
  - up => UpOutlined 
  - right => RightOutlined 
  - copy => CopyOutlined 
  - copyright => CopyrightOutlined 
  - file-text => FileTextOutlined 
  - file-add => FileAddOutlined 
  - 事件图标：icon: 'database' => DatabaseOutlined 
  - arrow-down => ArrowDownOutlined 
  - arrow-right => ArrowRightOutlined 
  - arrow-left => ArrowLeftOutlined 
  - close-circle => CloseCircleOutlined 
  - reload => ReloadOutlined 
  - menu => MenuOutlined 
  - column-height => ColumnHeightOutlined 
  - column-width => ColumnWidthOutlined 
  - select => SelectOutlined 
  - file-image => FileImageOutlined 
  - scan => ScanOutlined 
  - play-circle => PlayCircleOutlined 
  - caret-up => CaretUpOutlined 
  - folder-open => FolderOpenOutlined 
  - appstore => AppstoreOutlined 
  - robot => RobotOutlined 
  - user => UserOutlined 
  - file => FileOutlined 
  - form => FormOutlined 
  - 特殊：:type="icon || 'plus'" => select-menu组件的icon属性
  - 特殊：:type="icon" => tools-button组件的icon属性
  - 特殊：:type="item.icon" => src\views\home\menu\Index.vue:gradMenu的icon属性
  - 特殊：:type="tab.icon" =>  暂时无影响
  - more => MoreOutlined 
  - logout => LogoutOutlined 
  - setting => SettingFilled 
  - swap => SwapOutlined 
  - pushpin => PushpinOutlined 
  - plus-square => PlusSquareFilled 
  - import => ImportOutlined 
  - export => ExportOutlined 
  - tag => TagOutlined 
  - ellipsis => EllipsisOutlined 
  - folder => FolderOutlined 
  - search => SearchOutlined 
  - file-add => FileAddOutlined 
  - play-square => PlaySquareOutlined 
  - file-done => FileDoneOutlined 
  - reload => ReloadOutlined 
  - link => LinkOutlined 
  - profile => ProfileOutlined 
  - unordered-list => UnorderedListOutlined
  - folder-open => FolderOpenOutlined 
  - global => GlobalOutlined 
  - build => BuildOutlined
  - calculator => CalculatorOutlined
  - database => DatabaseOutlined 
  - upload => UploadOutlined
  - tag => TagOutlined
  - bug => BugOutlined
  - funnel-plot => FunnelPlotOutlined
  - pull-request => PullRequestOutlined
  - hdd => HddOutlined




