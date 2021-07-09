**启动和关闭mongdb**

mongod命令默认用当前mongod命令所处的根目录下的data目录作为存储数据库的目录，如果没有该目录会启动失败

启动数据库服务：mongod

连接数据库：mongo，默认连接本地数据库

断开数据库：exit



mongo的分布规则，其实和sql差不多，都是一个大的data文件夹，下面分为各种数据库，然后数据库下面又有各种数据表（在mongoDB中叫collections，每个collection是数组），每个数据表下面又有各种记录（在mongoDB中叫文档，必须是对象），比如：

```shell
qq:{
	users:[
		{name: "jmm"},
		{name:"wangwei"},
		...
		{name:"wangli"}
	],
	shopping:[
		{age:23, sex:"男",passwd:"12345"},
		{age:22, sex:"男",passwd:"1345"},
		...
	],
	...
	money:[
	
	]
},
taobao:{

},
...
tianmao:{

}
```



**MongoDB优点**：

1. 方便操作，而且是动态的创建，比如一张数据表或者数据库只有在真正有了数据之后才会创建
2. 数据对象不用那么规则，就是简单的对象，没有那么多的像sql设计表的约束

缺点：

非关系型数据库，不能表关联



**关系型**：表之间是存在关系的，如Mysql，Oracle，所以的关系型数据库都需要sql语句，而且操作之前，要设计表规则，并且单个表内是有约束的，比如主键约束

**非关系**：非常灵活，约束不是很强，但是为了数据的清洁，最好也要设计规则



```javascript
1.引入mongoose包,并连接至数据库
var mongoose = require("mongoose");

2.连接数据库
mongoose.connect("对应ip的一个数据库", {useMongoClient:true}),如果没有该数据库会自行创建


3.建立模型构造函数,第一个参数是数据库名字，在内部会自动转换成小写复数，第二个是表的规则
var User = mongoose.model(数据库名字, obj)

4.利用这个模型进行增删查改，后面的增删查改全是依靠这个模型
增：
var admin = new User({
    username:"jmm",
    passwd:"123456",
    age:22
})

admin就是一个集合

5.保存以上修改

admin.save(function(err) {
    if(err) {
console.log(err)
    } else {
        console.log("success")
    }
})
```

**查询：**

User.find(callback(err, res))查询所有

按照条件查询：

User.find(obj,callback) obj就是条件

User.findOne(obj,callback)只查找到第一个符合条件的

**删除：**

User.remove(obj,callback)按条件删除有多少个就删除多少个

User.findByIdAndRemove()

User.findFindAndDelete()



**更新：**

User.findByIdAndUpdate(id,obj,callback)

第一个是要更新的id，第二个参数是条件，第三个是回调







在使用mogoose的时候，需要注意一些，首先，mongoose会在每条数据插入的时候自动生成id，这个id是一段很复杂的16进制数字，后面自己在访问的时候，是通过id属性来访问的（老版本是通过_id，但是该属性拿到的是字符串，所以后面在服务器端编程的时候，需要手动去除这个字符串自带的引号），新版本没有任何问题。





Promise优点：

- 为了保证异步的结果有序，或者一步步拿到异步的结果，需要嵌套异步操作，但是嵌套的话，代码会变得特别难看，俗称”回调地狱“，为了解决回调地狱的问题，有了Promise，

- 通过then方法，是链式操作，在保证上一步的promise有了结果之后，再去执行后面的promise

- promise本身在实例化的时候是同步的，但是其内部一般会放异步任务，而异步任务都有回调函数，回调函数有两种返回值，即要么成功，要么失败,

  ```javascript
  new Promise((resolve, reject) => {
  	异步任务，如：
  	readFile(filename, function (err, res) {
  		if(err) {
  			throw(err)
  		} else {
  			return resolve(res)
  		}
  	})
  })
  ```

  

mongoose案例：

根据像服务器的请求来渲染当前页面，比如：当前页面会根据页面发送的请求，得到不同的数据，再把利用这个数据渲染页面；

具体来说有一个表单页面，这个页面里的表单元素默认时根据我当前发送的请求来做渲染的，比如我现在发送一个想查看id为4号的学生的信息，get(..../4)，然后发送该url，拿到数据，拿到数据后直接利用模板引擎进行渲染，把渲染好的数据，直接append到body中，或者如果时服务端渲染的话，通过send发送；