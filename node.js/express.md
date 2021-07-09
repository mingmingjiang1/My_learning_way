# express

## 一、nodemon的安装与使用

直接npm install nodemon --save 然后每次保存服务器文件都会自动重启，而不必手动重启

express是一个框架，更好的进行服务端开发，它相当于是http的一个扩展

## 二、express的基本使用

基本语法

``` javascript
var express = require("express");
vat sever = express();//建立sever对象

//创建路由：
sever.get(url, function(req, res))//get请求对应的回调函数
sever.post(url, function(req, res))//post请求对应的回调函数

//自己配置静态文件访问资源
sever.use(允许访问的url, 该对应的路径)，比如：
server.use("/public/", express.static("./public"))//如果url以/public/开头，则相应的去当前public路径下去寻找
另一种写法：
server.use(express.static("./public"))//省略第一个参数，则url中可以直接写这个文件夹下的资源

//配置模板
sever.engine(arg1, arg2)
arg1是后缀名，表示以这个后缀的文件才使用模板引擎渲染，args2是express对应的模板引擎包

response.render("文件名"，{})
第一个参数默认是views下的文件，即默认渲染views下面的文件，如果不存在则报错，{}渲染数据对象

如果想要修改默认的views文件夹，可以：
sever.set("views", 新路径)
```

**Tips:**res.send和res.redirct会自动结束响应，不需要主动end；一个网页中的内容可以通过url来获取，但是必须资源必须在项目文件夹下，并且需要对这个url进行配置；或者使用域名，这样就可以直接前往这个域名获取



express如何用post请求：

在express没有内置获取表单请求体的API，必须使用第三方包body-parser, 然后配置一下，只有加如这个配置，req就会多一个属性req.body表示请求体.

**Tips:**回调是为了处理异步操作后的代码，具体来说，无法在主线程直接获得异步操作内的数据，而异步操作就是为了获得异步操作的数据，所以回调函数一般都会放在异步执行代码的后面；其实也可以不用回调，直接在异步操作内部拿到数据后直接写相关操作代码，但是这样达不到封装的效果，如果回调函数的话，每次只需要修改callback这个外部函数即可，而不用修改主调用函数

## 三、crud实战

1. 路由设计

| method                  | url              | get参数 | post参数 | 说明                     | 结束后是否redirect    |
| ----------------------- | ---------------- | ------- | -------- | ------------------------ | --------------------- |
| get("/students", )      | /students        | 是      | 无       | 为了获取主页面           | 否                    |
| get("/students/new")    | /students/new    | 是      | 无       | 为了获取添加学生的页面   | 否，直接模板渲染      |
| post("/students/new")   | /students/new    | 无      | 是       | 为了提交添加新学生的信息 | 是，redirect to主页面 |
| get("/students/edit")   | /students/edit   | 是      | 无       | 为了获取编辑学生的页面   | 否                    |
| post("/students/edit")  | /students/edit   | 无      | 是       | 为了提交编辑学生的信息   | 是，redirect to主页面 |
| get("/students/delete") | /students/delete | 是      | 无       | 为了获得删除后的新页面   | 是，redirect to主页面 |





2. 路由模块的提取

   单独把各个路由单独放在一个文件内，调用router.get，router.post等等可以进行相应的路由配置；配置完之后，在另一个文件中挂载路由：sever.use(router)



**模块化历史：**

由于原生js没有模块化，所以后来民间各自衍生出了各种模块化，首先是common js，在node中使用，常用于服务器，在服务器端，文件存放在服务器本地，读取比较快这没什么问题，它是同步加载。但是在浏览器端，这种同步加载就有问题了，想要加载服务器的文件，可能会很慢，于是在浏览器端就衍生了浏览器的2大第三方异步加载包，一个是AMD(requires.js)，特点是一次性加载，另一个是CMD(sea.js),特点是懒加载，即用即加载。后来官方终于在es6提出了一个标准，es6加载，它的特点是动态加载，但很多浏览器都不支持这个它，目前常用的是在vue中，所以为了在浏览器中使用es6语法，通常会对这些东西进行打包把es6语法转换为es5。



**Tips:** package-lock.json作用

- 保存了node_modules中所有的依赖及其下载路径和固定版本号，这样下次下载npm install 更快
- 因为固定了版本号，所以下载会按照指定版本下载，如果没有该文件，那么在package.json会看到^version,那么会下载大于等于该版本的最新版本



