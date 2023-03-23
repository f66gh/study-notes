# Vue面试

### **data为什么是一个函数**

- 如果data是一个函数的话，这样`每复用一次组件，就会返回一份新的data`(类似于给每个组件实例创建一个私有的数据空间，让各个组件实例维护各自的数据)
- Object是引用数据类型，`里面保存的是内存地址`，单纯的`写成对象形式`，就`使得所有组件实例共用了一份data`，就会造成一个变了全都会变的结果。

### **vue2和vue3的区别**

1. **响应式原理**

**vue2** 的[响应式](https://so.csdn.net/so/search?q=响应式&spm=1001.2101.3001.7020)原理是利⽤es5 的⼀个 API ，Object.defineProperty()对数据进⾏劫持结合发布订阅模式的⽅式来实现的。

**vue3** 中使⽤了 es6 的 proxy API 对数据代理，通过 reactive() 函数给每⼀个对象都包⼀层 Proxy，通过 Proxy 监听属性的变化，从⽽ 实现对数据的监控。

这⾥是引相⽐于vue2版本，使⽤proxy的优势如下

1.defineProperty只能监听某个属性，不能对全对象监听

2.可以省去for in、闭包等内容来提升效率（直接绑定整个对象即可）

3.可以监听数组，不⽤再去单独的对数组做特异性操作,通过Proxy可以直接拦截所有对象类型数据的操作，完美⽀持对数组的监听。 （vue3.x可以检测到数组内部数据的变化）

2. **Vue3支持碎片**

**vue3在组件中可以有很多个根节点**

3. **数据的方法的定义**

**Vue2使⽤的是选项类型API（Options API），Vue3使⽤的是组合式API（Composition API）**

旧的选项型API在代码里分割了不同的属性: data,computed属性，methods，等等。新的组合式API能让我们用方法（function）来分割，相比于旧的API使用属性来分组，`这样代码会更加简便和整洁`。

`setup`函数可以说是`Vue3`的属性和方法入口。在`Vue2`中，使用的是`data`、`methods、``computed`。在`Vue3`中我们把属性和方法都放在`setup`函数中。`setup`函数中有以下几个特点：

​		 **1.setup(props,context):接收两个参数**

(1) props ：接收来自父组件传来的参数

(2) context ：上下文，主要包含3个使用参数：attrs,emits,slots，相当于Vue2中this的 attrs,emits,slots

​		**2.有返回值，返回值可以是两种：**

 (1) 返回 对象 ，返回的对象中的属性方法，可在模板中直接使用；

 (2) 返回 渲染函数 ，可以自定义渲染的内容；

​		**3.函数内部没有`this`；** 

​		 **4.当内部有异步函数，需要使用到`await`的时候，可以直接使用，不需要在`setup`前面加`async`**

4. 生命周期钩子

   |               |                 |                             |
   | ------------- | --------------- | --------------------------- |
   | vue2          | vue3            | 说明                        |
   | beforeCreate  | setup()         | 组件创建之前                |
   | created       | setup()         | 组件创建完成                |
   | beforeMount   | onBeforeMount   | 组件挂载之前                |
   | mounted       | onMounted       | 组件挂载完成                |
   | beforeUpdate  | onBeforeUpdate  | 数据更新，虚拟DOM打补丁之前 |
   | updated       | onUpdated       | 数据更新，虚拟DOM渲染完成   |
   | beforeDestroy | onBeforeUnmount | 组件销毁之前                |
   | destroyed     | onUnmounted     | 组件销毁后                  |
   | activated     | onActivated     |                             |
   | deactivated   | onDeactivated   |                             |

- 若组件被`<keep-alive>`包含，则多出下面两个钩子函数。

1. onActivated(): 被包含在中的组件，会多出两个生命周期钩子函数。被激活时执行 。
2. onDeactivated(): 比如从 A组件，切换到 B 组件，A 组件消失时执行。

5. **父子通信**
   1. 父传子

​	vue2：console.log(‘props’,this.xxx)
​	vue3：setup(props,context){ console.log(‘props’,props) }

```javascript
/*
	1. 父组件提供数据
	2. 父组件将数据传递给子组件
	3. 子组件通过defineProps进行接收
	4. 子组件渲染父组件传递的数据
*/
```

 子传父

vue2：this.$emit()
vue3：setup(props,context){context.emit()}

```javascript
/*
	在子组件中获取emit , defineEmits()
	子组件中用emit发送事件
	父组件要监听子组件的事件
	父组件提供事件的处理函数
*/
```

### $nextTick

（案例：点击编辑按钮后会弹出输入框，同时这个输入框要获取焦点，则必须要用nextTick或者setTimeout，因为setTImeout也会把要执行的事件放到事件事件队列中）

1. **nextTick是等待下一次DOM更新刷新的工具方法。**（其实一句话就可以把$nextTick这个东西讲明白：**就是你放在 $ nextTick 当中的操作不会立即执行，而是等数据更新、DOM更新完成之后再执行**，这样我们拿到的肯定就是最新的了。

   再准确一点来讲就是 $nextTick方法将回调延迟到下次DOM更新循环之后执行。（看不懂这句人话的，可以看上面）

2. **Vue有个异步更新策略**，意思是如果数据变化，Vue不会立刻更新DOM，而是开启一个队列，把组件更新函数保存在队列中，在同一[事件循环](https://so.csdn.net/so/search?q=事件循环&spm=1001.2101.3001.7020)中发生的所有数据变更会异步的批量更新。这一策略导致我们对数据的修改不会立刻体现在DOM上，此时如果想要获取更新后的DOM状态，就需要使用nextTick。
3. 开发时，有两个场景我们会用到nextTick
   - created中想要获取DOM时;
   - 响应式数据变化后获取DOM更新后的状态，比如希望获取列表更新后的高度。
4. nextTick类型

`function nextTick(callback?: () => void): Promise<void>`

所以我们只需要在传入的回调函数中访问最新DOM状态即可，或者我们可以await nextTick()方法返回的Promise之后做这件事。

5. 在Vue内部，nextTick之所以能够让我们看到DOM更新后的结果，是因为我们传入的callback会被添加到队列刷新函数(flushSchedulerQueue)的后面，这样等队列内部的更新函数都执行完毕，所有DOM操作也就结束了，callback自然能够获取到最新的DOM值。

```html
<script>
import { nextTick } from 'vue'

export default {
  data() {
    return {
      count: 0
    }
  },
  methods: {
    async increment() {
      this.count++

      // DOM 还未更新
      console.log(document.getElementById('counter').textContent) // 0

      await nextTick()
      // DOM 此时已经更新
      console.log(document.getElementById('counter').textContent) // 1
    }
  }
}
</script>

<template>
  <button id="counter" @click="increment">{{ count }}</button>
</template>
```

给了一个count，想要在界面中绑定展示。一开始是0，如果用户点了increment，希望这个count能够++，我们希望界面中能够变成1，可是在紧接着的console.log中你会发现它并没有更新，结果仍然为0，并不是1，那么怎么输出1呢？await nextTick()，后面再输出结果就是1了。为什么会这样呢？这就是nextTick()带给我们最关键的东西了。说白了，**就是nextTick()会返回一个promise，未来在异步的方式再来调用我们这行代码。**也就是说nextTick()下的console.log将来执行的时候我们的DOM已经更新了。还有一种方式就是nextTick()中间加一个回调函数，在回调函数中输出我们的console.log里面的那一行代码。这样也是可以的。

**总结一下：就是 $nextTick将回调函数放到微任务或者宏任务当中以延迟它地执行顺序；**

### 为什么Vue组件模板只有一个根元素

对于这个问题，我们从以下三个方面来考虑：

1. new Vue({el:'#app'})
2. 单文件组件中，template下的元素div。其实就是“树”状数据结构中的“根”
3. diff算法要求的，源码中，patch.js里patchVnode()

### MVVM

`MVVM`是`Model-View-ViewModel`的缩写。`MVVM`是一种设计思想。`Model` 层代表[数据模型](https://so.csdn.net/so/search?q=数据模型&spm=1001.2101.3001.7020)，也可以在`Model`中定义数据修改和操作的业务逻辑；`View` 代表`UI 组件`，它负责将数据模型转化成`UI` 展现出来，`ViewModel` 是一个同步`View` 和 `Model`的对象。

dom通过监听事件操作vue里的data，反之vue中的data通过指令操作dom，这就是所说数据驱动视图，这就是mvvm的理解。

`ViewModel` 通过双向数据绑定把 `View` 层和 `Model` 层连接起来，而`View` 和 `Model` 之间的同步工作完全是自动的，无需人为干涉，因此开发者只需关注业务逻辑，不需要手动操作`DOM`, 不需要关注数据状态的同步问题，复杂的数据状态维护完全由 `MVVM` 来统一管理。



### MVVM 和 MVC 的区别

**MVC**

Model数据层 View视图层 Controller控制视图层和数据层的关联，主要通过监听dom事件

1. 视图发生变化触发controller，并且将数据传递给Controller
2. Controller拿到新的数据触发Model并将更新的数据传递给model
3. model拿到数据更新数据并且触发view视图更新

**MVVM**

是MVC的改进版，整体和MVC差不多，**最大的区别是mvc是单向的，而MVVM是双向的，而且是自动的**，也就是数据发生变化自动同步视图，视图发生变化自动同步数据，**同时解决了 mvc 中大量的 DOM 操作使页面渲染性能降低**，加载速度变慢，影响用户体验。和当 Model 频繁发生变化，开发者需要主动更新到 View

### 路由跳转

**路由传值的方式**

- 声明式（**标签跳转**）`<router-link to="需要跳转到页面的路径">`
- 编程式（ **js跳转**）

this.$router.push()跳转到指定的url，并在history中添加记录，点击回退返回到上一个页面

this.$router.replace()跳转到指定的url，但是history中不会添加记录，点击回退到上上个页面

this.$touter.go(n)向前或者后跳转n个页面，n可以是正数也可以是负数

**页面使用router-view承载路由**

**vue-router钩子函数**

分为三类：

* 全局钩子包含beforeEach，包含三个参数：
  * to:router即将进入的路由对象
  * from当前导航即将离开的路由
  * next: function 进行管道中的一个钩子，如果执行完了，则导航的状态就是confirmed，否则为false，终止导航
* 单独路由独享组件beforeEnter
* 组件内钩子
  * beforeRouterEnter
  * beforeRouterUpdate
  * beforeRouterLeave

**Vue的路由实现模式：hash模式和history模式**

hash模式：在浏览器中符号“#”，#以及#后面的字符称之为hash，用 window.location.hash 读取。特点：hash虽然在URL中，但不被包括在HTTP请求中；用来指导浏览器动作，对服务端安全无用，hash不会重加载页面。

history模式：history采用HTML5的新特性；且提供了两个新方法： pushState()， replaceState()可以对浏览器历史记录栈进行修改，以及popState事件的监听到状态变更

**编程式导航使用方法以及常用的方法**

路由跳转 ： this.$router.push()  路由替换 : this.$router.replace()  后退： this.$router.back()  前进 ：this.$router.forward()

**Vue如何去除URL中的#**

vue-router 默认使用 hash 模式，所以在路由加载的时候，项目中的 URL 会自带 “#”。如果不想使用 “#”， 可以使用 vue-router 的另一种模式 history：new Router ({ mode : 'history', routes: [ ]})

需要注意的是，当我们启用 history 模式的时候，由于我们的项目是一个单页面应用，所以在路由跳转的时候，就会出现访问不到静态资源而出现 “404” 的情况，这时候就需要服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 “index.html” 页面。

**$route和$router的区别**

$route是路由信息对象，包括‘path，hash，query，fullPath，matched，name’等路由信息参数； $router是路由实例对象，包括了路由的跳转方法，实例对象等

### 说一下在Vue中踩过的坑

1. 给对象添加属性的是时候，直接通过给data里边的对象添加属性然后赋值，新添加的属性不是响应式

   **解决方法：**通过Vue.set(对象，属性，值)，对象新添加的属性是响应式的

2. 在created操作dom的时候，是报错的，获取不到dom，这个时候vue实例还没有挂载

   **解决方法：通过Vue.nextTick**

### **Vue.set()和this.$set()**

受 ES5 的限制，Vue.js 不能检测到对象属性的添加或删除。因为 Vue.js 在初始化实例时将属性转为 getter/setter，所以 属性必须在 data 对象上才能让 Vue.js 转换它，才能让它是响应的。

**因此：**
Vue 不能检测以下变动的数组：
当你利用索引直接设置一个项时，例如：vm.items[indexOfItem] = newValue
当你修改数组的长度时，例如：vm.items.length = newLength
eg：
使用 this.arr[0] 去更新 array 的内容，视图没有刷新
使用 Vue.set(this.arr, 0, !this.arr[0]) 去更新 array 的内容，视图被刷新
使用 this.arr[0] = !this.arr[0] 和 this.obj.a = !this.obj.a 同时更新，视图被刷新

**结论：**
如果方法里面单纯的更新数组 Array 的话，要使用 Vue.set()；
如果方法里面同时有数组和对象的更新，直接操作 data 即可;

### vue操作DOM元素的三种方法

1. 原生JS操作DOM
2. vue官方方法：ref    this.$refs....
3. jQuery操作dom（不建议）

### H5页面滑动至指定位置

方法一：

```javascript
//先获取目标位置距离
mounted() {
  this.$nextTick(() => {
     setTimeout(() => {
        let targetbox= document.getElementById('targetbox');
        this.target= targetbox.offsetTop;        
   })
  })
}
//再滑动指定距离
document.body.scrollTop = this.target;
```

方法二：

```javascript
this.$nextTick(() => {
     this.$refs.DOM.scrollTo(0,200);
})
```

方法三：

```javascript
document.getElementById("target").scrollIntoView();
```



##  面试题

1. v-show 与v-if

   v-show通过css display控制显示和隐藏，v-if真正的渲染和销毁，而不是显示和隐藏

2. 为何v-for用Key

   快速查找节点，减少渲染次数，提升渲染性能

3. vue组件生命周期

​	挂载： beforeCreate => created => beforeMount => mounted
​	更新： beforeUpdate => updated
​	销毁： beforeDestroy => destroyed

​	父组件生命周期图

​	挂载： parent beforeCreate => parent created => parent beforeMount => child beforeCreate => child created => child beforeMount => child mounted => parent mounted
​	更新： parent beforeUpdate => child beforeUpdate => child updated => parent updated
​	销毁： parent beforeDestroy => child beforeDestroy => child destroyed => parent destroyed
​	从以上能够看出：
​	挂载时，子组件是在父组件before mount后开始挂载，并且子组件先mounted，父组件随后
​	更新时，子组件是在父组件before update后开始更新，子组件先于父组件更新
​	销毁时，子组件是在父组件before destroy后开始销毁，并且是子组件先销毁，父组件随后。

5. 