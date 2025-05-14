# JQuery

标签（空格分隔）： 技术栈

---

## 1.语法
- $(selector).action(function(){});

- $(document).ready(function(){})

- 另一种表示方法$(function(){}):确保文档完全加载后执行jQuery函数
  与window.onload=function(){}区别:jQuery在DOM结构加载完毕后执行，JavaScript全部加载完成再执行

## 2.效果示例
- hide(duration,string,callback)
- show(duration,string.callback)
- toggle(duration,string,callback):显示或隐藏

## 3.动画
- animate({attribute,})
- 队列功能:编写多个animate()调用或采用链的方式书写，创建某个控件内部队列
- 停止方法：stop(stopAll,goToEnd):stopAll:停止队列内所有动画，goToEnd:是否立即完成当前动画

## 4.DOM
- 返回内容：
  - $(element).text()/html()/val()

  - $(element).attr(attribute):自定义属性

  - $(element).prop(property):自带属性

- 设置内容：
  - $(element).text(string)/html(string)/val(string)

  - $(element).attr (attribute,string) / ({attribute:string,attribute:string})
  
  - DOM操作均可执行回调函数

- 添加元素:$(element).append()/prepend()/after()/before()

- 删除元素:$(element).remove()/empty(),函数内接受参数(选择器语法)作为过滤器
    
- 操作CSS:addClass()/removeClass()/toggleClass()
  css()返回的是第一个匹配到的元素的css属性

## 5.遍历
- 祖先：
  - parent():直接父元素
  - parents():接受参数(选择器语法)作为过滤器
  - parentUntil():接受参数(元素)返回两元素之间
- 后代
  - children():直接子元素，接受参数(选择器语法)作为过滤器
  - find():所有符合过滤器要求的后代
- 过滤
  - eq():被选元素中对应索引号的元素

## 6.AJAX
- $(selector).load(URL,data,callback) : 加载内容将写入元素中
  
  - callback(responseTxt,statusTXT,xhr):调用内容、调用状态、XMLHttpRequest对象

- $.get(URL,callback)

  - callback(data,status)
- $.post(URL,data,callback)
  - callback(data,status)
      



