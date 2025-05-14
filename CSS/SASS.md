# SASS

标签（空格分隔）： CSS

---

## 1.变量
变量声明：属性名前加上$作为变量名可在css内重用
```css
$nav-color: #F90;
nav {
  $width: 100px;
  width: $width;
  color: $nav-color;
}
//编译后
nav {
  width: 100px;
  color: #F90;
}
```


## 2.嵌套CSS规则
嵌套方法：相当于后代选择器
```css
#content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  aside { background-color: #EEE }
}
// 编译后
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```

父选择器子组合选择器和同层组合选择器：&、>、+和~
```css
article a {
  color: blue;
  &:hover { color: red }
}
// 编译后
article a { color: blue }
article a:hover { color: red }
```

```css
// 嵌套属性

nav {
  border: {
      style: solid;
      width: 1px;
      color: #ccc;
  }
}
```


## 3.导入SASS文件
- `@import 文件名` 即可进行引入
- CSS与SASS的区别：
  - CSS中@import引用其他css文件，运行时加载，页面加载缓慢
  - SASS中@import引用其他sass文件，编译时加载生成一个完整的css文件
- 局部SASS：
  - 定义：局部SASS文件不需要独立生成css，仅作其他sass文件调用
  - 命名：文件名前缀加\_，调用时不需要该前缀
- 默认变量值：被引入文件定义`$fancybox-width: 400px !default;`引入文件中如果对该变量没有定义，则使用被引入文件中的该变量
- 嵌套导入：`.blue-theme {@import "blue-theme"}`即可引入对应文件内的样式
- 原生的css导入：导入文件后缀是css、css文件的url、url地址时都会采用默认的css@import


## 4.混合器
混合器：对常用单元的复用，但生成css文件大小不变，仅为展示性复用
```css
// @mixin定义 @include调用

@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}
```

```css
// 混合器定义入参
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}

// 混合器参数带有默认值
@mixin link-colors($normal, $hover: $normal, $visited: $normal){
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}

// 函数调用按顺序入参
a {
  @include link-colors(blue, red, green);
}

// 给对应参数赋值
a {
    @include link-colors(
      $normal: blue,
      $visited: green,
      $hover: red
  );
}
```


## 5.继承
继承：相较于混合器的展示性复用，继承为语义性复用，生成的css文件减少冗余代码
```css
// 继承与该选择器相关的所有组合选择器样式

.error {
  border: 1px solid red;
  background-color: #fdd;
}

//应用到.seriousError a
.error a{
  color: red;
  font-weight: 100;
}

//应用到hl.seriousError
h1.error {
  font-size: 1.2rem;
}

//.seriousError从.error继承所有相关样式
.seriousError {
  @extend .error;
  border-width: 3px;
}
```
