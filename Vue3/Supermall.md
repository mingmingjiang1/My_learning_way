项目的一个过程：

1. 划分目录结构
2. 引用css文件，base和normalize
3. vue.config.js对别名进行配置和editorconfig对编码风格进行配置
4. 组件划分和路由配置





1. 我记得除了v-for要加key属性，还有一个什么元素也要加
2. 主文件app中只放最简单的代码
3. public中的文件时干嘛的:静态资源目录，build之后，public下面的文件会原封不动的添加到dist中，不会被合并、压缩；多用来存放第三方插件。类似于vue2中的 static 目录
4. 为了更多的基于用户样式的控制，可以直接在使用组件的文件中重新给该组件设置样式，这样利用继承性就可以了
5. 在进行模块化组件的时候，包含性组件：Tabbar中又包含很多小的item，轮播图也是外部一个包裹性的大组件，里面有很多小的item
6. resolve：一个拼接路径的包，另外一个变量叫___dirname，表示当前运行文件的目录（绝对）
7. 路径的波浪号~：当用了别名的时候，如果是在vue代码中，那么直接使用别名即可，如果是在html代码中，则必须在别名前面加~
8. sass
9. request的config
10. 回调函数再悟：之前的回调函数都是直接处理结果，外面包裹一个对象就可以把值导出来
11. 面向中间件编程，比如网络的基本配置都放在一个文件中，然后不同的页面对数据的请求都打包放在另一个文件里
12. 懒渲染导致了缓存复用，最常见的就是input元素中的value
13. 在vue中导入的css文件时是全局起作用的，以及在单个组件内如果不加scoped属性也是全局的，为了隔离组件的css影响，需要使用scoped属性
14. betterscroll
15. 百分之99的文件都不应该放在public文件下，因为大部分文件都需要经过webpack打包处理压缩，但是又2个文件必须放在public文件夹下，一个是index.js一个是图标，因为这和seo优化有关，而且图标文件是index.js的head中就引入了。
16. betterscoll用了之后，内部的默认效果都不起作用了，比如sticky，click事件，因为它用了自己重写的一些滑动覆盖了原来的
17. 为什么有.native还要有emit，主要是因为如果子组件是原生事件，父组件想监听这个原生事件，那么确实可以用.native方便一点，但是.native必须是vue已经注册过的原生事件类似click，focus这种，对于第三方插件，比如pullup，vue原生是没有的，那么.native是无法监听这个事件的，所以只能用emit自定义事件
18. 当默认值是对象或数组，default必须是函数的形式：default() { return []}
19. $on相当于监听组件的自定义事件，$emit相当于组件自定义事件，这两个是成对出现的
20. 一定要注意事件的运行顺序，比如betterscroll的一个bug：在孙代组件中请求数据（异步），完成之后，会发射给根组件一个事件，一旦检测到这个事件，就立马调用betterscroll的refresh的方法；而betterscroll的实例是在mounted中生成，也就是在根组件挂载完之后才生成了该实例；想象一下，申请数据是在created完组件之后立即申请，这个时候把主线程的程序执行完，然后进行数据申请，如果数据很快申请完，那么立马发射事件，然后由根组件检测到，然后执行refresh，但是这个时候betterscroll还没实例化呢。反之，如果数据请求比较慢，那么这个时候该数据放在异步队列中，退出该函数，继续执行主线程的代码，即挂载组件，然后执行mounted里的方法，然后在这里面实例化betterscroll实例，然后如果这个时候去执行refresh是可以的。
21. 防抖：等等你吧，我们干脆等到timeout执行
22. nextTick只能等同步的，无法等异步的



手机端滑动页面时的弹簧效果，

吸顶效果：就是sticky



生命周期都干了些什么？



文件夹划分：

第一级：

- node_modules
- publics
- src所有代码文件都在这里，核心开发部分
- package-lock.json，锁定版本以及下载比较块
- package.json
- vue.config.js更新配置，比如说别名什么的



二级：

- main.js
- App.js核心汇总组件的文件
- views各种视图，或者是页面
- store：状态
- router：控制路由，一般只需要一个index.js文件
- network：网络配置
- components：这个里面又包含两者组件，一个是common不只是在当前项目中可以用的，在其他项目中也可使用，这里都是高度包装且比较规则的组件，另一个是content，仅仅是在当前项目中使用的，一般common中的组件都会经过自己的修饰然后再放在content中
- common：一般是所有文件都需要的一些全局变量
- assets：静态资源



template、el和render

先说优先级：render > template > el

template和el：共同点都是vue语法的起始点，它们本身并不参与渲染，区别template只是标识了组件的始末，但是并不知道组件挂载在何处，所以template一般用来生明子组件，然后通过父组件的component属性来进行挂载，挂载在父组件下面，这样就形成了组件树；而el标识了这个组件挂载在哪个html下面，从而把整个组件树挂载在html文档上，所以一般只有跟组件有el，综上：el是特殊的一种template，由于template是vue渲染的基本摹本，而且el上指示了要挂载的元素，所以el是最慢的，

template和el最终都要经过render函数，如果确实想直接把某一个子组件挂载在html上，可以直接在该组件上书写el属性，这样它就知道该挂载在哪个标签下面，或者用$mount也是可以的

所以这样就明确了一件事：只有当组件挂载（mount）到了html文档树上，它才属于dom对象，可用dom对象操作

所以生命周期：

beforeCreated:数据都没创建好，这个时候是无法获取data，method的

Created：组件创建完成，但是没有挂载，这阶段可以回调一些数据请求

beforeMounted：生成完了虚拟dom，但是仍然无法操作dom，因为还未挂载完成（这个时候已经完成了从el=>template=>render的过程，生成了结点描述对象，即虚拟结点）

mounted：挂载完成，可以操作dom，即调用相关dom的api（这个时候生成了真正的dom）

beforeUpdate：这个函数完成，还未更新，但是组件内的数据是新的，视图上的数据还是旧的，即还未完成diff的对比（本质上其实从新渲染了一个虚拟dom，但还未对比）

Updated：已完成diff算法对虚拟dom和真是dom的对比，并更新视图上的数据

以上两个方法都是在组件运行期间，即数据发生变化时才调用，初始创建组件并不调用

beforeDestroy：准备销毁组件，但是组件仍然可用

destroyed：已经消耗，组件不可用



![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS84LzE5LzE2Y2E3NGYxODM4MjdmNDY?x-oss-process=image/format,png)



已完成的部分：

项目第一天：

项目第二天：

- **FeatureView**
  - div(root)>a>img

<img src="C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210628200252507.png" alt="image-20210628200252507" style="zoom:50%;" />

- **TabControl(选项卡)**
  - div(root)>div>span
  - 选中哪一个选项，哪一个选项就变色，这个hover做不了，因为不仅本身会变化，也涉及其他元素

<img src="C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210628200617912.png" alt="image-20210628200617912" style="zoom:80%;" />

- **首页商品数据的请求（动态获取**）
  - 默认每个链接展示申请的第一页，当然第一次申请是请求3个链接的第一页数据，每次点击链接的时候，就会动态地向视图中传递该链接对应的数据；每次加载新的数据时，都会重新渲染页面



- **对获取的数据进行展示**
  - GoodsList组件
    - GoodsItem组件
      - div(root)>a>img



- **BackTop**
  - 监听组件，不用emit
  - 回到顶部：scroll.scrollTo(x, y , time)



- **下拉加载：BetterScroll**

  - BetterScroll的注意点
    1. wrapper>content>很多子元素，content必须设置高度
    2. 监听滚动：probeType:0,1,2,3(2不监听惯性滚动，3只要是滚动)，scroll.on("scroll",(position)=>{})
    3. 上拉加载：pullingUp,上拉到当前的最底部的时候会触发
    4. click:如果为false，则内部的div无法触发点击，true的话内部的div也是可以点击的
    5. 对better-scroll的依赖封装，避免强耦合
    6. refresh解决了什么问题？图片加载完才refresh，但是这里它是加载完一张图片就响应一个onload事件，然后refresh一次，这样会带来频繁调用refresh一次，实际上只要每一页仅仅refresh一次
    7. refresh带来了什么问题？频繁调用，过于频繁，可以用防抖，防抖这种是工具类函数可以放在全局文件内，
    8. scroll.finishUp()每完成一次，就要调用该方法一次；

  其实这里我这里的方法不一样：不需要管图片是否加载完成问题，所以也不需要解决防抖问题

  把所有的有关BetterScroll的方法全都放在this.$nextTick中回调，这个函数是数据更新完才会回调

  

- 保留页面跳出时的内容
  - 在页面离开时，保存当前的移动位置
  - 再回到这个页面时，直接跳到这个位置.



路由跳转：

组件上可以直接用path属性，而不必要用to属性进行路由跳转

在bus里的是全局事件，全局事件可以通过off(事件名)，但是这个注销的是所有相关的事件监听，而off(事件名, callback)是对指定的事件名和回调注销

this.$route.path当前激活的路由的route

详情页:

有一个bug:在原版本的实现中，由于详情页推荐商品展示和主页的商品展示用的是同一个组件，而这个组件为了保证图片完全加载再供better-scroll计算高度，所以会发射一个imageLoad全局事件（之前用bus注册过），表明组件已经加载完成，但是详情页和主页会同时收到这个事件，这其实是不必要的，甚至会导致bug。所以我们必须得对这个2个事件分开，以表明图片加载是针对哪个父组件而言的，有两种方法；

1. 其一是路由，如果this.$route.path.indexOf("/home"),那么说明当前在首页，则发送ImageHome事件；反之如果this.$route.path.indexOf("/detail"),那么说明当前在详情页，则发送ImageDetail事件

2. 其二是注销：即如果离开了首页，就注销图片加载事件和当前回调函数之间的关联，this.$bus.$off()；

   反之，如果离开了详情页，就注销图片加载事件和当前回调函数之间的关联；这里图片加载事件是同一个事件，但是回调函数是各自的回调函数







组件相当于是类，而每次使用组件相当于是实例化一个对象，所以对象（组件）之间的变量是没有关系的



吸顶组件：

组件.$el获取组件的根元素.

渲染不包括数据填充，所以为了用betterscroll不仅需要确保dom已经更新了，而且需要确保数据加载完成



如果没有scoped，上层组件样式覆盖下层的





详情页最好3个任务：

1. 点击详情页按钮，立即跳转到对应位置
2. 滚动到对应位置的时候，tab按钮也会随之改变
3. 底部的工具栏，点击加入购物车，会放入购物车内部，vuex
4. 详情页的回到顶部,混入



Tips:变量名可以驼峰，但是在绑定变量的时候使用“-”





MVC和MVVM

响应式原理

生命周期在各自时间节点内可以干什么事

nextTick所在的作用域应该写和获取数据相关的，但是nextTick只能保证dom已经渲染完成，但是图片不一定渲染完成，dom渲染和图片渲染是分离的，dom先渲染，然后给对应有img标签做个标记，表明这是图片，等到图片dom渲染完成，再去获取图片并渲染（但是图片在本地如果已经缓存过，则不会出现这样的bug;h还有就是）如：

[Web图片资源的加载与渲染时机 - SegmentFault 思否](https://segmentfault.com/a/1190000010032501)

{

​	数据操作

​	nextTick（）

​	数据操作

}



监听图片是否加载完：防抖和判断是否已经加载到最后一张





函数赋值：func = debounce(callback);把callback修饰为了防抖的函数

在其他地方直接使用这个函数func



防抖：只执行最后一次，以下版本一不行

debounce(callback) {

​	if(timer) {

​		timer = null;

​	}

​	let timer = setTimeout(callback, timeout);//也可以是其他异步函数

};





debounce(callback, delaytime) {

```javascript
    var timer = null;
    return function(){
        if(timer !== null){
            clearTimeout(timer);
        }
        timer = setTimeout(fn,wait);
    }};
```



节流：只执行第一次 

var flag = false;

func(callback, delay){

​	if(!flag) {

​		flag = true;

​		setTimeout(callback, delay);//也可以是其他异步函数

​	}

}

图片加载包括其他外部文件加载好像和dom渲染是异步的，但是script的执行和dom渲染时同步地，即共用一个线程，所以一般把script放在最好，不然会阻塞dom的渲染

可以先给图片占位，现代浏览器对图片都采用懒加载方式，优化网页性能，所以是异步的





betterscroll:











store:components->action->mutation->state->components

mapgetters, mapmutations, mapactions

index.js是集中文件不要放太多代码，mutation是简单基本操作，而action是对mutations的集中操作，mutations有对state进行集中管理



