# Vue CLI 创建项目

Vue CLI 相当于 React 的 Create-react-app，用于快速创建 Vue 项目工程的脚手架。

官网地址：https://cli.vuejs.org/zh

<br>

> 在 @vue/cli 官网上写着：“无需 eject”，是在内涵 create-react-app 无法直接二次修改 webpack 的配置。
>
> 真是各说各家好。



<br>

#### 全局安装

**全局安装：**

```
yarn global add @vue/cli

# or

npm i @vue/cli -g
```

<br>

**安装完成后，可查看版本：**

```
vue --verison

# or

vue -V
```

> 此刻我安装的版本为 4.5.13



<br>

**查看所有命令的帮助：**

```
vue -h

# or

vue --help
```


<br>

**补充说明：**

1. @vue/cli 包含有 @vue/cli-service 这个包，用于创建可调试、构建项目
2. @vue/cli 是基于 webpack 编译的，若想修改成 vite 则需要其他方式
3. @vue/cli 可用于创建 vue 2x 或 vue 3 项目
4. @vue/cli 创建的项目自带 typescript



<br>

#### 初始化项目

当 @vue/cli 全局安装成功后，提供 3 个关键命令。



**创建新项目**

```
vue create my-project
```

<br>

**创建项目的 4 点说明：**

1. 第一次创建项目时会询问 是否将当前 NPM 源修改为 cnpm。

2. 创建过程中会询问你使用 npm 还是 yarn  命令，这个命令决定了将来你调试项目时使用的命令前缀。例如我选择 yarn，则当我调试项目时对应的命令为 yarn serve，至于其他命令都可查阅创建好项目文件中的 README.md。

3. 创建过程中会显示 3 种创建内容方式：

   1. 创建 vue 2 (默认不包含 typescript)

   2. 创建 vue 3

   3. 手工选择特性(Manually select features)

      > 可以选择是否包含 typescript、vuex、vue router 等

4. @vue/cli 官网上面的某些操作步骤界面已经和当前最新版本略微不同，不过这些不同都不是特别重要的，只要创建一次就明白了。



<br>

**关于手工选择安装特性的操作补充：**

在创建项目时，若选择 手工选择安装特性(`Manually select features`) 这种方式时，其中有一环节会让你选择究竟安装哪些内容。

除了默认自动选中的几项外，你还可以额外去勾选其他几种，包括：

1. typescript
2. vuex
3. vue router
4. ...

请注意，选择的操作方式为：

1. 通过上`下箭头` 切换到当前选项
2. 通过摁 `空格键` 来选中或取消当前项
3. 摁 `i 键`可以对当前已选项进行 反选
4. 最终摁 `回车键` 确认并进入下一环节



<br>

**补充说明：**

如果你选择手工选择特性，并且选择使用 eslint ，当项目创建完成后，你需要手工修改 .eslintrc.js

在 parserOptions 一项中，加入语言版本(ecmaVersion)和模块类型(sourceType)。

```
module.exports = {
  ...
  parserOptions: {
    parser: "babel-eslint",
    ecmaVersion: 2017,
    sourceType: "module"
  }
};
```



<br>

**构建自定义想法的原型**

```
vue serve
```


<br>

**通过图形化界面管理项目**

```
vue ui
```


<br>

这些命令操作实际上 create-react-app 非常类似，就不做过多讲解了。



<br>

#### VSCode中需要安装的插件

需要安装一些插件，方便我们开发使用。



**Better Comments**

安装之后，打开 .vue 文件，在 VSCode 底部状态栏的 “Select Language Mode”，从弹出列表中选择 “Vue”，这样我们就可以获取 .vue 相关文件的一些代码格式规范。

> 如果你不安装这个插件，选项栏中是没有 vue 这个选项的



<br>

对于格式化代码的插件推荐：Vetur 或 Volar

> 请注意，你只应该选择其中一个，而不是 两个都安装。
>
> 我个人偏向选择 Volar



<br>

**Vetur(不推荐)**

用于格式化、规范 .vue 代码

> 格式化代码依然使用 VSCode 默认的 shift + alt + F



<br>

**Volar(强烈推荐)**

针对 Vue 3 提供一些新特性支持。

但实际上对于 Vue 2.x 也有很大的帮助，安装之后，在 .vue 文件的底部状态栏会出现 2 个新的选项：

1. Atrs：规定属性名命名方式

   > 我偏向将默认的 kebab-case 修改为 cameCase

2. Tag：规定 Tag 命名方式

   > 我偏向使用默认的 PascalCase

当你打开 .vue 文件后，在 VSCode 右上角，还会新出现一个 绿色 V 的图标，点击该图标可以快速将当前 .vue 中的 3 个标签( template/script/style )拆分到 3 个窗口中，非常方便。



<br>

#### 修改默认配置

这里说的 “默认配置” 包含 2 层含义：

1. 默认 webpack 的配置
2. 默认 Vue 的一些配置

我们需要在新创建的 Vue 项目根目录，新建一个 `vue.config.js` 的配置文件，以后需要添加什么配置就直接修改这个文件的内容即可。

具体修改规则，可参考 Vue CLI 官方配置指南：https://cli.vuejs.org/zh/config/

<br>

下面列举几个最常用的修改配置。



<br>

**将文件路径由默认的根目录修改为相对目录：**

```
// vue.config.js

const path = require('path');
module.exports = {
    publicPath: process.env.NODE_ENV === 'production'? './': '/',
}
```

> 对于 React 项目而言，我们只需在 package.json 中添加 ` "homepage": "."` 即可，但是这个操作在 Vue CLI 创建的项目中不起作用。

上面的配置中会根据当前 运行环境 来决定根目录为什么，我们可以不做区分，直接写死：

```
const path = require('path');
module.exports = {
    publicPath: './'
}
```



<br>

**向 webpack 添加新规则：**

假设我们需要使用 cesium.js，目前来说最新版本中存在一个加载第三方 zip.js 的问题，需要用到一个第三方加载插件。

当我们安装好 `@open-wc/webpack-import-meta-loader` 后，就可以继续修改 vue.config.js 文件，添加 webpack 相关规则。

```
// vue.config.js

const path = require('path');
module.exports = {
    configureWebpack: {
        module: {
            rules: [
                {
                    test: /\.js$/,
                    include: path.resolve(__dirname, 'node_modules/cesium/Source'),
                    use: { loader: require.resolve('@open-wc/webpack-import-meta-loader') }
                }
            ]
        }
    }
}
```



<br>

#### 模式和环境变量

**Vue 的 3 种模式：**

1. 开发模式：development，适用于本地开发过程中的调试、热更新等
2. 测试模式：test，适用于本地测试
3. 生产模式：production，适用于正式发布到线上

这 3 种模式分别对应的是 package.json 中 3 中调试命令：

1. `yarn serve`：开发模式

2. `yarn test`：测试模式

   > 假设在创建项目时就没有选择包含 测试模式，那么你是看不到这条命令的

3. `yarn build`：生产模式



<br>

**指定某种模式**

通过查看 package.json 可以看到 `build` 对应的真实命令为：

```
"build": "vue-cli-service build"
```

假设想更改 build 对应的模式，可以通过在后面添加 `--mode` 参数来实现，例如：

```
"build": "vue-cli-service build --mode development"
```

> 这样修改之后，当再执行 `yarn build` 则编译出的文件实际上是 开发模式下的文件。



<br>

**环境变量：**

关于如何设置环境变量，可查阅：

https://cli.vuejs.org/zh/guide/mode-and-env.html#%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F



<br>

至此，一个基本的 Vue 开发环境配置完成，接下来就可以开始写代码了。



<br>

#### 关于Vue组件中编写JS代码的补充说明：

**React VS Vue**

对于 React 而言，假设需要在一个 div 标签 需要被真实创建后进行某些编程，那么 React 的做法通常是：

1. 在 React 组件 return () 中添加 div 标签
2. 并通过 ref、useRef 将该 div 进行勾住
3. 然后在 useEffect 中通过 divRef.current 找到该 div 并开始编写 JS 代码
4. 当组件要卸载前，通过 renturn ()=>{ ... } 取消一些事件侦听 或 销毁某些对象

上面这些操作，对于 Vue 组件而言，对应的操作流程为：

1. 创建 xx.vue 组件文件

2. 在该组件的 `<script>` 标签中，遵循以下格式，添加对应的 挂载完成和销毁前的钩子函数

   ```
   <script>export default {
       name: 'xxx',
       mounted: function(){ ... },
       beforeDestroyed:function { ... }
   }
   </script>
   ```



<br>

**组件钩子函数的拆分：**

假设 mounted 或 beforeDestroyed 对应的函数比较复杂，想拆分出去，那么通常有 2 种拆分方式：

1. 通过 混合(mixin) ：将其他地方定义的配置项，通过 mixins 来进行 并入。
2. 通过 引入(import)：将其他地方定义的函数引入，并赋值给 mounted。

对于第一种 混入(mixin) 而言，具体怎么用在前面已经讲过，这里不做过多阐述。

下面重点说一下第二种 引入(import) 这种方式。



<br>

先看以下代码：

```
//myFun.js
const myFun = function(){
 ...
}

//xxx.vue
<script>
import myFun from './myFun'
export default {
    mounted: myFun
}
</script>
```

以上代码运行是没有问题的。

但是请注意：mounted 是一个 不包含参数的 function，因此 myFun 也必须是一个没有参数的 function。

所以在某些情况下，上面的代码存在 2 种问题。

<br>

**问题1：参数**

假设 myFun 中需要某些参数，那么你就将 myFun 改造成可以接收参数且返回某个 function 的形式，例如：

```
//myFun.js
const myFun = function(str){
   console.log(str)
   return function(){
      str
   }
}

//xxx.vue
<script>
import myFun from './myFun'
export default {
    mounted: myFun('puxiao')
}
</script>
```



<br>

**问题2：无法获取 this**

问题 1 的解决方式虽然可以实现传递普通参数的问题，但是却无法传递 this。

> 组件的 mounted 生命周期，只表明已挂载到父级中，但不一定就已真正挂载到 HTML 中了。

经过试验，在 xxx.vue 中将 this 传递进去，实际上 myFun 内部得到的参数为 undefined。

结论就是：假设你的生命周期函数中需要使用 this，那么你就只能通过 混合(mixin) 的方式来实现 钩子函数拆分。



<br>

实际项目中使用哪种拆分方式，可以根据实际情况来决定。