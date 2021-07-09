## 一、关于Vue的组织

Vue本身是一个全局对象，它的原型上有很多属性和方法，如：

Vue.prototype.$el,用于获取组件的根元素，

Vue.prototype.$mount()，用于绑定元素

Vue.prototype.$emit()，子组件发射事件

Vue.prototype.$off()

Vue.prototype.$on()

Vue.prototype.$once()

Vue.prototype.$自定义属性，如Vue.prototype.$bus

Vue本身也有一些静态方法，如：

Vue.component()，注册组件，其实就是实例化一个组件对象

Vue.directive(),注册自定义指令，其实就是实例化一个指定对象

Vue.extend()，生成一个Vue子类

以上的这些都是全局的，意味着在任何一个地方都可以调用，任何一个根组件可以调用，任何一个子组件也可以调用

比如，当我们自定义插件的时候，只要导入了这些插件，就可以在任何项目中使用了；

另一个概念是Vue对象自己的属性，只属于自己，比如vue-router,vuex,这些东西在实例化一个根组件的时候都会传入根组件，就单单对于这两个组件来说，是2个对象，而所有子组件都是共享这两个对象的

这2个对象如何访问：

- 对于router

  ```javascript
  this.$router
  表示注册时的router页面，可以通过该属性的一个path属性进行路由的跳转，这个path相当于是一个栈
  
  与其相似的一个对象是this.$route，表示当前激活的路由(view)，所以可以通过这个属性获得当前页面的信息如动态路由传过来动态信息$params，url传过来的query，或者说当前url的path；
  ```

  

- 对于vuex

  ```javascript
  this.$store
  这个就是store对象，可以通过该对象获取在store中定义的actions，state，mutations等，如
  this.$store.state访问state属性
  ```

  

1. 自定义指令：

2. 自定义插件：

   因为如果我们在一个项目中只定义一个根组件的话，可能有些组件不能作为其子元素存在，比如模态框，模态框是一个根window窗口定位的一个组件，所以其不应该作为app的子组件。所以就衍生出了另外定义一个Vue对象，其本质是自定义个对象，通过这个方法，我们也可以封装属于自己的插件

   ```javascript
   1.声明属于自己的类：
   var myVue = Vue.extend(obj),obj是一个组件，这里原始的组件可以定义一些默认值
   2.实例化组件对象
   var myvue = new myVue();
   这里可以传过去一些属性，覆盖默认值
   3.既然想作为另一个根元素，必须要载在一个元素上，因为挂载是替代，所以不能挂载在html或body上
   myvue.$mount("div")
   4.因为这是个插件，希望每个vue对象都可以使用，所以要操作原型
   Vue.prototype.$myvue = myvue;
   当然如果你的项目中只有一个vue根对象，那么在实例化那唯一一个vue根对象的时候传入也可以
   
   
   这样的话，页面的html结构就会大致如下：
   <div id="app"/>
   <div id="myvue"/>
   是上下并列的两个组件
   
   
   如果希望像使用插件一样使用以上的组件，需要单独定义一个文件，并且该文件有一个对象，对象有一个install函数，把上面的代码都放在install属性中，当使用Vue.use()的时候，就是自动调用install函数
   ```

   

## 二、better-scroll的使用









## 三、vuex

vuex一般用于全局状态管理，就是有一些变量和方法需要在各个组件中都要调用的，这个时候就需要对它们使用vuex了

在vuex中：

- state：components 中的变量交由state管理（即components->state）其实一般在项目中不会单独在其中定义变量，而是由module进行分类管理，在使用的时候，使用module上下文不同的类别state进行访问；
- mutations：用于操作state中管理的变量，一般不建议mutations中操作过于复杂的功能，而是把一个复杂的功能拆分成小的一个个mutation，然后交由actions管理（mutations->actions），官网的图也是mutations交由actions一并提交
- getters：用于获取state中管理的变量，不能设置state里的变量
- actions: 正如前面所说一般是对mutations的集中管理提交，由它直接控制state (actions->state),当然如果是异步操作，则必须在actions中
- module：用于对state的分类管理，可以自己设置命名空间，生成各自的上下问，最后会顶替state，

由上面我们所画的箭头，汇总起来就是官网的图：

> #### components->state->mutations->actions->state

举一个例子吧：

比如有一个管理系统，其中有一些数据和与这些数据相关的方法我们想把它们托付给vuex管理，假设数据大概如下：

> students:{
>
> ​	grade:{
>
> ​		....
>
> ​	},
>
> ​	credit: [
>
> ​		{id:1, credit:10},{id:2, credit:100}
>
> ​	]
>
> ​	info:[
>
> ​		{name:"jmm"}, {name:"wangwei"}
>
> ​	]
>
> }

可以看到上面有3类数据，如果这3类数据毫不相干的话，放在一个state里确实不好管理，所以就有了如下module:

```
modules: {
	"user/grade":storeGrade,
	"user/credit":storeCredit,
	"user/info":storeInfo,
	"imgs":storeImgs,
}

其中是user是前缀，前缀可以加，也可不加，看自己管理，如果大分类下面还是有很多小分类，就可以用前缀，一般不建议超过2层modules，还有这些子store必须注明namespaced:true，这个namespace可以理解为就是一个上下文

然后具体用某个store的时候，需要指定上下文，默认下我们访问根state是不需要上下文的，this.$store即可，但是需要访问子state的时候，如果子store还带有namespace则必须要有上下文：
this.$store.state["imgs"]
this.$store.state["user/info"]
另外在指定别名的时候，也需要指定上下文：
mapState("imgs", {})
maoState("user/grader", {})
```



既然vuex本身就是为了很多组件都要用，所以state, getters，mutations, actions需要大量使用，但实际上只有state, getters， actions它们三经常使用，因为mutations交由actions管理了，回到主题，如果使用的话，会有很长的前缀，所以可以指定别名，

mapState(["前后名字相同"]),mapState({newname:oldname})

其他类似，如果是对于子store，需要书写第一个参数，改变上下文，默认第一个参数为空，表示上下文是根store



## 四、导航守卫

- 全局守卫：

  全局定义，router可访问，在任何有路由跳转的地方都会调用相关的钩子函数，如果书写了的话

  - beforeEach: 也就是路由还没跳转提前告知，以免跳转了再通知就为时已晚, 作用主要是用于登录验证
  - beforeResolve: 这个钩子和beforeEach类似，也是路由跳转前触发
  - afterEach: 它是在路由跳转完成后触发

- 路由独享守卫：

  定义单个路由时定义，仅仅在使用该路由组件相关跳转时，才会调用相关的钩子函数，如果书写了的话

- 组件内守卫：

  在定义组件时定义, 类似于组件内的生命周期，相当于为配置路由的组件添加的生命周期钩子函数，仅该路由声明周期间可使用，相当于是特殊的声明周期函数

  



**三类守卫的用处：**

- 全局：适用于监听所有页面都可能触发一个函数，比如所有页面在跳转后都更新页面标题，因为单页面默认无法更改标题


- 路由独享：仅仅与该页面相关跳转的时候，才响应该事件，比如一个网站有一个页面有加密信息，所以转向该页面的时候，会发送提示信息


- 组件内守卫：适用于在该组件内的使用，**尤其适用于局部渲染的组件**，比如一个页面有一个组件，组件内有一个搜索框，在每次搜索内容的时候，都会请求不同的数据，从而渲染同一个组件(局部页面), 这个时候在跳转局部路由的时候，就可以请求数据了。但是这个，最好别用路由独享守卫，因为可能无法访问属于vue对象或者单独某个组件的变量，别忘了，路由独享守卫是在路由配置文件中定义的；更不能用全局守卫，因为不可能每个组件都需要申请数据吧


## 五、Vue文件组织

对于组件，要么是根组件就是App文件，要么是主组件，要么是子组件，建议主组件放在views里，每个view是一个主组件文件和子组件文件夹，如果一个页面组件有很多子组件，那么其子组件应该放在同一个文件夹下，且命名必须以该页面组件为前缀，文件名最好都是驼峰命名法

components：一般是通用组件和常用组件

store：index.js，也包含其他的抽离的mudules文件，state，actions文件

router：路由配置文件，一般只有一个index.js文件

common: utils，公用变量，mixins



## 六、表单请求体提交

之前我们看到的都是通过get方式申请请求





## 七、render函数





## 八、vue-router的使用

1. 适用于切换页面（通过to），如下通过选项卡切换整个页面，且没有数据渲染，一般是静态路由

<img src="C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210705101023962.png" alt="image-20210705101023962" style="zoom:67%;" />



1. 适用于同一页面一个组件复用（局部渲染）（常见于通过参数传递，申请不同的数据，但是这些数据是用同一个组件渲染的），如下点击不同的按钮，使用同一个组件渲染，一般配合路由的动态传参

<img src="C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210705101241580.png" alt="image-20210705101241580" style="zoom:67%;" />



1. 适用于同一页面局部渲染的时候，该局部是有多组件组成的（就是在路由设置的时候，components有多个组件，且路由是嵌套的），这个往往要配套命名视图使用


<img src="C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210705101936928.png" alt="image-20210705101936928" style="zoom:67%;" />

## 九、组件的数据获取注意事项

对于单个组件来说，一般会在其created内部获取数据，但是直接在cerated内获取数据，而且created内部无法直接拿到dom，因为还未挂载，无法达到对dom的真正修改，如果有对dom的操作，要放到nextTick中，created内部nextTick是初始化dom渲染完成的延迟回调；

但是上面仅限于同步获取数据的情况，如果是异步获取数据的话，异步获取数据的操作是在声明周期后面的，所以nextTick最好放在mounted后面，最好是在updated里面，并对updated设置防抖；



对于父子组件之间的数据：父组件一般会把获得的数据通过props传递给子组件，但是子组件的挂载先于父组件的数据更新，也就是是说，子组件可能已经挂载完成了，父组件数据还未全部获取到，一种解决方案同上，在updated里设置防抖获取数据；另一种做法是用v-if判断一下数据是否完全获取，如果取得再传过去





## 十、vue的性能优化

1. v-if和v-for为什么避免在同一个标签上一起用 ？

   v-for本身再绑定key属性

   由于v-for的优先级大于v-if，所以当在同一个标签上时，会优先遍历所有的元素，之后再根据条件删除其中的元素；这样无疑会时减慢了速度；有2种解决方案

   - 在要使用v-for的元素上，再套有一个带有v-if的template，这样就可以先判断时否有，有了再渲染，而且template元素本身就不参与dom渲染
   - 使用一个计算属性代替v-if的判断

2. keep-alive

   keep-alive本身就是为了避免重复渲染，所以性能比较好

   [(27条消息) Vue ＜keep-alive＞ 首次渲染、组件缓存和缓存优化处理 源码实现_陈坚泓的博客-CSDN博客](https://blog.csdn.net/weixin_42752574/article/details/108560026)

3. 组件复用

4. vue的作用域链

   [vue 中4个级别的作用域 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1627192)

5. vue的依赖注入：

   [vue中provide和inject使用 - SegmentFault 思否](https://segmentfault.com/a/1190000021216039#:~:text=vue 提供了 provide 和 inject 帮助我们解决多层次嵌套嵌套通信问题。 在 provide,中指定要传递给子孙组件的数据，子孙组件通过 inject 注入祖父组件传递过来的数据。 其实， provide 和 inject 主要为高阶插件%2F组件库提供用例。)



## 九、路由的参数传递

1. 如果使用params传递，则可以通过字符串形式传递:

   ```javascript
   :to = "'/user/'+id"
   ```

2. 因为params不可以和path一起使用，所以如果显示的传入params的话，就只能通过命名路由：

   ```javascript
   :to = "{name:"user", params:{id:id}}"
   ```

3. 最好时通过query，这种方式不需要配置动态id

   ```javascript
   :to = "{path:"user", query:{id:id}}"
   ```



如何在该视图(路由)组件获得参数？

以上3做方法，其中前两个参数，都可以通过this.$route.params.id获取；其中最好一个是通过query获取，this.$route.query.id