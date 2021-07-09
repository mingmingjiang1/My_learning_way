# Tabbar组件的回顾

最后建成的Tabbar可以在其他页面中直接复用，甚至是在其他项目中也可以继续复用。

Tabbar由以下两部分组成：

- 外部的大框，由于外部的大框内部可能有很多小组件，数目不定，这个根据具体需求而定，所以可以放匿名slot；这个匿名slot可以被任意数目的子组件替换

- 内部的子组件：同样的，由于内部的子组件结构都是相似的，但是具体的内容是不同的（比如文字不同），所以子组件里的2个元素也可以用slot代替，因为子组件里的元素数目是2个，而且元素类型是指定的，所以可以用具名插槽

  ​		具体代码结构如下：

  1. 父组件结构：

     ```html
         <div class="tabbar">
             <slot></slot>
         </div>
     ```

     

  2. 子组件结构：

     ```html
     <template>
         <div class="tabbar-item" @click="btnClick" >
             <slot name="icon-active"></slot>
             <slot name="icon"></slot>
             <div>
               <slot name="icon-text"></slot>
             </div>
         </div>
     </template>
     ```

     **noting：**

     - [x] 对组件的封装，如果想某个功能由用户更多的决定，可以对这个组件用props，从父组件传递，比如这里有这样一个需求：用户在点击子组件的时候，子组件内部的字体可以发生变化，如果这个变化写死了的话，直接将子组件的变化比如设置为红色，当然为了不写死，可以在外层绑定一个props，让用户决定用具体的变化属性；

     - [x] 另外，由于slot插槽会被具体的标签替换掉，导致定义在slot上的属性可能会消失，为了避免这个情况，会在外层包裹一个div属性，把原本定义在slot上的属性全都转移到这个外部元素上；

     - [x] 大小写问题：html对大小写不敏感，如果组件名是大写的，那么html必须也要大写，但是html对所有的大写都会转换为小写，所以导致标签未定义，html找不到对应的情况；为了避免这样的情况，建议在组件名要么小写，要么驼峰命名法，如果是驼峰命名法：那么html就相对应的用连接符转换：如TabbarItem => tabbar-item;

     - [x] 动态属性：如果属性语法：path="/home"，那么这个时候path是写死的；相反：:path="/home",那么这个时候path是动态的。

     - [x] vue style中，导入其他样式文件是这样的语法：

     > @import url("./assets/css/base.css")

     - [x] 插件的使用：在index.js中定义插件并导出后，需要在main.js中导入，并在该文件生成vue对象的时候插入该属性，这样即在vue对象中拥有了该插件对象；不管是axios插件，还是vuex、vue-router对象都是如此。vuex的文件夹是store，然后store中有一个index.js，vue-router的文件夹一般命名为views，同样地有一个index.js

     - [x] 为了避免在App组件中出现大量的代码，通常还要把App中代码继续抽离

     

Tips:

| 功能                                                         | 方法或属性 |
| :----------------------------------------------------------- | :--------: |
| **vue.$router** 当前Vue对象的router对象，一个vue对象只有一个router，是全局的，任意组件都可以随意访问 |            |
| 用于更新当前页面，因为是用栈存储的，可以返回之前的页面，栈顶的页面（当前push的）就是当前展示的页面 |    push    |
| 用于更新当前页面，和push唯一的区别是不可以返回之前的页面     |  replace   |
| **vue.$route**,当前Vue对象的route对象，一个vue对象可以有很多个route对象，由路由数决定， |            |
| 可以获取当前页面（活动路由）的路径                           |    path    |
| 可以获取当前页面（活动路由）的参数, 局限于只能传递动态路由url后面的参数 |   params   |
| 可以获取当前页面（活动路由）的参数，传递任意参数             |   query    |




