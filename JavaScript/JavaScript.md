# JavaScript

标签（空格分隔）： JavaScript

---

## 1.变量
### 1.JS原生对象
  - 基本类型：number、string、boolean、null、undefined；引用类型：array、object
  - 原始类型：空值：null、undefined；包装对象：string、number、Boolean
  - 对象类型：构造器对象：obeject、function、date、array、error、regexp；单体内置对象：Math、JSON、window、arguments

### 2.RegExp类型
- RegExp:/正则表达式主题/修饰符(可选)
- RegExp.test():return boolean
- RegExp.exec():return array
- 动态设置限制位数：`reg = new RegExp('^(reg1) + (reg2' + 变量 + '})')`
- 获取匹配内容：RegExp.\$1 + RegExp.\$2，获取匹配的对应部分，'()'作为划分标识


### 3.Array类型
- 改变原数组：
  - `push()`：推入末尾
  - `pop()`：弹出末尾
  - `unshift()`：推入开头
  - `shift()`：弹出开头
  - `splice(index, number, a, b, ...)`：index位置替换number个元素为后面a，b...元素
  - `reverse()`：反转数组元素
  - `sort(function(a, b))`：function返回负值表示升序，正值表示降序
  - `forEach(function(item,index,array))`：遍历过程中修改数组
- 不改变原数组：
  - `concat(arr1, arr2, ...)`：拼接数组
  - `slice(first, *last*)`：[first, last)的数组，last空则剩余部分
  - `toString()`：逗号分隔组成字符串
  - `join(note)`：指定分隔符组成字符串
  - `filter(function(item,index,array))`：过滤数组内不符合要求的元素
  - `every/some(function(item,index,array))`：返回数组内元素是否都符合规则的布尔值
  - `reduce/reduceRight(function(prev,cur,index,array))`：返回归并后的值
  - 位置方法：indexof()、lastIndexof()、findIndex(function)、includes(value)
  - 检测：Array.isArray(array)


### 4.Date类型
  - 建立对象：
     - new Date()：可传入代表日期的毫秒数作为参数,空则表示现在
     - Date.now()：无需进行对象分析，直接返回现在
  - 将日期转化为毫秒数
     - Date.parse()：传入日期字符串
     - Date.UTC()：传入日期列表参数

### 5.String类型
- 搜索方法
  - `indexOf(text, *index*)`：返回指定文本text首次出现索引值，起始位置index
  - `lastIndexOf(text, *index*)`：返回指定文本text最后一次出现索引值，起始位置index
  - `search(text)`：返回搜索索引值，区别于indexOf，不支持index参数但text支持正则表达式
  - `match(reg)`：根据正则表达式返回匹配成功的字符串数组
  - `includes(text)`：判断是否包含某特定值
  - `startsWith(text)`：判断是否某特定值开头
  - `endsWith(text)`：判断是否某特定值结尾
- 获取方法
  - `slice(first, *last*)`：[first, last)字符串，无last表示剩余部分，接受负数
  - `substring(first, *last*)`：[first, last)字符串，无last表示剩余部分，不接受负数
  - `substr(first, *number*)`：[first, first+number)字符串，无number表示剩余部分
  - `charAt(index)`:返回对应索引位置的字符
- 返回新字符串
  - `replace(a,b)`：a替换成b，区分大小写且单个匹配，支持正则表达式
  - `toUpperCase()`：转大写
  - `toLowerCase()`：转小写
  - `concat(a, b, ...)`：拼接一个或多个字符串
  - `trim()`：去首尾空格
- 字符串转数组：`split(text)`根据分隔符返回字符串数组，空时全部返回
- localCompare():与字符串参数进行比较并返回-1、0、1
- String.fromCharCode():将参数内的字符编码转换为string并返回


### 6.Global对象
  - encodeURI(uri):将uri空格转换为utf-8编码(de+:解码)
  - encodeURIComponent(uri):将uri所有非字母数字替换成对应编码(de+:解码)
  - eval():参数被解析器解析成ES语句，其中定义的变量和函数不会进行提升，在严格模式下会错误
  - window：全局变量和函数都成为window的属性


### 7.Math对象
  - .max()与.min()：返回参数内的一个值
  - .ceil()与.round()与.floor()：对参数进行向上/四舍五入/向下舍入成整数
  - .random()：返回0~1的数字


### 8.对象属性类型
- 定义：Object.defineProperty(对象名称、属性名称、描述符对象）内部值
- for-in与for-of：
  - for-in：遍历对象的可枚举属性（包括原型链），遍历内容为键，适用普通对象
  - for-of：遍历可迭代对象的值，遍历内容为值，适用数组、字符串、Map、Set 等可迭代对象
- 数据属性：
```javascript
let obj = {};
Object.defineProperty(obj, 'name', {
    configurable: true, // 是否可通过delete删除属性从而重新定义属性，能否修改属性的特性，能否把属性修改为访问器属性，默认为false
    enumerable: true, // 是否可以通过for-in循环对象返回该属性，默认为false
    writable: true, // 是否可以修改属性的值，默认为false
    value: 'John' // 属性值，默认为undefined
});
```
- 访问器属性：
```javascript
let obj = {};
let _age = 25;
Object.defineProperty(obj, 'age', {
    configurable: true, // 与数据属性相同，表示属性是否可配置。默认值为false
    enumerable: true, // 与数据属性相同，表示属性是否可枚举。默认值为false
    // 一个函数，当读取属性时调用。默认值为undefined
    get: function() {
        return _age;
    },
    // 一个函数，当写入属性时调用。默认值为undefined
    set: function(newAge) {
        if (newAge > 0) {
            _age = newAge;
        }
    }
});
```
- 定义多个属性：Object.defineProperties(对象名称,{属性名称：描述符对象，})
- 读取属性特性：Object.getOwnPropertyDescriptor(对象名称，属性名称)返回一个描述符对象，包含所有特性


### 9.原型和原型链

#### 1.原型和原型链的基本概念
- 原型：每个 JavaScript 对象（除了 null）都有一个原型（prototype），原型类似于一个“类”的模板，定义了对象的共享属性和方法
- 原型链：当访问一个对象的属性或方法时，如果对象本身没有该属性或方法，JS会沿着原型链向上查找，直到找到该属性或方法或到达原型链的顶端（null）。原型链类似于类的继承链


#### 2.原型相关的属性和方法
- `prototype`：每个函数都有一个 prototype 属性，指向该函数的原型对象
- `__proto__`:每个对象（包括函数）都有一个 `__proto__` 属性，指向其构造函数的原型对象（即 constructor.prototype）
```javascript
function Person() {}
let person = new Person();
console.log(person.__proto__ === Person.prototype)
```
- `object.hasOwnProperty(attribute)`：用于检测属性是否存在于对象实例本身（而不是原型链上）
- `Object.getOwnPropertyNames(object)`：返回对象所有无论是否可枚举属性的字符串数组

#### 3.原型模式
- 动态修改原型
```javascript
// 原型可随时修改属性方法，实例均可调用
function Person() {}
let person1 = new Person();
Person.prototype.sayHello = function() { console.log("Hello"); };
person1.sayHello(); // Hello

// 重写原型，实例仍然指向原来的原型，无法访问新原型的属性或方法
Person.prototype = { sayHi: function() { console.log("Hi"); } };
let person2 = new Person();
person2.sayHi(); // Hi
person1.sayHi(); // 报错：person1 仍然指向旧的原型
```
- 原型的两种用途（继承复用）
  - 定义为类的情况：即该name类的超类，子类中存在的属性或者函数直接进行调用，若不存在则从该子类的prototype中调用类属性或者函数，逐级向上。object为所有类默认继承的超类
  - 定义为函数的情况：当同时创建多个对象时能减少资源的调用
- 原型链的问题
  - 如果原型中包含引用类型的属性（如数组或对象），所有实例会共享该属性，修改一个实例的属性会影响其他实例
  - 创建子类型实例时不能向超类型的构造函数传递参数
- 构造函数
  - 在构造函数中调用超类的.call()可向超类传递参数且每个实例拥有自己的引用值的属性
  - 问题：无法进行函数复用以及子类无法调用超类的方法
- 组合继承：结合构造函数以及原型链的方式，在构造函数中实现传递函数以及拥有独立属性，在原型链中定义函数实现函数复用，并且通过原型链实现函数继承
```javascript
function Person(name) {
    this.name = name;
    this.friends = ["Alice", "Bob"];
}
Person.prototype.sayHello = function() {
    console.log("Hello, " + this.name);
};

function Student(name, grade) {
    Person.call(this, name); // 继承属性
    this.grade = grade;
}
Student.prototype = Object.create(Person.prototype); // 继承方法
Student.prototype.constructor = Student; // 修复构造函数指向

let student1 = new Student("John", 10);
student1.sayHello(); // Hello, John
```
- class语法：ES6 引入了 class 语法，本质上是基于原型链的语法糖。它简化了继承的实现，但底层仍然是原型链
```javascript
class Person {
    constructor(name) {
        this.name = name;
    }
    sayHello() {
        console.log("Hello, " + this.name);
    }
}
class Student extends Person {
    constructor(name, grade) {
        super(name);
        this.grade = grade;
    }
}
```


### 10.变量
- 变量类型转换：
  - 手动转换：
     - 写入参数：Number()、parseInt()、parseFloat()、String()
     - 直接调用：.toString()、.valueOf()
  - 自动转换：num+string:返回字符串，null+num：null=0，null+string："nullstring"
- 变量特性：
  - 复制变量：基本类型完全独立的两个变量，引用类型指向同一个对象
  - 变量重复声明：重复声明后的值会覆盖先前的值，未赋值时不会覆盖先前的值
  - 传递参数：均为值传递，引用类型传入时形参与实参指向同一个对象，但在new方法后形参指向新对象
  - 变量提升：函数变量和变量声明会被解释器提升到方法体顶部。
     - 定义变量时只使用var定义变量,不分配变量初始值,此时函数的优先级更高,函数会覆盖变量;
     - 定义变量时为变量指定了初始值,此时变量的优先级更高,变量会覆盖函数。
  - 浮点型数据：精度无法确定，直接运算产生错误，可将浮点型数据运算为整型数据进行运算
- 数据类型检测
  - var instanceof dataType 检测引用类型 返回boolean
  - typeof var 返回数据类型
  - ==：转化为同一类型值进行比较；===：类型不同则不相等，但是对于array、object无区别
  - Array.isArray()检测数组、isNAN()检测数字
- this：
  - 函数中 return this，方便进行链式调用（函数执行后可执行其属性方法），返回函数对象
  - 函数内this的最终指向的是那个调用它的上一级对象，函数上一级对象没有调用则指向window
  - 函数内return 对象则指向该对象，return 非对象则依旧指向函数实例
  - apply模式：将this对象更换为传入的对象
- new：
  - 存在new：创建一个空对象并给该对象进行赋值，构造函数的this指向该对象
  - 不存在new：将函数返回值进行赋值，构造函数内的this指向window对象
  - new后的括号优先级高：(new Person()).num / new (Person.num)
  - new后的构造函数可无括号：new Person = new Person()
- 判断对象为空：Object.keys(obj).length
- 判断数组为空：=== []不可取，.length === 0可取
- 删除属性：delete obj[prop]

#### 1.垃圾收集机制
- 堆与栈
  - 栈：自动分配相对固定大小的内存空间，并由系统自动释放，先进先出。用于存储基本数据类型
  - 堆：动态分配内存，内存大小不固定，也不会自动释放。用于存储引用数据类型
- 标记清除
  - 原理：垃圾回收器会定期从根对象开始，遍历所有可达的对象，并标记它们为“活动对象”。遍历完成后，所有未被标记的对象被认为是“垃圾”，会被回收。最后，垃圾回收器会清除这些未被标记的对象，释放它们占用的内存
  - 优点：解决了循环引用的问题，是目前主流的垃圾回收算法
  - 缺点：在标记和清除过程中，可能会导致程序暂停（称为“Stop-the-World”），影响性能
- 引用计数
  - 原理：每个对象都有一个引用计数器，记录有多少个变量引用它。当引用计数为 0 时，表示该对象不再被使用，垃圾回收器会立即回收它
  - 优点：实时性高，对象不再被引用时会立即被回收
  - 缺点：无法处理循环引用的问题，非主流回收算法
- 管理内存
  - 原理：将不再使用的全局变量、对象属性或 DOM 引用设置为 null，可以显式地告诉垃圾回收器这些对象可以被回收。
  - 全局变量、缓存、事件监听器等长期存在的对象，如果不手动解除引用，可能会导致内存泄漏
- 现代垃圾回收机制的优化
  - 分代回收：将内存分为新生代和老生代。新生代存放短期存活的对象，使用Scavenge算法进行快速回收。老生代存放长期存活的对象，使用标记清除或标记整理算法进行回收。
  - 增量回收：将垃圾回收过程分成多个小步骤，避免长时间暂停程序
  - 空闲时间回收：在程序空闲时执行垃圾回收，减少对性能的影响



## 2.函数
### 1.函数特性
  - 函数可重复声明，函数名相同即重复声明，与参数无关
  - 两种函数：普通函数与函数对象。函数对象调用：a返回整个函数对象；a()返回函数返回值
  - 递归函数消除函数名耦合：递归名部分可改为arguments.callee，严格模式下错误
  - 函数名.caller：返回调用该函数的函数的引用

### 2.参数特性
  - 实参比形参少：形参为undefined；
  - 形参比实参少：不可调用对应的实参。
  - arguments[]：可以获取参数

### 3.函数与对象解耦合
  - function.apply(this,array)：传入调用function函数的this对象，并通过array传入参数
  - function.call(this,parm1,parm2)：功能与上面类似，参数一个个传入
  - function.bind(object)：this值等于object

### 4.自调用函数
   - 模仿块级作用域(私有作用域)
   - ( function () { } ) ()，自调用函数创建时即执行，执行完即销毁，避免外部函数变量冲突
   - 如果变量被赋值给包含作用域(外部作用域)中的变量，则该变量不会被立即销毁

### 5.闭包函数
- 定义：一个函数内部创建另一个函数，并且内部函数可以访问外部函数的变量，即使外部函数已经执行完毕
```javascript
function outerFunction() {
    let outerVariable = "I'm outside!";

    function innerFunction() {
        console.log(outerVariable); // 访问外部函数的变量
    }

    return innerFunction;
}

let closure = outerFunction();
closure(); // 输出: I'm outside!
```
- 特点：
  - 变量共享：闭包函数变量可以调用外部函数变量，只有内部函数解除引用才可取消引用
  - 私有变量：外部函数变量将成为私有变量
  - 作用域链：内部函数、外部函数、全局环境
- 应用场景
  - 数据封装：闭包实现私有变量
  - 函数工厂：使用闭包创建具有特定行为的函数
  - 回调函数：闭包常用于异步编程中，保留回调函数执行时的上下文
- 风险：闭包函数中引用 DOM 元素或大型对象，可能会导致内存泄漏，需手动解除引用
- 异步编程
```javascript
for (var i = 1; i <= 3; i++) {
    setTimeout(function() {
        console.log(i); // 输出: 4, 4, 4
    }, 1000);
}

// 使用闭包修复
for (var i = 1; i <= 3; i++) {
    (function(i) {
        setTimeout(function() {
            console.log(i); // 输出: 1, 2, 3
        }, 1000);
    })(i);
}
```

### 6.作用域与作用域链
   - 内层作用域可以访问外层作用域、反之则不可
   - 函数可创建一个作用域，块语句可创建块级作用域适用于let和const
   - 在函数的创建阶段，就会将所有的父变量对象保存在[[scope]]中
   - 当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级(词法层面上的父级)执行上下文的变量对象中查找，一直找到全局上下文的变量对象，也就是全局对象。这样由多个执行上下文的变量对象构成的链表就叫做作用域链。

### 7.私有变量
  - 函数内部通过只有对应的方法可以调用变量，该变量则为私有变量
  - 在私有作用域中采用原型的方式，对象默认为全局对象即可进行方法调用，但私有变量为静态私有变量全实例共享
  - 模块模式 var id = function(){}()使用返回对象的匿名函数

## 3.DOM
- 操作：
  - document.write()：位于输出流时会按照文档顺序进行写，在函数中执行时会覆盖整个文档进行写
  - element.innerHTML=""改变内容
  - element.attribute=""改变属性
  - element.style.property=""改变样式
  - document.getElementById()
  - document.getElementByTagName()
  - document.getElementByClassName()
  - document.createElement()
- 节点层次：
  - 节点关系：childNodes、previousSibling、nextSibling、parentNode
  - 操作节点：appendChild()、insertBefore()、replaceChild()、removeChild()
  - 在不同浏览器，节点不一定包含文本节点，需对结点的nodeType进行验证才可进行对应操作
  - document:获取文档属性
     - url、domain、referrer：通过设置domain为父域名、两个子域名不同页面的js对象可以互相访问
     - element：
         - .className：访问class类，返回一个字符串
         - .attribute:可直接访问结点属性，style中访问对象
         - .getAttribute():访问对应参数的属性，style中访问css文本
         - .setAttribute(a,b):修改对应特性a的属性为b
         - .removeAttribute():移除参数特性的值并完全删除参数特性
- DOM操作技术：
  - 动态脚本：动态插入script标签，进行js文件加载
     - 兼容问题：IE中不允许DOM访问script子节点，script.appendChild替换成script.text，为全版本兼容可使用try-catch语句实现两种方式的调用
  - 动态样式：动态插入link标签，实现css文件加载，或调用style标签进行修改
     - 兼容问题：IE中不允许DOM访问style子节点，style.appendChild替换成style.styleSheet.cssText,为全版本兼容可使用try-catch语句实现两种方式的调用
- 选择符API：可通过document或element实例进行调用
  - .querySelector():css选择器的第一个元素
  - .querySelectorAll():css选择器的全部元素
- 元素遍历：节点关系中的方法在IE9及以前版本会返回文本节点，因此需要仅遍历元素节点的方法，例如：childElementCount、firstElementChild
- 类操作：.className返回字符串，.classList返回类数组(仅在firefox与chrome中支持)
- 焦点管理：document.activeElement返回获得焦点的元素，document.hasFocus()检测文档是否获得焦点
- document.compatMode:严格模式：CSS1Compat；混杂模式：BackCompat
- 元素自定义属性：属性名加上前缀-data后即可，可通过.dataset进行全部自定义属性的访问
- DOM2和DOM3：
  - 偏移量：相对于offsetParent
     - element.offsetHeight/offsetWidth:元素除去外边距的大小,包括滚动条
     - element.offsetTop/offsetLeft:元素边框至包含元素边框的距离
  - 客户区大小
     - element.clientHight/clientWidth:元素内容及内边距，不包括滚动条
  - 滚动大小：
     - element.scrollHight/scrollWidth:带有滚动条页面的总宽度高度
     - element.scrollTop/scrollLeft:滚动后隐藏的页面，包括边框
- 元素的dataset属性可调用html标签中的data-*属性
 

 
## 4.BOM
- 访问未声明变量抛出错误，访问window对象回显示undefined
- window.frames[]与top.frames[]访问框架
- 窗口：
  - window.screenLeft(screenX)/window.sreenTop(screenY):窗口距离屏幕的距离
  - window.innerHeight/window.innerWidth:除去工具栏和滚动条的宽度高度
  - window.outerHeight/window.outerWidth:包括工具栏和滚动条的宽度高度
  - IE6中获取窗口宽度高度：
     - 标准模式：document.documentElement.clientWidth
     - 混杂模式：document.body.clientWidth
  - window.open()：导航和打开窗口，四个参数
     - 加载的url：类似于a标签的超链接
     - 窗口目标：窗口对应已有的窗口或框架则在里面加载url，若没有则打开新窗口
     - 一个特性字符串：设置窗口属性
     - 历史记录取代：bool值
     - .close()：关闭窗口
     - .opener=:null：切断新窗口与原窗口的通信
     - 浏览器存在禁止弹出窗口的设置，执行open操作时许进行验证
- 超时调用：setTimeout(function(){},delayTime)在执行栈执行完毕后，按照delayTime将function放入执行队列中；间隔调用：setInterval()隔一定的时间重复执行，直至页面卸载
- 系统对话框：执行是代码停止，关闭后代码继续
  - alert():根据字符串给出警示框
  - comfirm():显示提示字符串，根据用户确认或退出确定代码执行
  - prompt():显示提示字符串，并有默认的文本值
- location：属性：hash、host、hostname、href、pathname、port、protocol、search
  - 位置操作：重新记载页面
    - 设置location的值可以重新加载页面，历史记录会生成一条新纪录
    - .replace(URL):不会生成新纪录
    - .reload():可能从缓存中重新加载页面，参数为true时从服务器重新加载，后面的代码可能无法执行
- navigator：识别客户端浏览器各项属性
- screen：屏幕属性
- history：页面历史记录


     
## 5.事件
### 1.事件流
  - 事件冒泡：从最具体的元素开始，再向上传播到不具体的节点(IE)
  - 事情捕获：从不具体的元素开始，再向下传播到最具体的元素(Netscape)
  - 事件流的三个阶段：事件捕获阶段、处于目标阶段、事件冒泡阶段

### 2.事件处理
  - html中设置onclick="function()"，点击在函数定义之前会有错误，需要使用try-catch语句，不建议使用
  - js中设置element.onclick=function(){}，将.onclick设为null将不再支持事件处理
  - element.addEventListener(event,function,boolean) 
      - boolean：true在捕获阶段调用事件；false在冒泡阶段调用事件，默认为false
      - removeEventListener():参数与add相同，function需要单独定义并引入才有效
  - 兼容问题：IE浏览器中
      - element.attachEvent("on"+event,function)
      - element.detachEvent()：参数与add相同，可进行撤销
  - 处理浏览器差异：Eventutil.addhandler(元素，事件，处理函数(event))自定义

### 3.事件对象
  - event：function()中带有参数对应元素event，event.type返回事件类型
     - .target:事件的目标元素
     - .currentTarget:事件的绑定元素
     - .preventDefault():组织默认事件
     - .stopPropagation():组织事件在DOM上的传播
     - .eventPhase::确定事件处于哪个阶段，分别按顺序为1,2,3
  - 兼容问题：IE浏览器中
     - srcElement：事件的目标元素
     - window.event:取得event对象，如果使用attachEvent则可以直接传入event参数使用
     - returnValue：值为false时取消默认事件
     - cancelBubble：值为true组织事件冒泡

### 4.事件类型
  - 事件支持检测：document.implementation.hasFeature(event,version)
  - load：完全加载后触发事件，<script>与<link>只有在指定scr等属性并就加入到文档才会进行加载
  - unload：完全卸载切换页面后发生，不可以调用文档元素
  - resize：调整窗口高度或宽度
  - scroll：滚动事件
  - focus/blur：获取焦点/失去焦点时执行
  - click/dblclick/mousedown/mouseup：鼠标事件
     - event.clientX/clientY:鼠标事件客户区坐标位置，相对于客户区，不包括上方工作栏与滚动条
     - event.pageX/pageY：鼠标事件页面坐标位置，相对于页面，无滚动情况下与前面值相同
     - event.screenX/screenY：鼠标事件屏幕坐标位置
     - event.shiftKey/ctrlKey:修改键改变事件操作
  - keydown(任意键)/keypress(字符键)/keyup：键盘事件
  - textInput：文本事件

### 5.事件委托
  - 利用事件冒泡机制将事件监听器绑定到父元素上，来管理多个子元素的事件。利用事件冒泡，将子元素的事件处理委托给父元素
  - 常见形式：onchange、onclick、onmouseover、onmouseout、onkeydown、onload



## 6.表单脚本
- 获取表单：document.forms[]
- 提交表单：存在下列元素时，在表单任一控件获得焦点时，回车键可提交该表单
  - 提交按钮：input、button元素，type = submit
  - 图像按钮：input元素，type = "image"
  - submit()：不会触发submit事件，submit事件可用于验证数据
  - 避免多次点击重复提交表单：第一次后禁用按钮，onsubmit事件取消后续提交操作
- 重置表单：重置表单所有值，默认值恢复默认值
  - 重置按钮：input、button元素，type = "reset"
  - reset()：触发reset事件
- 表单字段：
  - form.elements[name]/[num]：访问对应的元素
  - 共有的表单字段属性：name、type、value、disabled等
  - 共有的表单字段方法：focus()/blur()等
  - 共有的表单字段事件：blur、change、focus等
- 文本框脚本：
  - textarea与input text 均可设置属性完成相同效果
  - 选择文本框所有文本：select
  - 获取选择的文本：元素属性selectionStart、selectionEnd获取位置，IE8不支持
  - 选取部分文本：元素方法setSelectionRange()，首尾位置两个参数
- 过滤输入
  - 利用keypress与正则表达式屏蔽字符
  - 操作剪贴板：copy、cut、paste赋值剪切粘贴时触发事件
- 验证API：onsubmit="return function()",当function返回false时表单无法提交
  - 必填字段：required
  - 输入类型：name属性
  - 数值范围：max/min/step等
  - 禁用验证：表单内设置novalidate，控件内设置formnovalidate
  - element.checkValidity()：input元素中的数据合法返回true，否则返回false
- 富文本编辑：页面内嵌入一个包含空HTML页面的iframe
  - 通过对iframe进行操作修改HTML界面，提交时将innerHTML写入表单进行提交

  
## 7.严格模式
- 与网页中的 DTD(Document Type Definition) 直接相关，HTML5不存在分别
- 在脚本或函数开头使用"use strict"使用严格模式
- 变量必须声明后再使用
- 函数的参数不能有同名属性，否则报错
- 不能使用with语句
- 不能对只读属性赋值，否则报错
- 不能使用前缀0表示八进制数，否则报错
- 不能删除不可删除的属性，否则报错
- 不能删除变量delete prop，会报错，只能删除属性delete global[prop]
- eval不会在它的外层作用域引入变量
- eval和arguments不能被重新赋值
- arguments不会自动反映函数参数的变化
- 不能使用arguments.callee
- 不能使用arguments.caller
- 禁止this指向全局对象
- 不能使用fn.caller和fn.arguments获取函数调用的堆栈
- 增加了保留字（比如protected、static和interface）、



## 8.canvas绘图
- HTML：canvas元素引入，设置相应属性
- 获得绘图上下文：元素.getContext("2d")
- 导出绘制图像：元素.toDataURL("image/png")，获得图像的URI
- 2D上下文：基于context对象的操作
  - 填充和描边：fillStyle、strokeStyle，值可为颜色、渐变或图像
  - 绘制矩形：
     - 方法：fillRect()、strokeRect()、clearRect():填充矩形、描边矩形、消除矩形
     - 参数：矩形x坐标、矩形y坐标、矩形宽度、矩形高度
  - 绘制路径：
     - 直线：lineTo(x,y)、moveTo(x,y)
     - 弧线：arc(x,y,radius,startAngle,endAngle,counterclockwise)，最后参数为false为顺时针
  - 绘制文本：
     - 属性：context.font/textAlign/textBaseline
     - 方法：context.fillText()/strokeText()
     - 参数：字符串、x坐标、y坐标、最大像素宽度
  - 变换：
     - 方法：rotate()、scale()、translate()：旋转、缩放、移动原点
  - 设置：保存的是设置和变换而不是内容
     - 方法：save()、restore()：保存当前所有配置，返回上次所有配置
  - 绘制图像：
     - 方法：drawImage()
     - 参数：图像，源图像坐标大小，目标图像坐标大小
  - 阴影：
     - 属性：shadowColor、shadowOffsetX、shadowOffsetY、shadowBlur
  - 渐变：
     - createLinearGradient()：创建线性渐变对象，四个参数对应起始坐标
     - createRadialGradient()：创建径向渐变对象，六个参数对应起始坐标及半径
     - addColorStop():为渐变对象创建对应区间节点及颜色
     - fillStyle:设置为渐变对象，即可对图形进行渐变设置
     - 图形与渐变的坐标要匹配才能实现
  - 模式：重复的图形进行填充或描边
     - createPattern():参数为img元素和重复的形式
     - fillStyle：设置为模式对象即可实现
  - 使用图像数据：
     - getImageData()：四个参数对应坐标及大小
     - ImageData：得到的实例中有三个属性，宽度高度以及data数组
     - data：4个元素表示红绿蓝和透明度，分别表示1个像素
     - putImageData():参数为data,0,0，显示修改结果
  - 合成：
     - globalAlpha：设置全局透明度
     - globalCompositionOperation：设置合成方式
- WebGL：3D上下文

## 9.HTML5脚本编程
- 跨文档消息传递：
  - window.postMessage(信息，目的域):传送信息
  - window接收消息触发window对象的message事件
     - event.origin:发送消息的域
     - event.data:发送的消息
     - event.source:发送消息的域的window对象代理，通过此发送回执
- 原生拖放：
  - 拖放事件：
     - dragstart：按下鼠标并移动时触发
     - drag：拖动元素过程中持续触发
     - dragend：拖动停止时触发
     - dragenter：元素拖动到放置目标
     - dragover：元素在放置目标移动持续触发
     - dragleave：元素被拖出放置目标
     - drop：元素放到了放置目标
  - 自定义放置目标：
     - 使用preventDefault事件使不可放置目标可放置
     - Firefox：默认打开元素的url，使用preventDefault事件
  - dragTransfer：事件对象的属性
     - getData()/setData():为该对象设置信息，两个参数：第一个为数据类型(text/URL)，第二个为数据
     - 在执行拖放的过程中会调用方法，也可手动设置方法
     - dropEffect：被拖动的元素能执行何种放置行为，例如move/copy/link，ondragenter事件设置
     - effectAllowed：允许拖动元素的何种dropEffect，例如move/copy/link，ondragstart事件设置
  - 可拖动：
     - 默认情况下图片链接文本都可移动
     - 元素的draggable设置决定是否可拖动
- 媒体元素：
  - video与audio为视频音频标签，标签内内容在不支持的情况下显示
  - 标签内设置source标签指定不同的媒体来源
  - 属性：
     - src：指定媒体文件来源，在有source时在source标签内设置
     - controls：用于显示或隐藏浏览器内置的控件
     - poster：加载内容期间显示图像的url
     - width/height：占据大小
     - paused：播放器是否暂停
  - 事件：
     - play：播放
     - pause：暂停
     - load/canplaythrough：加载完成
  - 检测编解码器：canPlayType()接收一种格式/编解码器字符串，检测是否支持
  - Audio类型：使用new Audio()创建对象并调用play()即可播放音频
  - 历史状态管理：页面不重载的情况下不同状态间切换
    - hash：指的是url中"#"字符后面的部分，定位到对应的值，不会导致页面重载和不发送请求
    - hashChange：URL片段标识符更改(包括#)
    - history对象：
        - pushState()：状态对象加入历史状态栈
        - popstate：window的popstate事件对象有state属性，processState(state)实现后退
        - replaceState()：重写当前的状态

## 10.错误处理与调试
- IE：页面左下角显示JavaScript错误，使用InternetOptions中自动显示错误
- FireFox：页面不显示JavaScript错误，使用Error Console显示错误，FireBug插件错误显示
- Chrome：F12实现控制台显示错误
- 错误处理：
  - try-catch：catch接收error对象，对象有错误类型的name属性与错误信息的message属性
  - 错误类型：error instanceof检测错误类型
  - 抛出错误：throw new Error("")
  - error事件：没有try-catch事件处理的错误会触发window默认的error事件，onerror事件处理程序 return false阻止浏览器报告错误的默认行为
  - 避免调用不可调用的属性方法：基本类型使用typeof，对象类型使用instanceof检测类型
  - 避免通信错误：对url进行encodeURIComponent进行数据编码
  - 错误写入服务器：通过image.src对服务器请求将错误日志写入服务器
- 调试技术：
  - console对象，IE8、FireFox、Chrome、Safari支持
     - .error(message)：错误信息
     - .info(message)：信息性消息
     - .log(message)：一般消息
     - .warn(message)：警告消息
  - opera.postError(message)：Opera支持
  - java.lang.System.out.println(message)：FireFox、Safari、Opera支持
  - 抛出错误：assert()，第一个参数为判定条件返回布尔值，第二个参数为条件为false时抛出的错误
  - 常见的IE错误：操作终止、无效字符、未找到成员，未知运行错误、语法错误、未找到指定资源

## 11.JSON
- JSON属性及字符串要使用双引号
- JSON.stringify(data)：js对象转化为json字符串
  - 可选第二个参数：过滤结果
     - 数组：接收数组内元素对应的属性
     - 函数：根据不同的key返回自定义的value
  - 可选第三个参数：字符串缩进，同时换行
     - 数字：表示缩进空格数，最大为10，超过10自动转换为10
     - 字符串：字符串用作缩进字符
  - toJSON():序列化优先调用
- JSON.parse(data)：json字符串转化为js对象
  - 可选第二个参数：还原函数根据不同的key返回自定义的value

## 12.AJAX
### 1.XMLHttpRequest对象
  - 创建：new XMLHttpRequest
  - xhr.open()启动一个请求以备发送
     - 参数一：发送请求的类型：get、post等
     - 参数二：请求的url
     - 参数三：是否异步发送请求的布尔值
  - xhr.send()发送请求
     - 参数一：请求主体发送的数据
  - xhr.responseText：响应主体返回文本
  - xhr.responseXML：响应类型为text/xml或application/xml时
  - xhr.status：响应的http状态(200~300||304)
  - xhr.statusText：http状态的说明
  - xhr.onreadystatechange：open()之前指定事件处理程序进行异步调用跨浏览器兼容性
  - xhr.readyState：异步请求时属性为4表示接收到全部响应数据
  - xhr.abort()：取消异步操作，停止触发事件，不再允许访问任何与响应有关的对象属性
  - xhr.setRequestHeader(key,value)：open()之后，send()之前，用于修改http头部信息，模拟表单提交时设置Content-Type为application/x-www-form-urlencoded
  - xhr.getResponseHeader(key)/getAllResponseHeaders()：获取头部信息
  - GET请求：对url后的查询字符串参数进行encodeURIComponent编码
  - POST请求：通过send(data)发送数据，data格式与查询字符串格式相同


### 2.XMLHttpRequest 2级
  - FromData(form)：表单序列化并且不需明确设置表头信息即可识别
  - xhr.timeout：超时设定，ontimeout事件处理程序
  - xhr.overrideMimeType()：修改返回响应的MIME类型


### 3.进度事件
  - xhr.onload：接收到完整响应数据时触发
  - xhr.onprogress：接收数据期间周期性触发，接受event对象
     - event.lengthComputable：进度信息是否可用的布尔值
     - event.position：已经接收的字节数
     - event.totalSize：预期字节数


### 4.跨域源资源共享CORS
- 基本概念：允许浏览器向跨域服务器发送请求，并接收响应。它是通过自定义的 HTTP 头部来实现的
- 简单请求：无需预检，响应速度快
  - 请求方法限制为 HEAD、GET、POST
  - Content-Type 限制为 application/x-www-form-urlencoded、multipart/form-data、text/plain
  - 不包含自定义头部信息
- 非简单请求：需要预检，增强安全性
  - 其他请求方法（如 PUT、DELETE）或包含自定义头部信息的请求
  - 非简单请求会先发送一个预检请求（OPTIONS），服务器确认允许后再发送实际请求
- CORS 的 HTTP 头部
  - Access-Control-Allow-Origin：域名，对应域名开启CORS
  - Access-Control-Allow-Credentials：true，服务端允许浏览器发送cookie
  - Access-Control-Expose-Headers：服务端允许浏览器获取的其他头部信息
  - Access-Control-Request-Method：浏览器的CORS请求会用到的方法
  - Access-Control-Request-Headers：浏览器的CORS请求会发送的字段
  - Access-Control-Allow-Methods：服务器接受的所有跨域请求的方法
  - Access-Control-Allow-Headers：服务器支持的所有头信息字段
- 跨浏览器CORS支持
  - XHR（XMLHttpRequest）：现代浏览器原生支持 CORS
  - XDR（XDomainRequest）：IE8/IE9 中的跨域请求对象，功能有限，仅支持异步操作
  - 跨浏览器兼容性
     - 检测 withCredentials 属性，存在则使用 XHR
     - 否则检测 XDomainRequest，存在则使用 XDR


### 5.其他跨域技术
特点：相比起CORS不需要修改服务器代码，跨域拦截的是xhr类型的请求

#### 1.图像ping
- 原理：利用 <img> 标签的 src 属性加载跨域图像，常用于追踪用户点击或广告曝光。
- 优点：简单易用，无需修改服务器代码。
- 缺点：只能发送 GET 请求，无法获取服务器响应数据。
```javascript
let img = new Image();
img.src = "https://example.com/track?event=click";
```

#### 2.JSONP
- 原理：通过 <script> 标签加载跨域脚本，服务器返回一个函数调用，将数据作为参数传递给客户端定义的回调函数。
- 优点：支持跨域，兼容性好。
- 缺点：只支持 GET 请求，安全性较低。
```javascript
function handleResponse(data) {
    console.log("Received data:", data);
}

let script = document.createElement("script");
script.src = "https://example.com/api?callback=handleResponse";
document.body.appendChild(script);
```

#### 3.Comet
- 原理：服务器向客户端推送数据，常用于实时应用。
- 实现方式：
  - 长轮询：浏览器定时发送请求，服务器保持连接直到有数据可发送。
  - 流：HTTP 连接保持打开，服务器周期性地发送数据。

#### 4.SSE
- 原理：服务器向客户端单向发送数据，基于 HTTP 协议。
- 优点：简单易用，支持断线重连。
- 缺点：仅支持服务器到客户端的单向通信
```javascript
let source = new EventSource("https://example.com/events");
source.onmessage = function(event) {
    console.log("Received data:", event.data);
};
```

#### 5.Web Sockets
- 原理：基于自定义协议的全双工通信，适用于实时应用。
- 优点：支持双向通信，性能高。
- 缺点：需要服务器支持 WebSocket 协议
```javascript
let socket = new WebSocket("wss://example.com/socket");
socket.onmessage = function(event) {
    console.log("Received data:", event.data);
};
socket.send("Hello Server");
```

#### 6.document.domain
- 原理：将不同子域的 document.domain 设置为同一主域，实现跨域访问。
- 限制：仅适用于同一主域下的不同子域
```javascript
// 页面 A (https://a.example.com)
document.domain = "example.com";

// 页面 B (https://b.example.com)
document.domain = "example.com";
```

#### 7.window.name
- 原理：利用 window.name 在不同页面间传递数据。
- 优点：支持跨域，数据容量较大。
- 缺点：实现复杂，安全性较低。

#### 8.location.hash
- 原理：通过修改 location.hash 在不同页面间传递数据。
- 优点：支持跨域，实现简单。
- 缺点：数据容量有限，安全性较低。

#### 9.postMessage
- 原理：通过 window.postMessage 在不同窗口或 iframe 间传递数据。
- 优点：支持跨域，安全性高。
- 缺点：需要目标窗口的引用。
```javascript
// 发送消息
let targetWindow = window.open("https://example.com");
targetWindow.postMessage("Hello", "https://example.com");

// 接收消息
window.addEventListener("message", function(event) {
    if (event.origin === "https://example.com") {
        console.log("Received message:", event.data);
    }
});
```


## 13.高级技巧
- 惰性载入函数：函数调用的if语句不必每次执行，第一次调用即可确定则可以使用
  - 1.在第一次调用时，该函数被覆盖为另一个按合适方式执行的函数
  - 2.在声明函数时通过if语句就制定适当的函数进行return
- 函数绑定：函数绑定在特定的执行环境中
  - 函数.bind(对象)，在绑定的同时参数也都会传给绑定的函数
- 防篡改的对象：无法撤销
  - 不可扩展对象：Object.preventExtensions(对象)：不能添加新对象
  - 密封对象：Object.seal(对象)：不可扩展且不可删除方法和属性
  - 冻结对象：Object.freeze(对象)：不可扩展且密封且不可修改属性和方法
- 高级定时器：
  - 超时调用setTimeout(function,delay)：在规定的时间后将function放入执行队列，等待空闲时执行
  - 间歇调用setInterval(function,delay)：在规定的时间间隔内重复地将function放入执行队列中，存在间隔时间跳过的问题，可以使用setTimeout(arguments.callee,delay)获取对当前函数的引用实现重复调用
- Yielding Processes：数组分块技术进行setTimeout分块执行，避免长时间脚本阻塞界面
- 自定义事件：观察者模式：主体与观察者(事件处理程序)
- javascript:void():仅执行void内的表达式不返回值
- void():仅执行void内表达式不返回值


## 14.离线应用与客户端存储
- 离线检测：navigator.onLine属性false代表网络离线
- 应用缓存：appcache浏览器缓存中分出来的一块缓存区
- 数据存储：
  - Cookie：
     - 向服务器发送请求时都包含Cookie
     - 可以设置失效时间expires，默认是结束即删除
     - 同源窗口内共享
     - document.cookie获取所有cookie的字符串，由分号隔开。其中可以包含多个不同的cookie键值对
  - Storage：getItem(),removeItem(),setItem()操作Storage
  - sessionStorage：
     - 向服务器发送请求时不包含sessionStorage
     - 浏览器关闭即失效
     - 仅在当前浏览窗口有效
  - localStorage：window.localStorage
     - 向服务器发送请求时不包含localStorage
     - 永久保存数据，除非在浏览器中显式删除
     - 同源窗口内共享
     - 过期时间设置：设置开始时间以及期望时间，当调用getItem时通过运算决定remove
  - token：
     - 相比于于cookie，cookie不支持移动端的存储，cookie需要服务器设置多个sessionID占用空间，而token仅需要服务端进行生成和校验
     - 发送到服务端，服务端通过密钥与算法生成签名返回给客户端
     - 客户端对服务端发送请求附带token进行对比确定登录信息


## 15.实践
- 提高可维护性手段
  - 代码注释：函数方法、复杂算法、大段代码需要注释
  - 分离关注点：HTML与JS分离，CSS与JS分
  - 解耦应用逻辑和时间处理程序：提高代码的可读性和可维护性
  - 避免修改已有对象：不去修改已有的对象属性或方法
  - 减少全局变量：将变量集成到一个对象当中，避免全局变量污染
  - 避免与null做比较
  - 常量通过具体名称变量进行调用
- 性能优化手段
  - 减少属性查找：将在一个函数中使用多次的全局对象存储为局部对象/将对象属性存储在局部对象中
  - 优化循环：减值迭代、简化终止条件、简化循环体、后测试循环(do-while)、展开循环
  - 最小语句数：单个var语句通过逗号分开命名变量、使用数组或对象、插入迭代
  - 优化DOM交互  
     - 使用 DocumentFragment：将新建节点插入DocumentFragment再一次性插入页面避免页面闪烁
     - 使用innerHTML：插入大量 HTML 代码时，使用 innerHTML 比逐个创建节点更高效
     - 事件代理：将事件处理程序绑定到父元素，利用事件冒泡减少事件处理程序数量
  

## 16.异步编程
- setTimeout(function,delay):在time时间之后执行回调函数function
- 异步ajax：XMLHttpRequest.onload=function(){}在请求发送成功时执行函数
- Promise函数实现异步的良好编程风格
  - Promise函数：return Promise对象
  - Promise对象：new Promise(function(resolve,reject){settimeout(function,delay)})
  - resolve():执行成功向下一个then传递一个值；reject():执行失败传递异常给catch，但函数会继续执行除非使用throw跳转至catch实现中断
  - Promise函数.then(Promise函数).then(Promise函数).catch(Promise函数).finally(Promise函数);通过then(Promise函数)为异步排顺序，在内部通过返回一个Promise对象使下一个then相对于这个Promise对象进行操作
  - 异步函数asyc function(){await Promise函数;await Promise函数.....}
- nextTick(() => {})：一个事件循环(一个宏任务以及其内的微任务)后执行对DOM的操作



## 17.ES6
### 1.Babel
  - ES6转码器，将ES6代码转为ES5代码
  - .babelrc:配置presets的规则集
  - babel-cli：命令行转码
  - babel-node：直接执行ES6代码
  - bable-register：改写require命令，通过require加载ES6文件前使用Babel解码
  - bable-core：对某些代码进行转码
  - babel-polyfill：Babel默认值转换新语法，不转换新API，使用即可进行转码


### 2.let和const变量
  - 不属于顶层对象(window)的属性
  - 不存在变量提升
  - 不允许重复声明
  - 作用域为块级作用域
  - const变量不可修改，但若作为对象可以修改对象属性和方法
  - 暂时性死区：在一个代码块内外定义同名的let与var变量，let变量会绑定该块级作用域，在let变量声明前形成暂时性死区
  - 自调用函数可以通过块级作用域实现，let变量成为私有变量
  - 块级作用域内可声明函数，ES5将所有函数声明提升到头部，ES6的声明函数提升到块级作用域头部
     - 允许在块级作用域内声明函数
     - 函数声明类似于var，即会提升到全局作用域或函数作用域的头部。(仅提升函数名)
     - 同时，函数声明还会提升到所在的块级作用域的头部。
     - 因为环境差异需要避免块级作用域内声明函数，必要时写成函数表达式

### 3.变量的解构赋值
- 定义：解构赋值是一种从数组或对象中提取值，并赋值给变量的语法。
- 条件：只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。
- 数组的解构赋值
```javascript
let [a, b, c] = [1, 2, 3];
console.log(a, b, c); // 输出: 1 2 3

let [a = 1, b = 2, c = 3] = [10, undefined];
console.log(a, b, c); // 输出: 10 2 3

let [a, ...b] = [1, 2, 3, 4];
console.log(a, b); // 输出: 1 [2, 3, 4]

let [a, b, c] = new Set(["a", "b", "c"]);
console.log(a, b, c); // 输出: a b c
```

- 对象解构赋值
```javascript
let { a, b } = { a: 1, b: 2 };
console.log(a, b); // 输出: 1 2

let { a: x, b: y } = { a: 1, b: 2 };
console.log(x, y); // 输出: 1 2

let { a = 1, b = 2 } = { a: 10 };
console.log(a, b); // 输出: 10 2

let { 0: a, 1: b } = [1, 2];
console.log(a, b); // 输出: 1 2
```
- 用途：
```javascript
// 交换变量
let x = 1, y = 2;
[x, y] = [y, x];
console.log(x, y); // 输出: 2 1

// 函数返回多个值
function f() {
    return [1, 2, 3];
}
let [a, b, c] = f();
console.log(a, b, c); // 输出: 1 2 3

// 函数参数定义
function f([x, y, z]) {
    console.log(x, y, z);
}
f([1, 2, 3]); // 输出: 1 2 3

// 提取json数据
let jsonData = { a: 1, b: 2, c: 3 };
let { a, b, c } = jsonData;
console.log(a, b, c); // 输出: 1 2 3

// 函数参数默认值
function f(a, { b = 1, c = 2 } = {}) {
    console.log(a, b, c);
}
f(10); // 输出: 10 1 2
f(10, { b: 20 }); // 输出: 10 20 2

// 遍历Map结构
let map = new Map();
map.set("a", 1);
map.set("b", 2);

for (let [key, value] of map) {
    console.log(key, value);
}
// 输出:
// a 1
// b 2

// 输入模块指定方法
const { method1, method2 } = require("module");
```


### 4.字符串扩展
- 背景：JavaScript 最初使用 UTF-16 编码，每个字符通常占用 2 个字节。然而，一些字符（如中文、Emoji 等）需要 4 个字节表示，传统的字符串处理方法无法正确处理这些字符
- 目的：扩展字符串的功能，使其能够正确处理多字节字符（如中文符号、Emoji 等）
- Unicode 表示法
  - 单字符：`\uxxxx`，例如`\u0041`表示字符 A
  - 多字节字符：`\u{xxxxxx}`，例如`\u{4E2D}`表示中
- 扩展方法
```javascript
// 返回字符串中指定位置的字符的 Unicode 码点
let str = "中文 ";
console.log(str.codePointAt(0)); // 输出: 20013

// 根据 Unicode 码点返回对应的字符
console.log(String.fromCodePoint(20013)); // 输出: 中

// 返回字符串中指定位置的字符
let str = "你好";
console.log(str.at(0)); // 输出: 你
console.log(str.at(-1)); // 输出: 好 

// 判断字符串是否以指定的子字符串开头、结尾或包含指定的子字符串
let str = "你好，世界！";
console.log(str.startsWith("你好")); // 输出: true
console.log(str.endsWith("世界！")); // 输出: true
console.log(str.includes("，")); // 输出: true

// 将字符串重复指定的次数
let str = "中文";
console.log(str.repeat(3)); // 输出: 中文中文中文

// 在字符串的头部或尾部填充指定的字符，直到字符串达到指定的长度
let str = "123";
console.log(str.padStart(5, "0")); // 输出: 00123
console.log(str.padEnd(5, "0")); // 输出: 12300
```
- 字符串遍历
```javascript
// 传统for循环无法处理多字节字符
let str = "中文";
for (let i = 0; i < str.length; i++) {
    console.log(str[i]); // 输出:    
}

// for...of 循环能够正确处理多字节字符
let str = "中文";
for (let char of str) {
    console.log(char); // 输出: 中 文
}
```
- 模板字符串
```javascript
// 支持多行文本、嵌入变量、表达式和函数（通过 ${}）
let name = "小明";
let age = 20;
let str = `姓名：${name}
年龄：${age}
明年你将 ${age + 1} 岁。`;
console.log(str);
// 输出:
// 姓名：小明
// 年龄：20
// 明年你将 21 岁。
```


  

### 5.函数扩展
- 函数可设定默认参数
```javascript
// 非尾部参数设置默认值，调用时省略该参数后会报错，除非显示输入undefined
function example(a, { b = value1, c = value2 } = {}) {
  console.log(a, b, c);
}
// 默认值可以是表达式或函数调用，甚至是其他参数的引用
function foo(a, b = a * 2) {
  console.log(a, b);
}
foo(10); // 输出 10, 20
```
- 函数变量的length属性：返回函数定义时没有默认值的参数个数，如果设置默认值不是尾参数，则不再记入后面参数，不包含rest参数
- 函数默认值作用域：先是函数的作用域，然后才是全局作用域
```javascript
let x = 1;
function foo(y = x) {
  let x = 2;
  console.log(y);
}
foo(); // 输出 1，因为 y 的默认值取自全局的 x
```
- 默认值应用场景：
  - 定义默认值为抛出错误函数，省略则执行
  - 定义默认值为undefined，表明参数可省略
- rest参数：使用 ...变量名 获取多余的参数，替代传统的 arguments 对象
```javascript
function sum(...numbers) {
  return numbers.reduce((acc, num) => acc + num, 0);
}
sum(1, 2, 3); // 输出 6
```
- 扩展运算符`...[a,b,c]`
  - 作用：扩展运算符将数组拆分成一个个参数进行调用，可替代apply方法
  - 应用：
     - 合并数组：[...arr1, ...arr2]
     - 与解构赋值结合：[a, ...rest] = [1, 2, 3]
     - 函数返回值：return [...arr]
     - 字符串、Map、Set、Generator等可迭代对象的展开
- 严格模式：
  - 参数使用了默认值、解构赋值或者扩展运算符，函数内就不能显示设定为严格模式
  - 解决办法：全局性的严格模式或将函数包在一个无参数的立即执行函数里面
- name属性：
  - function.name返回函数的函数名
  - (new Function).name返回anoymous
  - function.bind({}).name返回bound function

#### 1.箭头函数 
- 示例：`var a = v => v` 函数名、参数、返回值
- 参数部分：不需要参数或多个参数时，由()代替
- 返回值部分：代码块部分多于一条语句，由{}代替
- 直接返回对象：`const getObj = () => ({ name: 'Alice', age: 25 });`
- 函数内的this对象 
  - 特性：是定义时所在的对象，而不是调用时所在的对象
  - 运用在setTimeout：普通函数执行时this指向window
  - 运用在封装回调函数：普通函数执行时this指向window
  - 箭头函数没有自己的this，而是引用外层的this
- 不存在arguments、super、new.target变量，也不能适用call()、apply()、bind()方法
- 嵌套的箭头函数：前一个函数的输出是后一个函数的输入
```javascript
const add = a => b => a + b;
console.log(add(1)(2)); // 输出 3
```
- 绑定this：对象::函数，将对象绑定在函数上面
```javascript
// ES7语法，需要兼容可以使用bind方法，箭头函数没有自己的this不能改变this指向
const obj = {
  name: 'Alice',
  greet: function() {
    console.log(`Hello, ${this.name}!`);
  }
};

const greetFunc = obj.greet;
greetFunc(); // 输出 "Hello, undefined!"，因为 this 指向全局对象（或 undefined）

// 使用 :: 绑定 this
const boundGreetFunc = obj::greetFunc;
boundGreetFunc(); // 输出 "Hello, Alice!"，因为 this 指向 obj
```


#### 2.尾调用
- 定义：函数以`return function();`结尾即为尾调用
- 优化：执行到最后一步，在不再用到外部函数的内部变量时，删除外部调用帧，保留内部调用帧，节省内存
- 尾递归：尾调用自身，将所有用到的内部变量改写成函数的参数
- 只在严格模式下开启
- ES2017 允许函数最后一个参数加尾逗号
```javascript
function factorial(n, total = 1) {
  if (n === 1) return total;
  return factorial(n - 1, n * total);
}
```


### 6.对象扩展
- 属性简写：允许直接写入变量作为对象的属性，属性名为变量名，属性值为变量值`return {x:x,y:y} => return {x,y}`
- 方法简写：允许直接写入函数作为对象的方法`method:function(){} => method(){}`
- 用表达式作为属性名`[表达式]`，属性名表达式为对象时会自动转换为[object,object]字符串
- 新增方法：
  - 实现对象合并，进行浅拷贝，为对象添加属性方法：`Object.assign({},obj)`
  - 返回对象的键/值/对组合成的数组：`Object.keys(obj)/values(obj)/entris(obj)`

### 7.数组扩展
- 扩展运算符：复制数组、apply参数替换、参数传递、数组合并、字符串转数组
- Array.from(object)：具有迭代器的对象转化为数组
- Array.of()：将一组值转化为数组
- array.fill()：数组填充
- array.include()：数组检测值存在，返回布尔值


### 8.class
- 示例
```javascript
// 定义
class Person {
  constructor(name, age) {
    this.name = name; // 实例属性
    this.age = age;
  }

  // 实例方法
  greet() {
    console.log(`Hello, my name is ${this.name}`);
  }

  // 静态方法
  static info() {
    console.log('This is a Person class');
  }
}

// 调用
const person = new Person('Alice', 25);
person.greet(); // 输出 "Hello, my name is Alice"

// 方法不可枚举
for (let key in person) {
  console.log(key); // 只输出 "name" 和 "age"，不会输出 "greet"
}
```

- constructor()：默认返回一个实例对象(即this)
- 不存在变量提升，class定义不会进行提升
- class表达式：
```javascript
const MyClass = class Me {
  getClassName() {
    return Me.name; // Me 只能在类内部使用
  }
};

const instance = new MyClass();
console.log(instance.getClassName()); // 输出 "Me"

// 匿名类
const MyClass = class {};

// 立即执行的类
const person = new class {
  constructor(name) {
    this.name = name;
  }
  greet() {
    console.log(`Hello, ${this.name}`);
  }
}('Alice');

person.greet(); // 输出 "Hello, Alice"
```
- 私有方法：
```javascript
// 移出类外：将私有方法定义为模块内部的函数
function privateMethod() {
  console.log('This is private');
}

class MyClass {
  publicMethod() {
    privateMethod();
  }
}

// 利用 Symbol：使用 Symbol 作为方法名，避免外部直接调用
const privateMethod = Symbol('privateMethod');

class MyClass {
  [privateMethod]() {
    console.log('This is private');
  }
  publicMethod() {
    this[privateMethod]();
  }
}
```
- 类方法this对象始终绑定对应实例
```javascript
// bind 绑定：在 constructor 中绑定 this
class MyClass {
  constructor() {
    this.method = this.method.bind(this);
  }
  method() {
    console.log(this);
  }
}

// 箭头函数：使用箭头函数定义方法，箭头函数的 this 始终指向实例对象
class MyClass {
  method = () => {
    console.log(this);
  };
}
```
- 继承：
```javascript
// 通过 extends 关键字实现类的继承
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} makes a noise`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    // 必须在子类的 constructor 中首先调用 super()
    // super 指向父类的原型对象，可以通过 super 调用父类的方法
    // super 调用父类方法时，会绑定子类的 this
    super(name); // 调用父类的 constructor
    this.breed = breed;
  }
  speak() {
    super.speak(); // 调用父类的方法
    console.log(`${this.name} barks`);
  }
}

const dog = new Dog('Rex', 'Labrador');
dog.speak(); // 输出 "Rex makes a noise" 和 "Rex barks"
```
- 原型链
```javascript
// 子类的 __proto__ 属性指向父类，表示构造函数的继承
// 子类 prototype 属性的 __proto__ 属性指向父类的 prototype，表示方法的继承
console.log(Dog.__proto__ === Animal); // true
console.log(Dog.prototype.__proto__ === Animal.prototype); // true
```
- 静态方法与静态属性
```javascript
class MathUtils {
  static add(a, b) {
    return a + b;
  }
}
console.log(MathUtils.add(1, 2)); // 输出 3

class Config {
  static version = '1.0.0';
}
console.log(Config.version); // 输出 "1.0.0"
```


### 9.Symbol
  - 用途：独一无二的值，不会产生命名冲突
  - 定义：let s = Symbol()，可接受一个字符串作为参数，参数为对象会调用toString转为字符串
  - 调用：调用变量s返回 Symbol()，如果有参数也会返回参数
  - 具有相同参数的Symbol函数返回值不相等，保证独一无二
  - 不能与其他类型值进行运算，可以显示转为字符串与布尔值
  - 作为对象属性名：[s]，不能使用点运算符，使用点运算符时相当于字符串，且为公开属性
  - 使用Symbol定义常量值从而保证常量值不相等
  - 实例：消除魔术字符串(代码中多次出现的字符串或数值字符串)，Symbol代替常量
  - 遍历：
     - 不会出现在for-in、for-of循环中
     - 也不会被Object.keys()、Object.getOwnPropertyNames()、JSON.stringify()返回
     - Object.getOwnPropertySymbols方法，可以获取指定对象的所有 Symbol 属性名
     - Reflect.ownKeys方法可以返回所有类型的键名
  - Symbol.for()，Symbol.keyFor()：
     - Symbol.for()：使用同一个参数可以创建两个相同的Symbol值，定义时进行登记，再次创建检索
     - Symbol.keyFor(s)：返回一个已登记的 Symbol 类型值的key，即定义时的参数
  - 实例：Singleton模式(调用一个类任何时候返回的都是同一个实例)
     - 将实例放到顶层对象global的属性上，缺点在其他文件调用属性可以修改
     - 使用Symbol方法生成，外部就无法引用修改对应属性，每次执行得到的symbol对象不一样
  - 11个内置的Symbol值，指向语言内部方法：
     - Symbol.hasInstance：指向一个内部方法。当其他对象使用instanceof运算符判断是否为该对象的实例时，会调用这个方法
     - Symbol.isConcatSpreadable：等于一个布尔值，表示该对象使用Array.prototype.concat()时，值为true或undefined时可展开，false不可展开
     - Symbol.species：指向一个方法。该对象作为构造函数创造实例时，会调用这个方法
     - Symbol.match：指向一个函数。当执行str.match(myObject)时会调用myObject的该属性函数
     - Symbol.replace/Symbol.search/Symbol.split：字符串函数：对象[] (this,参数)
     - Symbol.iterator：指向默认的遍历器方法


### 10.Set数据结构
  - 创建：new Set()，内部可传入数组进行初始化
  - set内没有重复的元素，可以通过set去除数组重复成员[...new Set(array)]
  - set不会进行类型转换，类似于===，两个对象总是不相等的
  - set属性：constructor：构造函数，默认Set函数、size：成员总数
  - set方法：add(value):添加值、delete(value):删除值、has(value):判断值存在、clear()清除全部
  - set转为数组：Array.form(items)，items为set结构
  - set遍历：键名键值是同一个值，默认可遍历，可不使用方法，等同于values()
     - keys():键名遍历器、values():键值遍历器、entires():键值对遍历器
     - forEach(function):函数参数对每个成员进行操作
     - 并集、交集、差集、操作均可通过集合与数组间的转换完成
  - WeakSet：
     - 成员只能是对象，当其他对象不再引用内部的对象时，会对内部的对象进行回收


### 11.Map数据结构
  - 创建：new Map()，内部可以传入一个个表示键值对的数组
  - 类似于对象，但是键的范围不限于字符串，可以是各种类型
  - 只有对同一个对象引用才是同一个键，两个对象数值相等但内存地址不同则不是同一个键
  - map属性：size：成员总数、
  - map方法：set(key,value):添加键值、get(key):读取对应键值、delete(key):删除键、has(key):判断键存在、clear()清除全部
  - map遍历：默认可遍历，可不使用方法，等同于entires()
     - keys():键名遍历器、values():键值遍历器、entires():键值对遍历器
     - forEach(function):函数参数对每个成员进行操作
  - WeakMap：
     - 成员只能是对象，当其他对象不再引用内部的对象时，会对内部的对象进行回收


### 12.Proxy
  - 含义：对target各种形式的访问进行拦截
  - 定义 var proxy = new Proxy(target, handler);
  - handle内可设置不同的拦截方法：
     - get(target, propKey, receiver)：属性读取，返回value
     - set(target, propKey, value, receiver)：属性设置，返回布尔值
     - has(target, propKey)：判断键存在，返回布尔值
     - deleteProperty(target, propKey)：删除键，返回布尔值
     - ownKeys(target)：返回对象内可遍历的属性
     - getOwnPropertyDescriptor(target, propKey)：返回属性的描述对象
     - defineProperty(target, propKey, propDesc)：定义描述对象，返回布尔值
     - preventExtensions(target)
     - getPrototypeOf(target)
     - isExtensible(target)
     - setPrototypeOf(target, proto)
     - apply(target, object, args)：作为函数调用的操作
     - construct(target, args)：作为构造函数调用的操作
  - Proxy.revocable方法返回一个可取消的Proxy实例{proxy,revoke}，调用revoke()取消实例
  - 操作时针对proxy，而不是针对target，如果handler为空则访问proxy等于访问target，target内this指向proxy


### 13.Reflect
  - 含义：相当于调用变形的Object对应的函数方法
  - 将Object对象的明显属于语言内部的方法（比如Object.defineProperty），放到Reflect对象上。
  - 修改某些Object方法的返回结果，让其变得更合理
  - 让Object操作都变成函数行为
  - 让Proxy对象可以方便地调用对应的Reflect方法，完成默认行为，作为修改行为的基础
  - receiver改变this成对应的receiver


### 14.Generator
  - 定义：function* 函数名() { yield语句；return语句;} 返回一个迭代器对象，不使用yield等同于暂缓执行函数
  - 调用：var 变量 = 函数()
  - .next()：返回语句内的value、done，done为布尔值，遍历结束时为true
  - yield语句
     - 遍历暂停标志
     - 返回yield后面表达式的值
     - 不能使用在普通函数中
     - 用在一个表达式之中，必须放在圆括号里面
     - 用作函数参数或赋值表达式的右边，可以不加括号


### 15.Promise
- 创建：`new Promise((resolve,reject) => {})`
  - resolve()：成功时接受一个结果作为参数进行传递
  - reject()：失败时接受一个错误作为参数进行传递 
- resolve返传入值：
  - 普通值：作为then回调的参数
  - Promise对象：该Promise对象决定原Promise状态，相当于原Promise后续拼接在该Promise上
- Promise静态方法：直接将对象转化为对应状态 
  - Promise.resolve()
  - Promise.reject()
- 状态：
  - Pending（进行中）
  - Resolved（已完成，又称Fulfilled）
  - Rejected（已失败）
- `then(res => {}, error => {})`
  - 作为回调函数，分别响应不同状态的处理
  - 带有第二个参数时截取reject，不再执行后续的catch，该值缺省
  - 返回什么值相当于resolve什么值，值为promise时则该Promise对象决定原Promise状态
  - 调用多次则执行多次
- `catch(error => {})`
  - 截取reject进行处理
  - catch执行后返回promise对象，可继续执行then/catch
- Promise函数方法：
  - `all([]).then(res=>{})`：对Promise对象数组进行批处理，then方法中获取返回值数组；只要有一个Promise状态为rejected，便返回rejected
  - `allSettled([]).then(res=>{})`：相比于all，then方法中可获取所有Promise返回值数组
  - `race([])`：对Promise进行批处理，将多个Promise对象打包成一个，只要其中一个Promise对象改变状态，就返回状态值
  - `any([])`：相比于race，只获取第一个resolve的Promise，全部rejected则报错AggregateError
- 微任务执行队列
  - 按照放入队列的先后顺序执行
  - 微任务执行过程中先执行完同步代码再将后续微任务添加到队列当中
  - 当微任务执行过程中再产生新的微任务，将新的微任务放到队列，再将后续微任务放到队列



## 18.JS原生方法实现
### 1.防抖
```
function debounce(fn, delay) {
  let timer
  return function (...args) {
    if (timer) {
      clearTimeout(timer)
    }
    timer = setTimeout(() => {
      fn.apply(this, args)
    }, delay)
  }
}

```

### 2.节流
```
function throttle(fn, delay) {
  let last = 0 // 上次触发时间
  return (...args) => {
    const now = Date.now()
    if (now - last > delay) {
      last = now
      fn.apply(this, args)
    }
  }
}

```

### 3.深拷贝
```
function deepClone(obj) {
    if (!obj || typeof obj !== 'object') return obj  //对象不存在或为基本类型return 
    if (obj instanceof Date) return new Date(obj)  // Date类型
    if (obj instanceof RegExp) return new RegExp(obj)  // RegExp类型
  
    let i,ret
    // Array 类型
    if (obj instanceof Array) {
        ret = []
        for (i in obj) {
            ret.push(deepClone(obj[i]))
        }
    }
    if (obj instanceof Object) {
        ret = {}
        for (i in obj) {
           ret[i] = deepClone(obj[i]) 
        }
    }
    return ret
}

```

### 4.Promise
```
class MyPromise {
  constructor(executor) { // executor执行器
    this.status = 'pending' // 等待状态
    this.value = null // 成功或失败的参数
    this.fulfilledCallbacks = [] // 成功的函数队列
    this.rejectedCallbacks = [] // 失败的函数队列
    const that = this
    function resolve(value) { // 成功的方法
      if (that.status === 'pending') {
        that.status = 'resolved'
        that.value = value
        that.fulfilledCallbacks.forEach(myFn => myFn(that.value)) //执行回调方法
      }
    }
    function reject(value) { //失败的方法
      if (that.status === 'pending') {
        that.status = 'rejected'
        that.value = value
        that.rejectedCallbacks.forEach(myFn => myFn(that.value)) //执行回调方法
      }
    }
    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }
  then(onFulfilled, onRejected) {
    if (this.status === 'pending') {
      // 等待状态，添加回调函数到成功的函数队列
      this.fulfilledCallbacks.push(() => {
        onFulfilled(this.value)
      })
      // 等待状态，添加回调函数到失败的函数队列
      this.rejectedCallbacks.push(() => {
        onRejected(this.value)
      })
    }
    if (this.status === 'resolved') { // 支持同步调用
      onFulfilled(this.value)
    }
    if (this.status === 'rejected') { // 支持同步调用
      onRejected(this.value)
    }
  }
  all(promises) {
    let results = [];
    let promiseCount = 0;
    let promisesLength = promises.length;
    return new Promise(function(resolve, reject) {
      for (let val of promises) {
        Promise.resolve(val).then(function(res) {
          promiseCount++
          results.push(res)
          // 当所有函数都正确执行了，resolve输出所有返回结果。
          if (promiseCount === promisesLength) {
              return resolve(results)
            }
        }, function(err) {
          return reject(err)
        })
      }
    })
  }
}

```


### 5.获取url参数
```
// 创建一个URLSearchParams实例
const urlSearchParams = new URLSearchParams(window.location.search);
// 把键值对列表转换为一个对象
const params = Object.fromEntries(urlSearchParams.entries());
```


### 6.事件总线|发布订阅
```
class EventEmitter {
  constructor() {
    this.cache = {}
  }

  on(name, fn) {
      this.cache[name] = fn
  }

  off(name) {
    const task = this.cache[name]
    if(task) {
       delete this.cache[name]
    }
  }
  
  emit(name, ...args) {
    let handler = this.cache[name]
    if (args.length > 0) {
       handler.apply(this, args);
    } else {
       handler.call(this);
    }
  }
}
```


### 7.new
```
function myNew () {
	//创建一个新对象
	var obj = new Object ();
	//取出参数中的第一个参数，获得构造函数
	var constructor = Array.prototype.shift.call(arguments);
	//连接原型，新对象可以访问原型中的属性
	obj._proto_ = constructor.prototype;
	// 执行构造函数，即绑定 this，并且为这个新对象添加属性
	var result = constructor.apply(obj,arguments);
	return typeof result === "object" ? result :obj ;
}
```


### 8.call
```
Function.prototype.myCall = function(context) {
  context.fn = this;
  let args = [];
  if(arguments.length > 1) {
     for (let i = 1; i < arguments.length; i++) {
       args.push(arguments[i]);
     }
  }
  let result = context.fn(...args);
  delete context.fn;
  return result;
};

```


### 9.apply
```
Function.prototype.myapply = function(context) {
  context.fn = this;
  let args = [];
  if(arguments[1] && arguments[1] instanceof Array) {
     args = arguments[1]
  }
  let result = context.fn(...args);
  delete context.fn;
  return result;
};
```


### 10.bind
```
Function.prototype.bind = function () {
    var self = this,                        // 保存原函数
    context = [].shift.call(arguments), // 保存需要绑定的this上下文
    args = [].slice.call(arguments);    // 剩余的参数转为数组
    return function () {                    // 返回一个新函数
        self.apply(context,[].concat.call(args, [].slice.call(arguments)));
    }
}
```
