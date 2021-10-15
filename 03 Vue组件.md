# Vue组件

#### 初探Vue组件

假设我们使用 React ，那么我们可以在 .jsx 文件中直接在 return() 中定义组件。

Vue 也支持 jsx 这种形式，但是我们先学习最基础的，通过 JS 来定义组件的方式：

`Vue.component('name',{ ... })`



<br>

Vue.component() 函数用于创建自定义组件。

第一个参数为该自定义组件的名字。

> 请注意名字的唯一性

第二个为配置该组件的具体内容，通常我们需要配置 2 个属性：props 和 template

1. props：为一个数组，该数组中的元素即为该组件从父级获取的配置变量

   > 对于 React 组件而言 props 为一个对象

2. template：组件的具体字面内容。

   > 请注意是 字面内容，而不像 React 那样直接就是 标签。

举例：

```
Vue.component('todo-item',{
    props:['todo'],
    template:`<li>{{todo.text}}</li>`
})
```

代码分析：

1. 我们将该组件命名为 todo-item，那么将来我们就可以使用 `<todo-item></todo-item>` 这个自定义标签来使用该组件。

2. 表示组件接收参数的 props 的值为一个数组，在上面代码中该数组仅包含一个元素 'todo'，请记得这个 'todo'   相当于该组件对外暴露的自定义参数(属性)名。

   > 我们目前将 props 设置为数组，在本文后面，我们还可以将 props 设置为对象，从而可以明确定义 props 的结构类型。

3. template 的内容即该组件的实际内容的 字面量。在该字面量中遵循 Vue 基础语法，并且 在字面量中，可以将 todo 视为该组件内可访问使用的变量。

使用该组件：

```
var app = new Vue({
    ...
    data:{
        list:[
            {text: 'React'},
            {text: 'Vue'},
            {text: 'TypeScript'}
        ]
    }
})

//

<ul>
    <todo-item v-for="（item,index) in list" v-bind:todo="item" v-bind:key="index" ></todo-item>
</ul>
```

请注意上述代码中的 v-bind:todo="item"，我们将 v-for 得到的 item 与 props 中的 todo 进行绑定。

> Vue 就是这么神奇，需要一定时间来适应这种 “便捷” 的操作语法。



<br>

#### 组件抛出自定义事件

在组件内部，可以通过 `$emit` 来实现抛出自定义事件。

例如：

```
Vue.component('todo-item',{
    props:['todo'],
    template:`<li>
        {{todo.text}}
        <button v-on:click="$emit('item-del')">del</button>
    </li>`
})
```

在上述代码中，当点击 del 按钮后，通过 `$emit(item-del)` 就可以抛出一个名为 item-del 的自定义事件。

在父级中，可以添加 item-del 的事件监听处理：

```
<ul>
    <todo-item 
        v-for="（item,index) in list" 
        v-bind:todo="item" 
        v-bind:key="index"
        v-on:item-del="handleDel()"
        >
    </todo-item>
</ul>
```

请注意，在上面的代码中，我们仅仅是触发和监听了自定义事件，但是对于这个示例中，我们还需要告诉父级，在  item-del 事件中携带参数，以便父级可以正确删除当前项。

Vue 的语法规定，我们可以将自定义事件需要携带的参数作为 $emit() 的第 2 个参数。

我们修改一下上面的代码：

```
var app = new Vue({
    ...
    data:{
        list:[
            {id:0, text: 'React'},
            {id:1, text: 'Vue'},
            {id:2，text: 'TypeScript'}
        ]
    },
    methods:{
        handleDel: function(id){
            const index = this.list.findIndex(item => item.id === id)
            if(index !== -1)
            this.list.splice(index,1)
        }
    }
})

Vue.component('todo-item',{
    props:['todo'],
    template:`<li>
        {{todo.text}}
        <button v-on:click="$emit('item-del',todo.id)">del</button>
    </li>`
})

//

<ul>
    <todo-item 
        v-for="（item,index) in list" 
        v-bind:todo="item" 
        v-bind:key="index"
        v-on:item-del="handleDel(id)"
        >
    </todo-item>
</ul>
```



<br>

与 React 组件内部作用域不通过，Vue 组件内部可以访问父级中所有的 变量。

> 这一点确确实实可能会容易造成混乱。
>
> 这也是 Vue 为了提升数据绑定便利 所作出的让步。



<br>

自此，关于 Vue 组件的基础知识已经学习完成，接下来进一步深入学习。



<br>

#### 深入了解学习组件

针对组件的细节，进一步深入学习。



**组件名：**

通常一个组件名字应该和实际用途有关联，让人通过名字即可大致了解组件的功能和用途。

组件名有 2 种风格：

1. 全小写+横杠(kebab-case)：例如 my-component-name
2. 首字母大写(PascalCase)：例如 MyComponentName

> 请注意，如果你使用的是原生引入 vue.js 的方式，那么一定要使用第一种。
>
> 如果你使用 单个文件 .vue 这种形式，那么推荐使用第二种命名方式。关于 .vue 这种文件形式，我们会在后面讲解。



ponent('xxx', { ... }) 这种形式创建的组件为全局组件，即在任何地方(哪怕是其他组件内部)都可以使用。

同时在该组件内部也可以访问全局变量。

全局组件的缺点：即使当前项目并未实际使用到该组件，但该组件的代码也会被打包进去。

<br>

这时我们就需要 局部组件。

Vue 创建局部组件的方式，实际上就是将原本的 Vue.component() 进行了拆解和重组。

1. 对外使用 JS 创建一个组件
2. 将该组件添加的 Vue 实例的配置项 components 中

举例：

```
//全局组件
Vue.component('component-a', { ... })

//局部组件
var ComponentA = { ... }

var app = new Vue({
    el:'#app',
    components:{
        'component-a': ComponentA
    }
})
```

> 假设我们在 Vue 初始化时并没有引入 ComponentA，那么将来项目打包时也不会将 ComponentA 的代码打包进去。

假设并不是 Vue 初始化是要引入 ComponentA，而是另外一个组件 Xxxx 要引入 ComponentA，那么引入方式和 Vue 初始化类似，也是通过 components 这个配置属性来引入的。

举例：

```
var ComponentA = { ... }

// import ComponentA from './xxx'

var ComponentB = {
    components:{
        'component-a': ComponentA
    }
}
```



<br>

**组件参数Props：**

在 Vue 基础部分我们代码中演示的是 props 为一个数组，数组中的元素为 变量名。

例如：props:['xxx']，这里要强调一点，变量名要遵循 驼峰命名原则，例如：props:['myName']。

在使用该组件，设置其属性时，需要转成 小写加横杠的 标签名，即： `<my-component my-name="xxx" >`



<br>

实际上 props 也可以不是数组，而是对象，并且直接定义该 props 中各变量名和值类型。

例如：

```
props:{
    myName:String
}
```

> 请注意，这里的类型定义采用的是该类型的构造函数，即首字母为大写风格和 JSDoc 类似。
>
> 这一点和 TypeScript 不同，假设类型为字符串即 String，而不是 TypeScript 中的 string。

以此类推，我们还可以将 props 拓展为：

```
props:{
    myName: String,
    age: Number,
    myFun: Function,
    arr: Array,
    arr2:[String, Number]
    other: Ojbect
}
```

> props 中属性值类型的定义，几乎和 JSDoc 相同

请注意，props 属性值类型还支持你自定义的 JS 类，例如：

```
function Person(name,age){
    this.name = name,
    this.age = age
}

props:{
   me:Person
}
```



<br>

Vue 组件嵌套组件会产生一个 属性叠加的 效果，如果不想让当前属性继承父类中的任何属性，则可以通过设置 `inheritAttrs: false` 的形式来明确拒绝。

```
Vue.component('base-input',{
    inheritAttrs: false,
    props:...
})
```



<br>

**自定义事件**

在 Vue 基础部分我们已经对 自定义事件 做过简单的学习。

通过 `$emit('xxx')` 可以抛出自定义事件，事件名(事件类型)为 xxx。

这里强调一点：事件名不要使用驼峰命名，而应该始终采用 小写+横杠 的形式。

> 小写+横杠 这种命名方式对应的英文称呼为：kebab-case



<br>

**自定义组件上的 v-model**

我们知道在原生 DOM 标签中 v-model 用于双向绑定某变量。对于一般的原生 DOM 标签而言，通常 v-model 隐含对应的是该标签的 value 属性值。例如：

```
<input v-model="name" >
```

<br>

对于 复选框等类型而言，其原生 value 有可能有特殊用途，为了避免 v-model 与之冲突，我们可以在自定义组件中添加 model 配置项来进行 “内部区分”。

先看一段代码：

```
Vue.component('base-component',{
    model:{
        prop: 'checked',
        event: 'change'
    },
    props:{
        checked: Boolean
    },
    template:`
        <input 
            type='checkbox' 
            v-bind:checked='checked' 
            v-on:change='$emit("change", $event.target.checked)' >
    `
})
```

在上面代码中，我们在自定义组件中添加了 model 配置项，该配置项表达 2 个含义：

1. `prop: 'checked'`：监控一个名为 checked 的 prop
2. `event: 'change'`：添加 change 事件的侦听，当组件内部监控到 change 事件时，将 change 事件携带的参数赋值给 checked 这个 prop。

这样一番操作后，对于组件使用者而言，v-mode 不需要考虑 value 这个原生 DOM 标签属性了。

```
<base-checkbox v-model="myCheck"></base-checkbox>
```



<br>

自定义组件的 model 配置项实际上是一种简约式的内部事件处理函数的声明处理。

> 暂时不理解没有关系，以后用的多了，自然就理解了。



<br>

#### 深入骨髓般的原生事件监听

试想一下这个组件：

1. 表面上，对于使用者而言 它 看上去应该是一个输入框
2. 实际上，在组件内部，它的根标签是 `<label>` ，根标签内部是一个 `<input>`

如果作为使用者想对这个组件的原生 “input" 事件进行监听，那么你需要使用到 `$listeners` 这个语法。

> 我个人不太赞同和认可这种做法。
>
> 组件就是组件，就应该是封装的，使用者不应该过多去关注细节，若父类需要什么直接让组件内部提供即可。
>
> 在这个场景中，我认为组件完全可以 抛出自定义事件，而没有必要把自定义事件伪装成原生事件。



<br>

具体用法和套路：

1. 在组件内部的 `<input>` 标签中，添加 v-on="inputListeners" 的属性配置

   > 还有 v-bind="$attrs"  v-bind:value="value"

2. 这个 inputListeners 实际上是在 computed 中创建的一个 衍生变量

3. 在定义衍生变量 inputListeners 的代码中，通过 “某些套路” 将组件内部的 input 事件对外转化为名称和 input 相同的一个自定义事件

4. 最终，使用者在使用该组件时，可以 “以为” 自己添加的是 input 原生事件

5. 提醒：组件内部为了避免使用者可以直接监听原生事件，所以要配置 inheritAttrs:false

整个过程概括起来就是：**组件内部将 原生事件伪装成名称相同的自定义事件**

<br>

具体代码套路如下：

```
Vue.component('base-input',{
    inheritAttrs:false,
    props:{
        label:String,
        value:String
    },
    computed:{
        inputListeners: function(){
            var vm = this
            return Object.assign({}, this.$listeners, {
                input: function(event){
                    vm.$emit('input',event.target.value)
                }
            })
        }
    },
    template:`
        <label>
            {{label}}
            <input v-bind="$attrs" v-bind:value="value" v-on="inputListeners">
        </label>
    `
})
```

经过上面一番操作之后，我们这个自定义组件 `<base-component>` 对于使用者而言，和普通的 `<input>` 标签没有什么区别了，因为它们拥有相同的 “对外特征”。



<br>

#### 组件prop的双向绑定

当组件使用者向组件传递一个 prop 参数后，组件内部希望修改这个 prop 的值，最简单的做法就是组件抛出对应的修改事件，并携带 prop 的新值作为参数，让使用者捕获该事件并在使用者的范围内对 prop 进行修改。

> 所谓的 “使用者的范围内” 是指以下 2 种情况：
>
> 1. Vue 配置项中的 事件处理
> 2. 直接在添加事件侦听时直接进行处理

当 prop 被修改之后，会触发组件的更新，从而达到了 “双向都更新修改 prop” 的目的。



<br>

关于 “同步更新” 自定义事件名的补充说明：

我们很自然想到这个事件名可以写成：update-xxx(小写加横行)，但是针对这类情况，Vue 推荐使用 update + : + xxx 这种格式，即 `update:xxx`，也就是说不使用 横杠而是 冒号。

> 实际中究竟采用横杠，还是 冒号，执行决定。



<br>

**同步操作的简写形式**

针对以上这种 “同步” 情况，Vue 为我们提供了一个 `.sync` 的语法，以便简化 "使用者的范围内" 这一步操作。

> sync 是英文单词 synchronize 的缩写，意思为 “同步”

```
//为简化之前的使用者代码
<text-document v-bind="doc" v-on:update:doc="doc=$event"></text-document>

//使用 .sync 之后的简化代码
<text-document v-bind.sync="doc" ></text-document>
```

可以看出 `.sync` 实际上简化了 2 个环节：

1. 绑定数据
2. 添加数据变化请求事件侦听，以及修改该值的代码

sync 将上述 2 个过程合并成 1 个代码：v-bind.sync="doc"



<br>

请注意 .sync 仅仅是用于简化 使用者 的代码，对于组件内部而言是没有变化的，依然需要抛出相应的事件。



<br>

**特别强调：**

在我们简化后的 同步代码 `v-bind.sync="doc"` 中 "doc" 只可以这样写，不允许针对 "doc" 再增加其他表达式。

> 如果是 v-bind:doc="doc"，那么这个代码中的 "doc" 是可以添加其他表达式的，例如修改为：
>
> v-bind:doc="doc + 'aaa' "



<br>

#### 保持组件状态

当发生数据更新时，Vue 会计算并更新当前页面中的 DOM 元素。

对于有些组件而言，当你希望 “即使此刻它不被渲染，但也希望下次渲染时能保持之前的状态”。

这个时候，你就需要 Vue 提供的一个标签 `<keep-aliv>`，将需要保持状态的组件放入到该标签中。

例如：

```
<keep-alive>
  <component ></component>
</keep-alive>
```



<br>

#### 过渡动画

当某个组件 进入(被添加) 或 离开(被删除) 时，可以添加一些过渡动画。

Vue 内置的 `<transition>` 标签就是用于创建过渡动画的。

简单来说就是把需要有过渡动画的组件 放入到 `<transition>` 标签中。

同时 Vue 也支持通过 JS 来添加这些过渡动画过程的侦听。

具体用法可参见 Vue 官网中关于 过度动画的部分。



<br>

#### 状态过渡

上面讲到的是 过渡动画，但是还有另外一种情况：状态过渡

例如一个数字由  10 变更为 29，那么默认情况下网页是直接将数字进行更改。

Vue 提供一些状态过渡的套路，借助第三方库，可以实现将 数字有 10 在规定时间内逐渐不断 +1 直到变为 29。

这些第三方过渡动画库为：tween.js、color-js 等等。



<br>

#### 自定义指令

像 v-on、v-if 这些都是 Vue 内置的指令，我们还可以自己去创建一些自定义指令。

通过 `Vue.directive('name',{ ... })` 这种形式来创建命令。

1. 第一个参数：指令名称，例如 focus，请注意在使用该命令时应该在前面加上 v-，即 v-focus
2. 第二个参数：指令具体内容，包括 何时触发、触发执行的函数

举例：

```
Vue.directive('foucs',{
    inserted: function(el){
        el.focus()
    }
})

//使用该指令
<input v-foucs >
```

代码解析：

1. 上面代码中，我们创建了一个名为 focus 的命令(指令)
2. 指令的内容为 当 "inserted" 时触发一个函数 function(el){ ... }



<br>

**何时触发？**

Vue 提供了 3 种触发场景：

1. bind：仅调用(触发)一次，即第一次绑定数据到该元素上时调用。

2. inserted：被绑定的元素插入到父节点时调用 (并不限制次数)

   > 请注意这里仅强调父节点存在，并不强调父节点必须已插入到 实际 DOM 中

3. update：所在组件发生数据更新时调用 (并不限制次数)

4. componentUpdated：所在组件以及包含的子组件全部更新完成后调用 (并不限制次数)

5. unbind：仅调用(触发)一次，即指令与元素解绑时调用



<br>

**触发函数的参数详情**

在上面示例代码中，我们看到函数 function(el)，其中 el 即该元素(标签) 对应的真实 DOM 标签元素。

除了 el 这个参数，Vue 还提供其他几个参数，可供你在需要是使用。

1. binding：一个对象，包含以下属性

   1. name：指令明，注意不包含前缀 v-

   2. value：指令绑定的值

      在上面示例中指令并未绑定任何有效值，但是指令是可以绑定值的，类似 v-focus="xxx" 中 xxx 即指令绑定的值，请注意 xxx 可以是有效的 JS 运算结果

   3. oldValue：指令前一个值

   4. expression：指令值的字符串(原始)值，假设 v-focus="1+2"，那么 value 为 3，expressin 为原始的字符串值，即 "1+2"

   5. arg：传给指令的参数，例如 v-focus:aaa="xxx"，其中 aaa 即为指令参数

   6. modifiers：包含修饰符的对象，例如 v-focus.aa.bb="xxx"，那么 “aa.bb” 会被转换为一个对象 {aa:true, bb:true}，这个对象就是 modifiers 对应的值。

2. vnode：组件以及子组件对应的 虚拟 DOM 节点 

3. oldVnode：上一个虚拟节点，请注意该参数仅在 update 和 componentUpdated 中出现、有值。

特别强调：除了 el 参数外 ，其他参数都只能是 读 的模式，不要尝试去修改它们的值。



<br>

假设你希望在 bind 和 update 时都触发某个相同指令，那么是不需要写 2 遍的，同时可以把 directive() 函数第二个参数由 对象改为一个函数。

你可以把它们简写(合并)写成：

```
Vue.directive('xxx',function(el,binding){
    el.xxx = binding.xx
})
```



<br>

#### 混入

当我们想实例化一个 Vue 时，需要提供一些配置选项。

例如：

1. 钩子函数：created
2. 自定义函数：methods
3. 引用组件：components
4. 自定义命令：directives
5. ...

假设有多个 实例化的 Vue，且它们拥有相同的某些 钩子函数 或 自定义函数，那难道要每一个都添加一遍吗？

<br>

Vue 提供了一个 “混入(mixin)” 的概念和语法，可以帮助我们将那些相同的 钩子函数或自定义函数 在外部统一声明定义，然后 “混入” 到每一个 Vue 实例中。

```
//先定义一个对象，包含 钩子函数和自定义函数
var myMixin = {
    created:function(){ ... },
    methods:{
        hello:function() { ... }
    }
}

//将 myMixin 混入到我们需要实例化的Vue或自定义组件中
var app = new Vue({
   mixins:[mixin],
   ...
})

var app2 = new Vue({
   mixins:[mixin],
   ...
})
```



<br>

上面的 “混入” 只是针对具体某个 Vue 实例，实际上还可以有更逆天的操作：将定义的 myMixin 混入到全局所有(包括 Vue 实例)的组件中。

需要使用到的函数是 `Vue.minxi({ ... })`

```
Vue.mixin({
    created:function(){ ... },
    methods:{
        hello:function(){ ... }
    }
})
```

> 注意，一定要不用或慎用 全局混入。



<br>

**关于 混入(mixin) 的优先级说明：**

假设我们定义的  myMixin 中和被混入组件中有相同的名称，也就是说名称冲突了，那会发生什么事情呢？

1. 对于 钩子函数来说，Vue 会将 它们两个都进行保存，并执行。顺序是：先执行 myMixin 中的钩子函数，然后再执行 组件内部的同名钩子函数。
2. 出钩子函数外，其他的(例如自定义方法，组件，命令)，Vue 会仅保留和执行 组件内部定义的那个值。



<br>

#### 过滤器

在 Vue 基础部分，我们提到可以针对某些 绑定的变量进行简单的过滤，例如：

1. .number：转换为数字
2. .mirt：去掉首尾空格

假设你需要自定义一些过滤器，那么就需要用到 Vue 提供的 `filters` 语法。

实现起来需要  2 个步骤：

1. 在组件内部 或 通过 Vue.filter() 来创建过滤函数
2. 在 v-bind 变量值时，除了值的后面加上 竖线和过滤函数名

举例：

```
//全局定义过滤器
Vue.filters('filter-name',function(value){
    if(!value) return ''
    value = xxxxx
    return value
})

//组件内部定义过滤器
var component = {
    filters:{
        filterName:function(value){
            ...
        }
    }
}

//绑定数据时使用过滤器
<div v-bind:xx="xxx | filterName"></div>
```

> 在上面代码中，绑定的值为 `xxx | filterName`，那么就意味着 xxx 最终实际值为 filterName(xxx) 函数的过滤之后的值。



<br>

还可以创建多个 自定义过滤器，然后多个过滤器 同时组合。

例如：

```
<div v-bind:xx="xxx | filterA | filterB | filterC"></div>
```

请注意，当存在多个过滤器时，他们执行的顺序是 从左向右 依次执行，即第一个先执行 filterA，然后将结果传递个 filterB，最终 filterC 的计算结果才是 xxx 的最终值。



<br>

#### 单个Vue文件组件

目前在上面所有的示例中，我们是把所有的组件和 JS 都写在了一起(一个文件中)。

项目稍微一复杂起来，肯定就需要对各个组件进行拆分，拆分到不同的文件中。

对于 Vue 而言，推荐将一个组件对应成一个 .vue 文件，该文件内容为 3 个部分：

1. 由  `<template>` 标签包裹的 组件标签
2. 由 `<script>` 标签包裹的 JS 代码
3. 由 `<style>` 标签包裹的 css 样式

举例：

```
//xxx.vue

<template>
    <p>{{ name }}</p>
<template>

<script>
    module.exports = {
        data: function(){
            return {
                name:'puxiao'
            }
        }
    }
</scrpt>

<style scoped>
    p{
        font-size: 2em;
    }
<style>
```

> 请注意在 样式标签中添加有 `scoped`，表示这些样式仅对当前组件生效。



<br>

对于上述代码中的 `<script>`  和 `<style>` 还支持引用外部文件，即修改成：

```
//xxx.vue

<template>
    <p>{{ name }}</p>
<template>

<script src="./xxx.js"></scrpt>
<style src="./xxx.css"><style>
```

但是这种做法并不推荐，因为这容易造成文件管理、互相引用 异常混乱。



<br>

**关于 .vue 中 JS 的补充：**

在 .vue 文件中的 `<script>` 标签内，实际上 Vue 组件配置项中的 data 可以省略，直接将原本 data 中的需要定义的变量写在 `<script>` 内。

例如：

```
//xxx.vue

<template>
    ...
<template>

<script>
    name:'puxiao'
</scrpt>

<style scoped>
    ...
<style>
```



<br>

#### 组件的其他知识点

例如比较 “重要” 的 插槽、动态组件、异步组件、各种边界处理。

依照我目前对 Vue 的掌握情况，我很难去理解为什么要有这些，他们存在的意义是什么？

我认为他们将 Vue 项目变得如此复杂，所以这里就不过多阐述和学习了。



<br>

除此之外，还应该完整看一遍以下内容：

Vue API：https://cn.vuejs.org/v2/api/

Vue 代码风格指南：https://cn.vuejs.org/v2/style-guide/



<br>

暂且跳过这部分，接下来开始学习使用 vue cli3 来创建工程化的 vue 项目。