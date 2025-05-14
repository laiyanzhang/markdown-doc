# HTTP

标签（空格分隔）： 技术栈

---

## 1.TCP/IP协议
- DNS：域名解析成IP地址
- HTTP协议：生成请求报文
- TCP协议：将HTTP报文分割成报文段
- IP协议：搜索目标IP地址并进行传送


## 2.URI/URL
- URI：互联网资源标识
- URL：资源地点，URI子集
- 绝对URI格式：`http://user:pass@www.example.jp:80/dir/index.htm?uid=1#ch1`
  - 协议方案名：http/https等指定协议类型
  - 登录信息认证：指定用户名和密码作为获取资源时的身份验证，可选项
  - 服务器地址：域名/IP地址等服务器地址
  - 服务器端口：省略则为默认端口
  - 文件路径：指定服务器上的文件路径
  - 查询字符串：传入参数，可选项
  - 片段标识符：标记文档内某个位置，可选项

## 3.请求/响应
- 请求报文
  - 请求方法：Get/Post
  - URI：请求地址
  - 协议版本：HTTP/1.1
  - 请求首部字段
  - 请求内容
- 响应报文
  - 协议版本：HTTP/1.1
  - 状态码：2--/3--/4--/5--
  - 状态码原因短语：OK
  - 响应首部字段
  - 响应内容
- http方法
  - get：获取资源（1.0/1.1）
  - post：传输信息（1.0/1.1）
  - put：传输文件，存在安全问题（1.0/1.1）
  - head：获取头部信息确认（1.0/1.1）
  - delete：删除文件，存在安全问题（1.0/1.1）
  - options：获取服务器支持的方法（1.1）
  - trace：追踪请求路径（1.1）
  - connect：用隧道协议连接代理，ssl/tls（1.1）
  - link：建立和资源的联系（1.0）
  - unlink：断开连接关系（1.0）
- 字段用途
  - range：获取指定范围内的字节内容
  - accept-language：协商页面文字语言
- 状态码类别
  - 100 服务请求中
  - 101 切换协议，服务端根据客户端要求切换到更高的协议
  - 200 服务请求成功
  - 201 created 已创建，成功请求并创建了新的资源
  - 202 accepted 已接受，已接受但并未处理完成
  - 204 No Content无内容，服务器成功处理但未返回内容
  - 301 Moved Permanently 永久移动，返回信息包括新URI，浏览器以后会自动重定向到新URI
  - 302 Found 临时重定向，保留旧的URI
  - 304 没有被修改，读取的内容为缓存
  - 400 Bad Request 客户端语法错误，服务器无法理解
  - 401 Unauthorized 请求需要用户认证
  - 403 禁止访问(Forbidden)
  - 404 没有找到要访问的内容（Not Found)
  - 500 内部服务器错误
  - 501 Not implemented 服务器不支持请求的功能，无法完成请求
- 通信数据转发程序
  - 代理：减少带宽流量以及访问控制
     - 缓存代理：缓存响应用于相同请求返回响应
     - 透明代理：区别与是否对报文进行加工，加工则为非透明代理
  - 网关：将http转换成其他协议通信
  - 隧道：隧道中使用ssl等加密手段通信



## 4.http头部
### 1.通用首部字段
- Cache-Control
  - public：缓存可被所有用户使用
  - peivate：缓存只被特定用户使用
  - no-cache：不缓存过期资源，请求不再获取缓存资源；响应不允许缓存资源
  - no-store：不缓存资源
  - max-age：标志缓存有效时长，请求缓存时间内直接返回；响应缓存时间不再确认有效性
  - s-maxage：适用于供多位用户使用的公共缓存服务器，与max-age类似，忽略max-age与expire
  - min-fresh：请求指定时间内的缓存资源不再返回
  - max-stale：即使过期，在指定时间内的缓存也会被客户端接收
  - only-if-cached：只加载缓存中的资源
  - must-revalidate：代理再次向服务器验证缓存有效性，忽略max-stale指令
  - proxy-revalidate：所有缓存服务器在返回响应前必须再次验证缓存有效性
  - no-transform：缓存不能改变实体主体的媒体类型，防止压缩图片等操作
- Connection
  - `Connection：不再转发的首部字段名`：控制首部字段的转发与否
  - `Connection: close`：http1.1默认持久连接，服务端明确断开连接
  - `Connection：keep-Alive`：http1.1之前默认非持久连接，明确持久连接
- `Pragma: no-cache`：与`Cache-Control：no-cache`相同，用于兼容http协议旧版本
- `Trailer：报文主体后的首部字段`：这些首部字段用于分块传输编码时
- `Transfer-Encoding：chunked`：使用分块编码的方式
- Upgrade：检测是否可使用更高版本的协议通信，参数用于指定一个完全不同的通信协议
- Via：经过代理服务器时，将服务器的信息写在字段中
- Warning：通知用户关于缓存的警告


### 2.请求首部字段
- 举例：`Accept-Language: zh-cn,zh;q=0.7,en-us,en;q=0.3`：q表示权重
- Accept：客户端支持的媒体类型
- Accept-Charset：客户端支持的字符集
- Accept-Language：客户端支持的语言
- Accept-Encoding：客户端支持的内容编码
- TE：客户端支持的传输编码
- Authorization/Proxy-Authorization：客户端告知服务器的认证信息
- Expect：客户端告知服务器的期待行为（状态码），错误时返回417
- From：用户的电子邮件地址
- Host：请求资源所在的互联网主机名和端口号，用于区分同一ip下的虚拟主机
- If-Match：匹配服务器资源的实体标记ETag，一致返回资源，不一致返回412
- If-None-Match：与If-Match相反
- If-Modified-Since：指定时间后更新则返回更新资源，否则返回304，用于确定资源有效性
- If-Unmodified-Since：与If-Modified-Since相反
- If-Range：Etag/时间内则返回指定范围的资源，不符合则返回全部资源
- Max-Forwards：指定经过服务器的最大个数，为0时返回响应，用于排查过程问题
- Range：获取指定范围资源，服务器可以处理则返回206以及对应资源，否则返回200以及全部资源
- Referer：原始资源的URI
- User-Agent：创建请求的浏览器和用户代理名称的信息


### 3.响应首部字段
- Accept-Ranges：告知客户端服务器是否能处理范围请求；bytes/none
- Age：源服务器创建该响应后过去的时间
- ETag：实体标识
- Location：将响应接收方引导至另外一个URI，搭配重定向状态码
- Proxy-Authenticate：代理服务器要求的认证信息
- Retry-After：多久之后再次发送请求
- Server：服务器上安装的http服务器应用程序信息
- Vary：传递首部字段，代理服务器仅对包含该首部字段的请求返回缓存
- WWW-Authenticate：用于http访问认证，告知客户端认证方案


### 4.实体首部字段
- Allow：通知客户端能够支持的http方法
- Content-Encoding：告知客户端，服务器对实体的主体部分选用的内容编码方式
- Content-Language：告知客户端，实体主体使用的自然语言
- Content-Length：实体部分的大小，经过编码后则不能使用该字段
- Content-Location：资源报文对应的URI
- Content-MD5：由MD5生成值，检查报文主体传输过程中是否完整以及确认传输到达
- Content-Range：告知客户端，作为响应返回的实体的哪个部分符合范围请求
- Content-Type：实体内对象的媒体类型
- Expires：资源失效的日期告知客户端
- Last-Modified：资源最终修改的时间


### 5.为Cookie服务的首部字段
- Set-Cookie：状态管理所使用的Cookie信息，响应首部字段
  - NAME=VALUE：Cookie的名称与值
  - expires=DATE：Cookie有效期，默认值为浏览器关闭前
  - path=PATH：对应文件目录为Cookie的适用对象
  - domain=域名：Cookie适用对象的域名，默认值为创建Cookie的服务器的域名
  - Secure：仅在HTTPS安全通信时才发送Cookie
  - HttpOnly：不允许js脚本访问
- Cookie：服务器接收到的Cookie信息，请求首部字段


## 5.content-type和enctype
- content-type：标识请求或响应中主体（Body）的媒体类型（MIME 类型）

| 类型  | 示例 | 用途 |
| -- | -- | -- |
|application/x-www-form-urlencoded|``key1=value1&key2=value2``|默认表单提交格式（URL 编码）|
|multipart/form-data              |分块传输，带文件上传|表单含文件上传时使用|
|application/json                 |`{"key": "value"}`|REST API 常用格式|
|text/plain                       |纯文本|普通文本数据|
|application/xml	              |``<data>value</data>``|XML 格式数据|


- enctype：HTML 表单的属性，仅用于指定表单数据在POST请求中的编码方式，本质上是告诉浏览器如何设置请求的 Content-Type

|类型	|对应 Content-Type	|用途|
|--|--|--|
|application/x-www-form-urlencoded（默认）	|同左	|普通表单提交（键值对 URL 编码）
|multipart/form-data	                    |同左	|表单含文件上传时使用
|text/plain	                                |同左	|纯文本格式（罕见）


## 6.GET和POST
1. 发送的数据数量
在 GET 中，只能发送有限数量的数据，因为数据是在 URL 中发送的。
在 POST 中，可以发送大量的数据，因为数据是在正文主体中发送的。
2. 安全性
GET 方法发送的数据不受保护，因为数据在 URL 栏中公开，这增加了漏洞和黑客攻击的风险。
POST 方法发送的数据是安全的，因为数据未在 URL 栏中公开，还可以在其中使用多种编码技术，这使其具有弹性。
3. 加入书签中
GET 查询的结果可以加入书签中，因为它以 URL 的形式存在；而 POST 查询的结果无法加入书签中。
4. 编码
在表单中使用 GET 方法时，数据类型中只接受 ASCII 字符。
在表单提交时，POST 方法不绑定表单数据类型，并允许二进制和 ASCII 字符。
5. 可变大小
GET 方法中的可变大小约为 2000 个字符。
POST 方法最多允许 8 Mb 的可变大小。
6. 缓存
GET 方法的数据是可缓存的，而 POST 方法的数据是无法缓存的。
7. 主要作用
GET 方法主要用于获取信息。而 POST 方法主要用于更新数据。



## 7.http头部信息与缓存
- 响应头
  - Expires：过期时间(绝对时间)，浏览器再次发送请求前检查是否超过该时间，0或-1为不缓存
  - Cache-control：(http1.1)
     - public：无条件缓存响应（仅响应头）
     - private：针对单个用户缓存响应（仅响应头）
     - no-cache：回服务器校验，未被改则调用缓存
     - no-store：任何情况下都不被缓存
     - max-age(相对时间)：max-age优先级比Expires高
  - Pragma: no-cache，浏览器不要缓存网页(http1.0)
  - Last-Modified：最后一次修改时间
  - Etag：被请求变量的实体标记，优先级高于Last-Modified
- 请求头
  - Cache-control
  - If-Modified-Since：值为Last-Modified，发送给服务端对比时间，选择返回200或304
  - If-None-Match：值为Etag，发送给服务端对比实体标记，选择返回200或304
- 页面访问缓存
1.判断是否有缓存，若有缓存判断是否过期
2.未过期则直接读取，过期则发送If-Modified-Since、If-None-Match判断，后者优先级更高
3.若服务器决策有更新则返回200以及新资源；没有更新则返回304并从缓存中读取


## 8.url解析到页面显示全过程
1. 输入网址。
2. 浏览器查找域名的IP地址。
3. 浏览器给服务器发送一个HTTP请求
4. 网站服务的永久重定向响应(网站存在不同域名指向同一网站的情况)
5. 浏览器跟踪重定向地址，向服务器发送另一个获取请求。
6. 服务器“处理”请求，服务器接收到获取请求
7. 服务器发回一个HTTP响应
8. 浏览器开始显示HTML
9. 浏览器发送请求，以获取嵌入在HTML中的资源对象。


## 9.http与https的区别
- https协议需要到CA申请证书，一般免费证书较少，因而需要一定费用。
- http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。
- http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
- http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全。
- https通信过程：
  - 访问服务器时，服务器向浏览器发送证书，证书内有公钥等信息
  - 浏览器生成对称密钥，使用公钥加密后发送给服务器
  - 服务器通过私钥进行解密得到对称密钥
  - 浏览器和服务器之间通过对称密钥加密通信
  - 握手阶段发送证书并协商对称密钥
- https证书：
  - 客户端自带CA认证中心的公钥
  - 包含信息：机构名称、证书数字签名、证书持有者公钥、证书签名用到的hash算法
  - 数字签名生成过程： 明文->通过 hash 算法->摘要->CA 私钥->数字签名
  - 辨别非法证书：浏览器无法识别即为危险证书 -> 根据CA名获取内置CA根证书、CA公钥 -> 根据公钥对数字签名解密，使用hash算法计算摘要，错误即非法证书


## 10.http获取进度
- axios的options配置项方法：onDownloadProgress与onUploadProgress
- 方法属性progress：progress.total与progress.loaded
- 示例：
```javascript
onUploadProgress: (progressEvent) => {
    if(progressEvent.lengthComputable){
        var complete =( (progressEvent.loaded / progressEvent.total) * 100) | 0
        this.percent = complete
    }
}
```


## 11.http安全
- XSS：跨站脚本攻击，恶意脚本代码植入到页面中从而实现盗取用户信息等操作
  - 反射性XSS：访问路径上有恶意脚本，点击路径访问时获取用户cookie信息并进行恶意操作
  - 持久性XSS：恶意脚本写在文章内，文章写入数据库中，每次访问文章都会执行恶意脚本
  - 预防措施：
     - 对输入、输出结果进行过滤和必要的转义
     - 尽量使用post，使用get方式时对路径长度进行限制
     - 使用httponly禁止黑客通过脚本获取用户cookie数据，无法完全阻止
- CSRF：跨站请求伪造，伪装成用户身份执行一些恶意操作或非法操作
  - 用户在访问其他网站时，其他网站向对应网站进行请求的非法操作，用户的cookie自动发送过去
  - 预防措施：
     - 验证码
     - tokenId令牌
     - 判断请求的Referer是否正确
- 登录信息
  - 直接传输http信息可能被抓包的形式进行抓取
    - 加密：利用加密算法将明文加密，存在截获密文直接发送给服务器同样能够登录的问题
    - token：每次请求携带token解决上面的问题，存在路由截取修改数据包字段的问题
    - 数字签名：将报文通过哈希函数生成报文摘要，再用私钥进行加密生成签名，签名与报文一起发给接收方。接受方也通过哈希函数生成报文摘要，用公钥对数字签名进行解密生成验证摘要。对比之下确定字段是否进行修改，从而解决上面的问题。数字签名是HTTPS能够确保数据完整性和防篡改的根本原因。
  - 对称加密算法
     - 需要同时修改代码
     - 加密算法在JS里面可能被直接破解并识别加密算法
  - 非对称加密算法
     - 公私钥机制进行加密（HTTPS）
     - 仅能保证传输过程不被截获，客户端和服务端仍可被截获
     - 客户端被恶意引导安装‘中间人’的WEB信任证书造成泄漏
  - JS加密函数
     - 采取策略：异步请求响应动态加载JS加密算法，服务端根据随机token返回加密策略
  - Token令牌
     - 后端生成token令牌并写入Redis数据库并设置token参数
     - token返回给前端，将token写入localStorage/store中
     - 路由守卫检测localStorage/store存在与否，否则进行跳转进登录界面，否则next()
     - 设置axios的拦截器进行拦截放入token
     - 后端从请求头中提取token并且判断token是否存在
     - 登录退出时后端从Redis数据库中删除，前端删除localStorage中token
- 拦截请求在头部加入token让服务端进行校验:
``` javascript
axios.interceptors.request.use(
  config => {
    const token = sessionStorage.getItem('token')
    if (token ) { // 判断是否存在token，如果存在的话，则每个http header都加上token
      config.headers.authorization = token  //请求头加上token
    }
    return config
  },
  err => {
    return Promise.reject(err)
  })
```
- 拦截响应在服务端传回对应code情况下清除token进行登出的操作:(非法或失效)
``` javascript
axios.interceptors.response.use(
  response => {
    //拦截响应，做统一处理 
    if (response.data.code) {
      switch (response.data.code) {
        case 1002:
          store.state.isLogin = false
          router.replace({
            path: 'login',
            query: {
              redirect: router.currentRoute.fullPath
            }
          })
      }
    }
    return response
  },
  //接口错误状态处理，也就是说无响应时的处理
  error => {
    return Promise.reject(error.response.status) // 返回接口返回的错误信息
  })
``` 
- 实现长时间未点击登出效果：最后一次点击时间与当前时间的差别
``` javascript

export default{
    name: 'App',
    data (){
        return {
            lTime: new Date().getTime(), // 最后一次点击的时间
            ctTime: new Date().getTime(), //当前时间
            tOut: 10 * 60 * 1000   //超时时间10min
        } 
    },
    mounted () {
        window.setInterval(this.tTime, 1000)
    }
 
    methods:{
        clicked () {
            this.lTime = new Date().getTime()  //当界面被点击更新点击时间
        }
 
        tTime() {
            this.cTime = new Date().getTime()
            if (this.ctTime -this.lTime > tOut) {
                if(JSON.parse(sessionStorage.getItem('Login')) === true){
                    // 退出登录
                }
            }
        }
    }
}
```
- 实现登出效果可以是删除token/localStorage
- 实现离开页面保存一定时间的登录状态：页面加载中通过localStorage中设置的过期时间进行运算比较，大于过期时间则跳转回登录界面，小于过期时间则照常运行；除此之外可以设置cookie也可实现过期时间



## 12.请求取消
- XMLHttpRequest

```javascript
// 创建 XMLHttpRequest 实例
const xhr = new XMLHttpRequest();
// 打开请求
xhr.open('GET', 'https://example.com/api/data', true);
// 发送请求
xhr.send();

// 模拟一段时间后取消请求
setTimeout(() => {
    if (xhr.readyState!== 4) {
        xhr.abort();
        console.log('请求已取消');
    }
}, 2000);

```
- Fetch API

```javascript
// 创建 AbortController 实例
const controller = new AbortController();
const signal = controller.signal;

// 发起 Fetch 请求并关联信号
fetch('https://example.com/api/data', { signal })
   .then(response => {
        if (!response.ok) {
            throw new Error('请求失败');
        }
        return response.json();
    })
   .then(data => {
        console.log('请求成功:', data);
    })
   .catch(error => {
        if (error.name === 'AbortError') {
            console.log('请求已取消');
        } else {
            console.error('请求出错:', error);
        }
    });

// 模拟一段时间后取消请求
setTimeout(() => {
    controller.abort();
}, 2000);

```
- Axios

```javascript
import axios from 'axios';

// 创建 AbortController 实例
const controller = new AbortController();
const signal = controller.signal;

// 发起 Axios 请求并关联信号
axios.get('https://example.com/api/data', { signal })
   .then(response => {
        console.log('请求成功:', response.data);
    })
   .catch(error => {
        if (axios.isCancel(error)) {
            console.log('请求已取消');
        } else {
            console.error('请求出错:', error);
        }
    });

// 模拟一段时间后取消请求
setTimeout(() => {
    controller.abort();
}, 2000);

```

## 13.竞态条件处理
- 概念：相同的接口发送的http请求前后返回时，出现旧数据覆盖新数据的情况
- 使用 AbortController 取消上一个请求：不消耗非必要网络资源

```javascript
// 在 Vue 组件中
import axios from 'axios';

export default {
  data() {
    return {
      currentPage: 1,
      tableData: [],
      abortController: null, // 存储 AbortController 实例
    };
  },
  methods: {
    async fetchData(page) {
      // 取消上一个未完成的请求
      if (this.abortController) {
        this.abortController.abort();
      }

      // 创建新的 AbortController
      this.abortController = new AbortController();

      try {
        const response = await axios.get(`/api/data?page=${page}`, {
          signal: this.abortController.signal, // 绑定取消信号
        });
        this.tableData = response.data;
        this.currentPage = page;
      } catch (error) {
        if (axios.isCancel(error)) {
          console.log('请求已取消', error.message);
        } else {
          console.error('请求失败', error);
        }
      }
    },
    handlePageChange(page) {
      this.fetchData(page);
    },
  },
};
```

- 通过请求标记（Request Token）忽略过期响应：消耗非必要网络资源

```javascript
export default {
  data() {
    return {
      currentPage: 1,
      tableData: [],
      fetchToken: 0, // 请求标记
    };
  },
  methods: {
    async fetchData(page) {
      const token = Date.now(); // 生成唯一标记
      this.fetchToken = token;

      try {
        const response = await axios.get(`/api/data?page=${page}`);
        
        // 只有当前请求是最新的才更新数据
        if (this.fetchToken === token) {
          this.tableData = response.data;
          this.currentPage = page;
        }
      } catch (error) {
        if (this.fetchToken === token) {
          console.error('请求失败', error);
        }
      }
    },
    handlePageChange(page) {
      this.fetchData(page);
    },
  },
};
```