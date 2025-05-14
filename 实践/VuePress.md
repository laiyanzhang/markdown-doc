# VuePress

标签（空格分隔）： 实践

---

## 1.目录结构
```
├── docs
│   ├── .vuepress (可选的) 用于存放全局的配置、组件、静态资源等
│   │   ├── components (可选的) Vue 组件将会被自动注册为全局组件
│   │   ├── theme (可选的) 存放本地主题
│   │   │   └── Layout.vue 网页布局渲染的统一标准
│   │   ├── public (可选的) 静态资源目录
│   │   ├── styles (可选的) 存放样式相关的文件
│   │   │   ├── index.styl 被自动应用的全局样式文件，具有比默认样式更高的优先级
│   │   │   └── palette.styl 重写默认颜色常量，或者设置新的 stylus 颜色常量
│   │   ├── templates (可选的, 谨慎配置) 存储 HTML 模板文件
│   │   │   ├── dev.html 用于开发环境的 HTML 模板文件
│   │   │   └── ssr.html 构建时基于 Vue SSR 的 HTML 模板文件
│   │   ├── config.js (可选的) 配置文件的入口文件
│   │   └── enhanceApp.js (可选的) 客户端应用的增强
│   │ 
│   ├── README.md
│   ├── guide
│   │   └── README.md
│   └── config.md
│ 
└── package.json
```

## 2.路径
- 页面路径：
  - 根路径：docs/
  - 默认路径：'/'默认获取README文件
- 资源路径
  - 相对路径：当前文件相对路径，`![An image](./image.png)`
  - 别名路径：config.js中配置别名，`![Image from alias](~@alias/image.png)`
  - 公共路径：public内部资源作为md文件中根路径进行访问，`![An image](/page.png)`
  - 基础路径：config中设置 base，`<img :src="$withBase('/foo.png')">`
  - 附：基础路径作为网站部署路径配置，配置后在config文件中作为所有资源的默认前缀路径



## 3.enhanceApp.js
- 应用级别配置，安装一些附加的Vue插件、注册全局组件，或者增加额外的路由钩子等
```
export default ({
  Vue, // VuePress 正在使用的 Vue 构造函数
  options, // 附加到根实例的一些选项
  router, // 当前应用的路由实例
  siteData, // 站点元数据
  isServer // 当前应用配置是处于 服务端渲染 或 客户端
}) => {
  // ...做一些其他的应用级别的优化
}
```





## 4.markdown中使用vue
- 组件访问浏览器/DOM的API
``` 
// 只能在beforeMount / mounted 访问浏览器 / DOM 的 API。
mounted () {
    import('./lib-that-access-window-on-import').then(module => {})
}
```
- 组件
  - 定义：.vuepress/components 中文件将会自动地被注册为全局的异步组件
  - 使用：`<demo-1/>`自定义组件名字需要包含连接符或PascalCase(单词首字母大写)
- 脚本样式提升：Markdown 文件中使用原生的 < script > 或者 < style > 标签


## 5.markdown插槽
- md文件插槽
```
::: slot header
# Here might be a page title
:::

- A Paragraph
- Another Paragraph

::: slot footer
Here's some contact info
:::
```
- vue布局组件Layout.vue
```
<template>
  <div class="container">
    <header>
      <Content slot-key="header"/>
    </header>
    <main>
      <Content/>
    </main>
    <footer>
      <Content slot-key="footer"/>
    </footer>
  </div>
</template>
```
- html文件
```
<div class="container">
  <header>
    <div class="content header">
      <h1>Here might be a page title</h1>
    </div>
  </header>
  <main>
    <div class="content default">
      <ul>
        <li>A Paragraph</li>
        <li>Another Paragraph</li>
      </ul>
    </div>
  </main>
  <footer>
    <div class="content footer">
      <p>Here's some contact info</p>
    </div>
  </footer>
</div>
```


## 6.config主要配置
```
module.exports = {
  title: 'Hello VuePress',               // 网页导航栏中的标题
  description: 'Just playing around'     // <meta>标签
  base: '/'                              // 公共资源基础路径
  head: [  ['link', { rel: 'icon', href: '/logo.png' }] ]  // <head>标签
  host: '0.0.0.0'                        // dev server 主机名
  port: 8080                             // dev server 端口
  temp：'/path/to/@vuepress/core/.temp'  // 客户端文件临时目录
  permalink: "/:year/:month/:day/:slug"  // 设置永久链接生成形式取代路径链接
  theme：undefined                       // 使用自定义主题
  themeConfig： {  }                     // 主题配置
  markdown: {
    lineNumbers：undefined               // 每个代码块左侧是否显示行号
    anchor: { permalink: false },        // markdown-it-anchor 的选项
    toc: { includeLevel: [1, 2] },       // markdown-it-toc 的选项
    extendMarkdown: md => {
      md.use(require('markdown-it-xxx')) // 使用更多的 markdown-it 插件
    }
  }
  configureWebpack: {                    // webpack配置
    resolve: {
      alias: {
        '@alias': 'path/to/some/dir'     // 路径别名
      }
    }
  }
}
```


## 7.全局计算属性
- 网站元数据：\$site
```
{
  "title": "VuePress",
  "description": "Vue 驱动的静态网站生成器",
  "base": "/",
  "pages": [
    {
      "lastUpdated": 1524027677000,
      "path": "/",
      "title": "VuePress",
      "frontmatter": {}
    },
    ...
  ]
}
```
- 页面元数据：\$page
```
{
  "title": "Global Computed",
  "frontmatter": {
    "sidebar": "auto"
  },
  "regularPath": "/zh/miscellaneous/global-computed.html",
  "key": "v-bc9a3e3f9692d",
  "path": "/zh/miscellaneous/global-computed.html",
  "headers": [
    {
      "level": 2,
      "title": "$site",
      "slug": "site"
    },
    {
      "level": 2,
      "title": "$page",
      "slug": "page"
    },
    ...
  ]
}
```
- 主题配置：\$themeConfig


## 8.开发主题
- 主题配置目录结构
```
theme
├── global-components  // 目录下组件自动注册为全局组件
│   └── xxx.vue
├── components  // Vue组件
│   └── xxx.vue
├── layouts  // 布局组件
│   ├── Layout.vue (必要的)
│   └── 404.vue
├── styles  // 全局样式和调色板
│   ├── index.styl
│   └── palette.styl
├── templates  // 修改默认的模板文件
│   ├── dev.html
│   └── ssr.html
├── index.js  // 主题文件入口文件
├── enhanceApp.js  // 主题水平的客户端增强文件
└── package.json
```
- config配置主题
```
themeConfig: {
    logo: '/头像.jpg',         // 顶部左上角图标
    lastUpdated: '最近更新',   // 最后更新时间标识
    nav: [                     // 顶部导航栏配置
        { text: '版本发布', link: '/markdown/publish/' },
        { text: '技术能力', link: '/markdown/technology/' },
    ],
    displayAllHeaders: false,  // 默认只显示当前页面标题组成链接
    activeHeaderLinks: true,   // 默认根据滚动实时更新url中的链接hash值
    sidebar: {                 // 侧边栏配置
        '/markdown/publish/': [ 
            '', 'doc1', 'doc2' // ''默认为当前文件夹下README
        ],

        '/markdown/technology/': [
            '',
            {                 // 配置折叠导航
                title: 'IDE操作手册',
                children: ['doc1', 'doc2']
            }, {
                title: '控件',
                children: ['doc3', 'doc4']
            }
        ]
    }
}
```
- 获取原有默认主题：yarn vuepress eject，获取主题后进行文件夹覆盖后即可进行自定义更改


## 9.全局搜索
- 场景：vuepress默认只支持标题级别的搜索
- 安装插件：
```
npm i vuepress-plugin-fulltext-search -D
# or
yarn add -D vuepress-plugin-fulltext-search -D
```
- 引入插件：
``` 
// config配置文件
module.exports = {
   plugins: ['fulltext-search']
}
```


## 10.ant-design-vue
- enhanceApp.js：
```
import Antd from 'ant-design-vue'
import 'ant-design-vue/dist/antd.css'
​
export default ({ Vue }) => {
  Vue.use(Antd)
}
```
- 兼容报错
```
module.exports = {
    chainWebpack: config => {
        config.resolve.alias.set('core-js/library/fn', 'core-js/features')
    }
}
```



## 11.项目热更新
- package.json："vuepress dev docs" => "vuepress dev docs --temp .temp"
- 缺陷：生成多一个.temp文件夹


## 12.修改原有样式变量
- .vuepress/styles/palette.styl
```
// 颜色
$accentColor = #3eaf7c
$textColor = #2c3e50
$borderColor = #eaecef
$codeBgColor = #282c34
$arrowBgColor = #ccc
$badgeTipColor = #42b983
$badgeWarningColor = darken(#ffe564, 35%)
$badgeErrorColor = #DA5961

// 布局
$navbarHeight = 3.6rem
$sidebarWidth = 20rem
$contentWidth = 740px
$homePageWidth = 960px

// 响应式变化点
$MQNarrow = 959px
$MQMobile = 719px
$MQMobileNarrow = 419px
```
