# Vue Router路由

Vue Router 官网地址：https://router.vuejs.org/zh/



<br>

#### Vue项目中安装router

**第一种：原生html**

直接通过标签引入最新的 router.js

```
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>
```

或者指定某个具体版本：

```
<script src="https://unpkg.com/vue-router@2.0.0/dist/vue-router.js"></script>
```



<br>

**第二种：手动NPM安装**

```
yarn add vue-router
```



<br>

**第三种：通过CLI额外追加**

假设你的项目是通过 Vue CLI 已创建好的，那么在命令窗口中先切换到你的项目目录中，然后通过 CLI 追加安装 router。

```
vue add router
```



<br>

**第四种：通过CLI创建包含router的项目**

这是最简单，也最方便的方式，即直接使用 CLI 创建一个新的包含 router 功能和示例的 vue 项目。

```
vue create my-vue
```

然后在给出的选项中，选择 `Manually select features`。

在列出的安装特性中，通过上下箭头切换到 Router 这一项，然后摁 空格键 选中 Router 这一项。

接下来摁回车，进行后续步骤。

...

其中会有一个选项：Use history mode for router？(Y/n)

建议选择 Y

最终一路往下操作，即可创建出一个包含 router 简单示例的 Vue 项目。



<br>

**关于Router版本的补充说明：**

假设我们是基于 vue 2.x 的项目，那么 CLI 默认会使用 Router 3.x 版本。

假设我们是基于 vue 3.x 的项目，那么 CLI 默认会使用 Router 4.x 版本。



<br>

本文下面讲解的都是基于 Router 3.x 版本。



<br>

#### Vue Router 基础用法

在 Vue 项目中使用 路由(router) 的基本流程为：

1. 先以组件的形式创建不同页面内容(路由对应的渲染内容)，例如 PageA、PageB

   ```
   //这里创建 2 个最简单基础的 组件
   const PageA = { template: '<div>This is PageA</div>'}
   const PageB = { template: '<div>This is PageB</div>'}
   ```

2. 在 JS 中创建一个路由(router)实例，并设置其路由匹配规则 routes 的值，routes 为数组类型，其中每一个元素拥有 2 个属性：

   1. path：路由对应的路径(url)，例如 "/page-a"、"/page-b"
   2. component：路由命中后对应要渲染的组件，例如 PageA、PageB

   ```
   const router = new VueRouter({
       routes: [
           {path:'/pagea', component:PageA},
           {path:'/pageb', component:PageB}
       ]
   })
   ```

3. 实例化一个 Vue，并将刚才创建好的 router 实例作为配置参数传递给这个 Vue 实例

   ```
   new Vue({
       router,
       render:h =>h(App)
   }).#mount('#app')
   ```

4. 在入口页面(App.vue)中，创建 2 个内容：

   1. 使用 `<router-link>` 标签，添加 N 组 路由跳转的菜单

   2. 添加 `<router-view>` 标签用来作为承载 路由命中后对应需要渲染的组件

      > 你甚至可以简单把 `<router-view>` 标签理解为 “临时占位符” 或 “插槽”

   ```
   <p>
       <router-link to="/pagea">Go to PageA</router-link>
       <router-link to="/pageb">Go to PageB</router-link>
   </p>
   <router-view />
   ```



<br>

**关于 `<router-link>` 中 to 值的补充说明：**

在日常 `<router-link>` 的使用中，关于 to 的值有 2 种设置方式：

1. `to="/xxx"``

2. ``:to="{ ... }"`

   > 请注意 to 前面是有一个 冒号 的

第1种：`to="/xxx"`

这种方式中 to 的值也就是该 路由路径 的字面值。

第2种：`:to="{ ... }"`

这种方式中 to 的值为一个对象，该对象有存在互斥的 2 种属性结构：

1. path 搭配 query：{ path: 'xxx', query?:{ ... } }

   > 如果 to 的值中出现了 path 属性，即使出现 params 也会被忽略

2. name 搭配 params：{ name:'xxx, params?:{ ... }}

   > 如果 to 的值中出现了 name 属性，即使出现 query 也会被忽略

<br>

举例说明：

下面 3 种对 to 的值设定方式，其最终结果是相同的：

```
<router-view to="/xxx"></router-view>
<router-view :to="{path:'xxx'}"></router-view>
<router-view :to="{name:'xxx'}"></router-view>
```

若使用 `:to` 这种形式，且出现了 query 或 params，那么他们分别对应的结果为：

```
<router-view to="/xxx?id=2"></router-view>
<router-view :to="{path:'xxx',query:{id=2}}"></router-view>
```

```
<router-view to="/xxx/2"></router-view>
<router-view :to="{name:'xxx',params:{id=2}}"></router-view>
```

> 与这部分知识对应的是后面我们将要讲解的 “通过 JS 编程控制路由切换”。



<br>

补充说明：

1. 对于 path + query 创建的路由，其访问 query 参数为：this.$router.guery.xx

2. 对于 name + params 创建的路由，其访问 params 参数为：this.$router.params.xx

3. 如果在 router-link 标签中出现 replace 则表明此次跳转不产生新的 浏览器历史记录，而是替换当前历史记录

   ```
   <router-link to="/b" replace ></router-link>
   ```

4. 如果在 router-link 标签中出现 append 则表示标签中的 to 的值是对当前路由地址的追加

   ```
   //假设当前路由路径为 /a
   <router-link to="/b" append ></router-link>
   //由于使用了 append ，所以最终跳转的实际路径为 /a/b
   ```

5. 默认情况下 `<router-link>` 最终会被渲染成 `<a>` 标签，如果想修改渲染成 `<li>` 标签，则需要添加 tag="li" 即可

   ```
   <router-link to='/xxx' tag="li"><router-link>
   ```

6. 默认情况下 `<router-link>` 在 click 后会被触发，但是也可以通过 event 修改成其他事件

   ```
   <router-link to='/xxx' event="mousedown"><router-link>
   ```

   

<br>

**路由没有匹配到会怎样？**

例如上面示例中，假设我们不小心手抖，把路由跳转连接写错了：

```
//正确的
<router-link to="/pageb">Go to PageB</router-link>

//不小心手抖写错了
<router-link to="/pagebb">Go to PageB</router-link>
```

当我们给 `<router-link>` 的 to 属性值设置错了，而  router 中并没有配置 pagebb 对应的页面组件，那会发生什么事情呢？

答：

1. 并不会有任何警告、报错 或 提示
2. `<router-view>` 会被渲染为 空

所以，为了避免这种情况，通常我们会在路由配置中，添加一条可以匹配到 404 的路由规则，具体如何配置稍后讲解。



<br>

**组件与“页面”的目录结构：**

我们知道普通组件为 xxx.vue，而路由跳转对应的页面实际上也是一个组件，形式也是 xxx.vue。

在实际的 Vue 工程化项目中，通常遵循以下目录结构：

1. components 目录用于存放所有组件

2. views  目录用于存放所有页面

   > 当然某些项目可能会将该目录命名为 pages

3. router 目录用于存放定义 router 的 js



<br>

**组件内部获取当前路由信息：**

在组件内部，可以通过 `this.$router` 获取当前路由实例，也就是下面代码中的 router 对象。

```
const router = new VueRouter(...)
```

当使用 `this.$router` 获取到路由实例后，可针对该路由进行一些属性值的获取，或者是执行一些路由跳转的方法。

1. 例如，获取路由中的某些参数

   ```
   this.$router.params.username
   ```

2. 例如，让路由后退到上一个页面

   ```
   this.$router.go(-1)
   ```



<br>

**设置路由的基础路径：**

```
const router = new VueRouter({
    base:'xxxx'
})
```

默认情况下，CLI 创建的项目会设置为：

```
const router = new VueRouter({
  base: process.env.BASE_URL
})
```



<br>

#### 动态路由匹配

在前面例子中，我们设置的路由匹配规则中，命中路由的路径是写死、固定的，即：

```
const router = new VueRouter({
    routes: [
        {path:'/pagea', component:PageA},
        {path:'/pageb', component:PageB}
    ]
})
```

但是在实际项目中，我们需要面临很多种复杂的、动态的路由路径。

不可能使用写死的方式来把所有路由路径都写进去，因此需要路由具备 `动态匹配`。



<br>

请注意：Vue Router 路由匹配使用的是第三方NPM包 `path-to-regexp` 作为路由引擎。

path-to-regexp：https://github.com/pillarjs/path-to-regexp/tree/v1.7.0

也就是说，凡是 path-to-regexp 支持的动态匹配规则 Vue Router 也都支持。

> 这是一句废话



<br>

**优先级规则**

在学习 path-to-regexp 之前，我们先说一下 Vue Router 的优先级规则：路由命中的优先级是按照路由规则定义的先后顺序决定的。

不同于 Nginx 这类专业的服务器后端程序，Vue Router 目前无法做到按照匹配精确度来实现优先级。因此在定义 Vue Router 路由规则时，一定把精准的放在靠前位置，而通用(模糊)类的规则(例如 path:* )放到靠后位置。

<br>

恰恰以为 Vue Router 路由规则优先级的规定，所以通常会将 `path:*` 放在最后，让它作为匹配 404 的路由路径。



<br>

#### path-to-regexp的动态匹配规则

**第1条规则：使用斜杠(/)来作为路径的默认分割符**

例如：/page 和 /page/a 就是 2 个不同的路径

> 注意：斜杠为 默认分隔符，实际 Vue Router 已经封装好了这个分隔符，因此你也无法修改



<br>

**第2条规则：使用冒号(:)来匹配一个动态参数**

假设我们将匹配规则设置为：/page/:id

那么 /page/2、/page/a、/page/b、/page/abc 这些路径都将被命中。

同时路由参数中 `this.$router.params.id` 的值分别对应 2、a、b、abc

<br>

假设匹配规则设置为：/:foo/:bar，哪又对应什么路由呢？

1. this.$router.params.foo
2. this.$router.params.bar



<br>

**第3条规则：路径中只能正常解析以下字符**

1. 大写字母：A-Z
2. 小写字母：a-z
3. 数字：0-9
4. 下划线：_

请记得：不支持中划线(-)



<br>

**第4条规则：在参数后面加上问号(?)来表达这个参数为可选项**

假设我们的路由规则为：/foo/:bar?

那么 bar 即为可选项，也就是说无论 bar 是否存在都符合(命中)该条路由规则。

例如：/foo、/foo/aa、/foo/0



<br>

**第5条规则：使用加号(+)来表明参数必须至少出现一次**

假设我们的路由规则为：/:foo+

由于参数 foo 后面有 + ，所以 foo 必须至少出现一次才会命中该路由。

例如：

1. /：由于参数 foo  一次也没出现，所以该路径不会被命中
2. /aa：由于参数 foo 出现了一次，值为 aaa，所以该条路径会被命中
3. /aa/bb：由于参数  foo 出现了不止一次，此时 foo 对应的值为 aa/bb，所以该条路径会被命中



<br>

**第5条规则：使用括号+正则表达式来限定参数的字符格式**

假设我们的路由规则为：`/:foo(\\d+)`

从括号里的正则表达式可以看出必须要求出现 1 次以上的数字。

> 请注意 正则表达式 `d+` 中 d 表示数字，+ 表示至少出现 1 次
>
> `d+`并不要求必须全部是数字

那么：

1. /aaa：由于没有出现任何数字，所以这个路径不会被命中
2. /123：由于出现了至少 1 个数字，所以这个路径会被命中

补充：

当我们书写规则为 /:foo 的时候，没有使用任何正则表达式，实际上 path-to-regexp 默认 /:foo 对应的正则表达式为 `[^\/]+`

> `[^\/]+` 表达的意思为：以 / 为开头，且至少出现一次

提醒：在括号中书写正则表达式时，切记要使用 双斜杠，例如 `\\d`。



<br>

**第7条规则：使用(.*)来匹配剩余所有未命名参数**

假设我们的路由规则为：/:foo/(.*)

我们要匹配的路由路径为：/aa/bb

那么：

1. 路由中有一个参数为 foo，值为 aa
2. 路由中剩余所有的参数为一个对象，该对象属性中数字索引 0 对应的值为 bb
3. 假设路由路径为 /aa/bb/cc，那么对象属性中数字索引 0 对应的值为 bb，数字索引 1 对应的值为 cc，以此类推



<br>

**第8条规则：使用星号(*)来匹配任意多个字符**

假设我们的路由规则为：/*

那么由于 * 可以匹配到任意的字符，所以下面的路径都可以被匹配到：

1. /
2. /aaa
3. /aaa/bb

所以通常会将 /* 作为路由最后一条规则，用于匹配 404 。



<br>

**补充说明 1：**

path-to-regexp 实际上执行的是类似 伪静态 一样的路径规则。

与之对应的像：/aa?id=2&name=ypx 这种路径实际上也在它考虑和解析的范围内。

> 这类请求变量值存储在 this.$router.query 中



<br>

**补充说明 2：**

无论第 7 条 还是第 8 条规则，都使用到了 通配符 * ，对于 Vue Router 而言，他会将所有通配符匹配到的参数存放在路由(router)参数(this.$router.params)的 `.pathMatch` 变量中。



<br>

**补充说明 3：**

在 Vue Router 中除了 * 号可以匹配任意路径，实际上使用 空白字符('') 也可以启到匹配任何未匹配到参数的效果。



<br>

#### 嵌套式路由

在我们上面提到的路由例子中，采用的是下面这样的流程逻辑：

1. 定义组件 ComponentA、ComponentB

2. 定义页面 PangeA、PageB

   > 页面中可能会使用到 ComponentA 或 ComponentB

3. 定义路由实例 router，并且添加路由规则

4. 在 App.vue 中添加 `<router-link>` 和 `<router-view>`

这里面存在一个简单的 “上下级渲染” 关系，即 router 根据命中的路由规则确定哪个页面内容被渲染到 `<router-view>` 中。

至于该页面的内容是什么，实际上 router 并不知道，也无权决定。



<br>

但是在实际的项目中，页面渲染什么内容的逻辑可能比较复杂，这时候就需要使用到 **嵌套式路由**，赋予 router 更多权利，用于管理和配置被渲染页面里的内容。

具体的操作流程为：

1. 除了 App.vue 添加 `<router-view>` 标签外，还在页面组件的模板中也添加该标签，例如在 PageA 和 PageB 的模板中也添加 `<router-view>`。

   > 请注意，对于 PageA 和 PageB 而言，他们可以把 `<router-view>` 看作是某个 “占位符” 或 “插槽”，至于 `<router-view>` 将来究竟要被渲染成什么内容，不由他们自己决定。

2. 在配置 router 时，每一条路由规则中除了原本需要设置的 2 个属性 `path` 和 `component` 外，新增一个 `children`的属性。该属性的值和 VueRouter 中 routes 的值定义方式完全相同。

   > children.routes 也是一个数组，数组每个元素也是需要设置 2 个属性 `path` 和 `component`

3. 当某个页面被命中后，会再次根据路径中的某些参数去匹配 children 中 routes 的规则，当再次匹配到内容后则会将对应的 component 组件内容渲染到该页面中 `<router-view>`。

经过上面的流程，实际上存在  2 个层级的路由：

1. App.vue 中的 `<router-view>` 为第 1 层路由，负责承载被渲染的那个页面
2. 被渲染页面中的 `<router-view>` 为第 2 层路由，负责承载由 router.routes[x].children.routes[y] 中的规则来决定渲染的那个组件

而所有的最终决定权，都由  router 来决定。



<br>

路由虽然看上去简单，但是整个应用程序的核心入口。



<br>

#### 通过JS控制路由切换

**this.$router.push()**

在上面的示例中，我们都是通过 `<router-link>` 标签来控制路由切换的。

实际中，我们也可以通过 JS 来对路由进行切换、跳转。

`<router-link :to="{ ... }">` 对应的是 `this.$router.push({...})`

举例：

```
//点击 <router-link> 标签进行跳转到 /xxx
<router-link to='/xxx'></router-link>

//通过 JS 控制路由跳转同样的 url
this.$router.push('xxx')
```

```
//点击 <router-link> 标签进行跳转到 /xxx?id=2
<router-link :to='{path:"xxx",query:{id:2}}'></router-link>

//通过 JS 控制路由跳转同样的 url
this.$router.push({
    path:'xxx',
    query:{
        id:2
    }
})
```

```
//点击 <router-link> 标签进行跳转到 /xxx/2
<router-link :to='{name:"xxx",params:{id:2}}'></router-link>

//通过 JS 控制路由跳转同样的 url
this.$router.push({
    name:'xxx',
    params:{
        id:2
    }
})
```



<br>

请注意：上面示例中例如 name:'xxx'，实际上还可以使用模板字符串，例如 name:`/xxx/${xx}`

由于可以使用模板字符串来拼接 name 或 path，那么无论 name 还是 path 都可以拼接处彼此对方的形态。

例如 原本使用 name 其结果为伪静态类型的路径，但通过模板字符串可以改造成 动态类型，例如：

```
this.$router.push({
    name:'/xxx?id=${xx}'
})
```



<br>

**this.$router.replace()**

上面提到的 .push() 方法会向浏览器历史添加一条历史记录。

而 .replace() 函数用法和 .push() 完全相同，只不过它并不会添加一条历史记录，而是替换当前这条历史记录。

如果使用的是 `<router-link>` 标签，可通过添加 `replace` 属性来表明这条连接是用于替换当前历史记录，而不是新增。

```
<router-link to='/xxxx' replace>
```



<br>

**this.$router.go(n)**

这个 .go() 函数用于跳转到某个历史记录。

```
this.$router.go(1) //前进一个页面
this.$router.go(-1) //后对一个页面
this.$router.go(3)  //前进 3 个页面(前提是历史记录中存在这个记录)
```

如果历史记录并不够用，那么就会默默跳转失败。

> 所谓 “默默”，意味着并不会收到报错信息

想要获取浏览器历史记录可用长度，对应代码为：

```
window.history.length
```



<br>

**this.$router.back()**

后退一步



<br>

**this.$router.forward()**

前进一步



<br>

**this.$router.fullPath**

读取该属性，可以获取完整的路径



<br>

**this.$router.matched**

读取该属性，返回一个数组，包含所有命中的路由对象



<br>

**this.$router.name**

读取该属性，返回当前路由对应的 name 值



<br>

**this.$router.redirectedFrom**

如果存在重定向，则返回重定向来源的路由的名字



<br>

**关于路由 path/name 的补充：**

在前面我们提到定义路由路径的 2 种方式：

1. 使用 path + query
2. 使用 name + params

这实际上是站在 `<router-link>` 或 `this.$router.push()` 角度上来看待如何最终匹配的。

但是如果站在 路由实例 router 的角度来看，path 和 name 是不冲突的。

准确来说：name + params 实际上是 path 的另外一种定义方式。

例如：

```
const router = new VueRouter=({
    routes:[
        {
            path:'/user/:userid',
            name:'user',
            component:User
        }
    ]
})
```

在上面代码中，我们在路由实例内部，定义了 `path:/user/:userid`，同时我们也定义了 `name:user`。

因此在使用的过程中 (站在 `<router-link> 和 this.$router.push()`的角度)，我们可以通过 name + param 的组合形式来命中 `/user/:userid`。

```
//命中 /user/:userid
this.$router.push({
    name:user,
    params:{
        userid:2
    }
})
```



<br>

#### `<router-view>`的name属性

在上面讲解的路由例子中，无论 App.vue 还是页面，他们都只包含一个 `<router-view>` 标签。

实际上它们是可以同时包含多个 `<router-view>` 标签的。

> 在 Vue Router 官方文档中，将 `<router-view>` 称呼为 “视图”



<br>

试想一下这个应用场景：假设某个页面中存在 A、B、C 3 个模块。

> 你可以把 A B C 想象成页面的 顶部、侧边栏 和 中间内容

当命中某个路由路径时，希望分别将 A、B、C 3 块渲染成 3 个不同的组件内容。

这个时候，我们就可以在这个页面中 添加 3 个 `<router-view>` 标签，并为其添加不同的 name 属性值。

```
<router-view name="a"></router-view>
<router-view name="b"></router-view>
<router-view name="c"></router-view>
```

> 你可以理解为  3 个 “占位符” 或 “插槽”

然后我们将路由实例的配置由渲染单个的 `component` 修改为 `components`，并进行相关设置：

```
const router = new VueRouter({
    routes: [
        path:'/xx',
        components:{
            a:ComponentA,
            b:ComponentB,
            c:ComponentC
        }
    ]
})
```

这样当路由命中 `/xx` 后，就会根据 components 中配置的多个 `name` 名字找到对应的 `<router-view>`，然后依次对他们进行渲染。 



<br>

实际上当我们使用 `<router-view />` 时，在没有向其添加 name 属性值时，默认 name 的值为 "default"。



<br>

上面的套路也可以应用在 嵌套式路由 中。



<br>

#### 重定向和别名

**重定向：**

假设当前命中了路由路径 '/aa'，但是由于某种原因我们需要跳转到 '/bb' 对应的路由路径上。

这就叫做：路由重定向，可以通过 `redirect` 属性来实现。

```
const router = new VueRouter({
    routes:[
        { path:'/aa', redirect:'/bb'},
        ...
    ]
})
```

在上面的代码中，重定向的目标路径我们写成了固定的 "/bb"，实际中还可以使用以下 2 种设定方法：

1. 采用 对象 的形式，例如：

   ```
   const router = new VueRouter({
       routes:[
           { path:'/aa', redirect:{name:'bb'}
           },
           ...
       ]
   })
   ```

2. 采用箭头函数 返回值的形式，例如：

   ```
   const router = new VueRouter({
       routes:[
           { 
               path:'/aa', 
               redirect: to=>{
                   return ...
               }
           },
           ...
       ]
   })
   ```



<br>

经过上面重定向后，当访问 "/aa" 时会自动变成(跳转) "/bb"。



<br>

**别名：**

向路由规则中添加 `alias`  配置。

举例：

```
const router = new VueRouter({
    routes:[
        { path:'/aa',component:ComponentA, alias:'/bb'}
    ]
})
```

上面代码中意味着当访问 "/aa" 和 "/bb" 是效果完全相同，并且浏览器地址也不会发生变化。

> 如果是重定向则浏览器地址会发生变化。



<br>

#### 是否大小写敏感

默认情况下，路由路径是对大小写不敏感的。

如果希望大小写敏感，则通过下面方式进行配置：

```
count router = new VueRouter({
    routes:[ ... ],
    caseSensitive:true
})
```

> 默认为 caseSensitive 为 false



<br>

#### 路由组件传参(解耦)

假设我们有一个路由规则为 "/xxx/:id"，且此刻被命中，需要将组件 `CompA` 渲染到 `<router-view>` 中。

如果组件 CompA 中需要获取参数 id，并且根据 id 的值来做一些内容显示。

那么我们很容易想到在 CompA 组件中使用 `this.$router.param.id` 来获取并使用 id 这个参数值。

问题来了，假设我们在别的地方也需要使用到 CompA 这个组件，但是 CompA 需要使用到的 id 并不是通过路由获取，而是通过设置设置属性值来传递的，例如：

```
<CompA v-bind:id="xxx"></CompA>
```

由于 CompA 内部使用的是 this.$router.params.id，那么很明显 CompA 无法同时适用以上 2 种场景。

为了提高组件的复用性，我们可以通过给 路由组件传参 的形式来解决这个问题。



<br>

所谓 “路由组件传参” 就是在组件内部原本需要使用 `this.$router.params.xx` 获取参数的形式改为通过设置组件绑定属性的方式。



<br>

**操作流程为：**

1. 将 “路由组件” 改成普通的组件，也就是说移除组件内部 this.$router.params 的相关代码，改为通过定义组件 props:[ ... ] 的形式来获得参数。

2. 修改路由规则，添加 `props:true` 设置，将 this.$router.params 中的各个值键对(属性名和属性值)为 “组件属性” 传递给组件。

   例如将 this.$router.params.id 以 id="xxx" 的形式设置于 CompA 上，从而 CompA 可以顺利得到参数 id 的值。

```
const router = new VueRouter({
    routes:[
        { path:'/xxx/:id', component:CompA, props:true }
    ]
})
```

假设路由规则中使用的是 components，那么 props 需要对各个 视图(`<router-view>`) 都做相应设置。

```
const router = new VueRouter({
    routes:[
        { 
            path:'/xxx/:id', 
            components: { default:CompDef, a:CompA, b:CompB },
            props:{ default:true, a:true, b:false}
        }
    ]
})
```



<br>

**关于 Props 值的补充说明：**

Props 可以有 2 种形态：

1. 布尔值
2. 对象
3. 有返回值(对象)的箭头函数

<br>

第1种：布尔值

1. 若为 true：向 Vue Router 表明，请将 this.$router.params 中的值键对以属性(参数)的形式传递给被渲染的组件中。
2. 若为 false：也就是默认值，表明不需要将 this.$router.params 中的值键对传递给组件

<br>

第2种：对象

若为对象，例如 props:{ name:'puxiao' }，包含 2 层含义：

1. 无需将 this.$router.params 中的值键对传递给被渲染的组件
2. 但是请将 props 定义的对象 { name:'puxiao' } 中的值键对作为属性传递给被渲染的组件

<br>

第3种：有返回值(对象)的箭头函数

实际上这是 第2种 的变种形态。简单来说，例如：

```
props:()=>{ return { name:'puxiao' }}
```

实际中可以将 router 中的某些参数进行修改和调整，然后再传递给组件。

举例：

假设路由中的某个参数名为 id，但是组件中定义的变量名却为 useid，那么我们可以这样操作：

```
const router = new VueRouter({
    routes:[
        {
            path:'/xx/:id',
            component:CompA,
            props: router => { useid: router.params.id }
        }
    ]
})
```

> 在上面代码中，我们通过 props 对应的箭头函数，将当前路由 router 的参数 id 成功转化为另外一个对象，其中对应的属性名为 useid，然后将这个对象的值键对作为属性参数传递给组件。

<br>

#### 路由模式

准确来说是 Vue 基于 HTML5 的路由模式。

路由模式分为 3 种：

1. 哈希模式(hash)：相当于单页面模式，并且使用 # 模式，即跳转不通过路由后，网址仅发生 # 后面的变化，历史记录中不存在前进后退。

2. 历史记录模式(history)：相当于不同网址，有前进后退的历史记录，每次跳转网址会自动发生变化

   > 前提是浏览器支持这种模式，当然绝大多数浏览器都是支持的

3. 抽象模式(abstrct)：只要支持 JS 的环境即可，例如 浏览器或 Nodejs 。

   假设 Vue Router 发现当前并不是处于浏览器模式，而是处于 Nodejs 服务器模式，那么就会强制转成 抽象模式。



<br>

实际中更多倾向于使用 历史记录模式(history)，因为这个更加符合用户操作体验。

但问题是，我们知道 Vue 项目入口实际上就只有一个  index.html，所谓路由网址的变化都仅仅是 Vue Router 内部实现的，例如：/aa/bb，实际服务器上并不存在这个目录结构。

当我们选择将构建好的 Vue 项目发布到服务器上时，还需要针对后端 Web 服务程序进行相应的路由设置，以避免出现 404 情况。



<br>

**后端 Web 服务程序相对应的路由配置**

后端 Web 服务程序有非常多种，例如 Nginx、IIS、Apache、Nodejs 等等。

如果是 Nginx ，那么需要在当前站点的配置文件中，做以下配置：

```
location / {
  try_files $uri $uri/ /index.html;
}
```

> 将当前站点下所有路径请求都返回 index.html



<br>

**Vue 对应的 404 设置：**

对于 Vue 项目而言，最好要添加 404 处理，以给用户明确的提示。

> 如果你不做 404 处理，那么客户请求不存在的网址则永远会显示 首页

```
const router = new VueRouter({
    routes:[
        ...,
        { path:'*',component: NotFoundComponent }
    ]
})
```

> 上面代码中，假定我们有一个显示 404 内容的组件 NotFoundComponent



<br>

#### 导航守卫

当用户通过点击 `<router-link>`标签 或者通过 JS this.$router.push() 来操作跳转当前路由地址(URL) 时，Vue 提供了一套机制，可以让你有机会进行改变或者取消此次跳转。

应用场景举例：

假设现在正处于提交表单的视图中，此时因为用户的某些操作，发生路由要跳转到其他 “页面”。

1. 用户可能直接通过修改网址进行跳转
2. 也可能通过鼠标点击其他栏目进行跳转

如果真的直接就跳转到其他页面，当前表格中已输入的内容就可能会随之消失，此时通过添加路由 “守卫”，让 我们有机会取消此次跳转，或者弹框告诉用户是否跳转还是继续停留在当前页面中。

这个 “守卫” 机制就叫做 “导航守卫”。

> 当然你也可以把他称作为 路由守卫



<br>

**导航守卫的几种设置方式：**

1. 全局前置守卫
2. 全局解析守卫
3. 全局后置钩子函数 (这并不是一个路由守卫)
4. 路由规则独享的守卫
5. 组件内监控路由变化 (这并不是一个路由守卫)
6. 组件内的守卫



<br>

**全局前置设置：**

所谓 “前置”，即针对每一次路由跳转，都需要先执行的函数。

```
const router = new VueRouter({ ... })
router.beforeEach((to,from,next) =>{
    ...
})
```

路由跳转实际上是一个异步的过程，假设添加有 .beforeEach()，那么每次路由跳转前一定先执行完 .beforeEach() 后才会开始真正跳转。

因此你有机会在 .beforeEach() 的函数中取消本次跳转。

其中 .beforeEach((to,from,next)=>{ ... }) 对应的 3 个参数为：

1. to：类型为 Router，即将进入的目标路由对象

2. from：类型为 Router，当前导航即将要离开的路由对象

3. next：Function，一定要调用该方法来结束当前的异步进程。

   1. 若执行 next()，则 意味着进入下一操作环节

   2. 若执行 next(false)，则意味着中断(终止)路由跳转，并将浏览器中的 URL 恢复成当前路由对应的 URL

   3. 若执行 next('/') 或 next({ path:'/' })，或者是其他任意的路由路径，则意味着即将要跳转到另外一个不同的 URL 中。同时与允许你添加任何符合 router-link 标签规范的属性，例如 replace。

   4. 若执行 next(error)，则意味着路由跳转将会被终止，且对外抛出一个 错误事件，该事件会被 router.onError() 捕获，并执行相应的回调函数。

      > 当然，假设你从未添加过 router.onError()，那么也意味着这个错误将 “默默” 的发生并消失。

   请记得，无论执行的是上面哪种情况，next 函数只应该被当前守卫执行一次。

举例：

假设当用户想要访问某个视图前，我们都对其进行一次身份验证。

1. 若发现用户没有登陆，那么就将路由跳转到用户登录页

   > 假设用户此刻本身就访问的是登录页，那么这个情况下除外

2. 若发现用户已登录，则跳过此次守卫，让路由按照客户想要的进行跳转。

对应代码：

```
router.beforeEach((to,from,next)=>{
   if(to.name !=='login' && !isAuthenticated ){
       next({name:'Login'}) // 若发现要跳转的目标视图并非登录页 且 用户身份没有验证，那么就跳转到登录页
   }else{
       next() //跳过此次守卫，进行下一路由环节
   }
})
```

请注意，你可以通过添加多个 .beforeEach()，他们会按照添加的先后顺序依次执行。



<br>

**全局解析守卫：**

通过 router.beforeResolve() 添加一个全局解析守卫。和 router.beforeEach() 很类似，区别在于 先执行 .beforeEach()，然后才执行 .beforeResolve()。



<br>

**全局后置钩子：**

通过 router.afterEach((to,from) =>{ ... }) 可以添加全局后置钩子。

请注意全局后置钩子表示当路由跳转已发生完成后才会触发的钩子函数，因此在这个函数中是没有 next 函数的。

> 路由已经跳转，既成事实，所以无法使用 next 来进行路由拦截和守卫。



<br>

**路由规则中独享的守卫：**

也就是在配置路由规则时，添加 `beforeEnter` 对应的回调函数。

举例：

```
const router = new VueRouter({
    routes:[
        {path:'/foo',component:Foo, beforeEnter:(to,from,next)=>{ ... }}
    ]
})
```

也就是说，当命中该路由规则时，准备跳转到这个路由前(beforeEnter) 触发的守卫函数



<br>

**组件内监控路由发生变化：**

在组件内部，可以通过添加 `watch:{$route(to,from){}}` 来监控路由的变动。

```
const User = {
    template:'...',
    watch:{
        $route(to,from){
            ...
        }
    }
}
```

> 请注意，这仅仅是监控路由变化，参数中也不包含 next 函数，因此无法取消或者改变路由跳转



<bt>

**组件内的守卫**

在组件内部，通过添加一些路由钩子函数，来对路由的变化进行一些操作。

1. beforeRouteEnter(to, from, next)：

   当路由发生变化，刚刚准备创建该组件时触发的钩子函数，在该函数中不可使用 this

   尽管不支持 this，但是 beforeRouteEnter 中的 next 却支持通过回调 访问当前组件的 “虚拟DOM”，例如：

   ```
   const User = {
       template:'...',
       beforeRouteUpdate(to,form,next){
           next(vm =>{
               vm.xxx //此时的 vm 和 this 相似但又不同
           })
       }
   }
   ```

   > 只有 beforeRouteEnter() 支持这种 vm 回调

2. beforeRouteUpdate(to, from, next)：

   当路由发生变化，且当前组件依然被重复调用时触发的钩子函数，在该函数中可以使用 this

3. beforeRouteLeave(to, from, next)：

   当路由发生变化，即将离开时触发的钩子函数，在该函数中可以使用 this

举例：

```
const User = {
    template:'...',
    beforeRouteUpdate(to,form,next){
        ...
    }
}
```



<br>

**总结：完整路由跳转的整个流程**

1. 导航被触发
2. 在失活的组件里调用 beforeRouteLeave 守卫
3. 调用全局的 beforeEach 守卫
4. 在重用的组件里调用 beforeRouteUpdate 守卫
5. 在路由配置里调用 beforeEnter
6. 解析异步路由组件
7. 在被激活的组件里调用 beforeRouteEnter
8. 调用全局的 beforeResolve 守卫
9. 导航被确认
10. 调用全局的 afterEach 钩子函数
11. 触发 DOM 更新
12. 调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入



<br>

#### 路由元信息

> 之所以叫 “元信息” 是因为对应的英文单词 meta 直译过来就叫 元信息

在每一条路由配置规则中，除了 path，component 以外，还可以添加一个 `meta` 的字段，meta 的值为一个对象，用于传递一些元信息(额外信息)。

> 之所以称呼为 额外信息、元信息 是因为这些信息并不是给 组件使用的

使用代码如下：

```
const router = new VueRouter({
    routes:[
        { path:'/xx', component:CompXxx, meta:{ ... }}
    ]
})
```

可以将 meta 的值设置为值键对形式的对象，这些值键对对应的信息可传递给组件内的 路由守卫 来使用。

由于可能存在 嵌套路由，所以对于全局而言，命中的路由记录作为数组存储在 路由实例的 .mateched 中，那么我们需要做的就是遍历这个数组，针对每一个元素(路由实例)进行相关设置，即读取该路由实例中获得到的 meta 信息。

**举例说明：**

假设我们需要对视图增加一个 “只有在登录下才可以被使用”的功能，那么我们可以在路由规则中，对某些路由配置添加一个 元信息(meta)，该元信息包含 requiresAuth 的字段，用于表明是否需要验证已登录。

假设我们路由规则中不添加 meta 信息，那么就意味着这个组件无需接收用户是否已登录检查。

实现步骤为：

1. 通过调用全局路由实例的 beforeEach() 函数添加一个路由守卫

2. 检查该视图所有的路由中是否至少有一个需要登录验证

   > 也就是说，假设当前视图中有一个组件需要必须登录后才可使用，那么就不渲染当前视图

3. 具体方法为：遍历 to.mateched ，检查每一个元素 .meta 中存在 requiresAuth 字段，且值为 ture 的路由

   > 若全部都不需验证，则执行 next() 跳过

4. 验证用户是否已登录，若未登录则通过 next() 将路由跳转到登录页

   > 若已登录则执行 next() 跳过

```
// meta:{requiresAuth:true}

//以全局导航守卫为例，检查元信息
router.beforeEach((to,from,next)=>{
    if(to.matched.some(record => record.meta.requiresAuth)){
        if(!auth.loggedIn()){
            next({path:'/login',query:{redirect:to.fullPath}})
        }else{
            next()
        }
    }else{
        next()
    }
})
```

> Array 的 .some() 函数用于检查数组是否至少有一个元素与之匹配，并返回 布尔结果。



<br>

**路由元信息的作用：**

在上面那个示例场景中，假设组件需要用户登录才可以使用，那么通过给路由添加元信息，并在全局路由守卫中进行检查，相当于把原本需要写在组件内的登录判断抽出，移动到全局路由守卫中。

这样相当于降低了组件本身的逻辑，提高了整个视图的性能。



<br>

#### 数据获取

假设路由获取了一个参数 id，组件或视图需要根据这个 id 来进行网络数据请求，并将结果显示出来。

那通常只有 2 种方案：

1. 先请求数据，后显示组件(完成导航)：

   在组件路由守卫中，例如 router.beforeRouteEnter() 去发起网络数据请求，等得到结果后再通过调用 next() 来进行组件渲染。 

2. 先显示组件(完成导航)，后请求数据：

   先正常显示(渲染)组件，在组件内部创建 loading 变量 和 请求 id 变量。并默认先显示 loading 界面，然后根据请求 id 变量来发起网络数据请求，在得到结果后更新对应内容。

   请注意，所谓 “根据请求 id 变量” 包含 2 层含义：

   1. 组件第一次挂载完成后，根据 id 立即发起请求
   2. 组件通过 watch 监控 id 的变化，当发现 id 更新后则重新发起网络请求

无论哪种方案，都需要考虑 网络请求错误 的情况。



<br>

**先请求数据，后完成导航**

假设当前页面为 PageA，需要跳转到 PageB。

那么我们需要做的事情是：

1. 在当前页面 PageA 上叠加一层显示加载的内容
2. 在路由守卫函数中发起网络请求
3. 当得到请求结果后，执行 next()，此时 PageA 和 PageA 上的 loading 内容消失，页面跳转(视图渲染) 为 PageB

```
//组件内可能的JS
export default{
    data:{post:null,error:null},
    beforeRouteEnter(to,from,next){
        getPost(to.params.id,(err,post)=>{
            next(vm => vm.setData(err,post))
        })
    },
    beforeRouteUpdate(to,from,next){
        this.post = null
        getPost(to.params.id,(err,post)=>{
            this.setData(err,post)
            next()
        })
    },
    methods:{
        setData(err,post){
           ...
        }
    }
}
```

> 既要考虑 beforeRouteEnter，有需要考虑 beforeRouteUpdate



<br>

**先完成导航，再请求数据**

假设当前页面为 PageA，需要跳转到 PageB。

那么我们需要做的事情是：

1. 将当前视图渲染 PageB
2. 默认视图中一部分区域显示 loading 内容
3. 组件监控 id 并开始发起网络请求，并将结果渲染到当前视图中

```
//组件可能的结构
<template>
    <div>
        <div v-if="loading" ></div>
        <div v-if="error" ></div>
        <div v-if="post" ></div>
    </div>
</template>

//组件可能的JS逻辑
export default{
    data：{loading:false, error:null, post:null},
    created:function{ this.fetchData() },
    watch:{ '$route': 'fetchData'}，
    methods:{
        fetchData(){
            ...
        }
    }
}
```

> 既要考虑 created，又要考虑 watch 去监控路由的改变



<br>

#### 页面视图滚动定位

我们可以设置当一个视图被显示后，页面滚动条显示到哪个位置。

通过给路由实例添加 `scrollBehavior` 的方法来实现。

```
const router = new VueRouter({
    routes:[...],
    scrollBehavior(to,from,savedPosition){
        //将期望的位置通过 return 形式返回
        return ...
    }
})
```

例如：

```
scrollBehavior(to,from,savedPosition){
    return { x:number, y:number }
}
```

或

```
scrollBehavior(to,from,savedPosition){
    return {
        selector:string,
        offset?:{x:number, y:number}
    }
}
```

> 请注意上面代码中的 "x:number" 表示 x 的值为一个数字，例如 “x:24”

如果返回的是 虚值(falsy) 或 空，则不做任何滚动。

> 所谓 虚值(falsy) 是指假设把这个值转换为 Boolean 类型其结果为 false 的值。



<br>

关于 scrollBehavior 的更多复杂用法，可参考 Vue Router 官网 API。



<br>

#### 路由懒加载

简单来说就是原本组件应该是整体打包成一个 .js 文件，现在为了考虑拆分文件大小，将一些组件的内容先不打包到整体中，只有当使用到该组件时才进行加载。

**实现方式：**

加载我们原本定义组件的方式为：

```
const Foo = { ... }
```

修改成：

```
const Foo = () => import('./Foo.vue')
```

> 这样 webpack 就不会将 Foo.vue 的内容打包到整体中，但是独立拆分成一个文件。
>
> 当 Foo 组件真正被调用时才会真正加载这个文件

无论组件是否采用懒加载的形式定义，对于路由而言是没有区别的。

> 路由只知道当命中某个 url 时需要将一个名为 Foo 的组件渲染到 route-view 中，但至于 Foo 组件究竟是否使用了懒加载，路由是不关心的。



<br>

**将多个组件打包到一个文件中**

假设现在有 3 个组件，都希望不打包到整体中，但是这 3 个组件又不想各自独立被打包成 3 个文件，而是希望将他们 3 个打包在一个文件中。那么这个时候就需要使用 webpack 的 魔法注释 了。

<br>

这个魔法注释就是：`/* webpackChunkName:"group-foo" */`

> 关于 webpack 其他类型的魔法注释，请查阅相关文档

```
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue')
```

这样 3 个组件 Foo、Bar、Baz 就会都被打包到一个 group-foo 的文件模块中。



<br>

#### 监控导航(路由)跳转出错

通常情况下，我们是无需监控路由跳转失败或发生错误的。

1. 因为即使路由跳转到一个不存在的地址，即 404，路由也不会报错
2. 通常情况下路由都会按照我们的预期进行发生

但是假设某些异常，或者是我们在某些路由守卫中

1. 使用 next(error) 人为得抛出一些错误

2. 使用 next(false) 中断此次路由跳转

   > 由于跳转被中断，实际上相当于路由没有按照预期进行跳转，因此也被视为 异常情况

对于这些特殊情况，是可以通过在路由跳转时添加 catch 来捕获的。

```
router.push('/admin').catch(failure =>{
    ...
})
```



<br>

**判断是哪种类型的错误异常**

在 catch 的回调函数中，Vue 为我们提供了一个 `isNavigationFailure()` 的函数，用于检测发生错误的原因。

这个函数接受 2 个参数：

1. failure：发生错误对应的此次错误，实际上就是将 catch 捕获到的 failure 传递给 isNavigationFailure() 函数
2. string(常量，可选参数)：用于判断是否是以下哪种原因造成的异常
   1. NavigationFailureType.redirected：导航守卫将路由进行重定向
   2. NavigationFailureType.aborted：导航守卫中断了本次导航
   3. NavigationFailureType.cancelled：在当前导航还未顺利完成前，在其他某些地方又调用了新的导航跳转
   4. NavigationFailureType.duplicated：导航被阻止，例如我们此刻本身就处在这个目标导航中

假设不传递第 2 个参数，只是认定这是一次导航故障，则不做过多原因的细分和检测。



<br>

**获取失败的具体对象细节**

在 failure 回到函数内部，我们可以根据 failure 来作出相应的代码。

其中：

1. failure.to 表示要跳转的目标路由

   > failure.to.path 即我们最开始希望跳转的目录路径

2. failure.from 表示跳转前的路由



<br>

#### Vue Router 的 API 细则

在上面我们只是讲解了 Vue Router 最基础，常见的用法。

更多 API 需要去查看官方文档。

https://router.vuejs.org/zh/api/