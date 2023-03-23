# 第一章：基础总结深入

## 01_数据类型

### 分类与判断

1. 分类
   1. 基本类型（值类型）前三种较为常用
      * String: 任意字符串
      * Number:任意的数字，跟数字的大小、正负和是否是小数无关
      * Boolean：true/false
      * undefined：undefined命名未赋值
      * null:null
   2. 对象类型（引用）除了Object外两种都是比较特别的对象
      * Object：任意对象都是Object，包括函数和数组
      * Function：一种特别的对象（可以执行），内部包含可以运行的代码。对象用来存数据，函数用来存代码
      * Array：一种特别的对象（数值下标属性，通过下标取值，**内部数据是有序的**）
2. 判断（如何判断一个数的数据类型）
   1. typeof：**可以判断布尔值、字符串、undefined、数值、函数，不能判断null与object、object与array**
   2. instanceof：**专门用于判断对象的具体类型，不能判断基本类型**
   3. ===：三个等号不会做数据转换，两个等号会做数据转换，在写代码的时候尽量用三个等号，**可以判断null和undefined**

代码部分：

1. 基本
   * typeof**返回数据类型的字符串表达**。
     * 如果要利用typeof判断一个字面量是不是undefined，需要写`typeof a === 'undefined'`
     * **注意typeof判断返回的都是小写的string、number、boolean，不是首字母大写！！！！**
     * **如果用typeof 判断 一个null 返回的是一个'object'。**判断一个变量是否为null的最好方法是`a === null`，直接等于。同理判断一个变量是否为undefined的最好方法是`a === undefined`，因为这两种基本类型都只对应着唯一的值（null和undefined）
     * 综上，typeof能判断的基本类型有数值、字符串、布尔值、undefined
2. 对象
   * instanceof：instance是实例的意思
     * b1 instanceof Object 询问b1是否是Object的实例，返回true或false
     * 如果b1 instanceof Array 和 b2 instanceof Object 均为真，那么b1和b2也一定instanceof Object
   * 利用typeof可以判断函数类型` typeof b3 === 'function' `注意同样是小写
     * **如何直观判断一个函数类型？ 看一下表达式后边是不是有括号**
     * **知道了一个表达式是函数，如何调用？加上一个括号就能调用**
     * **面试常问：`typeof [...]`返回的是什么？是'object'，而不是'array'**

### 实例与类型

实例：实例对象

类型：类型对象

```javascript
function Person (name,age){ //构造函数：类型  是一个对象 一般来说构造函数的首字母都要大写
	this.name = name
	this.age = age
}
var p = new Person()//根据类型创建的实例（对象）当一个函数被new的时候才被称之为构造函数
Person('tom',12)//此时的Person就不是构造函数了，但不推荐这么写，因为函数首字母大写一般都是构造函数
```

### 相关问题

1. undefined和null的区别？**（面试经常问）**

   * undefined代表定义了未赋值
   * null是定义并赋值了，只是值为null

2. 什么时候给变量赋值为null呢？

   * 为什么null的typeof是一个Object呢？null明明只是一个基本类型的数据

   * 在实际开发中，变量可能需要先定义，赋值在后边，这时候要用null占位。**初始赋值为null，标明将要赋值为对象。**

     ```javascript
     //起始
     var b = null
     //确定对象就赋值
     b = ['atguigu',12]
     //最后把b设置为null是为了释放数组所占用的内存，不赋值为null对象不会释放。没有引用指向的对象叫垃圾对象，释放对象需要让变量不再指向对象，即令b=null，让b指向的对象成为垃圾对象（被浏览器的垃圾回收器回收）
     b = null
     ```

   * 初始赋值，标明将要赋值为对象
   * 结束前，让b指向的对象成为垃圾对象（被垃圾回收器回收）

3. 严格区别变量类型与数据类型
   * 如何严格的区分引用与对象？
     * 以`var c = {}`为例，**c里边存的是对象的地址（即对象的引用），对象在堆中，变量名称在栈中**。所以严格来说，变量和数据类型不一样。
     * JS本身是一个弱类型语言，变量本身没有类型
   * 数据的类型
     * 基本类型
     * 对象类型
   * **变量类型（变量值的类型）**
     * 基本类型：保存的就是基本类型数据
     * 引用类型：保存的是地址值

## 02_数据、变量和内存

### 概念

1. 什么是数据

   * 存储在内存中代表特定信息的’东西‘，本质上是0101...
   * 数据的特点：可传递，可运算
   * 一切皆数据
   * 内存中所有操作的目标：数据
     * 算数运算
     * 逻辑运算
     * 赋值运算（数据拷贝）
     * 运行函数

2. 什么是内存

   * 内存条通电后产生的可存储数据的空间（临时的）

   * 内存的产生和死亡：内存条（电路版）==>通电==>产生内存空间==>存储数据==>处理数据==>断电==>内存空间和数据都消失

   * 一块小内存中的2个数据

     * 内部存储的数据
     * 地址值数据（用来标识内存的）

   * 内存的分类：

     * 运行一段代码时，先把代码放到内存中，然后再编译，编译完成后再解析
     * 栈：存储全局变量和局部变量
     * 堆：存储对象

     ```javascript
     var obj = {name: 'Tom'}
     var a = obj
     console.log(obj.name)
     ```

     在上述代码中，**obj在栈内存中，对象在堆内存中，obj里边存的是对象的地址**，栈内存中既可以存基本数据，也可以存地址数据。**变量赋值是将一个变量（obj）保存的内容（地址或基本类型）拷贝一份放到左边的变量（a）的内存里。**

     首先我们会读取**obj的内存值（内存的内容）**，只是内存的内容是地址。**"."点的作用是从找到地址对应的内存，所以存的是地址的变量后面才能接'.'**。

     冒号左边（name）存储的是内存的标识，右边是存储的数据（Tom）。**而且name即不是全局变量也不是局部变量，是堆内存中的数据**

     只有对象的地址我们会用到，其他都不用，直接用内存中存储的数据。

3. 什么是变量

   * **可变化的量，由变量名和变量值组成**
   * 每一个变量都对应一块小内存，变量名用来查找对应的内存，变量值就是内存中保存的数据

4. 内存，数据，变量三者之间的关系

   * 内存用来存储数据的空间
   * 变量是内存的标识

### 相关问题

var a = xxx，a内存中到底保存的是什么

* xxx是基本数据，保存的就是这个数据
* xxx是对象，保存的是对象的地址值（函数是对象，所以也能用变量等于）
* xxx是变量，保存的是xxx的内存内容（可能是基本数据，也可能是地址值）

关于引用变量赋值的问题

* n个引用变量指向同一个对象，即两个变量内存储的地址（数据）一样。**通过一个变量修改对象内部数据，其他所有变量看到的是修改之后的数据**
* 两个引用变量指向同一个对象，让其中一个引用变量指向另一个对象，另一引用变量依然指向前一个对象

```javascript
var obj1 = {name: 'Tom'}
var obj2 = obj1
function fn (obj){
    obj.name = "A"
}
fn(obj1)
console.log(obj2.name) //A
```

* 在上述例子中**，obj也是指向obj1和obj2同一地址的，因为实参赋值给形参的是地址**

```javascript
var a = {age: 12}
var b = a
function fn2(obj){
	obj = {age: 15}
}
fn2(a)
console.log(a.age)//12
```

* 在上述例子中，obj的指向改变了（因为用的等于号），指向了新开辟的内存，如果用的是点.改那就是改内存了（上一个例子）
* 在函数执行完毕后，函数内部的局部变量就会自动释放，一旦释放对象就会成为垃圾对象



面试常问：在js调用时传递变量参数时，是值传递还是引用传递

* 理解1：都是值（基本值/地址值）传递 （从赋值拷贝角度说）
* 理解2：可能是值传递，也可能是引用传递（地址值）（从变量类型角度来说）

* 值传递

* 内存只有两种操作，一种是读，一种是写。除了变量被赋值是写以外，其他都是读

* **参数传递的本质实质上是一个变量赋值给另外一个变量，函数的形参是局部变量**



JS引擎如何管理内存？

1. 内存声明周期
   * 分配小内存空间，得到它的使用权
   * 存储数据，可以反复进行操作
   * 释放小内存空间

2. 释放内存：
   * 局部变量：函数执行完自动释放
   * 对象：成为垃圾回收器 ==> 垃圾回收器回收

**局部变量函数执行的时候产生，函数执行完毕时自动释放。释放和回收不一样，回收需要垃圾回收器，并不是立即执行，是间隔式执行（间隔事件非常短），是在后面某一时刻才会执行。**

```javascript
function fn(){
	var b = {}
}
fn()
```

**b是自动释放，但是b指向的对象是在后面的某个时刻由垃圾回收器回收。事实上由于回收间隔很短，释放和回收对于编程人员来说几乎是同时的**

## 03_对象

1. 什么是对象
   * 多个数据的封装体
   * 用来保存多个数据的容器
   * 一个对象代表现实世界中的一个事物
2. 为什么要用对象？
   * 统一管理多个数据
3. 对象的组成
   * 属性：是由属性名（字符串）和属性值（任意）组成
   * 方法：一种特别的属性（属性值是函数）

```javascript
  var p = {
    name: 'Tom',
    age: 12,
    setName: function (name) {
      this.name = name
    },
    setAge: function (age) {
      this.age = age
    }
  }
  console.log(p.setName)//输出的是整个函数

p.setName('Jack')
p['age'](23)
```

4. 如何访问对象内部数据？
   * .属性名：编码简单，有时候不能用
   * ['属性名']：编码复杂，可以通用



**问题：什么时候必须用['属性名']方式**

```javascript
 p['content-type'] = 'text/json'

var prop = 'xxx'
  var value = 123
  // p.prop = value  //不正确,JS不知道是把xxx作为属性名还是把prop作为属性名
  p[prop] = value
```

1. 属性名包含特殊字符：-  空格
2. 属性名不确定

## 04_函数

1. 什么是函数
   * 具有实现特定功能的n条语句的封装体
   * 只有函数是可以执行的，其他类型的数据不能执行
2. 为什么用函数
   * 提高代码复用
   * 便于阅读交流
3. 如何定义函数
   * 函数声明 `function fn1(){}`
   * 表达式 `var fn2 = function(){}`
4. 如何调用（执行）函数
   * test() 直接调用
   * obj.test() 通过对象调用（执行方法）
   * new test() new调用
   * test.all/apply(obj)：临时让test成为obj的方法进行调用

```javascript
var obj = {}
function test2(){
	this.xxx = 'atguigu'
}
test2.call(obj) //可以让一个函数成为指定任意对象的方法进行调用
console.log(obj.xxx)
```

## 05_回调函数

1. 什么函数才是回调函数 例如：dom事件回调函数和定时器回调函数
   * 你定义的
   * 你没有调用
   * 但是最终他执行了
2. 常见的回调函数
   * DOM事件的回调函数
   * 定时器回调函数
   * ajax请求回调函数
   * 声明周期回调函数

```javascript
//1. DOM事件函数
  var btn = document.getElementById('btn')
  btn.onclick = function () {
    alert(this.innerHTML)
  }

  //2. 定时器函数
  setInterval(function () {
    alert('到点啦!')
  }, 2000)
```

## 06_IIFE

1. 理解：
   * 全称：Immediately-Invoked Function Expression
   * 立即执行函数表达式（匿名函数自调用）
2. 作用：
   * 隐藏实现，外边的程序看不见
   * 不会污染外部（全局）命名空间，函数内部定义的变量是局部变量，函数执行完就被回收了
   * 用它来编码js模块

```javascript
(function(){
	...//匿名函数自调用，加第一个大的小括号，表示前面整个函数是一个整体
 })()
```

```javascript
;(function(){
	var a = 1
	function test(){
		console.log(++a)
	}
	window.$ = function(){ //向外暴露一个全局函数胡
		return {
			test: test
		}
	}
})()

$().test()
```

## 07_函数中的this

1. this是什么

   * 任何函数本质上都是通过某个对象来调用的，如果没有指定就是window

   * 所有函数内部都有一个变量this
   * 它的值是调用函数的当前对象

2. 如何确定this的值

   * test() :window
   * p.test(): p
   * **new test() :新创建的对象**
   * p.call(obj):obj

**重点看例子：**

```javascript
function Person(color) {
    console.log(this)
    this.color = color;
    this.getColor = function () {
      console.log(this)
      return this.color;
    };
    this.setColor = function (color) {
      console.log(this)
      this.color = color;
    };
  }

  Person("red"); //this是谁? window

  var p = new Person("yello"); //this是谁? p new指令是指定当前新创建的对象来调用函数的，然后这个新的对象给了p

  p.getColor(); //this是谁? p

  var obj = {};
  p.setColor.call(obj, "black"); //this是谁? obj call可以改变this的指向

  var test = p.setColor;
  test(); //this是谁? window 相当于直接setColor(),test=function(){}，而且test()前面也没有new

  function fun1() {
    function fun2() {
      console.log(this);
    }

    fun2(); //this是谁? window
  }
  fun1();
```

## 补充：关于语句分号问题

```
<!--
1. js一条语句的后面可以不加分号
2. 是否加分号是编码风格问题, 没有应该不应该，只有你自己喜欢不喜欢
3. 在下面2种情况下不加分号会有问题
  * 小括号开头的前一条语句
  * 中方括号开头的前一条语句
4. 解决办法: 在行首加分号
5. 强有力的例子: vue.js库
6. 知乎热议: https://www.zhihu.com/question/20298345
-->
```

```javascript
// 情形一: 小括号开头的前一条语句
var a = 3
;(function () {

})
/*
错误理解: 将3看成是函数调用
 var a = 3(function () {

 })
 */

// 情形二: 中方括号开头的前一条语句
var b = a
;[1, 3, 5].forEach(function (item) {
  console.log(item)
})
/*
 错误理解:
 a = b[5].forEach(function(e){
 console.log(e)
 })
 
 */
```

# 第二章：原型与原型链

## 01_函数的prototype

1. 函数的prototype属性
   * 每一个函数都有一个**prototype属性**，它默认指向一个Object空对象（即称为：**原型对象**）
   * 原型对象中有一个属性constructor，它指向函数对象
2. 给原型对象添加属性（一般都是方法）
   * 作用：函数的所有实例对象自动拥有原型中的属性（方法）

```javascript
console.log(Date.prototype, typeof Date.prototype) //"object"
  function fn() {

  }
  console.log(fn.prototype, typeof fn.prototype)
```

**prototype属性默认指向一个Object空对象：如果我们定义了一个函数，prototype中除了\__proto__和constructor没有任何方法和属性（没有我们的属性）**

可以自己向原型中添加属性（一般是方法），代表实例对象可以访问

```javascript
fn.prototype.test = function(){
	console.log("test()")
}
console.log(fn.prototype)

var fun = new fn()
fun.test()
```

原型对象中有一个属性constructor，它指向函数对象（**constructor是一个引用变量属性**），即：

```javascript
  console.log(Date.prototype.constructor===Date)//Date是一个函数对象
  console.log(fn.prototype.constructor===fn)
```

![image-20221107174244639](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221107174244639.png)

prototype指向的是type的原型对象，constructor指向的是type，两个都是引用变量

小贴士：Shift + F6 可以更改所有的相同的函数名

## 02_显示原型与隐式原型

1. 每个函数function都有一个prototype，即显式原型（属性）
2. 每个**实例对象(见1.1的类型与实例)**都有一个\__proto__，可称为隐式原型（属性），proto也是一个引用类型变量
3. **对象的隐式原型的值为其对应构造函数的显式原型的值**

```javascript
  function Fn() {

  }
  var fn = new Fn()
  console.log(Fn.prototype, fn.__proto__)
  console.log(Fn.prototype===fn.__proto__)
```

1. 内存结构(图)

![D277F913D7028B899C991B491BBD086A](D:\WebStrom Coding\课件\D277F913D7028B899C991B491BBD086A.png)

1. 总结:

     * 函数的prototype属性: **在定义函数时自动添加的,** 默认值是一个空Object对象。**自动添加语句：**`this.prototype = {}`

     * 对象的\__proto__属性: **创建对象时自动添加的**, 默认值为构造函数的prototype属性值。 **自动添加语句：**`this.__proto__ = Fn.prototype`

     * **程序员能直接操作显式原型, 但不能直接操作（添加之前）隐式原型**(ES6之前)

```javascript
  //给原型添加方法，可以操作显示原型
  Fn.prototype.test = function () {
    console.log('test()')
  }
  //Fn的实例对象中没有test方法，顺着原型链往下找找到了
  fn.test()
```

## 03_原型链(这节课难度直接封神)

1. 原型链(图解)

     * 访问一个对象的属性时，
       * 先在自身属性中查找，找到返回
       * 如果没有, 再沿着\__proto__这条链向上查找, 找到返回
       * 如果最终没找到, 返回undefined
     * 别名: **隐式原型链**（因为**找属性是本着隐式原型找的，都是拿着实例去.属性，所以又叫隐式原型链**），跟别人表达的时候要把隐式原型链表达出来
     * 作用: 查找对象的属性(方法)

```javascript
function Fn() {
    this.test1 = function () {
      console.log('test1()')
    }
  }
  console.log(Fn.prototype)
  Fn.prototype.test2 = function () {
    console.log('test2()')
  }
```

* * **小问题：代码中打印的Fn.prototype中有没有test2属性？ --有**

    * 这里的Fn.prototype里边有test2这个属性，但是没有test2的实质性内容

    * JS申明的变量都会提前，所以这里的prototype在一开始就知道有一个叫test2属性的

    * 但是实际的内容prototype不知道，只有`Fn.prototype.test2 = function(){...}`执行完之后才知道，可以在这个定义函数下面一行再输出一遍prototype验证一下test2方法

    * 会发现test2方法和第一次打印不一样了，此时test2正式作为函数加入了prototype中

    * 每一个“.”代表用地址索引内容，就是顺着箭头往前找的意思

  * **Fn的prototype（图中的空对象）是Object的实例对象，是Object的实例，所以里边有一个属性叫\__proto\_\_**

  * **上面说的Object是一个全局的函数对象，也是在所有函数定义之前就定义好得函数对象**

  * 即**先有了Object函数**（Object函数对象/Object构造函数/Object类型对象）**显式原型**（显式原型对象/prototype）**的属性值**（地址/图中的0x345）**，然后才有Object空对象**（Object的实例/Object的实例对象/Fn的Prototype指向的对象）**隐式原型**（隐式原型对象/\__proto__）**的属性值**（地址/0x345)**和上面的Object.prototype相等。**

  * 为啥相等？**因为Fn函数对象构造时自动生成的Object空对象就是Object函数的实例，实例的proto和类型的prototype必然相等**
    * Object函数对象的原型对象里边东西很多了，**注意Object的原型中\__proto__的值是null**。
      * 如果顺着原型链找某一个不是函数的属性，则返回的是undefined
      * 如果顺着原型链找某一个是函数的属性（后边加个括号），报错xxx is not a function

  * **即Object的原型对象就是原型链的尽头**

![IMG_0195](D:\WebStrom Coding\课件\IMG_0195.PNG)

注：图中的Object空对象指的是Fn的原型对象

2. 构造函数/原型/实体对象的关系（图解）

   ```javascript
   var o1 = new Object()
   var o2 = {}
   ```

   * 注意以上两种构造类的方法都会产生原型对象

![image-20221107215020377](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221107215020377.png)

3. 构造函数/原型/实体对象的关系2（图解）

```javascript
function Foo(){}
var Foo = new Function() //这一行和上一行代码执行效果和内容一样
```

紫色线在总结与补充会讲

![image-20221107225612063](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221107225612063.png)

* * 函数对象有一个显式原型属性（prototype），默认是一个空的Object对象
  * **所有的实例对象都有隐式属性，函数是一个实例对象，是Function的实例**，所以会有左边那条(Foo created by Function)的线。**即所有的函数都有两个原型属性，一个是显式原型，一个是隐式原型**
  * **隐式原型(Foo.\__proto__)指向的是Function的显式原型（Function.prototype）**
  * 注意，**Function自身的隐式原型和显式原型是一样的（Function.prototype === Function.\__proto__）**，即`Function = new Function()` 。
  * 别的函数都不会有这个特点，**因为别的函数的显式原型是new Object**，**但是所有函数的隐式原型都是一样的，因为都是new Function产生的**，和Function.prototype相等，而且是不会变的
  * **实例函数的隐式原型是构造函数的显式模型，看最上面的红框，任何函数都是new Function得来的，所有函数的隐式原型都是Function的显示原型**，所以Object.\__proto = Function.prototype

### 总结与补充：

1. **函数的显式原型指向的对象：默认是空的Object实例对象，有一个函数特殊：**
   * Object()，如图其prototype指向的是Object.prototype，Object的显示原型对象的\__proto__属性是null。而空的Object实例对象只有 \_\_proto\_\_（**隐式原型**）属性，指向的是Object.prototype和Object显式原型一样。
   * **即空Object实例对象是Object的实例，可以用Instanceof验证，而Object.prototype则不是，和Object实例对象的\_\_proto\_\_指向的原型一样。**
   * **Function显式原型对象也是Object的实例，遵循默认规则，所以上面那张图中有一根线没画上（用紫色线补上了）**

![image-20221107232922117](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221107232922117.png)

```javascript
console.log(Fn.prototype instanceof Object) //true
console.log(Object.prototype instanceof Object) //false
console.log(Function.prototype instanceof Object) //true
```

2. 所有函数都是Function的实例，包括Function自身
3. Object的原型对象是原型链的尽头，其\__proto__的值是null

## 04_原型链的属性问题

1. 读取对象的属性值时: 会自动到原型链中查找
2. 设置对象的属性值时: 不会查找原型链, 如果当前对象中没有此属性, 直接添加此属性并设置其值
3. **方法一般定义在原型中, 属性一般通过构造函数定义在对象本身上**

```javascript
  var Fn = function (){

  }
  Fn.prototype.a = 'xxx'
  var fn1 = new Fn()
  console.log(fn1.a)

  var fn2 = new Fn()
  fn2.a = 'yyy'
  console.log(fn1.a, fn2.a) //xxx yyy
```

* 先从自身找属性值，再到原型链中查找



```javascript
  function Person(name, age) {
    this.name = name;
    this.age = age;
  }
  Person.prototype.setName = function (name) {
    this.name = name;
  }
  var p1 = new Person('Tom', 12)
  p1.setName('Jack')
  var p2 = new Person('Bob', 23)
  p2.setName = new Person("Deng",99)
```

* 注意，上面的p1.name已经不是Jack而是Tom了，**构造函数的显式原型里边的this指向构造函数**
* **p1和p2两个对象的属性值不同，但是他们的隐式原型相同**，即把方法塞到了原型中`p1.__proto__ === p2.__proto__`



## 05_探索instanceof

1. instanceof是如何判断的?

     * 表达式: A instanceof B；A是实例对象，B是函数
     * **如果B函数的显式原型对象在A对象的原型链上, 返回true, 否则返回false**
2. Function是通过new自己产生的实例

![IMG_0201](D:\WebStrom Coding\课件\IMG_0201.PNG)

* 在图中，最上方是object的原型，如果这一排原型有一个是B的显式原型，那结果就返回true

```javascript
function Foo(){ }
var f1 = new Foo()
console.log(f1 instanceof Foo) //true
console.log(f1 instanceof Object) //true
```

![image-20221108192320580](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221108192320580.png)



```javascript
console.log(Object instanceof Function); //true
console.log(Object instanceof Object); //true
console.log(Function instanceof Function); //true
console.log(Function instanceof Object); //true

function Foo() {}
console.log(Object instanceof  Foo); //false
```

![image-20221108192922479](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221108192922479.png)

![IMG_0204](D:\WebStrom Coding\课件\IMG_0204.PNG)

## 06_面试题(我甜蜜的全错了)

![3E8497BD07B28A35605C6113EEA6117A](D:\WebStrom Coding\课件\3E8497BD07B28A35605C6113EEA6117A.png)

![IMG_0209](D:\QQ下载和缓存\MobileFile\IMG_0209.PNG)

* 注意，f.b()是报错，因为b是个函数，要是属性就是undefined

# 第三章：执行上下文与执行上下栈 

**当函数执行时，会创建一个称为执行上下文的内部对象。一个执行上下文定义了一个函数执行时的环境；**

## 01_变量提升与函数提升

1. 变量声明提升

     * **通过var定义(声明)的变量**, 在定义语句之前就可以访问到
     * 值: undefined
2. 函数声明提升

     * **通过function声明的函数**, 在之前就可以直接调用**（直接可以执行）**
     * 值: 函数定义(对象)
3. 问题: 变量提升和函数提升是如何产生的?

**一道面试题： 输出什么？ --undefined，因为函数内优先找的是函数内定义的变量**

```javascript
  var a = 4
  function fn () {
    console.log(a)
    var a = 5
  }
  fn()
```

```javascript
  /*变量提升*/
  console.log(a1) //可以访问, 但值是undefined
  /*函数提升*/
  a2() // 可以直接调用

  var a1 = 3
  function a2() {
    console.log('a2()')
  }
```



**下面例子中，fn3不能调用，因为用var定义的属于变量提升**

```javascript
fn3()
var fn3 = function(){
	console.log("fn3()")
}
```



## 02_执行上下文

1. 代码分类(位置)

     * 全局代码
     * 函数（局部）代码
2. 全局执行上下文

   * 在执行全局代码前将window确定为全局执行上下文
   * 对全局数据进行预处理
       * v**ar定义的全局变量==>undefined, 添加为window的属性**
       * **function声明的全局函数==>赋值(fun), 添加为window的方法**
       * **this==>赋值(window)**
     * 开始执行全局代码
```javascript
  console.log(a1)
  console.log(a2)
  console.log(a3)
  console.log(this)

  var a1 = 3
  var a2 = function () {
    console.log('a2()')
  }
  function a3() {
    console.log('a3()')
  }
```

光写一个a3和window.a3是一样的

3. 函数执行上下文

     * 在调用函数, **准备执行函数体之前, 创建对应的函数执行上下文**

   * 对局部数据进行预处理
       * 形参变量==>赋值(实参)==>添加为执行上下文的属性
         * arguments==>赋值(实参列表), 添加为执行上下文的属性
         * var定义的局部变量==>undefined, 添加为执行上下文的属性
         * function声明的函数 ==>赋值(fun), 添加为执行上下文的方法
         * this==>赋值(调用函数的对象)

     * 开始执行函数体代码
```javascript
  function fn(x, y) {
    console.log(x, y)
    console.log(b1)
    console.log(b2)
    console.log(arguments) //arguments是传入的实参，伪数组（或者叫类数组）
    console.log(this)

    // console.log(b3)

    var b1 = 5
    function b2 () {

    }
    b3 = 6
  }
  fn()
```

注：**上下文对象其实不是一个真实的对象，只是把它理解成对象了**，**是一个栈里边的区域。**局部变量存在栈中（全局也存在栈中），把上面提到的arguments、var、function、this都放到这里来。注意对象是放在堆内存里边的，只不过栈中的上下文存放了函数的内容，而且这一部分空间是封闭的，函数外边获取不到函数内部的变量。而且在一个函数被调用执行完毕后，会清理其内存空间，所有的局部变量都会释放，方便空间复用。**如果不调用函数则函数不会创建函数上下文**

## 03_执行上下文栈

1. 在全局代码执行前, JS引擎就会创建一个栈来存储管理所有的执行上下文对象
2. 在全局执行上下文(window)确定后, 将其添加到栈中(压栈)
3. 在函数执行上下文创建后（函数被调用时，注意不是定义时）, 将其添加到栈中(压栈)
4. 在当前函数执行完后,将栈顶的对象移除(出栈)
5. 当所有的代码执行完后, 栈中只剩下window（**栈底只可能是window**）

```javascript
                            //1. 进入全局执行上下文
  var a = 10
  var bar = function (x) {
    var b = 5
    foo(x + b)              //3. 进入foo执行上下文 调用foo的时候foo是存在的，因为在执行bar之前已经把函数赋值给foo
  }
  var foo = function (y) { //赋值语句执行不代表函数执行
    var c = 5
    console.log(a + c + y)
  }
  bar(10)                    //2. 进入bar函数执行上下文
```

![image-20221108215127415](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221108215127415.png)

![image-20221108215921644](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221108215921644.png)

例题：f1里边调f2，f2里边调f3，问栈内最多有几个上下文？ ---4个

例题：f1里边先调f2后调f3，问栈内最多有几个上下文？ ---3个

## 04_面试题

1. 依次输出什么？undefined fb1 fb2 fb3 fe3 fe2 fe1 ge1 (注意实参和形参不一样)
2. 整个过程中产生了几个执行上下文？5

```javascript
console.log('gb:', i)
var i = 1
foo(1);
function foo(i){
	if(i == 4){
		return ;
	}
	console.log('fb:' + i)
	foo(i + 1)
	console.log('fe:' + i)
}
console.log('ge:' + i)
```

```javascript
  /*
  测试题1: 先预处理变量, 后预处理函数
  */
  function a() {}
  var a;
  console.log(typeof a) //'function'


  /*
  测试题2: 变量预处理, in操作符
   */
  if (!(b in window)) {
    var b = 1;
  }
  console.log(b) //undefined

  /*
  测试题3: 预处理, 顺序执行
   */
  var c = 1
  function c(c) {
    console.log(c)
      var c = 3
  }
  c(2)
```

1. **先执行变量提升，后执行函数提升**
2. if语句内的变量也会预处理
3.  报错：c is not a function。因为在提升时，变量c是undefiend，第二步是提函数，然后才是给c赋值为1，这样的话c自然就不是一个函数了。相当于：

```javascript
var c
function c(c){
	console.log(c)
    var c = 3
}
----------
c = 1
c(2)
```

# 第四章：作用域与作用域链

## 01_作用域

1. 理解

     * 就是一块"地盘", 一个代码段所在的区域
     * 它是静态的(相对于上下文对象), 在编写代码时就确定了
2. 分类

     * 全局作用域
     * 函数作用域
     * 没有块作用域(ES6有了)  块作用域：大括号作用域
```javascript
if(true){
	var c = 3
}
console.log(c) //可以输出3
```

3. 作用
     * 隔离变量，不同作用域下同名变量不会有冲突

```javascript
  var a = 10,
      b = 20
  function fn(x) {
    var a = 100,
        c = 300;
    console.log('fn()', a, b, c, x)
    function bar(x) {
      var a = 1000,
          d = 400
      console.log('bar()', a, b, c, d, x)
    }

    bar(100)
    bar(200)
  }
  fn(10)
```

**现在自己的作用域找，找不到了再到外边找**

## 02_作用域与执行上下文

1. 区别1：产生的时机

     * 全局作用域之外，每个函数都会创建自己的作用域，**作用域在函数定义时就已经确定了。**而不是在函数调用时
     * **全局执行上下文环境是在全局作用域确定之后, js代码马上执行之前创建**
     * **函数执行上下文环境是在调用函数时, 函数体代码执行之前创建**
2. 区别2：状态是否改变

     * **作用域是静态的,** 只要函数定义好了就一直存在, 且不会再变化
     * **执行上下文环境是动态的, 调用函数时创建, 函数调用结束时上下文环境就会被自动释放**
3. 联系

     * 执行上下文环境(对象)是从属于所在的作用域

     * 全局上下文环境==>全局作用域

     * 函数上下文环境==>对应的函数使用域

![image-20221108225334000](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221108225334000.png)

## 03_作用域链

1. 理解

     * 多个上下级关系的作用域形成的链, 它的方向是从下向上的(从内到外)
     * 查找变量时就是沿着作用域链来查找的
2. 查找一个变量的查找规则

     * 在当前作用域下的执行上下文中查找对应的属性, 如果有直接返回, 否则进入2

     * 在上一级作用域的执行上下文中查找对应的属性, 如果有直接返回, 否则进入3

     * 再次执行2的相同操作, 直到全局作用域, 如果还找不到就抛出找不到的异常

## 04_面试题

```javascript
/*
 问题: 结果输出多少?
 */
var x = 10;
function fn() {
  console.log(x);
}
function show(f) {
  var x = 20;
  f();
}
show(fn);
```

答案：10

不要看在哪里调用的，在函数执行前作用域已经分好了。看作用域

```javascript
  /*
   说说它们的输出情况
   */

  var fn = function () {
    console.log(fn)
  }
  fn()//function(){console.log(fn)}

  var obj = {
    fn2: function () {
      console.log(fn2)
    }
  }
  obj.fn2()// 报错了，因为没有在函数内作用域和全局作用域找到这个函数（fn2 is not defined）
```

# 第五章：闭包

 在JS中，有两大神兽。一个叫原型，一个叫闭包（基本面试必问）。

现有如下需求：点击某一个按钮，提示”点击的是第n个按钮“

```javascript
var btns = document.getElementByTagName('button') //这里的btns是伪数组
for(var i = 0, length = btns.length; i < length; i++){//把btns.length单独拿到第一个分号前就只用计算一遍了，要不然在第一个分号后边写每一次循环都要计算一遍
	var btn = btns[i]//这里的btn是一个对象
    btn.onclick = function(){
        alert("第"+(i+1)+'个')
    }
}

console.log(i) // 可以看到i，因为i是全局变量。因为局部变量定义在了函数里边
```

**如果按照上面那么写，那每一个按钮的提示都是4。**因为执行点击事件时循环已经结束了，点击事件是回调函数，当循环结束之后才会执行，此时i=3.

解决方法1：保存下标到每个btn里边

```javascript
for(var i=0,length=btns.length;i<length;i++) {
    var btn = btns[i]
    btn.index = i //这里边给btn一个Index属性
    btn.onclick = function () {
      alert('第'+(this.index+1)+'个') //这个是btn的回调所以用index要用this
    }
  }
```

解决方法2：利用闭包

```javascript
  for(var i=0,length=btns.length;i<length;i++) {//这个i是全局变量
    (function (i) {//这个i是局部变量
      var btn = btns[i]
      btn.onclick = function () {
        alert('第'+(i+1)+'个')
      }
    })(i)
  }
```

## 01_闭包的理解

1. 如何产生闭包?
     * **当一个嵌套的内部(子)函数引用了嵌套的外部(父)函数的变量(函数)时, 就产生了闭包**

2. 闭包到底是什么?

     * 使用chrome调试查看
     * **理解一: 闭包是嵌套的内部函数**(绝大部分人) **（fn2）**
     * **理解二: 包含被引用变量(函数)的对象**(极少数人) **（fn2里边的Closure）**
     * 注意: 闭包存在于嵌套的内部函数中
3. 产生闭包的条件?

     * **函数嵌套** 且**一定要调用外部函数**

     * **内部函数引用了外部函数的数据(变量/函数)** 就算内部函数只定义不执行也可以（即**执行内部函数的定义就行**）

```javascript
function fn1 () {
    var a = 3
    function fn2 () { //即使没执行fn2，函数对象也产生了。这里边是执行了函数的定义（不是执行函数）
      console.log(a)
    }
    return fn2//对于新版浏览器，如果不加return fn2，那么在给var a = 3打断点时候就看不到function fn2的声明了（被优化掉了），这里加上return fn2只是为了演示效果
  }
  fn1()
```

给a = 3打的断点，**图中scopes下的Closure就是表示闭包的意思**

<img src="C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221121225954843.png" alt="image-20221121225954843" style="zoom:80%;" />

闭包目前可以理解成一个对象，**里边有a因为这个fn2引用了外部作用域的变量，如果没有引用则不会有变量，如下图所示。**

![image-20221121230252535](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221121230252535.png)

**当一个嵌套的内部(子)函数引用了嵌套的外部(父)函数的变量(函数)时, 就产生了闭包**

![image-20221123142458398](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221123142458398.png)

## 02_常见的闭包

### 将函数作为另一个函数的返回值

```javascript
  function fn1() {
    var a = 2
    function fn2() {
      a++
      console.log(a)
    }
    return fn2
  }
  var f = fn1() // 注意这种写法调用了fn1，只是把fn2给了f
```

```javascript
  f() // 3
  f() // 4
//----------------------------↑产生了一个闭包---------------------------------
  fn1()//相当于又创建了一次内部对象，注意这里的执行和调用不一样，执行指直接写fn1，调用指利用变量指向函数
```

在上述案例中，共产生了一个闭包。**因为产生闭包的必要条件是创建内部函数（执行一次fn1会创建一次内部函数，而利用变量调用不会），创建内部函数要通过执行外部函数实现（而不是调用），而不是调用（是执行）函数。**

**注意这里第二个函数返回的是4**。说明a在不断地累加，没有消失。**局部变量在函数执行的时候产生，在函数调用结束之后死亡**。当一个函数的返回值是一个内部函数，则触发了闭包的产生条件，局部变量没有在函数结束调用的时候消失。

如果闭包不存在，那我调用f的时候找不到a了就。

现在我们给`var a = 2`和`a++`分别打一下断点，执行效果如下：

1. 在执行`var f = fn1()`时相当于执行了fn1，注意，**在`var a = 2`行的断点处，闭包已经产生了。**因为函数声明提前了（先理解为函数声明比变量声明提前），所以函数在执行这一行时已经定义完成，故产生闭包。
2. 注意因为函数内没调用fn2所以直接跳到了`return fn2`，没有执行`a++`
3. 现在执行到第一个`f()`，应该直接去执行了`a++`，因为return 的是fn2，执行f()相当于执行fn2()，执行完毕之后a = 3
4. 执行第二个`f()`时候，跳到了`a++`，在执行完毕之后a = 4

### 将函数作为实参传递给另一个函数调用

```javascript
  function showMsgDelay(msg, time) {
    setTimeout(function () {
      console.log(msg)
    }, time)
  }
  showMsgDelay('hello', 1000)
```

上述例子中存在闭包，因为他满足了：

1. 有外部showMsgDelay函数
2. 有内部回调函数
3. **内部函数引用了外部函数的变量(msg)**，注意不是time
4. 执行了外部函数

## 03_闭包的作用（C++中的类，很像啊）

1. **使用函数内部的变量在函数执行完后, 仍然存活在内存中(延长了局部变量的生命周期)**
2. **让函数外部可以操作(读写)到函数内部的数据(变量/函数)**

举个例子：在一个函数中用一个变量，我只想当外部调用函数时，能给这个变量做加一操作，而其他任何操作都不能对这个变量做（本来外部就不能对内部变量做任何操作）

```javascript
function fun1() {
    var a = 3;
    function fun2() {
      a++;            //引用外部函数的变量--->产生闭包
      console.log(a);
    }
    return fun2;
  }
  var f = fun1();  //由于f引用着内部的函数-->内部函数以及闭包都没有成为垃圾对象
```

**总结（我自己写的）：通过嵌套函数的方式向外暴露一个方法，可以让外部对函数内部的局部变量进行操作（查看或修改）**

```javascript
function fun1() {
    var a = 3;
    function fun2() {
      a++;            //引用外部函数的变量--->产生闭包
      console.log(a);
    }
    function fun3(){
        a--
        console.log(a)
    }
    return fun3;
  }
  var f = fun1();  //由于f引用着内部的函数-->内部函数以及闭包都没有成为垃圾对象
f()
f()
```

上述例子中，在执行完`var f = fun1()`结束后，函数内部的fun2还在不在？

* **不在了。因为没有人引用fun2，所以这个函数对象成为了一个垃圾对象**。相当于return就是把产生了闭包的这个内部函数作为一个对象保存，可以赋值给全局对象

问题:
  1. 函数执行完后, 函数内部声明的局部变量是否还存在?
     * a存在，因为它已经在闭包里边了
     * fun2不在闭包里边，在函数执行完之后就释放了
     * fun3也是一个变量，注意闭包里边没有fun3。**fun3这个变量被释放了**，但是指向的函数对象没有成为垃圾对象就没有释放，是`var f = fun1()`导致了函数对象没有释放，用了一个变量f指向了这个函数对象。**也就是说，闭包一直存在不消失的根本原因是f指向了函数对象（闭包），fun3里边存的是函数对象的地址，在释放完了不代表指向的函数对象变成了垃圾对象，而是f被赋值了这个函数对象的地址**
     * 总结：一般不存在，存在于闭包中的变量才**可能**存在。可能：闭包所在的函数对象（外部函数）不是垃圾对象。

```javascript
function fun1() {
    var a = 3;
    function fun2() {
      a++;            
      console.log(a);
    }
    return fun2;
  }
  fun1(); //这种情况下存在于闭包的局部变量也被释放了，因为内部函数地址没有引用指向 
```

  1. 在函数外部能直接访问函数内部的局部变量吗?
     * 不能。但是可以通过闭包让外部操作他。

## 04_闭包的生命周期

1. 产生: 在嵌套内部函数**定义执行**完时就产生了(**不是在调用**)
2. 死亡: 在嵌套的内部函数成为垃圾对象时

```javascript
function fun1() {
    //此时闭包已经产生（函数提升，内部函数对象已经创建了）
    var a = 3;
    function fun2() {
      a++;            
      console.log(a);
    }
    return fun2;
  }
  var f = fun1();
f = null //闭包死亡 包含闭包的函数对象成为了垃圾对象 引用函数对象的的变量不再引用他时
```

如果是按照下面这么写的：

```javascript
function fun1() {
    var a = 3;
    //-----
    var fun2 = function() {    //这种写法闭包在执行完虚线内的所有语句（函数定义）时才执行完，因为变量提升和函数提升不同，变量提升在没执行到变量定义的那一行时是undefined，而函数提升是在执行语句开始前已经把函数给定义了，这里的写法属于变量提升
      a++;            
      console.log(a);
    }
    //----
    return fun2;
  }
  var f = fun1();
f = null //闭包死亡 包含闭包的函数对象成为了垃圾对象 引用函数对象的的变量不再引用他时
```

## 05_闭包的应用-自定义JS模块

闭包的应用2 : 定义JS模块
  * 具有特定功能的js文件
  * 将所有的数据和功能都封装在一个函数内部(私有的)
  * 只向外暴露一个包含n个方法的对象或函数
  * 模块的使用者, 只需要通过模块暴露的对象调用方法来实现对应的功能

先创建一个JS文件：

```javascript
/**
 * 自定义模块1
 */
function coolModule() {
  //私有的数据
  var msg = 'atguigu'
  var names = ['I', 'Love', 'you']

  //私有的操作数据的函数
  function doSomething() {
    console.log(msg.toUpperCase())
  }
  function doOtherthing() {
    console.log(names.join(' '))
  }

  //向外暴露包含多个方法的对象，如果不加return 执行完就会直接把方法和变量直接释放掉了
  return {
    doSomething: doSomething,
    doOtherthing: doOtherthing
  }
}
```

在另一个html文件中引用：

```html
<script type="text/javascript" src="05_coolModule.js"></script>
<script type="text/javascript">
  var module = coolModule() //必须要执行一遍函数
  module.doSomething()
  module.doOtherthing()
</script>
```

**另一种写法：**

JS文件：

```javascript
/**
 * 自定义模块2
 */
(function (window) { //匿名函数自调用，这个window可以和下面传参的小括号里边的window可以不用写。但是当代码压缩(webpack会学到)时，形参会压缩为单个字母，这样的话就建议这样写。
  //私有的数据
  var msg = 'atguigu'
  var names = ['I', 'Love', 'you']
  //操作数据的函数
  function a() {
    console.log(msg.toUpperCase())
  }
  function b() {
    console.log(names.join(' '))
  }

  window.coolModule2 =  { //把要暴露的东西交给windows使用
    doSomething: a,
    doOtherthing: b
  }
})(window)
```

html文件：

```html
<script type="text/javascript" src="05_coolModule2.js"></script>
<script type="text/javascript">
  coolModule2.doSomething()
  coolModule2.doOtherthing()
</script>
```

## 06_内存溢出与内存泄漏（面试常问）

### 闭包的缺点

1. 缺点

     * 函数执行完后, 函数内的局部变量没有释放, 占用内存时间会变长
     * 容易造成内存泄露 解决方法：让内部函数成为垃圾对象 ---> 回收闭包对象
2. 解决

     * 能不用闭包就不用

     * 及时释放

### 内存溢出与内存泄漏

1. 内存溢出：
   * 一种程序运行出现的错误
   * 当程序运行需要的内存超过了剩余的内存时，就抛出了内存溢出的错误
2. 内存泄漏：（能正常运行，但是可用内存变小了）
   * 占用的内存没有及时释放
   * 内存泄漏积累多了就容易导致内存溢出
   * 常见的内存泄漏：
     * 意外的全局变量
     * 没有及时清理的计时器或回调函数
     * 闭包

```Javascript
  // 1. 内存溢出
  var obj = {}
  for (var i = 0; i < 100000; i++) {
    obj[i] = new Array(10000000)
  }
  console.log('------')
```

```javascript
 // 2. 内存泄露
    // 意外的全局变量
  function fn () {
    a = [] //不小心没有var定义，这里相当于window.a
  }
  fn()

    // 没有及时清理的计时器
  setInterval(function  () {
    console.log('----')
  }, 1000)
```

## 07_面试题

```javascript
//代码片段一
  var name = "The Window";
  var object = {
    name: "My Object",
    getNameFunc: function () {//不满足闭包的条件，因为外部函数没有变量可以供内部函数调用
      return function () {
        return this.name;
      };
    }
  };
  console.log(object.getNameFunc()());  //The Window
```

注意看，这种写法相当于直接调用了最里边的那个函数，里边的函数又不是构造函数，this直接指向了window。

```javascript
//自己编的一道题
 name1 = "88"
  var fn = function (){
    var name1 = "22"
    var fn2 = function (){
      console.log(this.name1)
    }
    fn2()
  }

  var f = new fn() //输出88，证明普通函数this指向window，如果不加this会向外找输出22
```

```javascript
//代码片段二
  var name2 = "The Window";
  var object2 = {
    name2: "My Object",
    getNameFunc: function () { //这里有闭包，变量为that
      var that = this;
      return function () {
        return that.name2;
      };
    }
  };
  console.log(object2.getNameFunc()()); //My Object
```

第二道题，getNameFunc的this指向的是对象，故输出的是object

```javascript
function fun(n, o) { //注意调用外部函数才会产生闭包
    console.log(o)
    return {
      fun: function (m) {
        return fun(m, n)
      }
    }
  }
  var a = fun(0)
  a.fun(1) //执行完这一步里边的对象被抛弃了，故产生了一次闭包但很快消失了 var xxx = a.fun(1)这样闭包才会保存。无论传值传的啥始终用的是a的闭包
  a.fun(2)
  a.fun(3) //undefined,?,?,?

//下面是连续产生了闭包
  var b = fun(0).fun(1).fun(2).fun(3) //undefined,?,?,?

  var c = fun(0).fun(1)
  c.fun(2)
  c.fun(3) //undefined,?,?,?
```

闭包有没有产生看内部的函数有没有创建

答案：undefined,0,0,0    undefined,0,1,2   undefined,0,1,1

# 第六章 面向对象高级

## 01_对象创建模式

我要创建一个人

方式一: Object构造函数模式
  * 套路: 先创建空Object对象, 再动态添加属性/方法
  * 适用场景: 起始时不确定对象内部数据
  * 问题: 语句太多

```javascript
var p = new Object()
p = {}
p.name = 'Tom'
p.age = 12
p.setName = function (name) {
  this.name = name
}
p.setaAge = function (age) {
  this.age = age
}
```

方式二: 对象字面量模式
  * 套路: 使用{}创建对象, 同时指定属性/方法
  * 适用场景: 起始时对象内部数据是确定的
  * 问题: 如果创建多个对象, 有重复代码

```javascript
  var p = { //注意这是一条赋值语句
    name: 'Tom',
    age: 23,
    setName: function (name) {
      this.name = name
    }
  }
```

方式三: 工厂模式
  * 套路: 通过工厂函数动态创建对象并返回
  * 适用场景: 需要创建多个对象
  * 问题: 对象没有一个具体的类型, 都是Object类型

```javascript
// 工厂函数: 返回一个需要的数据的函数
function createPerson(name, age) {
  var p = {
    name: name,
    age: age,
    setName: function (name) {
      this.name = name
    }
  }
  return p
}

var p1 = createPerson('Tom', 12)
var p2 = createPerson('JAck', 13)
console.log(p1)
console.log(p2)
```

方式四: 自定义构造函数模式
  * 套路: 自定义构造函数, 通过new创建对象
  * 适用场景: 需要创建多个类型确定的对象
  * 问题: 每个对象都有相同的数据, 浪费内存

```javascript
function Person(name, age) {
    this.name = name
    this.age = age
    this.setName = function (name) {
      this.name = name
    }
  }

  var p1 = new Person('Tom', 12)
  var p2 = new Person('Tom2', 13)
  console.log(p1, p1 instanceof Person)

function Student(name, age) {
    this.name = name
    this.age = age
  }
//和工厂模式不同，可以设置不同的类型，person和student
var s1 = new Student('Tom', 6666)
```

![image-20221207112002288](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221207112002288.png)

**注意两个setName是一模一样的（但不是同一个对象），想办法放在原型中**

方式六: 构造函数+原型的组合模式
  * 套路: 自定义构造函数, 属性在函数中初始化, **方法添加到原型上**
  * 适用场景: 需要创建多个类型确定的对象

```javascript
  function Person (name, age) {
    this.name = name
    this.age = age
  }
//原型的方法是给实例对象用的，原型中的this指向的的是实例对象
  Person.prototype.setName = function (name) {
    this.name = name
  }
  var p1 = new Person('Tom', 12)
  var p2 = new Person('JAck', 23)
  p1.setName('TOM3')
  console.log(p1)

  Person.prototype.setAge = function (age) {
    this.age = age
  }
  p1.setAge(23)
  console.log(p1.age)

  Person.prototype = {}
  p1.setAge(34)
  console.log(p1)
  var p3 = new Person('BOB', 12)
  p3.setAge(12)
```

上述模式中，工厂模式用的最少

## 02_继承模式

### 原型链继承

方式1: 原型链继承
  1. 套路
        1. 定义父类型构造函数
      2. 给父类型的原型添加方法
      3. 定义子类型的构造函数
      4. 创建父类型的对象赋值给子类型的原型
      5. 将子类型原型的构造属性设置为子类型
      6. 给子类型原型添加方法
      7. 创建子类型的对象: 可以调用父类型的方法
  2. 关键
        1. **子类型的原型为父类型的一个实例对象**`Sub.prototype = new Supper()`

```javascript
function Supper() { //父类型
  this.superProp = 'The super prop'
}
//原型的数据所有的实例对象都可见
Supper.prototype.showSupperProp = function () {
  console.log(this.superProp)
}

function Sub() { //子类型
  this.subProp = 'The sub prop'
}

// 子类的原型指向父类的实例
Sub.prototype = new Supper()
// 让子类型的原型的constructor指向子类型，修正Sub.prototype.constructor为Sub本身
Sub.prototype.constructor = Sub //constructor指向实例对象的构造函数，constructor在supper实例对象的原型对象中，故此处的constructor指向Supper的函数对象

Sub.prototype.showSubProp = function () {
  console.log(this.subProp)
}

// 创建子类型的实例
var sub = new Sub()
// 调用父类型的方法
sub.showSubProp()
// 调用子类型的方法
sub.showSupperProp()
```

![IMG_0287](D:\WebStrom Coding\课件\IMG_0287.PNG)

如图所示，注意：

1. 栈里边有Supper和Sub及他们的实例对象地址，把Supper的实例对象地址给省略了
1. Sub实例对象隐式原型指向的是Supper的实例对象，不是Supper的原型

### 借用构造函数继承

方式2: 借用构造函数继承(假的)
1. 套路:
   1. 定义父类型构造函数
   2. 定义子类型构造函数
   3. 在子类型构造函数中调用父类型构造
2. 关键:
   1. 在子类型构造函数中通用call()调用父类型构造函数

```javascript
  function Person(name, age) {
    this.name = name
    this.age = age
  }

  function Student(name, age, price) {
    Person.call(this, name, age)   // 相当于this.Person(name, age),把Person的this指向Student的this
    this.price = price
  }

  var s = new Student('Tom', 20, 12000)
  console.log(s.name, s.age, s.price)
```

**A.call( B,x,y )：就是把A的函数放到B中运行，x 和 y 是A方法的参数。call可以改变this的指向**

### 组合继承

在原型链继承中，父元素内部的变量（如this.superProp）都是写死的，如果要等于传入的参数需要在子对象原型链指向父对象原型时传参，但是子元素只想看到父对象原型中的方法，在这里传参不合适。

即

```javascript
function Supper(superProp) { //父类型
  this.superProp = superProp
}
Sub.prototype = new Supper(???)//不要在这里传
```

方式3: 原型链+借用构造函数的组合继承
1. 利用原型链实现对父类型对象的方法继承
2. 利用call()借用父类型构建函数初始化相同属性

```javascript
  function Person(name, age) {
    this.name = name
    this.age = age
  }
  Person.prototype.setName = function (name) {
    this.name = name
  }

  function Student(name, age, price) {
    Person.call(this, name, age) //得到父类型的属性
    this.price = price
  }
  Student.prototype = new Person()  //得到父类型的方法
  Student.prototype.constructor = Student
  Student.prototype.setPrice = function (price) {
    this.price = price
  }

  var s = new Student('Tom', 12, 10000)
  s.setPrice(11000)
  s.setName('Bob')
  console.log(s)
  console.log(s.constructor)
```

方法是所有实例公用的，所以用原型继承；属性是每个对象自身的，用构造函数继承（复制代码）

# 第七章 线程机制与事件机制

## 01_进程与线程

1. 进程(process)：程序的一次执行, 它占有一片独有的内存空间
2. 线程(thread)： 是进程内的一个独立执行单元，CPU的基本（最小）调度单位, 是程序执行的一个完整流程
3. 进程与线程

     * 一个进程中一般至少有一个运行的线程: 主线程
     * 一个进程中也可以同时运行多个线程, 我们会说程序是多线程运行的
     * 一个进程内的数据可以供其中的多个线程直接共享
     * 多个进程之间的数据是不能直接共享的
     * 线程池(thread pool)：保存多个线程对象的容器，实现线程对象的反复利用
4. 浏览器运行是单进程还是多进程?

     * 有的是单进程
       * firefox
       * 老版IE
     * 有的是多进程
       * chrome
       * 新版IE
5. 如何查看浏览器是否是多进程运行的呢?
     * 任务管理器==>进程
6. 浏览器运行是单线程还是多线程?
     * 都是多线程运行的

![image-20221207170629362](D:\WebStrom Coding\课件\image-20221207170629362.png)

![image-20221207171036660](D:\WebStrom Coding\课件\image-20221207171036660.png)

## 02_浏览器内核

![image-20230207122755193](D:\WebStrom Coding\课件\image-20230207122755193.png)

## 03_定时器

* 定时器真的是定时执行的吗？
  * 定时器并不能保证真正定时执行
  * 一般会延迟一丁点，也有可能延迟很长时间

```JavaScript
const btn = document.getElementById('btn')
btn.onclick = () => {
  const start = Date.now()
  console.log("启动定时器前")
  setTimeout(() => {
    console.log("定时器执行了,", Date.now() - start)
  }, 200)
  console.log("启动定时器后")
}
```

![image-20230207124715201](D:\WebStrom Coding\课件\image-20230207124715201.png)

如果在触发函数中添加一个循环次数特多的循环，则定时器执行会有明显延迟，

```JavaScript
const btn = document.getElementById('btn')
btn.onclick = () => {
  const start = Date.now()
  console.log("启动定时器前")
  setTimeout(() => {
    console.log("定时器执行了,", Date.now() - start)
  }, 200)
  console.log("启动定时器后")
  for(let i = 0; i < 1000000000; i++){

  }
}
```

![image-20230207125039431](D:\WebStrom Coding\课件\image-20230207125039431.png)

通过上面的例子：

* 定时器回调函数是在分线程执行的吗？
  * 是在主线程执行的，无论是否为回调函数，均在主线程执行

## 04_JS是单线程执行的

* 如何证明JS执行是单线程的？
  * setTimeout()的回调函数是在主线程执行的
  * 定时器回调函数只有在运行栈中的代码全部执行完后才有可能执行

**在新版本的浏览器中，alert不会暂停计时器，弹窗只能依次弹出**

```javascript
setTimeout(function () {
  console.log('timeout 2222')
}, 2000)
setTimeout(function () {
  console.log('timeout 1111')
  alert('++++')
}, 1000)

function fn() {
  console.log("fn")
}
fn()

console.log('alert()之前')
alert('---') //暂停当前主线程的执行，同时暂停计时，点击确定后，回复程序执行和计时(2023，edge不会了)
console.log('alert之后')
```

* **为什么js要用单线程模式，而不用多线程模式**
  * 例子：在一个页面上的标签，如果JS有两个线程一个要删除标签一个要更新标签，这就造成了很复杂的同步问题。为了利用多核CPU 的计算能力，HTML5提出了web worker标准，允许JS脚本创建多个线程，但是子线程完全受控于主线程，且不得操控DOM，所以这个新标准并没有改变JS单线程的本质
  * 因为JS的单线程，与他的用途有关
  * 作为浏览器脚本语言，js的主要用途是与用户交互及操控dom
  * 者确定了它只能是单线程，否则会带来很复杂的同步问题
* 代码的分类：
  * 初始化代码
  * 回调代码（在回调函数中的代码）
* **JS引擎执行代码的基本流程**
  * 先执行初始化代码（普通函数等）：包含一些特别的代码
    * 设置定时器
    * 绑定监听
    * 发送ajax请求
  * 后边在某个时刻才会执行回调代码

## 05_事件循环模型

![image-20230207175330311](D:\WebStrom Coding\课件\image-20230207175330311.png)

1. 所有代码分类

     * 初始化执行代码(同步代码): 包含绑定dom事件监听, 设置定时器, 发送ajax请求的代码
     * 回调执行代码(异步代码): 处理回调逻辑
2. js引擎执行代码的基本流程:
     * 初始化代码===>回调代码
3. 模型的2个重要组成部分:

     * 事件管理模块
     * 回调队列
4. 模型的运转流程

     * **执行初始化代码, 将事件回调函数交给对应模块管理**

     * **当事件发生时, 管理模块会将回调函数及其数据添加到回调列队中**

     * **只有当初始化代码执行完后(可能要一定时间), 才会遍历读取回调队列中的回调函数执行**

![image-20230207175953283](D:\WebStrom Coding\课件\image-20230207175953283.png)

## 06_H5 Web Worker多线程

```javascript
function fibonacci(n) {
  //递归调用，函数内部调用自己
  return n <= 2 ? 1 : fibonacci(n - 1) + fibonacci(n - 2)
}

const input = document.getElementById('number')

const btn = document.getElementById('btn')
btn.onclick = function () {
  let number = input.value
  let result = fibonacci(number)
  console.log("" + result)
}
```

以上边的递归计算斐波那契数列为例，如果输入的数很大，递归会占用主线程相当长一段时间，在此期间不能对DOM进行任何操作（点击网页没反应），即页面冻结

因此有了Web Worker多线程的解决方案

1. H5规范提供了js分线程的实现, 取名为: Web Workers，**我们可以将一些大计算量的代码交由web worker运行而不冻结用户界面**，但是子线程完全受主线程控制，且不得操作DOM，所以这个新标准并没有改变JS单线程的本质
2. 相关API
   * Worker: 构造函数，加载分线程执行的JS文件
   * Worker.prototype.onmessage: 用于接收另一个线程的回调函数
   * Worker.prototype.postMessage: 向另一个线程发送消息

3. 不足
  * worker内代码不能操作DOM(更新UI)
  * 不能跨域加载JS
  * 不是每个浏览器都支持这个新特性

main.html

```
```

# 补充

### var let const区别：

1. var 存在变量提升，而let 和const不存在
2. var定义的变量可以声明多次，而let、const定义的变量只能声明
3. var、let声明的变量可以再次赋值，而const声明的变量不能再次赋值
4. var声明的变量没有自身的作用域，而Let、const声明的变量有自身的作用域

### 事件队列

所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不立即进入主线程、而先进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

**任务队列：**

"任务队列"是一个先进先出的数据结构，排在前面的事件，优先被主线程读取。任务队列又分为宏队列和微队列，分别存放宏任务和微任务。

**宏任务和微任务：**

在JavaScript中，任务被分为两种，一种宏任务（MacroTask），一种叫微任务（MicroTask）。

常见的宏任务：

script全部代码、setTimeout、setInterval

常见的微任务：

Promise中then的回调函数、MutationObserver、Process.nextTick

（1）js代码执行时，先按代码顺序将同步任务压入主执行栈中执行

（2）遇到异步任务则先将异步任务压入对应的任务队列中（宏队列或微队列）

（3）同步任务执行完毕后，查看微队列，将微任务一一取出进入主执行栈中执行

（4）微任务队列清空后，再查看宏队列，只取出第一个宏任务执行，执行完一个宏任务后，回到第三步的操作

 这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）。

（需要注意的点就是then中的回调函数要确定Promise状态后才能压入微队列）

### 如何利用setTimeout实现setInterval

1. 第一种实现，利用递归

```javascript
// setInterval:隔一段时间执行一次
// setTimeout：倒计时ks执行一次

let fn = () => {
    console.log('执行SI');
}
function setTimeToInterval(fn, delay, times) {
    if (!times) {
        return
    }
    setTimeout(() => {
        fn()
        setTimeToInterval(fn, delay, --times)
    }, delay)
}
setTimeToInterval(fn, 2000, 3)
```

2. 利用具名回调函数

```javascript
    function func2(times){
        let timer = setTimeout(function a() {
            times--
            console.log("time:", times)
            timer = setTimeout(a, 2000)
            if(times <= 0) clearTimeout(timer)
        }, 2000)
    }

    func2(5)
```

**定时器概念介绍**

关于定时器要记住的最重要的事情是，指定的时间间隔表示何时将定时器的代码添加到队列，而不是何时实际执行代码。假设有这样一句代码：

```JavaScript
var btn = document.getElementById("my-btn"); 
btn.onclick = function(){ 
 setTimeout(function(){ 
 document.getElementById("message").style.visibility = "visible"; 
 }, 250); 
 //其他代码
};
```

首先会将onclick 事件处理程序加入队列，再有 250ms后，指定的代码才被添加到队列中等待执行。实际上，对 setTimeout()的调用表示要晚点执行某些代码。

假如前面的onclick事件执行了300ms，那么定时器的代码至少要在定时器设置之后的 300ms 后才会被执行，队列中所有的代码都要等到JavaScript 进程空闲之后才能执行，而不管它们是如何添加到队列中的。如下图所示，**尽管在255s的时候添加了定时器代码，但是此时还不能执行，因为需要等待onclick程序执行完毕，。**定时器代码最早能执行的时机是在 300ms 处，即 onclick 事件处理程序结束之后。

![在这里插入图片描述](https://img-blog.csdnimg.cn/192b48f23ac64b2a92f67b9ce62ccea9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAZGFyYWJpdXo=,size_20,color_FFFFFF,t_70,g_se,x_16)

**setInterval的缺点**

- 使用 setInterval()创建的定时器确保了定时器代码规则地插入队列中。这个方式的问题在于，**定时器代码可能在代码再次被添加到队列之前还没有完成执行。**

- 所以可能会出现的下面的情况：**当前执行栈执行的时间很长，导致事件队列里边积累多个定时器加入的事件，**当执行栈结束后，这些事件会依次执行而之间没有任何停顿，不能达到间隔一段时间执行的效果

- 幸好，JavaScript 引擎够聪明，能避免这个问题。当使用 setInterval()时，**仅当没有该定时器的任何其他代码实例时，才将定时器代码添加到队列中**

这种重复定时器的问题在于

- **某些间隔会被跳过**
- 多个定时器的代码执行之间的间隔可能会比预期的小。
  **假设，某个 onclick 事件处理程序使用 setInterval()设置了一个 200ms 间隔的重复定时器。如果事件处理程序花了 300ms 多一点的时间完成，同时定时器代码也花了差不多的时间，就会同时出现跳过间隔且连续运行定时器代码的情况**

![在这里插入图片描述](https://img-blog.csdnimg.cn/2895136292a74c90bd5d2fe937be5c60.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAZGFyYWJpdXo=,size_20,color_FFFFFF,t_70,g_se,x_16)

这个例子中的第 1 个定时器是在 205ms 处添加到队列中的，但是直到过了 300ms 处才能够执行。当执行这个定时器代码时，在 405ms 处又给队列添加了另外一个副本。在下一个间隔，即 605ms 处，第一个定时器代码仍在运行，同时在队列中已经有了一个定时器代码的实例。结果是，在这个时间点上的定时器代码不会被添加到队列中。结果在 5ms 处添加的定时器代码结束之后，405ms 处添加的定时器代码就立刻执行。

- 所以如果用setTimeout替代的话，就可以确保只有一个事件结束了之后，才会触发下一个定时器事件。**这确保了定时器代码加入到队列中的最小时间间隔为指定间隔**

## 事件的委派

### 原理

事件的委托：即事件代理，JS高级程序上讲：事件委托就是利用事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。

事件委托是利用事件的冒泡原理来实现的，何为[事件冒泡](https://so.csdn.net/so/search?q=事件冒泡&spm=1001.2101.3001.7020)呢？就是事件从最深的节点开始，然后逐步向上传播事件，举个例子：页面上有这么一个节点树，div>ul>li>a;比如给最里面的a加一个click点击事件，那么这个事件就会一层一层的往外执行，执行顺序a>li>ul>div，有这样一个机制，**那么我们给最外面的div加点击事件，那么里面的ul，li，a做点击事件的时候，都会冒泡到最外层的div上，所以都会触发，这就是事件委托，委托它们父级代为执行事件。**

### 实现

1. 在介绍事件委托的方法之前，我们先来看一段一般方法的例子：

子节点实现相同的功能：

```html
<ul id="ul1">
    <li>111</li>
    <li>222</li>
    <li>333</li>
    <li>444</li>
</ul>
```

实现功能是点击li，弹出123：

```javascript
window.onload = function(){
    var oUl = document.getElementById("ul1");
    var aLi = oUl.getElementsByTagName('li');
    for(var i=0;i<aLi.length;i++){
        aLi[i].onclick = function(){
            alert(123);
        }
    }
}
```

上面的代码的意思很简单，相信很多人都是这么实现的，我们看看有多少次的dom操作，首先要找到ul，然后遍历li，然后点击li的时候，又要找一次目标的li的位置，才能执行最后的操作，每次点击都要找一次li；

2. 那么我们用事件委托的方式做又会怎么样呢

```javascript
window.onload = function(){
    var oUl = document.getElementById("ul1");
   oUl.onclick = function(){
        alert(123);
    }
}
```

这里用父级ul做事件处理，当li被点击时，由于冒泡原理，事件就会冒泡到ul上，因为ul上有点击事件，所以事件就会触发，当然，这里当点击ul的时候，也是会触发的，那么问题就来了

**如果我想让事件代理的效果跟直接给节点的事件效果一样怎么办，比如说只有点击li才会触发，不怕，我们有绝招：**

**Event对象提供了一个属性叫target，可以返回事件的目标节点，我们成为事件源，也就是说，target就可以表示为当前的事件操作的dom，但是不是真正操作dom**，当然，这个是有兼容性的，标准浏览器用ev.target，IE浏览器用event.srcElement，此时只是获取了当前节点的位置，并不知道是什么节点名称，这里我们用nodeName来获取具体是什么标签名，这个返回的是一个大写的，我们需要转成小写再做比较（习惯问题）：

```javascript
window.onload = function(){
　　var oUl = document.getElementById("ul1");
　　oUl.onclick = function(ev){
　　　　var ev = ev || window.event;
　　　　var target = ev.target || ev.srcElement;
　　　　if(target.nodeName.toLowerCase() == 'li'){
　 　　　　　　 alert(123);
　　　　　　　  alert(target.innerHTML);
　　　　}
　　}
}
```

这样改下就只有点击li会触发事件了，且每次只执行一次dom操作，如果li数量很多的话，将大大减少dom的操作，优化的性能可想而知！

3. 要是每个li被点击的效果都不一样，那么用事件委托还有用吗

```html
<div id="box">
        <input type="button" id="add" value="添加" />
        <input type="button" id="remove" value="删除" />
        <input type="button" id="move" value="移动" />
        <input type="button" id="select" value="选择" />
    </div>
```

```javascript
window.onload = function(){
            var Add = document.getElementById("add");
            var Remove = document.getElementById("remove");
            var Move = document.getElementById("move");
            var Select = document.getElementById("select");
            
            Add.onclick = function(){
                alert('添加');
            };
            Remove.onclick = function(){
                alert('删除');
            };
            Move.onclick = function(){
                alert('移动');
            };
            Select.onclick = function(){
                alert('选择');
            }
            
        }
```

4. 如何用事件委托进行优化

```javascript
window.onload = function(){
            var oBox = document.getElementById("box");
            oBox.onclick = function (ev) {
                var ev = ev || window.event;
                var target = ev.target || ev.srcElement;
                if(target.nodeName.toLocaleLowerCase() == 'input'){
                    switch(target.id){
                        case 'add' :
                            alert('添加');
                            break;
                        case 'remove' :
                            alert('删除');
                            break;
                        case 'move' :
                            alert('移动');
                            break;
                        case 'select' :
                            alert('选择');
                            break;
                    }
                }
            }
            
        }
```

## 事件的冒泡和捕获

### 原理

以click点击事件为例。假如我们有一个多层结构标签。如下图，是4个div嵌套。每个div都有点击的监听事件，分别输出1234。当我们点击最里面的div时，点击事件开始传递，传递的**全过程是1-2-3-4-4-3-2-1**。
前半部分，事件从最外面的父div依次传递到最里面的后代div，**1-2-3-4这部分我们叫捕获过程**。
之后事件又从最里层的后代div逐层传出，**4-3-2-1这部分我们叫冒泡过程**。

事件冒泡通俗讲以整个文档体doucment参照就是从里到外（可以自行脑补一下水里冒泡现象）

说到事件冒泡就不得不提一下dom0级事件与dom2及事件

### dom0级事件

dom0级事件只有事件冒泡，没有事件捕获

dom0级事件会覆盖,如下控制台只会输出结果 2：

```javascript
var oDiv=document.getElementById('div');
 
　　oDiv.onclick=function(){
        console.log(1)
     }
     oDiv.onclick=function(){
        console.log(2)
     }       
```

### dom2级事件

dom2级事件涉及到事件捕获，第三个参数布尔值如果为false，就是事件冒泡，否则就是事件捕获

dom2级事件不会覆盖，如控制台只会输出结果 1,2：

```javascript
var oDiv=document.getElementById('div');
   oDiv.addEventListener('click',function(){
 
  console.log(1)
　　},true)
 
　　
 
   oDiv.addEventListener('click',function(){
 
  console.log(2)
　　},true)
```

### 冒泡例子

```javascript
var body=document.getElementsByTagName('body')[0];
 
window.addEventListener('click',function(){
        console.log('window')
},false)
 body.addEventListener('click',function(){
        console.log('body')
},false)
 
var oDiv=document.getElementsByTagName('div')[0];
 oDiv.addEventListener('click',function(){
    console.log(1)
 },false)
 
oDiv.addEventListener('click',function(){
  console.log(2)
},false)点击div运行结果
 
```

![img](https://img-blog.csdnimg.cn/20210509202512681.png)

### 事件捕获

```javascript
var body=document.getElementsByTagName('body')[0];
 
window.addEventListener('click',function(){
        console.log('window')
},true)
 body.addEventListener('click',function(){
        console.log('body')
},true)
 
var oDiv=document.getElementsByTagName('div')[0];
 oDiv.addEventListener('click',function(){
    console.log(1)
 },true)
 
oDiv.addEventListener('click',function(){
  console.log(2)
},true)
```

![img](https://img-blog.csdnimg.cn/20210509202608445.png)

**事件捕获就是从上往下一级一级往下找，先找父级在找子级**

![img](https://img-blog.csdnimg.cn/img_convert/ace416934cfef9e325f46b94893a66c5.png)

### 经典面试题

![img](https://img-blog.csdnimg.cn/20210509202841785.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MzYxMzg0OQ==,size_16,color_FFFFFF,t_70)
