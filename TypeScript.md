JS代码不方便维护，无类型标注

变量不是最大的问题，而是函数，当传参时，想传什么传什么

微软公司设计出了TS，是JavaScript的超集，可以在任何支持JS的平台中执行。

TS扩展了JS并添加了类型，TS不能被JS解析器直接执行（不能在浏览器中直接执行），需要编译成JS

# 第一章 快速入门

## 1.2 基本类型

注意即使TS中报错，在编译成JS中仍然会通过

![image-20230127213044621](D:\WebStrom Coding\课件\image-20230127213044621.png)

**如果变量的声明和赋值是同时进行的，TS可以自动对变量进行类型监测**，所以变量类型声明用的不多

![image-20230127213352269](D:\WebStrom Coding\课件\image-20230127213352269.png)

JS中不考虑参数的类型和个数，但是TS需要，相比于参数函数参数的类型声明用的更多一些

![image-20230127213933148](D:\WebStrom Coding\课件\image-20230127213933148.png)

可以对函数的返回值进行声明类型

![image-20230127214242238](D:\WebStrom Coding\课件\image-20230127214242238.png)

* 类型

![image-20230127214645950](D:\WebStrom Coding\课件\image-20230127214645950.png)

```typescript
//也直接使用字面量进行类型声明

let a: 10;//只能限制其值为10
//a = 11 //报错

//一般这么用，或者是male或者是female，可以使用|连接多个类型
let b: "male" | "female"
//不仅可以连接字面量，也可以连接多个类型值
let c: boolean | string;
```

```typescript
//any，any表示的是任意类型，一个变量设置类型为any后相当于对该变量关闭了类型检测
//使用TS时，不建议使用any类型
let d: any;
d = 10
d = true
//如果声明变量时不指定类型，则TS解析器会自动判断变量类型为any（隐式的any)
```

```typescript
//unknown 表示未知类型的值，实际上就是一个类型安全的any，unknown类型的变量，不能直接赋值给其他变量
let e:unknown;
e = 10;
e = "hello"
e = true

let s: string
//d的类型是any，它可以赋值任意变量（非常的不好）
s = d

e = "hello"
// 把一个unknown对象赋值给一个已经声明类型的变量会报错，注意变量引用时看的是变量的类型而不是值
// s = e 
//unknown类型的变量不能直接赋值给其他变量，如何不报错赋值？
if(typeof e === "string"){
    s = e
}
```

```typescript
//如果函数没返回值，则返回类型为void
function fn(){
    return
}
//如果函数类型明确为void，有返回值则会报错（返回null不会）
// function f(): void{
//     return 123
// }
```

```typescript
//never 表示永远不会返回结果（例如专门用来报错的函数，因为程序一旦报错则不会接着运行）
function fn2(): never{
    throw new Error("报错了!")
}
```

```typescript
//object表示一个js对象，但是类型限制为对象没意义
let a:object
a = {}
a = function () {

}

//我们要限制对象里边的属性，才有意义
/*
*   {}永爱指定对象中可以包含哪些属性
*   语法：{属性名：属性值，属性名：属性值}
*   属性值后边加上问号，表示这个属性值有也行，没有也行
*
* */
let b: {name: string, age?: number}
// b = {}  //报错了
b = {name: 'abc'}

/*
*   [xx: string]表示属性名，里边的xx写啥都可以，string表示属性名是一个字符串
*   any表示任意类型的属性值，两个结合一块则表示该对象可以有任意属性
*   可以把any替换成string，则允许任意值为字符串的属性放在对象中
* */
let c: {name: string, [propName: string]: any}
c = {name: '猪八戒'}

/*
*   设置函数结构的类型声明
*   语法：（形参：类型， 形参：类型...） => 返回值
* */
let d: (a: number, b: number)=>number
d = function (n1, n2) {
    return n1 + n2
}
```

```typescript
//string[] 表示字符串数组
let e: string[]
e = ['a', 'b', 'c']

//number[] 表示数值数组，或者用array<number>
let f: number[]
let g: Array<number>
```

```typescript
/*
* enum枚举，在适合有选项的情况下用枚举
*   定义一个枚举类
*   
* */
enum Gender{
    Male,
    Female
}
    
let i: {name: string, gender: Gender}
i = {
    name: "aaa",
    gender: Gender.Male //'male'
}

console.log(i.gender === Gender.Male)
```

```typescript
//&表示同时满足
let j: {name: string} & {age: number}
j = {name: "孙悟空", age: 18}

//类型的别名，可以复用定义的类型
type myType = 1 | 2 | 3 | 4 | 5;
let k: myType;
let m: myType;
k = 2
```

## 1.3 编译选项

监视编译：tsc xxx.ts -w

如何对多个ts文件进行监视而不用每一个ts文件单独开一个命令窗口

在文件目录下创建tsconfig.json文件，在目录下的命令窗口直接输入tsc -w就可以对所有ts文件进行编译监视

**tsconfig.json**

* tsconfig.json是ts编译器的配置文件，ts编译器可以根据他的信息来对代码进行编译

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es5",
    "sourceMap": true
  },
  "exclude": [
    "node_modules"
  ]
}
```

* "include":["./src/\**/\*"]  include配置项表示哪个文件需要编译，其中两个\*表示任意目录，一个\*表示任意文件
* "exclude":["./src/\**/\*"] exclude配置项表示不需要遍历的文件

![IMG_0401](D:\WebStrom Coding\课件\IMG_0401.PNG)

* compilerOptions 编译器选项，重点

```json
"compilerOptions": {
    //指定要使用的模块化规范
    //'none', 'commonjs', 'amd', 'system', 'umd', 'es6', 'es2015', 'es2020', 'es2022', 'esnext', 'node16', 'nodenext'.
    "module": "es2015",

    //target用来指定ts被编译为js的版本，里边写ES的版本
    //'es3', 'es5', 'es6', 'es2015', 'es2016', 'es2017', 'es2018', 'es2019', 'es2020', 'es2021', 'es2022', 'esnext'
    "target": "es5",

    //lib表示项目中要使用的库，一遍情况下在浏览器运行的代码不需要单独加dom包
    "lib": ["dom"],

    //outDir  用来指定编译后文件所在的目录
    "outDir": "./dist",

    //outFile 将代码合并成一个文件，下面的代码相当于把编译后的文件合并到一个文件当中
    //注意如果想使用这个功能，则必须要将module选项配置为amd或者system，（使用模块化export导入导出没法合并）
    "outFile": "./dist/app.js",

    // allowJs是否对JS文件进行编译，默认是false
    "allowJs": true,
    
    //检查Js代码是否符合语法规范，如果程序员在JS中编写代码，可以选择是否按照ts的规则检查js代码规范，默认是false
    "checkJs": true,

    //是否在编译过程中移除注释，默认为false
    "removeComments": false,

    //不生成编译后的文件，默认为false，一般用于只想编译看看是否会报错的情况
    "noEmit": false,

    //当有错误的时候不生成编译文件
    "noEmitOnError": true,

    //严格模式 在浏览器运行时性能更好 默认为false 在编译后的Js文件中第一行都加上了"use strict"字样
    "alwaysStrict": true,

    //是否不允许隐式的any类型，例如在定义函数时函数参数如果不设置类型则为隐式any 默认值为false
    "noImplicitAny": true,

    //不允许出现不明确类型的this 默认值为false 回顾：函数里边调用this指向window（严格模式undefined）以方法形式调用，里边this指向调用方法的对象
    //如果在函数内中调用this，则需要在参数里边加上this: xxx字样
    "noImplicitThis": true,

    //严格的检查空值 默认为true 例如值为null的后边不能加 .xxx
    "strictNullChecks": true,

    //所有严格检查的总开关 默认为true 一般开发建议设置为true
    "strict": true,
    
    
    "sourceMap": true
  },
```

有关于严格检查空值的示例，如果不加?.那么由于box1是null，则在ts中会报错：

```typescript
let box1 = document.getElementById('box1')
box1?.addEventListener('click', function (){
    alert("hello")
})
```

## 1.4 webpack配置项

先略过



# 第二章：面向对象

## 2.1 类

