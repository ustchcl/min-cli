# Min Cli

> 令小程序组件的开发和使用变得愉悦


## ○ 最佳实践

[MinUI](https://github.com/meili/minui)，是基于 Min 平台产出的一套 UI 组件库，同时也是蘑菇街小程序在应用的 UI 组件库。通过下面的小程序二维码，可以在手机中体验 MinUI（微信基础库版本 1.6.3 以上支持）：

<img width="200" src="https://s10.mogucdn.com/mlcdn/c45406/171101_6k682blbc5j30lj08galbk1k0g95h_1280x1280.jpg" style="display:block;margin:0 auto;" />

## ○ 环境安装

``` bash
$ npm install -g @mindev/min-cli
```

## ○ 组件开发

### ☞ 初始化组件开发环境

``` bash
$ cd ~/your-custom-project
$ min init

# 创建完毕后，在 "微信开发者工具" 中新建一个小程序项目，项目目录指向新建工程里的 dist/ 文件夹。如此，组件就能在开发者工具中进行预览了。
```

### ☞ 新建组件

``` bash
$ min new *name
```

### ☞ 调试组件

通过设置一个组件名 *name，来开启组件的调试。任何组件的改动，都会触发实时编译，并在 "微信开发者" 工具中显示改动后的效果。

``` bash
$ min dev *name
```

``` bash
# 支持英文逗号分隔，来同时调试多个组件
$ min dev *name1 *name2

# 无组件名，则监听整个工程环境的改动
$ min dev
```

### ☞ 发布组件

发布后的组件即为一个随时可用的 npm 包：

``` bash
# 例如发布 loading 组件
$ cd ~/your-weapp-project/packages/wxc-loading
$ npm publish

# 发布scope的npm包到外网，它是默认被限制的，发布到外网要带上`--access=public`
$ npm publish --access=public
```

## ○ 组件应用

### ☞ 安装组件

在小程序项目中安装一个组件，这里用 [MinUI](https://github.com/meili/minui) 的 loading 和 toast 组件举例：

``` bash
$ cd ~/your-weapp-project
$ min install @minui/wxc-loading
```

批量安装：

``` bash
$ min install @minui/wxc-loading @minui/wxc-tast
```

### ☞ 使用组件

按照微信小程序组件化文档所示，引入组件并使用即可。

使用已注册的自定义组件前，首先要在页面的 `json` 文件中进行引用声明。此时需要提供每个自定义组件的标签名和对应的自定义组件文件路径：

```json
{
  "usingComponents": {
    "component-tag-name": "path/to/the/custom/component"
  }
}
```

这样，在页面的 `wxml` 中就可以像使用内置组件一样使用自定义组件。节点名即自定义组件的标签名，节点属性即传递给组件的属性值。

**代码示例：**

```html
<view>
  <!-- 以下是对一个自定义组件的引用 -->
  <component-tag-name inner-text="Some text"></component-tag-name>
</view>
```

自定义组件的 `wxml` 节点结构在与数据结合之后，将被插入到引用位置内。

### ☞ 更新组件

``` bash
# 全部更新
$ min update

# 选择性更新
$ min update @minui/wxc-loading @minui/wxc-tast
```

## ○ 工程说明（可选阅读）

### ☞ 关于单文件

Min 采用单文件的方式来开发组件，后缀为 `.wxc`，即 weixin component 的简写。

采用单文件的方式开发一个组件，是因为通过 Min 提供的插件化能力，在编译环节可以做一些渐进增强的事情。

同理，Min 还提供了 `.wxp` 和 `.wxa`，来为 page 和 app 的开发提供一些额外的赋能。如果您已经在本地尝试基于 Min 的开发，就会发现本地的组件开发环境这个小程序，就是基于 .wxp 和 .wxa 实现的。

### ☞ 关于结构

#### 组件工程结构

```
- /
    - dist // 打包目录，在微信开发者工具中添加这个目录来运行项目
    - src
        - packages // 组件源码
            - loading // loading 组件源码
                - src
                    - index.wxc // 组件单文件
                - .npmignore
                - package.json
                - README.md
                - LICENSE
            - ...
        - pages // 组件 Demo 示例
            - loading // loading 组件示例
                - demos
                    - demo-default.wxc // 一个 demo 示例，在 index.wxp 中引入
                    - ...
                - docs // 组件文档
                - index.wxp // demo 页
            - ...
```

#### *.wxc 组件结构

``` html
<template>
  <view class="loading">
    <view>Loading Component</view>
  </view>
</template>

<script>
  export default {
    config: { ... },
    properties: {
      isShow: {
        type: Boolean,
        value: false
      }
    },
    data: { }
  }
</script>

<style>
  .loading { }
</style>
```

#### *.wxp 页面结构

``` html
<template>
  <view class="loading">
    <wxc-loading is-show="{{true}}"></wxc-loading>
  </view>
</template>

<script>
  export default {
    config: {
      navigationBarTitleText: 'Loading',
      usingComponents: {
        'wxc-loading': '@minui/wxc-loading'
      }
    },
    data: {}
  }
</script>

<style>
  .loading { }
</style>
```

#### *.wxa 文件结构
``` html
<!-- *.wxa 单文件里的 template 模块定义为全局公共模板概念，编译后会应用到所有的 .wxp 页面 -->
<template>
  <view>
    <wxc-head></wxc-head>

    <!-- wxp template -->
    <page></page>

    <wxc-foot></wxc-foot>
  </view>
</template>

<script>
  export default {
    config: {
      usingComponents: {
        'wxc-head': 'components/head',
        'wxc-foot': 'components/foot'
      },
      pages: [],
      window: { ... },
      ...
    },
    globalData: { },
    onLaunch: function () { },
    onShow: function () { },
    onHide: function () { }
  }
</script>

<style>
</style>
```

### ☞ 关于编译

#### 编译策略

- 从 `src` 源码目录下的文件编译到 `dist` 目录；
- 非单文件的文件在编译期间会自动从 `src` 关联到 `dist` 目录下；
- 单文件 `.wxc` 和 `.wxp` 格式，编译成小程序原生的 `.wxml`、`.wxss`、`.js`、`.json` 多文件

``` html
<template>
<!-- template模块编译后，提取到 *.wxml 文件里 -->
</template>

<script>
// script模块编译后，将逻辑部分的代码提取到 *.js，将配置部分的代码 *.json 文件里
</script>

<style>
/* style模块编译后，提取到 *.wxss 文件里 */
</style>
```

#### 脚本的编译

script 模块导出一个简单的对象，不同扩展的单文件在编译阶段会将在 `export default` 导出对象的外部嵌套 `Component` or `Page` or `App` 构造器

单文件模块源码结构

``` html
<script>
    export default {...}
</script>
```

*.wxc 文件的`script`模块编译后生成 *.js 文件结构

``` js
export default Component({...})
```

*.wxp 文件的`script`模块编译后生成 *.js 文件结构

``` js
export default Page({...})
```

*.wxa 文件的`script`模块编译后生成 *.js 文件结构

``` js
export default App({...})
```

#### 样式的编译

+ 支持 `px单位` 转换，1px 自动转成 1rpx
+ 支持 `rem单位` 转换，1rem 自动转成 100rpx
+ 支持 `bem` 命名方法书写，分别@b、@e、@m定义，前提在style标签上增加lang="pcss" 就能使用此能力了。
+ 支持 @import 文件动态编译，非 `*.wxss` 后缀的文件经过预编译插件生成`.wxss`文件
+ 支持 less 和 postcss 预编译插件

``` html
// 源码
<style>
@import './a.wxss';
@import './b.pcss';
@import './c.less';

.search {
    border: 1px solid #ddd;
    width: 1rem;
}
@b search {
    padding: 0;

    @e submit{
        padding: 1rem;

        @m button{
            border: 1px solid #ccc;
        }
    }
}
</style>

// 编译后
<style>
@import './a.wxss';
@import './b.wxss';
@import './c.wxss';

.search {
    border: 1rpx solid #ddd;
    width: 100rpx;
}
search {
    padding: 0;
}
.search__submit {
    padding: 100rpx;
}
.search__submit--button {
    border: 1rpx solid #ccc;
}
</style>
```

#### 配置的编译

小程序原生的 `*.json` 配置项，放在单文件 `<script></script>` 模块中的 `config` 字段里：

``` html
<!--.wxc 组件文件的 script 模块 -->
<script>
  export default {
    // 组件的配置
    config: {
      component: true,
      usingComponents: {}
    }
  }
</script>
```

``` html
<!--.wxp 页面文件的 script 模块 -->
<script>
export default {
  // 页面的配置
  config: {
    navigationBarTitleText: 'Title',
    usingComponents: {
      'example': '@minui/wxc-example',
      'example-demo': '@minui/wxc-example-demo',
      'wxc-loading': '@minui/wxc-loading'
    }
  }
}
</script>
```

``` html
<!--.wxa 应用文件的 script 模块-->
<script>
  export default {
    // 应用的配置
    config: {
      pages: [],
      window: {
        backgroundTextStyle: 'dark',
        backgroundColor: '#efefef',
        navigationBarBackgroundColor: '#ffffff',
        navigationBarTitleText: 'MinUI',
        navigationBarTextStyle: 'black'
      },
      networkTimeout: {
        request: 10000
      }
    }
  }
</script>
```

### ☞ 关于编辑器的支持

#### VS Code 代码高亮

+ 在 Code 里先安装 Vue 的语法高亮插件 Vetur
+ 打开任意 `.wxc` 或 `.wxp` 文件
+ 点击右下角的选择语言模式，默认为纯文本
+ 在弹出的窗口中选择 `.wxc` 或 `.wxp` 的配置文件关联...
+ 在选择要与 `.wxc` 或 `.wxp` 关联的语言模式 中选择 Vue

#### Sublime 代码高亮

+ 文件后缀为 `.wxc` 或 `.wxp`，可共用vue高亮，但需要手动安装。
+ 打开 Sublime > Preferences > Browse Packages.. 进入用户包文件夹。
+ 在此文件夹下打开 cmd，运行 `git clone git@github.com:vuejs/vue-syntax-highlight.git`，无 git 用户可以直接下载 [zip包](https://github.com/vuejs/vue-syntax-highlight/archive/master.zip)，解压至当前文件夹。
+ 关闭 `.wxc` 或 `.wxp` 文件重新打开即可高亮。

#### WebStorm 代码高亮

+ 打开 Preferences，搜索 Plugins，搜索 Vue.js 插件并安装。
+ 打开 Preferences，搜索 File Types，找到 Vue.js Template，在 Registered Patterns 添加 `*.wxc` 或 `*.wxp`，即可高亮。

#### Atom 代码高亮

+ 在 Atom 里先安装 vue 的语法高亮 - language-vue，如装过就忽略此步。
+ 打开 Atom > Config 菜单。在 core 键下添加：

``` js
customFileTypes:
  "text.html.vue": [
    "wxc",
    "wxp"
  ]
```

#### VIM 代码高亮

+ 安装 vue 的 VIM 高亮插件，如 posva/vim-vue。
+ 配置 `.wxc` 或 `.wxp` 后缀名的文件使用 vue 语法高亮

``` vim
 au BufRead,BufNewFile *.wxc setlocal filetype=vue.html.javascript.css
 au BufRead,BufNewFile *.wxp setlocal filetype=vue.html.javascript.css
```

## ○ 开源协议

本项目基于 [MIT](http://opensource.org/licenses/MIT) License，请自由的享受、参与开源。

## ○ 更新记录

#### v0.0.1（2017.11.02）

- 初始版本

