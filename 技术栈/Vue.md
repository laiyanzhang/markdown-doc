# Vue

标签（空格分隔）： 技术栈

## 1.LifeCycle
- **beforeCreate**：数据观测和初始化事件未开始
- **created**：完成数据观测，属性和方法的计算，初始化事件，\$el属性不可用
- **beforeMount**：render函数被调用，实例完成编译模板，并把数据和模板生成HTML
- **mounted**：用编译好的HTML来替换el属性指向的DOM对象，完成模板中的HTML渲染到页面
- **beforeUpdate**：数据更新时调用，能访问当前DOM
- **updated**：组件DOM已经更新，可以执行依赖于DOM的操作；大多数情况下避免在此期间改变状态；不会保证所有子组件都会重绘，可以使用nextTick确保操作重绘后的DOM
- **beforeDestory**：在此状态仍然可以调用实例
- **destroyed**：实例被销毁，监听事件也被销毁
- **activated**：keep-alive缓存组件激活时调用，每次切换到该组件时不需要再调用created与mounted
- **deactivated**：keep-alive缓存组件停用时调用

### Vue3
- **beforeUnmount**：取代beforeDestroy
- **unmounted**：取代destroyed
- **renderTracked**：跟踪目标对象，在第一次渲染组件时记录跟踪对象的键值以及操作类型
- **renderTriggered**：触发目标对象，在操作触发重新渲染时获取操作后对象的键值以及操作类型


 
## 2.computed
```
computed: {
   data: {
      get(){ }，
      // 数据重新赋值情况下才触发，同时前提是data值需在页面内渲染
      set(){ }
   }
}
```
- computed与method的差别：
  - 调用差别：method需要(),computed调用属性名(当v:on时都是直接调用属性名)
  - 执行差别：在vue未改变的情况下，computed属性第一次执行后不再执行;method会伴随着调用不断执行
- computed与watch的差别：
  - 当data内的某数据随着其他数据改变而改变，适合computed
  - 当需要在数据变动时执行异步或开销较大时，适合watch


## 3.watch
```
watch: {
    data: { // data是需要监听的属性
       handler(newValue, oldValue) { },
       deep: true, // false：无法监听对象属性变化，仅监听对象属性增减
       immediate: true // true：首次获得值时也会执行handler
    }
} 
```


## 4.bind
    // 绑定属性
    <div v-bind:[attribute]=""> </div> // attribute可以是JavaScript表达式，不允许空格和引号
    
    // 绑定class
      <div :class="{'class1': booleanData1, 'class2': booleanData2}"></div>
    or 
      <div :class="[class1,class2]"></div>
    
    // 绑定style
    <div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>


## 5.on
- 传入事件参数event：`v-on:click = "function($event)"`
- 事件修饰符：v-on:click.修饰符 = ""
  - .stop：阻止点击子元素产生父元素的冒泡
  - .prevent：阻止默认事件发生，如阻止表单提交并执行函数内程序
  - .capture：冒泡时优先捕捉，然后再进行从内到外冒泡，同时存在capture时由外到内
  - .native：将组件转化为普通html标签，从而根元素可以实现事件触发
  - .self：只有自身元素才会触发，不是内部元素
  - .once：只触发一次
- 按键修饰符：v-on:keyup.按键名 = "" （控件内使用按键触发相应事件）


## 6.model
- 复选框与v-model结合：
  - 单选：`<input type="checkbox" v-model="checked"> //绑定boolean值`
  - 多选：`<input type="checkbox" value="Runoob" v-model="checkedNames"> //选中后推入checkedNames的元素值为Runoob`
- 单选按钮与v-model结合
  - `<input type="radio"  value="Google" v-model="picked"> //选中后picked的值为Google `
- 下拉列表与v-model结合
  - `<select v-model="selected"> <option value="www.runoob.com">Runoob</option> </select> //选中后selected的值为www.runoob.com`
- 修饰符：v-model.修饰符
  - .lazy:发生更新的是change时间而不是input时间
  - .number:将input内的值转化为数字写入data中
  - .trim:过滤输入的首尾空格



## 7.component
- prop定义：
  - `props: ['title']`：接受父元素数据（基础类型单向绑定，对象类型传递地址），数据以数据形式存储
  - `props: { title: String }`：接受父元素数据的同时进行数据类型验证，数据以字典形式存储
  - 子组件prop：驼峰命名法
  - 父组件prop：短横线分割命名法
- prop改变：
  - prop传入数组/对象：改变会影响父组件
  - prop传入基础类型：改变不会影响父组件
- \$attrs与inheritAttrs：属性传递
  - v-bind="\$attrs"：\$attrs对应props没有定义的属性，将其绑定在指定元素内，成为该元素属性
  - inheritAttrs: false：默认没有props没有定义的属性渲染在根元素上，定义之后不再渲染
- \$listeners与自身监听器：事件传递
  - \$listeners：父级添加的所有监听器
  - computed：将自身的监听器方法与父级监听器方法并入同一个对象
  - v-on = "对象"：将所有的监听器指向特定的子元素，子元素触发事件
- .sync修饰符：数据双向绑定
  - 组件内：`this.$emit("update:prop", value)`
  - 组件外：`v-bind:attr.sync / v-bind.sync` 对象内每一个属性可分别改变
- \$emit：子元素触发该方法从而触发父元素的事件，也可传入值：
  - `$emit(event,data)` event:父元素事件，data：传入父元素的数据
- model:
  - 组件内可定义 `v-bind:data="prop" v-on:change="$emit('change', $event.target.data)"`
  - 组件外可定义 `v-model="data" // 绑定input、change等事件`
  - 修改监听属性及事件：`model: { prop: 'title', event: 'change'}`
- slot:
  - 插槽内容：
     - 组件外：`<div>content</div>`
     - 组件内：`<slot>默认值</slot> => content`
  - 具名插槽：
     - 组件内：`<slot name="名称">`，没有name则为默认default
     - 组件外：`<template v-slot:名称>content</template>`，没有v-slot则为默认插槽内容
  - 作用域插槽：
     - 组件内：`<slot v-bind:user="user">`，元素上的属性user为插槽prop
     - 组件外：`<template v-slot:default="slotProps">`，slotProps为包含所有插槽prop的对象
     - 解构插槽：`v-slot="slotProps" => v-slot="{user}"`
     - 重命名：`v-slot="{ user: person }"`
     - 后备内容：`v-slot="{ user = { firstName: 'Guest' } }"`
  - 语法缩写：`v-slot:user => #user (v-slot => #)`
  - 插槽访问：
     - this.\$slots：访问静态插槽内容
     - this.\$scopedSlots：访问作用域插槽
  - 2.6版本废弃语法：
     - slot属性：具名插槽，可用在template或者普通元素上
     - slot-scope属性：作用域插槽，可用在template或者普通元素上
- `<keep-alive>`
  - `<component v-bind:is="名称" :include="['a', 'b']" :exclude="['c', 'd']"></component>` 
  - 组件生命周期函数：activated：激活时调用；deactivated：停用时调用
  - 获取新数据：在activated生命周期进行数据获取
- 处理边界情况
  - 访问根实例：`this.$root`
  - 访问父级组件实例：`this.$parent`
  - 访问子组件实例或子元素：`this.$refs.name`（元素绑定ref属性name）
  - v-for与ref搭配使用：`this.$refs.name`返回子组件数组，`this.$refs.name[0]`返回对应子组件
  - 程序化的事件侦听器：
     - 通过 \$on(eventName, eventHandler) 侦听一个事件
     - 通过 \$once(eventName, eventHandler) 一次性侦听一个事件
     - 通过 \$off(eventName, eventHandler) 停止侦听一个事件
  - 模板定义的替代品
     - 内联模板：组件内出现inline-template这个attribute
     - X-Template：script标签内定义text/x-template类型，通过id引用定义的模板
  - v-once属性：用于一次性渲染大量静态内容
- 依赖/注入
  - 依赖provide：
     - 常量`provide: { user: 'lyz' }`
     - 实例`provide() { return { user: this.user } } // 传递属性或方法`
     - 响应性`provide() { return { user: Vue.computed(() => this.user) } }`
  - 注入inject：inject: ['getMap']


## 8.directive
- 全局自定义指令
```
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时
  inserted: function (el) {
    el.focus()
  }
})
```
- 局部自定义指令
```
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```
- 指令绑定：`<input v-focus>`
- 指令定义提供的钩子函数（指令属性绑定到元素时生效）
  - bind：绑定到元素时调用，只调用一次
  - inserted：被绑定元素插入父节点时调用
  - update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前
  - componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用
  - unbind：解绑时调用，只调用一次
- 钩子函数的参数：
  - el：被绑定元素，仅此参数可读可写，其他均为只读
  - binding：一个对象，包含以下属性
     - name：指令名（不包含v-前缀）
     - value：指令绑定值
     - oldVaule：指令绑定前一个值
     - expression：指令绑定值的表达式或变量名
     - arg：传给指令的参数：v-directive:arg，可使用动态指令参数 arg -> [direction]
     - modifiers：修饰符对象：v-directive:foo.bar {bar:true}
  - vonde：编译生成的虚拟结点
  - oldVnode：上一个虚拟结点
- 函数简写：只在bind和update时触发行为：`Vue.directive('direction', function (el, binding) { })`
- 对象字面量：指令需要多个值可以传入JavaScript对象字面量



## 9.transition
- 过渡
```
<transition name="fade">
   <p v-if="show">hello</p>
</transition>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
```
- transition处理顺序：CSS过渡--JavaScript钩子--下一帧立即执行
- 过渡类名：v对应transition的name
  - v-enter：进入过渡的开始状态（元素插入前生效，插入后下一帧移除）
  - v-enter-active：进入过渡生效时的状态（定义过渡的过程时间，延迟和曲线函数）
  - v-enter-to：进入过渡的结束状态（元素插入之后下一帧生效，过渡完成后移除）
  - v-leave：离开过渡的开始状态
  - v-leave-active：离开过渡生效时的状态
  - v-leave-to：离开过渡的结束状态
- 自定义过渡类名（transition内属性定义，结合css动画库）
  - enter-class
  - enter-active-class
  - enter-to-class (2.1.8+)
  - leave-class
  - leave-active-class
  - leave-to-class (2.1.8+)
- duration属性：显性的过渡持续时间
- JavaScript钩子：
  - v-on绑定属性before-enter、enter、after-enter、enter-cancelled
  - vue实例中methods定义方法：enter:function(el,done){done()}
  - 仅用JavaScript过渡时，enter与leave必须使用done回调，否则同步调用，过渡立刻完成
  - 仅用JavaScript过渡时，v-bind:css="false"，跳过CSS检测，避免CSS影响
- appear属性：设置节点在初始渲染的过渡
- key属性：多个相同标签名的元素切换，需要使用key属性来设置唯一标识让vue进行区分
- mode属性：过渡默认行为是进入和离开同时发生，可定义过渡模式
  - in-out：新元素先进行过渡，完成之后当前元素过渡离开
  - out-in：当前元素先进行过渡，完成之后新元素过渡进入
- 列表过渡：
  - < transition-group > 组件定义一个列表，默认为一个< span >，可通过tag属性替换为其他元素
  - 不仅可以进入和离开动画还可以改变定位
  - 过渡模式不可用
  - 总是需要key属性
  - CSS过渡类应用于内部元素而不是容器本身
  - 过渡类名v-move定义元素改变定位时的切换时间和过渡曲线，元素不可为display: inline
  

  

## 10.ajax
- get请求：
```
// URL写入参数
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });

// params写入参数
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```
- post请求：
```
axios.post('/user', {
    firstName: 'Fred',        
    lastName: 'Flintstone'    
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```
- 并发请求：
```
function getUserAccount() {
  return axios.get('/user/12345');
}
 
function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}
axios.all([getUserAccount(), getUserPermissions()])
  .then(axios.spread(function (acct, perms) {
    // 两个请求现在都执行完成
  }));
```
- 配置请求：
```
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flintstone'
  }
});
```
- 配置默认值：
```
var instance = axios.create({
  baseURL: 'https://api.example.com'
});
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
```
- 拦截器：
```
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```
- 定义状态码错误范围：
```
axios.get('/user/12345', {
  validateStatus: function (status) {
    return status < 500; // 状态码在大于或等于500时才会 reject
  }
})
```

- vue-resource：
```
this.$http.get('/someUrl', [options]).then(successCallback, errorCallback);
this.$http.post('/someUrl', [body], [options]).then(successCallback, errorCallback);

// body对象：包含传输的文件/数据
// option对象：url、body、headers、params、method、timeout、before、progress、credentials、emulateHTTP、emulateJSON
```


## 11.mixin
- 混入合并
```
var mixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data)
    // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```
- 选项合并：混入对象和组件含有同名选项时将以恰当的方式进行合并
  - 数据：混入排除，组件数据优先
  - 钩子函数：混入合并，混入对象钩子在组件自身钩子之前调用
  - methods、components 和 directives选项：混入排除，组件对象优先
- 全局混入：Vue.mixin()影响每一个之后创建的vue实例



## 12.render
- v-for与v-if：v-for的优先级高，在每一项中都设置v-if
- v-if与v-show：
  - v-if：对应元素的添加或删除
  - v-show：对应添加CSS属性display:none(对本身display:none的元素无效)
- key的作用：
  - key值最好不要设置为数组下标，不利于更新指定节点
  - 列表渲染中定义不同的key，当列表中发生变化时仅做对应变化的改变，无需重新全部渲染
  - 当为元素定义key时，key值做出改变会强制元素替换，先删除旧元素再添加新元素
  - 适用于diff算法进行节点变化
- 虚拟DOM结点与模板渲染：
  - template模板编译生成成render渲染函数
  - 执行渲染函数得到虚拟节点树，虚拟节点树有tag、attribute、children三个属性
  - diff算法对比新旧虚拟节点树，只对差异的DOM进行DOM操作
  - 虚拟节点树生成真实DOM
- Virtual DOM的优点：
  - 跨平台的优势：由于 Virtual DOM 是以 JavaScript 对象为基础而不依赖真实平台环境， 所以使它具有了跨平台的能力，比如说浏览器平台、Weex、Node 等；
  - 提高效率：操作 DOM 慢，js运行效率高，所以将DOM对比操作放在JS层可以提高效率；
  - 提高渲染性能：通过patch的核心diff算法，找出本次DOM需要更新的节点来更新，其他的不更新。对某项数据的数次修改在一次事件循环中将最后统一为一次修改（异步更新队列）
- 数据双向绑定：
  - 原理：将JavaScript对象传给vue实例的data选项时，vue将遍历此对象的所有data选项并且遍历对象所有属性，Object.defineProperty() 把这些属性全部转为getter/setter进行依赖收集，变更通知
  - 限制：无法检测到数组的索引与长度修改、对象属性的增加与删除
  - 对于对象：
     - 添加单个响应式property：`this.$set(object,key,value) / Vue.set(vm.object,key,value)`
     - 添加多个响应式property：`this.object = Object.assign({}, this.object, { a: 1, b: 2 })`
  - 对于数组：
     - 索引值修改：`this.$set(object,index,value)`
     - 设置数组长度：`this.object.splice(newLength)`
- 异步更新队列：
  - vue更新DOM是异步执行的，异步更新队列会缓冲一次事件循环内发生的所有数据变更，从而避免不必要的计算和DOM操作。在下一次事件循环tick中刷新队列并执行实际工作
  - 实现数据变化后等待vue完成更新DOM：`Vue.nextTick(callback) / this.$nextTick(callback)`
- 渲染函数：
  - 组件定义：`render:function(createElement){return createElement()}`
  - createElement参数：元素名称，元素属性{}，子元素[]
  - VNode必须唯一，不存在同名的Vnode生成函数，可采用工厂函数代替生成
  - 渲染函数中vue指令的不同实现
     - v-bind：元素属性对应实现
     - v-if/v-for：JavaScript函数实现
     - v-model：元素属性中的domProps与on共同实现逻辑
     - 事件&按键修饰符：部分提供相应前缀用于元素属性中的on / 其他修饰符使用对应事件处理函数
     - 插槽：this.\$slots访问插槽内容，this.\$scopedSlots访问作用域插槽，scopedSlots属性创建插槽
  - JSX替代渲染函数书写
  - 函数式组件
     - 不管理监听任何状态，也没有生命周期方法，只接受一些prop函数，没有响应式数据，没有实例
     - 组件需要的一切都是通过context参数传递
     - 用于程序化地在多个组件中选择一个来代为渲染、在将children、props、data传递给子组件之前操作它们
- 插件：使用插件：Vue.use()；开发插件：vue.install
- 过滤器：vue实例中filter属性定义过滤器函数
- 安全：不要将不可信任的内容作为模板内容使用，例如注入html、url、js、css等
  


## 13.Vuex
### 1.vuex的应用场景：
  - state：驱动应用的数据源；
  - view：以声明方式将 state 映射到视图；
  - actions：响应在 view 上的用户输入导致的状态变化。
  - 多个视图依赖于同一状态；来自不同视图的行为需要变更同一状态。
  - 使用vuex以全局单例模式进行管理，任何组件都能获取状态或者触发行为


### 2.vuex与单纯的全局对象的不同
  - vuex的状态存储是响应式的，状态发生变化时对应组件高效更新
  - 不能直接改变store中的状态，需要显示提交mutation，便于追踪状态变化
  - 全局变量多会造成命名污染，vuex不会，同时解决父孙组件兄弟组件之间通信的问题


### 3.vuex的调用：核心是store容器
  - 组件中调用：this.\$store
  - 存储清空：每次页面刷新的时候store都会清空
  - 组件内外定义：
     - 使用插件：Vue.use(Vuex)
     - 组件外定义store实例，store = new Vuex.Store({state,mutations})
     - vue根实例内：store:store
     - 组件内方法调用：this.\$store.commit('方法')
     - 组件内数据调用：this.\$store.state.数据

  
### 4.核心概念
- state：类似于vue实例中的data，调用：this.\$store.state.数据
- getters：类似于vue实例中的computed
  - 定义：
     - 属性：接受两个参数，(state，getters)，getters可以省略
     - 方法：接受参数传递：(state) => (参数) =>{}
  - 调用：
     - 通过属性访问：this.\$store.getters.属性
     - 通过方法访问：this.\$store.getters.方法(参数)
- mutations：类似于vue实例中的methods
  - 定义：方法名(参数){}，必要参数：state；可选参数：传入数据
  - 调用：this.\$store.commit(参数)，参数形式：
     - 方法
     - 方法+传入数据
     - 传入配置对象
  - 必须是同步函数，异步函数情况下状态变化无法追踪
- actions：类似于mutations
  - 定义：方法名(context){context.commit(参数)}，与mutations不同
  - 调用：this.\$store.dispatch(参数)
  - 相比于mutations而言，可以处理异步事务以及大批量的同步事务，可以使用promise对象以及async/await进行异步调用
- moudles：分成多个模块，每个模块均可定义根节点的所有属性
  - 根store定义：{a:moduleA,b:moduleB}
  - 模块内部的state：根store与模块store数据访问的区别在于需要加上对应的访问模块this.\$store.state.a(访问模块A的状态)
  - 模块内部的action、mutation和getter：注册在全局命名空间，若根store与模块store存在同名则调用时都发生事件，namespace:true可创建命名空间，创建后调用属性a/getters属性名



## 14.Vue Router
### 1.Html配置
- < router-link to="link" >：路由链接，对应path
- < router-view name="可选" >：路由匹配渲染目标，对应path指向的组件
 

### 2.常规路由配置
- path：路径
- name：路径命名
- component：路径对应的组件
- children：路径嵌套路由

### 3.路由定义
1、动态路由：
- `path:'/user/:id'`：通过`this.$route.params.id`获取id
- `path: '/user-*'`：通过`his.$route.params.pathMatch`获取*
2、路由重定向：`{ path: '/a', redirect: '/b' }`
3、路由别名：`{ path: '/a', component: A, alias: '/b' }`，url是/b，页面是/a
4、路由组件传参：`{path:'/user/:id', component:User, props:true}`，id将作为props传入组件
5、路由参数：
（1）query：路径附带参数形式
  - router-link定义：
     - 第一种定义：`{ name: 'W', query: { id:'1234'，age:'12' }}`
     - 第二种定义：`{ path: '/W', query: { id:'1234',age:'12' }}`
  - routes定义：
     - 第一种定义：`{ path: '/hhhhhhh'(任意),  name: 'W'(指定), component: W}`
     - 第二种定义：`{ path: '/W'(指定),  name: 'hhhhhhh'(任意), component: W}`

（2）params：路径内添加的形式
  - router-link定义：`< router-link :to="{ name: 'W', params: { id:'1234',age:'12' }}" />`
  - routes定义：`{ path:'/W/:id/:age', name:'W', component:W }`，path里面的/W可以任意写

### 4.组件定义
- 组件懒加载：避免加载页面时需要import加载全部组件，在进入路由时才加载
  -  第一种：`component: resolve => require(['@/components/index'],resolve)`
  -  第二种：`const About = () => import('@/components/about') `+ `component：About`
- 命名组件视图：当view中指定name属性时，需针对不同name属性显示不同视图
 `components: {default: component1, name: component2 }`


### 5.组件调用
- 监听路由参数变化：watch: { \$route(to, from) {} }
- 编程式导航：
  - router.push():前往对应路由并可携带参数,提供完整的path或name+params
  - router.replace():不会产生新history，而是更改history
  - router.go():前进或后退几步
- 访问路由器：this.\$router
- 访问当前路由：this.\$route


### 6.路由守卫
  - 导航被触发。
  - 在失活的组件里调用 beforeRouteLeave 守卫。
  - 调用全局的 beforeEach 守卫。(检测localStorage中的token检测是否登录)
  - 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
  - 在路由配置里调用 beforeEnter。
  - 解析异步路由组件。
  - 在被激活的组件里调用 beforeRouteEnter。
  - 调用全局的 beforeResolve 守卫 (2.5+)。
  - 导航被确认。
  - 调用全局的 afterEach 钩子。
  - 触发 DOM 更新。
  - 调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。


### 7.路由模式
- hash模式：
  - 缺点：url后面有#号，不美观
  - 优点：刷新页面不会重新向服务器请求，后端不需要对应配置
  - 特性：更改#号后面的hash值，不会对服务器重新请求页面，刷新后仍访问#号前url
- history模式：
  - 缺点：刷新页面会重新向服务器请求，后端需要对应配置
  - 优点：url没有#号，美观
  - 特性：通过pushState、replaceState更改url但不重新请求，但刷新后根据完整url请求
  



## 15.vue3.0
### 1.setup()
- 生命周期：位于created和beforeCreated之前，用于代替两者；内部执行生命周期函数
- 接收参数：props, context
```
props: {
    title: String
},
setup(props, context) {
    const { title } = toRefs(props) // props为响应式对象，需借助toRefs结构保留响应性
    console.log(title.value)
    const { attrs, slots, emit, expose } = context // context为非响应性对象，直接解构
}
```
- context
  - attrs：等同于\$attrs，伴随组件更新，避免解构，其属性为非响应式的
  - slots：等同于\$slots，伴随组件更新，避免解构，其属性为非响应式的
  - emit：等同于\$emit
  - exponse：`expose({ increment })`，子组件expose方法后父组件可通过ref访问到该方法
- 访问组件属性：
  - 可访问：props、attrs、slots、emit
  - 不可访问：data、computed、methods、ref
- 优先级：data、props、setup同名属性中，setup返回的属性优先级最高
- 适应vue2.0：
  - 整体变化：
     - Vue2.0采用对象形式创建组件
     - Vue3.0采用setup函数通过函数形式创建组件
  - 选项变化：
     - Vue2.0以对象属性的形式创建组件选项
     - Vue3.0以函数执行的形式创建组件选项，执行的函数如toRefs、ref、computed、watch等需要通过import from ‘vue’的形式引入
  - data选项：
     - 简单变量：const name = ref('name')，变量的值需用通过name.value获得
     - 对象变量：const data = reactive({data})，变量的值可直接通过对象属性获得
     - 变量响应性：return {...data}的形式解构变量将导致变量失去响应性
     - 解构变量：const refData = toRefs(data)中的refData可通过上面的方式解构并仍具备响应性
     - 定义数据属性：return { data } 返回数据属性
     - vue2.0：仍通过return的方式返回响应性数据name（自动解绑）/data
  - methods选项：
     - 定义函数变量：const function = () => { }
     - vue2.0：通过return 函数变量的方式返回方法
  - computed选项：
     - 定义计算属性：const attr = computed( () => { return data } )，attr为ref对象
     - vue2.0：通过return 计算属性的方式返回计算属性
  - watch选项：
     - 定义监听属性：watch(getter函数，回调函数，options)
     - getter函数：data / () =>  data.value 
     - 回调函数：监听多个值时，(v,o) => { }中v为监听值数组，通过数组形式获取变化
     - options：deep: true，immediate: true
     - vue2.0：直接定义在watch函数中即可实现监听
- 样例
```
  const name = ref('name')           //  name.value获取值
  const data = reactive({data})      //  data获取值
  const function = () => { }         //  methods
  const attr = computed( () => { } ) //  computed
  watch(                             //  watch
     () => data,
     (n, o) => { },
     {
         deep: true,
         immediate: false
     }
  )
  return {
     data,
     function,
     attr
  }
```


### 2.组合式API
- 生命周期函数：
  - 在vue2.0的生命周期函数的命名中加入前缀on，并且生命周期函数的操作写在setup中
  - setup在beforecreate与created前面执行，setup代替执行这两个函数
  - 生命周期函数需要通过import形式引入
- 依赖/注入：
  - 依赖provide：
     - 常量：`provide('user', 'lyz')`
     - 响应性：`const user = ref('lyz')  provide('user', user)`
     - 方法：`provide('getUser', getUser)`
     - 不可修改只读：`provide('user',readonly(user))`
  - 注入inject：`const user = inject('user', '默认值') // 可直接修改值`
- 模板引用
  - 组件上绑定：`ref="root"`
  - setup内定义：`root = ref(null) , return { root }`
  - setup内调用：`root`访问对应组件实例
- teleport：逻辑存在与父组件中，渲染在指定页面位置
  - `<teleport to="元素">`位置书写格式遵循选择器格式，决定渲染的页面位置
  - teleport中包含子组件，即使子组件在其他页面位置渲染，依旧采用teleport中的逻辑
- 触发组件选项emits：
  - 父组件绑定：`@event="method"`
  - 子组件数组定义：`emits:['event']`
  - 子组件对象定义：`emits:{ click:null //无验证,submit:(value)=>{}//返回布尔值表示参数是否有效`
  - 触发：`this.$emit('submit',value)`
  - 变化：不会从组件的根元素继承，也将从\$attrs属性中移除
- 全局API：
  - createApp({})创建全局的应用实例
  - 挂载vue根实例：app.mount('#app')
  - 插件使用：app.use(VueRouter)
  - 应用之间共享配置：创建工厂功能



### 3.模板指令
- `v-model`：
  - vue2.0：`v-bind:title.sync`
  - vue3.0：`v-model:title // 不带参数时prop更改为modelValue，event更改为update:modelValue`
  - 处理修饰符：`props: { modelModifiers: { default: () =>({})}` 通过'this.modelModifiers.修饰符名称'获取修饰符
- `v-if/v-else/v-else-if`：
  - vue2.0：可以使用相同的key或不同的key
  - vue3.0：自动生成key，若指定则需使用不同的key
- `<template v-for>`：
  - vue2.0：template不能有key，子节点可以设置key
  - vue3.0：key应该设置在template标签上
- v-if与v-for同时使用：v-if的优先级高
- v-bind：与单独属性定义冲突时，绑定顺序在后面的优先
- v-for与ref结合使用：不再产生\$refs数组
```
<div v-for="item in list" :ref="setItemRef"></div>
const setItemRef = el => {
    if (el) {
        itemRefs.push(el)
    }
}
```
- mixins选项：仅合并根级属性，根级属性内部存在差异时内部不再合并
- data选项：不再接受object，而需要使用function返回的object



### 4.渲染
- 响应性原理：
  - 定义：new Proxy(target,handler)
  - Proxy：将对象包装在Proxy中，对对象进行拦截处理
  - Reflect：返回值
  - track：跟踪对象
  - trigger：更改对象
``` javascript
const dinner = {
  meal: 'tacos'
}

const handler = {
  get(target, property, receiver) {
    track(target, property)
    return Reflect.get(...arguments)
  },
  set(target, property, value, receiver) {
    trigger(target, property)
    return Reflect.set(...arguments)
  }
}

const proxy = new Proxy(dinner, handler)
console.log(proxy.meal)
```
- template片段：支持多根节点
- 异步组件：
```
defineAsyncComponent(() => import('./Modal.vue'))

const asyncModalWithOptions = defineAsyncComponent({
  loader: () => import('./Modal.vue'),  // vue2为component选项
  delay: 200,
  timeout: 3000,
  errorComponent: ErrorComponent,
  loadingComponent: LoadingComponent
})
```
- 渲染：
  - h全局导入，不需要作为参数传递进渲染函数，render函数内直接h('div')渲染即可
  - 渲染函数配置项domProps展平
  - 渲染函数中不可使用字符串ID隐式查找已注册组件，需要导入resolveComponent('组件名称')显示查找
- 插槽：
  - 插槽内容：vue2.0渲染函数中slot作为节点属性定义；vue3.0渲染函数中slot作为节点子对象定义
  - 作用域插槽：this.\$scopedSlots访问转为this.\$slots访问
- 自定义指令：钩子函数替换成生命周期函数
  - beforeMount
  - mounted
  - beforeUpdate
  - updated
  - beforeUnmount
  - unmounted
- 过渡类名变更：
  - v-enter过渡类重命名为v-enter-from
  - v-leave过渡类重命名为v-leave-from


 
### 5.删除
- .native修饰符
- 键码修饰符
- functional属性
- \$children属性（访问子组件实例建议使用\$refs）
- \$listeners属性
- \$on，\$off、\$once 方法
- 过滤器：可替换成方法调用或计算的属性或定义全局过滤器app.config.globalProperties.\$filters，调用\$filters.function()
- 内联模板：父组件上定义属性inline-template则将内部内容视为其模板

 
     


## 16.API
- Vue.extend:返回扩展实例构造器，可理解为创建子类继承Vue身上部分功能
  - 创建vue组件Component
  - 创建子类：let ComponentCtrl = Vue.extend({..Component})
  - 创建实例：const modal = new ComponentCtrl({options})
  - 挂载实例：const modalVm = modal.\$mount()
  - 元素插入：container.appendChild(modalVm.\$el)

   
