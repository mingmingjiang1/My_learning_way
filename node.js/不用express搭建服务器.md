# Node.js day1

node.js主要是用来模拟服务器，可以编写与服务器交互的程序，当然ruby，python，java都可以

node.js有一个特性，大部分函数都是异步的，都有一个回调函数

### 一、读写文件

fs = require("fs");

fs.reafFile(filename, (err, data)=>) 第二个参数是读完文件后的回调函数

如果出错，那么err将是一个对象，data为null；否则err为null，data默认是二进制，如果想要处理或者显示，得先用toString

fs.writeFile(filename, data, (err, data)=>) 第三个参数是读完文件后的回调函数



### 二、简单的服务器

http= require("http");

sever = http.createSever()

sever.on("request", function(req, res)...) req是请求对象，包括请求的url，on是监听事件函数，这里是监听是否有客户端发送请求，res是响应对象，res有一些重要的方法

| response对象对应的方法 |                           功能描述                           |
| :--------------------: | :----------------------------------------------------------: |
|  response.setHeader()  |                          设置响应头                          |
|   response.end(data)   | 表示响应结束，不然浏览器不知道何时结束，end支持二进制或者字符串 |
|                        | response.setHeader("content-type", "text/html;charset=utf-8") |

同样的request对象也有对应的方法和属性，比如如果想看一下发送请求的客户端的ip或者端口，都是通过这个对象来访问的



sever.listen(port, "终端显示的文字提示")



### 三、node.js模块化

node.js每个文件都是一个模块，如果希望文件之间相互访问变量，需要导入和导出，node.js的导入方式是立即执行，即在某一行导入一个文件的时候，该文件的代码就执行了，导出的时候需要一个exports对象，如果没有任何导出的变量，则该变量为空，exports.x = y，表示将y变量导出

### 四、简单路由

可以通过获取请求的url为相应的url申请对应的请求，ip:port**/....**  url就是红色的部分，可以通过判断语句为特定的url返回特定的请求；

content-type:指定给浏览器告诉浏览器是什么类型，比如在服务器端想要发送的是汉字，由于服务器端是utf-8编码的字符串，而浏览器默认用gbk编码，所以这样会乱码，content-type用于告诉浏览器返回的数据是什么类型，比如如果返回页面，就是text/html，如果是图片就是/img/jpeg ...



### 五、Apache搭建

用node.js模拟Apache



## 六、模板引擎

模板引擎需要安装第三方依赖：npm install template

模板引擎语法:



**Tips:**服务器发送的(end)的数据是二进制的，如果发送的是html字符串，且内部已经指定了charset = "utf-8"，则就可以不用在content-type里指定，否则需要在content-type内指定；url会自动在前面加上/，如果路径是url格式的，则必须转换为路径格式的



## 七、客户端渲染和服务的渲染

浏览器收到html响应内容之后，就要从上到下依次解析，在解析的过程中，如果遇到link，img，script，iframe，video，audio等带有src或href属性的标签的时候，浏览器会自动对这些文件发送请求。为了方便统一处理这些静态资源，所以我们约定把所有的静态资源都存放在public目录中

Tips: 在服务端中，文件的路径不要写相对路径，因为请求的url来标识的，这样浏览器会不认识

因为一个页面通常有多个url需要请求，所以，每次有新资源，都会发送请求，有的时候会很慢，因此就有了服务端渲染

服务端渲染：直接把所有的请求数据在服务器端就已经在页面解析完毕，常见的用模板在服务端渲染html页面，这样就不需要在页面重新再发送请求了，服务端渲染有利于SEO优化，爬虫很难爬到

客户端渲染：为了用户体验，通常需要异步请求，客户端渲染一般审查元素的时候都看不到一部分数据，服务端渲染却可以看到

## 八、表单提交与重定向

1. 表单中需要提交的表单控件必须有name属性
2. 表单提交分为
   - 默认的提交行为
   - 表单异步提交



在组件上绑定属性=>props，如果没有给v-bind传入被绑定对象，这个时候绑定对象必须是对象，且其属性和props里面的属性一一对应

在组件上定义原生事件=>没办法加.native修饰符，但是可以分发组件事件，如果某个子元素需要该监听事件的行为则可以重写该事件监听函数，并通过v-on绑定该监听函数。

在组件上放置普通静态属性=>会继承给该组件的根元素

在组件上使用v-model=>默认是绑定给了props中的value变量，当然可以修改这一默认行为，v-model本质也是绑定语法，所有也就不难想象会把值自动绑定到props中。



前面的语法均适用于组件，对于slot又有一套规则，不过slot会被代替，所以没必要定于事件、属性什么的，唯一可能需要的就是在父级作用域替换插槽的时候，对于插槽中的数据需要访问，这个时候就涉及插槽之间的通信了。

同样地，插槽之间的数据访问，也是通过props，比如：

在定义插槽的时候<slot :values="titles">这就默认把values作为一个插槽prop，绑定的是内部的titles变量，当然如果有多个绑定的化，就会形成一个props对象，所以在父组件中访问的时候，是通过：

插槽对象.其中一个prop 来访问的，其中插槽对象可以随意命名，比如这里我想访问titles，slotprop.values即可

v-model其实本质是是事件和v-bind的结合体，一般只用于表单元素，表单元素，如果含有value属性，那么绑定的就是value属性的值，否则可能是布尔值，也可能是其他的原始变量类型



全局注册组件Vue.componnet(ID, {}),第二个是个对象，

也可以只要Vue.extend()对第二个对象做一层包装也是可以的，这个对象一般会至少含有2个属性，一个是data，一个是template，template可以放在外面定义。



jsonp在本地写好函数名，然后函数名作为值，再设置一个键，这样在script中通过src把带有这个键值对的参数传给服务器，服务器解析，并返回一段调用该函数的js代码，原因是src可以访问跨域的脚本并放在本地；这样实际上申请数据都是在本地js代码进行的，所以jsonp只能发送get请求，不能发送post请求（script引用的资源请求方式只能是get）



## 九、案例总结

```javascript
http.createServer(function(req, res) {
    var parseObj = url.parse(req.url, true);
    console.log(parseObj)
    var pathname = parseObj.pathname;
    if(pathname === "/") {
        fs.readFile("./views/index.html", function(err, data) {
            if(err) {
                return res.end("404 not found.")
            } else {
                var tmplstr = template.render(data.toString(), {
                    comments
                });
                res.end(tmplstr)//end方法既可以接受字符串也可以接受二进制
            }
        })
    } else if(pathname.indexOf("/public/") === 0) {
        fs.readFile("." + pathname, function(err, data) {
            if(err) {
                return res.end("404 not found.")
            } else {
                res.end(data)//既可以结束字符串也可以接受二进制
            }
        })
    } else if(pathname === "/post") {
        fs.readFile("./views/post.html", function(err, data) {
            if(err) {
                return res.end("404 not found.")
            } else {
                res.end(data)
            }
        })
    } else if(pathname === "/pinglun") {
        var comment = parseObj.query;
        comment.dataTime = "2017-5-12";
        comments.unshift(comment);
        //301永久重定向，302临时重定向
        res.statusCode = 302;
        res.setHeader("Location", "/")
        res.end();
    } else {
        fs.readFile("./views/404.html", function(err, data) {
            if(err) {
                return res.end("404 not found.")
            } else {
                res.end(data)
            }
        }) 
    }
}).listen(8000, "127.0.0.1")
```

这个服务器会对5种请求做处理：

1. 首页：对应的url是"/", 唯一要注意的就是这里用的是模板渲染，先从网页文件读取数据，渲染后的数据

2. 静态资源的申请：用户可以访问那些文件是由我们决定的，我们一般把用户可以访问或者我们希望用户访问到的资源放在一起(一个public文件夹)，然后对这些静态资源的申请都是直接写成/public...的格式，由服务器对这些申请这些静态资源的请求统一处理；

3. post请求没什么好说的，唯一要注意的就是<a>对应的src是url不是路径

4. 通过get方法接受用户的输入，同样地，表单元素的action对应的也是url，而不是路径，这样提交的时候触发的就是向这个url发送请求；浏览器对表单元素的提交会加上一些键值对，即当然这个url包含了query的键值对，所以我们需要对这个url做进一步处理。（浏览器默认所对表单元素的处理是把表单元素的name和值作为键值对放在url后面，后面也会提到用post请求）；这里还有一个重定向的知识，需要设置重定向的代码，并且重新设置头部（

   ```javascript
       res.statusCode = 302;
       res.setHeader("Location", "/")
   ```

5. 最后一个是用户输入了不在范围之内的请求则转错误处理