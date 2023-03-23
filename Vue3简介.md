# Vue3简介

## Vue的两个核心功能

声明式渲染：Vue基于标准HTML拓展了一套模板语法，使我们可以声明式地描述最终输出的HTM和JavaScript状态之间的关系

响应性：Vue会自动跟踪JavaScript状态并在其发生变化时响应式地更新DOM

## Vue是一种渐进式框架

## API风格

### 选项式API

我们用包含多个选项的对象来描述组件的逻辑，例如data，methods，mounted等，选项所定义的属性都会暴露在函数内部的this上，他会指向当前的组件实例

待就业青年

```html
<script>
export default {
  // data() 返回的属性将会成为响应式的状态
  // 并且暴露在 `this` 上
  data() {
    return {
      count: 0
    }
  },

  // methods 是一些用来更改状态与触发更新的函数
  // 它们可以在模板中作为事件监听器绑定
  methods: {
    increment() {
      this.count++
    }
  },

  // 生命周期钩子会在组件生命周期的各个不同阶段被调用
  // 例如这个函数就会在组件挂载完成后被调用
  mounted() {
    console.log(`The initial count is ${this.count}.`)
  }
}
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

### 组合式API

通过组合式 API，我们可以使用导入的 API 函数来描述组件逻辑。在单文件组件中，组合式 API 通常会与\<script setup>搭配使用。这个 `setup` attribute 是一个标识，告诉 Vue 需要在编译时进行一些处理，让我们可以更简洁地使用组合式 API。比如，`<script setup>` 中的导入和顶层变量/函数都能够在模板中直接使用。

```html
<script setup>
import { ref, onMounted } from 'vue'

// 响应式状态
const count = ref(0)

// 用来修改状态、触发更新的函数
function increment() {
  count.value++
}

// 生命周期钩子
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

# 基础

## 模板语法

```
<p>Using text interpolation: {{ rawHtml }}</p> 
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

Using v-html directive: \<span style="color: red">This should be red.\</span>

Using text interpolation :  <span style="color: red">This should be red.</span>

### 受限的全局访问

模板中的表达式将被沙盒化，仅能够访问到[有限的全局对象列表](https://github.com/vuejs/core/blob/main/packages/shared/src/globalsWhitelist.ts#L3)。该列表中会暴露常用的内置全局对象，比如 `Math` 和 `Date`。

没有显式包含在列表中的全局对象将不能在模板内表达式中访问，例如用户附加在 `window` 上的属性。然而，你也可以自行在 [`app.config.globalProperties`](https://cn.vuejs.org/api/application.html#app-config-globalproperties) 上显式地添加它们，供所有的 Vue 表达式使用。

### 参数 Arguments

某些指令会需要一个“参数”，在指令名后通过一个冒号隔开做标识。例如用 `v-bind` 指令来响应式地更新一个 HTML attribute：

```
<a v-bind:href="url"> ... </a>

<!-- 简写 -->
<a :href="url"> ... </a>
```

这里 `href` 就是一个参数，它告诉 `v-bind` 指令将表达式 `url` 的值绑定到元素的 `href` attribute 上。在简写中，参数前的一切 (例如 `v-bind:`) 都会被缩略为一个 `:` 字符。

另一个例子是 `v-on` 指令，它将监听 DOM 事件：

```
<a v-on:click="doSomething"> ... </a>

<!-- 简写 -->
<a @click="doSomething"> ... </a>
```

这里的参数是要监听的事件名称：`click`。`v-on` 有一个相应的缩写，即 `@` 字符。我们之后也会讨论关于事件处理的更多细节。

### 动态参数

```
<!--
注意，参数表达式有一些约束，
参见下面“动态参数值的限制”与“动态参数语法的限制”章节的解释
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- 简写 -->
<a :[attributeName]="url"> ... </a>
```

这里的 `attributeName` 会作为一个 JavaScript 表达式被动态执行，计算得到的值会被用作最终的参数。举例来说，如果你的组件实例有一个数据属性 `attributeName`，其值为 `"href"`，那么这个绑定就等价于 `v-bind:href`。

相似地，你还可以将一个函数绑定到动态的事件名称上：

```
<a v-on:[eventName]="doSomething"> ... </a>

<!-- 简写 -->
<a @[eventName]="doSomething">
```

在此示例中，当 `eventName` 的值是 `"focus"` 时，`v-on:[eventName]` 就等价于 `v-on:focus`。

**动态参数数值的限制**

动态参数中表达式的值应当是一个字符串，或者是 `null`。特殊值 `null` 意为显式移除该绑定。其他非字符串的值会触发警告。

**动态参数语法的限制**

动态参数表达式因为某些字符的缘故有一些语法限制，比如空格和引号，在 HTML attribute 名称中都是不合法的。例如下面的示例：

```
<!-- 这会触发一个编译器警告 -->
<a :['foo' + bar]="value"> ... </a>
```

如果你需要传入一个复杂的动态参数，我们推荐使用[计算属性](https://cn.vuejs.org/guide/essentials/computed.html)替换复杂的表达式，也是 Vue 最基础的概念之一，我们很快就会讲到。

当使用 DOM 内嵌模板 (直接写在 HTML 文件里的模板) 时，我们需要避免在名称中使用大写字母，因为浏览器会强制将其转换为小写：

```
<a :[someAttr]="value"> ... </a>
```

上面的例子将会在 DOM 内嵌模板中被转换为 `:[someattr]`。如果你的组件拥有 “someAttr” 属性而非 “someattr”，这段代码将不会工作。单文件组件内的模板**不**受此限制。

![image-20221213185316631](D:\WebStrom Coding\课件\image-20221213185316631.png)

属性（Attribute）名、属性参数、属性修饰符、属性值

## 响应式基础

### 声明响应式状态

我们可以使用 [`reactive()`](https://cn.vuejs.org/api/reactivity-core.html#reactive) 函数创建一个响应式对象或数组：

```javascript
import { reactive } from 'vue'

const state = reactive({ count: 0 })
```

要在组件模板中使用响应式状态，需要在 `setup()` 函数中定义并返回。注意利用setup函数书写组合式代码时，不需要再script标签上写setup

```html
<script>
import {reactive} from "vue";
// const state = reactive({count: 0})
// const func = function (){
//   state.count++
// }
export default {
  setup(){
    const state = reactive({count: 0})
    function func(){
      state.count++
    }

    //别忘记return
    return{
      state,
      func
    }
  }
}
</script>

<template>
  <div @click="func">{{state.count}}</div>
</template>
```

### \<script setup>

在setup函数中手动暴露大量的状态和方法非常繁琐。当使用单文件组件时，我们可以使用script setup大幅简化代码

```
<script>
	import {reactive} from "vue";
	const state = reactive({count: 0})
	const func = function (){
		state.count++
	}
</script>

<template>
  <div @click="func">{{state.count}}</div>
</template>
```

**script setup中的顶层的导入和变量声明可以在同一组件的模板中直接使用**。大多数的Vue开发者都会使用单文件组件+script setup

### DOM更新时机

当你更改响应式状态后，DOM 会自动更新。然而，你得注意 DOM 的更新并不是同步的。相反，Vue 将缓冲它们直到更新周期的 “下个时机” 以确保无论你进行了多少次状态更改，每个组件都只更新一次。

若要等待一个状态改变后的 DOM 更新完成，你可以使用 [nextTick()](https://cn.vuejs.org/api/general.html#nexttick) 这个全局 API：

```html
<script setup>
import {reactive, nextTick} from "vue";
const state = reactive({count: 0})
const func = function (){
  console.log("bbb:", state.count)
  nextTick(() => {
    console.log("aaa:", state.count)
  })
  state.count++
}
</script>

<template>
  <div @click="func">{{state.count}}</div>
</template>
```

注意要引用nextTick，在点击函数中，由于打印函数比改变DOM元素先执行，所以bbb后边输出是旧的，但是aaa后面输出是新的

### 深层响应性

在 Vue 中，状态都是默认深层响应式的。这意味着即使在更改深层次的对象或数组，你的改动也能被检测到。

你也可以直接创建一个[浅层响应式对象](https://cn.vuejs.org/api/reactivity-advanced.html#shallowreactive)。它们仅在顶层具有响应性，一般仅在某些特殊场景中需要。

### 响应式代理 vs 原始对象

`reactive()` 返回的是一个原始对象的 [Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)，它和原始对象是不相等的：

```javascript
const raw = {}
const proxy = reactive(raw)

// 代理对象和原始对象不是全等的
console.log(proxy === raw) // false
```

**只有代理对象是响应式的，更改原始对象不会触发更新。**因此，使用 Vue 的响应式系统的最佳实践是 **仅使用你声明对象的代理版本**。

为保证访问代理的一致性，对同一个原始对象调用 `reactive()` 会总是返回同样的代理对象，而对一个已存在的代理对象调用 `reactive()` 会返回其本身：

```javascript
// 在同一个对象上调用 reactive() 会返回相同的代理
console.log(reactive(raw) === proxy) // true

// 在一个代理上调用 reactive() 会返回它自己
console.log(reactive(proxy) === proxy) // true
```

**这个规则对嵌套对象也适用。依靠深层响应性，响应式对象内的嵌套对象依然是代理：**

```javascript
const proxy = reactive({})

const raw = {}
proxy.nested = raw

console.log(proxy.nested === raw) // false
```

### reactive的局限性

`reactive()` API 有两条限制：

1. **仅对对象类型有效（对象、数组和 `Map`、`Set` 这样的[集合类型](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects#使用键的集合对象)），而对 `string`、`number` 和 `boolean` 这样的 [原始类型](https://developer.mozilla.org/zh-CN/docs/Glossary/Primitive) 无效。**
2. 因为 Vue 的响应式系统是通过属性访问进行追踪的，**因此我们必须始终保持对该响应式对象的相同引用。**这意味着我们不可以随意地“替换”一个响应式对象，因为这将导致对初始引用的响应性连接丢失：

```javascript
let state = reactive({ count: 0 })

// 上面的引用 ({ count: 0 }) 将不再被追踪（响应性连接已丢失！）
state = reactive({ count: 1 })
```

同时这也意味着当我们将响应式对象的属性赋值或解构至本地变量时，或是将该属性传入一个函数时，我们会失去响应性：

```javascript
  //定义一个响应式对象及其引用
  let num = reactive({count: 0})
  //定义一个局部变量，不会影响引用的数据（失去了响应式链接）
  let n = num.count
  n++
  console.log("ccc:",num.count)
  //不会影响引用的数据
  let {count} = num.count
  count++
  console.log("ddd:", num.count)
  //该函数会接收一个普通的数字，并且将无法跟踪state.count的变化
  ;(function (count) {
    count++
    console.log("eee:", num.count)
  })(num.count)
```

### 用ref()定义响应式变量

`reactive()` 的**种种限制归根结底是因为 JavaScript 没有可以作用于所有值类型的 “引用” 机制。**为此，Vue 提供了一个 [`ref()`](https://cn.vuejs.org/api/reactivity-core.html#ref) 方法来允许我们创建可以使用任何值类型的响应式 **ref**：

`ref()` 将传入参数的值包装为一个带 `.value` 属性的 ref 对象：

```html
<script setup>
  import {ref} from "vue";
  const count = ref(0)
  console.log("count:", count)
  console.log("count.value:", count.value)
  const func = function (){
    count.value++
  }
</script>

<template>
  <div @click="func">{{count}}</div>
</template>
```

注意在模板中引用ref定义的响应式变量不需要加.value

和响应式对象的属性类似，ref 的 `.value` 属性也是响应式的。同时，当值为对象类型时，会用 `reactive()` 自动转换它的 `.value`。

一个包含对象类型值的 ref 可以响应式地替换整个对象：

```javascript
const objectRef = ref({ count: 0 })

// 这是响应式的替换
objectRef.value = { count: 1 }
```

ref 被传递给函数或是从一般对象上被解构时，不会丢失响应性：

```javascript
const obj = {
  foo: ref(1),
  bar: ref(2)
}

// 该函数接收一个 ref
// 需要通过 .value 取值
// 但它会保持响应性
callSomeFunction(obj.foo)

// 仍然是响应式的
const { foo, bar } = obj
```

简言之，`ref()` 让我们能创造一种对任意值的 “引用”，并能够在不丢失响应性的前提下传递这些引用。这个功能很重要，因为它经常用于将逻辑提取到 [组合函数](https://cn.vuejs.org/guide/reusability/composables.html) 中。

### ref在模板中的解包

当 ref 在模板中作为顶层属性被访问时，它们会被自动“解包”，所以不需要使用 `.value`。下面是之前的计数器例子，用 `ref()` 代替：

```html
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }} <!-- 无需 .value -->
  </button>
</template>
```

请注意，仅当 ref 是模板渲染上下文的顶层属性时才适用自动“解包”。 例如， foo 是顶层属性，但 object.foo 不是。

```html
<script setup>
  import {ref} from "vue";
  const object = {foo: ref(1)}

</script>

<template>
    <!--返回的是[object object]1-->
  <div>{{object.foo + 1}}</div>
</template>
```

因为 `object.foo` 是一个 ref 对象。我们可以通过将 `foo` 改成顶层属性来解决这个问题：

```html
<script setup>
  import {ref} from "vue";
  const object = {foo: ref(1)}
  const {foo} = object
</script>

<template>
  <div>{{foo + 1}}</div>
</template>
```

需要注意的是，如果一个 ref 是文本插值（即一个 `{{ }}` 符号）计算的最终值，它也将被解包。因此下面的渲染结果将为 `1`：

```
{{ object.foo }}
```

这只是文本插值的一个方便功能，相当于 `{{ object.foo.value }}`。

### ref 在响应式对象中的解包

当一个 `ref` 被嵌套在一个响应式对象中，作为属性被访问或更改时，它会自动解包，因此**会表现得**和一般的属性一样：（即再次访问或更改其本身不会有任何问题）

```javascript
const count = ref(0)
const state = reactive({
  count
})

console.log(state.count) // 0

state.count = 1
console.log(count.value) // 1
```

如果将一个新的 ref 赋值给一个关联了已有 ref 的属性，那么它会替换掉旧的 ref：

```javascript
const otherCount = ref(2)

state.count = otherCount
console.log(state.count) // 2
// 原始 ref 现在已经和 state.count 失去联系
console.log(count.value) // 1
```

只有当嵌套在一个深层响应式对象内时，才会发生 ref 解包。当其作为[浅层响应式对象](https://cn.vuejs.org/api/reactivity-advanced.html#shallowreactive)的属性被访问时不会解包。

### 数组和集合类型的ref解包

跟响应式对象不同，当 ref 作为响应式数组或像 `Map` 这种原生集合类型的元素被访问时，不会进行解包。

```javascript
const books = reactive([ref('Vue 3 Guide')])
// 这里需要 .value
console.log(books[0].value)

const map = reactive(new Map([['count', ref(0)]]))
// 这里需要 .value
console.log(map.get('count').value)
```

### 响应性语法糖 Experimental

相对于普通的 JavaScript 变量，我们不得不用相对繁琐的 `.value` 来获取 ref 的值。这是一个受限于 JavaScript 语言限制的缺点。然而，通过编译时转换，我们可以让编译器帮我们省去使用 `.value` 的麻烦。

[(108条消息) vue3中使用ref语法糖_Web_boom的博客-CSDN博客_vue3怎么使用ref](https://blog.csdn.net/Android_boom/article/details/127188549?ops_request_misc=%7B%22request%5Fid%22%3A%22167103593316782412529779%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=167103593316782412529779&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-2-127188549-null-null.142^v68^control,201^v4^add_ask,213^v2^t3_control2&utm_term=%24ref is not defined&spm=1018.2226.3001.4187)

```html
<script setup>
  import {$ref} from "vue/macros";
  let num = $ref(0)
  const numIncrement = function (){
    num++
  }
</script>

<template>
  <div @click="numIncrement">{{num}}</div>
</template>

<style scoped>

</style>
```

你可以在 [响应性语法糖](https://cn.vuejs.org/guide/extras/reactivity-transform.html) 章节中了解更多细节。请注意它仍处于实验性阶段，在最终提案落地前仍可能发生改动。

## 计算属性

```html
<script setup>
import { reactive, computed } from 'vue'

const author = reactive({
  name: 'John Doe',
  books: [
    'Vue 2 - Advanced Guide',
    'Vue 3 - Basic Guide',
    'Vue 4 - The Mystery'
  ]
})

// 一个计算属性 ref
const publishedBooksMessage = computed(() => {
  return author.books.length > 0 ? 'Yes' : 'No'
})
</script>

<template>
  <p>Has published books:</p>
  <span>{{ publishedBooksMessage }}</span>
</template>
```

我们在这里定义了一个计算属性 `publishedBooksMessage`。`computed()` **方法期望接收一个 getter 函数，返回值为一个计算属性 ref**。和其他一般的 ref 类似，你可以通过 `publishedBooksMessage.value` 访问计算结果。计算属性 ref 也会在模板中自动解包，因此在模板表达式中引用时无需添加 `.value`。

### 计算属性缓存 vs 方法函数

利用函数：

```javascript
// 组件中
function calculateBooksMessage() {
  return author.books.length > 0 ? 'Yes' : 'No'
}
```

若我们将同样的函数定义为一个方法而不是计算属性，两种方式在结果上确实是完全相同的，然而，不同之处在于**计算属性值会基于其响应式依赖被缓存**。一个计算属性仅会在其响应式依赖更新时才重新计算。这意味着只要 `author.books` 不改变，无论多少次访问 `publishedBooksMessage` 都会立即返回先前的计算结果，而不用重复执行 getter 函数。

这也解释了为什么下面的计算属性永远不会更新，因为 `Date.now()` 并不是一个响应式依赖：

```javascript
const now = computed(() => Date.now())
```

相比之下，方法调用**总是**会在重渲染发生时再次执行函数。

为什么需要缓存呢？想象一下我们有一个非常耗性能的计算属性 `list`，需要循环一个巨大的数组并做许多计算逻辑，并且可能也有其他计算属性依赖于 `list`。没有缓存的话，我们会重复执行非常多次 `list` 的 getter，然而这实际上没有必要！如果你确定不需要缓存，那么也可以使用方法调用。

### 可写计算属性

计算属性默认是只读的。当你尝试修改一个计算属性时，你会收到一个运行时警告。只在某些特殊场景中你可能才需要用到“可写”的属性，你可以通过同时提供 getter 和 setter 来创建：

```html
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('John')
const lastName = ref('Doe')

const fullName = computed({
  // getter
  get() {
    return firstName.value + ' ' + lastName.value
  },
  // setter
  set(newValue) {
    // 注意：我们这里使用的是解构赋值语法
    [firstName.value, lastName.value] = newValue.split(' ')
  }
})
</script>
```

现在当你再运行 `fullName.value = 'John Doe'` 时，setter 会被调用而 `firstName` 和 `lastName` 会随之更新。

### 最佳实践

**Getter 不应有副作用**

计算属性的 getter 应只做计算而没有任何其他的副作用，这一点非常重要，请务必牢记。举例来说，**不要在 getter 中做异步请求或者更改 DOM**！一个计算属性的声明中描述的是如何根据其他值派生一个值。因此 getter 的职责应该仅为计算和返回该值。在之后的指引中我们会讨论如何使用[监听器](https://cn.vuejs.org/guide/essentials/watchers.html)根据其他响应式状态的变更来创建副作用。

**避免直接修改计算属性值**

从计算属性返回的值是派生状态。可以把它看作是一个“临时快照”，每当源状态发生变化时，就会创建一个新的快照。更改快照是没有意义的，因此计算属性的返回值应该被视为只读的，并且永远不应该被更改——应该更新它所依赖的源状态以触发新的计算。

## Class与Style绑定

渲染多个class

```
<div :class="[activeClass, errorClass]"></div>
```

```
const activeClass = ref('active')
const errorClass = ref('text-danger')
```

有条件渲染**某一个**class

```html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

`errorClass` 会一直存在，但 `activeClass` 只会在 `isActive` 为真时才存在。

然而，这可能在有多个依赖条件的 class 时会有些冗长。因此也可以在数组中嵌套对象：

```html
<div :class="[{ active: isActive }, errorClass]"></div>
```

### 在组件上使用

对于只有一个根元素的组件，当你使用了 `class` attribute 时，这些 class 会被添加到根元素上，并与该元素上已有的 class 合并。

举例来说，如果你声明了一个组件名叫 `MyComponent`，模板如下：

```
<!-- 子组件模板 -->
<p class="foo bar">Hi!</p>
```

在使用时添加一些 class：

```
<!-- 在使用组件时 -->
<MyComponent class="baz boo" />
```

渲染出的 HTML 为：

```
<p class="foo bar baz boo">Hi</p>
```

Class 的绑定也是同样的：

```
<MyComponent :class="{ active: isActive }" />
```

当 `isActive` 为真时，被渲染的 HTML 会是：

```
<p class="foo bar active">Hi</p>
```

如果你的组件有多个根元素，你将需要指定哪个根元素来接收这个 class。你可以通过组件的 `$attrs` 属性来实现指定：

```html
<!-- MyComponent 模板使用 $attrs 时 -->
<p :class="$attrs.class">Hi!</p>
<span>This is a child component</span>
```

```
<MyComponent class="baz" />
```

这将被渲染为：

```html
<p class="baz">Hi!</p>
<span>This is a child component</span>
```

## 条件渲染

### v-if与v-show

```html
<h1 v-show="ok">Hello!</h1>
```

不同之处在于 `v-show` 会在 DOM 渲染中保留该元素；`v-show` 仅切换了该元素上名为 `display` 的 CSS 属性。

`v-show` **不支持在 `<template>` 元素上使用**，也不能和 `v-else` 搭配使用。

## 列表渲染

你也可以使用 `v-for` 来遍历一个**对象的所有属性**。**遍历的顺序会基于对该对象调用 `Object.keys()` 的返回值来决定。**

```html
<script setup>
  import {$ref} from "vue/macros";
  import {reactive, ref} from "vue";

  let num = $ref(0)
  const numIncrement = function (){
    num++
  }

  const obj = reactive({
    name: 'Tom',
    age: 18,
    gender: 'male'
  })
</script>

<template>
  <div @click="numIncrement">{{num}}</div>
  <div v-for="(value, key, index) of obj">
    {{key}}-{{value}}-{{index}}
  </div>
</template>
```

第一个参数value表示值，第二个参数key表示键，第三个参数index表示位置索引

## 表单输入绑定

在前端处理表单时，我们常常需要将表单输入框的内容同步给 JavaScript 中相应的变量。手动连接值绑定和更改事件监听器可能会很麻烦：

另外，`v-model` 还可以用于各种不同类型的输入，`<textarea>`、`<select>` 元素。它会根据所使用的元素自动使用对应的 DOM 属性和事件组合：

- 文本类型的 `<input>` 和 `<textarea>` 元素会绑定 `value` property 并侦听 `input` 事件；
- `<input type="checkbox">` 和 `<input type="radio">` 会绑定 `checked` property 并侦听 `change` 事件；
- `<select>` 会绑定 `value` property 并侦听 `change` 事件。

注意：`v-model` 会忽略任何表单元素上初始的 `value`、`checked` 或 `selected` attribute。它将始终将当前绑定的 JavaScript 状态视为数据的正确来源。你应该在 JavaScript 中使用响应式系统的 API 来声明该初始值。

### 复选框

可以将多个复选框绑定到同一个数组或[集合](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)的值：

```javascript
const checkedNames = ref([])
```

```html
<div>Checked names: {{ checkedNames }}</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>
```

![image-20221215111359246](D:\WebStrom Coding\课件\image-20221215111359246.png)

**注意响应式是个数组**

### 单选按钮

```html
<div>Picked: {{ picked }}</div>

<input type="radio" id="one" value="One" v-model="picked" />
<label for="one">One</label>

<input type="radio" id="two" value="Two" v-model="picked" />
<label for="two">Two</label>
```

### 选择器

```html
<div>Selected: {{ selected }}</div>

<select v-model="selected">
    <!--空值禁用选项-->
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

**如果 `v-model` 表达式的初始值不匹配任何一个选择项，`<select>` 元素会渲染成一个“未选择”的状态。在 iOS 上，这将导致用户无法选择第一项，**因为 iOS 在这种情况下不会触发一个 change 事件。因此，**我们建议提供一个空值的禁用选项，如上面的例子所示。**

**选择器的选项可以用v-for动态渲染**

```javascript
const selected = ref('A')

const options = ref([
  { text: 'One', value: 'A' },
  { text: 'Two', value: 'B' },
  { text: 'Three', value: 'C' }
])
```

```html
<select v-model="selected">
  <option v-for="option in options" :value="option.value">
    {{ option.text }}
  </option>
</select>

<div>Selected: {{ selected }}</div>
```

### 更多请查阅官方文档

## 生命周期钩子

### 去看API

## 侦听器

计算属性允许我们声明性地计算衍生值。然而在有些情况下，我们需要在状态变化时执行一些“副作用”：例如更改 DOM，或是根据**异步操作**的结果去修改另一处的状态。

在组合式 API 中，我们可以使用 [`watch` 函数](https://cn.vuejs.org/api/reactivity-core.html#watch)在每次响应式状态发生变化时触发回调函数：

```html
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Questions usually contain a question mark. ;-)')

// 可以直接侦听一个 ref
watch(question, async (newQuestion, oldQuestion) => {
  if (newQuestion.indexOf('?') > -1) {
    answer.value = 'Thinking...'
    try {
      const res = await fetch('https://yesno.wtf/api')
      answer.value = (await res.json()).answer
    } catch (error) {
      answer.value = 'Error! Could not reach the API. ' + error
    }
  }
})
</script>

<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</template>
```

注意和选项式的API不一样了

### 侦听数据源类型

`watch` 的第一个参数可以是不同形式的“数据源”：它可以是一个 ref (包括计算属性)、一个响应式对象、一个 getter 函数、或多个数据源组成的数组：

```javascript
const x = ref(0)
const y = ref(0)

// 单个 ref
watch(x, (newX) => {
  console.log(`x is ${newX}`)
})

// getter 函数
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`sum of x + y is: ${sum}`)
  }
)

// 多个来源组成的数组
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x is ${newX} and y is ${newY}`)
})
```

**注意，你不能直接侦听响应式对象的属性值（必须监听整个响应式对象），例如:**

```javascript
const obj = reactive({ count: 0 })

// 错误，因为 watch() 得到的参数是一个 number
watch(obj.count, (count) => {
  console.log(`count is: ${count}`)
})
```

这里需要用一个返回该属性的 getter 函数：

```javascript
// 提供一个 getter 函数
watch(
  () => obj.count,
  (count) => {
    console.log(`count is: ${count}`)
  }
)
```

### 深层监听器

直接给 `watch()` 传入一个响应式对象，会隐式地创建一个深层侦听器——该回调函数在所有嵌套的变更时都会被触发：

```javascript
<script setup>
  import {reactive, ref, watch} from "vue";

  const obj = reactive({count: 0})
  watch(obj, (newValue, oldValue) => {
    console.log(`new:${newValue}, old:${oldValue}`)
  // 在嵌套的属性变更时触发
  // 注意：`newValue` 此处和 `oldValue` 是相等的
  // 因为它们是同一个对象！
  })
  function numIncrement(){
    obj.count++
  }
</script>

<template>
  <div @click="numIncrement">aaaaaa</div>
</template>
```

此处的newValue和oldValue是相等的，都是对象

![image-20221215161101476](D:\WebStrom Coding\课件\image-20221215161101476.png)

相比之下，一个返回响应式对象的 getter 函数，只有在**返回不同的对象时（监听到被改变的对象）**，才会触发回调：

```javascript
watch(
      () => obj.count,
      (newValue, oldValue) => {
    console.log(`new:${newValue}, old:${oldValue}`)
  })
```

![image-20221215161654993](D:\WebStrom Coding\课件\image-20221215161654993.png)

```javascript
 const obj = reactive({count: 0, num: 0})
  watch(
      () => obj.count,
      (newValue, oldValue) => {
    console.log(`new:${newValue}, old:${oldValue}`)
  })
  function numIncrement(){
    obj.num++
  }
```

**上述代码监听失效**

```javascript
  import {reactive, ref, watch} from "vue";

  const obj = reactive({innerObj:{
      "a": 1,
      "b": 2
    }})
  watch(
      () => obj.innerObj,
      (newValue, oldValue) => {
    console.log(`new:${newValue}, old:${oldValue}`)
  })
  function numIncrement(){
    obj.innerObj.a++
  }
```

**如果是响应式对象内嵌对象，则仍遵守上述规则，上述代码先听失效（把监听元素换成obj.innerObj.a）**

```javascript
 watch(
      () => obj.innerObj,
      (newValue, oldValue) => {
    console.log(`new:${newValue}, old:${oldValue}`)
  },{deep: true})
```

如果加上深度监听，则此处的新值和旧值均为对象（能监听到内部元素的改变）

![image-20221215163607833](D:\WebStrom Coding\课件\image-20221215163607833.png)

注意：深度侦听需要遍历被侦听对象中的所有嵌套的属性，当用于大型数据结构时，开销很大。因此请只在必要时才使用它，并且要留意性能。

### watchEffect()

`watch()` 是懒执行的：仅当数据源变化时，才会执行回调。但在某些场景中，我们希望在创建侦听器时，立即执行一遍回调。举例来说，我们想请求一些初始数据，然后在相关状态更改时重新请求数据。我们可以这样写：

```javascript
const url = ref('https://...')
const data = ref(null)

async function fetchData() {
  const response = await fetch(url.value)
  data.value = await response.json()
}

// 立即获取
fetchData()
// ...再侦听 url 变化
watch(url, fetchData)
```

我们可以用 [`watchEffect` 函数](https://cn.vuejs.org/api/reactivity-core.html#watcheffect) 来简化上面的代码。`watchEffect()` 会立即执行一遍回调函数，如果这时函数产生了副作用，Vue 会自动追踪副作用的依赖关系，自动分析出响应源。上面的例子可以重写为：

```javascript
watchEffect(async () => {
  const response = await fetch(url.value)
  data.value = await response.json()
})
```

这个例子中，回调会立即执行。在执行期间，**它会自动追踪 `url.value` 作为依赖**（和计算属性的行为类似）。每当 `url.value` 变化时，回调会再次执行。

注意：`watchEffect` 仅会在其**同步**执行期间，才追踪依赖。在使用异步回调时，只有在第一个 `await` 正常工作前访问到的属性才会被追踪。

### `watch` vs. `watchEffect`

`watch` 和 `watchEffect` 都能响应式地执行有副作用的回调。它们之间的主要区别是追踪响应式依赖的方式：

- `watch` 只追踪明确侦听的数据源。它不会追踪任何在回调中访问到的东西。另外，仅在数据源确实改变时才会触发回调。`watch` 会避免在发生副作用时追踪依赖，因此，我们能更加精确地控制回调函数的触发时机。
- `watchEffect`，则会在副作用发生期间追踪依赖。它会在同步执行过程中，自动追踪所有能访问到的响应式属性。这更方便，而且代码往往更简洁，但有时其响应性依赖关系会不那么明确。

### 回调的触发时机

当你更改了响应式状态，它可能会同时触发 Vue 组件更新和侦听器回调。

默认情况下，用户创建的侦听器回调，都会在 Vue 组件更新**之前**被调用。这意味着你在侦听器回调中访问的 DOM 将是被 Vue 更新之前的状态。

如果想在侦听器回调中能访问被 Vue 更新**之后**的 DOM，你需要指明 `flush: 'post'` 选项：

```javascript
watch(source, callback, {
  flush: 'post'
})

watchEffect(callback, {
  flush: 'post'
})
```

后置刷新的 `watchEffect()` 有个更方便的别名 `watchPostEffect()`：

```javascript
import { watchPostEffect } from 'vue'

watchPostEffect(() => {
  /* 在 Vue 更新后执行 */
})
```

例如：

```html
<script setup>
  import {reactive, ref, watch, watchEffect} from "vue";

  const num = ref(0)
  function numIncrement(){
    num.value++
    console.log("numIncrement:", num)
  }

  watch(num, () => {
    console.log("numInWatch:", num.value)
  })

  watch(num, () => {
    console.log("numInWatchPost", num.value)
  },{flush: "post"})
  watchEffect(() => {
    console.log("numInWatchEffect", num.value)
  })
</script>

<template>
  <div @click="numIncrement">aaaaaa</div>
</template>
```

**上面有两个需要注意的地方，watch在监听ref时监听引用变量，但是在回调函数内在获取ref响应式变量仍需使用.value；注意post指的是Vue更新之后再执行回调，而不是指对应监听的参数改变前还是改变后**

### 停止侦听器

在 `setup()` 或 `<script setup>` 中用同步语句创建的侦听器，会自动绑定到宿主组件实例上，并且会在宿主组件卸载时自动停止。因此，在大多数情况下，你无需关心怎么停止一个侦听器。

一个关键点是，侦听器必须用**同步**语句创建：如果用异步回调创建一个侦听器，那么它不会绑定到当前组件上，你必须手动停止它，以防内存泄漏。如下方这个例子：

```html
<script setup>
import { watchEffect } from 'vue'

// 它会自动停止
watchEffect(() => {})

// ...这个则不会！
setTimeout(() => {
  watchEffect(() => {})
}, 100)
</script>
```

要手动停止一个侦听器，请调用 `watch` 或 `watchEffect` 返回的函数：

```javascript
const unwatch = watchEffect(() => {})

// ...当该侦听器不再需要时
unwatch()
```

注意，需要异步创建侦听器的情况很少，请尽可能选择同步创建。如果需要等待一些异步数据，你可以使用条件式的侦听逻辑：

```javascript
// 需要异步请求得到的数据
const data = ref(null)

watchEffect(() => {
  if (data.value) {
    // 数据加载后执行某些操作...
  }
})
```

## 模板引用

虽然 Vue 的声明性渲染模型为你抽象了大部分对 DOM 的直接操作，但在某些情况下，我们仍然需要直接访问底层 DOM 元素。要实现这一点，我们可以使用特殊的 `ref` attribute：

```html
<input ref="input">
```

`ref` 是一个特殊的 attribute，和 `v-for` 章节中提到的 `key` 类似。它允许我们在一个特定的 DOM 元素或子组件实例被挂载后，获得对它的直接引用。这可能很有用，比如说在组件挂载时将焦点设置到一个 input 元素上，或在一个元素上初始化一个第三方库。

### 访问模板引用

为了通过组合式 API 获得该模板引用，我们需要声明一个同名的 ref：

```html
<script setup>
import { ref, onMounted } from 'vue'

// 声明一个 ref 来存放该元素的引用
// 必须和模板里的 ref 同名
const input = ref(null)

onMounted(() => {
  input.value.focus()
})
</script>

<template>
  <input ref="input" />
</template>
```

注意，你只可以**在组件挂载后**才能访问模板引用。如果你想在模板中的表达式上访问 `input`，在初次渲染时会是 `null`。这是因为在初次渲染前这个元素还不存在呢！

```javascript
watchEffect(() => {
  if (input.value) {
    input.value.focus()
  } else {
    // 此时还未挂载，或此元素已经被卸载（例如通过 v-if 控制）
  }
})
```

当在 `v-for` 中使用模板引用时，对应的 ref 中包含的值是一个数组，它将在元素被挂载后包含对应整个列表的所有元素：

```html
<script setup>
import { ref, onMounted } from 'vue'

const list = ref([
  /* ... */
])

const itemRefs = ref([])

onMounted(() => console.log(itemRefs.value))
</script>

<template>
  <ul>
    <li v-for="item in list" ref="itemRefs">
      {{ item }}
    </li>
  </ul>
</template>
```

应该注意的是，ref 数组**并不**保证与源数组相同的顺序。

![image-20221216000916203](D:\WebStrom Coding\课件\image-20221216000916203.png)

### 函数模板引用

除了使用字符串值作名字，`ref` attribute 还可以绑定为一个函数，会在每次组件更新时都被调用。该函数会收到元素引用作为其第一个参数：

```html
<script setup>
import { ref, onMounted, watch } from 'vue'
const inputEl = ref(null)
watch(inputEl,()=>{
  console.log("inputEl:", inputEl.value)
})

</script>

<template>
  <input :ref="(el) => {inputEl = el}">
</template>
```

注意我们这里需要使用动态的 `:ref` 绑定才能够传入一个函数。当绑定的元素被卸载时，函数也会被调用一次，此时的 `el` 参数会是 `null`。你当然也可以绑定一个组件方法而不是内联函数。

### 组件上的ref

模板引用也可以被用在一个子组件上。这种情况下引用中获得的值是组件实例：

```html
<script setup>
import { ref, onMounted } from 'vue'
import Child from './Child.vue'

const child = ref(null)

onMounted(() => {
  // child.value 是 <Child /> 组件的实例
})
</script>

<template>
  <Child ref="child" />
</template>
```

如果一个子组件使用的是选项式 API 或没有使用 `<script setup>`，被引用的组件实例和该子组件的 `this` 完全一致，这意味着父组件对子组件的每一个属性和方法都有完全的访问权。这使得在父组件和子组件之间创建紧密耦合的实现细节变得很容易，当然也因此，应该只在绝对需要时才使用组件引用。大多数情况下，你应该首先使用标准的 props 和 emit 接口来实现父子组件交互。

有一个例外的情况，使用了 `<script setup>` 的组件是**默认私有**的：一个父组件无法访问到一个使用了 `<script setup>` 的子组件中的任何东西，除非子组件在其中通过 `defineExpose` 宏显式暴露：

```html
<script setup>
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({
  a,
  b
})
</script>
```

当父组件通过模板引用获取到了该组件的实例时，得到的实例类型为 `{ a: number, b: number }` (ref 都会自动解包，和一般的实例一样)。

## 组件基础

组件可以被重用任意多次：

```html
<h1>Here is a child component!</h1>
<ButtonCounter />
<ButtonCounter />
<ButtonCounter />
```

你会注意到，每当点击这些按钮时，每一个组件都维护着自己的状态，是不同的 `count`。这是因为每当你使用一个组件，就创建了一个新的**实例**。

如果你是直接在 DOM 中书写模板 (例如原生 `<template>` 元素的内容)，模板的编译需要遵从浏览器中 HTML 的解析行为。在这种情况下，你应该需要使用 `kebab-case` 形式并显式地关闭这些组件的标签。

```
<!-- 如果是在 DOM 中书写该模板 -->
<button-counter></button-counter>
<button-counter></button-counter>
<button-counter></button-counter>
```

### 传递props

如果我们正在构建一个博客，我们可能需要一个表示博客文章的组件。我们希望所有的博客文章分享相同的视觉布局，但有不同的内容。要实现这样的效果自然必须向组件中传递数据，例如每篇文章标题和内容，这就会使用到 props。

Props 是一种特别的 attributes，你可以在组件上声明注册。要传递给博客文章组件一个标题，我们必须在组件的 props 列表上声明它。这里要用到 [`defineProps`](https://cn.vuejs.org/api/sfc-script-setup.html#defineprops-defineemits) 宏：

```html
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

**`defineProps` 是一个仅 `<script setup>` 中可用的编译宏命令，并不需要显式地导入。**声明的 props 会自动暴露给模板。`defineProps` 会返回一个对象，其中包含了可以传递给组件的所有 props：

```javascript
const props = defineProps(['title'])
console.log(props.title)
```

如果你没有使用 `<script setup>`，props 必须以 `props` 选项的方式声明，props 对象会作为 `setup()` 函数的第一个参数被传入：

```javascript
export default {
  props: ['title'],
  setup(props) {
    console.log(props.title)
  }
}
```

一个组件可以有任意多的 props，默认情况下，所有 prop 都接受任意类型的值。

当一个 prop 被注册后，可以像这样以自定义 attribute 的形式传递数据给它：

```html
<BlogPost title="My journey with Vue" />
<BlogPost title="Blogging with Vue" />
<BlogPost title="Why Vue is so fun" />
```

在实际应用中，我们可能在父组件中会有如下的一个博客文章数组：

```javascript
const posts = ref([
  { id: 1, title: 'My journey with Vue' },
  { id: 2, title: 'Blogging with Vue' },
  { id: 3, title: 'Why Vue is so fun' }
])
```

这种情况下，我们可以使用 `v-for` 来渲染它们：

```html
<BlogPost
  v-for="post in posts"
  :key="post.id"
  :title="post.title"
 />
```

例子：

```html
<template>
  <div>this is Child component</div>
  <div>{{props.title}}</div>
</template>

<script setup>
  const props = defineProps(['title'])

</script>

<style scoped>

</style>
```

**注意在子组件模板中使用先要给defineProps赋值变量**

### 监听事件

父组件可以通过 `v-on` 或 `@` 来选择性地监听子组件上抛的事件，就像监听原生 DOM 事件那样：

```html
<BlogPost
  ...
  @enlarge-text="postFontSize += 0.1"
 />
```

子组件可以通过调用内置的 [**`$emit`** 方法](https://cn.vuejs.org/api/component-instance.html#emit)，通过传入事件名称来抛出一个事件：

```html
<!-- BlogPost.vue, 省略了 <script> -->
<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
</template>
```

因为有了 `@enlarge-text="postFontSize += 0.1"` 的监听，父组件会接收这一事件，从而更新 `postFontSize` 的值。

我们可以通过 [`defineEmits`](https://cn.vuejs.org/api/sfc-script-setup.html#defineprops-defineemits) 宏来声明需要抛出的事件：

```html
<!-- BlogPost.vue -->
<script setup>
defineProps(['title'])
defineEmits(['enlarge-text'])
</script>
```

**和 `defineProps` 类似，`defineEmits` 仅可用于 `<script setup>` 之中**，并且不需要导入，它返回一个等同于 `$emit` 方法的 `emit` 函数。它可以被用于在组件的 `<script setup>` 中抛出事件，因为此处无法直接访问 `$emit`：

```html
<script setup>
const emit = defineEmits(['enlarge-text'])

emit('enlarge-text')
</script>
```

**例子父组件：**

```html
<script setup>
import { ref, onMounted, watch } from 'vue'
import Child from "@/components/Child.vue";
const title = ref("aaa")
const increment = function (){
  num.value++
  console.log("num:", num.value)
}
const num = ref(0)
</script>

<template>
  <Child :title="title" @incre="increment"></Child>
</template>
```

**子组件：**

```html
<template>
  <div>this is Child component</div>
  <div>{{props.title}}</div>
  <button @click="trigger">increment</button>
</template>

<script setup>
import {ref} from 'vue'
  const props = defineProps(['title'])
  const emit = defineEmits(['incre'])
  const trigger = function (){
    emit('incre')
  }
</script>
```

如果你没有在使用 `<script setup>`，你可以通过 `emits` 选项定义组件会抛出的事件。你可以从 `setup()` 函数的第二个参数，即 setup 上下文对象上访问到 `emit` 函数：

```javascript
export default {
  emits: ['enlarge-text'],
  setup(props, ctx) {
    ctx.emit('enlarge-text')
  }
}
```

### 通过插槽来分配内容

略

### 动态组件

```html
<script setup>
import { ref, onMounted, watch } from 'vue'
import Child from "@/components/Child.vue";
import Son from "@/components/Son.vue"
const title = ref("aaa")
const num = ref(0)
const tabs = [Child, Son]
</script>

<template>
  <Component :is="tabs[num % 2]"></Component>
  <button @click="num++">num++</button>
  <div>this is Father</div>
</template>
```

在上面的例子中，被传给 `:is` 的值可以是以下几种：

- 被注册的组件名
- 导入的组件对象

当使用 `<component :is="...">` 来在多个组件间作切换时，被切换掉的组件会被卸载。我们可以通过`<keepAlive>`强制被切换掉的组件仍然保持“存活”的状态。

**提示在tabs数组中的组件名称不需要加引号**

### DOM模板解析注意事项

如果你想在 DOM 中直接书写 Vue 模板，Vue 则必须从 DOM 中获取模板字符串。由于浏览器的原生 HTML 解析行为限制，有一些需要注意的事项。

请注意下面讨论只适用于直接在 DOM 中编写模板的情况。如果你使用来自以下来源的字符串模板，就不需要顾虑这些限制了：

- 单文件组件
- 内联模板字符串 (例如 `template: '...'`)
- `<script type="text/x-template">`

**具体细节请参考官方文档**

# 深入组件

## 组件注册

我们可以使用 [Vue 应用实例](https://cn.vuejs.org/guide/essentials/application.html)的 `app.component()` 方法，让组件在当前 Vue 应用中全局可用。

**main.js**

```javascript
import { createApp } from 'vue'
const app = createApp({})
app.component(
  // 注册的名字
  'MyComponent',
  // 组件的实现
  {
    /* ... */
  }
)
```

如果使用单文件组件，你可以注册被导入的 `.vue` 文件：

```javascript
import MyComponent from './App.vue'
app.component('MyComponent', MyComponent)
```

`app.component()` 方法可以被链式调用：

```javascript
app
  .component('ComponentA', ComponentA)
  .component('ComponentB', ComponentB)
  .component('ComponentC', ComponentC)
```

全局注册的组件可以在此应用的任意组件的模板中使用，意味着这几个组件都可以在彼此内部使用

## 局部注册

全局注册虽然很方便，但有以下几个问题：

1. 全局注册，但并没有被使用的组件无法在生产打包时被自动移除 (也叫“tree-shaking”)。如果你全局注册了一个组件，即使它并没有被实际使用，它仍然会出现在打包后的 JS 文件中。
2. 全局注册在大型项目中使项目的依赖关系变得不那么明确。在父组件中使用子组件时，不太容易定位子组件的实现。和使用过多的全局变量一样，这可能会影响应用长期的可维护性。

相比之下，局部注册的组件需要在使用它的父组件中显式导入，并且只能在该父组件中使用。它的优点是使组件之间的依赖关系更加明确，并且对 tree-shaking 更加友好。

在使用 `<script setup>` 的单文件组件中，导入的组件可以直接在模板中使用，无需注册：

```html
<script setup>
import ComponentA from './ComponentA.vue'
</script>


<template>
  <ComponentA />
</template>
```

全局注册虽然很方便，但有以下几个问题：

1. 全局注册，但并没有被使用的组件无法在生产打包时被自动移除 (也叫“tree-shaking”)。如果你全局注册了一个组件，即使它并没有被实际使用，它仍然会出现在打包后的 JS 文件中。
2. 全局注册在大型项目中使项目的依赖关系变得不那么明确。在父组件中使用子组件时，不太容易定位子组件的实现。和使用过多的全局变量一样，这可能会影响应用长期的可维护性。

相比之下，局部注册的组件需要在使用它的父组件中显式导入，并且只能在该父组件中使用。它的优点是使组件之间的依赖关系更加明确，并且对 tree-shaking 更加友好。

在使用 `<script setup>` 的单文件组件中，导入的组件可以直接在模板中使用，无需注册：

```html
<script setup>
import ComponentA from './ComponentA.vue'
</script>

<template>
  <ComponentA />
</template>
```

如果没有使用 `<script setup>`，则需要使用 `components` 选项来显式注册：

```javascript
import ComponentA from './ComponentA.js'

export default {
  components: {
    ComponentA
  },
  setup() {
    // ...
  }
}
```

对于每个 `components` 对象里的属性，它们的 key 名就是注册的组件名，而值就是相应组件的实现。上面的例子中使用的是 ES2015 的缩写语法，等价于：

```javascript
export default {
  components: {
    ComponentA: ComponentA
  }
  // ...
}
```

请注意：**局部注册的组件在后代组件中并\*不\*可用**。在这个例子中，`ComponentA` 注册后仅在当前组件可用，而在任何的子组件或更深层的子组件中都不可用。

### 组件名格式

在整个指引中，我们都使用 PascalCase 作为组件名的注册格式，这是因为：

1. PascalCase 是合法的 JavaScript 标识符。这使得在 JavaScript 中导入和注册组件都很容易，同时 IDE 也能提供较好的自动补全。
2. `<PascalCase />` 在模板中更明显地表明了这是一个 Vue 组件，而不是原生 HTML 元素。同时也能够将 Vue 组件和自定义元素 (web components) 区分开来。

在单文件组件和内联字符串模板中，我们都推荐这样做。但是，PascalCase 的标签名在 DOM 模板中是不可用的，详情参见 [DOM 模板解析注意事项](https://cn.vuejs.org/guide/essentials/component-basics.html#dom-template-parsing-caveats)。

为了方便，Vue 支持将模板中使用 kebab-case 的标签解析为使用 PascalCase 注册的组件。这意味着一个以 `MyComponent` 为名注册的组件，在模板中可以通过 `<MyComponent>` 或 `<my-component>` 引用。这让我们能够使用同样的 JavaScript 组件注册代码来配合不同来源的模板。

### Props声明

一个组件需要显式声明它所接受的 props，这样 Vue 才能知道外部传入的哪些是 props，哪些是透传 attribute (关于透传 attribute，我们会在[专门的章节](https://cn.vuejs.org/guide/components/attrs.html)中讨论)。

在使用 `<script setup>` 的单文件组件中，props 可以使用 `defineProps()` 宏来声明：

```html
<script setup>
const props = defineProps(['foo'])

console.log(props.foo)
</script>
```

在没有使用 `<script setup>` 的组件中，prop 可以使用 [`props`](https://cn.vuejs.org/api/options-state.html#props) 选项来声明：

```javascript
export default {
  props: ['foo'],
  setup(props) {
    // setup() 接收 props 作为第一个参数
    console.log(props.foo)
  }
}
```

注意传递给 `defineProps()` 的参数和提供给 `props` 选项的值是相同的，两种声明方式背后其实使用的都是 prop 选项。

除了使用字符串数组来声明 prop 外，还可以使用对象的形式：

```javascript
// 使用 <script setup>
defineProps({
  title: String,
  likes: Number
})
```

```javascript
// 非 <script setup>
export default {
  props: {
    title: String,
    likes: Number
  }
}
```

对于以对象形式声明中的每个属性，key 是 prop 的名称，而值则是该 prop 预期类型的构造函数。比如，如果要求一个 prop 的值是 `number` 类型，则可使用 `Number` 构造函数作为其声明的值。

对象形式的 props 声明不仅可以一定程度上作为组件的文档，而且如果其他开发者在使用你的组件时传递了错误的类型，也会在浏览器控制台中抛出警告。我们将在本章节稍后进一步讨论有关 [prop 校验](https://cn.vuejs.org/guide/components/props.html#prop-validation)的更多细节。

如果你正在搭配 TypeScript 使用 `<script setup>`，也可以使用类型标注来声明 props：

```html
<script setup lang="ts">
defineProps<{
  title?: string
  likes?: number
}>()
</script>
```

### 传递Prop的细节

**Prop名字格式**

如果一个 prop 的名字很长，应使用 camelCase 形式，因为它们是合法的 JavaScript 标识符，可以直接在模板的表达式中使用，也可以避免在作为属性 key 名时必须加上引号。

虽然理论上你也可以在向子组件传递 props 时使用 camelCase 形式 (使用 [DOM 模板](https://cn.vuejs.org/guide/essentials/component-basics.html#dom-template-parsing-caveats)时例外)，但实际上为了和 HTML attribute 对齐，我们通常会将其写为 kebab-case 形式：

```html
<MyComponent greeting-message="hello" />
```

对于组件名我们推荐使用 [PascalCase](https://cn.vuejs.org/guide/components/registration.html#component-name-casing)，因为这提高了模板的可读性，能帮助我们区分 Vue 组件和原生 HTML 元素。然而对于传递 props 来说，使用 camelCase 并没有太多优势，因此我们推荐更贴近 HTML 的书写风格。

**传递不同的值类型**

注意在传入非字符串值时，需要在props参数前面加上冒号

**使用一个对象绑定多个prop**

如果你想要将一个对象的所有属性都当作 props 传入，你可以使用[没有参数的 `v-bind`](https://cn.vuejs.org/guide/essentials/template-syntax.html#dynamically-binding-multiple-attributes)，即只使用 `v-bind` 而非 `:prop-name`。例如，这里有一个 `post` 对象：

```javascript
const post = {
  id: 1,
  title: 'My Journey with Vue'
}
```

以及下面的模板：

```javascript
<BlogPost v-bind="post" />
```

### 单项数据流

所有的 props 都遵循着**单向绑定**原则，props 因父组件的更新而变化，自然地将新的状态向下流往子组件，而不会逆向传递。这避免了子组件意外修改父组件的状态的情况，不然应用的数据流将很容易变得混乱而难以理解。

另外，每次父组件更新后，所有的子组件中的 props 都会被更新到最新值，这意味着你**不应该**在子组件中去更改一个 prop。

导致你想要更改一个 prop 的需求通常来源于以下两种场景：

**prop 被用于传入初始值；而子组件想在之后将其作为一个局部数据属性**。在这种情况下，最好是新定义一个局部数据属性，从 props 上获取初始值即可：

```javascript
const props = defineProps(['initialCounter'])

// 计数器只是将 props.initialCounter 作为初始值
// 像下面这样做就使 prop 和后续更新无关了
const counter = ref(props.initialCounter)
```

**需要对传入的 prop 值做进一步的转换**。在这种情况中，最好是基于该 prop 值定义一个计算属性：

```javascript
const props = defineProps(['size'])

// 该 prop 变更时计算属性也会自动更新
const normalizedSize = computed(() => props.size.trim().toLowerCase())
```

Vue 组件可以更细致地声明对传入的 props 的校验要求。比如我们上面已经看到过的类型声明，如果传入的值不满足类型要求，Vue 会在浏览器控制台中抛出警告来提醒使用者。这在开发给其他开发者使用的组件时非常有用。

要声明对 props 的校验，你可以向 `defineProps()` 宏提供一个带有 props 校验选项的对象，例如：

```javascript
defineProps({
  // 基础类型检查
  // （给出 `null` 和 `undefined` 值则会跳过任何类型检查）
  propA: Number,
  // 多种可能的类型
  propB: [String, Number],
  // 必传，且为 String 类型
  propC: {
    type: String,
    required: true
  },
  // Number 类型的默认值
  propD: {
    type: Number,
    default: 100
  },
  // 对象类型的默认值
  propE: {
    type: Object,
    // 对象或数组的默认值
    // 必须从一个工厂函数返回。
    // 该函数接收组件所接收到的原始 prop 作为参数。
    default(rawProps) {
      return { message: 'hello' }
    }
  },
  // 自定义类型校验函数
  propF: {
    validator(value) {
      // The value must match one of these strings
      return ['success', 'warning', 'danger'].includes(value)
    }
  },
  // 函数类型的默认值
  propG: {
    type: Function,
    // 不像对象或数组的默认，这不是一个工厂函数。这会是一个用来作为默认值的函数
    default() {
      return 'Default function'
    }
  }
})
```

一些补充细节：

- 所有 prop 默认都是可选的，除非声明了 `required: true`。
- 除 `Boolean` 外的未传递的可选 prop 将会有一个默认值 `undefined`。
- `Boolean` 类型的未传递 prop 将被转换为 `false`。这可以通过为它设置 `default` 来更改——例如：设置为 `default: undefined` 将与非布尔类型的 prop 的行为保持一致。
- 如果声明了 `default` 值，那么在 prop 的值被解析为 `undefined` 时，无论 prop 是未被传递还是显式指明的 `undefined`，都会改为 `default` 值。

当 prop 的校验失败后，Vue 会抛出一个控制台警告 (在开发模式下)。

如果使用了[基于类型的 prop 声明](https://cn.vuejs.org/api/sfc-script-setup.html#typescript-only-features) ，Vue 会尽最大努力在运行时按照 prop 的类型标注进行编译。举例来说，`defineProps<{ msg: string }>` 会被编译为 `{ msg: { type: String, required: true }}`。

## 组件事件

### 事件校验

和对 props 添加类型校验的方式类似，所有触发的事件也可以使用对象形式来描述。

要为事件添加校验，那么事件可以被赋值为一个函数，接受的参数就是抛出事件时传入 `emit` 的内容，返回一个布尔值来表明事件是否合法。

```html
<script setup>
const emit = defineEmits({
  // 没有校验
  click: null,

  // 校验 submit 事件
  submit: ({ email, password }) => {
    if (email && password) {
      return true
    } else {
      console.warn('Invalid submit event payload!')
      return false
    }
  }
})

function submitForm(email, password) {
  emit('submit', { email, password })
}
</script>
```

**注意，组件事件的事件校验是校验子组件传值是否合适，和props接受父组件再校验区别在时机上**

### v-model与子组件

如果希望子组件的表单（如输入框）和父组件的元素双向绑定，请移步至官方文档

## 透传Attributes

