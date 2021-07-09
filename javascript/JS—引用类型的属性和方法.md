# JS查漏补缺二——JS引用类型的属性

## 一、Object

1. Object.assign(target, ...sources) 用于将所有可枚举属性的值从一个或多个源对象分配到目标对象。它将返回一个新的目标对象

2. Object.create(proto, [options]), 以proto为原型构造一个对象，并且赋予properties，options是关于定义性质的部分，这个定义和defineProperty第三个参数类似

3. Object.defineProperty(obj, prop_name, descriptor)，更改对象的某个属性以及其配置

   obj——要修改的对象，

   prop_name，属性名，字符串

   descriptor，相关属性的配置

   https://segmentfault.com/a/1190000007434923

   `Object.defineProperties`本质上定义了`obj` 对象上`props`的可枚举属性相对应的所有属性

4. Object.entries(obj), 返回由键值对构成的迭代器（实际是数组），可由for in 遍历：

   for (const [key, value] of Object.entries(object1))

5. Object.keys(), 返回由键构成的迭代器（实际是数组），对于数组其键就是索引。

6. Object.values(),返回由可枚举属性值组成的数组

7. Object.freeze(obj),可以**冻结**一个对象。一个被冻结的对象再也不能被修改；冻结了一个对象则不能向这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举性、可配置性、可写性，以及不能修改已有属性的值。此外，冻结一个对象后该对象的原型也不能被修改。这个方法也可以不赋值新的对象，就是在传入对象是原地修改

