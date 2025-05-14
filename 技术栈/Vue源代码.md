# Vue源代码

标签（空格分隔）： 技术栈

---

## 1.Object的变化侦测
- Vue的数据侦测粒度提高到组件层面，状态变化时通知组件进行虚拟DOM对比
- 定义observer类，将正常的object转换成可观测的object
  - 新增一个`__ob__`属性，值为value的Observer实例，表示已转化成响应式
  - object类型的数据会调用walk将每一个属性转换成getter/setter的形式来侦测变化
  - 在defineReactive中当传入的属性值还是一个object时使用new observer（val）来递归子属性
- 依赖收集与依赖更新
  - 将所有使用到对应数据的视图放入依赖数组当中，依赖改变集体通知
  - getter中收集依赖，setter中通知依赖更新
  - 依赖管理器Dep负责依赖数组的删除、添加、通知更新
  - 对window.target绑定watcher类，通过watcher读取数据时触发getter，将watcher添加到依赖中；数据发生变化时触发setter，从而向dep中的依赖watcher发送通知



## 2.Array的变化侦测
- 数组方法拦截器
  - 继承自Array原型的空对象arrayMethods，接着在arrayMethods上使用object.defineProperty方法将那些可以改变数组自身的7个方法遍历逐个进行封装
  - 把它挂载到数组实例与Array.prototype之间，把数据的`__proto__`属性设置为拦截器arrayMethods
  - 利用getter和setter管理依赖
  - 下标修改数组以及修改数组长度无法监测，使用两个全局API:Vue.set和Vue.delete


## 3.虚拟DOM
- 真实DOM的操作消耗性能，通过JS对象模拟一个虚拟的DOM结点，比较变化前后的虚拟DOM节点，通过DOM-Diff算法计算出需要更新的地方，然后去更新需要更新的视图
- DOM-Diff：patch过程，节点包括：元素节点、注释节点、文本节点
- 更新子节点：创建、移动子节点在所有未处理节点之前
- 优化更新子节点策略：新前新后、旧前旧后的特殊子节点对比
    


## 4.模板编译
- 将用户写的类html模板编译成render函数
- 具体流程
  - 模板解析阶段：将一堆模板字符串用正则等方式解析成抽象语法树AST；
  - 优化阶段：遍历AST，找出其中的静态节点，并打上标记；
  - 代码生成阶段：将AST转换成渲染函数
- HTML解析器
  - parseHTML函数传入另个参数：template（待转换的模板字符串）、option（转换时所需选项，包括四个钩子函数start、end、chars、comment）
  - 解析到开始标签时调用start函数接受标签名tag、标签属性attrs、标签是否自动闭合unary三个参数，使用createASTElement函数来创建元素类型的AST节点
  - 解析到结束标签时调用end函数
  - 解析到文本时调用chars函数，根据文本是动态或静态创建对应AST节点
  - 解析到注释时调用comment函数生成注释类型的AST节点
  - 解析html注释：模板字符串html进行正则表达式验证，根据验证结果确定注释真实内容，将真实注释内容传进去创建注释类型的AST节点，其中options.shouldKeepComment配置是否保留注释
  - 解析游标advance移动：根据移动数确定解析html
  - 解析条件注释：与html注释相似，但是不需要创建AST节点，条件注释不存在于真正的DOM树中
  - 解析DOCTYPE：与解析条件注释相同
  - 解析开始标签：parseStartTag
     - 1.解析标签名：匹配正则表达式，
     - 2.解析标签属性：匹配正则表达式，将属性放入属性数组并利用游标移动循环判断
     - 3.解析标签是否自闭合：匹配正则表达式，根据结果判断自闭合/非自闭合
  - 处理开始标签：handleStartTag
     - 对属性进行处理，必要情况下针对属性进行兼容性处理
     - 将处理后的属性写入attrs数组中
     - 非自闭合标签则将标签推入栈中；自闭合标签则调用start函数创建AST节点
  - 解析结束标签 parseEndTag：根据匹配结果确定调用parseEndTag，判断栈中是否有对应的标签进行弹出，若出现缺少闭合标签或需补全开始标签的情况则调用end函数
  - 解析文本：判断‘<’的位置，根据位置不同进行不同处理
     - 在第一个位置：为其他节点类型
     - 不在第一个位置：循环判断内部的文本是否包含其他节点类型
     - 没有位置：整个模板字符串都是文本
     - 调用chars函数生成textAST
  - 保证AST节点层级：非自闭合标签推入栈中，结束标签推出栈中，根据顺序确定层级关系
- 文本解析器
  - 通过parseText检测文本中是否包含变量，包含变量的情况下AST节点多出expression和tokens属性
  - parseText接受两个参数：
     - text：待解析的文本内容
     - delimiters：自定义的符号（检测包含变量所使用的自定义符号）
  - 对text内进行正则表达式判断，在循环过程中，判断变量所在位置与当前字符串位置从而分别取出变量以及纯文本写入数组当中
- 优化阶段
  - AST中为静态节点/静态根节点打上标记，patch过程中跳过对比这些节点
  - 标记静态节点：
     - 根据节点类型以及节点是否具有动态属性
     - 对当前结点的子节点进行递归判断，若子节点为非静态节点，当前节点也为非静态
     - 对当前结点的条件结点进行递归判断，若条件结点为非静态结点，当前节点也为非静态
  - 标记静态根节点：
     - 节点本身必须是静态节点
     - 必须拥有子节点children
     - 子节点不能只是只有一个文本节点
- 代码生成阶段：根据节点类型的不同执行不同代码的生成函数
- 总结： 
  - compileToFunctions函数内部调用compile函数
  - compile函数内部调用baseCompile函数
  - baseCompile函数返回的是代码生成阶段生成好的render函数字符串
  - compileToFunctions函数内部将render函数字符串传给createFunction函数
  - createFunction生成真正的render函数返回出去，最后将其赋值给options.render
     


## 5.生命周期
### 1.初始化阶段（new Vue）
- 将用户传递的options选项与当前构造函数的options属性及其父级构造函数的options属性进行合并，得到新的options选项赋值给变量vm的\$options属性，并挂载到vue实例上
  - Vue.options：initGlobalAPI函数将ASSET_TYPES中的‘component’、‘directive’、‘filter’选项以及Vue.options.components的内置组件写入Vue.options
  - mergeOptions：首先递归地把extends和mixins合并到parent上；遍历parent将parent、child中的选项进行合并；遍历child将存在于child但不存在与parent的选项进行合并，不同属性具有不同的合并策略
  - 生命周期函数合并：钩子函数转化为数组，保留多个钩子函数同时触发
- 初始化生命周期、事件、渲染
- 调用生命周期钩子函数callHook(vm, 'beforeCreate')
- 初始化injections、props、methods、data、computed、watch、provide
- 调用生命周期钩子函数callHook(vm, 'created')
- 判断用户是否传入el选项，传入则调用\$mounted函数进入模板编译与挂载，否则需手动执行方法


### 2.初始化阶段（initLifecycle）
- 挂载options的parent属性：若当前组件不是抽象组件并且存在父级，则向上循环找到不是抽象类型的父级，将父级赋值给vm.\$parent，将实例自身添加进父级的\$children属性中
- 挂载实例的\$root属性：若存在父级，则为父级的跟属性；若不存在父级，则为自身实例
- 初始化其他属性


### 3.初始化阶段（initEvents）
- 解析事件：
  - 解析标签：调用process方法解析标签属性，通过正则判断是否是指令，若是则执行addHandler
  - 增加指令：addHandler根据修饰符对事件名做处理，根据原生事件或自定义事件将回调函数字符串保留到对应的事件当中
  - 代码生成：指令用于生成AST元素节点中的data数据
  - 挂载：自定义事件挂载在子组件当中并在子组件初始化过程中处理；原生事件在父组件中处理
- initEvents分析：
  - initEvents：将父组件注册到子组件的事件注册到子组件的实例中
  - updateComponentListeners：将事件listeners和add、remove两个函数传入updateListeners中
  - updateListeners：
     - 遍历listeners
     - normalizeEvent根据事件名进行反处理获得修饰符
     - 获取事件名判断事件是否存在
     - 继续判断事件名在oldListeners中是否存在，不存在就给事件绑定回调函数并调用add添加事件
     - 若存在则判断事件事件值是否相同，不相同则修改oldListeners的回调函数为新的回调函数
     - 遍历oldListeners，若listeners中不存在，则调用remove



### 4.初始化阶段（initInjections）
- initInjections函数分析
  - normalizeInject函数将inject的写法统一成规范化格式
  - resolveInject将inject转化为键值对result
  - toggleObserving设置为非响应式
  - defineReactive把键值对result添加到当前实例
- resolveInject
  - 遍历inject，获取inject的from
  - 向上循环获取父组件的_provided与from相匹配
  - 若没有匹配的来源则取默认值，若无默认值则报错


### 5.初始化阶段（initState）
- 初始化顺序：props、methods、data、computed、watch
- 初始化props：


## 6.keep-alive
- 使用
``` javascript
<keep-alive>
    <component :is="chooseComponent"></component>
</keep-alive>
```
- props：keep-alive支持配置的属性作为prop传入
```javascript
props: {
    include: [String, RegExp, Array],//缓存包含组件
    exclude: [String, RegExp, Array],//缓存不包含组件
    max: [String, Number]//最大缓存组件
}
```
- created：创建组件缓存数组以及标识数组
```javascript
created () {
    this.cache = Object.create(null)//缓存组件数组
    this.keys = []//组件数组中元素的标识
}
```
- mounted：检测include和exclude的变化，变化规则后的部分组件不需要缓存则从其中剔除
```javascript
mounted () {
    this.$watch('include', val => {
        pruneCache(this, name => matches(val, name))
    })
    this.$watch('exclude', val => {
        pruneCache(this, name => !matches(val, name))
    })
}

function pruneCache (keepAliveInstance, filter) {
  const { cache, keys, _vnode } = keepAliveInstance
  for (const key in cache) {
    const cachedNode = cache[key]
    if (cachedNode) {
      const name = getComponentName(cachedNode.componentOptions)
      if (name && !filter(name)) {
        pruneCacheEntry(cache, key, keys, _vnode)
      }
    }
  }
}
```
- render：
  - 获取渲染节点
  - 不符合传入规则不缓存直接渲染
  - 符合规则对该组件进行判断，缓存中存在的调用缓存组件，缓存中不存在的写入缓存，最后再渲染
```javascript
/* 获取渲染节点 */
const slot = this.$slots.default
const vnode = getFirstComponentChild(slot)

/* 不符合传入规则不缓存直接渲染 */
function getComponentName (opts: ?VNodeComponentOptions): ?string {
   return opts && (opts.Ctor.options.name || opts.tag)
}
const name = getComponentName(componentOptions)
const { include, exclude } = this
if (
    (include && (!name || !matches(include, name))) ||
    (exclude && name && matches(exclude, name))
) {  return vnode  }

/* 符合规则对该组件进行判断，缓存中存在的调用缓存组件，缓存中不存在的写入缓存 */
const { cache, keys } = this
const key = vnode.key == null
? componentOptions.Ctor.cid + (componentOptions.tag ? `::${componentOptions.tag}` : '')
: vnode.key

/* 缓存中存在的调用缓存组件 */
if (cache[key]) {
    vnode.componentInstance = cache[key].componentInstance
    // 移除原来的可以将其放到最后一个，便于引用缓存淘汰策略
    remove(keys, key)
    keys.push(key)
}
/* 缓存中不存在的写入缓存 */
else {
    cache[key] = vnode
    keys.push(key)
    // 执行
    if (this.max && keys.length > parseInt(this.max)) {
        pruneCacheEntry(cache, keys[0], keys, this._vnode)
    }
}
/* 最后设置keepAlive标记位 */
vnode.data.keepAlive = true
```
- destroyed：将那些被缓存的并且当前没有处于被渲染状态的组件都销毁掉并将其从this.cache对象中剔除
```javascript
destroyed () {
    for (const key in this.cache) {
        pruneCacheEntry(this.cache, key, this.keys)
    }
}

function pruneCacheEntry (cache,key,keys,current) {
  const cached = cache[key]
  /* 判断当前没有处于被渲染状态的组件，将其销毁*/
  if (cached && (!current || cached.tag !== current.tag)) {
    cached.componentInstance.$destroy()
  }
  cache[key] = null
  remove(keys, key)
}
```