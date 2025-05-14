# Eslint

标签（空格分隔）： 技术栈

---

## 1.vue-cli构建
- 配置位置：cue-cli生成项目时，可能会将`eslintConfig`配置写入`package.json`文件当中
- 单独文件：在项目根目录创建 `.eslintrc.js` 文件，从 `package.json` 中复制 `eslintConfig` 内容到新文件，删除 `package.json` 中的 `eslintConfig` 字段


## 2.配置文件
- 文件类型
  - `JavaScript`：使用 `.eslintrc.js` 并导出包括配置的对象
  - `JavaScript (ESM)`：当在 JavaScript 包中运行 ESLint 时，且其 package.json 中指定 "type":"module" 时，使用 `.eslintrc.cjs`。请注意 ESLint 目前不支持 ESM 配置
  - `YAML`：使用 `.eslintrc.yaml` 或 `.eslintrc.yml` 来定义配置结构
  - `JSON`：使用 `.eslintrc.json` 来定义配置结构。ESLint JSON 文件中也可以使用 JavaScript 风格注释
  - `package.json`：在 `package.json` 文件中创建 `eslintConfig` 属性并在那里定义你的配置。
- 文件优先级
  - .eslintrc.js
  - .eslintrc.cjs
  - .eslintrc.yaml
  - .eslintrc.yml
  - .eslintrc.json
  - package.json
- 配置层级：
  - 优先级：优先应用同级目录的`.eslintrc` 文件，沿目录结构向上搜索，合并沿途发现的任何 `.eslintrc` 文件，直到到达 `root: true` 的 `.eslintrc` 文件或根目录
  - 父子目录合并：根目录下有`package.json`文件，而其中又有`eslintConfig`字段，它所描述的配置将适用于它下面的所有子目录。若与子目录中的`.eslintrc`文件存在配置冲突时，子目录内优先
  - 冲突优先：在同一目录下有`.eslintrc`和`package.json`文件，`.eslintrc`将优先使用，`package.json `文件将不被使用


## 4.扩展配置
- 概念：配置文件使用扩展后，就可以继承另一个配置文件的所有特征（包括规则、插件和语言选项）并修改所有选项
- 配置类型
  - 基础配置：被扩展的配置（使用的extends）
  - 派生配置：扩展基础配置的配置（配置文件中自定义内容）
  - 结果的实际配置：将派生配置合并到基础配置的结果（最后实际配置效果）
- 派生方式
  - 改变一个继承的规则的严重程度，而不改变其选项。
     - 基本配置：`"eqeqeq": ["error", "allow-null"]`
     - 派生配置：`"eqeqeq": "warn"`
     - 产生的实际配置：`"eqeqeq": ["warn", "allow-null"]`
  - 覆盖基础配置中的规则选项：
     - 基本配置：`"quotes": ["error", "single", "avoid-escape"]`
     - 派生配置：`"quotes": ["error", "single"]`
     - 产生的实际配置：`"quotes": ["error", "single"]`
  - 覆盖基础配置中作为对象给出的规则的选项：
     - 基本配置：`"max-lines": ["error", { "max": 200, "skipBlankLines": true, "skipComments": true }]`
     - 派生配置：`"max-lines": ["error", { "max": 100 }]`
     - 产生的实际配置：`"max-lines": ["error", { "max": 100 }]` 其中 `skipBlankLines` 和 `skipComments` 默认为 `false`
     

## 5.extends
- 概念：扩展的规则，以此作为基础配置可以进行自定义继承
- 使用推荐的核心规则：`"extends": "eslint:recommended"`
- 使用当前版本所有核心规则：`"extends": "eslint:all"`
- 使用插件配置命名规则
  - 示例：`"plugin:react/recommended"`
  - 组成规则：
     - `plugin:`
     - 包名（可以省略其前缀，如 `react` 是 `eslint-plugin-react` 的缩写）
     - /
     - 配置名称（如 `recommended`）
- 使用现有配置文件：`"extends": "./node_modules/coding-standard/eslintDefaults.js"`


## 6.env
- 概念：环境会提供预设的全局变量
- 常见类型：
  - browser：浏览器全局变量
  - node：Node.js 的全局变量和 Node.js 的范围
  - es2021：添加所有 ECMAScript 2021 的全局变量，并自动将解析器选项 ecmaVersion 设置为 12
- 使用配置注释：`/* eslint-env node, mocha */` 启用了 Node.js 和 Mocha 环境


## 7.globals
- 概念：指定全局变量
- 使用配置注释：`/* global var1, var2 */` 指定全局变量var1、var2
- 全局变量配置：`"globals": { "Promise": "off" }`不允许使用Promise


## 8.parserOptions
- 概念：指定支持的语言类型
- 支持对应JS版本：`"parserOptions": { "ecmaVersion": 6 }`


## 9.rules
- 规则严重性
  - "off" 或 0 - 关闭规则
  - "warn" 或 1 - 启用并视作警告（不影响退出）
  - "error" 或 2 - 启用并视作错误（触发时退出代码为 1）
- 使用配置注释：`/* eslint eqeqeq: "off", curly: "error" */`
- 使用配置文件

```javascript
{
    "rules": {
        "eqeqeq": "off",
        "curly": "error",
        "quotes": ["error", "double"],
        "plugin1/rule1": "error" // 插件规则
    }
}
```
- 使用注释禁用规则：`/* eslint-disable */`
- 使用文件禁用规则：配置文件中禁用一组文件的规则

```javascript
{
  "rules": {...},
  "overrides": [
    {
      "files": ["*-test.js","*.spec.js"],
      "rules": {
        "no-unused-expressions": "off"
      }
    }
  ]
}
```

## 10.plugins
- 配置插件

```javascript
// 可以省略插件名称中的 eslint-plugin- 前缀
{
    "plugins": [
        "plugin1",
        "eslint-plugin-plugin2"
    ]
}
```

## 11.忽略文件
- 概念：ESLint 在检查时忽略明确的文件或目录
- 隐含的规则：忽略 node_modules，忽略点文件（除了 .eslintrc.*），以及点文件夹和它们的内容
- 配置文件中ignorePatterns选项
  - 举例：`"ignorePatterns": ["temp.js", "**/vendor/*.js"]`
  - 字符串以 / 开头，该模式是相对于配置文件的当前目录而言的，不会渗透入子目录
- `.eslintignore` 文件：类似`.gitignore`配置方式（最优先）
- 替代文件：命令行执行指定其他的文件用作`.eslintignore`文件
- package.json 中的 eslintIgnore：当前面都没有时则在该配置项获取需要忽略的文件