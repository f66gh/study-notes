# Promise的理解与使用

## Promise 是什么

### 理解

1. 抽象表达：
   1. Promise是一门新的技术（ES6规范）
   2. Promise是JS重进行一步编程的新解决方案（旧方案是单纯使用回调函数）
2. 具体表达：
   1. 从语法上来说：Promise是一个构造函数
   2. 从功能上来说：Promise对象用来封装一个异步操作并可以获取其成功/失败的结果值
      * 异步编程：
        * fs 文件操作（node.js中）
        * 数据库操作
        * AJAX
        * 定时器

### promise的状态改变

promise的状态是promise实例状态的一个属性，叫**promiseState**

* 属性一共有三种值，第一种是**pending**，表示**未决定的**
  * pending变为**resolved / fullfilled** 成功
  * pending变为**rejected** 失败

* 说明：**pending改变状态只有这两种，且一个promise对象只能改变一次，即不可能由成功转失败，也不可能由失败转成功**
  * 无论成功还是失败，都会有一个结果数据
  * 成功的结果数据一般称为value，失败的结果数据一般称为reason

### promise对象的值

实例对象中的另一个属性，叫**promiseResult**

这个属性保存对象成功或失败的结果，有两个方法可以修改这个值：

* resolve
* reject

除了上述两个函数外，其余方法均不能对实例对象中的promiseResult进行赋值



## 为什么要用Promise

### 指定回调函数的方式更加灵活

1. 旧的：**必须在启动异步任务前指定**
2. promise：启动异步任务 -》返回promise对象 -》给promise对象绑定回调函数（甚至可以在异步任务结束后指定/多个）

### 支持链式调用，可以解决回到地狱问题（面试必问）

1. 什么是回调地狱？

   回调函数嵌套调用，外部回调函数异步执行的结果时嵌套的回调执行的条件

```javascript
asyncFunc1(opt, (...args1) => {
            asyncFunc2(opt, (...args2) => {
                asyncFunc3(opt, (...args3) => {
                    asyncFunc4(opt, (...args4) => {
                        //some operation
                    })
                })
            })
        })
```

2. 回调地狱的缺点？

   不便于阅读

   不便于异常处理

3. 解决方案？

​		promise链式调用

### Promise初体验

案例：抽奖

```javascript
// 生成随机数
    function rand(m, n) {
        return Math.ceil(Math.random() * (n - m + 1)) + m - 1
    }
    /*
        点击按钮，2s后显示是否中奖（30%中奖）
        用alert提示
    */
    const btn = document.querySelector('#btn')
    btn.addEventListener('click', function () {
        //定时器模拟与后端交互
        setTimeout(() => {
            let n = rand(1, 100)
            if (n <= 30) {
                alert('恭喜')
            } else {
                alert('再接再厉')
            }
        }, 1000)
    })
```

* promise是一个**构造函数**，所以要用**new给对象实例化**
* 实例化需要**接收一个函数类型的值**
  * 函数有两个形参，分别是resolve和reject，resolve表示解决，reject表示拒绝，注意这两个都是**函数类型的数据**
  * 当异步任务成功的时候调resolve，当异步任务失败的时候调reject
  * **当调用resolve时，可以将promise对象的状态设置为成功**
  * **当调用reject时，可以将promise对象的状态设置为失败**

* 要给实例化的对象设置then方法，两个参数为函数类型的数据
  * 第一个表示成功时候调用的函数
  * 第二个表示失败的时候调用的函数

```javascript
btn.addEventListener('click', function () {
        //Promise 形式实现
        const p = new Promise((resolve, reject) => {
            setTimeout(() => {
                let n = rand(1, 100)
                //判断
                if (n <= 30) {
                    //成功调resolve
                    resolve()
                } else {
                    //失败调reject
                    reject()
                }
            }, 1000)
        })

        //调用 then 方法
        p.then(() => {
                alert('恭喜')
            }, () => {
                alert('再接再厉')
            })
    })
```

* 新的需求：中奖了需要提示中奖数字，失败了提示失败号码
* 需要在对象中的resolve和reject传参数
  * 在then方法中，一般用value和reason表示上述两个函数的形参，意义为value(值)和reason(理由)

```
if (n <= 30) {
                    //成功调resolve
                    resolve(n)
                } else {
                    //失败调reject
                    reject(n)
                }
...
 p.then((value) => {
            alert('恭喜' + value)
        }, (reason) => {
            alert('再接再厉' + reason)
        })
```

### 案例——fs读取文件

* 执行方法：右键js文件在终端中打开，输入 `node 文件名.js`

```
const fs = require('fs')

fs.readFile('./resource/2022.09.15 组会推荐阅读论文.txt', (err, data) => {
    //如果出错抛出错误
    if(err) throw err
    //输出文件内容
    console.log(data.toString())
})
```

-----

用Promise写一下

```javascript
const fs = require('fs')

let p = new Promise((resolve, reject) => {
    fs.readFile('./resource/2022.09.15 组会推荐阅读论文.txt', (err, data) => {
        if(err) reject(err)
        resolve(data)
    })
})

p.then(value => {
    console.log(value.toString())
}, reason => {
    console.log(reason)
})
```

### 案例——AJax

```javascript
btn.addEventListener('click', function(){
        //1.创建对象
        const xhr = new XMLHttpRequest()
        //2.初始化
        xhr.open('GET', 'http://api.apiopen.top/Parameters')
        //3.发送
        xhr.send()
        //4.处理相应结果
        xhr.onreadystatechange = function(){
            if(xhr.readyState === 4){
                //判断相应状态码 2xx
                if(xhr.status >= 200 && xhr.status < 300){
                    //控制台输出响应体
                    console.log(xhr.response)
                }else{
                    //控制台输出相应状态码
                    console.log(xhr.status)
                }
            }
        }
    })
```

### 案例——Promise封装fs读取文件操作

```javascript
/**
 * 封装一个函数mineReadFile 读取文件内容
 * 参数：path 文件路径
 * 返回：promise 对象
 */
const fs = require('fs')

function mineReadFile(path) {
    return new Promise((resolve, reject) => {
        fs.readFile(path, (err, data) => {
            if (err) reject(err)
            resolve(data)
        })
    })
}

mineReadFile('./resource/2022.09.15 组会推荐阅读论文.txt')
    .then(value => {
        //输出文件内容
        console.log(value.toString())
    }, reason => {
        console.log(reason)
    })
```

* 提示，在函数中return的promise实例对象不需要再return reject和resolve

### util.promisify方法进行promise风格转化

* promisify是node.js内置的方法，属于util这个模块

`util.promisify(original)` 传入一个遵循常见的错误优先的回调风格的函数，即以`(err, value) => ...` 回调作为最后一个参数，并返回一个promise版本

有了这个方法，以后每一个函数不必手动封装promise，可以直接将**回调函数风格方法**转换为promise的函数

```javascript
//引入util模块
const util = require('util')
//引入fs模块
const fs = require('fs')
//会返回一个新的函数
let mineReadFile = util.promisify(fs.readFile)

//这样就可以直接以promise实例对象的方式调用函数
mineReadFile('./resource/2022.09.15 组会推荐阅读论文.txt').then(value => {
    console.log(value.toString())
})
```

### 案例——ajax请求函数封装

```javascript
/*
            封装一个函数 sendAJAX 发送 GET AJAX 请求
            参数 URL
            返回结果 Promise 对象
        */
        function sendAJAX(url){
            return new Promise((resolve, reject) => {
                const xhr = new XMLHttpRequest()
                xhr.open('GET', url)
                xhr.send()
                //处理结果
                xhr.onreadystatechange = function(){
                    if(xhr.readyState === 4){
                        //判断成功
                        if(xhr.status >= 200 && xhr.status < 300){
                            //成功的结果
                            resolve(xhr.response)
                        }else{
                            reject(xhr.status)
                        }
                    }
                }
            })
        }
        sendAJAX('').then(value => {
            console.log(value)
        }, reason => {
            console.log(reason)
        })
```

# async与await

## async函数

1. 函数的返回值为promise对象
2. promise对象的结果由async函数执行的返回值决定

* **如果async函数返回值是非promise类型的数据，那么函数结果就是一个成功（fullfilled）的promise对象，return后边写什么，promiseResult就是什么**

```javascript
 async function main(){
            //如果返回值是非promise类型的数据，那么函数结果就是一个成功的promise对象
            return 521
        }

        let result = main()

        console.log(result) //结果为promise对象
```

![image-20221017231741624](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221017231741624.png)

* **如果返回的是一个promise的对象，return的结果就决定了main函数返回接口的状态**

```javascript
async function main(){
            //如果返回的是一个promise对象
            return new Promise((resolve, reject) => {
                //main函数的接口状态是成功的状态
                // resolve('OK')
                //反之同理
                reject('Error')
            })
        }
```

![image-20221017231832945](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221017231832945.png)

* 如果抛出了异常，则接口状态为失败

```javascript
async function main(){
            throw "oh no"
        }
```

![image-20221017231930794](C:\Users\Lenovo\AppData\Roaming\Typora\typora-user-images\image-20221017231930794.png)

## await表达式

1. await右侧的表达式一般为promise对象，但也可以是其他的值
2. 如果表达式是promise对象，await返回的是promise成功的值
3. 如果表达式是其他值，直接将此值作为await的返回值

```javascript
async function main(){
            //右侧为promise的情况
            let p = new Promise((resolve, reject) => {
                resolve('OK')
                // reject('Error')
            })
            //await必须写在async函数当中
            let res = await p //如果表达式是promise对象，await返回的是promise成功的值
            let res2 = await 20//如果表达式是其他值，直接将此值作为await的返回值
            console.log(res)
            console.log(res2)
        }

        main()
```

## 注意

1. await必须写在async函数中，但async函数中可以没有await
2. 如果await的promise失败了，就会抛出异常，需要通过try...catch捕获处理

```javascript
 async function main(){
            //右侧为promise的情况
            let p = new Promise((resolve, reject) => {
                // resolve('OK')
                reject('Error')
            })
            //如果promise失败 需要通过try..catch处理
            try{
                let res3 = await p
            }catch(e){
                console.log(e)
            }
        }

        main()
```

## 案例——async与await结合实践

* 使用回调函数方法执行多个异步函数（回调地狱）

```javascript
fs.readFile('./resource/1.txt', (err, data1) => {
    if(err) throw err
    fs.readFile('./resource/2.txt', (err, data2) => {
        if(err) throw err
        fs.readFile('./resource/3.txt', (err, data3) => {
            if(err) throw err
            console.log(data1 + data2 + data3)
            
        })
    })
})
```

* 错误处理方面比纯回调函数方便的多
* 在async和await的使用过程当中我们是看不到回调函数的，在promise的then和catch方法里边有

```javascript
const fs = require('fs')
//这里用的是util模块的promisify方法
const util = require('util')
const mineReadFile = util.promisify(fs.readFile)

//async 与 await
async function main() {
    
    try {
        let data1 = await mineReadFile('./resource/1.txt')
        let data2 = await mineReadFile('./resource/2.txt')
        let data3 = await mineReadFile('./resource/3.txt')
        console.log(data1 + data2 + data3)
    } catch (e) {
        console.log(e)
    }
}
```



## Promise的三种状态

Promise对象有三种状态，他们分别是：

1. pending：等待中，或者进行中，表示还没有得到结果
2. resolved(Fulfilled)：已经完成，表示得到了我们想要的结果，可以继续向下执行
3. rejected：也表示得到结果，但是由于结果并非我们所愿，因此拒绝执行

这三种状态不受外界影响，而且状态只能从pending改变为resolved或者rejected，并且不可逆

## 五、promise的用法 

### 1.promise的实例方法

①then()得到异步任务的正确结果

②catch()获取异常信息

③finally()成功与否都会执行(尚且不是正式标准)
 **注意：\**then方法可以接受两个函数\**，第一个函数为\**promise状态为成功的回调函数\**，第二个函数为\**promise状态为失败的回调函数\**（\**可以不写，一般用catch方法捕获promise状态为失败的异常信息）\****

### 2.promise的对象方法(p1,p2,p3为promise的实例对象)

①Promise.all()并发处理多个异步任务，所有任务都执行完成才能得到结果

```JavaScript
Promise.all( [p1,p2,p3] ) .then ( (result) => {consoleog (result)
})
```

②Promise.race()并发处理多个异步任务，只要有一个任务完成就能得到结果


```javascript
Promise.race ( [p1,p2,p3] ).then ( (result)=>{
console. log (result)
})
```

## 六、promise的特点 

**1.在Promise对象的构造函数中，将一个函数作为第一个参数。**

**2.而Promise对象的构造函数的第一个参数中的这个函数，就是用来处理Promise的状态变化，这个函数的第一个参数表示promise的状态为成功，第二个参数表示promise的状态为失败，这两个参数（名字可以自己命名）都为一个函数，他们的作用分别是将promise状态修改为resolved(成功)和rejected（失败）。**

```javascript

        function fn(flag) {
            //构造函数
               return new Promise(function(resolve, reject) {
                if (flag === true) {
                    resolve('promise状态为成功！')
                };
                if (flag === false) {
                    reject('promise状态失败！')
                };
            })
        }
 
        console.log(fn(true));

```

**3. Promise对象中的then方法，可以接收构造函数中处理的状态变化，并分别对应执行。then方法有2个函数参数，第一个函数接收resolved(promise状态为成功)的执行，第二个函数接收reject(promise状态为失败)的执行。**

```javascript

        function fn(flag) {
            return new Promise(function(resolve, reject) {
                if (flag === true) {
                    resolve('promise状态为成功！')
                };
                if (flag === false) {
                    reject('promise状态失败！')
                };
            }).then(function(res) {
                    console.log(res);
                },
                function(err) {
                    console.log(err);
                })
        }
        fn(true)
fn(false)
```

**4.promise的状态只能从 未完成->完成, 未完成->失败 且状态不可逆转。**

promise的异步结果，只能在完成状态时才能返回，而且我们在开发中是根据结果来选择来选择状态的，然后根据状态来选择是否执行then()。

**实例化的Promise内部会立即执行，then方法中的异步回调函数会在脚本中所有同步任务完成时才会执行。因此，promise的异步回调结果最后输出。示例代码如下：**

```javascript
var promise = new Promise(function(resolve, reject) {
  console.log('Promise instance');
  resolve();
});
 
promise.then(function() {
  console.log('resolved result');
});
for(var i=0;i<100;i++) {
console.log(i);
/*
Promise instance
1
2
3
...
99
100
resolved result
*/
```

###  5.Promise.then（）方法

①then()函数返回的实际也是一个Promise对象（无论函数内部返回什么类型的数据，函数都会进行加工返回一个promise对象）

```javascript

    <script>
        function fn(flag) {
            return new Promise(function(resolve, reject) {
                if (flag === true) {
                    resolve('promise状态为成功！')
                };
                if (flag === false) {
                    reject('promise状态失败！')
                };
            })
        }
        console.log(fn(true).then());
    </script>
```

![img](https://img-blog.csdnimg.cn/c79a64277efd4651b80b9b16da1548f7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAQ2lycm9k,size_20,color_FFFFFF,t_70,g_se,x_16)

②then()函数内部返回为普通值（非Promise类型的属性），返回的普通值会直接传递给下一个then，通过then参数中函数的参数接收该值。

这时then()函数返回的Promise对象状态为成功（resloved），then（）函数的返回值为对象的成功值，如return 123，返回的Promise对象值为123，如果没有返回值，是undefined

```javascript

    <script>
        const p = new Promise((resolve, reject) => {
            setTimeout(() => {
                resolve('用户数据');
            })
        });
        let result = p.then(value => {
            return 123;
        }, reason => {
            console.log(reason)
        })
        console.log(result);
        result.then(function(res) {
            console.log(res);
        })
    </script>
```

![img](https://img-blog.csdnimg.cn/9885cf20a5744118a44f630fec0a002c.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAQ2lycm9k,size_20,color_FFFFFF,t_70,g_se,x_16)

③ 当then()函数内部返回的是Promise类型的对象时，then（）函数的返回的Promise对象的状态值为这个Promise对象的状态值，成功值也是如此，返回的该promise对象会调用下一个then方法。

```javascript
<script>
    const p = new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('用户数据');
        })
    });
    let result = p.then(value => {
        return new Promise((resolve, reject) => {
            resolve('ok')
        })
    }, reason => {
        console.log(reason)
    })
    console.log(result);
    result.then(function(res) {
        console.log(res);
    })
</script>
```

控制台运行：

![img](https://img-blog.csdnimg.cn/22b5cf4b67384d8abd1c9925b24e4886.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAQ2lycm9k,size_20,color_FFFFFF,t_70,g_se,x_16)

④当then()函数内部果抛出异常，则then（）函数的返回值状态也是rejected

```javascript
<script>
        const p = new Promise((resolve, reject) => {
            setTimeout(() => {
                resolve('用户数据');
            })
        });
        let result = p.then(value => {
            throw 123
        }, reason => {
            console.log(reason)
        })
        console.log(result);
    </script>
```

![img](https://img-blog.csdnimg.cn/c7b18fb9371b4b819f2830175e64f797.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAQ2lycm9k,size_20,color_FFFFFF,t_70,g_se,x_16)

### 6.Promise.catch（）方法

①catch（）函数只有一个回调函数，意味着如果Promise对象状态为失败就会调用catch（）方法并且调用回调

```javascript

//catch（）函数只有一个回调函数，意味着如果Promise对象状态为失败就会调用catch（）方法并且调用回调函数
<script>
const p = new Promise((resolve, reject) => {
setTimeout(()=>{
reject('出错啦')
},1000)
})
 
p.catch(reason => {
console.log(reason)
})
</script>
```

## 七、总结

1.promise其实就是一个对象或者说是构造函数

2.promise的出现（es6） 就是解决异步编程和回调地狱等问题，async和await的出现（ES8）就是基于promise的一种解决异步编程的终极解决方案（简化代码等等）

 3.在前端中，ajax和axios都会用到异步编程，axios更是基于promise的，所以一定要掌握promise以及用async和await搭配promise的使用

# async-await和promise的区别

### 关于async-await的

`async`和`await`关键字让我们可以用一种更简洁的方式写出基于[`Promise`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)的异步行为，而无需刻意地链式调用`promise`。

await表达式会暂停整个async函数的执行进程并出让其控制权，只有当其等待的基于promise的异步操作被兑现或被拒绝之后才会恢复进程。promise的解决值会被当作该await表达式的返回值。使用`async` / `await`关键字就可以在异步代码中使用普通的`try` / `catch`代码块。

async函数一定会返回一个promise对象。如果一个async函数的返回值看起来不是promise，那么它将会被隐式地包装在一个promise中。

### 关于Promise

`promise`的精髓是状态的传递，方法的封装者并不需要关心异步方法的执行结果，方法的封装者通过状态传递拿到执行结果书写自己的逻辑，使得封装者与使用者的真正解耦。

这种状态传递有种发布订阅的味道，回调地狱并非书写上的地狱而诟病，promise 的链式调用也会有**地狱**之感，而回调地狱真正为之诟病的是没有真正解耦。**async 是 promise 的语法糖**。

 **Async/Await 允许异步程序用同步的方式执行，这样两个异步且有依赖关系的程序不仅可以按照先后顺序执行，而且对于其它同步程序来说，它仍然是异步的，不会阻塞主线程。**

在 Promise 为出现之前，我们用回调函数来编写异步程序，来满足日常开发的异步需求。随着前端的发展，代码的日益增多，逻辑逐渐复杂，大量使用回调函数的代码，让程序员开始头疼。代码可读性低、维护困难、大量前台回调函数，看的程序员是直接怀疑人生。

为了解决工程师们面临的这个问题，Promise 诞生了。它提供了一些 API，让工程师们可以用一种新的方式来编写异步程序。**通过 then 方法来写异步程序执行成功后执行的代码，用 catch 方法来捕捉异常，而且可以链式调用。 函数地狱和回调函数造成的程序可读性差的问题**，被解决了，小伙伴们又可以快乐的编程了。

等等，似乎还有个问题没有被解决。在使用 Promise 的时候，可能会一个 Promise 中有多个 Promise。**如果是多个独立的 Promise，可以用 Promise. all 来解决，但是它们之间如果彼此之间有依赖关系，需要有特定的执行顺序，这个时候 Promise 就无法解决了。** Promise 也遇到问题了，怎么办，该怎么解决呢？

就在工程师们发愁的时候，Async/Await“脚踏七彩祥云”出现在了工程师面前，说到：“你的问题我来解决”。 在 Promise 为出现之前，我们用回调函数来编写异步程序，来满足日常开发的异步需求。随着前端的发展，代码的日益增多，逻辑逐渐复杂，大量使用回调函数的代码，让程序员开始头疼。代码可读性低、维护困难、大量前台回调函数，看的程序员是直接怀疑人生。

为了解决工程师们面临的这个问题，Promise 诞生了。它提供了一些 API，让工程师们可以用一种新的方式来编写异步程序。通过 then 方法来写异步程序执行成功后执行的代码，用 catch 方法来捕捉异常，而且可以链式调用。 函数地狱和回调函数造成的程序可读性差的问题，被解决了，小伙伴们又可以快乐的编程了。

等等，似乎还有个问题没有被解决。在使用 Promise 的时候，可能会一个 Promise 中有多个 Promise。**如果是多个独立的 Promise，可以用 Promise. all 来解决，但是它们之间如果彼此之间有依赖关系，需要有特定的执行顺序，这个时候 Promise 就无法解决了。** Promise 也遇到问题了，怎么办，该怎么解决呢？

```javascript
    //不用async-await解决回调地狱
    function request1(){
        return new Promise(resolve => resolve(1))
    }
    function request2(){
        return new Promise(resolve => resolve(2))
    }
    function request3(){
        return new Promise(resolve => resolve(3))
    }
	//then里边可以return Promise 防止回调地狱
    request1.then(res => {
        return request2()
    })
    .then(res => {
        return request3()
    })
    .then(res => {
        return 
    })
```



就在工程师们发愁的时候，Async/Await“脚踏七彩祥云”出现在了工程师面前，说到：“你的问题我来解决”。 **Async/Await 允许异步程序用同步的方式执行，这样两个异步且有依赖关系的程序不仅可以按照先后顺序执行，而且对于其它同步程序来说，它仍然是异步的，不会阻塞主线程。 就这样 Promise 产生的问题，被 Async/Await 解决了，工程师们终于可以放心大胆的去写程序了。**

最后说一下，**Async/Await 是在 Promise 的基础上实现的，它的返回值仍然是一个 Promise，所以工程师们仍然可以链式的使用 then 或 catch 等 API。**就这样 Promise 产生的问题，被 Async/Await 解决了，工程师们终于可以放心大胆的去写程序了。

最后说一下，Async/Await 是在 Promise 的基础上实现的，它的返回值仍然是一个 Promise，所以工程师们仍然可以链式的使用 then 或 catch 等 API。

### 相同点和不同点

**相同点**

1. `Promise`和 `async-await` 都是优化异步编程体验的解决方案。

**不同点**

1. `Promise` 是应用层的解决方案，它有一个规范，不同的语言也可以实现，它只能异步的处理错误，在`js` 里它本质上是一个对象。
2. `async-await` 是语言层的解决方案，它可以说是 `Promise`的补充，可以让用户像编写同步代码一样编写异步代码，通过`try-catch` 可以同步地处理错误。
3. `Promise` 更多应用在函数封装中，`async`用在函数的使用中。
4. `Promise`链式调用相当于一个新的回调地狱， 也不能统一处理异常。 `Promise` 本身是同步函数，多个不会等待。
5. `async-await`用同步的写法使得可读性更强，同时方便 `try-catch` 捕获异常， `async-await` 有明确的前后关系，可读性好。

# 面试题

1. 下面代码执行结果是

```javascript
const promise = new Promise((resolve, reject) => {
  console.log(1);
  console.log(2);
});
promise.then(() => {
  console.log(3);
});
console.log(4);
```

最后应该输出1 2 4。这样最主要的就是3，要知道promise.then是微任务，会在所有的上一轮宏任务执行完之后才会执行，同时需要promise内部的状态发生变化，因为这里内部没有发生变化，所以不输出3。

2. 下面代码的执行结果是

```javascript
const promise1 = new Promise((resolve, reject) => {
  console.log('promise1')
  resolve('resolve1')
})
const promise2 = promise1.then(res => {
  console.log(res)
})
console.log('1', promise1);
console.log('2', promise2);
```

输出结果如下：

```
'promise1'
'1' Promise{<resolved>: 'resolve1'}
'2' Promise{<pending>}
'resolve1'
```

需要注意的是，直接打印promise1，会打印出它的状态值和参数。

这里说一下这道题的具体思路：

- script是一个宏任务，按照顺序执行这些代码
- 首先进入Promise，执行该构造函数中的代码，打印`promise1`
- 碰到`resolve`函数, 将`promise1`的状态改变为`resolved`, 并将结果保存下来
- 碰到`promise1.then`这个微任务，将它放入微任务队列
- `promise2`是一个新的状态为`pending`的`Promise`
- 执行同步代码1， 同时打印出`promise1`的状态是`resolved`
- 执行同步代码2，同时打印出`promise2`的状态是`pending`
- 宏任务执行完毕，查找微任务队列，发现`promise1.then`这个微任务且状态为`resolved`，执行它。

这样，就执行完了所有的的代码。

3. 下面代码的执行结果是：

   ```javascript
   const promise = new Promise((resolve, reject) => {
     console.log(1);
     setTimeout(() => {
       console.log("timerStart");
       resolve("success");
       console.log("timerEnd");
     }, 0);
     console.log(2);
   });
   promise.then((res) => {
     console.log(res);
   });
   console.log(4);
   ```

   
