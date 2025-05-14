# mock

标签（空格分隔）： 实践

---

## 1.概念
- 用途：模拟后端接口假数据，进行分离开发
- 用例：针对url的请求返回的response即mock中设定的data
``` javascript
Mock.mock(url, {
    'list|1-10': [{
        'id|+1': 1
    }]
})
```


## 2.数据模板定义规范DTD
- 'name|rule'：value
  - name：属性名
  - rule：生成规则，需依赖属性值的类型才能确定
  - value：属性值，可含有占位符@，指定最终值的初始值和类型
- 属性值是字符串 String
  - 'name|min-max': string：通过重复 string 生成一个字符串，重复次数大于等于 min，小于等于 max。
  - 'name|count': string：通过重复 string 生成一个字符串，重复次数等于 count。
- 属性值是数字 Number
  - 'name|+1': number：属性值自动加 1，初始值为 number。
  - 'name|min-max': number：生成一个大于等于 min、小于等于 max 的整数，属性值 number 只是用来确定类型。
  - 'name|min-max.dmin-dmax': number：生成一个浮点数，整数部分大于等于 min、小于等于 max，小数部分保留 dmin 到 dmax 位。
- 属性值是布尔型 Boolean
  - 'name|1': boolean：随机生成一个布尔值，值为 true 的概率是 1/2，值为 false 的概率同样是 1/2。
  - 'name|min-max': value：随机生成一个布尔值，值为 value 的概率是 min / (min + max)，值为 !value 的概率是 max / (min + max)。
- 属性值是对象 Object
  - 'name|count': object：从属性值 object 中随机选取 count 个属性。
  - 'name|min-max': object：从属性值 object 中随机选取 min 到 max 个属性。
- 属性值是数组 Array
  - 'name|1': array：从属性值 array 中随机选取 1 个元素，作为最终值。
  - 'name|+1': array：从属性值 array 中顺序选取 1 个元素，作为最终值。
  - 'name|min-max': array：通过重复属性值 array 生成一个新数组，重复次数大于等于 min，小于等于 max。
  - 'name|count': array：通过重复属性值 array 生成一个新数组，重复次数为 count。
- 属性值是函数 Function
  - 'name': function：执行函数 function，取其返回值作为最终的属性值，函数的上下文为属性 'name' 所在的对象。
- 属性值是正则表达式 RegExp
  - 'name': regexp：根据正则表达式regexp反向生成可以匹配它的字符串，生成自定义格式的字符串


## 3.数据占位符定义规范DPD
- 用途：占位符引用的是Mock.Random中的方法，亦可并优先调用数据模板的属性
- 格式：@占位符 / @占位符(参数 [, 参数])
- 用例：可使用提供的方法email等生成对应的类型数据，也可自定义方法
``` javascript
Mock.mock(url, {
    email: '@email'
})
```
- Mock.Random.method([参数])：根据方法生成各种随机数据，提供部分内置方法
- Mock.Random.extend()：方法内可自定义扩展方法，方法名为自定义生成规则
``` javascript
Random.extend({
    constellation: function(date) {
        var constellations = ['白羊座', '金牛座', '双子座', '巨蟹座', '狮子座', '处女座', '天秤座', '天蝎座', '射手座', '摩羯座', '水瓶座', '双鱼座']
        return this.pick(constellations)
    }
})
```


## 4.mock方法
- Mock.mock( rurl?, rtype?, template|function( options ) )：拦截路由返回数据
  - rurl：可选，拦截的路由
  - rtype：可选，拦截的请求类型
  - template：可选，DTD，根据模板生成数据返回
  - function：可选，用于生成相应数据的函数，根据函数生成数据返回
  - options：选项集，含有请求ajax的url、type和body三个属性
- Mock.setup( settings )：配置拦截路由时的行为
  - setting：必选，配置项集合
  - timeout：可选，配置项内响应时间，'400'/'200-400'，默认值'10-100'
- Mock.valid( template, data )：校验真实数据与数据模板是否匹配
- Mock.toJSONSchema( template )：将模板转化为JSON Schema
- axios-mock-adapter：设置拦截axios请求的代理api地址
``` javascript
var axios = require('axios')
var MockAdapter = require('axios-mock-adapter')
var mock = new MockAdapter(axios)
mock.onGet('/users').reply(200,{Users})
```




