# Node.js

标签（空格分隔）： JavaScript

---

## 1.V8引擎
- 非阻塞IO：等待资源时，若资源不可用，IO请求不会等待资源而是直接返回资源不可用
- 相比于其他JavaScript引擎转换成字节码或解释执行，V8将其编译成原生机器码，并且使用了如内敛缓存等方法来提高性能，运行速度堪比二进制程序
- 支持众多操作系统，如windows、linux、Android等
- 数据表示：数据实际内容(内容变长，类型不同) + 数据的句柄(内容固长，包含数据的指针)
- 对象包含三部分：隐藏类指针，这是v8为JavaScript对象创建的隐藏类；属性值表指针，指向该对象包含的属性值；元素表指针，指向该对象包含的属性。
- 工作过程：
  - 执行过程：编译+运行，JS需要在用户使用时完成编译和执行。V8中并非立刻全部完成编译，某些代码需要执行时才会编译，减少时间开销
  - 编译过程：
     - 源代码：Script类调用Compiler类的Compile函数为其生成本地代码
     - 抽象语法树AST：Compile函数使用Parser类生成AST
     - 本地可执行代码：Compile函数使用FullCodeGenerator类来生成本地代码
     - 跳过AST转换字节码过程：提高速度，缺少优化代码机会，为了提升性能，V8会在生成本地代码后，使用数据分析器(profiler)采集一些信息，然后根据这些数据将本地代码进行优化，生成更高效的本地代码
  - 运行过程：
     - 函数调用：函数是一个基本单位。调用函数，若函数已生成本地代码则执行，否则生成本地代码。减少了处理尚未用到的代码的时间
     - 构建JS对象：Runtime类辅助创建对象
     - 分配内存：Heap类分配内存
     - 完成功能：借助Runtime类中辅助函数完成一些功能
     - 垃圾回收：MarkCompactCollector用来标记、清除和整理等基本的垃圾回收过程
  - 优化回滚：Crankshaft编译器为了性能考虑，通常会做出比较乐观和大胆的预测—代码稳定且变量类型不变，所以可以生成高效的本地代码。若代码变量类型执行过程中改变则进行优化回滚




## 2.Node的特点
- 事件和回调函数：事件驱动和异步回调
- 单线程：
  - 优点：JavaScript与其余线程无法共享任何状态，无需在意状态的同步问题，没有死锁，也没有线程上下文交换所带来的性能上的开销。
  - 缺点：无法利用多核CPU、错误会引起应用退出，长时间占用CPU会导致无法继续调用异步I/O
  - 解决：创建子线程，需要计算的程序发给子线程进行计算，结果通过消息传递的方式返回，解决单线程在健壮性和无法利用多核CPU方面的问题
- 跨平台：通过libux实现window与Linux的跨平台
- 应用场景：
  - I/O密集型：利用事件循环的处理能力，有效地组织更多硬件资源
  - CPU密集型：
     - 执行效率高
     - 大量计算使CPU不能释放影响后续I/O，可将大型运算分为多个小任务使运算适时释放
     - 常驻进程进行计算，利用线程间的消息传递结果



## 3.模块机制
- CommonJS模块规范
  - 模块引用：var math = require('math')
  - 模块定义：exports对象用于导出当前模块的方法或变量
  - 模块标识：传递给require()方法的参数，符合小驼峰命名字符串
- module.exports与exports的区别
  - 模块内部：exports = module.exports = {}
  - 关系：module.exports是真正的接口，exports是辅助工具，最终返回给调用的是module.exports
  - 定义：所有的exports的属性和方法都赋值给了module.exports。但如果module.exports已经具备一些属性和方法，那么exports收集来的信息将被忽略
  - 使用：若是特定类型用module.exports，若是典型的‘实例化对象’就用exports
- Node模块实现
  - 引入模块：路径分析、文件定位、编译执行
  - 模块分为两类：
     - Node提供的模块(核心模块)：已编译成二进制执行文件，文件定位和编译执行步骤省去
     - 用户编写的模块(文件模块)：需要完整引入模块过程
  - 优先从缓存加载：对引入模块进行缓存，二次加载缓存优先
  - 路径分析：
     - 核心模块：核心模块优先级仅次于缓存加载，加载速度最快
     - 路径形式文件模块：将路径转换为真实路径，以此为索引将编译结果放到缓存
     - 自定义模块：模块路径沿着父级的node_modules目录生成，当前文件路径越深查找耗时越多
  - 文件定位：
     - 文件扩展名：以.js、.json、.node顺序补足扩展名
     - 目录分析和包：node加载模块的顺序方式：文件-目录-目录下的package.json-目录下的index
  - 文件模块编译：
     - JavaScript模块编译(.js)：编译过程中头部包装添加(function (exports, require, module, __filename, __dirname) 从而可以调用参数变量
     - C/C++模块编译(.node)：C/C++编译生成不需要编译，只需要加载和执行，用于提高效率
     - JSON文件编译：利用fs模块读取JSON文件内容，调用JSON.parse()获得对象再将其付给exports以供外部调用。当用作配置文件时调用require()引入即可
- 核心模块：与文件模块区别在于从内存中加载以及缓存执行结果的位置
  - JavaScript核心模块编译过程：转存为C/C++代码，编译JavaScript核心模块
  - C/C++核心模块编译过程：C++负责内核，提高性能（内建模块）；JS负责封装，提高开发速度




## 4.异步I/O
- 异步I/O：绝大多数操作都是以异步的方式进行调用的，每个调用之间无需等待之前的I/O结束
- 非阻塞I/O：需要重复调用I/O确认完成，即为轮询


## 5.Vuepress+Node
### 1.Vuepress
- config.js：配置接口对应跨域信息，支持跨域
```
devServer: {
        proxy: {
            '/api': {
                target: 'http://localhost:8085/api',
                changeOrigin: true,
                pathRewrite: {
                    '^/api': ''
                }
            }
        }
    },
```
- 接口请求：对数据进行特殊处理传给node进行处理
```
let param = new URLSearchParams();
param.append('directory', this.form.directory)
param.append('title', this.form.title)
param.append('content', this.form.content)
this.$axios({
    method: 'post',
    url: '/api/editor',
    data: param
}).then((res) => {
    this.directory = JSON.parse(res.data) // 返回数据为res.data
})
```

### 2.Node
- 安装包准备：
```
cnpm install express --save // node框架
cnpm install body-parser --save //  处理 JSON, Raw, Text 和 URL 编码的数据
cnpm install cookie-parser --save // req.cookies获取请求的cookie并转成对象
cnpm install multer --save // 处理上传的文件数据
```
- 服务器准备：创建server文件夹作为服务器，文件夹内配置逻辑
- main.js：服务器启动文件
```
var express = require('express')
var app = express()
var api = require('./api')
// 修改请求体可接受数据量
var bodyParser = require('body-parser');
app.use(bodyParser.json({limit: '10mb'}));
app.use(bodyParser.urlencoded({limit: '10mb', extended: true}));
// 路由接口监听
app.use('/api', api)
// 监听本机8085端口
var server = app.listen(8085, function() {
    var host = server.address().address
    var port = server.address().port
    console.log('Server has running at http://%s:%s', host, port)
})
```
- api.js：服务器接口文件
```
var express = require('express');
var router = express.Router();
// 数据表单解析
var bodyParser = require('body-parser');
var urlencodedParser = bodyParser.urlencoded({ extended: false });
// 文件处理
var multer = require('multer');
var fs = require("fs");


// 路由匹配接口调用，get请求返回数据
router.get('/directory', function(req, res) {
    fs.readFile('./directory.json', 'utf8', (err, data) => {
        if (err) {
            console.error(err)
            return
        }
        res.end(JSON.stringify(data));
    })
})

// post请求中有表单类型数据时需传入表单类型解析参数进行处理
router.post('/editor', urlencodedParser, function(req, res) {
    let data = req.body; // req.body存储请求中的data数据
    /*
      执行上传逻辑
    */
})
```