# webpack

标签（空格分隔）： 技术栈

---

## 1.前端工程化
- 1.技术选型
- 2.统一规范
- 3.前端组件化
- 4.测试

### 静态文件
- public
  - 特点：webpack对该目录下的资源进行简单的复制
  - 引用路径：`src="'/images/a.jpg'"`（绝对路径，默认从public中获取）
- src/assets
  - 特点：webpack将大图片放入dist/img文件夹，小图片以base64编码格式内联
  - 引用路径：`src="'../assets/a.jpg'"`（相对路径，根据当前文件项目获取）
  - 动态绑定：`:src="require('../assets/a.jpg')"`使用require触发对url的解析
  - 优点：压缩打包处理、编译时监测、文件哈希

### 模块加载
- commonJS：运行时加载，只可获取整个模块，无法进行静态处理
- ES6：编译时加载，可获取模块中的部分内容，可以进行静态处理
  


## 2.vue.config.js
```javascript
module.exports = {
    publicPath: './',
    // 当运行`vue-cli-service build`时生成的生产环境构建文件的目录，构建前目录内容清空
    outputDir: 'dist',
    // 放置生成的静态资源 (js、css、img、fonts) 的 (相对于 outputDir 的) 目录
    assetsDir: '',
    // 指定生成的 index.html 的输出路径 (相对于 outputDir)
    indexPath: 'indexPath',
    // 生成的静态资源在它们的文件名中包含了 hash 以便更好的控制缓存
    filenameHashing: true,
    // 多页面应用中每个page对应一个JavaScript入口文件
    pages: {
        index: {
          // page 的入口
          entry: 'src/index/main.js',
          // 模板来源
          template: 'public/index.html',
          // 在 dist/index.html 的输出
          filename: 'index.html',
          // 站点标题
          title: 'Index Page',
          // 在这个页面中包含的块，默认情况下会包含
          // 提取出来的通用 chunk 和 vendor chunk。
          chunks: ['chunk-vendors', 'chunk-common', 'index']
        }
    }
    // 使用eslint-loader检验代码
    lintOnSave: 'default',
    // 使用.vue文件则false，使用template属性则true
    runtimeCompiler: false,
    // 是否对node_modules文件进行转译，数组形式则对数组内包进行转译
    transpileDependencies：false,
    // 是否需要生产环境的source map
    productionSourceMap：true,
    // 设置生成的 HTML 中 <link rel="stylesheet"> 和 <script> 标签的 crossorigin 属性
    crossorigin: undefined,
    // 是否在生成的 HTML 中的 <link rel="stylesheet"> 和 <script> 标签上启用 SRI
    integrity: false,
    // webpack配置
    configureWebpack: Object | Function
    // CSS选项
    css: { 
        extract: process.env.NODE_ENV === "production",
        sourceMap: false,
        requireModuleExtension: true,
        loaderOptions: {}
    },
    devServer: {
        port: 2021, // 设置端口号
        host: '10.32.120.69', // ip 本地
        disableHostCheck: true, //是否关闭用于 DNS 重绑定的 HTTP 请求的 HOST 检查
        hotOnly: false, // 热更新
        https: false, // https:{type:Boolean}配置前缀
        open: false, //配置自动启动浏览器
        proxy: null,  //设置代理
        proxy: { //目的是解决跨域，若测试环境不需要跨域，则不需要进行该配置
            '/api': { // 拦截以 /api 开头的url接口
                target: 'https://api.taobao.cn/', //目标接口域名
                changeOrigin: true, //是否跨域
                ws: true, //如果要代理 websockets，配置这个参数
                secure: false, // 如果是https接口，需要配置这个参数
                pathRewrite: { // 标识替换
                   '^/api': '/'   //重写接口 后台接口指向不统一  所以指向所有/
                }
            }
        }
    }
}
```

### publicPath
- 默认值：'/'
- 相对路径：'./'，可部署在任意路径下，在history模式下注意修改router的base
- 绝对路径：'/center/'，部署在指定的路径下，在逻辑上对项目进行划分
- 关联：BASE_URL的值与该值相同


### lintOnSave
- 默认值：'default'
- 用途：是否在开发环境下通过 eslint-loader 在每次保存时 lint 代码
- false：不进行检验
- true/'warning'：检验失败后编译正确但输出警告
- 'default'/'error'：检验失败后编译失败且输出错误
- process.env.NODE_ENV !== 'production'：生产构建时禁用eslint-loader


### configureWebpack
```
// 对象形式，配置字段将被 webpack-merge 合并到最后的 webpack 配置中，某些字段是基于vue.config.js配置的则无法在此配置，比如outputDir、publicPath

configureWebpack: {
    plugins: [
      new MyAwesomeWebpackPlugin()
    ]
}

// 函数形式，获取到解析后的config配置再进行修改

configureWebpack: config => {
    if (process.env.NODE_ENV === 'production') {
      // 为生产环境修改配置...
    } else {
      // 为开发环境修改配置...
    }
}
```


### chainWebpack

- 修改loader选项
```javascript
chainWebpack: config => {
    config.module
      .rule('vue')
      .use('vue-loader')
        .tap(options => {
          // 修改它的选项...
          return options
        })
}
```

- 添加一个新的 Loader
```javascript
chainWebpack: config => {
    // GraphQL Loader
    config.module
      .rule('graphql')
      .test(/\.graphql$/)
      .use('graphql-tag/loader')
        .loader('graphql-tag/loader')
        .end()
      // 你还可以再添加一个 loader
      .use('other-loader')
        .loader('other-loader')
        .end()
  }
```

- 替换一个规则里的 Loader
```javascript
chainWebpack: config => {
    const svgRule = config.module.rule('svg')

    // 清除已有的所有 loader。
    // 如果你不这样做，接下来的 loader 会附加在该规则现有的 loader 之后。
    svgRule.uses.clear()

    // 添加要替换的 loader
    svgRule
      .use('vue-svg-loader')
        .loader('vue-svg-loader')
}
```

- 修改插件选项
```javascript
chainWebpack: config => {
    config
      .plugin('html')
      .tap(args => {
        return [/* 传递给 html-webpack-plugin's 构造函数的新参数 */]
      })
  }
```

### CSS选项
- `modules`：v4 起已`弃用`，使用css.requireModuleExtension，含义与其相反
- `requireModuleExtension`：默认`true`；默认情况下，只有 *.module.[ext] 结尾的文件才会被视作 CSS Modules 模块。设置为 false 后你就可以去掉文件名中的 .module 并将所有的 *.(css|scss|sass|less|styl(us)?) 文件视为 CSS Modules 模块。
- `extract`：默认生产环境`true`，开发环境`false`；是否将组件中的 CSS 提取至一个`独立CSS文件`中 (而不是动态注入到 JavaScript 中的 inline 代码)，开发环境下设为true则无法进行`CSS热重载`
- `sourceMap`：默认`false`；是否为 CSS 开启`source map`。设置为 true 之后可能会影响构建的性能
- `loaderOptions`：向 CSS 相关的 loader 传递选项

### devServer选项
- `inline`：默认`true`，用于设置代码保存时是否自动刷新页面
- `hot`：默认`true`，用于设置代码保存时是否进行热更新（局部更新）
- `open`：默认`false`，用于设置server启动后是否自动打开浏览器
- `openPage`：指定deserver 编译完成后自动打开的页面，配合`open`，hash模式下url前加上`/#`
- `https`：默认`false`，是否启用https
- `port`：指定要监听请求的端口号
- `host`：默认`localhost`，指定主机，希望服务器外部可访问则`0.0.0.0`
- `compress`：默认`false`，对devServer 所有服务启用 gzip 压缩，减少服务器向前端传输的数据量
- `headers`：所有响应中添加头部信息
- `overlay`：默认`false`，是否浏览器全屏显示错误或警告
- `proxy`：配置跨域信息
```
'/api': {
    target: 'https://steampy.com/', // 修改baseURL为target的对应路由
    ws: true, // 开启websocket
    changOrigin: true, // 开启代理
    pathRewrite: {
        '^/api': '' // 将对应的路径名进行重写，不影响http地址，但影响实际地址
    }
}
```


## 2.vue-cli
- 特性：
  - webpack：带有合理的webpack默认配置
  - 自定义配置：内置文件配置，3.0前`webpack.config.js`，3.0后`vue.config.js`
- 快速原型开发：对单个vue文件进行快速原型开发
  - vue serve：需要安装全局依赖，机器一致性无法保证
  - vue build：将目标文件构建成一个生产环境的包用来部署


### 1.CLI插件
- 特性
  - 新建项目时根据选择特性被预安装
  - @vue/cli-plugin-作为前缀
  - 可修改webpack配置
  - 可向vue-cli-service注入命令
- 现有项目安装插件：vue add [ plugin ]
- 引入项目本地的插件（待深入）
```javascript
// package.json
{
  "vuePlugins": {
    "service": ["my-commands.js"]
    "ui": ["my-ui.js"]
  }
}
```
- Preset：新建项目时的预定义JSON对象，存储于~/.vuerc
```javascript
{
  "useConfigFiles": true,
  "cssPreprocessor": "sass",
  "plugins": {
    "@vue/cli-plugin-babel": {},
    "@vue/cli-plugin-eslint": {
      "version": "^3.0.0", // 指定插件版本，无则默认最新
      "config": "airbnb",
      "lintOn": ["save", "commit"],
      "prompts": true // 允许创建项目时自定义命令提示
    },
    "@vue/cli-plugin-router": {},
    "@vue/cli-plugin-vuex": {}
  }
  // "useConfigFiles":true，configs字段合并到vue.config.js中
  "configs": {
    "vue": {...},
    "postcss": {...},
    "eslintConfig": {...},
    "jest": {...}
  }
}
```


### 2.CLI服务
- `vue-cli-service serve`：启动一个开发服务器
- `vur-cli-service build`：产生可用于生产环境的包
- `vur-cli-service inspect`：审查一个Vue CLI项目的webpack config


### 3.浏览器兼容性
- `vue-cli-service build --modern`：产生两个应用的版本，根据浏览器按需加载
  - `<script type="module">`：被支持语言特性的浏览器加载的包
  - `<script nomodule>`：被不支持语言特性的浏览器加载的包


### 4.HTML
- `public/index.html`：在构建过程中，资源链接会被自动注入。另外Vue CLI会自动注入resource hint(preload/prefetch、manifest 和图标链接 (当用到 PWA 插件时)） 以及构建过程中处理的 JavaScript 和 CSS 文件的资源链接。
- 插值：可以使用lodash template插入内容
  - <%= VALUE %> 用来做不转义插值；
  - <%- VALUE %> 用来做 HTML 转义插值；
  - <% expression %> 用来描述 JavaScript 流程控制
- Preload：初始化加载
   - `<link rel="preload">`：指定**页面加载后很快用到资源**
   - 默认情况：所有初始化渲染需要的文件自动生成 preload 提示
   - `@vue/preload-webpack-plugin`：负责注入，可通过chainWebpack的`config.plugin('preload')` 进行修改和删除
- Prefetch：空闲时加载
  - `<link rel="prefectch">`：利用**空闲时间提前获取用户未来可能会访问的内容**
  - 默认情况：所有作为 async chunk 生成的 JavaScript 文件 (通过动态 import() 按需 code splitting 的产物) 自动生成 prefetch 提示。
  - `@vue/preload-webpack-plugin`：负责注入，可通过chainWebpack的`config.plugin('prefetch')` 进行修改和删除
```
// vue.config.js
module.exports = {
  chainWebpack: config => {
    // 移除 prefetch 插件
    config.plugins.delete('prefetch')

    // 或者
    // 修改它的选项：
    config.plugin('prefetch').tap(options => {
      options[0].fileBlacklist = options[0].fileBlacklist || []
      options[0].fileBlacklist.push(/myasyncRoute(.)+?\.js$/)
      return options
    })
  }
}

// 移除插件情况下进行手动注释
import(/* webpackPrefetch: true */ './someAsyncComponent.vue')
```

### 5.静态资源
- 处理方式
  - 在JavaScript被导入或在template/CSS中通过相对路径被引用。这类引用会被webpack处理
  - 放置在public目录下或通过绝对路径被引用。这类资源将直接被拷贝而不经过webpack处理
- 从相对路径导入：资源url都会被解析为一个模块依赖，chainWebpack调整内联文件的大小限制
```
// vue.config.js
module.exports = {
  chainWebpack: config => {
    config.module
      .rule('images')
        .set('parser', {
          dataUrlCondition: {
            maxSize: 4 * 1024 // 4KiB
          }
        })
  }
}
```
- URL转换规则
  - 绝对路径：保留
  - .开头：作为相对模块请求
  - ~开头：作为模块解析请求，可以引用Node模块资源
  - @开头：Vue CLI 默认会设置一个指向 `<projectRoot>/src` 的别名 @。(仅作用于模版中)
- public文件夹
```
// 若应用没有部署在域名的根部，那么需要为URL配置publicPath前缀
<link rel="icon" href="<%= BASE_URL %>favicon.ico">
```


### 6.CSS选项
- 自动化导入：在每个单文件组件和样式文件中导入全局样式文件
```
// vue.config.js：导入stylus类型文件例子
const path = require('path')

module.exports = {
  chainWebpack: config => {
    const types = ['vue-modules', 'vue', 'normal-modules', 'normal']
    types.forEach(type => addStyleResource(config.module.rule('stylus').oneOf(type)))
  },
}

function addStyleResource (rule) {
  rule.use('style-resource')
    .loader('style-resources-loader')
    .options({
      patterns: [
        // 引入的全局文件
        path.resolve(__dirname, './src/styles/imports.styl'),
      ],
    })
}
```
- PostCSS：默认开启autoprefixer，无需手动写css前缀
- CSS Modules：CSS模块，以模块变量的形式获取样式
- loader：传入全局变量
```
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      sass: {
        // 所以这里假设你有 `src/variables.sass` 这个文件
        additionalData: `@import "~@/variables.sass"`
      },
      // `scss` 语法会要求语句结尾必须有分号，`sass` 则要求必须没有分号
      scss: {
        additionalData: `@import "~@/variables.scss";`
      },
      // 给 less-loader 传递 Less.js 相关选项
      less:{
        // `primary` is global variables fields name
        globalVars: {
          primary: '#fff'
        }
      }
    }
  }
}
```



### 7.模式和环境变量
- 模式
  - development：`vue-cli-service serve`
  - test：`vue-cli-service test:unit`
  - production：`vue-cli-service build` 和 `vue-cli-service test:e2e`
  - `vue-cli-service build --mode development`：mode改写模式
- process.env.NODE_ENV：模式变量
  - 优先级：环境文件->模式
  - test：webpack 配置不会处理图片以及一些对单元测试非必需的其他资源
  - development： webpack 配置不会对资源进行 hash 也不会打出 vendor bundles
  - production：获取可用于部署的应用程序
- 环境文件：
```
// 优先级：命令内变量->特定模式文件->一般文件
// 文件后缀名样式
.env                # 在所有的环境中被载入
.env.local          # 在所有的环境中被载入，但会被 git 忽略
.env.[mode]         # 只在指定的模式中被载入
.env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略

// 环境文件-环境变量定义
FOO=bar
VUE_APP_NOT_SECRET_CODE=some_value
```
- 环境变量：只有`NODE_ENV`，`BASE_URL`和以`VUE_APP_`开头的变量将通过`webpack.DefinePlugin` 静态地嵌入到客户端侧的代码中


### 8.构建目标
- target选项指定不同的构建目标
- 应用模式：默认模式
  - index.html 会带有注入的资源和 resource hint
  - 第三方库会被分到一个独立包以便更好的缓存
  - 小于 4kb 的静态资源会被内联在 JavaScript 中
  - public 中的静态资源会被复制到输出目录中
- 库模式lib：
  - 项目的 publicPath 是根据主文件的加载路径动态设置
  - Vue是外置的，在命令中添加--inline-vue标志避免错误
  - 构建库输出文件：提供给打包器的CommonJS、给浏览器或UMD、压缩后的UMD、提取出的CSS
- Web Components模式：
  - Vue是外置的，既使导入包依旧假设有可用全局变量Vue
  - 构建一个组件，在脚本引入后作为元素使用
  - 注册多个组件的包，包内名称：名称前缀-文件名
  - 异步组件：`--target wc-async`，使用中按需获取组件实例，避免包过大



## 3.babel
- 作用：主要用于在当前和旧的浏览器或环境中，将ES5+代码转换为JS向后兼容版本的代码
- 配置文件：babael.config.js
- Polyfill：根据浏览器环境，为低版本浏览器添加的补丁包用来识别语言特性
1. 默认情况：`useBuiltIns:'usage'`传递给`@babel/preset-env`，根据源代码中语言特性自动检测需要的polyfill
2. 依赖基于一个目标环境不支持的 ES 版本撰写：依赖写入添加到 vue.config.js 中的 `transpileDependencies`选项，为该依赖同时开启语法转换和根据使用情况检测 polyfill
3. 依赖交付了 ES5 代码并显式地列出了需要的 polyfill：`@vue/babel-preset-app`的 polyfills 选项预包含所需要的 polyfill
4. 依赖交付 ES5 代码，但使用了 ES6+ 特性且没有显式地列出需要的 polyfill：`useBuiltIns: 'entry'` 然后在入口文件添加`import 'core-js/stable'; import 'regenerator-runtime/runtime'`
5. 当使用 Vue CLI 来构建一个库或是 Web Component 时：推荐给 `@vue/babel-preset-app` 传入`useBuiltIns: false`
```
// babel.config.js
module.exports = {
  presets: [
    ['@vue/app', {
      polyfills: [
        'es.promise',
        'es.symbol'
      ]
    }]
  ]
}
```



## 4.package.json
- `browserslist`：指定了项目的目标浏览器的范围。这个值会被 @babel/preset-env 和 Autoprefixer 用来确定需要转译的 JavaScript 特性和需要添加的 CSS 浏览器前缀


## 5.webpack
### 1.作用
- 定义：静态模块打包器，递归地构建一个依赖关系图，其中包含应用程序需要的每个模块，然后将所有这些模块打包成一个或多个 bundle
- 打包：可以把多个Javascript文件打包成一个文件，减少服务器压力和下载带宽
- 转换：把拓展语言(SCSS/ES6)转换成为普通的JavaScript，让浏览器顺利运行
- 责任：优化和提升性能


### 2.核心概念
- `entry`：定义入口的chunk，入口获取依赖模块
- `output`：定义输出的bundles
- `loader`：将非JS文件转化为能够处理的有效模块并进行打包处理
- `plugins`：打包优化、资源管理、注入环境变量，通过require引入，new创建
- `mode`：development、production、none，启用对应环境的内置优化
- `chunk`：模块的集合
  - 入口产生chunk：entry对象字段个数对应chunk个数
  - 异步加载产生chunk：webpack中异步加载的模块产生chunk
  - 代码分割产生chunk：通过配置进行代码分割产生chunk
- `bundle`：打包结果形成的代码块
- `devtool`：source-map工具
  - 开发：`cheap-module-eval-source-map`
  - 生产：`source-map`
- `manifest`：当编译器(compiler)开始执行、解析和映射应用程序时，它会保留所有模块的详细要点形成的数据结构
- `runtime`：在模块交互时，连接模块所需的加载和解析逻辑。包括浏览器中的已加载模块的连接，以及懒加载模块的执行逻辑。通过使用manifest中的数据，runtime将能够查询模块标识符，检索出背后对应的模块


### 3.input
- 单入口chunk：`entry: ['file_1.js', 'file_2.js'] / entry: 'file.js'`
- 多入口chunk：
  - `entry: { [entryChunkName]: string|Object }`
  - `optimization: { runtimeChunk: 'single' }`：避免问题
- 程序与第三方库分离：
```
/* 
* 作用：vendor中引入第三方库，打包成chunk由浏览器独立缓存减少加载时间
* 编译替换：应用程序中引用第三方库的部分编译替换为_ _webpack_require_ _()调用
* 升级方案：webpack4中使用optimization.splitChunks为第三方库创建一个单独的文件
*/
module.exports = {
  entry: {
    main: './src/app.js',
    vendor: './src/vendor.js',
  },
};
```
- 多页面应用：为不同页面创建html文档，复用入口之间的代码/模块
```
// 方法：optimization.splitChunks为页面间共享的应用程序代码创建bundle从而复用
module.exports = {
  entry: {
    pageOne: './src/pageOne/index.js',
    pageTwo: './src/pageTwo/index.js',
    pageThree: './src/pageThree/index.js',
  },
};
```


### 4.output
- 存在多个入口起点，但只指定一个输出配置
- 最低要求：配置成一个对象
  - filename：输出文件文件名
  - path：输出目录的绝对路径
- 多个入口起点：使用占位符确保每个文件具有唯一名称filename: '[name].js'
- 清空dist文件夹：`clean: true`（webpack4：CleanWebpackPlugin）
- 高级进阶：
  - 使用CDN：定义publicPath属性设置CDN地址
  - 资源hash：路径末尾写入[hash]


### 5.loader
```
// 执行顺序：当use配置项中使用多个loader，将按照从下到上的顺序对代码进行链式处理
module: {
    rules: [
        {
            test: /\.js$/,
            exclude: /node_modules/,
            include: [resolve('src')],
            use: 'Happypack/loader?id=js'
        },
        {
            test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
            loader: 'url-loader',
            options: {
                esModule: false,
                limit: 10000,
                name: utils.assetsPath('img/[name].[hash:7].[ext]')
            }
        }
    ]
}
```
- 使用loader的三种方式
  - 配置：webpack.config.js 文件中指定 loader
  - 内联：每个import语句中显示指定loader
  - CLI：在shell命令中指定它们
- loader特性
  - loader 将按照相反的顺序执行链式传递，最后一个loader返回webpack所预期的JavaScript
  - loader 可以是同步的，也可以是异步的。
  - loader 运行在 Node.js 中，并且能够执行任何可能的操作。
  - loader 接收查询参数。用于对 loader 传递配置。
  - loader 也能够使用 options 对象进行配置。
  - 除了使用 package.json 常见的 main 属性，还可以将普通的 npm 模块导出为 loader，做法是在 package.json 里定义一个 loader 字段。
  - 插件(plugin)可以为 loader 带来更多特性。
  - loader 能够产生额外的任意文件。
- 典型loader
  - 加载CSS：style-loader和css-loader，含有CSS字符串的< style >插入到html文件的< head >中
  - 加载图片：file-loader，图像被处理添加到output目录中，将原有相对路径替换成output目录中图像的最终路径
  - 加载字体：file-loader
  - 加载数据：JSON文件内置支持，CSV、TSV和XML需要使用 csv-loader 和 xml-loader


### 6.plugins
```
plugins: [
    new HtmlWebpackPlugin({
        excludeChunks: ['ui-preview'],
        template: './src/main/index.ejs',
        filename: './index.html',
        favicon: './public/favicon/favicon.ico',
        title: version.CONFIG_VERSION_NAME,
        inject: false,
        hash: true,
        mode: devMode //是否是调试模式 'development'
    })
] 
```
- 插件HtmlWebpackPlugin：生成新的index.html，内部引入全部生成的bundle文件，将新生成的index.html替换旧的index.html
- 插件CleanWebpackPlugin：构建前清空dist文件夹中内容，仅存在新构建的文件



### 7.modules
```
resolve: {
    extensions: ['.js', '.json', '.vue'], 
    modules: [
        resolve('src'),
        resolve('node_modules')
    ],
    alias: {
        '@': path.resolve(__dirname, "../src"),
    }
}
```
- 三种文件路径解析
  - 绝对路径：`import '/home/me/file'`不需要解析直接获取
  - 相对路径：`import '../src/file1'`根据上下文拼接解析目录
  - 模块路径：`import "module"`根据resolve配置项寻找上下文目录
- extensions：路径指向文件不具备扩展名时作为文件扩展名
- modules：定义模块路径解析中查找模块路径的指定目录
- alias：module设置别名，根据别名路径查找模块
- 路径指向文件夹，采取以下步骤找到正确文件：
  - 文件夹包含package.json：`resolve.mainFields`配置选项中指定的字段匹配package.json 中的第一个这样的字段确定文件路径。
  - 文件夹不包含package.json / package.json中的main字段没有返回有效路径： `resolve.mainFiles`配置选项中指定的文件名匹配 import/require 目录下的文件名。
  - 文件扩展名通过 `resolve.extensions` 选项采用类似的方法进行解析。
- Loader解析遵循与文件解析器指定的规则相同的规则。但是resolveLoader配置选项可以用来为Loader提供独立的解析规则




### 9.模块热替换
- 作用：HMR会在应用程序运行过程中替换、添加或删除模块，而无需重新加载整个页面
- 支持：不同文件的loader内置支持HMR
- 方式：保留在完全重新加载页面时丢失的应用程序状态，只更新变更内容
- 步骤：
  - 应用程序代码要求 HMR runtime 检查更新。
  - HMR runtime（异步）下载更新，然后通知应用程序代码。
  - 应用程序代码要求 HMR runtime 应用更新。
  - HMR runtime（同步）应用更新
- 编译器update：
  - 更新后的 manifest(JSON)
  - 一个或多个更新后的 chunk (JavaScript)
- HMR：
  - 可选功能：只影响包含HMR代码的模块，描述模块更新后行为
  - 更新冒泡：模块没有HMR处理函数情况下更新冒泡，一个处理函数能够对整个模块树进行更新
- 启用HMR：
  - 配置devServer：`+ hot: true`
  - 配置plugins：` + new webpack.HotModuleReplacementPlugin() `
  - 修改index.html：`+ if (module.hot) { module.hot.accept('', function() {} ) }`
- 通过Node.js API：
```
const webpackDevServer = require('webpack-dev-server');
const webpack = require('webpack');

const config = require('./webpack.config.js');
const options = {
  contentBase: './dist',
  hot: true,
  host: 'localhost'
};

webpackDevServer.addDevServerEntrypoints(config, options);
const compiler = webpack(config);
const server = new webpackDevServer(compiler, options);

server.listen(5000, 'localhost', () => {
  console.log('dev server listening on port 5000');
});
```



### 12.tree shaking
- 模块导出但未使用：
```
// 不打包square，可以判断未使用square
import { cube, square } from './math'   /   import * as Obj from './math'
console.log(cube(2))

export function square(x) {}
export function cube(x) {}

// 打包square，无法判断是否使用square
import obj from './utils'
console.log(obj.cube());

export default obj = { square, cube }
```
- 表达式级别的副作用
```
import { cube } from './math'
console.log(cube(2))


function square(x) {
  Math.stdSquare = 1
  return x * x
}
export function cube(x) {
  return x * x * x
}
export var stdSquare = square(1)  // 无法判断是否有副作用，不能移除stdSquare
export var stdSquare = /*#__PURE__*/ square(1)  // 表明无副作用，可以移除stdSquare
```
- 模块级别的副作用
```
// 导入polyfill但未使用，无法判断是否有副作用，仍需检查并打包
// ./index.js（入口文件）
import { cube } from './math';
console.log(cube(2));


// ./math.js
import { pow } from './utils'
export function square(x) {
  return pow(x)
}
export function cube(x) {
  return x * x * x
}


// ./utils.js
import './polyfill';
export function pow(x) {
  return x.pow()
}


// ./polyfill.js
Array.prototype.pow = function (x) {
    return x * x;
}
```
- sideEffects：package.json中用于声明代码中是否包含副作用
  - 副作用：在模块导入后即执行的语句
  - 值为true：默认值，表示webpack需检查是否存在副作用
  - 值为false：表示webpack无需检查是否存在副作用
  - 值为数组：表示webpack仅对数组内文件检查是否存在副作用



### 13.代码分离
- 代码分离：代码分离获取更小的bundle
  - 入口起点：使用 entry 配置手动地分离代码
  - 防止重复：
     - webpack4：使用 CommonsChunkPlugin 去重和分离 chunk:
     - webpack5：使用 Entry dependencies 或者 SplitChunksPlugin 去重和分离 chunk
  - 动态导入：通过模块的内联函数调用来分离代码
- 入口起点缺陷：
  - 重复模块：如果入口chunks之间包含重复模块，那么重复模块都会被引入到各个bundle
  - 动态拆分：不能将核心应用程序逻辑进行动态拆分代码
- 防止重复：
  - webpack4：CommonsChunkPlugin插件将公共的依赖模块生成新的共用bundle：`new webpack.optimize.CommonsChunkPlugin({name: 'common' // 指定公共 bundle 的名称。})`
  - webpack5：SplitChunksPlugin插件将公共的依赖模块生成新的共用bundle：`optimization:{ splitChunks: { chunks: 'all' } }`
- 动态导入：
  - 导入方式1：`import()`动态导入
  - 导入方式2：`require.ensure`导入
  - 定义模块格式：`chunkFilename: '[name].bundle.js'`：
  - 定义模块名称：`return import(/* webpackChunkName: "lodash" */ 'lodash').then()`



### 14.缓存
- 本质：客户端通过对比文件名是否变化命中缓存
  - 文件哈希值变化：保证修改文件被应用
  - 文件哈希值不变：保证未修改文件能够获取缓存减少网络请求
- webpack4：
```
entry: {
     main: './src/index.js',
     vendor: [
       'lodash'
    ]
}

plugins: [
    // vendor中module.id基于解析顺序进行增量导致变化，插件避免该变化导致hash值变化
    // 开发环境NamedModulesPlugin/生产环境HashedModuleIdsPlugin
    new webpack.HashedModuleIdsPlugin() 
    
    // 第三方库变化不频繁，提取成单独chunk确保hash值不变减少请求
    new webpack.optimize.CommonsChunkPlugin({  // 抽取第三方库生成vendor.[hash]
       name: 'vendor'
    }),
    
    // 模板易受影响，提取模板成单独chunk确保hash值不变减少请求
    new webpack.optimize.CommonsChunkPlugin({  // 抽取模板生成mainfest.[hash]
       name: 'manifest'
    })
]

output: {
    filename: '[name].[chunkhash].js', // 文件hash
    path: path.resolve(__dirname, 'dist')
}
```
- webpack5:
```
optimization: {
     moduleIds: 'deterministic',  // 对应模块标识符处理
     runtimeChunk: 'single',  // 对应模板处理
     splitChunks: {  // 对应第三方库处理
       cacheGroups: {
         vendor: {
           test: /[\\/]node_modules[\\/]/,
           name: 'vendors',
           chunks: 'all',
         },
       },
     },
},
```



### 15.library
- 打包library，实现目标：
- 不打包 lodash，而是使用 externals 来 require 用户加载好的 lodash。
- 设置 library 的名称为 webpack-numbers.
- 将 library 暴露为一个名为 webpackNumbers的变量。
- 能够访问其他 Node.js 中的 library。
- 访问
```
var path = require('path');

module.exports = {
entry: './src/index.js',
output: {
  path: path.resolve(__dirname, 'dist'),
  filename: 'webpack-numbers.js',
  library: 'webpackNumbers'  // 可作为全局变量引入
  libraryTarget: 'umd'       // 控制暴露的方式
},
externals: {  // 配置外部依赖，不打包在library中，需在用户环境中存在且可用
  lodash: {   // 多个文件时无法通过目录方式访问，需逐个写明或者正则表达式
    commonjs: 'lodash',
    commonjs2: 'lodash',
    amd: 'lodash',
    root: '_'
  }
}
};
```
- libraryTarget属性：控制library以不同方式暴露，若无则默认为var
- 变量：作为一个全局变量，通过 script 标签来访问（libraryTarget:'var'）。
- this：通过 this 对象访问（libraryTarget:'this'）。
- window：通过 window 对象访问，在浏览器中（libraryTarget:'window'）。
- UMD：在 AMD 或 CommonJS 的 require 之后可访问（libraryTarget:'umd'）。
- 设置 package.json 中的 main 字段，添加生成 bundle 的文件路径


### 16.shimming
- 解决问题：某些三方库可能引入一些全局变量或者创建一些需要被导出的全局变量
- 全局变量：使用到对应变量的模块中引入对应的第三方库
```
plugins: [
 new webpack.ProvidePlugin({
   _: 'lodash' // 全局导入
   join: ['lodash', 'join']  // 单个变量导入
 })
]
```

- 细粒度shimming：覆写模块this指向
```
module: {
 rules: [
   {
     test: require.resolve('index.js'),
     use: 'imports-loader?this=>window'
   }
 ]
},
```
- 全局exports：全局变量作为模块变量引入
```
module: {
rules: [
   {
     test: require.resolve('globals.js'),
     use: 'exports-loader?file,parse=helpers.parse'
   }
]
},
```
- 加载polyfills：index.html中根据需要动态引入生成文件获取浏览器拓展功能
```
entry: {
  polyfills: './src/polyfills.js',
  index: './src/index.js'
},
output: {
  filename: '[name].bundle.js',
  path: path.resolve(__dirname, 'dist')
},
```



### 17.PWA
- 定义：离线时应用程序依旧能运行
- 引入插件：build后生成额外的sw.js文件
```
plugins: [
new WorkboxPlugin.GenerateSW({
 // 这些选项帮助 ServiceWorkers 快速启用
 // 不允许遗留任何“旧的” ServiceWorkers
 clientsClaim: true,
 skipWaiting: true
})
],
```
- 注册Service Worker：注册sw.js实现离线服务
```
if ('serviceWorker' in navigator) {
window.addEventListener('load', () => {
    navigator.serviceWorker.register('/sw.js').then(registration => {
        console.log('SW registered: ', registration);
    }).catch(registrationError => {
        console.log('SW registration failed: ', registrationError);
    });
});
}
```


### 18.性能优化
- `loader`：使用include、exclude应用于最少数的必要模块
- `resolve`：减少resolve中modules、extensions、mainFiles、descriptionFiles 中类目的数量，减少文件系统调用的次数
- DLL：DllPlugin 将更改不频繁的代码进行单独编译
- worker pool：thread-loader 可以将非常消耗资源的 loaders 转存到 worker pool 中
- 持久化缓存：package.json 的 "postinstall" 清除缓存目录
  - webpack4：在性能消耗较大的loader前加入cache-loader
  - webpack5：`cache: true`开启缓存
- 开发环境优化
  - 内存编译
     - webpack-dev-server
     - webpack-hot-middleware
     - webpack-dev-middleware
  - `devtool: cheap-module-eval-source-map`
  - 避免生产环境下的工具
     - TerserPlugin
     - [fullhash]/[chunkhash]/[contenthash]
     - AggressiveSplittingPlugin
     - AggressiveMergingPlugin
     - ModuleConcatenationPlugin
  - 避免额外的优化步骤
     - `removeAvailableModules: false`：关闭优化功能（作用）
     - `removeEmptyChunks: false`：关闭优化功能（作用）
     - `splitChunks: false`：关闭优化功能（作用）
  - 输出结果不带路径信息：`output: { pathinfo: false }`
  - TypeScript loader
     - ts-loader关闭类型检查：`options: { transpileOnly: true }`
     - 插件检查移至单独进程开启类型检查：`ForkTsCheckerWebpackPlugin`
- 生产环境：多进程编译
  - parallel-webpack: 对于单entry无收益，使用于多页面应用
  - HappyPack：不再维护，稳定性与扩展性欠佳
  - thread-loader：webpack官方loader，添加到所有loader前面，但不支持所有loader





### 19.webpack项目结构
- mode：选择环境
  - production
  - development
  - none
- entry:唯一入口文件，对应main.js
- output:输出文件对应的属性
- path：打包文件存放的地方
- filename：打包输出文件的文件名，对应bundle.js
- publicPath：生产模式下所有资源指定一个基础路径
- library：导出库的名称
- libraryTarget：导出库的类型
- devtool：source-map提供对应编译文件和源文件的方法，使得代码可读性更高，更容易调试
- source-map：从一个单独文件中产生完整且功能完全的文件，减慢打包速度
- cheap-moudle-source-map：从一个单独文件中产生不带列映射的map，只对应行不对应列，调试不便
- eval-source-map：使用eval打包源文件模块，不影响速度下生成source-map，适合开发不适合生产
- cheap-moudle-eval-source-map：最快速度生产source-map，具有上面两种模式的缺点
- devServer：需要单独安装才可使用，搭建本地服务器
- proxy：访问路径
- contentBase：设置本地服务器所提供的目录
- port：端口，默认为8080
- inline：设置为true，源文件改变时会自动刷新页面
- historyApiFallback：设置为true，所有的跳转将指向index.html
- hot：设置为true，前端代码改变时会进行替换而并非整个刷新
- module:
- rules:调用外部的脚本或工具，数据类型为数组，可实现对不同格式的文件的处理
 - test：处理文件的正则表达式，比如/\.css$/
 - loader：调用的外部脚本或工具名称(npm install)style-loader、css-loader
 - use：数组类型可引入多个loader方法
     - loader：调用的外部脚本或工具名称
     - options:
         - modules:true(css-loader中使用表示启用css modules避免全局污染)
         - localIdentName：'[name]__local]--[hash:base64:5]'指定类名格式内部调用
 - include：必须匹配的文件夹
 - exclude：必不匹配的文件夹（优先）
 - query：提供额外的设置选项(可选)
- plugins：外部引入插件函数对象，再通过new的方式进行创建插件实例
 - new 插件名({})可根据插件不同写入应有的对象
- resolve：解析模块请求的选项
 - modules：用于查找模块的目录
 - extention：使用的扩展名
 - alias：模块别名列表，模块访问路径别名
- target：包应该运行的环境，默认为'web'
- performance：提示webpack资源数量限制
- context：webpack的主目录
- externals：不要打包这些模块，而是运行时从环境中请求他们
- stats：控制要显示的bundle信息



### 20.IDEwebpack
#### 疑问
- thread-loader与cache-loader的先后顺序
- thread-loader与cache-loader选择的loader是否正确
- DllReferencePlugin：注释之后无法获取依赖关系
- CleanWebpackPlugin：仅在dll使用CleanWebpackPlugin，生产环境中不使用？


#### 配置措施
- devtool：开发环境更换为cheap-module-eval-source-map，首次编译提升10s（65 -> 50）
  - cheap：忽略列信息，debug时不需要列信息
  - module：包含了loader模块之间的sourcemap，可以看到非js的原始代码
  - eval：不需要生成模块的sourcemap，但会导致包的体积变大
- `noParse: /^(vue|vue-router|vuex|vuex-router-sync|jquery|lodash)$/`
- thread-loader与cache-loader：vue、js、ts使用缓存以及多线程
- Happypack：转为thread-loader


#### 升级措施
- 升级webpack：`npm install webpack@latest`
- 升级webpack-cli：`npm install webpack-cli@latest`
- 升级mini-css-extract-plugin：`npm install mini-css-extract-plugin@latest`
- 升级html-webpack-plugin：`npm install html-webpack-plugin@latest`
- 严格devtool：`^(inline-|hidden-|eval-)?(nosources-)?(cheap-(module-)?)?source-map$`
- devServer（暂定）
  - 升级webpack-dev-server：`npm install webpack-dev-server@latest`
  - setup => setupMiddlewares
  - progress => client.progress
  - publicPath => devMiddleware.publicPath
  - contentBase => static[0].directory
  - 移除'inline'
  - 移除'hot'
- 移除 node.js polyfill
```
resolve: {
    fallback:  {
        "path": require.resolve("path-browserify"),
        "crypto": require.resolve("crypto-browserify"),
    }
}
```
- IgnorePlugin语法修改
```
new webpack.IgnorePlugin({
    resourceRegExp: /^\.\/locale$/,
    contextRegExp: /moment/,
})
```
- require.context：修改正则表达式过滤绝对路径
- 持久化缓存：
```
cache: {
    type: 'filesystem',
}
```
- dll
  - 保留情况：相比起webpack4会消耗更多时间编译
  - 删除dll：速度加快但不清楚
- webpack5打包：包的数量增多，但整体大小几乎无差别，不清楚影响
- 问题：
  - dll：dll有进行打包却没有将依赖引入，如何发挥作用？
  - dll不打包编译速度更快
  - webpack5打包后包的数量会增多，不确定影响
  - dev-server升级与否？
  - devtool：开发环境更换为cheap-module-eval-source-map
- 客户端升级：
  - ansi-html的module nofound
  



### 21.引擎webpack
- 升级vue-cli-service：`vue upgrade @vue/cli-service --next`（底层webpack5）
- 升级webpack：`npm install webpack@latest`（webpack版本与vue-cli版本一致：`Progress Plugin Invalid Options`）
- 'prefetch-index'：切换语法，否则报错
- 'css.requireModuleExtension'：不支持，直接取消使用默认值即可
- 'devServer'：配置变更，弹出无法访问网址的情况
  - 配置本地ip：`host: 'local-ip'`
  - 热更新：`webSocketURL: { hostname: '0.0.0.0', pathname: '/ws', port: 0 }`
- 持久化缓存：
```
cache: {
    type: 'filesystem',
}
```
- 移除 node.js polyfill
```
fallback:  {
    "path": require.resolve("path-browserify"),
    "crypto": require.resolve("crypto-browserify"),
    "stream": require.resolve("stream-browserify")
},
```
- IgnorePlugin语法修改
```
new webpack.IgnorePlugin({
    resourceRegExp: /^\.\/locale$/,
    contextRegExp: /moment/,
})
```
