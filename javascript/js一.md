# JS查漏补缺一

[TOC]



## 一、类型强制转化

JS有基本的6种数据类型：Number, Boolean, String, Object, Null 和 Undefined

其中除了Object都是都是原始值类型，Object包括由此衍生的Array，Set都是引用值类型，引用值类型的特点就是在实例化一个对象的时候，会在堆内存中开辟一部分连续空间，后面由栈中的一个个变量引用这个空间（实际上通过保存该空间的首地址），js自带引用计数垃圾回收机制，一旦某个变量的引用计数为0，那么该对象所对应的堆空间就会被释放，具体的垃圾回收机制我们不需要管，我们所做的就是当一个变量不再需要的时候，就可以把它赋值为Null。

### 常见的三种原始值类型转化

1. 转为String
   - toString()

   特点：对Null 和 Undefined使用该方法会报错；

   - String()

   特点：本质是把原始值包装成对象，调用了new String()函数，对Null 和 Undefined也可以使用该方法

2. 转为Boolean

   - Boolean()

   特点：对于一些是肯定的东西都为true，对于一些是否定东西都为false，比如非空字符串，非0数字，非null，非undefined，非NaN都是True

3. 转为Number

   - Number()

   特点：只认识纯数字的字面量，如果字面量含非数字，则全转换为NaN

   **Tips:**NaN是Number类型，R里也是

   - parseInt()

   特点：这是常用的转化为整数的方法，它会从头开始直到遇到第一个非数字的字符则结束，如 "123px" => 123，"123.234" => 123, 所以它不能识别小数

   - parseFloat()

   特点：这是常用的转化为小数的方法，它可以识别第一个小数点，只会遇到第二个非数字字符则结束，如 "123.456.789" => 123.456, "123.456sdd" => 123.45

### 变量提升（let const var）

var声明的变量有个特点，存在变量提升，同时函数也是有变量提升的（貌似C++中必须先有函数声明，才能调用，函数定义可以在任意处）

**未用var关键字声明的变量不存在变量提升  ! ! !**

eg:

```javascript
var x = 5;
function f() {
	console.log(x);
	var x = 3;
}

f()
// undefined

以上函数声明相当于：
function f() {
	var x;
	console.log(x);
	x = 3;
}

以上就是提升（hoist）,在内部用var声明的变量无论如何都会在当前作用域中寻找，不会再离开本作用域，再看下面的没有var关键字
var x = 5;
function f() {
	console.log(x);
	x = 3;
}
console.log(x);

f() 
// 5 3
这里就不是变量提升了，x会在本层找是否声明了x变量，没有则继续往上层寻找，找到了则打印,后面的修改都是全局变量x。

```

由于js一开始没有块级作用域，所以后来引进了const，let，这两个关键字都是引进块级作用域的，以及这两个关键字是没有变量提升的，如果强制使用为声明的变量，只会导致**暂时性死区**。

没有块级作用域导致的问题：[var、let、const 区别？ - 简书 (jianshu.com)](https://www.jianshu.com/p/4e9cd99ecbf5)

let的特点：

- **形如for (let x...)的循环在每次迭代时都为x创建新的绑定**
- 用let重定义变量会抛出一个语法错误，也就是不能声明相同的变量

const特点：

- 声明即赋值，且后面不能改变（这个和C++一样）

## 二、对象

工厂函数生成对象的缺点，无法识别某个对象到底是什么类型，**Object构造函数包装的对象type of永远都是Object类型**。因而有了new生成对象的方法，**new生成对象的过程**：

[手动首先js new方法](https://www.cnblogs.com/echolun/p/10903290.html)



在类中直接定义的方法，在生成每一个对象的时候，都会为对应的对象的方法申请一些空间，既然所有对象都用用一个方法，可以放在原型里，所以一般对象方法和所有对象公用的数据放在原型里。对象的__proto__和对应构造函数的prototype指向同一个对象———原型，一个属性会在当前对象中寻找，如果找不到会一层一层向原型对象中寻找，直到原型的终点——Null。**原型中定义的属性可以被关键字in检测到，但是不能被hasOwnProperty检测到，后者只能检测当前对象是否存在某个属性，那对象的hasOwnProperty和toString来自哪里？他们就是来自原型，在某一个对象里检测"hasOwnProperty"是否存在**：

```javascript
obj.hasOwnProperty("hasOwnProperty")
//false
"hasOwnProperty" in obj
//true
```

[你必须知道的指针基础-8.栈空间与堆空间 ](https://www.cnblogs.com/edisonchou/p/4669098.html)

## 三、模板字符串与标签函数

模板字符串用反引号包围，优点是允许插值且识别写代码时的空格和换行

eg: console.log(`Hi, I'm ${p.name}! Call me "${p.nn}".`);



标签函数的语法是函数名后面直接带一个模板字符串，并从模板字符串中的插值表达式中获取参数

标签函数中第一个参数是除了插值之外的所有字符组成的数组，后续函数是一个个插值参数，eg:

```js
`I'm ${name}. I'm ${age} years old.`
那么在调用函数的时候第一个参数就是["I'm ", ". I'm ", " years old."],它是一个个寻找是否有插值参数的，如果有则到此构成一个数组元素，在看一个
`${a} + ${b} = ${c}`
这个时候第一个就是插值参数，那么它前面就是空字符串"", 然后是 " + ", 然后又来到一个插值参数，然后是" = ", 然后是最后一个插值参数${c}，最后再继续寻找，没有了，所以最后还是空串，所以最后的结果是:
["", " + ", " = ", ""]
```

## 四、剩余操作符和扩展操作符

**剩余操作符：**用于函数的最后一个参数，将函数剩余参数打包成一个数组，剩余参数也可以解构，类似数组解构的语法：

[a, b, c] = arr, arr是一个数组，那么类似的：...[a, b, c]，这里只不过剩余操作符后面的是数组，并且直接对这个数组进行解构,一般也是在函数传参的时候使用：

func(...[a, b, c]) {

​	return a + b + c;

}

**扩展操作符：**用于将可迭代对象解开成一个个单个元素，常用于填充元素：

```javascript
var parts = ['shoulders', 'knees'];
var lyrics = ['head', ...parts, 'and', 'toes'];
console.log(lyrics)// ['head', 'shoulders, 'knees', 'and', 'toes']
```



## 五、this的作用域和apply，bind，call

在绝大多数下，如果一个函数不是通过对象直接调用的，不是通过new调用函数的，那么它内部的this就是window，有2种特殊情况，一个是箭头函数，一个是回调函数。

大多数情况下，不论函数在哪里定义的，如果它调用的时候没有通过对象则默认都是window调用的，如果是对象调用的，则会通过引用去寻找堆空间的对象内存，然后寻找到对应的方法，所以在进入对象的存储空间的时候就已经进入了对象的作用域内，这个时候this会指向当前对象；

对于箭头函数，它的作用域内没有this，所以它会向外层寻找this，这没什么好说的；

对于回调函数，因为是回调函数，不论这个函数是否是全局的，还是属于某个对象的，它都会放到全局执行栈中执行，所以回调函数中的this指向window；

所以识别this的指向，主要看这个代码在哪里执行，如果是在执行主栈中，那么就是window，如果是在对象对应的局部空间内，则是对象。

有的时候需要改变this的指向，有以下3种方法：

1. apply: func.apply(obj, 数组传参)，这里是2个参数，一个是obj，就是func的新的指向，第二个是数组，就是传递给func的实参，必须要是个数组，这里数组传参的时候会解构成一个个参数（参数列表），apply改变this指向是立即的，且此方法只是临时改变thi指向一次；
2. call: func.call(obj, 参数列表)，和apply相同的是改变this指向是立即的，且此方法只是临时改变thi指向一次，不同的是第二个参数，是一个个参数

apply和call的区别：

注意这里Math.max参数必须是参数列表

```javascript
var arr=[1,10,5,8,3];
console.log(Math.max.apply(null, arr)); //10
console.log(Math.max.call(null,arr[0],arr[1],arr[2],arr[3],arr[4])); //10
```

3. bind: bind方法和call很相似，第一参数也是this的指向，后面传入的也是一个参数列表(但是这个参数列表可以分多次传入，call则必须一次性传入所有参数)，但是它改变this指向后不会立即执行，而是返回一个永久改变this指向的函数

```javascript
var arr=[1,10,5,8,12];
var max=Math.max.bind(null,arr[0],arr[1],arr[2],arr[3])
console.log(max(arr[4])); //12，分两次传参
```

参考：https://zhuanlan.zhihu.com/p/82340026

## 六、类数组和arguments对象

类数组对象类似数组，拥有length属性，一个简单的类数组对象：

var arrLike = {  0: 'name',  1: 'age',  2: 'job',  length: 3 }

它对于的数组应该是这样：var arr = ['name', 'age', 'job'];

类数组对象与数组的性质相似，是因为类数组对象在**访问**、**赋值**、**获取长度**上的操作与数组是一致的；但是，类数组对象与数组的区别是**类数组对象不能直接使用数组的方法**；

那么我们希望类数组对象能够和数组一样使用数组的方法，应该怎么做呢？我们一般是通过 Function.call 或者 Function.apply 方法来间接调用：

```javascript
// 使用 call
Array.prototype.push.call(arrLike, 'hobby');
console.log(arrLike); // { '0': 'name', '1': 'age', '2': 'job', '3': 'hobby', length: 4 }
var arrLikeStr = Array.prototype.join.call(arrLike, '&')
console.log(arrLikeStr); // name&age&job&hobby

// 使用 apply
Array.prototype.push.apply(arrLike, ['hobby']);
console.log(arrLike); // { '0': 'name', '1': 'age', '2': 'job', '3': 'hobby', length: 4 }
var arrLikeStr = Array.prototype.join.apply(arrLike, ['&'])
console.log(arrLikeStr); // name&age&job&hobby

```



## 七、正则表达式

这个我在其他语言已经学过很多次了，这里只是熟悉一些es6的一些语法，

创建正则对象

1. new RegExp(正则, 匹配符号)，eg: var pattern = new RegExp("$http:", i) ，然后pattern.test(string),test函数返回true，说明匹配到了，否则没有匹配到
2. var x = /正则/匹配符号, 对应上面的 var pattern = /$http:/i，然后pattern.test(string)

与正则对象有关的属性和方法：

- test()：rge.test(str)，找到了则返回true；
- search(): 这个不是正则对象的方法，而是字符串的，所以它的语法格式：str.search(rge),如果找到了则返回第一个对应的索引，否则一直找，如果没找到返回-1；
- match(): 同search，找到第一个则返回对应的匹配子串，默认是贪婪的，如果想找全部的，需要在正则对象中添加g匹配符
- replace(): str.replace(字符串or正则，替代字符串)，默认是贪婪的，需要在正则对象中添加g匹配符
- split(): str.split(字符串或者正则)将根据直到字符把字符串切割成字符串数组

**Tips:**

^ ——如果字符集里，相当于非

$—以什么结尾

^——以什么开头





------

后序：Array.from, Object.freeze,create,apply,bind,对象属性的高级定义设置

Promise

Proxy

继承

高阶函数的使用

迭代器与生成器

模块化的历史与语法

深复制和浅赋值