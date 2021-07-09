# axios

jsonp（实现跨域的一种方式）

ajax，jquery（为了使用其中的一个ajax而去加载整个jquery库得不偿失），

## 一、axios的基本使用：

```javascript
import axios from 'axios'
与其他插件不同，axios不需要实例化，它是需要调用几个axios全局方法即可,axios的一个优点是它直接返回promise，所以很容易实现异步操作
axios(url, method, config).then(res=>).catch(err=>)

如果是多个请求，希望2个请求都执行完之后再执行后面的：
axios.all([axios(), axios()]).them(results=>)
                                   
axios.all([axios(), axios()]).then(axios.spread((res1, res2) = >))
axios.spread相当于把results给展开了                                    
```

## 二、axios的全局配置

事实上，开发中很多参数都是固定的，这个时候可以利用axios的全局配置

axios.defaults.baseURL = 

axios.defaults.headers.post["content-type"]

axios.defaults.timeout = 设置超时时间

get需要配置params

post需要配置data

## 三、axios模块化

在第二点里的配置是全局axios实例，所以配置的设置是全局公用的，比如baseURL，timeout，实际上可能在一个项目需要多个不同的配置，就有了axios的单个对象：

var instance1 = axios.create(config),这里的config是全局配置的一些信息，baseURL，timeout

instance1.({

​	某一个单独请求的相关配置

})

同样地，var instance2 = axios.create(config)，

其实现在的instance就是等价的全局axios对象，所以axios()返回的是promise，那么instance()返回的也是promise



对一个文件的依赖不要过分，比如说axios不要在某多个文件内都导入，只需要在一个文件内，做相关配置即可；

## 四、拦截器

axios有请求拦截器和响应拦截器：

请求拦截器：axios.interceptors.request.use(config=>{一定要返回config,不然没办法发送给服务器})

在请求阶段要做的事可以有：

- 比如config中的一些信息不符合服务器要求
- 比如每次发送网络请求，希望在界面中显示一个请求的图标
- 比如某些网络请求（比如登录），都必须要求请求中含有token

响应拦截器：axios.interceptors.reponse.use(res=>{一定要返回res,不然没东西返回给浏览器})



移除拦截器:类似定时器，在创建拦截器的时候，赋值给一个变量，然后对该变量使用axios.interceptors.reponse.eject(myinterceptor) or axios.interceptors.request.eject(myinterceptor)