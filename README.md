# webpack-vuejs-template

此程式庫是演練使用**webpack**起始**Vue.js**專案。

## Editor

### Visual Studio Code

#### Extensions

* [EditorConfig for VS Code](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig): EditorConfig for Visual Studio Code
* [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur): Vue tooling for VS Code
* [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint): Integrates ESLint into VS Code.

## Package

* [Vue.js](https://vuejs.org/)
* [webpack](https://webpack.js.org/)
* []()
* []()
* []()
* []()
* []()

## Steps

### Add *.editorconfig* to **root**

```
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
```

### Add *.gitignore* to **root**

```
node_modules
```

### Init Project

```bash
npm init
```

### Install **Vue.js** via **npm**

```bash
npm install --save vue
```

### Hello Vue

* Create *index.html* in **root**

```html
<!DOCTYPE html>
<html>

<head>
    <script src="node_modules/vue/dist/vue.js"></script>

    <title>Document</title>
</head>

<body>
    <div id="app">
        {{ message }}
    </div>
</body>

<script>
    var app = new Vue({
        el: '#app',
        data: {
            message: 'Hello Vue!'
        }
    })
</script>

</html>
```

* 在瀏覽器開啟`index.html`，會出現`Hello Vue!`的字串

### 用webpack引入Vue.js

* Install webpack via npm

```bash
npm install --save-dev webpack
```

* 在root新增`webpack.config.js`並且設置

```js
'use strict'

var path = require('path');

module.exports = {
  entry: './src/main.js', // 要打包的目標檔案
  output: { // 要輸出的檔案資訊
    path: path.resolve(__dirname, './dist'),
    publicPath: '/dist/',
    filename: 'build.js'
  },
  resolve: {
    alias: {
      'vue$': 'vue/dist/vue.esm.js' // 要使用Compiler的話要加這個設置
    }
  }
}
```

是否需要使用Compiler可以參考[官網](https://vuejs.org/v2/guide/installation.html#Runtime-Compiler-vs-Runtime-only)

* 修改index.html

```html
<!DOCTYPE html>
<html>

<head>
  <title>Document</title>
</head>

<body>
  <div id="app">
    {{ message }}
  </div>
  <script src="/dist/build.js"></script>
</body>

</html>
```

* 在root新增src資料夾

* 在src資料夾下新增main.js

```js
import Vue from 'vue'

var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

* 在npm的package.json中的scripts新增build指令

```json
"scripts": {
  ...
  "build": "webpack"
},
```

* 執行建置

```bash
npm run build
```

* 在瀏覽器開啟`index.html`，會出現`Hello Vue!`的字串

### 改成Single File Components

* Install **Vue Loader**

```bash
npm install --save-dev vue-loader vue-template-compiler
```

* 在webpack.config.js中加入vue-loader

```js
...
module.exports = {
  ...
  module: {
    rules: [
      { // 指定.vue檔案使用vue-loader
        test: /\.vue$/,
        loader: 'vue-loader'
      }
    ]
  },
  ...
}
```

* 修改index.html

```html
<!DOCTYPE html>
<html>

<head>
  <title>Document</title>
</head>

<body>
  <div id="app">
  </div>
  <script src="/dist/build.js"></script>
</body>

</html>
```

* 在src中新增App.vue

```vue
<template>
  <div>{{ message }}</div>
</template>

<script>
export default {
  data(){
    return {
      message: 'Hello Vue!'
    }
  }
}
</script>
```

* 修改src/main.js

```js
import Vue from 'vue'
import App from './App.vue'

var app = new Vue({
  el: '#app',
  render: h => h(App) // 用App.vue渲染#app
})
```

* 執行建置

```bash
npm run build
```

* 在瀏覽器開啟`index.html`，會出現`Hello Vue!`的字串

### webpack引入Babel

* Install Babel

```bash
npm install --save-dev babel-core babel-loader babel-preset-env
```

* 在root新增.babelrc

```js
{
  "presets": ["env"]
}
```

* 建置webpack

```bash
npm run build
```

### 測試Babel是否運作

* 在App.vue的script加上`class foo{}`
* 執行webpack
* 點開dist/build.js
* 搜尋foo
* 失敗依然是`class foo {}`
* 成功: 會轉為下面的程式碼

```js
var foo = function foo() {
  _classCallCheck(this, foo);
};
```

### 加入ESLint

* 安裝ESLint

```bash
npm install --save-dev eslint
```

* 使用ESLint提供的互動介面完成設置

```bash
npx eslint --init
```

此庫使用[airbnb/javascript](https://github.com/airbnb/javascript) Code Style。

* 輸入指令驗證ESLint

```bash
npx eslint src/main.js
```

* 應該輸出

```bash
✖ 6 problems (6 errors, 0 warnings)
  5 errors, 0 warnings potentially fixable with the `--fix` option.
```

### ESLint加入eslint-plugin-vue

* 安裝eslint-plugin-vue

```bash
npm install --save-dev eslint-plugin-vue
```

* 修改.eslintrc.js

```js
module.exports = {
    "extends": [
      "airbnb-base",
      "plugin:vue/recommended"  // 增加vue code style
    ]
};
```

* 輸入指令驗證Vue Code Style有被ESLint吃到

```bash
npx eslint src/App.vue
```

* 應該輸出

```bash
✖ 6 problems (6 errors, 0 warnings)
  5 errors, 0 warnings potentially fixable with the `--fix` option.
```

### ESLint Visual Studio Code插件在.vue沒有作用

調整ESLint插件的config

```js
"eslint.validate": [
    "javascript",
    "javascriptreact",
    "vue" // 讓eslint可以吃到vue檔
]
```

### webpack在build之前執行eslint檢查

* 安裝eslint-loader

```bash
npm install --save-dev eslint-loader
```

* 在webpack.config.js中加入下面的設定

```js
...
module.exports = {
  ...
  module: {
    rules: [
      {
        test: /\.(js|vue)$/,
        loader: 'eslint-loader',
        enforce: 'pre'
      },
      ...
    ]
  },
  ...
}
```

### 加上Flow(跟SFC無法整合，先忽略)

* 安裝

```bash
npm install --save-dev flow-bin
```

* 初始

```bash
npx flow init
```

這指令會幫我們建立一個.flowconfig

* 將node_modules排除，修改Flow

```
[ignore]
.*/node_modules/.*
...
```

* 使用Flow

```bash
npx flow
```

* 如果指令沒有作用時，到工作管理員裡把flow的程序結束再重跑

## 設定Vetur

* 在root新增jsconfig.json

```js
{
  "include": [
    "./src/**/*"
  ]
}
```

* lint

將剛剛的eslint.validate設定改為

```js
"eslint.validate": [
  "javascript",
  "javascriptreact",
  {
    "language": "vue",
    "autoFix": true
  },
]
```

* lint template

* vetur.validation.template: false

* 將"eslint.autoFixOnSave": true可以自動fix style問題
