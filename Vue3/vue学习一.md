```html
<html>
    <head>
        <script src="../js/vue.js"></script>
    </head>
    <body>
        <div id="app">
            <h1>{{message | upperCase('I','love','you')}}</h1>
            <child :color="color1" :sex="sex1" @define-event="parentEvent">
                //多个具名子组件的时候，每个具名子组件最好用template包裹，虽然这个template形式上看没什么用，并不是个语义元素
                <template v-slot:slot1="slotscope">
                    <h2 slot="slot1">
                        {{slotscope.values}}<!-- 如果没加v-slot，这里是用不了子组件内部的数据的 -->
                    </h2>
                </template>
                <template v-slot:slot2="slotscope">
                    <h1 slot="slot2">
                        {{slotscope.values}}
                    </h1>
                </template>
                <!-- </template> -->
            </child>
            {{printName1()}}//调用计算方法中的函数
            {{printName2}}//调用计算属性中的函数
        </div>
        
        
        子组件模板定义
        <template id="child">
            <div> 
                <slot name="slot1" :values="age"></slot>//具名插槽1
                <slot name="slot2" :values="name"></slot>//具名插槽2
                <h1>
                    {{name}}//子组件属性
                </h1>
                <h2>{{color}} {{sex}}</h2>//子组件属性
                <input v-model="store"></input>//动态绑定子组件的store属性
                <button @click="childbtn">点击</button>//子组件触发一个事件，用于回传给父组件
            </div>
        </template>
    
    
    </body>
    <script>
        //注册全局子组件
        Vue.component("child", {
            template: "#child",
            data() {
                return {
                    age:"27",
                    name:"ww",
                    store:""
                }
            },
            props: ["color", "sex"],//用于和父组件通信的数据，有2种方式，数组形式或者对象，推荐对象形式，注意这里数组里的虽然是字符串，但其实在内部被解释为变量
            methods: {
                childbtn() {//用于回传的自定义事件
                    this.$emit("define-event", this.store);//还有很多这样的带$的Vue属性，可以整理一下
                }//自定义事件
            }
        })

        const app = new Vue({//创建一个Vue实例
            el: "#app",
            data: {
                message: "jmm",
                name: "I'm, Jmm",
                color1:"yellow",
                sex1:"女"
            },
            methods: {//方法
                printName1() {
                    console.log("I'm methods " + this.name);
                    return this.name
                },
                parentEvent(value) {
                    console.log(value);
                }
            },
            computed: {//计算属性
                printName2() {//这里的这个计算属性是为了证明属性只在数据变化的时候才运行，当然前提是在html中或者script中调用了，在页面加载的时候script和html如果有函数的化，都会调用，但是后面如果数据改变了，只会调用html中再运行一次，在下面会有解释
                    console.log("I'm computed " + this.name);
                    return this.name;
                },
                test: {//这里的这个计算属性只是为了说明get和set
                    get() {
                        return this.name;
                    },
                    set(newv) {
                        this.name = newv;
                    }
                },
            },
            filters: {//过滤器
                upperCase(value, ...res) {//第一个参数是管道符接受的值，后面任意参数随意
                    tmp = "";
                    for(let i of res) {
                        tmp += i;
                    }
                    return value.toUpperCase() + tmp;
                }
            }
        });

        app.printName1();
        app.printName2;//这一句在页面加载的时候并不会执行，因为并没有数据改变
    </script>
</html>
```

这个案例只是为了回顾一下知识，所以随便写的东西，看下页面：

![image-20210601222802832](C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210601222802832.png)





#### 一、先来看下计算属性和方法：

打开该文件，可以看到console控制台：

![image-20210601221942017](C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210601221942017.png)

在代码里面我有4个调用printName的代码，但是只显示了3个，前2个html中调用的，最后一个实际是方法调用的，所以页面加载的时候，因为第一次计算属性已经调用一次了，而在第二次调用之前数据也没发生变化，所以是不会再次调用计算属性的，它实际也会给调用也是会有值的，只不过是从缓存中直接取；但是方法不一样，它和正常的函数一样，调用一侧，函数就重新运行一次。

然后我们看一下如果改变了printName中的数据呢？

![image-20210601222603147](C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210601222603147.png)

**对应的页面：**

![image-20210601223225150](C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210601223225150.png)



可以看到打印了2句话，这2句话是html中函数运行的，并不是script中的调用语句运行的，因为可以看到页面也发生了变化，因为vue是页面数据和js的vue实例的双向绑定，所以一旦有数据的变化，会即刻响应到页面

总结：

> 计算属性的话，他其实是由一个对象组成，其中有2个函数，一个是set函数，一个是get
>
> 函数，因为一般情况下，不建议修改属性，所以也就不会显式的写set函数，所以默认的话
>
> 直接写一个方法其实是对get的一个重写，
>
> 计算属性与方法的区别：如果数据不变化的话，他是不会调用函数的，直接从缓存中取数据，而方法的话，只要调用了方法，就会运行整个方法



#### 二、可以用三种方式定义html块部分

1. el

2. template:

3. render

其实render是最好的，他是最贴近编译器的，而el和template都是要经过这一部分的如果编译器没找到render，那么继续往后找，先看看有没有template，如果有，就直接用templeate部分，而不再看el的挂载点，这也就解释了为什么同时有el和template， el会替代template的原因



#### 三、有2个关于函数调用的细节

1. 如果是计算属性，那么可以直接当属性使用，而不必书写函数调用的方式；相反，如果是函数，

则必须调用，调用的话，如果是在插值语句中，则不用加引号，如果是在属性中或者是事件监听

则必须加引号；

2. 对于监听事件的函数，如果是默认事件，则有一个参数event，这个是默认存在的，如果函数没有

多余的参数，则不必显式的书写这个event，它是作为默认参数传递的，如果有其他的参数的话，则

需要书写该参数以及其他参数。



#### 四、父子组件通信的三种方式

1. props和自定义事件（双向绑定，常用于表单元素）

props是由父组件向子组件传递，用于将父组件data中的数据向子组件传递，这个时候父组件data的变化也会引起子组件中props的变化，Noting：props的变化仅由其父亲决定，不能绑定其他属性让其根据他属性的变化而变化；

自定义事件是由子组件向父组件传递，用于子组件出发了某个事件，引起数据变动，然后一层一层

向父亲传递，一直传递到根组件。

2. children和refs

个人认为用于需要在当前组件的计算属性或方法中访问父亲结点或者子节点的数据，比如：需要访问第一个子组件的name属性：this.$children[0].name，这样不方便，常用的是给每一个组件添加ref属性，然后根据this.$refs["name"]去访问

3. slot中的通信：（常用于slot展示数据，不需要动态的特性）

主要是跨插槽的作用域，比如在父组件中给插槽赋予一个特定的样式，而且需要用到子组件内的数据，这个时候直接在父组件中是访问不了子组件的数据的，需要用到slot跨域。

绑定数据：<slot :values = "titles"> 给当前一个叫什么的插槽（这里是默认插槽）建立一个props对象，slots上用bind绑定的值都是自动在插槽prop中，这里这个titles就被绑定到这个prop对象的values上，

<template v-slot:default="slotp">
    slotp.values.register

这里为指定插槽（名为default）插入以上代码，注意刚才是没有给prop命名的，这里随便起一个叫slotp对象，然后名字有了就可以获得该对象的属性了

#### 五、基本指令

1. v-bind: 简写(:), 用于绑定属性

2. v-model：用于表单元素，相当于v-bind和v-on的合并，一旦出发input事件，就会引起v-model

绑定的属性的动态变化，随着表单元素的value动态变化

3. v-on没什么好说的，简写(@)

4. v-if没什么好说的，就是根据布尔值去决定元素是否展现，要区分v-show,一个是

压根不创建元素，一个是创建了元素但是通过display

5. v-for没什么好说的

6. v-slot这个用于跨作用域 但是这个属性只能用在component或template上，如果在当前组件中，只有一个插槽的话，那么可以直接在组件元素上使用v-slot，如果当前组件由多个slot的话，为了区分不同的插槽，需要给每一个插槽套一个v-slot，语法v-slot:插槽名 = 绑定对象的名字



#### 六、过滤器

​	过滤器也是特殊方法的一种，现在很少用了，和方法，计算属性一样，可以全局定义（所有的Vue实例均可以	使用），局部定义（只有在内部定义该过滤器的实例可以使用），过滤器的第一个参数默认是管道操作符传	来的参数，其余参数可以自己定义



#### 七、模块化

  早期的模块化，可以自定义对象，把想要定义的变量作为属性，通过闭包，给名称相同的变量套上了

  各自的作用域，这样就避免了名称相同的问题，但是这样内部定义的变量只在函数内起作用，后来可以通过

  导出一个对象，这个对象中包含了想要导出的变量



  有4种模块规范：

  common js, amd, cmd, ES6 (commonjs和es6常用)



  common js:适用于服务器端，即用即导入：

 

```javascript
导出：
module.exports = {
	a : 6,

	b : 7,

  }



  导入：

  var a, b = requires("") or

  var a, b

  module = requires("");

  a = module.a

  b = module.b
```



  es6:

  <script>标签默认情况下js文件公用一个命名空间，使用了属性type="module"后，每个文件都是单独的命名空间，相互独立，那么这个时候如何实现导入导出呢?

export和import可以实现命名空间的互相访问。



  es6导入导出的方式：

  导出导出方式一：

 

```javascript
export {要导出的变量}，这些变量已定义

import {要导入的变量} from 文件名

eg:

"a.js"

var x = 6, y = 7;

export {x, y}



"b.js"

import {x, y} from "a.js";

导出导出方式二：

定义变量的时候导出：

"a.js"

export var x = 6;

export var y = 7'

export class Person {

	constructor() {
	}

};



"b.js"

import {x, y, Person} from "a.js"
```

  

  导入导出方式三：

有的时候想要自己命名导出的变量，可以用default，但是一个文件只允许有一个default，而这个声明为default的变量可以自定义名称：

 

```javascript
   "a.js"

export var x = 6;

export var y = 7;

export default var z = 10;



"b.js"

import {x, y, myname} from "a.js"

(myname就是给z起的别名)



导入导出方式四：把所有导出的文件打包成一个对象

import * as 对象 from 文件名

对象.想要获取的变量
```



 注：以上除了第四种，前面三种应该都是不允许修改导出的变量的，至于第四种,因为是作为对象的属性的访问的，不知道可不可以修改，按理说是不建议修改从其他文件导出的变量的



#### 八、Webpack

  为什么需要webpack ？webpack有什么用？

  gulp/rollup/webpack都是打包工具

  ，模块化有4大规范，目前浏览器支持其中的es6，webpack会把其中的不支持模块化的语句转化成浏览器支持的规范（es6），以及处理模块间的依赖关系；

  不仅js文件，css，less, 图片，json在webpack中都可以被当作模块使用。

  Webpack中一切皆模块

  从这一方面讲，webpack是一种把所有文件打包并整理成一个大部分浏览器都支持的规范文件

  webpack本身依赖node（webpack是用node写的，所以需要node解释器）

  综上所述：webpack有以下几个主要功能：

1. 模块化

2. 规范化

3. 打包

  以上3个都需要自己对应的处理程序（或者说规范）

  模块化对应把所有文件都看出模块，可是文件，css，less等本身并不是模块，如何整理成模块呢？

  答：模块化，需要将以上文件打包成模块，对应的有css loader，less loader，url loader（如果url对应的文件过大则还需要file loader）

  规范化：主要是由于部分浏览器不支持es6，需要转化语法，主要是bable loader

  打包：主要就是把以上都标准化后的文件打包成一个文件，在html文件导入打包后的文件，一般只需要一个命令：webpack 原来文件 打包后的文件

  以上的打包命令直接使用了webpack，有很多问题，其中最重要的就是，这里的webpack使用的是全局的webpack命令，而实际很多现实情况是我们需要针对不同的

  项目使用不同的版本的webpack，类似与anconda管理不同版本python一样。所以我们应该为不同的项目使用不同的webpack

  为了更加智能的使用webpack命令，需要配置webpack.config.js

```
  node -v 查看版本

  npm install webpack@3.6.0 -g 全局安装3.6.0版本的webpack

  webpack --version 查看版本
```



#### 九、Webpack管理的文件推荐结构：

webpack管理的文件结构：

```
  项目根文件

​        —dist(装有打包文件的文件夹)

​        		—bundle1.js

​        		—bundle2.js

​        —node_modules(各种局部的api，一般webpack.json.js会优先在这里寻找对应的api，如果没有才去全局找)

​        		—vue.js

​        		—...

​      —src(源码，也是项目的核心代码)

​        	—css(装css文件的文件夹)

​        	—js(装js和vue文件的文件夹)

​			—vue(装vue文件的文件夹)

​        	—images(装图片的文件夹)

​          		—upload(经常更换的图片)

​          		—images(不经常更换的图片)

​        	—main.js

​      —index.html

​      —package-lock.json(这个是安装node_modules后自动生成的文件)

​      —package.json(关于npm的各种配置的文件，比如npm.run)

​      —webpack.config.js(关于webpack的各种配置的文件，比如loader)

```

  接下来讲一下package.json和webpack.config.js这2个重要文件

  npm init建立package.json文件，这里面有需要的依赖，这里面一般放当前项目才需要的依赖包

  这里面还有建立命令之间的映射：

  build:"对应的webpack命令"（注：这里是用的webpack是局部的，除非局部找不到，才去全局找，而cmd中直接使用webpack时是全局的webpack命令，当然也可以不用npm run build调用，也可以在当前工作根目录用./node_modules中寻找webpack，这就是局部的webpack命令）



  npm install 包名

  常用参数：

  开发时依赖 --save-dev(打包完就没用了)

  运行时依赖 --save运行该程序也需要该包





  逻辑顺序：在package.json中预先准备好build命令，然后执行npm run build，他去webpack.config.js寻找导入导出文件（entry和output），

  所以没有这个文件会报错：A configuration file could be named 'webpack.config.js' in the current directory。



  前面讲到了需要各种loader，这些loader需要去官网下载，然后在webpack.json.js中配置

1. css
2. less
3. 图片:
4. es6=>es5:babel-loader

​		在配置的时候有一个limit参数，如果图片的像素小于limit则默认使用url-loader,否则自动使用    file-		loader，在使用了file-loader后会有一个问题，webpack会自动生成一个自动命名的图片文件在打包文件		夹下,但是实际上html是找不到这个文件的，因为html是根据images标签的url属性，除非你手动修改该		属性		到该文件，但是这样每次打包都要修改太麻烦了，所以有一个options参数选项，如下：

 		{

​    		name:打包的文件名 如“images/[name].[hash.8].[ext]”

  		}

  	这里就设置了通过file-loader生成文件的路径和文件名，表示images目录下的和变化前同名的8个哈希字		符的原扩展名的新图片文件，这个哈希是为了防止文件重名







​	**5. vue配置：**

  	vue也是通过npm管理的一个包，相应的也可以设置局部和全局的，因为现在有的文件需要vue，所以需要	下载，一般下载局部的，也就是在node_modules中，有以下三种下载方式：

-   	下载简单的vue.js，然后用script引入

-   	cdn

-   	npm（常用）


用npm下载的vue，之后在使用import Vue from "vue" ，这句话会从node_modules里面找，npm下载	的包都在这个文件里面

这里再提一下vue的一个使用方案：下面一步步升级！！！

首先：在一个文件中，使用vue，肯定需要导入Vue，然后第一次直接使用最普通的方式，声明Vue实例，然后什么数据啊，方法啊都在该实例中定义，即模板，没有和js代码分离

然后我们优化一下：把template部分单独抽出来，可以仍然把抽出来的template放在同一个文件中，也可以单独放在另一个js文件中

最后：来到我们的终级方案，其实webpack已经为我们建立了一种叫vue格式的文件，相对于普通的文件，它的优点在于template不需要定义id属性，该文件只允许定义一个模板

下面的script默认使用该template，每一个文件都是单独的一个组件，该文件的文件名就是组件名

​    

**关于package.json的配置：**

一般通过npm install 安装的包都会在该文件中显示，所以每次安装或更新文件，这个文件也会相应的更新或者手动修改了该文件，那么再次运行npm init也会根据更新后的packkage.json去安装包。





**书上combox代码解析：**

根元素div本身没有input事件，所以input没什么用，所以只能用$this.listeners转发input事件，转发给适当的有input事件的元素，当子组件里的元素有input事件触发的时候，是会调用回调函数的，而这个回调函数会发送事件，从而修改selectedVal的值（这正响应了v-model的本质理念，修改视图的同时改变数据；只是对于组件来说，没办法直接修改视图，只能对其子元素修改，那怎么改变数据呢？数据是通过$emit进行向父级转发从而达到修改组件的目的）这个例子子元素的value也是动态绑定的，和父元素的selectedVal始终相同。







