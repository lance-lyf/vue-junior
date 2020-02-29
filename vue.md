# Vue 复习总结
## 数据属性
> 数据驱动视图，视图响应数据的变化。
>1. 选项对象属性data中的数据会加入响应系统中，当这些属性的值发生改变的时候，视图也随之改变。
> 
>2. Object.freeze会阻止修改现有的属性，也就是说，响应系统无法随着选项对象中data的值得所有属性值得变化而变化。
>3. v-once 该指令会让应用节点（包括此节点）下的视图不会随着随之响应。
## 实例属性
> 除了数据属性，vue实例还提供了一些有用的实例属性与方法，他们都有$，以便于与用户定义的属性区分开来。
>> 例如 $watch 是一个实例的方法。
        
        vm.$watch('a',function(newValue,oldValue){
            这个回调将在vm.a的数据放生变化后调用。
        });
## 一、模板语法
> vue.js使用基于html的模板语法，允许开发者声明式地将dom绑定至底层Vue实例的数据，所有vue.js的模板都是合法的HTML，所以能被遵循规范的浏览器和html解析器解析。

> 在底层的实现上，vue将模板编译成虚拟dom渲染函数，结合响应系统，vue能够智能地计算出最少需要重新渲染多少组件，并把dom操作次数减少到最小。

>如果你熟悉虚拟dom并且偏爱js原始力量，你也可不用模板，**直接写渲染函数**，使用可选用的jsx语法。

### 插值 
#### 文本
>数据绑定最常见的形式就是使用"mustache"语法（双大括号）的文本插值：    
    
    <span>Message: {{msg}}</span>

#### 原始Html
> 双大括号会将数据解析为普通文本，而非html代码。为了输出真正的html，你需要使用v-html指令。**注：你的站点上动态渲染的任意html可能会非常危险，因为它很容易导致xss攻击。请只对可信内容使用html插值，绝不要对用户提供的内容使用插值**

#### Attribute
> Mustache语法不能作用在HTML attribute 上，遇到这种情况应该用v-bind指令

    <div v-bind:id="dymancId></div>
> 对于布尔值attribute(他们只要存在就意味着值为true), v-bind工作起来略有不同，如下：

    <button v-bind:disabled='isButtonDisabled'>Button<button>
> 如果isButtonDissabled的值是null、undefined、false,则disabled attribute甚至不会被包含在渲染出来的<button>元素中
#### js表达式
> 迄今为止，在我们的模板中，我们一直都只绑定简单的属性键值，但实际上，对于所有的数据绑定，Vue.js都提供了完全的js表达式的支持。**注：只对js表达式的支持，不对js语句的支持（包括if的流控制)**

>**模板表达式都放在沙盒中",只能访问全局变量的一个白名单，如 Math 和 Date，你不应该在模板表达式中试图访问用户定义的全局变量。**


## 二、指令
>指令是带有 v-前缀的特殊的attribute,指令attribute的值预期是个单个的js表达式（v-for是例外情况，稍后我们再讨论）。指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于Dom。
    
    <p v-if="seen"> 现在你看到我了</p>//根据seen的值得真假插入和移除<p>元素

### 参数
> 一些指令能够接受一个参数，在指令后面用：表示。例如 v-bind 指令可用于响应式地更新html attribute：

    <a v-bind:href="url">...</a>
> 在这里href是参数，告知v-bind指令将改元素的href attribute 与表达式url的值绑定。

### 动态参数
    从v2.6.0开始，可以用方括号括起来的js表达式作为一个指令的参数：

    <a v-bind:[attributeName ] = 'url'>...</a>
>这里的attributeName 会被作为一个js表达式进行动态求值，求得的值将作为最终的参数来使用。例如，如果你的Vue实例有一个data属性 attribute，其值为'href'，那么这个绑定等价于'v-bind:href'

#### 对动态参数值得约束
>动态参数表达式有一些语法约束，因为某些字符，如空格和引导，放在HTML attribute名里是无效的。

    <a v-bind:['foo'+bar]="value">...</a>
>变通的办法是使用没有空格或引号的表达式，或者用计算属性替代这种表达式。
#### 对动态参数表达式的约束
>在dom中使用模板的时，还需要避免attribute中的键名有大写字母，因为浏览器会把attribute名全部转换为小写。
    <a v-bind:[someAttr]="value">...</a>
### 修饰符
>修饰符是以半角句号.指明的特殊后缀，用于指出一个指令应该以特殊的方式绑定。如.prevent修饰符告诉v-on指令对于处罚的事件调用event.preventDefault();
    
    <form v-on:submit.prevent="onSubmit">...</form>//阻止默认提交后的重载
### 缩写
>v- 前缀作为一种视觉提示，用来识别模板中Vue特定的Attribute。当你在使用Vue.js为先用标签添加动态行为的，v-前缀会很有帮助，然而对于一些频繁用到的指令来说，就会感觉到使用繁琐。同时，在构建由Vue管理所有的模板的单页面应用程序(SPA-single page application)时，v-前缀也就变得没那么重要了。因此，Vue为v-bind和v-on这两个是常用的指令,提供了特定简写:

#### v-bind缩写
    <!-- 完整语法 -->
    <a v-bind:href="url">...</a>
    <!-- 缩写语法 -->
    <a  :href="url">...</a>
    <!-- 动态参数缩写语法 -->
    <a :[key]="url">...</a>
#### v-on缩写
    <!-- 完整语法 -->
    <a v-on:click="url">...</a>
    <!-- 缩写语法 -->
    <a  @click="url">...</a>
    <!-- 动态参数缩写语法 -->
    <a @[event]="url">...</a>
## 三、计算属性与侦听器
### 计算属性
> 模板内的表达式非常便利，但是设计他们的初衷用于简单运算的。在模板中放入太多逻辑会让模板变得沉重而且不易维护。例如：

    <div id="exmple">{{message.split('').reverse().join('')}}</div>
>在这个地方，模板不再是简单的声明式逻辑，你必须看一段时间才能意识到，这里想要显示便利message的反转字符串。当你想要在模板中多次引用出的反转字符串时，就会更加难以处理。**所以对于任何复杂逻辑，你都应当使用计算属性。**

#### 基础例子
    <div id='example'>
        <p> Original message: "{{message}}"</p>
        <p> Computed reversed message: "{{reverseMessage}}"</p>
    </div>
    var vm = new Vue({
        el: '#example'
        data: {
            message: 'Hello'
        },
        computed: {
            reversedMessage: function(){
                return this.message.split(').reverse().join('')
            }
        }
    })
    结果：Original message: "Hello"
    Computed reversed message: "olleH"
>在这里我们声明了一个计算属性 reversedMessage。我们提供的函数将用做属性vm.reverseMessage的getter函数：

    console.log(vm.reverseMessage);//=>'olleH'
    vm.message = 'GoodBye';
    console.log(vm.reversedMessage);//'eyBdooG
    
>vm.reversedMessage始终取决于vm.message.你可以像绑定普通属性一样在模板中绑定计算属性。Vue知道vm.reversedMessage依赖于vm.message，因此当vm.message发生改变时，所有依赖vm.reversedMessage的绑定就会更新，而且最妙的时我们已经以声明的方式创建了这种依赖关系：计算属性的getter是没有副作用的，这使他更易于测试和理解。
#### 计算属性vs方法

> 在模板语法中用表达式调用方法同样达到上诉的效果。然而，他们不同的是计算属性是基于他们的响应依赖进行缓存的。只有相关响应式依赖发生改变的时，才会重新求值。这就意味着message没有发生改变，多次访问reverseMessage计算属性会立即返回之气哦按的计算结果，而不必再次执行函数。
>这也同样意味着下面的计算属性将不再更新，因为 Date now()不是响应式依赖：

     computed: {
        now: function(){
        return Date.now();
    }
}
> 相比之下，每当触发重新渲染时，调用方法将总会再次执行函数。
>
> 我们为什么需要缓存？假设我们有一个性能开销比较大的计算属性A，它需要遍历一个巨大的数组并且做大量的计算。然后我们可能有其他的计算属性依赖计算属性A，如果没有缓存，我们将不可避免的多次执行A的getter!如果你不希望有缓存，请用方法替换。
#### 计算属性vs侦听属性
> Vue提供了一种更通用的方式来观察和响应Vue实例上的数据变动,侦听属性.当你有一些数据需要随着其他数据变动而变动时候，你很容易滥用watch -- 特别是如果你之前使用AngluarJs。然而，通常更好的做法是使用计算属性而不是命令是的watch回调。
    
    <div id="demo">{{fullName}}</div>
    var vm = new Vue([
        el: 'demo',
        data: {
            firstName: "Foo",
            lastName: "Bar",
            fullName: "Foo Bar'
        }
        watch: {
            firstName: function(val){
                this.fullName = val + this.lastName;
            },
            lastName: function(val){
                this.fullName = this.firstName +val;
            }
        }
    ])
    //监听只能监听当前的属性
> 上面代码是命令式且重复的，将它与计算属性的版本进行比较：

    var vm = new Vue({
        el: "#demo",
        data: {
            firstName: "Foo",
            lastName: "Bar"
        },
        computed: {
            fullName: function(){
                return this.lastName + ' ' + this.firstName;
            }
        }
    })
    //一个函数解决的事情为什么两个来。可用计算改变靠的值。
#### 计算属性的setter
>计算属性默认只有getter,不过需要时你也可以提供一个setter:

    computed: {
        fullName: {
            get: function(){
                return this.firstName +_' ' + this.lastName
            },
            set: function(newValue){
                var names = newValue.split('');
                this.firstName = names[0];
                this.lastName = names[names.length-1];
            }
        }
    }
### 侦听器
>虽然计算属性在大多情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么Vue通过watch选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销比较大的时候，这个方式有用的。

    <div id="watch-example">
        <p>ask a yes/no question:<input v-model="question"></p>
        <p>{{answer}}</p>
    </div>
    <!-- 因为ajax库和通用工具的生态已经相当丰富,Vue核心代码没有重复 -->
    <!-- 提供這些功能保持精简。这也可以让你自由选择自己更熟悉的工具 -->
    <script src="https://cdn..../axios.min.js">
    <script src="https://cdn.../lodash.min.js">
    <script>
        var watchExampleVm = new Vue({
            el:"#wathc-example",
            data: {
                question: '',
                answer: "I cannot give you an answer until you aks a question"
            },
            watch: {
                question: function(newQuestion,oldQuestion){
                    this.answer = "waiting for you to stop typing..';
                    this.debouncedGetAnswer();
                }
            },
            created: function(){
                //'_.debounce是一个通过Loadah限制操作频率的函数。
                //在这个例子中，我们希望限制访问 uyeson.wtf/api的频率。
                //alax请求查到用户输入完毕才会发出。
                //'_debounce'函数（及其近亲`_.throttle`）的知识，
                //请参考：https://loadsh.com/docs#debounce
                this.debounceGetAnswer = _.debounce(this.getAnswer,500)
            }
        })
>在这个示例中，使用watch 允许我们执行异步操作（访问一个API），限制我们执行该操作的概率，并在我们得到的最终结果前，设置中间状态。这些都是计算属性无法做到的。
## 四、Class与Style绑定
> 操作元素的class列表和内联样式是数据绑定的一个常见需求。因为他们都是属性，所以我们可以用v-bind处理他们；只需要通过表达式计算出字符串即可，不过，字符串拼接麻烦且易错。因此，在将v-bind用于class和style时，vue.js做了专门的增强。表达式结果的类型除了字符串外，还可以是对象和数组。

### 绑定HTML Class
#### 对象语法
> 我们可以传给v-bind:class一个对象，以动态地切换class:

    <div v-bind:class="{active:isActive}"></div>

> 上面的语法表示active这个Class存在与否取决于数据属性isActive的truthiness。你可以在对象中传入更多属性来动态切换多个class，此外，v-bind:class指令也可以与普通的class属性共存。

    <div class="static" v-bind:class="{active:isActive,text-danger:hasError}"></div>

> 绑定的数据对象不必内联定义在模板里：
    
    <div v-bind:class="classObject"></div>
    var vm = new Vue({
            el: "#demo",
            data: {
                classObject: {
                    isActive: true,
                    hasError: true
                }
            }
        })

> 我们也可以在这里绑定一个返回对象德计算属性。这是一个常用且强大的模式：
    
    <div id="demo">
        <div class="static" v-bind:class="classObject">111111</div>
    </div>
    var vm = new Vue({
            el: "#demo",
            data: {
                isActive: true,
                error: null
            },
            computed: {
                classObject: function () {
                    return {
                        active: this.isActive && !this.error,
                        'text-danger': this.error && this.error.type === "fatal"
                    }
                }
            }
        })
#### 数组语法
> 我们可以把一个数组传给v-bind:class,以应用一个class列表：

    <div id="demo">
        <div class="static" v-bind:class="[isActive,hasError]">1111</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                isActive: 'active',
                hasError: 'text-danger'
            }
        })
    </script>

> 如果你也想根据条件切换列表中的class,可以用三元表达式：

    <div id="demo">
        <div class="static" v-bind:class="[isActive? activeClass: '',hasError]">1111</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                isActive: true,
                activeClass: 'active',
                hasError: 'text-danger'
            }
        })
    </script>
    这样写将始终添加hasError，但是只有在isActive是truthy的时候才添加activeClass。不过当有多个条件class时候，这样写很繁琐。所以在数组语法中也可用对象语法：
    <div id="demo">
        <div class="static" v-bind:class="[{active: isActive},hasError]">1111</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                isActive: true,
                activeClass: 'active',
                hasError: 'text-danger'
            }
        })
    </script>
#### 用在组件上
> 待续。。。

### 绑定内联式
#### 对象语法
> v-bind:style的对象语法十分直观--看着非常像css，但其实是一个JavaScript对象。css属性名可以用驼峰式（camelCase)或短横线分割（kebab-case ,记得用引号括起来）来命名：

    <div id="demo">
        <div v-bind:style="{color: activeColor, fontSize: fontSize + 'px'}">1111</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                activeColor: 'red',
                fontSize: 30
            }
        })
    </script>
>直接绑定到一个样式对象通常更好，这回让模板更清晰:

    <div id="demo">
        <div v-bind:style="styleObject">1111</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                styleObject : {
                    color: 'red',
                    fontSize: '13px'
                }
            }
        })
    </script>
#### 数组语法
> v-bind:style的数组语法可以将多个样式对象应用到同一个元素上：

    <div id="demo">
        <div v-bind:style="[styleObject,fontSize]">1111</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                styleObject : {
                    color: 'red'
                },
                fontSize: {
                    fontSize: '18px'
                }
            }
        })
    </script>
#### 自动添加前缀
>当v-bind:style 使用需要添加浏览器引擎前缀的css属性时，如 transform,vue.js会自动侦测并添加相应的前缀。

#### 多重值
>从2.3.0起，你可以为style绑定中的属性提供一个包含多个值得数组，常用于提供多个带前缀的值，例如：

    <div :style="{ display: ['-webkit-box','-ms-flexbox','flex']}></div>
    这样写智慧渲染数组中最后一个被浏览器支持的值，在本列中，如果浏览器支持不带浏览器前缀的flexbox,那么只会渲染display: flex。

## 五、条件渲染
### v-if
> v-if 指令用于条件性地渲染一块内容。这块内容只会在指令的表达式返回truethy值的时候被渲染。

    <h1 v-if="awesome">Vue is awesome!</h1>
    
> v-else-if,顾名思义，充当v-if的"else-if块"，可以连续使用：

>也可以用v-else添加一个'else块':
    
    <h1 v-if="awesome">Vue is awesome!</h1>
    <h1 v-else>Oh no </h1>
    
#### 在 `<template>` 元素上使用 v-if条件渲染分组
>待续。。。
#### 用 `key`管理可复用元素。
### v-show
> 带有v-show的元素始终会备选人并保留在DOM中。v-show知识简单地切换元素的css属性的display。 **注意:v-show不支持`<template>`元素，也不支持v-else**
### v-show vs v-if
> v-if 是真正的条件渲染，因为它会确保潜在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

> v-if也是惰性的:如果在初始渲染时条件为假，则什么也不做--直到条件第一次为真时，才会开始渲染条件块。

>相比之下,v-show就简单得多--不管初始条件是什么，元素总是会被渲染，并且只是简单地基于css进行切换。

>一般来说，v-if有更高的切换开销，而v-show有更高的初始渲染开销，因此，如果需要非常频繁地切换，则使用v-show较好;如果在运行时条件很少被改变，则使用v-if较好。
### v-if和v-for一起使用
> 待续。。。

## 六、列表渲染
### 用 v-for把一个数组对应为一组元素
>我们可以用v-for指令基于一个数组来渲染一个列表。v-for指令需要使用item in items形式的特殊语法，其中items是源于数据组，而ite则是被迭代的数组元素的别名。

     <div id="demo">
        <div v-for="item in items">{{item.name}}</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
               items:[
                   {name: "zs"},
                   {name: "ls"},
                   {name: "wag"}
               ]
            }
        })
>在 v-for块中，我们可以访问所有父作用域的属性。v-for还支持一个可选的第二个参数，即当前项的索引。
    
    <div id="demo">
        <div v-for="(item,index) in items">{{parentMessage}}-{{index}}-{{item.name}}</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
               items:[
                   {name: "zs"},
                   {name: "ls"},
                   {name: "wag"}
               ],
               parentMessage: "Parent"
            }
        })
    </script>

> **你也可以用of替代in作为分隔符，因为它更接近js迭代器的语法。

### 在v-for中使用对象

    <div id="demo">
        <div v-for="value in object">{{value}}</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
               object:{
                   name: 'zhangsan',
                   age: 18,
                   sex: "male"
               }
            }
        })
    </script>

> 你也可以提供第二个的参数为property名称（也就是键名）

    <div id="demo">
        <div v-for="(value,name) in object">{{name}}:{{value}}</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
               object:{
                   name: 'zhangsan',
                   age: 18,
                   sex: "male"
               },
               parentMessage: "Parent"
            }
        })
    </script>
> 还可以用到第三个参数作为索引

     <div id="demo">
        <div v-for="(value,name,index) in object">{{index}}.{{name}}:{{value}}</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
               object:{
                   name: 'zhangsan',
                   age: 18,
                   sex: "male"
               },
               parentMessage: "Parent"
            }
        })
    </script>
> **在遍历对象时，会按照Obect.keys()的结果遍历，但是不能保证它的结果在不同的js引擎下都一致**
### 维护状态
>待续。。。
### 变异方法
> Vue将被侦听的数组的变异方法进行包裹,所以他们也将会触发视图更新。这些被包裹的方法包括：

    . push()
    . pop()
    . shift()
    . unshift()
    . splice()
    . sort()
    . reverse()

### 替换数组
> 变异方法，顾名思义，会改变调用了这些方法的原始数组，相比之下，也有非变异（non-mutatingmethod)方法，例如（filter()、concat()和slice().他们不会改变原始数组，而总是返回一个新数组。当使用非变异方法时，可以用数组替换旧数组。
    
    vm.arrData = vm.arrData.slice(0,1);

>你可能认为这将导致vue丢弃现有dom并重新渲染整个列表，幸运的是，事实并非如此，vue为了使得dom元素得到最大范围的重用而实现了一些智能的启发方法，所以用一个含有相同元素的数组去替换原来的数组是非常高效的操作。

### 注意事项

>由于js的限制，vue不能检测以下数组的变动：
1. 当你利用索引直接设置一个数组的时候，例如：vm.items[indexOfitem] = newValue;
2. 当你修改数组的长度时，例如：vm.items.length = newLength

>举个例子:

    <div id="demo">
        <div v-for="(value,index) of arrData">{{index}}.{{name}}:{{value}}</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                arrData: [1, 2, 3, 4],
                parentMessage: "Parent"
            }
        });
        vm.arrData[1] = 'x';
        vm.arrData.length = 2;
    </script>
>为了解决第一类问题，以下两种方式都可以实现和vm.items[indexOfitem] = newValue相同的效果，同时也将响应式系统内触发状态更新：
    
    //Vue.set
    Vue.set(vm.items,indexOfItem,newValue)
    //Array.prototype.splice
    vm.items.splice(indexOfItem,1,newValue)
>你也可以使用vm.$set的实例方法，该方法是全局方法Vue.set的一个别名：
    vm.$set(vm.items,indexOfItem,newValue)
>为了解决第二类问题，你可以使用splice：
    vm.items.splice(newLength)

### 对象变更检测注意事项
>还是由于JavaScript的限制，Vue不能检测独享属性的添加或删除：

     <div id="demo">
        <div>{{a}}</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                a: 1
            }
        });
        // vm.a = 2;现在是响应式
        // vm.b = 2; 不是响应式
>对于已经创建的实例,Vue不允许动态添加根级别的响应式属性。但是，可以使用
Vue.set(object,propertyName,value)方法向嵌套对象添加响应式属性，例如，对于：

    var vm = new Vue({
        data: {
            userprofile: {
                name: 'Anika'
            }
        }
    })
>你可以添加一个新的age属性到嵌套的userProfile对象：

    Vue.set(vm.userProfile,'age',27)

>有时你可能需要为已有对象赋值多个新属性，比如使用Object.assign()或者_.extend()。在这种情况下，你应该用两个对象的属性创建一个新的对象，所以，如果你想添加新的响应式属性，不要像这样：

    Object.assign(vm.userProfile,{
        age: 27,
        favoriteColor: "pink"
    })

>你应该这样做：

    vm.userProfile = Object.assign({},vm.userProfile,{
        age: 27,
        favoriteColor: "pink"
    })

### 显示过滤/排序后的结果

> 有时，我们想要显示一个数组经过过滤或排序后的版本，而不实际改变或者重置原始数据。在这种情况下，可以创建一个计算属性，来返回过滤或排序后的数组。

>例如：
    
    <div id="demo">
        <li v-for="(value,index) of arrDataFilter">{{value}}</li>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                arrData: [1, 2, 3, 4, 5, 6]
            },
            computed: {
                arrDataFilter: function () {
                    return this.arrData.filter(function (number) {
                        console.log(number);
                        return number % 2 === 0
                    })
                }
            }
        })
        // vm.a = 2;
    </script>
> 在计算属性不适用的情况下（例如，在嵌套v-for循环中）你可以使用一个方法：

      <div id="demo">
        <li v-for="(value,index) of arrDataFilter(arrData)">{{value}}</li>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                arrData: [1, 2, 3, 4, 5, 6]
            },
            methods: {
                arrDataFilter: function (arrData) {
                    return this.arrData.filter(function (number) {
                        console.log(number);
                        return number % 2 === 0
                    })
                }
            }
        })
        // vm.a = 2;
    </script>
### v-for里使用值范围
> v-for也可以接受整数，在这种情况下，它会把模板重复对于次数。

    <div id="demo">
        <li v-for="n of 10">{{n}}</li>
    </div>
### 在`<template>`上使用 v-for
>待续。。。
### v-for 与 v-if 一同使用
> **我们不推荐在同一元素上使用v-if和v-for。**
>当他们处于同一个节点，v-for的优先级比v-if更高，这意味着v-if将分别重复运行于每一个v-for中，当你只想部分项渲染节点时，这种优先级的机制十分有用，如下：

    <div id="demo">
        <li v-for="todo in todos" v-if="!todo.isComplete">{{todo.number}}</li>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                todos: [{
                    number: 1,
                    isComplete: true
                },{
                    number: 2,
                    isComplete: true 
                },{
                    number: 3,
                    isComplete: false
                },{
                    number: 4,
                    isComplete: true 
                },{
                    number: 6,
                    isComplete: false
                }]
            }
        })
    </script>
>而如果你的目的是有条件地跳过循环的执行，那么可以将v-if置于外层元素（或`<template>`)上，如：

    <div id="demo">
        <ul v-if="todos.length">
            <li v-for="todo in todos" >{{todo.number}}</li>
        </ul>
        <p v-else>No todos left</p>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                todos: [{
                    number: 1,
                    isComplete: true
                },{
                    number: 2,
                    isComplete: true 
                },{
                    number: 3,
                    isComplete: false
                },{
                    number: 4,
                    isComplete: true 
                },{
                    number: 6,
                    isComplete: false
                }]
            }
        })
        vm.todos.splice(0);
### 在组件上使用v-for
> 待续。。。
## 事件处理
### 监听事件
> 可以用v-on指令监听Dom事件，并在触发时，运行一些js代码。

    <div id="demo">
        <button v-on:click="count+=1">add 1</button>
        <p>The button above has been clicked {{count}} times</p>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                count: 0
            }
        })
    </script>
### 事件处理方法
>然而许多事件处理逻辑会更为复杂，所以直接把js代码写在v-on指令中是不可行的。因此v-on还可以接受一个需要调用的方法名称。

    <div id="demo">
        <button v-on:click="addCount">add 1(最大数为5)</button>
        <p>The button above has been clicked {{count}} times</p>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                count: 0
            },
            methods: {
                addCount: function(e){
                    if(this.count >= 5)  return this.count;
                    return this.count += 1;
                }
            }
        })
    </script>
### 内联处理器中的方法
> 除了直接绑定到一个方法，也可以在内联js语句中调用方法：

    <div id="demo">
        <button v-on:click="addCount(null)">add 1(最大数为5)</button>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                count: 0
            },
            methods: {
                addCount: function(number){
                    alert('我的数据类型是:' + typeof(number))
                }
            }
        })
    </script>
### 事件修饰符
>在事件处理程序中调用 event.preventDefault()或者event.stopPropagation()是非常常见的需求。尽管我们可以在方法中轻松实现这点，但更好的方式是：方法只是纯粹的数据逻辑，而不是去处理Dom事件细节。

>为了解决这个问题，Vue.js提供事件修饰符。修饰符是由点开头的指令后缀来表示的。

* .stop
* .prevent
* .capture
* .self
* .once
* .passive

>.stop阻止事件继续传播
     
     <div id="demo" @click="addCount">
        <div class="stopEvent" @click.stop="addCount">click me ,alert 1</div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                count: 0
            },
            methods: {
                addCount: function(){
                    alert(1)
                }
            }
        })
    </script>
    //父元素事件将不再发生
> .prevent阻止默认事件发生

    <div id="demo" @click="addCount">
        <a href="http://www.baidu.com" @click.prevent="addCount">指向百度</a>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                count: 0
            },
            methods: {
                addCount: function(){
                    alert(1)
                }
            }
        })
    </script>
    //a链接将不再跳转
> 修饰符可串联

    <div id="demo" @click="addCount">
        <a href="http://www.baidu.com" @click.stop.prevent="addCount">指向百度</a>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
                count: 0
            },
            methods: {
                addCount: function(){
                    alert(1)
                }
            }
        })
    </script>
    //父元素事件将不再发生，a链接默认跳转将不再跳转。

> .capture

    <div id="demo" @click="addCount(2)">
        <div class="outer" @click.capture="addCount(1)">外部事件
            <div class="inner" @click="addCount(0)">内部事件</div>
        </div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
              
            },
            methods: {
                addCount: function(number){
                   alert(number);
                }
            }
        })
    </script>
    //添加事件监听器时使用事件捕获模式
    //即内部元素触发的事件先在此处理，然后才交由内部元素进行处理

>.self

    <div id="demo" @click="addCount(2)">
        <div class="outer" @click="addCount(1)">外部事件
            <div class="inner" @click="addCount(0)">内部事件</div>
        </div>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
              
            },
            methods: {
                addCount: function(number){
                   alert(number);
                }
            }
        })
    </script>
    //只有当event.target 是当前元素自身时触发处理函数。
> **使用事件修饰符时，顺序很重要;相应代码会产生同样的顺序事件产生，因此，用v-on:click.prevent.self会阻止所有的点击，而v-on:click.self.prevent只会阻止对元素自身的点击**

    <div id="demo" @click="addCount(2)">
        <a href="http://www.baidu.com" class="outer" @click.prevent.self="addCount(1)">外部事件
            <div class="inner" @click="addCount(0)">内部事件</div>
        </a>
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
              
            },
            methods: {
                addCount: function(number){
                   alert(number);
                }
            }
        })
    </script>
    //在这里，只有满足.prenvent的时候才会执行.self，而.self.prevent 只有满足.self的时候，才会执行.prevent
>2.14Vue版本新增.once,点击事件将只会触发一次，不像其它智能对原生的dom事件起作用的修饰符，.once修饰符还能被用到自定义的组件事件上。未完待续。。。
>Vue还对应addEventListener中的passive选项提供了.passive修饰符。滚动事件的默认行为（即滚动行为）将会立即触发，而不会等待onScroll完成，这其中包含event.preventDefault()的情况。
    
### 按键修饰符
> 在监听键盘事件时，我们经常需要检查详细的按键，Vue允许为v-on在监听键盘事件时添加按键修饰符：

    !只有在`key`时`Enter`时调用`vm.submit()`-->
    <div id="demo">
       <input type="text" v-on:keyup.enter="submit">
    </div>
    <script>
        var vm = new Vue({
            el: "#demo",
            data: {
              
            },
            methods: {
                submit: function(){
                   alert(1);
                }
            }
        })
    </script>
    你可以直接将KeyboardEvent.key暴露的任意有效按键名转换为kebab-case来作为修饰符
    <input type="text" v-on:keyup.page-down="submit">
    在上述示例中，处理函数submit只会在$event.key等于pagedown的时候触发
### 按键码
>**keyCode的事件用法已经被废弃了并可能不会被最新的浏览器支持**
> 使用keyCode attribute也是允许的：

    <input type="text" v-on:keyup.13="submit">
> 为了在必要的情况下支持旧浏览器,Vue提供了绝大多数常用的按键码的别名：
* .enter
* .tab
* .delete
* .esc
* .space
* .up
* .down
* .left
* .right
> 有一些按键（.esc以及所有的方向键）在ie9中有不同的key值，如果你想支持ie9，这些内置的别名应该是首选

    <input type="text" v-on:keyup.tab="submit">

### 系统修饰键
> 2.1.0新增 可以用如下修饰符来实现仅在按下相应按键时才触发鼠标或键盘事件的监听器。
* .ctrl
* .alt
* .shift
* .meta
> **注意：在Mac系统键盘上，meta对应command键。在windows系统键盘meta对应windwows微标键。
>**请注意：修饰键与常规键不同，在和keyup事件一起用时，事触发时修饰键必须处理按下状态。换句话，只有在按住ctrl的情况下释放其它按键，才能触发keyup.ctrl。而单单释放ctrl也不会触发事件。如果你想要这样的行为，请为ctrl换用keyCode:keyup.17。**

### .exact修饰符
> 2.5.0新增 .exact 修饰符允许你控制由精确的系统修饰符组合触发的事件

    <!-- 即使alt或shift被一同按下时也会触发 -->
    <input type="text" v-on:click.ctrl="submit">
    <!-- 有且只有Ctrl按下时才会触发 -->
    <input type="text" v-on:click.ctrl.exact="submit">
    <!-- 没有任何系统修饰符被按下时才触发 -->
    <input type="text" v-on:click.exact="submit">
### 鼠标按钮修饰符
> 2.2.0新增
* .left
* .right
* .middle
这些修饰符会限制处理函数仅响应特定的鼠标按钮。
### 为什么在HTML中监听事件？
>你可能注意到这种事件监听的方式违背了关注点分离这个长期以来的优良传统。但不必担心，因为所有的Vue.js事件处理方法和表达式都严格绑定在当前视图的ViewModel上，他不会导致任何维护上的困难。实际上，使用v-on有几个好处：
1. 扫一眼HTML模板便能轻松定位在js代码里对应的方法。
2. 因为你无须再js里动手绑定事件，你ViewModel代码可以是非常纯粹的逻辑，和DOM完全解耦，更易于测试。
3. 当一个ViewModel被销毁时，所有的事件处理器自动被删除，你无须担心如何清理他们。






    
    


    





    



