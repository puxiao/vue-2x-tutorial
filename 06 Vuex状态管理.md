# Vuex状态管理

Vuex 官方网址：https://vuex.vuejs.org/zh/

本文是基于 Vue 2.x 对应的 Vuex V3.x。



<br>

#### Vuex简介

Vuex 是专门针对 Vue 项目而诞生的状态管理库。

如同 React 对应的 Redux 或 Recoil 。



<br>

**项目中不使用状态管理会怎样？**

无论是 Vue 还是 React，除非项目数据非常简单，可以直接使用自带的数据管理方式，否则都需要使用状态管理工具，因为如果你不使用，那么你就会面临以下 2 个情景：

1. 状态传递：当父组件、子组件、孙组件都需要某个数据状态时，组件之间的数据传递会变得异常繁琐。
2. 数据修改：当很多不同地方都需要对同一个数据状态进行修改时，单向数据流会让整个数据修改和同步变得异常难以维护。

<br>

所以，我们需要将整个项目的数据状态进行单独提升，抽离出来作为一个单例，对外提供统一的访问和修改，以便于我们管理整个项目数据状态。

你可以把 Vuex 就看作是针对 Vue 数据状态抽离出来的管理工具。



<br>

#### 安装Vuex

**第1种：原生JS引用：**

```
<script src="/path/to/vue.js"></script>
<script src="https://unpkg.com/vuex@3.6.2/dist/vuex.js"></script>
```



<br>

**第2种：NPM安装**

```
yarn add vuex
```

```
npm i vuex --save
```

当安装好 Vuex 后，在 Vue 模块化项目中就可以通过下面方式使 Vuex 打包到目标结果中。

```
//stor.js

import Vue from 'vue'
imprt Vuex from 'vuex'

Vue.use(Vuex)
export default new Vuex.Store({
    state:{},
    getters:{},
    mutations:{},
    actions:{},
    modules:{}
})
```

```
import store from './store'

new Vue({
    store,
    ...
})
```



<br>

**第3中：Vue CLI 初始化项目时即包含 Vuex**

```
vue create hello-vuex
```

1. 创建项目时，选择 "Manually select features"，手工选择安装特性。
2. 在出现的选项列表中，通过上下箭头切换到 Vuex 并摁下空格，选中该项。
3. 摁回车继续后续选择操作。

当项目创建完成后，默认已经自动帮我们创建好了 Vuex 实例文件：/src/store/index.js



<br>

#### Vuex的基础知识

在上面讲解安装 Vuex 的示例代码中，可能你也隐约看到了下面这样的代码：

```
new Vuex.Store({ ... })
```

可以推测出，我们通过给 Vuex.Store() 传递配置项，得到一个 store 实例。

**store** 这个单词对应的中文翻译为 "仓库"，在这个仓库中储存着我们项目中需要使用到的 数据状态(state)。

每一个仓库(store) 具有 2 个特征：

1. 响应式数据存储：当某个 Vue 组件从该仓库获取某个数据状态后，若仓库中的数据发生变更，那么仓库还会主动通知该组件，并且让该组件自动完成数据更新。
2. 显式数据更改：只能通过显式提交的方式修改仓库中的数据状态。这样做的目的是为了让仓库可以精准知道哪些数据状态发生了变更，从而通知相应的组件更新数据状态。



<br>

Vuex 一共有 5 个概念：

1. state
2. getters
3. mutations
4. actions
5. modules

> 请注意上面后 4 项单词都是复数

接下来将依次针对这 5 个核心展开讲解。

> 实际上 Vuex 学习难度相比 Vue Router 要简单很多。



<br>

#### State：单一状态树(集合)

一个仓库(store)中有能有一个数据状态树(集合)，换句话说一个仓库只对应一个数据状态树(集合)。

> 单例模式

```
const store = new Vue.Store({
    state:{},
    ...
})
```

在上述代码中，我们已经创建实例化了一个仓库 store，那么我们可以通过：`store.state` 来获取这个仓库中唯一的数据状态树(集合)。

> 在 Vuex 官方文档中把 state 称为 状态树，但是我更习惯将其称呼为 "数据状态集合"
>
> 至于究竟该叫 “树” 还是 “集合”，根据个人喜好，随意叫吧。



<br>

举例：

```
//在项目顶层，创建一个 仓库(store)
const store = new Vuex.Store({
    state:{
        count:0
    }
})

//将该仓库注入到 Vue 实例中，从而让这个 Vue 实例中所有的组件均可访问这个仓库(stroe)
//仓库中的数据就好像原本就存在于组件的 data 中一样
const app = new Vue({
    el:'#app',
    stroe
})

//在组件内部，访问仓库(store)中的数据集合(store)
const MyComp = {
    template:`<div>{{ count }}</div>`,
    computed:{
        count(){
            //获取仓库数据集合中的某个数据状态
            return this.$store.state.count
        }
    }
}
```



<br>

**mapState：辅助函数**

试想一下以下 2 个使用情景：

1. 某个组件中需要从仓库数据集合中读取多个数据状态(字段)

   > 把多个分散的数据状态 整合 成一个独立的数据对象

2. 某个组件需要将从仓库数据集合读取到的数据状态结果 “转化” 成另外一种心态

   > 对读取到的数据状态进行二次加工

那么可以通过 `mapState` 将数据集合中的这些字段进行 “拼凑”，从而形成另外一个 “独立” 的数据对象。

```
import { mapState } from 'vuex'
export default {
    computed: mapState({
        count: state => state.count,
        countAlias:'count',
        countPlusLocalState(state){
            return state.count + this.localCount
        }
    })
}
```



<br>

**组件内部数据状态与mapState()的合并**

在上面的代码示例中，我们将组件内的 computed 直接赋值为 mapState({ ... })，但实际情况是我们不光需要 mapState() 的返回对象，还需要组件内定义一些本组件内部的其他数据状态(值键对)。

> 换句话说并不是所有的数据状态都需要存放到 Vuex 的 Store 中。组件内部还是应该可以根据实际情况保留一部分自己的数据状态。

所以，通常情况下，我们会使用 对象的展开运算符，来最终 “拼凑组合” 出 computed 的最终结果。

```
import { mapState } from 'vuex'
export default {
    computed: {
        localXxx(),
        ...mapState({ ... })
    }
}
```

> 上面代码中 computed 的值最终为 { localXxx(), ...mapState({ ... })}



<br>

#### getter：派生(衍生)数据

在仓库实例中，state 用于存放原始的数据状态，同时允许我们通过 getters 添加一些派生(衍生)数据。

假设我们现在的仓库中，存储一个 dotos 的数据，其值为一个数组，包含一些统一的数据。

```
const store = new Vuex.Store({
    state:{
        todos:[
            {id:1,text:'aaa',done:true},
            {id:2,text:'bbb',done:false},
            {id:3,text:'ccc',done:true}
        ]
    }
})
```

假设有一个组件，他只需要读取仓库中 todos 中 done 为 true 的数据，那么我们就可以专门为其添加一个派生(衍生)数据。

> 你可能会有一个疑惑？为什么不让组件读取全部数据，然后组件内部来进行过滤？ 假设只有一个组件有这个需求，那么是可以把这部分工作交给组件自己处理的，但是假设有多个组件都需要这份数据，那么在仓库中统一处理是最为合适的。

我们将上面代码中，新增派生(衍生)数据：

```
const store = new Vuex.Store({
    state:{
        todos:[
            {id:1,text:'aaa',done:true},
            {id:2,text:'bbb',done:false},
            {id:3,text:'ccc',done:true}
        ]
    },
    getters:{
        doneTodos: state =>{
            return state.todos.filter(todo => todo.don)
        }
    }
})
```

> 请注意上述代码中的 doneTodos，实际上相当于我们需要将 state 作为参数传递给 getter 函数。



<br>

此时组件想获取过滤后的数据，代码为：

```
//错误的读取方式
this.$state.doneTodos

//正确的读取方式
this.$state.getters.doneTodos
```



<br>

**getters 中 getter 函数还可以使用其他 getter 函数**

getters 中可以存在多个 getter 函数，同时 getter 函数还可以读取其他 getter 函数，前提是将 getters 作为参数传递给它。

> 也就是说将 state 作为第 1 个参数，getters 作为第 2 个参数

举例：

```
const store = new Vuex.Store({
    state:{
        todos:[
            {id:1,text:'aaa',done:true},
            {id:2,text:'bbb',done:false},
            {id:3,text:'ccc',done:true}
        ]
    },
    getters:{
        doneTodos: state =>{
            return state.todos.filter(todo => todo.don)
        },
        doneTodosCount: (state,getters) =>{
            return getters.doneTodos.length
        }
    }
})
```



<br>

**针对 doneTodosCount 的补充说明：**

尽管我们在这个函数中并未使用到 state，但是依然需要将它作为第一个参数传递进来
如果在某些 TypeScript 项目中假设该参数并未使用 则会报错，那么我们可以使用参数占位符 下划线(_) 来代替 state。



<br>

**让 getter 函数返回另外一个函数：**

在上面的示例中，我们都是将 getter 函数直接返回一个具体的值，我们也可以让 getter 函数返回另外一个函数，这样以便于我们可以传递参数。

通常对数据集合中数据进行查询时，非常有用。

还是上面的示例代码，假设我们这次要新增一个 根据 id 返回对应结果的 getter 函数。

代码如下：

```
getters:{
    getTodoById: (state) => (id) =>{
        return state.todos.find( todo => todo.id === id)
    }
}
```

这样，在组件内即可通过 id 来查询得到对应的数据结果：

```
this.$store.getters.getTodoById(2)
```



<br>

**mapGetters：辅助函数**

在讲解 state 的时候我们提到了 mapState，而针对 gettes 也对应有辅助函数 mapGetters。

假设我们在组件内部想要访问 doneTodosCount，那么我们的代码为：

```
this.$store.getters.doneTodosCount
```

这个代码有一些过于长，我们可以通过 mapGetters 来简化对 doneTodosCount 的请求。

> 所谓的 “简化” 实际上更像是一种 映射

```
{
    computed: {
        xxx,
        ...mapGetters([
            'doneTodosCount',
            'anotherGetter'
        ])
    }
}
```

这样相当于我们把：

1. 在组件内部把 this.doneTodsCount 映射为 this.$store.getters.doneTodosCount
2. this.anotherGetter 映射为 this.$store.getters.anotherGetter



<br>

在上面的代码中 mapGetters() 中的值为一个数组，数组元素需要和 仓库数据集合中的 getters 所定义的 getter 名字完全相同。

实际上我们还可以在 混入(映射) 的时候，进行 “改名”，只不过这次不再使用数组，而是采用值键对的对象。

```
{
    computed: {
        xxx,
        ...mapGetters({
            count:'doneTodosCount',
            another:'anotherGetter'
        })
    }
}
```

我们将：

1. this.count 映射到  this.$store.getters.doneTodosCount
2. this.another 映射到 this.$store.getters.anotherGetter



<br>

#### mutations：更改数据状态

前面我们已经学习过了 state 和 getters，这两项都是用于定义和读取数据状态的。

如果想更改数据状态，则需要使用到 mutations。

> mutation 单词对应的英文翻译为 “变异、突变”，直白点就是 “新状态”，mutations 为 mutation 的复数。



<br>

**发布订阅模式：**

传统的发布订阅模式，通常采用：

1. 监听方添加事件监听
2. 发布方抛出某个类型的事件，同时该事件携带一些数据
3. 监听方接收到事件，然后根据事件类型和事件携带的数据进行下一步处理

对于 Vuex 而言，修改数据集中的数据也采用这样的套路。



<br>

**mutation用法：**

第1步：在仓库(store) 的 mutations 中添加用于修改某个数据状态的函数。

例如：

```
const store = new Vuex.Stroe({
    state:{
        count:1
    },
    mutations:{
        increment(state){
            state.count++
        }
    }
})
```

在上面的代码中：

1. 我们首先在仓库中声明一个名为 count 的数据状态
2. 然后在 mutations 中添加一个名为 increment 的函数，其中传入固定的 state 作为参数。
3. 在 increment 函数内部执行 count ++ 的操作

<br>

第2步：当我们在组件中需要修改仓库数据集中 count 的值时，我们可以：

```
this.$store.commit('increment')
```

这样，increment() 就会被调用，从而修改 count 的值。

<br>

请注意：

1. 我们在调用，希望触发 increment 函数时，是通过  store.commit('increment') 这种方式，这看上去不太像函数的调用，而更像是 抛出名为 "increment" 的事件。

   > 事实上也是这样的

2. 在 mutations 中定义 increment 时我们给他传递(设置)一个 state 的参数，而组件真正 ”调用 increment“ 的时候并没不需要传入这个参数。

   > 这再次印证所谓修改实际上是 触发一个事件，而不是触发一个函数的执行



<br>

**携带载荷(参数)**

继续上面的示例，假设现在我们希望修改 count 为指定的值，我们通过给负责修改的 “函数” 添加第 2 个参数即可。

> 上面中 函数 是加引号的，因为实际上它对应的是一个 定义事件。
>
> 我认为这种 “函数” 更像是 Vuex 给我们的一个语法糖

那么上面的代码应该修改为：

```
const store = new Vuex.Stroe({
    state:{
        count:1
    },
    mutations:{
        setCount(state,num){
            state.count = num
        }
    }
})
```

```
this.$stroe.commit('setCount', 3)
```

<br>

在上面的示例中，我们修改的是一个类型为 number 的数据状态。

那如果希望修改的是一个 对象(Object)，那就把第 2 个参数传入一个对象即可。



<br>

**关于载荷的补充：**

载荷，英文单词为 payload，实际上载荷是 网络传输 中的一个概念。

例如我们无论以 POST 还是 GET 方式发起一个网络请求，那么我们都可以携带一些数据，例如提交某个表单。

这些数据就是此次网络请求的 载荷。

Vuex 借用了这个名词，在 Vuex 官方文档中，把每次修改仓库中的数据携带的参数 称呼为 载荷。



<br>

**引申出来的另外一种修改情景：**

既然第 2 个参数可以是任意类型，那么我们可以定义一个修改函数，让这个修改函数可以一次修改 N 个不同的数据状态。

```
const store = new Vuex.Store({
    state:{
        name:puxiao,
        age:34
    },
    mutations:{
        setMe(state,obj){
            state.name = obj.name
            state.age = obje.age
        }
    }
})
```



<br>

**另外一种提交风格：**

上面讲到当触发一个修改时，如果携带的载荷(参数)是一个对象，那么代码为：

```
this.$store.commit('setMe',{ name:'ypx', age:18 })
```

Vuex 还只是另外一种修改触发形式，即 commit 的参数改为一个包含 type 属性的对象。

```
this.$store.commit({
    type:'xxx',
    name:'ypx',
    age:18
})
```

> 这看起来更像是抛出一个 “Event” 事件实例了。



<br>

**修改原有数据状态的结构：**

假设我们在仓库数据集中定义以下的数据：

```
count store = new Vuex.Store({
    state:{
        person:{
            name:'puxiao',
            age:18
        }
    }
})
```

假设在项目运行过程中，我们希望给 person 新增一个 `sex` 的属性，那又该如何做呢？

首先明确一点 Vuex 中的数据状态 和 组件 data 中的数据状态，他们面对这种要求时，遵循的原则是相同的。

```
//错误的方式：直接给从未定义过的属性赋值
store.state.person.sex = true
```

```
//正确的方式：通过展开运算符 添加新的属性
stroe.state.person = {...}
```

<br>

或者通过全局 Vue 的 .set() 来修改

> 因为 Vuex 实际上是针对 Vue 内部进行了数据状态的绑定，所以修改 Vue 也可以间接修改 Vuex

```
Vue.set(person,'sex',true)
```

> 但是请注意，这种修改针对的是全局 Vue，而不是具体的 Vue 实例，所以慎用这种方式。



<br>

**使用常量来代替 mutation 函数名：**

我们知道 mutation 函数名 实际上对应的是修改事件的类型(type)，那么可以借助 ES2015 的一些特性，让我们将需要定义的 mutation 函数名作为常量值(字符串类型)定义在其他地方。

对于一些大型项目而言，这样做法好处很多：

1. 可以提前统一定义所有的修改函数名
2. 在定义和调用时，都可以使用常量，VSCode 都会有代码提示和自动填充，避免拼写错误

代码示例：

```
//mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'
export const PersonMutation = {
    SET_NAME:'PERSON_SET_NAME',
    SET_AGE:'PERSON_SET_AGE'
}

//store.js
import { SOME_MUTATION, PeronMutation } from './mutation_types.js'
const store = new Vuex.Stroe({
    state:{ ... },
    mutations:{
        [SOME_MUTATION](state){ ... },
        [PersonMutation.SET_NAME](state){ ... },
        [PersonMutation.SET_AGE](state){ ... },
    }
})

//Vue 组件
this.$stroe.commit(PersonMutation.SET_AGE, 18)
```



<br>

**关于mutation函数的补充说明：**

在我们上面所有的示例代码中，mutation 函数内部都是一个 同步命令，例如：

```
mutations:{
    setName(state){
        state.name = xxx
    }
}
```

如果把上面的函数内容，修改成一个异步的函数，会怎样？

```
mutations:{
    setName(state){
        xxx.xx(()=>{ ... }) //这行代码产生一个异步的操作
    }
}
```

答案是：Vuex 将无法再正确跟踪到相应的数据状态。

**结论：所有的  mutation 函数内部必须是同步的代码，不可以包含异步代码。**

> 那假设我的需求就是想异步修改呢？mutations 是无法满足，这就需要使用到接下来讲解的 action。



<br>

#### actions：下达操作数据状态的命令

前面讲到 mutation 和 store.commit()，你可以把它看作是：下达并立即执行完命令。

Vuex Stroe 还可以添加 action，你可以把它看作是：下达并立即开始执行命令。

再次强调一遍：

1. mutation：是下达并  "立即执行完"，必须是同步的操作代码，不可以是异步完成的

2. action：是下达并 "立即开始执行"，只是强调立即开始执行，并不强调必须立即执行完成。

   因此 action 中的代码既可以是 同步的，也可以是 异步的。



<br>

**最简单的 action 示例**

```
const store = new Vuex.Store({
    state:{ count:0 },
    mutations:{
        increment(state){
            state.count++
        }
    },
    actions:{
        increment(context){
            context.commit('increment')
        }
    }
})

//触发 action
store.dispatch('increment')
```

从这个最简单的代码示例，我们可以看出：

1. 某一个 action 应该写在 actions 属性中

2. 为了便于互相对应，mutations 和 actions 中的某个 “命令” 名字可以完全相同

   > 这是一句废话，因为两个 increment 本身就不在一个对象中，当然名字是不冲突的。

3. action 函数中对应的参数为 context，而不是 state

   > 虽然没有 TypeScript 明确定义的类型，但是从字面上我们看出 context 应该指的是 “作用对象” 

4. 在 action 函数内部，我们并没有直接操作修改 count，而是通过调用 mutaion 间接触发了 count 的修改

   > 这个示例中，我们的 action 其结果是 “同步方式立即执行完成”

5. 当我们需要触发 action 时，代码为：`store.dispatch('increment')`

   > 触发 mutation 我们使用 `store.commit('increment')`



<br>

**关于参数 context 的补充：**

上面代码中我们使用了 context.commit() 这个函数。实际上 context 还包含以下可用属性：

1. context.state：获取到 数据集
2. context.getters：获取到衍生数据集

在实际的代码使用中，为了方便，我们可以采用解构对象的方式来设置代码：

```
actions:{
    increment({commit,state,getters}){
        commit( ... ),
        state.xxx,
        getters.xxx
    }
}
```



<br>

**关于action与mutation的相通之处：**

实际上 action 和 mutation 在触发时携带载荷(参数)、甚至是将 “事件(mutation)或命令(action)” 的名字使用常量来定义，这些用法二者完全相同。

例如：

```
store.dispatch('increment',3)

//

store.dispatch({
    type:'increment',
    value: 3
})
```



<br>

**mapActions：**

在学习 state、getters 时讲到，如果在组件中为了简化 this.$store.xxx.xx，可以使用辅助函数，在组件内定义某个映射。

> state 对应的是 mapState、getters 对应的是 mapGetters
>
> 实际上 mutations 也对应有 mapMutations，只不过是前面没有讲解而已，具体可参考 Vuex 官方 API

同样 actions 也对应有辅助函数 mapActions，用于将组件内的相关映射。

```
exoprt default {
    methods:{
        ...mapActions{
            add:'increment' // 将 this.add() 映射为 this.$store.dispatch('increment')
        }
    }
}
```

请注意：

1. mapState、mapGetters 添加到组件的 computed 中
2. mapMutations、mapAction 添加到组件的 methods 中



<br>

**将action设置为异步：**

上面示例中我们在 action 内部采用同步的方式直接触发了 mutation，如果都写成同步那 action 也没有必要存在了，真正的用法是将 action 内部写成异步代码。

例如，当命令下达后 1 秒后才真正开始触发 mutation ：

```
actions:{
    incrementAsync({commit}){
        setTimeout(()=>{
            commit('increment')
        },1000)
    }
}
```

请注意：尽管 actions 中的 action 是异步函数，但是我们无需在 actions 或 action 前面增加 async/await。



<br>

更加复杂的异步示例(提交购物车下单)：

```
actions:{
    checkout({commit,state}){
        const saveCartItems = [...state.cart.added] //我们先将购物车内容进行一次备份
        commit(types.CHECKOUT_REQUEST) //发出一条命令：清空购物车(我们先假定此时可以清空购物车)
        
        //假定我们有一个负责与后端提交购买的 shop 模块
        shop.buyProducts(
            products,
            ()=> commit(types.CHECKOUT_SUCCESS), //若成功，发出一条购买成功的命令
            ()=> commit(types.CHECKOUT_FAILURE) //若失败，发出一条购买失败的命令
        )
    }
}
```

在上面的例子中：

1. checkout 对应的命令内容中包含异步操作
2. 根据不同的操作过程和结果，在适当时刻触发不同的 mutation



<br>

你以为上面的例子就足够复杂了？

不，还可以更复杂。

1. 尽管知道某个 action 内容是异步的，那我如果想监控 action 异步执行完成呢？
2. 上面示例中 action 内部可以触发不同的 mutation，那是否 action 内部可以触发其他的 action 呢？
3. 如果 action 内部存在多个 异步的 action，又该怎样编写代码呢？



<br>

**组件监控某一个命令(action)的异步完成：**

每一个 action 命令会返回一个 Promise。

因此我们可以将之前的代码修改为：

```
store.dispatch('actionA').then( ()=>{ ... } )

//

cosnt promise = store.dispatch('actionA')
promise.then(()=>{ ... })
promise.catch(...)
```



<br>

**action 内部触其他 action：**

```
actions:{
    actionA(context){ ... },
    actionB({dispatch,commit}){
        return dispatch('actionA').then(()=>{
            commit('xxxMutation')
        })
    }
}
```

在上面代码可以看出：

1. actionB 内部可以触发 actionA
2. 但是你不能简单只是触发 actionA 就可以了，你还需要将 actionA 返回的异步 Promise 作为返回对象返回出去。只有这样对于外界而言 actionB  也才是一个异步的 Promise。

> 实际上就相当于将 actionA 的异步 Promise 作为 actionB 的对外内容。

<br>

如果你觉得这样写看着不够优雅，那么接下来就讲解优雅的书写方式。



<br>

**多个异步 action 的 async/await 写法：**

直接看代码：

```
//我们假定 getData() 和 getOtherData() 返回的是 Promise
//也就是说 actionA 和 actionB 实际执行的代码都一定是异步操作

actions:{
    async actionA({commit}){
        commit('gotData', await getData())
    },
    async actionB({dispatch,commit}){
        await dispatch('actionA') //等待 actionA 完成
        //... 你甚至可以在此处多写几个其他的 异步命令，但要在前面写上 await，以便按照顺序逐个异步执行
        commit('gotOtherData',await getOtherData())
    }
}
```



<br>

至此，关于命令 action 讲解完毕，实际开发中，会有更多错中复杂的使用套路。



<br>

#### modules：仓库的模块化组合

在前面的学习中，我们知道：

1. 一个 Vue 实例只能对应一个仓库(stroe)
2. 一个仓库只能对应一个数据集(状态树)

我们把所有的 state、getters、mutations、actions 都写在一起，这样长久之后 store.js 文件会原来越臃肿，不利于代码的组织和维护。

<br>

如同我们可以把 js 模块化，分散到不同的文件中一样，Vuex 也允许我们将一个 store 中的不同数据分散到不同的模块中。然后再通过 modules 将这些 子仓库 组合拼凑出整个大的仓库。

特别强调几点：

1. 每个子仓库需要是一个独立、完整的 “小仓库”，比如需要包含 state、mutations、actions 等。

2. 每个子仓库中还可以嵌套更小的 孙仓库

3. 最终所有的子仓库都汇聚到 总仓库中

4. 处于中仓库中的各个子仓库他们彼此之间都是互相独立的

   > 并不是说总仓库会把子仓库进行合并，而是会保持各个子仓库之间是相互独立的。这听上去特别像是借鉴了数据库设计中的套路，将原本一个庞大的总表 拆分成多个 子表单。



<br>

**代码示例：**

```
const moduleA = {
    state:{ ... },
    getters: { ... },
    mutations: { ... },
    actions:{ ... }
}

const moduleB = {
    state:{ ... },
    getters: { ... },
    mutations: { ... },
    actions:{ ... }
}

const store = new Vuex.Stroe({
    modules:{
        a:moduleA,
        b:moduleB
    }
})

//读取某个子仓库
store.state.a //对应得到 moduleA 这个子仓库
store.state.b //对应得到 moduleB 这个子仓库
```



<br>

**子仓库内的代码：**

对于每个子仓库中的 state、getters、mutations、actions 他们用法不变。

无论子仓库怎么嵌套，他们的 state 都是指本子仓库内的。

```
const moduleA = {
    state:{
        count:0
    },
    mutations:{
        addCount(state){
            state.count++
        }
    },
    actions:{
        addCount(context){
            context.state.count += 1
            context.commit('addCount')
        }
    }
}
```

在上面代码中 moduleA 中的 state 和 context 都是指本仓库的内容。



<br>

**子仓库获取总仓库中的数据**

对于子仓库中： 

1. gettes 中可用变量
2. action 的参数 context

他们又新增可用属性 rootState、rootGetters，用于访问总仓库中的 state

```
const moduleA = {
    getters:{
        mergeCount(state,rootState,rootGetters){
            return state.count + rootState.count + rootGetters.xxx
        }
    }
    actions:{
       doOther({state,rootState}){
           ...
       }
    }
}
```



<br>

**命名空间**

默认情况下，模块内的 action、mutation、getter 都是注册在全局命名空间中的。

> 对于子仓库，你可以把 “全局命名空间” 理解成 “当前子仓库内的全局命名空间”。

这样的结果就是 getter 或 mutation 或 action 都可以比较容易轻松访问到彼此。

假设现在要实现一个 超级复杂，更高级别的仓库封装，那么此时就需要使用到 命名空间 了。

说简单点，就是在同一个仓库中，再次划分出多个命名空间，然后在每个命名空间内设置独立的 孙仓库。

> 实际上我们之前使用 modules:{ a:moduleA, b:moduleB } 本身也是创建了 a 和 b 两个命名空间

示例代码：

```
const store = new Vuex.Stroe({
    modules:{
        one:{
            namespace: true, //明确当前 account 为一个独立的命名空间，也就是 子命名空间
            state:{ ... },
            mutations:{ ... },
            modules:{
                myPage:{
                    //这其实已经是 孙命名空间 的内容了
                    state:{ ... },
                    mutations:{ ... }
                }
            }
        },
        two:{ ... }
    }
})
```

上面的代码看着似乎很复杂，实际上它对应的是：

```
const my_page_store = { ... }

const one_store = { modules:{ myPage: my_page_store } }
const two_store = { ... }

const store = new Vuex.Stroe({
   modules:{
       one:one_store,
       two:two_store
   }
})
```

如果想访问 my_page_stroe 这个孙仓库，那么对应代码为：

```
store.one.myPage
```



<br>

**孙仓库中想访问总仓库：**

当触发一个命令并携带参数时，我们的代码为：

```
store.dispatch('xxx',xx)
```

> 第 1 个参数为 命令名称，第 2 个参数为携带载荷(数据)

Vuex 还允许我们添加第 3 个参数，一个包含 root 字段的对象：

```
store.dispatch('xxx',xxx,{ root:true })
```

假设子仓库中存在一个名为 "xxx" 的命令，而子仓库也存在这个命令。

那么通过添加第 3 个参数 { root: true } 可以 Vuex 明确知道你此次操作针对的是 根仓库(总仓库)。



<br>

**孙仓库中向全局中仓库添加命令**

Vuex 甚至允许你在孙仓库、子仓库的命令(action) 中向全局总仓库创建、添加命令。

> 别怀疑，就是真的可以这样做。

做法也很简单，就是将 孙仓库 或 子仓库 的命令(函数) 修改成 一个对象：

1. 该对象包含 root 属性，其值为 true
2. 该对象包含 handler 属性，其值就是之前的 命令函数

```
{
    ...
    modules:{
       xxx:{
           actions:{
               xxx:{
                  root:true,
                  handler(context){ ... }
               }
           }
       }
    }
}
```



<br>

当出现了很多 孙仓库 之后，为了简化命令，可以借助 mapActions 进行映射。

包括使用新的技巧：

```
methods:{
    ...mapAction('xxx/xx/xx',[
        'foo', //相当于把 this.foo 映射为 this['xxx/xx/xx/foo']()
        'bar'
    ])
}
```

> 请注意上述代码中的 `this['xxx/xx/xx/foo']()` 可不是指数组元素，而是指 this.xxx.xx.xx.foo()。



<br>

**动态注册(添加)子仓库：**

```
store.registerModule('newModule',{ ... })  //store.state.newModule
```

**动态注册(添加)孙仓库：**

```
store.registerModule(['myModule','newModule'],{ ... }) //store.state.myModule.newModule
```

**检查是否存在某子仓库：**

```
store.hasModule('moduleName')
```

**删除动态注册(添加)的子仓库：**

```
store.unregisterModule('moduleName')
```

> 注意，你只能删除通过 registerModule() 动态注册的子仓库，不可以(也做不到)删除在 js 文件中 store 原本创建的子仓库。



<br>

**模块重用：**

假设我们定义了一个 子仓库 的对象，例如：

```
const myModule = { ... }
```

假设在不同的子仓库中，都希望把 myModule 当做他们的 子仓库。

> 此时 myModule 被看作是 孙仓库

如果使用引用 `modules:{ xx: myModule }`，那么所有引用到 myModule 的地方他们实际上对应的是同一个对象(myModule)，改变其中一个就会影响所有的 “myModule”。

解决办法：使用函数来声明(引用)

```
modules: ()=> ({ xx: myModule })
```

> 这个套路和多个 state 引用同一个 对象的解决方式相似



<br>

**常见的目录结构：**

除了 子仓库，通常还会将 总仓库中的 actions、mutations 也进行拆分成不同的文件。

```
src
  store/
     index.js 总仓库入口文件
     actions.js 总仓库中 根级别的 action
     mutations.js 总参股中 根级别的 mutation
     modules/ 用于存放子仓库的目录
        aa.js 子仓库 a
        bb.js 子仓库 b
        ...
```



<br>

**关于Vuex的其他高级用法：**

例如 插件(plugins)、快照、日志(logger)、严格模式、测试 等等高级用法，这些对于我们初学者而言，暂时触及不到。

> 等实际使用 Vue 开发项目时，遇到了再学习吧。



<br>

更多 Vuex 用法可查阅其官网或 API。

https://vuex.vuejs.org/zh/guide/plugins.html

https://vuex.vuejs.org/zh/api/



<br>

**结束语：**

关于 Vue 的基础学习就到此为止。

在未来，或许还需要学习 Vue 生态中另外一个重要的环节：服务器渲染(ssr)

对应需要学习的是 vue-server-renderer



<br>

接下来，让把目前学到的：

1. vue基础知识
2. vue组件
3. vue router
4. vuex

用于实际当中吧。



<br>

我用了 7 天的时间完成了上面这些学习。

希望看到这段文字的你，也会成为 Vue 高手。

一起加油！

2021.10.15