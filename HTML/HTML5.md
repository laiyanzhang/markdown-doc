# HTML5

标签（空格分隔）： HTML

---

## 1.canvas元素
- 内部坐标：坐标均以左上角为(0, 0)，单一坐标均作为起始坐标
- 创建对象：
```
<canvas id="myCanvas" width="200" height="100"></canvas>

var c=document.getElementById("myCanvas");
var ctx=c.getContext("2d");
```
- 绘制线条：
```
ctx.moveTo(0,0); // 定义开始坐标
ctx.lineTo(200,100); // 定义结束坐标
ctx.stroke(); // 绘制线条
```
- 绘制圆：
```
ctx.beginPath();
ctx.arc(95, 50, 40, 0, 2*Math.PI); // (95, 50)为圆心，40为半径，（0，2*Math.PI）对应绘制周长
ctx.stroke();
```
- 定义背景颜色/图案：`ctx.fillStyle`
- 定义字体：`ctx.font`
- 绘制矩形：`ctx.fillRect(x, y, width, height)`
- 绘制实心文本：`ctx.fillText(text, x, y)`
- 绘制空心文本：`ctx.strokeText(text, x, y)`
- 绘制图片：`ctx.drawImage(img, x, y);`


## 2.SVG元素
- canvas元素
  - 依赖分辨率
  - 不支持事件处理器
  - 弱的文本渲染能力
  - 能够以 .png 或 .jpg 格式保存结果图像
  - 最适合图像密集型的游戏，其中的许多对象会被频繁重绘
- svg元素
  - 不依赖分辨率
  - 支持事件处理器
  - 最适合带有大型渲染区域的应用程序（比如谷歌地图）
  - 复杂度高会减慢渲染速度（任何过度使用 DOM 的应用都不快）
  - 不适合游戏应用

## 3.拖放
```
function allowDrop(ev)
{
    ev.preventDefault();
}
 
function drag(ev)
{
    ev.dataTransfer.setData("Text",ev.target.id);
}
 
function drop(ev)
{
    ev.preventDefault();
    var data=ev.dataTransfer.getData("Text");
    ev.target.appendChild(document.getElementById(data));
}

<div id="div1" ondrop="drop(event)" ondragover="allowDrop(event)"></div>

<img id="drag1" src="/images/logo.png" draggable="true" ondragstart="drag(event)">
```


## 4.地图定位
```
function getLocation()
{
	if (navigator.geolocation)
	    navigator.geolocation.getCurrentPosition(showPosition,showError);
	else 
	    x.innerHTML="该浏览器不支持定位。";
}

function showError(error)
{
	switch(error.code) 
	{
		case error.PERMISSION_DENIED:
			x.innerHTML="用户拒绝对获取地理位置的请求。"
			break;
		case error.POSITION_UNAVAILABLE:
			x.innerHTML="位置信息是不可用的。"
			break;
		case error.TIMEOUT:
			x.innerHTML="请求用户地理位置超时。"
			break;
		case error.UNKNOWN_ERROR:
			x.innerHTML="未知错误。"
			break;
	}
}
```

## 5.video/audio
支持通过js控制DOM元素的play播放、pause方法
```
<video id="video1" width="420">
    <source src="mov_bbb.mp4" type="video/mp4">
    <source src="mov_bbb.ogg" type="video/ogg">
    您的浏览器不支持 HTML5 video 标签。
</video>
<audio controls>
  <source src="horse.ogg" type="audio/ogg">
  <source src="horse.mp3" type="audio/mpeg">
您的浏览器不支持 audio 元素。
</audio>
```



## 6.input
`type属性支持不同的输入类型，非所有浏览器均支持`

- color：选色器
- date/datetime/datetime-local/month/week/time：选择日期时间
- email：表单提交时校验邮箱地址格式
- url：表单提交时校验url格式
- tel：便捷输入号码
- range/number：输入数字
  - min/max：最小最大值
  - required：表单校验必填
  - pattern：数字验证模式
  - step：数字间隔
  

## 7.表单新元素
- `<datalist>`：定义选项列表，结合input元素，提供下拉选项
- `<keygen>`：标签规定用于表单的密钥对生成器字段
- `<output>`：标签规定用于表单的密钥对生成器字段


## 8.新属性
### 1.form元素
- autocomplete：自动完成，输入时显示填写的选项
- novalidate：提交表单时不进行表单校验


### 2.input元素
- autofocus：页面加载时输入框自动获取焦点
- form：规定输入域属于的一个或多个表单
- formaction：表单提交的url地址，可覆盖表单的action属性（type="submit/image"）
- formenctype：表单提交到服务器的数据编码（type="submit/image"）
- formmethod：表单提交的方式（type="submit/image"）
- formnovalidate：提交表单时不进行表单校验，可覆盖表单的novalidate属性（type="submit"）
- formtarget：表单提交数据接收后的展示（type="submit/image"）
- height/width：image类型的高度与宽度（type="image"）
- list：规定输入域的datalist
- min/max/step：制定日期/数字等类型的约束（type="date pickers/number/range"）
- multiple：可选择多个值（type="email/file"）
- pattern：正则表达式验证（type="text/search/url/tel/email/password"）
- placeholder：描述期待值（type="text/search/url/tel/email/password"）
- required：必填（type="text/search/url/telephone/email/password/date pickers/number/checkbox/radio/file"）



## 9.语义元素
- `<section>`：定义文档中的节，比如章节、页眉、页脚或文档中的其他部分
- `<article>`：定义独立的内容
- `<nav>`：定义导航链接的部分
- `<aside>`：定义页面主区域内容之外的内容（比如侧边栏）
- `<header>`：描述了文档的头部区域，主要用于定义内容的介绍展示区域
- `<footer>`：描述了文档的底部区域
- `<figure>`：规定独立的流内容（图像、图表、照片、代码等等）
- `<figcaption>`：定义`<figure>`的标题，应该被置于"figure"的第一个或最后一个子元素的位置


## 10.web存储
- localStorage与sessionStorage
- web sql与indexdb：localforge第三方库



## 11.web worker
- web worker：运行在后台的 JavaScript，独立于其他脚本，不会影响页面的性能
- 定义：内部执行计算脚本，无法访问js对象，通过postmessage传递数据
```
var i=0;

function timedCount()
{
    i=i+1;
    postMessage(i);
    setTimeout("timedCount()",500);
}

timedCount();
```
- 使用：通过onmessage获取脚本计算结果
```
w = new Worker("demo_workers.js"); // 脚本文件创建线程
w.onmessage=function(event){
    document.getElementById("result").innerHTML=event.data;
};
w.terminate(); // 终止线程，释放资源
```


## 12.SSE
- SSE：网页自动获取来自服务器的更新，适用于服务器单向高频的数据发送
- 使用：接受服务器对应url传输的数据
```
var source=new EventSource("demo_sse.php");
source.onmessage=function(event)
{
    document.getElementById("result").innerHTML+=event.data + "<br>";
};
```

## 13.WebSocket
- 定义：客户端与服务端全双工通信
```
var ws = new WebSocket("ws://localhost:9998/echo");
                
ws.onopen = function()  // 连接建立时触发
{
  // Web Socket 已连接上，使用 send() 方法发送数据
  ws.send("发送数据");
  alert("数据发送中...");
};

ws.onmessage = function (evt) // 客户端接收服务端数据时触发
{ 
  var received_msg = evt.data;
  alert("数据已接收...");
};

ws.onclose = function() // 连接关闭时触发
{ 
  // 关闭 websocket
  alert("连接已关闭..."); 
};
```
