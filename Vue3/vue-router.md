# Vue-Router

[TOC]



## 一、前端路由

windows对象包含url和history属性，通过这两个属性，可以实现对页面的跳转，在一般情况下，改变url一般会重新向server发送一次请求，为了避免这种情况发生，有2种方案：

- 通过window.location.hash改变url，不会重新向服务器发送请求，但是这样挺难看的，哈希锚定有个**#**标记

- history.pushState(data, title, url),不会发生刷新浏览器的前进和后退就相当于是栈的push和pop；

- 与上一个类似，history.go和history.back，back只能一个一个退，go可以一次性弹出多个。forward大同小异

  **Tips:** href = hyper reference

- history.replaceState(data, title, url),，但是不是压栈和出栈的操作，它没有前进和后退页面的权力

  

**Tips:** 在vue中，如果import x from 文件夹，那么会默认导入该文件夹下的index.js文件

**插件使用的基本语法：**

1. **导入插件包**：import
2. **安装包：**Vue.use(插件名)
3. **实例化该包**，比如路由，实例化一个路由（包括路由配置）这个配置主要就是url和组件的配置，然后通过url去找到对应的组件用于渲染网页，所以这个配置是url和组件的映射，一个映射关系就是一个对象
4. **导出该实例**
5. **在main.js里导入该实例**：import

## 二、路由的基本用法

### 路由的基本使用

路由在主组件里面的使用：<router-link to=>相当于超链接，用于改变url，但是并不会刷新页面，其底层用到的就是前面讲过的不刷新页面的那些函数（即渲染的时候，不刷新页面）

<router-view>用于显示待渲染的组件

### router-link常用的属性

1. to：指向的地址，和配置路由里的相同
2. tag：默认渲染成a标签，可以通过该属性改变
3. replace：默认是pushstate，如果写了该关键字，replace就表示是replace状态
4. active-class：当对应的路由匹配成功时，会自动给当前匹配成功的元素设置一个router-link-active的属性名，设置该属性可以直接修改该属性名，也可以在路由配置的时候，修改linkActiveClass属性也可以达到该效果同时批量化
5. children：用于嵌套路由，是个数组类型，其元素均是子路由配置字典



### 路由配置时一些参数

1. **redirect:** 默认显示的组件，即一开始加载页面的时候，其实是没有赋予url任何哈希值的，只有当你点击了定义了的哈希值（如:/home）才会自动跳转到这些哈希值对应的组件；而如果没有给path定义值的话，即在页面加载初，就显示页面的内容，但是这样有个缺点：加载的时候url并没有改变，所以希望在没有给path赋值的情况下，可以重定向，即页面加载初就直接重定向了。

简单来说给路由配置一个默认的页面：

> {
>
> ​	path: "",
>
> ​	redirect: 默认路径
>
> }

2. mode:默认是hash模式，像下面一样拼接url: /#/path, 可以改为history模式

**Tips:** hash值拼接#后面的内容几乎在任何浏览器当中都不会被当作url，所以即使它改变了也不会向服务器发送请求，但是history它实际上就是url，只不过对于push(go)和pop(back)这2种操作，他们在本地都有栈进行管理，所以通过这两种方式改变url也不会向服务器发送请求，但是唯有刷新操作却不是记录栈状态的操作，所以它会直接向服务器发送请求

### 通过代码跳转页面

一般的路由使用方法都是通过<router-link>和<router-view>搭配实现组件跳转；如果有以下需求：点击一个按钮即可通往一个页面，当然可以修改默认的tag属性实现，那能不能通过更朴素的方法实现？

给当前元素添加一个监听事件，在该事件中手动通过push or replace的方法达到跳转的效果

``` javascript
btnClick() {
    this.$router.push(url) 
    // this.$router.replace(url) 
}
```

三种跳转且不向服务器发送请求的方式：原始方法（配合js使用）、路由方法、组件对象的router属性方法

### 动态路由

在我理解，动态路由就是类似一个页面下有很多子页面（更广义地说，就是一个文件夹下面有很多文件），动态路由就类似于此，它的目的在于对不同的子页面用相同的组件渲染；考虑有一个用户群，里面有很多用户，对于每个用户的登陆主界面都是相同的；

> {
>
> ​	path: "/home/:id"
>
> ​	component: 具体的某个组件
>
> }
>
> 具体渲染的时候，to="/home/foo", to="/hone/fred",这个时候这2位用户fred和foo的渲染组件就是一样的，这里的id可以限制类型，也可以动态绑定

### 参数传递

> 1. :to="/path/id"  										  获取：$route.params.id
> 2. :to="{path:, query:{name:jmm,...}}"        获取：  $route.query.name

其实任意的传递都可以写成对象的形式： to = "{path:"}"

### 懒加载组件

随着业务的进行，开发的js文件可能会非常大，所以需要对打包文件进行分离，然后分离得话需要用到懒加载，有3种语法：

> const Home = () => import('../views/Home.vue')

### 嵌套路由

就是在路由对应的渲染组件中继续使用子路由，通过**children属性**建立子路由映射，唯一要注意得在进行渲染的时候，路由配置的<route-link>中的to属性和children的子元素配置的时候要写成绝对路径，因为rout-link很笨，如果写成相对的，它是直接去从第一级目录下寻找，这样肯定是找不到的，所以要写绝对路径。



### keep-alive

在该组件内（news）如果改变了当前页面的渲染效果，进入了其子页面，那么会在离开该页面的时候，去了其他页面（如这里的home，about），这个时候会保存离开该页面时候所有子组件及其状态。keep-alive标签下的标签都是具有actived和deactived钩子的，一般标签没有；

**Tips:** keep-alive, router-link都是vue内部定义好的内置标签

## 三、路由守卫(回调)

路由守卫（回调or勾子）和组件钩子的区别，组件钩子只要是组件就有，它是局部的，而路由对象是全局的，一般只有一个就是在index.js文件中导出的那个，所以它的钩子会对全局造成影响（主要是指push和replace对组件的$router造成影响）

**Tips:** 动态绑定的时候，内部的字符串用'，单引号，如 :to="'/user1/'+userid"



## 小练习

实现一个页面，有4个链接，首页，新闻，用户和关于，点击用户会出现用户1按钮和用户2按钮，点击这两个按钮会出现对应的用户信息；并且最后从这个页面离开后，返回该页面仍然保存离开时的状态。新闻页面也是如此点击新闻页面出现新闻的子组件，点击新闻的子组件即可出现新闻子组件内容

这里主要的点有：

1. 两种参数传递的方法
2. 如何通过keep-alive和全局守卫实现保存页面信息，返回该页面后，仍然是之前的状态
3. 嵌套路由
4. 动态属性的绑定
5. 代码实现页面转移

页面如下：



<img src="C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210615154703581.png" alt="image-20210615154703581" style="zoom:50%;" />

点击News按钮：

<img src="C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210615154753185.png" alt="点击News按钮" style="zoom:50%;" />

点击新闻子组件![点击新闻子组件](C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210615154909816.png)