# Vuex

[TOC]



## 一、初识Vuex

之前提到过父组件可以通过props向子组件提供数据（动态的或静态的），这样的功能适用于封装的时候父组件的信息控制子组件的信息，或者自定义表单信息的时候，父子组件的通讯；

考虑以下需求：某些数据不光是父子组件，甚至在大部分同级的兄弟组件也需要共享，这个时候props便不好使了，或者如果在某些情况下，父子组件想要共享一些数据，同时这样的数据并不想通过props来传递，这个时候就需要Vuex。

> 数据共享？我们在vue实例的原型上直接定义数据不就是共享，但是这样定义的数据无法双向绑定......

**Vuex的安装：**npm install vuex -save

**Vuex的使用**：和Vue-router一样，Vue-router一般也有一个文件夹，通常命名为store，在它的内部也有一个index.js文件，这个文件就是Vuex对象的安装，实例化和导出；然后在main.js中导入该index.js文件，并在vue实例化的时候注入即可；

## 二、Vuex的工作原理

vuex最大的一个功能就是状态管理，简单来说，由于很多页面会修改共享的数据，势必会导致数据的紊乱，比如数据如果出问题了，却不知道是哪个页面出现了问题，vuex就是实现对数据在多个页面共享的同时，对数据进行状态的变化进行管理和记录。vuex实现管理的核心是在视图、行为和之间对数据变化监听



![img](https://upload-images.jianshu.io/upload_images/16550832-20d0ad3c60a99111.png?imageMogr2/auto-orient/strip|imageView2/2/w/701/format/webp)

从组件开始看这张图，如果不需要异步，则在组件内通过commit方法提交mutations，mutations内部修改数据，修改后的数据再去渲染组件；如果需要异步，先在组件内通过dispatch执行actions，然后由actions提交，相当于中间多了一步。



这张图里，虚线部分都是vuex控制的部分，如果是在组件内的同步代码（用于改变组件的），那么这个改变可以直接提交给Mutations，然后mutations和devtools交互，由devtools管理和可视化，这就实现了从视图到数据的变化；同样地，mutations里地数据发生变化，会引起state的变化，state是用于记录数据状态的一个方法，由更新后state渲染（render有渲染的意思）试图。而actions是管理异步代码的方法，可以看到actions上方有一个backend api这个一般都是网络请求，所有的异步代码，必须要有actions，然后由actions 提交给mutations。

## Vuex单界面到多界面状态管理切换

Vuex建议只用一个Vuex对象来管理所有页面的变量，而不是建立多个Vuex对象

## Vuex 和devtools

## Vuex mutations方法

对应组件里的methods，对于mutations内部的改变数据的函数，必须要交给Vuex对象的commit方法才能真正做的改变数据并由Vuex监听，mutations内部定义的函数可以接受2个参数，state和payload

提交mutation时有两种语法：

1. commit("事件名", payload)  这个时候是直接把payload传递给mutations的payload
2. commit({type:事件名, })  这个时候是直接把对象传递给mutations的payload



因为组件内的methods一般还需要提交一次mutations，而且两者的函数名一般情况下都是相同的，所以一般会把这个函数名抽成一个常量，后面通过这个常量来使用这个函数

## Vuex state方法

相当于组件里的data属性，用于存储共享数据，该属性里绑定的数据都是响应式绑定的，即通过mutation和action改变的数据在其中都可以立即发生改变，但是要注意一点，如果修改的是引用类型数据，则如果想达到响应式的效果，则必须使用响应式的方法，如数组的pop、push、splice等；Vuex建议使用Vue.set修改引用类型的数据，Vue.delete删除引用类型元素

### mapMutations

commit方法太过于冗长，考虑把commit方法给换得简单一点，而且这些本就是和插件相关的方法，而不是组件本身定义的方法，确实也应该打包在一起。如何打包？比如之前需要在组件内这样定义方法：

> methods: {
>
> ​	increment() {
> ​		this.$store.commit("increment", 100);
>
> ​	}，
>
> ​	decrement() {
>
> ​		this.$store.commit("decrement", 20);
>
> ​	}
>
> }

现在可以这样：

> ... mapMutations({
>
> ​	add: "increment",
>
> ​	sub: "decrement"
>
> }) 
>
> map函数返回的是数组，展开操作符用于把数组转化为一个个单个变量与外部的其他方法融合

之后在组件中调用的时候：

add(100) 就是等价于this.$store.commit("increment", 100);

sub(20) 就是等价于this.$store.commit("decrement", 20);

这样每次使用的时候都很方便，而且与Vuex相关的函数都放在了一起

与之相对应的有另外一个是用于管理数据的映射函数mapState

...mapState({

​	属性：state中的属性

})

## Vuex getters方法

对应组件里的computed，对于有些要做一定变化的公共数据，getters是非常有用的，因为对于公共数据，如果在每个组件里都定义methods或computed势必会增加复制代码的麻烦，如果在Vuex中只需定义一次即可，getters默认接受state和getters两个参数，语法如下：

> getters：{
>
> ​	f(state, getters) {
>
> ​		return ...
>
> ​	}
>
> }

那这是不是意味着getters内部的函数无法传递属于自己的额外参数？

如果想要传递自己的参数，可1让getter返回一个函数即可，这样在使用的时候，就可以传参了。

## Vuex modules方法

由于Vuex是单一状态管理，所以可能会导致再一个状态内定义过多的变量会引起太过于复杂的问题，所以Vuex允许我们在一个状态内将这些变量分开，即在一个状态内部切分成了很多的子状态，父状态时可以任意访问子状态的，而子状态必须通过rootState去访问根状态，那组件内如何访问子状态的state呢？store.state.模块名.模块内的变量

## Vuex actions方法

与mutations类似，只不过actions用于处理异步程序，所以它也要需要通过commit才能算是真正交给Vuex管理。

组件内：this.$store.dispatch("action内的一个方法名", payload)

vuex内：upload(state, payload) {

​	这是一个同步的代码

}

actions: f(context, payload) {

​	setTimeout(()=>

​		context.commit("upload", payload);	

​	,1000)

}

context类似一个store对象

dispatch是可以返回一个契约的

## Vuex 文件夹目录组织

state属性不用抽离，其他的属性全要抽离，尤其是modules，它还要自建文件夹，文件夹内有各种单个module。



**Tips**:Vue有2个最重要的地方，一个是通过Vue实例化的vue对象，几乎所有的后序工程都是围绕着这个对象，当然为了操作某些对象，Vue本身也提供了很多全局方法，如Vue.set, 当然这些方法很多用js方法也能实例，但是在vue管理的内容中，我们尽量用Vue全局方法。

### Conclusion

Vuex是一个状态管理的插件，整个插件以声明vuex对象为核心；vue对象中后面主动挂载的属性一般都用$访问，如vue.$store表示vuex对象，vue.$router表示路由对象，美元操作符主要是为了和原生js区分，因为我们实际上访问vue的属性和方法都是在vue实例内部，那么必然通过this.$的方式访问，但是全局对象可能也有同名方法，因此vue在注入的时候，就用了$来区别，基本上你可以看到的只会在this后面跟$，因为只有this可能会和全局this（window）混淆，后面一旦进入了指定属性的内部，都是确定的。

实战的一些注意点：

照片路径的写法要注意，直接写相对路径是找不到的，一般是通过import导入；定时器都要赋值，然后在必要的时候要清空变量，由于异步函数里的this是属于windows，所以在异步内使用匿名函数，在外层最好把this换个名字，比如that