# JS设计模式

标签（空格分隔）：JavaScript

---

## 1.面向对象编程
### 1.使用对象收编变量
```
// 优点：避免全局变量冲突与覆盖 
// 缺点：不利于复用
var CheckObj = {
    checkName: fucntion() {
    },
    checkEmail: fucntion() {
    },
}

CheckObj.checkName()
```

### 2.使用类定义
```
// 优点：功能可被复用
// 缺点：每个对象都有方法，造成消耗
var CheckObj = function() {
    this.checkName = fucntion() {
    },
    this.checkEmail = fucntion() {
    },
}

var a = new CheckObj()
a.checkName()

```

### 3.使用原型链
```
// 优点：对象调用的都是同一个方法，减少消耗
var CheckObj = function() {}
CheckObj.prototype = {
    checkName = fucntion() {
        return this  // 返回this便于连续调用
    },
    checkEmail = fucntion() {
        return this
    },
}
```

### 4.函数式对象
```
var Book = function(id, name, price) {
    // 私有变量
    var num = 1;
    // 私有方法
    function checkId () {}
    // 特权方法：对象通过该方法访问私有属性和调用私有方法
    this.getName = function() {}
    this.getPrice = function() {}
    this.setName = function() {}
    this.setPrice = function() {}
    // 公用变量
    this.id = id
    // 公用方法
    this.clone = function() {}
    // 构造器
    this.setName(name)
    this.setPrice(price)
}

// 类静态公有变量，通过类访问，不可通过对象访问
Book.isChinese = true
Book.resetTime = function() {}

// 原型上属于对象公有变量，可通过对象访问
Book.prototype = {
    isJSBook: flase
}
```

### 5.闭包函数
```
var Book = (function() {
    // 静态私有变量，仅闭包内可访问
    var bookNum = 0;
    // 静态私有方法，仅闭包内可访问
    function checkBook(name) { }
    // 创建闭包类
    function _book(id, name, price) {
        // 私有变量
        var num = 1;
        // 私有方法
        function checkId () {}
        // 特权方法：对象通过该方法访问私有属性和调用私有方法
        this.getName = function() {}
        this.getPrice = function() {}
        this.setName = function() {}
        this.setPrice = function() {}
        // 公用变量
        this.id = id
        // 公用方法
        this.clone = function() {}
        // 构造器
        this.setName(name)
        this.setPrice(price)
    }
    _book.prototype = {
        isJSBook: flase
    }
    return _book;
})()
```


### 6.创建对象的安全模式
```
// 避免因为new的遗漏导致新建不必要的全局变量
var Book = function(id, name, price) {
    if(this instanceof Book) {
        this.id = id
        this.name = name
        this.price = price
    }
    else return new Book(id, name, price)
}

var book = Book('1', 'JS', '32')
```


### 7.继承
- 类式继承：新建父类实例绑定在子类原型链上`SubClass.prototype = new SuperClass()`
  - 优点：继承父类原型链
  - 缺点：多个子类公用父类属性，子类对属性的修改相互影响
- 构造函数继承：子类中调用父类构造函数`SuperClass.call(this, id)`
  - 优点：多个子类有不同的父类属性备份，不会相互影响
  - 缺点：无法继承父类原型链
- 组合继承：
```
// 优点：属性不相互影响且继承父类原型链
// 缺点：两次调用父类构造函数
function SuperClass(name){
    this.name = name;
    this.books = ['html', 'css', 'javascript']
}

SuperClass.prototype.getName = function() {
    console.log(this.name);
}

function SubClass(name, time) {
     SuperClass.call(this, name)
     this.time = time
}

SubClass.prototype = new SuperClass()
```



## 2.创建型设计模式
### 1.简单工厂模式
```
// 特性：共性赋值，针对差异进行特殊处理
// 差异：类直接对变量进行处理，简单工厂生产对象赋值给变量
function createPop(type, text) {
   var o = new Object()
   o.content = text
   o.show = function() {}
   if(type === 'alert') {}
   if(type === 'prompt') {}
}
```


### 2.工厂方法模式
```
// 优点：扩展类直接添加到原型中，不需要根据扩展对工厂函数进行修改
var Factory = function(type, content) {
    // 保证正确调用工厂函数
    if(this instanceof Factory) {
        var s = new this[type](content)
        return s
    } else {
        return new Factory(type, content)
    }
}

Factory.prototype = {
    Java：function() {}
    JavaScript: function() {}
}
```


### 3.抽象工厂模式
```
// 优点：通过抽象类方法明确子类的类别，并且明确子类必备的属性和方法
// 定义抽象工厂，使子类能够继承抽象类
const VehicleFactory = function(subType, superType) {
  if (typeof VehicleFactory[superType] === 'function') {
    function F() {
      this.type = '车辆'
    } 
    F.prototype = new VehicleFactory[superType]()
    subType.constructor = subType
    subType.prototype = new F() 
  } else throw new Error('不存在该抽象类')
}

// 定义抽象类属性
VehicleFactory.Car = function() {
  this.type = 'car'
}
// 定义抽象类方法，子类若不覆写则报错
VehicleFactory.Car.prototype = {
  getPrice: function() {
    return new Error('抽象方法不可使用')
  },
  getSpeed: function() {
    return new Error('抽象方法不可使用')
  }
}

const BMW = function(price, speed) {
  this.price = price
  this.speed = speed
}
VehicleFactory(BMW, 'Car')  
BMW.prototype.getPrice = function() {
  console.log(`BWM price is ${this.price}`)
}
BMW.prototype.getSpeed = function() {
  console.log(`BWM speed is ${this.speed}`)
}

const baomai5 = new BMW(30, 99)
baomai5.getPrice()                          
baomai5 instanceof VehicleFactory.Car  
```


### 4.建造者模式
```
// 特性：负责对多对象进行组建
// 区别：建造者模式更注重的是零件的组装过程，而工厂方法注重的是零件的创建过程
function LivingRoom(wall,tv,sofa) {
    var wall,tv;
    this.setWall = function (wall) {
        this.wall = wall;
    };
    this.setTv = function (tv) {
        this.tv = tv;
    this.show = function () {
        console.log(this.wall,this.tv,this.sofa)
    }
}

//抽象建造者-包含创建子部件的抽象方法及返回产品的方法
function Builder() {
    this.product = new LivingRoom();
    this.getResult = function () {
        return this.product;
    }
}
Builder.prototype.buildWall = function () {}
Builder.prototype.buildTv = function () {}

//具体建造者
function ConcreteBuilder() {}
ConcreteBuilder.prototype = new Builder();
ConcreteBuilder.prototype.buildWall = function (){
    this.product.setWall("刷墙");
    console.log(this.product)
};
ConcreteBuilder.prototype.buildTv = function (){
    this.product.setTv("安装电视");
};

//指挥者
function Director(builder) {
    //获取产品方法
   this.getProduct = function () {
       //刷墙
       builder.buildWall();
       //安装电视
       builder.buildTv();
       //返回客厅
       return builder.getResult();
   }
}

var concreteBuild = new ConcreteBuilder();
var director= new Director(concreteBuild);
var product = director.getProduct();
product.show();
```


### 5.原型模式
```
// 特性：构造函数+原型链的组合式继承
// 优点：将共性写在原型链中，避免多次创建重复方法
var LoopImage = function(imgArr, container) {
    this.imagesArray = imgArr;
    this.container = container;
}
LoopImage.prototype = {
    createImage: function() {}
    changeImage: function() {}
}

var SlideLoopImg = fucntion(imgArr, container) {
    LoopImages.call(this, imgArr, container)
}
SlideLoopImg.prototype = changeImage = function() {}
```


### 6.单例模式
```
/* 特性与优点
（1）仅创建单个对象并返回使用
（2）作为命名空间进行管理，避免冲突
（3）存储静态变量仅可读取不可修改
*/
const DEFAULT_GDKey = '1993ac213d2f4675ac1bffb1b03ef1f0'
const DEFAULT_GDServiceHost = 'http://172.16.3.103:7799/_AMapService'

/**
 * @Description: 初始化高德地图
 * @author: lyz
*/
class GDApi {
    isInit: boolean // 是否初始化
    key: string // 高德key
    v: string // map版本
    uiVersion: string // UI组件库版本

    getNewKey() {
        return Storager.Setting.get('GDkey', '__system') || DEFAULT_GDKey
    }

    initial() {
        this.isInit = true
        this.key = this.getNewKey()
        this.v = '1.4.19'
        this.uiVersion = '1.0'

        initAMapApiLoader({
            key: this.key,
            plugin: this.plugin,
            v: this.v,
            uiVersion: this.uiVersion
        })
    }
    changeMap() {
        let GDkey = this.getNewKey()
        // 未初始化则不刷新
        if (!this.isInit) return false
        // 新的高德key与原有高德key不同时刷新
        else if (GDkey !== this.key) return true
        // 其他情况则不刷新
        else return false
    }

}

export const gdApi = new GDApi()
```


## 3.结构型设计模式
### 1.外观模式
```
// 优点：不需要关注内部逻辑，只需要知道外观下的用途
function addEvent(dom, type ,fn) {
    if(dom.addEventListener) {
        dom.adEventListener(type, fn, false)
    } else if(dom.attachEvent) {
        dom.attachEvent('on' + type, fn)
    } else {
        dom['on' + type] = fn
    }
}
```


### 2.适配器模式
```
// 将某种形式数据通过适配器转换成合适的数据
function arrToObjAdapter(arr) {
    return {
        name: arr[0],
        type: arr[1],
        title: arr[2],
        data: arr[3]
    }
}

var arr = ['JavaScript', 'book', '前端编程语言', '8月1日']
var adapterData = arrToObjAdapter(arr)
```


### 3.代理模式
```
// 代理模式进行图片懒加载，亦称虚拟代理

// 图片节点
var myImage = (function() {
	var imgNode = document.createElement('img');
	document.body.appendChild(imgNode);
	return {
    	setSrc: function(src) {
    		imgNode.src = src;
    	}
    }
})();

// 图片懒加载代理管理
var preImage = (function() {
	var img = new Image; 
    img.onload = function() {
    	myImage.setSrc = img.src;
    }; 
 
    return {
    	setSrc: function(src) {
    		myImage.setSrc('../loading.gif');
    		img.src = src;
    	}
    }
})(); 
 
preImage.setSrc('https://cn.bing.com/az/hprichbg/rb/TadamiTrain_ZH-CN13495442975_1920x1080.jpg'); 
```


### 4.装饰者模式
```
// 优点：不需要关注原有功能，在原有功能基础上直接增加功能
var decorator = function(input, fn) {
    var input = document.getElementById(input)
    if(typeof input.onclick === 'function') {
        var oldClickFn = input.onclick
        // 获取原有点击事件响应函数，并在此基础上增加功能
        input.onclick = function() {
            oldClickFn()
            fn()
        }
    } else {
        input.onclick = fn
    }
}
```


### 5.桥接模式
```
// 优点：解耦抽象层与实现层，两部分独立完成
function changeColor(dom, color, bg) {
    dom.style.color = color
    dom.style.background = bg
}

span[0].onmouseover = function() {
    changeColor(this, 'red', '#ddd')
}
```


### 6.组合模式
```
// 优点：仅需要调用组合对象便能对单个对象进行逐个调用
// 新建一个关门的命令
var closeDoorCommand = {
    execute: function(){
        console.log( '关门' );
    }
};
// 新建一个开电脑的命令
var openPcCommand = {
    execute: function(){
        console.log( '开电脑' );
    }
};
// 登陆QQ的命令
var openQQCommand = {
    execute: function(){
        console.log( '登录QQ' );
    }
};

// 创建一个宏命令
var MacroCommand = function(){
    return {
        // 宏命令的子命令列表
        commandsList: [],
        // 添加命令到子命令列表
        add: function( command ){
            this.commandsList.push( command );
        },
        // 依次执行子命令列表里面的命令
        execute: function(){
            for ( var i = 0, command; command = this.commandsList[ i++ ]; ){
                command.execute();
            }
        }
    }
};

var macroCommand = MacroCommand();
macroCommand.add( closeDoorCommand );
macroCommand.add( openPcCommand );
macroCommand.add( openQQCommand );
macroCommand.execute();
```


### 7.享元模式
```
// 优点：针对具有相同结构的大量数据，由共享对象针对不同情况存储不同数据
// 情景：全量获取数据，实现翻页功能，翻页过程中针对共享单页数据进行数据切换
var Flyweight = function() {
    var created = []
    function create() {
        var dom = document.createElement('div')
        document.getElementById('container').appendChild(dom)
        created.push(dom)
        return dom
    }
    return {
        getDiv: function() {
            if(created.length < 5) {
                return create()
            } else {
                var div = created.shift()
                created.push(div)
                return div
            }
        }
    }
}
```


## 4.行为型设计模式
### 1.模板方法模式
```
// 特性：抽取共同点作为基类，子类在基类的基础上进行改变
// 定义基类：将相同的步骤抽离出来作为init方法
var Beverage = function(){};
Beverage.prototype.boilWater = function(){
    console.log( '把水煮沸' );
};
Beverage.prototype.brew = function(){}; // 空方法，应该由子类重写
Beverage.prototype.pourInCup = function(){}; // 空方法，应该由子类重写
Beverage.prototype.addCondiments = function(){}; // 空方法，应该由子类重写
Beverage.prototype.init = function(){
    this.boilWater();
    this.brew();
    this.pourInCup();
    this.addCondiments();
};

// 定义子类，子类针对不同步骤进行不同操作，但操作步骤的顺序与基类相同
var Coffee = function(){};
Coffee.prototype = new Beverage();

Coffee.prototype.brew = function(){
    console.log( '用沸水冲泡咖啡' );
};
Coffee.prototype.pourInCup = function(){
    console.log( '把咖啡倒进杯子' );
};
Coffee.prototype.addCondiments = function(){
    console.log( '加糖和牛奶' );
};
var Coffee = new Coffee();
Coffee.init();
```


### 2.观察者模式
```
// 特性：各模块之间解耦，其他模块只需要发布通知，各模块内部接受通知后完成本模块需完成内容
var Observer = (function(){
    var _message = {};
    return {
        // 注册事件
        regist: function(type, fn) {
            if(typeof _message[type] === 'undefined') {
                _message[type] = [fn]
            } else {
                _message[type].push(fn)
            }
        },
        // 触发事件
        fire: function(type, args) {
            if(!_message[type]) return
            else {
                _message[type].forEach((item) => {
                    item.call(this, args)
                })
            }
        },
        // 移除事件
        remove: function(type, fn){
            if(_message[type] instanceof Array) {
                var i = _message[type].length - 1
                for(; i >= 0 ; i--) {
                    _message[type][i] === fn && _message[type].splice(i, 1)
                }
            }
        }
    }
})()
```


### 3.状态模式
```
// 特性：状态决定行为，分支语句取决于状态
class SuperMarry {
  constructor() {
    this._currentState = []
    this.states = {
      jump() {console.log('跳跃!')},
      move() {console.log('移动!')},
      shoot() {console.log('射击!')},
      squat() {console.log('蹲下!')}
    }
  }
  
  change(arr) {  // 更改当前动作
    this._currentState = arr
    return this
  }
  
  go() {
    console.log('触发动作')
    this._currentState.forEach(T => this.states[T] && this.states[T]())
    return this
  }
}

new SuperMarry()
    .change(['jump', 'shoot'])
    .go()                    // 触发动作  跳跃!  射击!
    .go()                    // 触发动作  跳跃!  射击!
    .change(['squat'])
    .go()                    // 触发动作  蹲下!
```


### 4.策略模式
```
// 特性：根据不同分支情况适用不同算法，使用与实现之间的解耦
var countPrice = {
  returnPrice: {
    full100: function (price) {
      return price - 5
    },
    full200: function (price) {
      return price - 15
    },
    full300: function (price) {
      return price - 30
    },
    full400: function (price) {
      return price - 50
    }
  },
  getPirce: function (type, money) {
    return this.returnPrice[type] ? this.returnPrice[type](money) : money;
  },
  addRule: function (type, discount) {
    this.returnPrice[type] = function (price) {
      return price - discount;
    }
  }
}
```


### 5.职责链模式
```
// 特性：业务划分为多个步骤，步骤间解耦，通过链的方式连接
// 500元订单
var order500 = function( orderType, pay, stock ){
    if ( orderType === 1 && pay === true ){
        console.log( '500元定金预购, 得到100优惠券' );
    }else{
        order200( orderType, pay, stock ); // 将请求传递给200元订单
    }
}
// 200元订单
var order200 = function( orderType, pay, stock ){
    if ( orderType === 2 && pay === true ){
        console.log( '200元定金预购, 得到50优惠券' );
    }else{
        orderNormal( orderType, pay, stock ); // 将请求传递给普通订单
    }
}
// 普通购买订单
var orderNormal = function( orderType, pay, stock ){
    if ( stock > 0 ){
        console.log( '普通购买, 无优惠券' );
    }else{
        console.log( '手机库存不足' );
    }
}
// 测试结果：
order500( 1 , true, 500); // 输出：500元定金预购, 得到100优惠券
```

### 6.命令模式
```
// 特性：将对象操作封装成命令，执行时只需调用对应的命令即可
class Editor {
    constructor() {
        this.content= ''
    }
    write(content) {
        this.content+=content
        return this
    }
    read() {
        console.log(this.content)
        return this
    }
    space() {
        this.content+= ' '
        return this
    }
}
```


### 7.访问者模式
```
// 特性：针对数据稳定，数据操作方法易变的情况，进行数据以及操作方法的解耦
var Visitor = (function() {
    return {
        splice: function() {
            var args = Array.prototype.splice.call(arguments, 1);
            return Array.prototype.splice.apply(arguments[0], args);
        },
        push: function() {
            var len = arguments[0].length || 0;
            var args = this.splice(arguments, 1);
            arguments[0].length = len + arguments.length - 1;
            return Array.prototype.push.apply(arguments[0], args);
        },
        pop: function() {
            return Array.prototype.pop.apply(arguments[0]);
        }
    }
})();

var a = new Object();
Visitor.push(a,1,2,3,4);
Visitor.push(a,4,5,6);
Visitor.pop(a);
Visitor.splice(a,2);
```


### 8.中介者模式
```
// 特性：对象与对象之间的引用解耦合，由中介者管理对象并处理操作信息
var playerDirector= ( function(){
    var players = {}, // 保存所有玩家
    operations = {}; // 中介者可以执行的操作
    /****************新增一个玩家***************************/
    operations.addPlayer = function( player ){
        var teamColor = player.teamColor; // 玩家的队伍颜色
        players[ teamColor ] = players[ teamColor ] || []; // 如果该颜色的玩家还没有成立队伍，则
        新成立一个队伍
        players[ teamColor ].push( player ); // 添加玩家进队伍
    };
    /****************移除一个玩家***************************/
    operations.removePlayer = function( player ){
        var teamColor = player.teamColor, // 玩家的队伍颜色
        teamPlayers = players[ teamColor ] || []; // 该队伍所有成员
        for ( var i = teamPlayers.length - 1; i >= 0; i-- ){ // 遍历删除
            if ( teamPlayers[ i ] === player ){
                teamPlayers.splice( i, 1 );
            }
        }
    };
    /****************玩家换队***************************/
    operations.changeTeam = function( player, newTeamColor ){ // 玩家换队
        operations.removePlayer( player ); // 从原队伍中删除
        player.teamColor = newTeamColor; // 改变队伍颜色
        operations.addPlayer( player ); // 增加到新队伍中
    };
    operations.playerDead = function( player ){ // 玩家死亡
        var teamColor = player.teamColor,
        teamPlayers = players[ teamColor ]; // 玩家所在队伍
        var all_dead = true;
        for ( var i = 0, player; player = teamPlayers[ i++ ]; ){
            if ( player.state !== 'dead' ){
                all_dead = false;
                break;
            }
        }
        if ( all_dead === true ){ // 全部死亡
            for ( var i = 0, player; player = teamPlayers[ i++ ]; ){
                player.lose(); // 本队所有玩家lose
            }
            for ( var color in players ){
                if ( color !== teamColor ){
                    var teamPlayers = players[ color ]; // 其他队伍的玩家
                    for ( var i = 0, player; player = teamPlayers[ i++ ]; ){
                        player.win(); // 其他队伍所有玩家win
                    }
                }
            }
        }
    };
    var ReceiveMessage = function(){
        var message = Array.prototype.shift.call( arguments ); // arguments的第一个参数为消息名称
        operations[ message ].apply( this, arguments );
    };
    return {
        ReceiveMessage: ReceiveMessage
    }
})();
```


### 9.备忘录模式
```
// 特性：缓存状态信息，在切换时直接获取原状态，减少网络请求
var Page = function() {
    var cache = {}
    return function(page, fn) {
        if(cache[page]) {
            showPgae(page, cache[page])
            fn && fn()
        } else  {
            proxy.$http.post(url, param).then((result) => {
                showPage(page, res.data)
                cahce[page] = res.data
                fn && fn()
            })
        }
    }
}
```


### 10.迭代器模式
```
// 特性：提供方法顺序访问一个聚合对象
class Iterator {
    constructor(conatiner) {
        this.list = conatiner.list
        this.index = 0
    }
    next() {
        if (this.hasNext()) {
            return this.list[this.index++]
        }
        return null
    }
    hasNext() {
        if (this.index >= this.list.length) {
            return false
        }
        return true
    }
}
```


### 11.解释器模式
```
// 例子：返回元素的文档路径，根据需求解析出语法规则
function getSiblingName(node) {
    if (node.previousSibling) {
        var name = '';
        var count = 1;
        var nodeName = node.nodeName;
        var sibling = node.previousSibling;

        while (sibling) {
            // 元素节点并且类型相同并且有名字
            if (sibling.nodeType === 1 && sibling.nodeType === node.nodeType && sibling.nodeName) {
                if (nodeName === sibling.nodeName) {
                    count++;
                    name += count;
                } else {
                    count = 1;
                    name += '|' + sibling.nodeName.toUpperCase();
                }
            }
            sibling = sibling.previousSibling;
        }
        return name;
    } else {
        // 不存在就返回空字符串了
        return '';
    }
}

var Interpreter = (function() {
    return function(node, wrap) {
        var path = [];
        wrap = wrap || document;
        // 处理特殊情况
        if (node === wrap) {
            if (wrap.nodeType === 1) {
                path.push(wrap.nodeName.toUpperCase());
            }
            return path;
        } else if (node.parentNode !== wrap) {
            path = arguments.callee(node.parentNode, wrap);
        } else {
            if (wrap.nodeType === 1) {
                path.push(wrap.nodeName.toUpperCase());
            }
        }
        var sublingsNames = getSiblingName(node);
        if (node.nodeType === 1) {
            path.push(node.nodeName.toUpperCase() + sublingsNames);
        }
        return path;
    }
})();
window.onload = function() {
    var path = Interpreter(document.getElementById('button'));
    alert(path);
}
```


## 5.技巧型设计模式
### 1.链模式
```
// 特性：通过点语法对对象方法进行链式调用
// 例子：jQuery实现
function _jQuery(selector){
    return new _jQuery.fn.init(selector); // new复制避免共用同一个对象
}
_jQuery.fn = _jQuery.prototype = {
    constructor: _jQuery,
    init: function(selector){
        this[0] = document.querySelector(selector);
        this.length = 1;
        return this;
    },
    length: 3,
    size: function(){
        return this.length;
    }
}
// new返回的是_jQuery.fn.init的实例，无法链式调用方法，调整原型指向
_jQuery.fn.init.prototype = _jQuery.fn;
```


### 2.委托模式
```
// 特性：解决请求与委托者之间的耦合关系，被委托者接收请求分发给委托者

// 例子：父元素通过冒泡监听点击事件，对触发点击事件的子元素进行操作，不需要给每个子元素都绑定点击事件。避免内存泄漏（清除元素时未清除事件）

ul.onclick = function(e) {
	// console.log(e.target)
	// 进行判定点击的是哪个元素
	if (e.target.nodeName.toLowerCase() === "span") {
		// 此时e.target是span
		this.removeChild(e.target.parentNode);
	} else if (e.target.nodeName.toLowerCase() === "li") {
		// 此时e.target是li
		e.target.style.backgroundColor = "red"
	}
}
```


### 3.数据访问模式
```
// 特性：对数据库操作进行封装
/**
 * LocalStorage数据访问类
 * @param {string} prefix Key前缀
 * @param {string} timeSplit 时间戳与存储数据之间的分割符
 */
var Dao = function (prefix, timeSplit) {
    this.prefix = prefix;
    this.timeSplit = timeSplit || '|-|';
}
// LocalStorage数据访问类原型方法
Dao.prototype = {
    // 操作状态
    status: {
        SUCCESS: 0,     // 成功
        FAILURE: 1,     // 失败
        OVERFLOW: 2,    // 溢出
        TIMEOUT: 3      // 过期
    },
    // 本地存储对象
    storage: localStorage || window.localStorage,
    // 获取带前缀的真实键值
    getKey: function (key) {
        return this.prefix + key;
    },
    // 添加（修改）数据
    set: function (key, value, callback, time) {
       ...
    },
    // 获取数据
    get: function (key, callback) {
        ...
    },
    // 删除数据
    remove: function (key, callback) {
        ...
    }
}

```


### 4.节流模式
```
// 特性：针对短时间内多次触发的事件进行节流
var throttle = function f() {
	arguments.slice = Array.prototype.slice;
	var fn,params=[];
	//如果第一个参数是boolean类型那么第一个参数表示清除定时器
	if(typeof arguments[0] === 'boolean') {
		//第二个参数为函数
		fn = arguments[1];
		//函数的计时器句柄存在，清除该定时器
		fn.__throttleID && clearTimeout(fn. __throttleID);
		//工作计时器延迟执行函数
	} else {
		fn = arguments[0];
		params = arguments.slice(1);
		f(true,fn);
		//为函数绑定句柄函数
		fn.__throttleID = setTimeout(function() {
			//执行函数
			fn.apply(null, params);
		}, 500)
	}
}
```


### 5.简单模板模式
```
// 特性：生成视图模板字符串插入视图中，避免对DOM的多次操作
// 模板生成器
A.view = function(name){
    var v = {
        code : '<pre><code>{#code#}</code></pre>',
        img : '<img src="{#src#}" alt="{#alt#}" title="{#title#}" />',
        part : '<div id="{#id#}" class="{#class#}">{#part#}</div>',
        theme : [
            '<div>',
                '<h1>{#title#}</h1>',
                '{#content#}',
            '</div>'
        ].join('')
    }
    if(Object.prototype.toString.call(name) === "[object Array]"){
        //模板缓存器
        var tpl = '';
        for(var i=0;i<name.length;i++){
            tpl += arguments.callee(name[i]);
        }
        return tpl;
    }else{
        //如果模板库中有该模板就返回，否则返回简易模板
        return v[name] ? v[name] : ('<'+name+'>{#'+name+'#}<'+name='>');
    }
}
// 模板字符串赋值
A.formateString = function(str, data) {
    return str.replace(/\{#(\w+)#\}/g, function(match, key) {
        retun typeof data[key] === undefined ? '' : data[key]
    })
}
```


### 6.惰性模式
```
// 特性：对象创建需要重复分支判断的情况，每次返回结果都为固定唯一

// 例子：运用闭包，加载时确定分支
var AddEvent = function(dom, type, fn){
  if(dom.addEventListener){
    return function(dom, type, fn){
        dom.addEventListener(type, fn, false);
      }
  }else if(dom.attachEvent){
    return function(dom, type, fn){
        dom.attachEvent('on'+type, fn);
      }
  }else{
    return function(dom, type, fn){
        dom['on'+type] = fn;
      }
  }
}();

// 例子：第一次调用时确定分支
var AddEvent = function(dom, type, fn){
  if(dom.addEventListener){
    AddEvent = function(dom, type, fn){
        dom.addEventListener(type, fn, false);
      }
  }else if(dom.attachEvent){
    AddEvent = function(dom, type, fn){
        dom.attachEvent('on'+type, fn);
      }
  }else{
    AddEvent = function(dom, type, fn){
        dom['on'+type] = fn;
      }
  }
 AddEvent(dom, type, fn);
};
```


### 7.参与者模式
```
// 特性：函数绑定 + 函数柯里化

// 函数柯里化：初始化时保存一部分参数，执行时接受一部分参数，最后返回所有参数共同执行的结果
function bind(fn, ctx /*ctx:作用域*/) {
    const slice = Array.prototype.slice // 保存数组原型上的slice方法，用来分割arguments
    const args = slice.call(arguments, 2) // 从第3个参数开始切割，因为前2个参数是fn和ctx
    return function () {
        const addArguments = slice.call(arguments) // 这里的arguments是返回的这个匿名函数的，是个空数组
        allArguments = addArguments.concat(args) // 数组拼接，拼接后的数组中保存的就是我们要传递的参数
        return fn.apply(ctx, allArguments)
    }
}

const P1 = function(name){
    console.log(name)
}
const P2 = function(name,age){
    console.log(name,age)
}
const P1_bind = bind(P1,this,'老六')
const P2_bind = bind(P2,this,'老六',18)

P1_bind() // 老六
P2_bind() // 老六,18
```


### 8.等待者模式
```
// 特性：处理耗时较长的多个操作，进行统一的状态管理，全部成功/单个失败时执行
function Waiter() {
    var dfd = [],
        // 成功的回调
        doneArr = [],
        // 失败的回调
        failArr = [],
        slice = Array.prototype.slice,
        that = this;
    var Promise = function() {
        this.resolved = false;
        this.rejected = false;
    };
    Promise.prototype = {
        // 全部成功则清空监听对象并执行成功函数
        resolve: function() {
            this.resolved = true;
            if (!dfd.length) return;
            for (var i = dfd.length - 1; i >= 0; i--) {
                if (dfd[i] && !dfd[i].resolved || dfd[i].rejected) {
                    return;
                }
                dfd.splice(i, 1);
            }
            _exec(doneArr);
        },
        // 失败一个则清空监听对象并执行失败函数
        reject: function() {
            this.rejected = true;
            if (!dfd.length) return;
            // 失败清除全部监控对象
            dfd.splice(0);
            _exec(failArr);
        }
    };
    
    // 创建监听对象
    that.Deferred = function() {
        return new Promise();
    };

    // 执行一个失败/全部成功的回调函数
    function _exec(arr) {
        var i = 0, len = arr.length;
        for (; i < len; i++) {
            try {
                arr[i] && arr[i]();
            } catch (e) {
            }
        }
    }

    // 将监听对象放入等待者监听数组中
    that.when = function() {
        dfd = slice.call(arguments);
        var i = dfd.length;
        for (--i; i >= 0; i--) {
            if (!dfd[i] || dfd[i].resolved || dfd[i].rejected || !dfd[i] instanceof Promise) {
                dfd.splice(i, 1);
            }
        }
        return that;
    };
    
    // 将成功回调函数放入等待者执行数组中
    that.done = function() {
        doneArr = doneArr.concat(slice.call(arguments));
        return that;
    };
    
    // 将失败回调函数放入等待者执行数组中
    that.fail = function() {
        failArr = failArr.concat(slice.call(arguments));
        return that;
    }
}


// 实际运用
var waiter = new Waiter();

function first(waiter) {
    var dtd = waiter.Deferred();
    setTimeout(function() {
            console.log('first finish');
            dtd.resolve();
        },
        500);
    return dtd;
}

function second(waiter) {
    var dtd = waiter.Deferred();
    setTimeout(function() {
            console.log('second finish');
            dtd.resolve();
        },
        2000);
    return dtd;
}

waiter.when(first(waiter), second(waiter)).done(function() {
    console.log('all finished')
})
```



## 6.架构型设计模式
- 同步模块模式：划分模块，同步加载对应模块
- 异步模块模式：划分模块，异步加载对应模块
- widget模式：将视图拆分成组件，组件合成完整视图
- MVC模式：模型层/视图层/控制器层分别工作，减少耦合
- MVP模式：模型层/视图层/管理器层，减少模型与视图的耦合，对数据的操作统一由管理器完成
- MVVM模式：模型层/视图层/视图模型层，视图绑定数据决定视图渲染