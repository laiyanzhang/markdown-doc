# aPaaS

标签（空格分隔）： 工作文档

---


## 1.UIFlycode
- Page表单对象
  - 表单属性：***（未实现）***
     - status：表单状态（编辑、查看、新增）
     - title：表单标题
     - params：表单参数：新建表单时传入的参数
  - 表单控制：
     - statusIs(status)：检测当前状态
     - setTitle(newTitle)：设置新标题
     - getLinkParams()：获取附带参数
     - applyLayout()：FLycode改变之后刷新布局
  - 表单值存取：
     - getValue(key)/getValues(keys)：获取页面绑定值
     - setValue(key, newValue)/setValues(keyValues)：设置页面绑定值
     - getCtrl(ctrlName)/getArrayCtrl(ctrlName)/getPickerCtrl(ctrlName)：获取指定控件
  - 表单事件调用：
     - runEvent(eventName, params)：执行当前表单指定名字事件
     - callEvent(eventKey, params)：调用内存中所有配置表单符合key的事件
     - constraintCheck()：校验表单数据合法性
     - openProgress(tip)/closeProgress()：开/关进度条
     - setLoadStatus(statusType)：设置表单数据加载的失败状态设置
     - alert(type, msg, choices)：弹出框的类型、信息以及可操作性的选项
     - linkToPage(pageName,param,mode,size)：跳转到对应表单***（web不支持该功能，手机端不支持mode，size属性）***
     - linkToPageCode(pageCode, param, mode, size)：手机端跳转到指定code的表单
     - link(pageCode, param, mode, size)：电脑端跳转到指定code的表单
     - returnToPage(pageName)：跳转回到目标页面，***web端仅支持return()返回上一级页面***
     - returnToPageCode(pageCode)：返回到指定code的表单***（web不支持）***
     - returnToPageCount(count)：返回到比当前表单高count级的表单***（web不支持）***
- FlowPage流程表单：
  - 流程属性：***（未实现）***
     - currentInfo：流程详细信息
  - 流程操作：
     - currentInfo()：返回当前流程表单信息，返回空则表示当前未在流程处理过程中
     - preaddFlow()：发起流程***（未实现）***
     - handleFlow()：获取表单传参***（未实现）***
- Ctrl控件对象
  - 获取控件：Page.getCtrl('控件名')获取到控件对象，对控件对象进行后续操作
  - 控件属性：
     - code：控件的code
     - value：控件的值
     - floatValue：数值型控件的值
     - hidden/check/readonly/required/color/bgcolor：控件属性
  - 控件方法：
     - setProperty(propertyName, newValue, groupIndex)：设置控件属性
     - getProperty(propertyName, groupIndex)：获取控件属性
     - setValue(value, setter)：设置控件值
     - getValue(getter)：获取控件值
     - setErrorMsg(msg)：提供校验错误信息
     - clearErrorMsg()：清除错误信息
     - validate()：触发控件校验***（web端只实现了附件控件的支持，手机端尚不支持）***
     - triggerEvent(triggerName)：触发控件EventTrigger所绑定的事件
  - 数组控件对象：Page.getArrayCtrl('控件名')获取控件对象
  - 数组控件属性：
     - checkNumber：勾选的数量
     - pageIndex：分页索引
     - rowNumber：数据总行数
  - 数组控件方法：
     - getRowAtIndexPath(indexPath)/getAllRows()/getCheckedRows()/getFocusRow()/getRowAtIndexes(index)：获取行控件
     - getColByName(name)：获取列控件
     - getCheckedRowsIndexPath()/getFocusRowIndexPath()/getIndexesInScope(scope)：获取行控件的indexPath
     - getData(arrayCtrlGetter)/getInIndexes(indexes,getter,isExhaustive)/getInScope(scope，getter，isExhaustive)/getInScopeReverse(scope，getter，isExhaustive)：获取数据
     - samecheck(checkData, keys, rule, checkFunction)：检测重复数据
     - getSerializedValue(scope, getter)/setSerializedValue(value, setter)：序列化数据
     - 旧版API：
         - reloadRows(rowData, indexPaths, setter)：刷新多个行数据
         - reloadSections(sectionData, sectionIndexes, setter)：刷新多个分组数据
         - insertRows(rowData, indexPath, setter)：指定位置连续插入若干行数据
         - insertSections(sectionData,sectionIndex,setter)：指定位置连续插入若干组数据
         - deleteRows(indexPaths)：删除指定行数据
         - deleteSections(sectionIndexes)：删除指定组数据
         - moveRow(fromInedxPath, toIndexPath)：指定行交换
         - updateList(data, setter)：重新加载所有分组数据
         - updateListWithOperations(operations)：批量修改分组或行数据
     - 新版API：
         - reload(rowsData, setter)：重新设置全部数据
         - insert(rowsData, index, setter)：插入多条数据到指定位置
         - append(rowsData, pos, setter)：插入多条数据到头部或尾部
         - update(rowsData, indexes, setter)：更新多条数据到指定位置
         - delete(indexes)：删除指定位置的数据
         - deleteInScope(scope)：删除指定范围的数据
         - deleteInScopeReverse(scope)：删除指定范围外的数据
  - 数组型控件-行控件：
     - 属性：checked：获取勾选状态
     - 方法：
         - getCtrl(ctrlName)：获取行控件的子控件
         - getPickerCtrl(ctrlName)：获取行控件的选项类控件
         - getValue(key)：获取行控件的值
         - setValue(key, newValue)：设置行控件的值
         - getValues(keys)：获取行控件多个绑定的值
  - 数组型控件-列控件：
     - 属性：hidden/required/readonly：隐藏/必填/只读
     - 方法：sum()/avg()/max()/min()：数据统计（仅editortable支持）
  - 选项控件对象：Page.getPickerCtrl('控件名')获取控件对象
  - 选项控件属性：option：控件选项值***（未实现）***
  - 选项控件方法：setLoadStatus/getOptions/setOptions/clearOptions()***（未实现）***
- Model模型对象
  - 对业务模型进行操作：Model对象
  - submitData(logicName, input, fileInfo, success, fail, completion)：提交数据
  - requestServer(logicName, input, success, fail, completion)：请求服务端逻辑
  - runLogicScript(name, input, success, fail, completion)：执行本地flycode逻辑
- semaphore信号量
  - wait(functions, completion)：等待任务完成执行
  - sign()：释放信号量
- SQLiteDB离线模型对象***（IOS支持，Android不支持）***
  - exceQuery(sql,success,fail,completion)：执行select的sql语句
  - exceModify(sql,success,fail,completion)：执行insert、update、delete或replace的sql语句
  - insertObject(bizObject, success, fail, completion)：插入一个或多个业务对象到数据库
  - updateObject(bizObject, success, fail, completion)：更新一个或多个业务对象到数据库
  - saveObject(bizObject, success, fail, completion)：新增或更新一个或多个业务对象到数据库
  - deleteObject(bizObject, success, fail, completion)：删除对应主键的对象
- System系统对象
  - user()：用户信息
  - context()：登录信息
  - uniqueid()：获取uniqueid
  - date()：获取服务端时间
  - functionCodes()：获取功能权限
  - functionCheck(functionCodes)：功能权限检测
  - console(msg)：日志输出
  - ideLog(p, title)：把日志发送到IDE
  - navinfo(key)：根据key获取对应导航目录信息
- Location定位对象
  - latestLocation()：获取最近一次定位信息
  - currentLocation(success, fail, completion)：获取当前位置信息
  - metersDistanceBetweenMapPoints(locations)：计算最近一次定位与固定位置的直线距离
  - metersCurrentBetweenMapPoints(points,success,fail,completion)：计算当前定位与固定位置直线距离


## 2.Flycode
- BO实例化业务对象
- DB数据库操作
- Date日期对象
- FLY工具库
- NOW当前时间
- SESSION会话信息
- String字符串处理
- TX事务控制
- ACCOUNT平台账号模块
- CONFIG系统配置
- MSG消息模块
- PINYIN拼音转换模块
- WORKFLOW工作流模块
- CACHE缓存模块操作
- OPENDATA模块



## 3.开发者文档
- aPaaS：介绍主要功能及跳转链接
- 快速入门：介绍该章主要内容及跳转链接
  - 安装升级
  - 系统登录
  - 创建实体
  - 准备领域接口
  - 配置表单
  - 导航与权限
- 表单配置：介绍该章主要内容及跳转链接
  - 主界面（原：控件）
     - 基础信息：
         - 控件分类
         - 控件通用属性（原：控件通用属性+枚举值）
         - 控件通用事件
         - 控件数据存取component摘要
         - 图标库
         - 时间表达式
     - 布局
     - 展示型控件
     - 输入型控件
     - 选择型控件
     - 数组型控件
     - 其他控件
     - 业务控件
     - 自定义控件
     - AI控件
  - 事件管理（原：事件行为）
     - 基础信息
         - 事件分类
         - 事件通用属性
     - 数据处理
     - 表单行为
  - 表单管理
  - 弹窗管理
  - 协议查看（原：UI协议）
- 调试工具：介绍该章主要内容及对应跳转链接
  - Android
  - IOS
- 异常排查及修复指导：介绍该章主要内容及对应跳转链接
  - web端-V9.1.x版本关于弹窗布局配置实践
  - 地址丢失调度修复
  - 手机端极光推送失效原因排查
  - 手机端消息推送验证及处理方案
- 其他文档：介绍该章主要内容及对应跳转链接
  - 项目部署相关：
     - web端-登录界面企业账户控制
     - 自动化部署使用手册
  - 前端：
     - 分布图支持自定义颜色配置
     - web端-表格勾选项导出
     - 二开控件接入流程
     - 已办/待办页面可配置化的配置用例
     - 组织管理转配置
     - UI布局系统
  - 后端：
     - 新消息分类改造使用方法
     - 实验项目配置AI识别
     - 任务调度接口
  - 数据库：
     - 数据库读写分离
     - 分布式ID生成
     - 终端分布图地图更新操作流程
     - 文档模板管理
  - APP：
     - app从9.0升级9.1配置问题指导
     - 应用节点显示消息推送
- UIFlycode：介绍该章主要内容及对应跳转链接
  - SQLiteDB离线模型对象（原：离线模型对象）
  - Model模型对象（原：模型对象）
  - Ctrl控件对象：
     - 控件通用：简介
         - 属性
         - 函数
     - 数组型控件：简介（原：数组控件对象+UIFlycode函数）
         - 属性
         - 函数
         - 数据结构
     - 选项型控件：简介
         - 属性
         - 函数
  - Page表单对象：简介
     - 属性
     - 表单控制
     - 表单值存取
     - 表单事件调用
  - System系统对象
  - Location位置对象
- Flycode
- 行业组件


## 4.IDE项目
### 1.整体概念
- main文件夹负责建立electron窗口以及启动vue项目，同时做好vue项目的配置。
- login文件夹构建登录界面。
- router建立路由控制，仅为不同身份登录提供不同入口。登录采用token进行认证，采用sessionStorage与localStorage分别记录信息。
- api文件夹统一调配所有的调用地址，api中设置地址代名以及http头部信息，服务器ip地址由localStorage获得。
- store采用模块进行控制，不同模块对应着软件模块功能。
- util中定义全局使用的工具方法。
- view中构建主界面。
- home代表一级界面根据不同view进行展示。
- component代表二级界面，主要为不同的功能模块，内部代表三级界面为新建功能中的不同tab界面。
- 表单模块主界面构建的主体思想
  - 协议主干：控件属性、预览与协议绑定，拖动、编辑、删除等修改触发协议修改从而触发页面修改
  - 控件缓存：缓存当前控件，针对当前控件进行显示；当触发点击等情况下修改当前控件
  - 钩子函数：Bus绑定全局钩子函数，可在某页面触发当前事件从而修改其他页面
  - 控件通用：将控件通用协议写入文件中，根据类型获取控件通用协议，根据具体协议修改对应控件

### 2.项目简单部分
- api：定义api对应接口路径以及默认的http头部信息等
- constants：定义全局的颜色、查看历史列表
- main
  - client：创建electron的窗口并进行配置
  - app：启动vue项目的主页
  - main.js：负责创建窗口并处理系统事件（包括系统更新）
  - index.js：配置vue项目
- router：分为不同登录模式的路由配置
- store/moudle：用于支持各项菜单功能的实现
  - authorityFunctionModule：功能管理&权限管理
  - businessFlow：流程管理
  - businessObject：业务实体
  - ***businessUI：表单管理***
  - catalogTree：目录树
  - gradMenu：工具模块的二级菜单
  - templateModule：模板管理
  - tools***：工具模块资源
- util：全局定义的工具方法
  - codeEditorLayoutManager：判断代码编辑器是否需要重新layout
- home：构建主页面，包括侧边栏以及内容界面
  - menu：构建不同的侧边栏，点击侧边栏后，根据侧边栏不同生成tab模块，通过tab模块生成显示不同页面
  - content：内容界面对tab界面进行统一管理，根据moudle进行显示并且可以对moudle进行移除，每个tab界面有对应的鼠标右击界面
     - userinfo：用户信息，在用户点击姓名框时弹出
     - view：表单界面
- login：构建登录界面


### 3.全局公共组件
- ***components/common：全局公共组件***
- IdeSelect：表单模块的表单类型选择控件
- SelectMenu：下拉菜单控件，如导出、添加
- ToolsButton：工具按钮控件，如删除、修改
- catalogTree：
  - 属性：将全部目录作为默认选中的节点，根目录为一级目录，其他目录为二级目录
  - 父节点：将树数据中的二级目录设为父节点
  - 初始化：获取目录并转为树数据的形式，当前结点设定为根节点，获取数据为父子结构
  - 删除功能：首先进行删除验证（根目录不能删除），验证成功后根据选中的目录进行删除
  - 增加功能：首先进行增加验证（只能一二级目录），验证成功弹出drawer进行填写目录信息
  - 编辑功能：首先进行编辑验证（根目录不能编辑），验证成功弹出drawer进行填写目录信息
  - 搜索功能：根据搜索条件过滤，将过滤后的树控件进行展开
  - generateList：根据data生成datalist
  - getParentKey：获取该结点的父节点的key


### 4.构建表单管理列表
- ***components/businessUi：构建表单管理列表***
- index：
  - 初始化
     - 获取表单类型：数据配合ide-select控件进行选择
     - 配置导入：设置导入路径以及头部信息
  - 切换目录/分页：根据目录/分页加载数据
  - 行选择改变：检测到行数据选中
  - 导入/导出：根据要求导入或导出json文件
  - 表单功能支持
     - 增加表单：非根目录情况下可增加，增加表单成功后将对应数据传入新tab当中，新tab添加进moudle，home根据moudle的不同生成对应的tab界面
     - 删除表单：验证是否选中及是否可删，确认删除后执行删除并重新加载数据
     - 编辑表单：点击表单标题，根据表单类型不同生成不同编辑页面
  - 功能支持
     - 查看：根据选择查看历史、协议或者复制表单
     - 编辑历史：判断表单是否在编辑，编辑则关闭再打开对应历史表单进行编辑
     - 查看协议：根据code获取对应协议并抽屉展示
     - 修改协议：修改协议内容，对部分固定内容重新覆盖
     - 复制表单：根据复制表单的信息提交并重新加载列表
  - 搜索目录功能支持：
     - 搜索：根据搜索条件加载表单
     - 选择时间段：将时间段转化为搜索条件
     - 加载表单：清空选中行，根据搜索条件获取表单列表
     - 跳转目录：点击所属目录跳转到对应目录
- add：新建表单drawer
  - 加载逻辑：根据不同页面类型产生不同选择框，根据搜索信息进行搜索
  - 页面类型：根据点击切换页面类型并且加载对应逻辑
  - 表单名称：表单名称同步表单标题，若名称曾修改则不同步补全
  - 加载原生组件：将全部组件的对应组件属性分别写入对象当中，存储为模块全局变量
  - 提交保存：将填写的相关信息保存
- ChangeCatalog：修改目录drawer
- Copy：复制drawer


### 5.构建表单编辑页面
- ***components/businessUi/edit：构建表单编辑页面***
- edit：主要界面，包括主界面+事件管理+表单管理+弹窗管理+协议查看
  - 初始化数据：获取页面参数、初始化自定义控件、初始化协议数据、改变编辑状态，获取功能点列表
  - 初始化事件：初始化事件名并将对应事件名写入tab缓存中
  - 切换tab：切换tab触发模块内的更新事件使协议更新



### 6.构建表单主界面
- ***components/businessUi/edit/ui：构建表单主页面***
- ui：包括controlList列表控件、controlPreview控件预览、controlAttr控件属性以及uiCanvas的页面预览
  - 初始化数据：获取tabName
  - 初始化事件：初始化更新事件以及删除、复制、粘贴控件事件
  - 设置协议数据：根据传入参数修改弹窗或主界面数据，传入的协议需转化为指定形式
  - UI预览显示/隐藏：默认隐藏，通过点击改变状态
  - 移动UI预览控件：改变协议
  - 插入控件到视图中：在当前结点后面插入控件并写入协议当中

#### 1.控件属性
- 路径：components\controlAttr
- 初始化数据：获取tabname
- 初始化事件：自身绑定属性改变事件（改变控件协议并重新绑定当前控件），页面预览以及控件预览中点击控件将触发该事件引起控件属性对应协议的改变
- 解决互斥属性：获取冲突属性并根据控件分别删除互斥属性
- 切换控件类型：更新控件协议，更新控件属性，更新控件属性数据
- 获取属性对象attrObj：根据控件类型获取控件属性通用协议（未配置）
- 划分控件属性：将控件属性通用协议按照属性类别进行划分，事件属性单独


#### 2.渲染样式与协议
- 路径：components\controlConfiguration
- view文件夹：各控件的渲染样式，在普遍样式基础下添加特定样式
- attribute文件夹：各控件的默认控件属性协议
- description文件夹：各控件的默认列表属性协议

#### 3.控件列表
- 路径：components\controlList
- 初始化数据：列表控件按照类型进行分类
- 初始化展开控件：根据设置进行控件展开
- 拖动控件：可以通过拖动或者添加的方式增加控件到视图中


#### 4.控件树
- 路径：components\ctrlPreviewTree
- ctrllcon.js：控件树中的图标
- utils.js：
  - 判断控件类型以及控件是否可拖入拖出
  - toTreeNode + convertToControlTreeData：将控件协议树转化为预览控件树
  - toArrayCtrl：将协议转化为数组形式，便于根据code将旧节点找出放入新控件树中
  - toCtrlNode：根据新节点控件树，将旧节点协议数组中的旧节点协议分别放入新节点控件协议树
  - getControlInUiProtocol：遍历协议中找到特定控件
- index.vue：
  - 根据节点控件树展示树，控件本身支持拖动
  - 拖动触发改变函数引发协议树更改
  - 点击触发控件改变并引起样式修改

   

#### 5.渲染方式
- 路径：components\uiCanvas
- controlRender.js：渲染布局，根据布局中是否包含子元素，子元素再逐一调用渲染函数进行渲染
- index.vue：在渲染组件中定义所有普遍的渲染样式
- handleStyle函数：将协议中的style样式转化为控件中的style样式
- updateCtrlFocusStatus函数：当更改焦点控件时为控件增加焦点样式
- draggable组件：
  - v-model绑定协议内的元素数组
  - 拖动过程中相当于对数组对象进行拖动，数组对象根据拖动的位置确定其在对应数组的位置


### 7.构建表单事件管理
- 路径：src\views\components\businessUi\components\edit\events
- 事件列表：传入协议中的页面初始化、页面监听、更多事件数组生成组件使用协议
- 渲染内容：行为工具固定渲染，根据数据渲染不同界面
- 属性列表：各种切换行为对应展示的事件/行为属性配置，配置协议转化为配置属性
- 业务模型数据：组件外部传入用于配置对应逻辑
- 控件列表数据：组件外部传入用于配置对应控件
- 真实协议E/视图协议S：按照协议文件内容生成默认协议节点，默认协议节点再根据真实协议进行变化，协议节点构成视图协议；协议更新时根据视图协议生成真实协议
- 生成基础事件/行为协议：根据actionDesc中的协议生成节点初始协议用于后续配置


#### 1.事件/行为协议
- 路径：js
- actionDesc：定义事件/行为的协议用作属性展示
- action：定义事件/行为的视图组件的描述协议，配置展示组件、图标以及列表
- constant：节点描述结构的选项值
- defaultTemplatePropValue：节点描述结构的value默认值
- eventicon：事件图标
- eventUtils：
  - parseToExportLogicInfo导出事件信息：ctrlMap生成控件code与控件对象的映射对象，ctrlLogicNameMap生成控件code与参数名的映射对象，logicPropertyMap生成出参属性名与出参属性对象的映射对象，columns获取出参绑定的表格列控件
  - getActionByActionType获取事件

#### 2.渲染样式
- 路径：components
- ActionTypeTools：行为类型工具栏，根据action中获取工具类型，拖动添加/点击添加时克隆协议
- EventPropTools：事件属性配置
- ActionPropTools：行为属性配置，渲染动态组件由协议中确定
- actionpanel：对应渲染的动态组件
- common：根据动态组件内部的属性类型进行不同的控件渲染
- setter：根据业务逻辑code获取到对应的业务逻辑对象并进行渲染


### 8.tag标签管理
- 租户1：
  - 创建普通标签，普通标签中关联元数据
  - 导出包
- 租户2：
  - 导入标签，出现下列报错情况
     - 标签名称与导入包标签名相同
     - 包内的元数据名称与租户2内现有元数据名称相同
  - 正常导入后，生成下列标签
    - 普通标签：引入包内的元数据，元数据生成在对应模块内，删除标签后依旧存在
    - 引用标签：自定义标签名称的引用标签，内容相当于备份，不可更改
    - 备份标签：当普通标签内的元数据与原有元数据存在code相同的情况，将原有元数据存入备份标签中



### 8.知识汇总
- watch定义：
  - handle：监听触发函数
  - immediate：为true时赋初值时触发函数
  - deep：为true时对对象数据的属性进行深度监听
- !!含义：将后面的表达式转化为布尔类型
- slot-scope：获取当前所在元素的数据
- slot-scope=“text,record,index”：text是本行本列值，record是整行的值，index是所在的第几行
- this.$nextTick(）：在DOM结构发生改变后调用回调函数
- .sync修饰符：子组件不允许修改父组件的值，父组件中使用sync修饰符后，子组件中可以通过\$emit(update)更新父组件中的值
- contextmenu：控件右击弹出表单事件
- this.\$options用来获取data外面的属性和方法
- vue实例的\$on、\$emit、\$off：vue上监听、触发、关闭事件
- 对象属性名与属性值同名是只需要写一个即可
- deepmerge：深度合并，浅度合并仅合并第一层，深度合并能合并对象属性中的属性
- <template #item="{element}">：draggable组件中element能获取draggable绑定的列表中的单个值
- 组件对象可以通过render函数直接渲染





### 9.IDE项目优化
- 整体使用：
  - 通用属性-标题宽度/标题宽度占比：两者的优先级，占比采用的单位需要进行输入提示，宽度优先级更高
  - 控件属性：上下限、最大小值设置要求不明或无法生效，（需要使用文档，设置采用flycode的距离当前天/月/年）自定义设置要求不明（设置快捷选择时间戳），秒单位无法设置（日期），周单位无法生效（日期区间）：手机与电脑端需要确定，暂时无需求则未实现
- 输入型控件：
  - 数字区间：显示风格无明显差异：显示风格未实现（手机端检查）
  - 周期选择：显示格式无明显差异；'5点时'格式错误：显示风格未实现（手机端检查）
  - 开关：点击后仅颜色变化，滑块无法滚动
- 事件：
  - 数据字典绑定：无设计文档；使用？
  - 导入导出：
  - 拜访流数据提交：使用？
  - 数据序列化：转化而成的内存属性变量如何使用？
  - 离线数据更新请求：重新对业务模型进行请求数据返回？
  - 事件呼叫：（当前存在页面？/目标页面？）收到广播，回调事件/回调key？
  - 事件监听：接收参数与回调参数，接收参数作用
  - 动态加载表单：目录的key？目录如何对应表单？传递参数的作用？
  - url链接：无法切换webview展示的页面
  - 执行事件：与调用控件事件的性质相似
  - 缓存数据搜索：表格对应的支持缓存数据搜索属性不存在？来源控件适用于逻辑输入的数组型控件/来源获取通常是datarequest？绑定搜索栏控件如何获取搜索栏中对应的搜索条件？
  

## 5.web端渲染
- 控件渲染整体思路：
  - Engine包负责渲染界面，ui-engine\engine\engine.js为控件渲染核心
  - 核心内调用xtion-web-component，将对应的引擎传入bind函数
  - 核心内根据pagecode进行请求获取到对应渲染协议
  - bind函数中根据xtion-web-component中的UIDirectory对引入的引擎进行register
  - register函数中对传入引擎的UIDirectory进行控件注册
  - render函数中引入的UIDirectory则为注册后的UIDirectory，根据协议依次调用渲染
     - 渲染过程中ref:code，为后续的事件调用控件做准备
     - 渲染过程中传入事件列表eventlist，为后续的事件调用做准备
- 事件执行整体思路：
  - 组件内调用parsePresenter函数，传入协议中的事件
  - parsePresenter函数内部整理事件数组eventPool，数组内元素整理行为队列eventsQueue
  - parseAction函数返回行为执行函数
- 关于新旧控件
  - 旧控件：component包充当控制器，widget包充当渲染器，component包的UIDirectory对应渲染组件
  - 新控件：Store包合并控制器与渲染器，component包的UIDirectory对应渲染组件，Engine\ui-engine\engine\render.js的render函数负责根据条件获取新/旧控件渲染
- 容器：WebAPP主要实现二开页面(不需要IDE配置)
- Engine包：起始渲染界面
  - page/layout：渲染整体布局，包括头部、导航以及主体
     - Nav2：导航路由，请求获取菜单组，根据点击项的路由进行跳转
     - Header：头部
     - Main：渲染engine
- Widget包：基础控件渲染
  - ui：普通控件渲染，组件内控制控件样式及相应事件，可通过XtWeb.Widget.UI属性获取渲染组件
  - ui-service：service控件渲染，组件内控制控件样式及相应事件，可通过XtWeb.Widget.UIService属性获取渲染组件
  - directives：对话框自定义指令
- Component包：render函数中调用渲染组件并传入prop进行渲染


## 6.超级表单lightform项目：configuration
- 整体思路：
  - 监听ctrls变化：根据ctrls变化修改其他部分(listview)协议
  - listview协议变化：与ctrls相关的直接修改，其他协议通过emit触发更新
  - 区分新增/编辑：新增情况直接获取默认协议
  - 配置协议-视图协议-保存协议：配置转为视图用于展示，视图转为保存
- packages\types：定义全局的类型
- moduleManagement：主界面，展示应用列表
  - edit：主界面-编辑新建应用弹窗
- pageManagement：主界面-表单管理界面，展示菜单栏以及根据菜单栏进行动态组件显示
  - pagelist：表单管理界面-表单管理，支持新建菜单
  - auth：表单管理界面-权限管理
  - module：表单管理界面-应用设置
- pageDesiger：主界面-表单设计界面
  - 新建表单：获取默认的协议模板作为表单协议
  - 编辑表单：获取对应表单作为表单协议
- ui：表单设计界面-表单配置
  - controlConfiguration：
     - attr：控件属性，common代表普遍属性
     - view：渲染组件
     - index：配置控件属性及渲染组件
  - components\controlAttr.vue：控件属性配置面板，根据协议中控件不同进行分别渲染
  - components\attrview：属性配置界面控件渲染样式
  - components\controlList.vue：控件面板，克隆协议的方式进行拖动或点击增加
  - components\uiCanvas：渲染面板，根据协议中渲染样式进行渲染
  - components\pageFormAttr.vue：表单属性配置面板
  - components\displaylogic：表单属性配置-显隐逻辑，配置协议选项，根据协议构建组件
  - components\pageattrview：表单属性配置-基础属性
  - components\submitValidate：表单属性配置-数据校验
- list：表单设计界面-列表配置
- publish：表单设计界面-发布配置
- preview：表单设计界面-协议预览
- 现有控件属性拓展：
  - types\controlDesc.d.ts：修改控件类型Result定义以及CtrlViewType定义
  - controlConfiguration\index.ts：新增控件默认属性，属性有无控制属性选项显隐
  - controlConfiguration\attr：新增控件属性选项
  - controlConfiguration\view：修改控件渲染样式，属性影响渲染情况下调用
  - components\controlAttr.vue：根据属性类型调用不同组件渲染
  - components\attrview：若控件属性渲染样式新增则新增渲染组件
- 新增子表控件：
  - controlConfiguration\attr\childtable：根据原型配置可设置属性
  - controlConfiguration\view\childtableView：根据原型设置样式，当child发生检测不允许的控件
  - controlConfiguration\index：设置初始属性，用作设置属性配置以及控件列表
  - controlDesc.d.ts：新增ctrlview以及修改result
  - list\Index-146：检测控件变化，控件的变化不写入listview中（子表以及子表内组件？）
  - protocolUtils-146：处理子表内部进行不同添加
  - protocolUtils-77：视图协议与协议转换过程中对子表进行特殊处理
  - 关联单选的默认值设置
  - 默认展示标题：可选控件根据进行控件拖动修改而修改，controlAttr.vue对该属性的option赋值
  - view选项只有子表code？子表内控件的code按顺序？：只有表单code
  - 子表主键code的生成规则，外键的值是哪个值？：根据表单code的生成规则
  - 子表内部控件属性只有这几个：基本保持一致，除了单行文本
  - 不可见字段赋值属性是否支持：子表不需要，子表内部保留
  - 数据序号的code：写死






## 7.超级表单lightform项目：app
### 1.整体构思
- 协议转换：根据路径的formcode获取完整表单协议，根据路由协议以及页面信息通过new生成对应pagestore，在这过程中同时生成控件控制器用于控制列表数据
- 信息存储：存储pagestore的storemap，在pageStore创建过程中放入storeMap中；可根据formcode获取pagestore中，其内包含页面所有数据信息
- 信息管理：利用类装饰器PageStoreOption构建storeMap、getStore，saveStore、deleteStore统一管理storeMap，根据不同pageStore将传入的state转化为可观测对象
- 控件管理：components中定义控件属性以及控件渲染类型，建立ctrlDirector统一管理所有控件，可通过控件类型type创建不同控件
- PageStore关键功能：
  - 构建协议键值对：根据ctrls协议生成ctrlViews，并以此生成键值对ctrlViewMap(code,view)
  - 构建控件键值对：根据ctrlDirector、view生成对应属性的控件，并以此生成键值对ctrlCodeMap(code,ctrl)，getCtrl函数根据code获取ctrlCodeMap中ctrl
- simpleObserver关键功能：
  - 将状态对象转换成可检测的对象
  - dealStateValue根据传入参数分类，符合拼接函数runSetState则执行该函数，this指向调用对象
  - dealStateValue根据传入参数分类，符合对象属性则直接进行set赋值，this指向调用对象
  - 设置基础方法如：注册注销触发事件、设置获取数据等


### 2.列表界面
- 列表数据获取：
  - 注册列表控制器的同时执行搜索，搜索函数中执行重载函数，内部执行loadData事件加载列表数据
  - 加载后将数据传入setStateValue并将数据写入rows属性中而且触发updateView事件
  - updateView事件中调用getUsedRows(返回rows属性)将行数据赋值给表格控件
  - 此外init初始化触发执行updateView事件也可将行数据赋值给表格控件
- list-form-list：
  - 根据路径中的参数获取formcode并以此获取对应协议
  - 根据协议以及其余数据作为参数通过new生成listPageStore
  - listPageStore初始化
- filter-engine：
  - 获取listPageStore中的搜索项并渲染
  - 搜索项comfirm后修改session以及listPageStore
  - 根据搜索项项数计算展示格数
  - 监听事件：展开/收缩、打开/关闭drawer、搜索
  - advance-search：drawer负责搜索项的操作
- list\index：
  - 作为prop传入render中调用
  - 定义列表初始属性ListState
  - 初始化列表列initColumn：根据协议原始列数据生成列对象并作不同渲染模式
  - 初始化子控件控制器initCtrlDirect：根据不同控件类型引入不同控件控制器
  - 初始化行数据initOriginRows：对行数据进行包装，过滤不存在对应列对象的数据
  - 切换选中模式handleChangeSelectMode：根据勾选切换表格内容
- list\render：
  - 根据listCtrl进行渲染，绑定listCtrl中的属性及事件，在infotable进行渲染
  - 通过listEL绑定表格控件，内部API调用loadData进行加载数据，数据写入listEL中实现数据更新
- widget\infotable：引入的vxe-table控件本体，绑定传入的属性以及事件
- listPageStore：
  - 调用created将参数写入自身state中并将自身存入storemap中
  - 根据协议获取列表视图listview
  - 根据listview获取搜索项filterView
  - 根据listController(list\index.tsx)类生成列表控制器listCtrl并赋值
  - 注册列表控制器：列表获取、删除、跳转(对应event中事件)
  - 初始化预设按钮operations：新增、删除
  - 支持搜索功能，搜索前执行更新参数


### 3.表单页面
- lithe-form-info：
  - 根据路径中的参数获取formcode并以此获取对应协议
  - 根据协议以及其余数据作为参数通过new生成formPageStore
  - formPageStore初始化
- FormPageStore：
  - getFormViewFromProtocol根据协议中的code获取对应视图
  - createViewCtrl根据视图创建视图控件
  - initFormCtrl初始化表单控件(除栅格外)，根据控件状态对默认值初始化
  - initData初始化数据，根据获取的数据对控件赋值
  - initCtrlHiddenWatch初始化显隐逻辑，对控件值进行监听，发生变化时调用函数进行逻辑判断显隐
  - 提交：对提交进行校验，校验成功则提交触发列更新；同时支持表单新增、修改、删除功能
- 表单渲染过程：遍历view获得view.code，根据PageStore.getCtrl(view.code)获取ctrl，调用ctrl.render作为渲染组件写入渲染函数中；ctrl作为组件属性写入渲染函数中
- 属性赋值过程：PageStore生成控件的同时对控件赋值，获取ctrl作为属性传入完成属性赋值
- 校验规则：
- 现有控件属性拓展：components中修改组件渲染样式(包括mobile与web)以及组件的state




## 8.项目构建思维
- 协议类型文件：
  - 配置使用：根据普遍适用的性质构建，读取文件获取协议进行整体配置
  - 适用场景：需要引入具有普遍适用性的对象类型/枚举类型
- 全局事件：
  - 配置使用：配置全局对象Bus，在外部import之后，通过on注册全局事件，emit触发全局事件
  - 适用场景：子组件修改props传值，通过emit的形式在父组件中更新props


## 9.演示环境搭建问题
- 租户硬性问题：
  - 接口无法更改目录 
  - 数据表无法迁移 
  - 产品级页面无法更改
  - UI控件无法迁移
  - 流程无法使用
- IDE与web端发版日志书写风格不同，是否需要统一
  - 增加开发人员姓名：web端有开发人员姓名
  - 开发类别的属性：对于新增功能、bug、ui等功能无具体描述
  - 发版日志版本划分：IDE未根据大版本划分内容，web端根据大版本划分内容
- 文档书写模板：
  - 需求背景：实现效果
  - 配置指导：步骤+图片，备注管理员/业务员权限下使用
  - 服务版本：版本限制+后端部署，如只有版本说明可在配置指导前面
  - 备注：备注信息（权限限制：管理员/业务员）
- IDE相关功能文档：
  - 开发者网站文档主要服务对象：实施/客户
  - 功能使用普及手段：没有文档的功能开发出来实施如何清楚功能使用，例如图片标签
  - 功能性迭代的文档需求：功能类似于增加一个配置项对应实现一个效果，或者增加一个导入导出功能可导出，这类功能需不需要做一个文档简要普及使用效果及用途
- IDE3.4可完善文档：
  - UI预览
  - 各种导入导出
  - 历史记录


## 10.开发者文档书写风格
- 旧版文档书写存在问题：
  - 发版日志版本错误：IDE发版日志全部放到一篇，web发版日志对应版本应展示对应版本内容
  - 版本内容划分不明显：IDE发版日志划分不明显
  - 内容重点不突出：应将重点从更新类型迁移到更新内容上
  - 更新内容不明显：部分内容描述含糊且无文档说明
  - 说明文档不够具体：书写出发角度各异，部分从需求角度出发，部分从开发角度出发，部分从部署角度出发，部分从使用角度出发
- 发版文档
  - 标题：版本名称+日期
  - 更新信息：版本更新信息
  - 更新内容：版本更新内容
  - 版本涵盖：例如：V3.4版本发版日志包括V3.4.0-V3.3.1的发版日志
- 更新内容：
  - 大版本IDE：实体、领域、表单、功能、工具、流程各模块以及其他
  - 大版本web引擎：功能新增、功能优化、UI/UE优化，bug修复各模块以及其他
  - 更新内容具体撰写模板：【控件/模块/页面】：更新内容[发版说明文档]
- 发版说明文档
  - 整体说明：将部分需要简单介绍的功能进行统一的文档介绍
  - 整体说明基础模板：各功能（用途+使用）
  - 个别说明：对于需要较长篇幅的功能进行单独文档介绍
  - 个别说明基础模板：版本+需求背景+版本要求+配置指导+部署说明
  


## 11.报表下钻
### 1.名词解释
- 事实表：对应需要的图表包含的数据量，一个事实表可对应多个维度表
- 维度表：事实表中各字段对应的维度以及维度标签形成的维度表
- ODS：与原数据表基本一致
- DWD：对ODS层进行数据清洗以及去除脏数据形成
- DWS：对DWD层进行数据规范化形成最终需要的图表数据


### 2.IDE-表管理
- 建表：创建ODS/DWD/DWS表，用于对应数据开发中的操作表，信息除字段与表名外基本一致
- ODS表：创建的字段与对应标准库中的字段基本一致，可增加需要字段
- DWD表：创建的字段与对应标准库中的字段基本一致，可增加需要字段
- DWS表：字段与最终需要形成图表所需的字段一致


### 3.IDE-数据开发
- 数据集成ODS：标准库中通过表查询生成一份新的表写入ODS表
- 数据加工DWD：对ODS表进行数据清洗，去除脏数据写入DWD表
- 数据加工DWS：获取最终形成图标所需的字段写入DWS表


### 4.IDE-多维模型管理（二选一）
- 模型图：新建模型图
- 事实表：关联原有的DWS表，需要展示的指标勾选分析
- 维度表：拉取维度表，未勾选分析的指标关联维度，用作图表的筛选


### 5.IDE-数据集管理（二选一）
- 数据集：通过SQL直接操作表的入参与出参


### 6.IDE-报表配置
- 主题：对应多维模型
- 数据源：对应数据集
- 指标展示：选择数据展示形式，将事实表/维度表的指标对应形式中的对应位置
- 指标配置：配置指标的汇总方法以及显示格式等
- 筛选设置：配置报表的维度筛选项的筛选规则（若为数据集需绑定数据源入参）


### 7.IDE-看板配置
- 图表创建：创建图表关联报表
- 筛选设置：配置报表的维度筛选项的筛选规则
- 图表广播：图表广播绑定筛选项


### 8.DI-看板挂载
- 导航配置：创建菜单关联看板链接
- 权限关联：角色权限绑定对应导航


### 9.IDE-看板配置下钻
- 看板跳转：看板中配置指标对应跳转看板