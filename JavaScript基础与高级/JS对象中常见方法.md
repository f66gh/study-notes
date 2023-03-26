# JS对象中常见方法

## 对象的创建并添加属性

1. new 对象

```javascript
// 1. 创建空对象后，在添加属性
const obj = { }
obj.uname = 'dengke'
obj.fn = () => {
    console.log('ggg')
}
console.log(obj) // { uname: 'dengke', fn: ƒ }
 
// 2. 创建对象并且直接添加属性 (常用)
const obj1 = {
    uname：'dengke',
    fn: () => {
        console.log('ggg')
    }
}
console.log(obj1) // { uname: "dengke", fn: ƒ }
```

2. Object.create()方法（先放一下）

Object.create()⽅法创建⼀个新对象，**使用第⼀个参数来提供新创建对象的\__proto__**（以第⼀个参

数作为新对象的构造函数的[原型对象](https://so.csdn.net/so/search?q=原型对象&spm=1001.2101.3001.7020)）；⽅法还有第⼆个可选参数，是添加到新创建对象的属性，写法如下。 **常用在JS组合继承中**

```javascript
const a = object.create(Person.prototype,{
	age:{
		value: 12,
		writable: true,
		configurable: true
	}
})
```

**new 产⽣的实例，优先获取构造函数上的属性；**构造函数上没有对应的属性，才会去原型上查找；
如果构造函数中以及原型中都没有对应的属性，就会报错
**Object.create() 产⽣的对象，只会在原型上进⾏查找属性，原型上没有对应的属性，就会报错。**
详见我另外一篇文章[js的继承](https://blog.csdn.net/csdssdn/article/details/124064074)

## 访问对象的属性

1. 以.的方式访问
2. 以[]的方式访问
3. 链式操作符，当对象上没有这个键的时候，不会报错，而是赋值undefined

## 删除对象属性

利用关键字delete

```javascript
const o = {
	p: 10,
	m: 20
}
delete o.p

// 删除对象的属性后，在访问返回 undefined
console.log(o.p) // undefined
```

## 枚举对象的属性

1. for in: 会遍历对象中所有的可枚举属性（包括自有属性和继承属性）
2. Object.keys(): 会返回一个包括所有的可枚举的自有属性的名称组成的数组
3. Object.getOwnPropertyNames(): 会返回自有属性的名称 （不管是不是可枚举的）

**for...in**

会遍历对象中所有的**可枚举**属性（**包括自有属性和继承属性**） **注意不能枚举属性值，只能枚举属性名**

```javascript
const obj = {
    itemA: 'itemA',
    itemB: 'itemB'
}
 
// 使用Object.create创建一个原型为obj的对象 （模拟继承来的属性）
var newObj = Object.create(obj) 
 
newObj.newItemA = 'newItemA'
newObj.newItemB = 'newItemB'
 
for(i in newObj){
    console.log(i)
}
// newItemA
// newItemB
// itemA
// itemB
 
// 现在我们将其中的一个属性变为不可枚举属性
Object.defineProperty(newObj, 'newItemA', {
    enumerable: false
})
 
for(i in newObj){
    console.log(i)
}
// newItemB
// itemA
// itemB
```

**Object.keys()**

会返回衣蛾所有**可枚举**的**自有属性**的名称组成的数组 **注意是可枚举且自有属性**

```javascript
// 接上例
 
const result = Object.keys(newObj)
console.log(result) // ["newItemB"]
```

**Object.getOwnPropertyNames()**

会返回**自有属性**的名称**（不管是不是可枚举的）**

```javascript
// 接上例
const result = Object.keys(newObj)
 
console.log(result) // ['newItemA','newItemB']
```

## Object常用API

**Object.assign()**

语法：Object.assign(target, ...sources) 

参数：target目标参数，sources源对象 返回值：目标对象

**`Object.assign()`** 方法用于将所有可枚举属性的值从一个或多个源对象分配到目标对象。它将返回目标对象。常用来合并对象。

```javascript

const obj1 = { a: 1, b: 2 }
 
const obj2 = { b: 4, c: 5 }
 
const obj3 = Object.assign(obj1, obj2)
 
const obj4 = Object.assign({}, obj1) // 克隆了obj1对象
 
console.log(obj1) // { a: 1, b: 4, c: 5 } 对同名属性b进行了替换 obj1发生改变是因为obj2赋给了obj1
 
console.log(obj2) // { b: 4, c: 5 }
 
console.log(obj3) // { a: 1, b: 4, c: 5 }
 
console.log(obj4) // { a: 1, b: 4, c: 5 }

console.log(obj3 === obj4) //true
```

- 如果目标对象中的属性具有相同的键，则属性将被源对象中的属性覆盖。
- `Object.assign` 方法只会拷贝**源对象自身**的并且**可枚举**的属性到目标对象。
- **assign其实是浅拷贝而不是深拷贝。**也就是说，如果源对象某个属性的值是对象，**那么目标对象拷贝得到的是这个对象的引用。**同名属性会替换。

- `Object.assign` 不会在那些`source`对象值为null或undefined的时候抛出错误。

**Object.keys()**

语法：`object.keys(obj)`

- 参数：`obj`要返回其**可枚举** **自身属性**的对象。
- 返回值：一个表示给定对象的所有可枚举属性的字符串数组。

```javascript
const arr = ['a', 'b', 'c']
 
console.log(Object.keys(arr)) // ['0', '1', '2']
 
const obj = { 0: 'a', 1: 'b', 2: 'c' }
 
console.log(Object.keys(obj)) // ['0', '1', '2']
 
const obj2 = { 100: 'a', 2: 'b', 7: 'c' }
 
console.log(Object.keys(obj2)) // ['2', '7', '100']
```

- 在ES5里，如果此方法的参数不是对象（而是一个原始值），那么它会抛出 TypeError。在ES2015中，非对象的参数将被强制转换为一个对象。

```javascript
console.log(Object.keys('12345')) //['0', '1', '2', '3', '4']
```

* 对象`key`为`number`的话，会从升序枚举返回。

**Object.values()**

语法：`Object.values(obj)`

- 参数：`obj`被返回可枚举属性值的对象。
- 返回值：一个包含**对象自身**的所有**可枚举**属性值的数组。

**`Object.values()`** 方法返回一个给定对象自身的所有可枚举属性值的数组，值的顺序与使用`for...in`循环的顺序相同 ( 区别在于 **for-in 循环枚举原型链中的属性** )。**与`Object.keys()`相似，区别在于这个返回的是数据的值也就是`value`**

- 对象`key`为`number`的话，会从升序枚举返回。

```javascript
const obj3 = { 100: 'a', 2: 'b', 7: 'c' }
console.log(Object.values(obj3)) // ['b', 'c', 'a']
```

**Object.entries(obj)**

- 参数：`obj`可以返回其**可枚举**属性的键值对的对象。
- 返回值：给定**对象自身**可枚举属性的键值对数组。

```javascript
const obj = { foo: 'bar', baz: 42 };
console.log(Object.entries(obj)); // [ ['foo', 'bar'], ['baz', 42] ]
 
// array like object 像数组的对象
const obj = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.entries(obj)); // [ ['0', 'a'], ['1', 'b'], ['2', 'c'] ]
 
// array like object with random key ordering  
const anObj = { 100: 'a', 2: 'b', 7: 'c' };
// 返回值会按照key升序排列
console.log(Object.entries(anObj)); // [ ['2', 'b'], ['7', 'c'], ['100', 'a'] ]
 
// getFoo is property which isn't enumerable getFoo是不可枚举的属性
const myObj = Object.create({}, { getFoo: { value() { return this.foo; } } });
myObj.foo = 'bar';
console.log(Object.entries(myObj)); // [ ['foo', 'bar'] ]  // 只会返回对象可枚举属性键值对组成的数组
 
// non-object argument will be coerced to an object //ES2015新增 会将非对象参数强转成对象
console.log(Object.entries('foo')); // [ ['0', 'f'], ['1', 'o'], ['2', 'o'] ]
 
// iterate through key-value gracefully 优雅地遍历键值对
const obj = { a: 5, b: 7, c: 9 };
for (const [key, value] of Object.entries(obj)) {
  console.log(`${key} ${value}`); // "a 5", "b 7", "c 9"
}
 
// Or, using array extras 使用数组的额外功能
Object.entries(obj).forEach(([key, value]) => {
console.log(`${key} ${value}`); // "a 5", "b 7", "c 9"
```

- 将**`Object`**转换为`Map`，`new Map()`构造函数接受一个可迭代的`entries`。借助`Object.entries`方法你可以很容易的将`Object`转换为`Map`:

```javascript
const obj = { foo: "bar", baz: 42 }
const map = new Map(Object.entries(obj))
console.log(map) // Map { foo: "bar", baz: 42 }
```

**Object.fromEntries**

语法：`Object.fromEntries(iterable)`

- 参数：`iterable`类似`Array`、`Map`或者其它实现了`可迭代协议`的可迭代对象。
- 返回值：一个由该迭代对象条目提供对应属性的新对象。

**`Object.fromEntries()`** 方法把**键值对列表**转换为一个对象。**与`Object.entries()`相反。相当于反转了`Object.entries()`方法返回的数据结构。**

```javascript
const entries = new Map([
  ['foo', 'bar'],
  ['baz', 42]
]);
 
const obj = Object.fromEntries(entries);
 
console.log(obj);
// Object { foo: "bar", baz: 42 }
```

**补充**

- `Map` 转化为 `Object`

通过 `Object.fromEntries`， 可以将`Map`转换为`Object`:

```javascript
const map = new Map([ ['foo', 'bar'], ['baz', 42] ])
const obj = Object.fromEntries(map)
console.log(obj)
// { foo: "bar", baz: 42 }
```

* Array转化为Object

```javascript
const arr = [ ['0', 'a'], ['1', 'b'], ['2', 'c'] ]
const obj = Object.fromEntries(arr)
console.log(obj)
// { 0: "a", 1: "b", 2: "c" }
```

- `Object.fromEntries` 是与 `Object.entries()`相反的方法，用 *数组处理函数* 可以像下面这样转换对象：

```javascript
const object1 = { a: 1, b: 2, c: 3 }
 
const object2 = Object.fromEntries(
  Object.entries(object1)
  .map(([ key, val ]) => [ key, val * 2 ])
)
 
// Object.entries(object1) >>> [["a",1],["b",2],["c",3]]
 
console.log(object2) // { a: 2, b: 4, c: 6 }
```

**Object.prototype.hasOwnProperty()**

上边枚举对象属性时为了避免`for..in`**遍历继承**来的属性，给大家补充了可以借助`Object.prototype.hasOwnProperty()`方法进行判断，在这里也具体为大家介绍一下它。

语法：`obj.hasOwnProperty(prop)`

- 参数：`prop` 要检测的属性的`String`字符串形式表示的名称，或者`Symbol`。
- 返回值：用来判断某个对象是否含有指定的属性的布尔值`Boolean`。

**`hasOwnProperty()`** 方法会返回一个布尔值，指示对象自身属性中是否具有指定的属性（也就是，是否有指定的键）。**可以查询不可枚举的属性**

**注意：**

- 只会对自身属性进行判断，继承来的一律返回`false`。配合`for...in`使用，可以避免其遍历继承来的属性。

```javascript
const obj1 = new Object();
	obj1.property1 = 42
	Object.prototype.property2 = 111
	console.log(obj1.hasOwnProperty('property1')) // true
	console.log(obj1.hasOwnProperty('property2')) // false
	console.log(obj1.hasOwnProperty('toString')) // false
	console.log(obj1.hasOwnProperty('hasOwnProperty')) // false
```

- 即使属性的值是 `null` 或 `undefined`，只要属性存在，`hasOwnProperty` 依旧会返回 `true`。

```javascript
const o = new Object();
o.propOne = null
o.propTwo = undefined
 
console.log(o.hasOwnProperty('propOne')) // true
console.log(o.hasOwnProperty('propTwo')) // true
```

**Object.getOwnPropertyNames()**

上边枚举对象属性时也有用到该方法，在这里也具体为大家介绍一下它。

**`Object.getOwnPropertyNames()`** 返回一个数组，该数组对元素是 `obj`自身拥有的枚举或不可枚举属性名称字符串。数组中枚举属性的顺序与通过`for...in`循环`Object.keys`迭代该对象属性时一致。**数组中不可枚举属性的顺序未定义。**

语法：`obj.getOwnPropertyNames(obj)`

- 参数：`obj`一个对象，其自身的**可枚举和不可枚举属性**的名称被返回。
- 返回值：在给定对象上找到的自身属性对应的字符串数组。

```javascript
const arr = ["a", "b", "c"];
console.log(Object.getOwnPropertyNames(arr).sort()) // ["0", "1", "2", "length"]
 
// 类数组对象
const obj = { 0: "a", 1: "b", 2: "c"};
console.log(Object.getOwnPropertyNames(obj).sort()) // ["0", "1", "2"]
 
// 使用Array.forEach输出属性名和属性值
Object.getOwnPropertyNames(obj).forEach(function(val, idx, array) {
  console.log(val + " -> " + obj[val]);
})
// 0 -> a
// 1 -> b
// 2 -> c
 
// 不可枚举属性
const my_obj = Object.create({}, {
  getFoo: {
    value: function() { return this.foo; },
    enumerable: false
  }
});
my_obj.foo = 1;
 
// 不可枚举属性也会一并输出 
console.log(Object.getOwnPropertyNames(my_obj).sort())
// ["foo", "getFoo"]
```

- **`Object.getOwnPropertyNames`**和**`Object.keys`**的区别：**`Object.keys`**只适用于可枚举的属性，而**`Object.getOwnPropertyNames`**返回对象的全部属性名称(包括不可枚举的)。

```javascript
'use strict'
(function () {
    // 人类的构造函数
    const person = function (name, age, sex) {
        this.name = name
        this.age = age
        this.sex = sex
        this.sing = () => {
            console.log('sing');
        }
    }
 
    // new 一个ladygaga
    const gaga = new person('ladygaga', 26, 'girl')
    
    // 给嘎嘎发放一个不可枚举的身份证
    Object.defineProperty(gaga, 'id', {
        value: '1234567890',
        enumerable: false
    })
 
    //查看gaga的个人信息
    const arr = Object.getOwnPropertyNames(gaga)
    console.log(arr) // name, age, sex, sing, id
    
    // 注意和getOwnPropertyNames的区别，不可枚举的id没有输出
    const arr1 = Object.keys(gaga)
    console.log(arr1) // name, age, sex, sing
})()
```

- 如果你只要获取到可枚举属性，可以用`Object.keys`或用`for...in`循环（`for...in`会获取到原型链上的可枚举属性，可以使用`hasOwnProperty()`方法过滤掉）。
- 获取不可枚举的属性，可以使用`Array.prototype.filter()`方法，从所有的属性名数组（使用`Object.getOwnPropertyNames()`方法获得）中去除可枚举的属性（使用`Object.keys()`方法获得），剩余的属性便是不可枚举的属性了：