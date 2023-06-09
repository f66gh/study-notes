# Web性能优化手段

那么，前端优化的目的是什么 ?

1. 从用户角度而言，优化能够让页面加载得更快、对用户的操作响应得更及时，能够给用户提供更为友好的体验。
2. 从服务商角度而言，优化能够减少页面请求数、或者减小请求所占带宽，能够节省可观的资源。
   总之，恰当的优化不仅能够改善站点的用户体验并且能够节省相当的资源利用。

## 页面级优化

1. 减少 HTTP请求数：从设计层面简化页面，合理设置HTTP缓存
2. 懒加载
3. 将CSS放在HEAD中
4. 避免重复的资源请求（路由传参）

## 代码级优化

1. 减少作用域链查找。如果在循环中需要访问非本作用域下的变量时请在遍历之前用局部变量缓存该变量，并在遍历结束后再重写那个变量，这一点对全局变量尤其重要，因为全局变量处于作用域链的最顶端，访问时的查找次数是最多的。

   低效写法：

   ```javascript
   // 全局变量 
   var globalVar = 1; 
   function myCallback(info){ 
   	for( var i = 100000; i--;){ 
   	//每次访问 globalVar 都需要查找到作用域链最顶端，本例中需要访问 100000 次 
   		globalVar += i; 
   	}
   } 
   ```

   高效写法：

   ```javascript
   // 全局变量 
   var globalVar = 1; 
   function myCallback(info){ 
   	//局部变量缓存全局变量 
   	var localVar = globalVar; 
   	for( var i = 100000; i--;){ 
   	//访问局部变量是最快的 
   		localVar += i; 
   	} 
   	//本例中只需要访问 2次全局变量 在函数中只需要将 globalVar中内容的值赋给localVar 中区
   	globalVar = localVar; 
   }
   ```

此外，要减少作用域链查找还应该减少闭包的使用。

2. 数据访问
   　　Javascript中的数据访问包括直接量 (字符串、正则表达式 )、变量、对象属性以及数组，其中对直接量和局部变量的访问是最快的，对对象属性以及数组的访问需要更大的开销。当出现以下情况时，建议将数据放入局部变量：
   　　a. 对任何对象属性的访问超过 1次
   　　b. 对任何数组成员的访问次数超过 1次
   　　另外，还应当尽可能的减少对对象以及数组深度查找。
3. 字符串拼接
   　　在 Javascript中使用"+" 号来拼接字符串效率是比较低的，因为每次运行都会开辟新的内存并生成新的字符串变量，然后将拼接结果赋值给新变量。与之相比更为高效的做法是使用数组的 join方法，即将需要拼接的字符串放在数组中最后调用其 join方法得到结果。不过由于使用数组也有一定的开销，因此当需要拼接的字符串较多的时候可以考虑用此方法。