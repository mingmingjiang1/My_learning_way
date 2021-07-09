### 6月4日手动配置webpack框架复习：

首先建立一个工作目录，在该目录下建立dist文件夹，该文件夹下放置打包后的文件，<u>*包括(经过url-loader或file-loader处理后)</u>图片*，然后在该工作区下建立src文件夹，主要放置各种源码和原始图片：css，js，vue，less，images，

然后在该工作区下初始化npm(npm 初始化包管理工具): npm init,然后会在该工作区下看到package.json文件，该文件是npm管理和下载的主要配置文件；这个时候在当前工作区并没有安装任何局部的包，所以不会有node_modules文件夹，该文件夹含有局部的（属于当前工作区）所有包

noting: package.json文件，每次下载或者卸载都会更新该包，或者直接npm install 也会根据该文件直接安装该文件所配置的该文件；刚开始初始化该工作区的时候，由于没有任何局部包，所有也不会有node_modules这个文件夹

在实际开发中，一般情况下是要安装局部的包的，所有在package.json中配置的命令，都会优先从node_modules寻找该包；回到主题，这里由于没有局部的webpack，所以需要安装局部：

npm install webpack@version --save-dev(仅仅开发时需要)

由于每次书写webpack 待打包文件 目标文件  文字太长很麻烦，而且这个命令不是从package加载的，所以默认时从全局运行的，这里需要配置package.json和另一个文件（webpackage.config.js),这个文件是要自己创建的，在工作区目录下，内部是一个导出对象，对象有以下几个比较重要的键值对：

entry:"带打包的文件"

output:{

​			path:目标文件的绝对路径,

​			filename:目标文件名

​			p<u>ublicPath:url_loader 或file_loader处理后的文件的前缀，一般是打包文件夹，因为一般希望打包后文件都是在打包文件夹下,其实这个不需要也可以，因为index.html本身就是放在该文夹</u>

}

module:各种loader

plugin:各种插件

devServer:开发的本地服务器

resolve:



配置好以上的entry，output和module部分基本上大部分文件就都可以运行了

注意main.js文件是集中的导入代码文件，即加载所有的模块。在webpack中，一切皆模块，一般除了vue，其他文件都用common.js的规范导入，这里注重说一下vue文件，vue是用ES6规范导入导出，且vue格式的文件有3部分，template，script和css，与以往写组件不同，这里不需要定义模板id，一个文件仅仅有一个模板和一个组件代码，所以这是一一定义的，vue的文件名首字母大写，文件名就是组件名，一旦导入了子组件，就必须在父组件中注册并使用。

看一下package.config.js文件吧：

```json
{
  "name": "webpack_review",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {//自定义命令
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {//--save-dev开发时依赖的包
    "css-loader": "^2.0.2",
    "file-loader": "^3.0.1",
    "less": "^4.1.1",
    "less-loader": "^4.1.0",
    "style-loader": "^2.0.0",
    "url-loader": "^1.1.2",
    "vue-loader": "^13.0.0",
    "vue-template-compiler": "^2.6.12",
    "webpack": "^3.6.0"
  },
  "dependencies": {//--save运行时依赖的包
    "vue": "^2.6.12"
  }
}
```







### CLI创建工作区的目录解析：

1.build文件夹：主要是webpack.config.js的配置文件的分离后的文件（与config的区别，这里主要是依赖）

2.config文件夹：主要是配置的变量

3.node_modules：局部的安装包，用npm进行管理

4.src文件夹：核心自己写代码的地方

5.static文件夹:放置静态文件，比如说之前的图片会自动转变为字符码（<limit）或者自动命名（>limit），这里的文件会原封不动的打包到dist文件夹

后面都是文件：

​			.babellrc：如果安装了preset-env，会有该配置文件,是对浏览器的兼容性的一个判断

​			.editorconfig：文件书写的一个限制，代码的规范约定

​			.eslintignore：对某些指定文件不作代码限制

​			.eslintrc.js：代码限制

​			.gitignore：git默认对所有仓库中的文件作版本跟踪的，以`.gitignore`文件定义哪些文件将被 Git 排							除跟踪, 被`.gitignore`匹配到的文件将不会显示在 Git 的

​			.postcssrc.js css做转化的配置文件

​			.index.html 模板文件

​			package.json

​			package-lock.json：package.json中书写的是大概的一个版本，这里是确切的一个版本

​			README.md：说明文档



插件的使用：在webpack.config.js中添加以下代码：

```js
const webpack = require("webpack")
const HtmlWebpackPlugin = require("html-webpack-plugin")
const uglifyJsPlugin = require("uglifyjs-webpack-plugin")
    plugins: [
        new webpack.BannerPlugin("我是蒋明明"), 
        new HtmlWebpackPlugin({template: "index.html"}),//模板文件
        new uglifyJsPlugin()
    ]
```

注意这里用插件自动打包并归档到dist文件夹下的文件index.html，在index.html中的script标签属性是这样的：

![image-20210605104257739](C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210605104257739.png)

src有一个前缀的原因是因为打包到dist目录下的所有文件，包括打包的图片都会加上这个前缀，因为这是前面的：publicPath：控制的。

这个配置会在打包后的bundle.js和经过插件处理的index.html中所有有关url的都会加上这个publicPath前缀。然而这个时候，index已经被打包到dist目录下了，就没必要加上这个前缀了，可以删除。

然后是本地服务器的搭建没什么好说的。



### webpack.config.js文件的分离：

安装合并需要的包：npm install webpack-merge --save-dev

分离原来的webpack.config.js文件，

加载合并需要的包：const webpackMerge = require("webpack-merge")

加载合并文件1：const baseconfig = require("./base.config")

合并：module.exports = webpackMerge.merge(baseconfig, prod里的配置)



### CLI:

安装指定版本的：npm install -g @vue/cli@3.2.1

#### runtime-template与runtime-only区别：

runtime-only更加高效，

实际vue运行是这样的

template->ast(abstract syntax tree)->render->VDom->DOM，而runtime-only没用template，它是不允许在main.js书写template相关代码的，只能写render相关代码；其余的普通组件文件是可以书写template代码的，当然runtime-only是不认识这些template标签的，是什么把组件里的template转化为render？是vue-template-compiler，通过该开发依赖可以把所有的template相关代码进行转化。所以最终运行时的代码是没用template的，所以就没必要安装runtime-template；

而runtime-template必须要书写template，它可以不需要vue-template-compiler，它只要文件有template就可以了，但是它的运行速度相对比较慢。

### 总结：

如果想用vue尾缀文件的话，可以用runtime-only，但是必须同时有vue-template-compiler

（vue-loader是为了加载vue文件，vue-template是为了解析vue文件，把它变为render函数）



想对于CLI2，CLI3中的文件结构更为简洁，只是把CLI2的很多文件都隐藏起来，如果想更改配置，有以下办法：

1. vue ui（启动GUI界面手动配置）
2. 如果自己想改变配置：那么可以创建一个vue.config.js文件这个文件会和原来的配置文件进行合并

### 1.在自定义组件上定义原生事件

子组件元素向父组件发送信息必须依靠自定义事件，这个时候组件上必须自定义事件，也就是说组件上没办法用原生事件。父组件上是办法直接写上事件的，因为他们是自定义标签，并不包含类似click，input事件，所以如果非要加的话，需要用个@click.native语法；这种语法在组件根元素是具备该事件的时候，是没错误的，因为该语法本质上是把组件上定义的原生事件传给了组件的根元素，但是倘若根元素没有该事件，就不好办了，这个时候需要一个this$listeners继续向子元素分发（转移）事件；

### 2.在自定义组件上使用v-model

之前在表单元素上使用v-model，当然也可以在自定义组件上使用该命令，但是默认的时使用value作为pro传递，默认是input事件。其实原本在表单中使用v-model的本意就是数据和视图的双向绑定；在这里也是但是必须通过给组件值改变才会引起内部的value改变，如果意图反向，则必须通过emit从子组件发射

一般组件上的v-model可以双向绑定；

但是自定义组件的v-model只能从父=>子，如果想反向，则必须使用发射$emit。



------

- [x] 以下内容以前重写记录过


### 前端路由

改变url一般会重新向server发送一次请求，为了避免这种情况发生，有2种方案：

1.改变哈希，location.hash  = ,不会重新向服务器发送请求

2.history.pushState(data, title, url),不会发生刷新

浏览器的前进和后退就相当于是栈的push和pop；

3.history.replaceState(data, title, url),跟上面类似，但是不是压栈和出栈的操作，他没有前进和后退的操作

4.history.go和history.back类似，back只能一个一个退，go可以一次性弹出多个。forward大同小异

href = hyper reference



如果import x from 文件夹，那么会默认导入该文件夹下的index.js文件



路由的基本语法：

1.导入路由

2.安装路由，Vue.use()

3.实例化一个路由（包括路由配置）这个配置主要就是url和组件的配置，然后通过url去找到对应的组件用于渲染网页，所以这个配置是url和组件的映射，一个映射关系就是一个对象

{

path:"/home", component:home,意味着只要出现/home，就对应这个组件

mode：:hash: or history

}

new Vuerouter(

)

4.导出路由

在其他的文件里挂载路由;



路由在主组件里面的使用：<router-link to>相当于超链接，用于改变url，但是并不会刷新页面，其底层用到的就是前面讲过的不刷新页面的那些函数（即渲染的时候，不刷新页面）

常用属性：to="target"  tag=""(默认渲染成a标签，可以通过该属性改变) replace默认是pushstate，可以来回返回，这样就不可以来回返回了）active-class（当对应的路由匹配成功时，会自动给当前匹配成功的元素设置一个router-link-active的属性名，设置该属性可以直接修改该属性名）也可以在实例化路由的时候，修改linkActiveClass属性，

​												<router-view>渲染路由组件的内容



**重定向：**

可以这样

path:"", component:  默认显示的组件，即一开始加载页面的时候，其实是没有赋予url任何哈希值的，只有当你点击了定义了的哈希值（如:/home）才会自动跳转到这些哈希值对应的组件；而如果没有给path定义值的话，即在页面加载初，就显示页面的内容，但是这样有个缺点：加载的时候url并没有改变，所以希望在没有给path赋值的情况下，可以重定向，即页面加载初就直接重定向了。

哈希值拼接（默认）：/#/path，可以通过mode改变



hash值拼接和history，

hash值拼接#后面的内容几乎在任何浏览器当中都不会被当作url，所以即使它改变了也不会向服务器发送请求，但是history它实际上就是url，只不过对于push(go)和pop(back)这2种操作，他们在本地都有栈进行管理，所以通过这两种方式改变url也不会向服务器发送请求，但是唯有刷新操作却不是记录栈状态的操作，所以它会直接向服务器发送请求





不通过路由标签跳转，而是通过代码跳转：this.router，this是vue组件实例，他有push，pop，replace，可以实现跳转



### Conclusion：

三种跳转且不向服务器发送请求的方式：

1.原始方法

2.路由方法

3.组件对象的router属性方法



动态路由：

一个页面的前面可能是确定的，但后面是不确定的，这个时候需要动态路由，表示不确定的文件名



this.$route（this.$route.params.属性）是当前活跃路由,this.$router是路由实例，包括整个所有的活跃路由

因为业务js文件可能会非常大，所以需要对打包文件进行分离，然后分离得话需要用到懒加载，有3种方式，

嵌套路由：就是在路由对应得组件中继续使用子路由，通过children属性建立子路由映射，唯一要注意得在进行渲染的时候，<route-link>中的to属性要写成绝对路径，因为rout-link很笨，如果写成相对的，它会直接去从第一级目录下寻找，这样是找不到的，所以要写绝对路径

其次是参数传递的2种方式（其实本质上原本的:to=“”这种传递方式可以写成对象的方式）

1. 传：：to="/path/id"  得：$route.params.id
2. 传：：to="{path:, query{name:jmm,...}}" 得：  $route.query.name

