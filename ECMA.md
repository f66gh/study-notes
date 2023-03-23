# ECMA

1. European computer manufacturers association 欧洲计算机制造联合会
2. 规范化脚本语言，叫ECMAScript

## ES5简介 

增加了以下特性：

1. strict模式
2. Array的every，some，forEach，filter, indexOf, lastIndexOf, isArray, map, reduce, reduceRight等方法
3. Object方法

## ES6

增加了新特性：

1. 块级作用域 关键字Let 常亮const
2. 对象字面量的属性赋值简写
3. 赋值解构
4. 函数参数的默认值、参数打包、数组展开(Default， Rest, Spread)
5. 箭头函数 Arrow functions

简化了代码模式，默认return 表达式效果

自动绑定语义this 即定义函数时的this

6. 字符串模板 Template strings
7. Iterators（迭代器） + for ... of
   * 迭代器的next方法，调用会返回：
     * 返回迭代对象的一个元素{done: false, value:elem}
     * 如果已经达到迭代对象的末端{done: true, value: reVal}
8. 生成器(Generators)
9. Class，有constructor、extend、super
10. Modules
    * 具有CommonJS的精简语法、唯一导出出口(singgle exports)和循环依赖(cyclic dependencies)的特点
    * 类似amd，支持异步加载和可配置的模块加载
11. 四种集合类型，Map Set WeakMap WeakSet
12. 一些新的API Math + Number + String + Array + Object APIs
13. Proxies 使用代理(proxy)监听对象的操作，包括get set has deleteProperty apply construct getOwnPropertyDescriptor defineProperty getPrototypeOf setPrototypeOf enumerate ownKeys preventExtensions isExtensible
14. Symbols 
15. Promises 异步处理操作的对象，使用了Promise对象之后可以用一种链式调用的方式来组织代码，让代码更加直观

# ECMAScript6

 ## 01_let和const

1. let声明变量，没有变量提升
2. Let可以作用于块级作用域，let是一个块作用域
3. let不支持重复声明
4. const一般声明常量，一旦被声明则无法修改，并且具有以上的特性

5. const定义一个对象，但是可以修改里边的属性，对象本身不能够修改（地址值不能被修改）

```javascript
var arr = []
for (var i = 0; i < 10; i++){
    arr[i] = function() {
        return i;
    }
}
console.log(arr[5]())
```

注意for循环语句中只是定义函数但是没有执行函数，即只有后面的程序在调用函数后才会返回**全局变量i**，这里如果在循环外边定义Let i = 0 也会是这个结果

7. let不会污染全局变量

```javascript
        let RegExp = 10;
        console.log(RegExp)
        console.log(window.RegExp)
```

window.RegExp是一个全局函数

建议使用const定义变量

## 02_模板字符串

`const str = xxxxx${变量名}`

注意反引号写的字符串可以换行，双引号不行

## 03_函数默认值和剩余参数

```javascript
function add(a = 10, b = 20){
            return a + b
        }

console.log(add())
```

```javascript
//默认参数也可以是一个函数
function add(a, b = getVal(5)){
	return a + b;
}
function getVal(val){
	return val + 5;
}
console.log(add(10))
```

现在有一个需求，要求定义一个函数，能够根据实参的对象及对象属性输出新的对象

```JavaScript
//ES5写法
function pick(obj){
    let result = Object.create(null);
    for(let i = 1; i < arguments.length; i++){
        result[arguments[i]] = obj[arguments[i]]
    }
    return result;
}
let book = {
    title: 'es6的教程',
    author: '小马哥',
    year: 2019
}
let bookData = pick(book, 'title', 'year', 'author')
console.log(bookData)
```

![image-20230301230413170](D:\WebStrom Coding\课件\image-20230301230413170.png)

```javascript
        //剩余参数
        function pick(obj, ...keys) {
            let result = Object.create(null);
            for (let i = 1; i < keys.length; i++) {
                result[keys[i]] = obj[keys[i]]
            }
            return result;
        }
        let book = {
            title: 'es6的教程',
            author: '小马哥',
            year: 2019
        }
        let bookData = pick(book, 'title', 'year', 'author')
        console.log(bookData)
```

![image-20230301232600552](D:\WebStrom Coding\课件\image-20230301232600552.png)

```JavaScript
        function checkArgs(...args){
            console.log(args) //返回的是一个真正的数组
            console.log(arguments) //返回的是伪数组
        }
        checkArgs('a', 'b', 'c')
```

![image-20230301232921363](D:\WebStrom Coding\课件\image-20230301232921363.png)