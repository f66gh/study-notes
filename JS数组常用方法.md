# JS数组常用方法

1. join（）数组转字符串 

   注意arr.join()返回原数组的加,字符串形式，需要arr.join("")才能真正拼接成所有元素连在一起的字符串

   数组转字符串，方法只接收一个参数：即默认为逗号[分隔符](https://so.csdn.net/so/search?q=分隔符&spm=1001.2101.3001.7020)（）。
   
   join实现重复字符串

```javascript
    function Func(str, n){
        return new Array(n + 1).join(str)
    }

    console.log("str:", Func('嗨', 4)) //嗨嗨嗨
```

2. push和pop
3. shift和unshift
4. sort排序 方法用于对数组的元素进行排序，如果括号内啥也不写则是按照字符编码的顺序进行排序

，要实现这一点必须使用一个排序函数（原数组改变）

5.  reverse()反转数组 方法用于颠倒数组中元素的排序（原数组改变）

6.  concat连接两个或多个数组（原数组不改变）**该方法不会改变现有的数组，而仅仅会返回被连接数组的一个副本。**在没有给 concat()方法传递参数的情况下，它只是复制当前数组并返回副本。

   如果传入的是一个二维数组呢？

```javascript
	var arr=[1,2,3,4];
	var arr2=[11,[12,13]] 
	var arrCopy = arr.concat(arr2);	
	console.log(arrCopy); // [1, 2, 3, 4, 11, Array(2)]
	console.log(arr); // [1, 2, 3, 4] (原数组未改变)
```

从上面代码中可以看出concat方法只能将传入数组中的每一项添加到数组中，如果传入数组中有些项是数组，那么也会把这一数组项当作一项添加到arrCopy中。

7. slice数组截取（不会改变原数组）

   **arr.slice(\**start\** , \**end\**);**

   **start**：可选。规定从何处开始选取。如果是负数，那么它规定从数组尾部开始算起的位置。也就是说，-1 指最后一个元素，-2 指倒数第二个元素，以此类推。

   **end**：可选。规定从何处结束选取。该参数是数组片断结束处的数组下标。如果没有指定该参数，那么切分的数组包含从 start 到数组结束的所有元素。如果这个参数是负数，那么它规定的是从数组尾部开始算起的元素。**返回的切片不包含末位**

8. splice()（数组更新）

   splice()方法向/从数组中添加/删除项目，然后返回被删除的项目（该方法会改变原始数组）

   **arr.splice(index, howmany, item1, ..., itemX)**

   index: 必须。整数，规定添加/删除项目的位置，使用负数可以从数组结尾处规定位置。

   howmany：必须。要删除的项目数量。如果设置为0，则不会删除项目。

   item1, ..., itemX：可选，向数组添加的新项目。

   **返回值：含有被删除元素的数组，若没有删除元素则返回一个空数组。**

```javascript

	var arr = ["张三","李四","王五","小明","小红"];
	/**************删除"王五"****************/
	var arrReplace1 = arr.splice(2,1);	
	console.log(arrReplace1);  // ["王五"] 
	console.log(arr);  // ["张三", "李四", "小明", "小红"] (原数组改变)
	//删除多个
	var arrReplace2 = arr.splice(1,2);	
	console.log(arrReplace2);  //  ["李四", "小明"] 
	console.log(arr);  // ["张三", "小红"]
	/**************添加"小刚"****************/
	var arrReplace3 = arr.splice(1,0,"小刚");
	console.log(arrReplace3);  // [] (没有删除元素，所以返回的是空数组)
	console.log(arr);  // ["张三", "小刚", "小红"]
	//添加多个
	var arrReplace4 = arr.splice(3,0,"刘一","陈二","赵六");
	console.log(arrReplace4);  // []
	console.log(arr);  // ["张三", "小刚", "小红", "刘一", "陈二", "赵六"]
	/**************"王五"替换"小刚"****************/
	var arrReplace5 = arr.splice(1,1,"王五");
	console.log(arrReplace5);  // ["小刚"]
	console.log(arr);  // ["张三", "王五", "小红", "刘一", "陈二", "赵六"]
	//替换多个
	var arrReplace6 = arr.splice(1,4,"李四");
	console.log(arrReplace6);  // ["王五", "小红", "刘一", "陈二"]
	console.log(arr);  // ["张三", "李四", "赵六"]
```

## ES5新增方法

### **两个索引方法：indexOf()和lastIndexOf()**

两个方法都返回要查找的顶在数组中首次出现的位置，在没找到的情况下返回-1

1. **array.indexOf(item, start)**

   item: 必须，查找的元素

   start：可选的整数参数，规定在数组中开始检索的位置。如果省略该参数，则将从array[0]开始检索

2. array.lastIndexOf(item, start) **（从数组的末尾开始向前查找，返回的额仍然是从数组第0位开始数的位置）**

   item：必须查找的元素

   start：可选的整数参数。规定在数组中开始检索的位置。如果省略该参数，则将从array[array.length - 1]开始检索 

### **五个迭代方法：forEach() map() filter() some() every()**

这几个方法语法都一样，都不会改变原数组

1. forEach()：对数组进行遍历循环，这个方法没有返回值，jQuery()也提供了相应的each()方法。

   语法：**array.forEach(function(currentValue, index, arr){**

   **}, thisValue)**

   currentValue：必须，当前元素

   index：可选，当前元素的索引值

   arr：可选，当前元素所属的数组对象

   thisValue：可选。传递给函数的值一般用this值，如果这个参数为空，undefined会传递给this值

```html
<script>
	var Arr = [1,4,7,10];
	Arr.forEach(function(currentValue, index, arr){
		console.log(index+"--"+currentValue+"--"+(arr === Arr));		
	})
	// 输出：
	// 0--1--true
	// 1--4--true
	// 2--7--true
	// 3--10--true	注意arr等于Arr，说明是arr是引用传值
</script>
```

[forEach](https://so.csdn.net/so/search?q=forEach&spm=1001.2101.3001.7020)() 方法对数组的每个元素执行一次提供的函数。

```javascript
lst.forEach(alert) //相当于每个都alert一遍
```

thisValue如果不加则返回window，注意如果要让this返回thisValue的值，回调函数必须写成普通函数，箭头函数自动找外边一层的this了

```javascript
    lst.forEach(function(item, index, arr){
        console.log(item, index, arr === lst, this)
    }, [4,5,6])
```

2. map()：指映射，**方法返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值。**

   array.map(function(currentValue, index, arr){}, thisValue)

3. filter()：过滤，**方法创建一个新数组**，其包含通过所提供函数实现的测试所有元素。和filter()方法类似，jquery中有个grep()方法也用于数组元素过滤筛选

```javascript
    var arr = [1,4,6,8,10];
    var result1 = arr.filter(function(currentValue){
        return currentValue>5;
    });
    console.log(result1);  // [6, 8, 10]
    var result2 = arr.filter(function(currentValue){
        return currentValue>"5";
    });
    console.log(result2);  // [6, 8, 10]
```

当我们分别设置item > 5和item > "5"时, 返回的结果是一样的，由此我们可以看出**函数****支持弱等于（==），不是必须全（===）。**

4. every()：判断数组中每一项是否都满足条件，只有所有项都满足条件才返回true

   语法：array.every(function(currentValue, index, arr){}, thisValue)

```html
<script>
	var arr = [1,4,6,8,10];
	var result1 = arr.every(function(currentValue){
	    return currentValue< 12;
	});
	console.log(result1);  // true
	var result2 = arr.every(function(currentValue){
	    return currentValue> 1;
	});
	console.log(result2);  // false
</script>
```

5. some()：判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回true

   语法：array.some(function(currentValue, index, arr){}, thisValue)

```html
<script>
	var arr = [1,4,6,8,10];
	var result1 = arr.some(function(currentValue){
	    return currentValue> 10;
	});
	console.log(result1);  // false
	var result2 = arr.some(function(currentValue){
	    return currentValue> 5;
	});
	console.log(result2);  // true
</script>
```

### 两个归并方法：reduce(), reduceRight()

这两个方法都会迭代数组中的所有项，然后生成一个最终返回值。他们都接收两个参数，**第一个参数**是每一项调用的函数，函数接受四个参数分别是初始值，当前值，索引值，和当前数组，**函数需要返回一个值，这个值会在下一次迭代中作为初始值**。**第二个参数**是迭代初始值，参数可选，如果缺省，初始值为数组第一项，从数组第一个项开始叠加，缺省参数要比正常传值少一次运算。

```javascript
	var arr = [1,2,3,4,5];
	var result1 = arr.reduce(function(total,cur,index,arr){	
		console.log("total:"+total+",cur:"+cur+",index:"+index);
	    return total+cur;
	});
	console.log("结果："+result1);
	// 输出
	// total:1,cur:2,index:1
	// total:3,cur:3,index:2
	// total:6,cur:4,index:3
	// total:10,cur:5,index:4
	// 结果：15
	var result2 = arr.reduce(function(total,cur,index,arr){	
		console.log("total:"+total+",cur:"+cur+",index:"+index);
	    return total+cur;
	},10);
	console.log("结果："+result2);
	// 输出
	// total:10,cur:1,index:0
	// total:11,cur:2,index:1
	// total:13,cur:3,index:2
	// total:16,cur:4,index:3
	// total:20,cur:5,index:4
	// 结果：25
```

从上面代码我们可以看出，当我们不给函数传递迭代初始值时初始值 ***\*total\**** 为数组第一项，函数从数组第二项开始迭代；若我们给函数传递迭代初始值，则函数从数组第一项开始迭代。

## ES6数组新增方法（注意浏览器兼容）

1. Array.from()方法是用于类似数组的对象（有length属性的对象）和可遍历对象转为真正的数组

```javascript
	let json ={
	    '0':'卢',
	    '1':'本',
	    '2':'伟',
	    length:3
	}
	let arr = Array.from(json);
	console.log(arr); // ["卢", "本", "伟"]	
```

**注意，被转换对象中的属性名必须是数字字符，而且必须要加length属性**

2. Array.of()

   方法是将一组值转变为数组，参数不分类型，只分数量，数量为0返回空数组

```javascript

	let arr1 = Array.of(1,2,3);	
	let arr2 = Array.of([1,2,3]);
	let arr3 = Array.of(undefined);
	let arr4 = Array.of();
	console.log(arr1); // [1, 2, 3]
	console.log(arr2); // [[1, 2, 3]]
	console.log(arr3); // [undefined]
	console.log(arr4); // []
```

3. find()

方法返回通过测试（函数内判断）的数组的第一个元素的值。方法为数组中的每个元素都调用一次函数执行。当数组中的元素在测试条件时返回 true 时, find() 返回符合条件的元素，之后的值不会再调用执行函数。如果没有符合条件的元素返回undefined。

回调函数可以接收3个参数，**依次为当前的值（currentValue）、当前的位置（index）、原数组（arr）**

语法：array.find((currentValue, index, arr) => {})

```javascript
	let Arr = [1,2,5,7,5,9];
	let result1 = Arr.find(function(currentValue,index,arr){			
		return currentValue>5;
	});
	let result2 = Arr.find(function(currentValue,index,arr){			
		return currentValue>9;
	});
	console.log(result1); // 7
	console.log(result2); // undefined
```

**注意：**find() 对于空数组，函数是不会执行的。find() 并没有改变数组的原始值。

**find()实现根据id取出数组中的对象**

```html
<script>
	let Arr = [
		{
			id:1,
			name:"张三"
		},
		{
			id:2,
			name:"李四"
		}		
	];
	let obj = Arr.find(function(currentValue,index,arr){			
		return currentValue.id===1;
	});
	console.log(obj.name); // 张三
</script>
```

4. findIndex()

findIndex和find差不多，不过默认返回的是索引，如果没有符合条件的元素返回 -1

```javascript
	let Arr = [1,2,5,7,5,9];
	let result1 = Arr.findIndex(function(currentValue,index,arr){			
		return currentValue>5;
	});
	let result2 = Arr.findIndex(function(currentValue,index,arr){			
		return currentValue>9;
	});
	console.log(result1); // 3
	console.log(result2); // -1
```

5. fill()

**fill()方法用一个固定值填充一个数组中从起始索引到终止索引内的全部元素。不包括终止索引**

语法：array.fill(value, start, end)

***\*value：\****必需。填充的值。

***\*start：\****可选。开始填充位置。如果这个参数是负数，那么它规定的是从数组尾部开始算起。

***\*end：\****可选。停止填充位置 (默认为 *array*.length)。如果这个参数是负数，那么它规定的是从数组尾部开始算起。

```javascript
let arr = [1,2,3,4,5,6];
    arr.fill(0);  // [0, 0, 0, 0, 0, 0]
    arr.fill(0,1);  // [1, 0, 0, 0, 0, 0] 
    arr.fill(0,1,2);  // [1, 0, 3, 4, 5, 6]
    arr.fill(0,-1);  // [1, 2, 3, 4, 5, 0]
    arr.fill(0,1,-1);  // [1, 0, 0, 0, 0, 6]
```

6. 遍历数组方法 keys() values() entries()

   这三个方法都是**返回一个遍历器对象**，**可用for...of遍历循环**，唯一区别：keys()是对键名的遍历，values()是对键值的遍历entries()是对键值对的遍历

```javascript
let arr = ["a","b","c","d"];
	for(let i of arr.keys()){ //for...in不行
		console.log(i);
	}
```

```javascript
	let arr = ["a","b","c","d"];
	for(let i of arr.values()){
		console.log(i);
	}
```

```javascript
    let arr = ["a","b","c","d"];
    for(let i of arr.entries()){
        console.log(i);
    }
    //打印：
    // [0, "a"]
    // [1, "b"]
    // [2, "c"]
    // [3, "d"]
    for(let [idx,item] of arr.entries()){ //注意这里只能用中括号括起来
        console.log(idx+":"+item);
    }
```

7. includes()

   方法用来判断一个数组是否包含一个指定的值，如果是返回true，否则返回false

   语法：**arr.includes(searchElement, fromIndex)**

   ***\*searchElement\** ：** 必须。需要查找的元素值。

   ***\*fromIndex：\****可选。从该索引处开始查找 searchElement。如果为负值，则按升序从 array.length + fromIndex 的索引开始搜索。默认为 0。

   ```javascript
   let arr = ["a","b","c","d"];
   	let result1 = arr.includes("b");
   	let result2 = arr.includes("b",2);
   	let result3 = arr.includes("b",-1);
   	let result4 = arr.includes("b",-3);
   	console.log(result1);  // true
   	console.log(result2);  // false
   	console.log(result3);  // flase
   	console.log(result4);  // true
   ```

8. copyWithin

   方法用于从数组的指定位置拷贝元素到数组的另一个指定位置中，会覆盖原有成员

**语法：array.copyWithin(\**target\** , \**start\** , \**end\**)**

***\*target\**** ：必需。从该位置开始替换数据。

***\*start\**** ：可选。从该位置开始读取数据，默认为 0 。如果为负值，表示倒数。

***\*end：\**** 可选。到该位置前停止读取数据，默认等于数组长度。如果为负值，表示倒数。

```javascript
let arr = [1,2,3,4,5,6];
	let result1 = [1,2,3,4,5,6].copyWithin(0);
	let result2 = [1,2,3,4,5,6].copyWithin(0,1);
	let result3 = [1,2,3,4,5,6].copyWithin(1,3,5);
	let result4 = [1,2,3,4,5,6].copyWithin(1,2,-1);
	let result5 = [1,2,3,4,5,6].copyWithin(1,-4,6);
	console.log(result1);  // [1, 2, 3, 4, 5, 6]
	console.log(result2);  // [2, 3, 4, 5, 6, 6]
	console.log(result3);  // [1, 4, 5, 4, 5, 6]
	console.log(result4);  // [1, 3, 4, 5, 5, 6]
	console.log(result5);  // [1, 3, 4, 5, 6, 6]
```

## 面试题：数组去重

双层for循环

indexOf/lastIndexOf 和循环

includes()查找想要的数组元素 

set去重

[(128条消息) js数组去重的10种方法_sun6sian的博客-CSDN博客](https://blog.csdn.net/weixin_42412046/article/details/81459294?ops_request_misc=%7B%22request%5Fid%22%3A%22167763799216800180629569%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=167763799216800180629569&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-81459294-null-null.142^v73^control,201^v4^add_ask,239^v2^insert_chatgpt&utm_term=数组去重&spm=1018.2226.3001.4187)

## 面试题：清空数组

length属性可设置或返回数组长度。当length属性的值小于数组本身的长度，数组中后面的元素将被截断；如果length属性的值为0，则可以清空整个数组。

**使用splice()删除全部数组元素**

**使用delete运算符**

delete 运算符可以用来删除指定下标的数组元素，删除后的元素为空位元素，删除数组长度不变。

利用delete 运算符和循环语句可以实现清空数组
