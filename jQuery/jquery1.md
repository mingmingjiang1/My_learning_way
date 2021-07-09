## 一、jQuery的使用

1. 一般使用，通过在script标签中引入，jQuery源码文件即可，官网有压缩版本的，也有未压缩版本的；
2. npm安装



### 1.jQuery入口函数

- 原生的js通过onload，在页面加载完成之后，执行js代码，默认的script标签可能会阻塞html的渲染，所以一般script放在页面最后，当然也可也放在前面，采用异步加载的方式，onload函数会延迟到dom渲染完毕图片加载完成才执行；其次如果页面中有两个onload，则后一个onload会覆盖前面的onload，这个和onclick它们是类似的；

- 而jQuery的入口函数，只是等到dom渲染完毕就即刻执行，它不会等待图片以及其他外部文件的加载完成，所以一般在这个入口函数内想要获取img的宽高则会获取不到，捕获jQuery有一个函数可以使得如库函数进入异步队列等待，即holdReady；另外，其次如果页面中有两个onload，则会依次执行；

jQuery入口函数四种书写方式：

1. $(document).ready(function);
2. jQuery().ready(function);
3. $(function);**建议用这个**
4. jQuery(function)

其中function是异步回调函数

解决jQuery命名冲突问题：

如果其他文件或者第三方文件也有$,则会引起问题，后面引入的带有$会覆盖前面的，假如后面引入的jQuery，这个适合jQuery的$就不起作用了，jQuery针对这个问题有两种方案，

- 释放jQuery的命名控制，简而言之，就是我放弃了，我不用了，你用吧，jQuery.noConflict();
- 重命名，比如我们对$重命名，var # = jQuery.noConfict();这样以后就是#代替了$

jQuery对象：

jQuery对象是一个伪数组，对于核心函数$()，其参数可以是function，就是上面我们提到的入口加载函数；可以是字符串，也可以是html代码片段（创建dom元素）更可以是元素选择器（选择对应的元素），这3种使用最终返回的都是jQuery对象



jQuery函数：

js对象有两种方法，静态方法，定义在类上，通过类调用，实例方法，可以定义在类定义的时候，也可也定义在原型上，更可以在创建对象的时候动态绑定，jQuery提供了操作jQuery对象的各种静态方法：这里提几个和原生js不同的

1. each

它和原生js的不同在于，既然它是jQuery对象的，而jQuery是伪数组，所以它是可以操作伪数组的，但是原生js不行

```javascript
$each(arr,function(index, item) {})
注意和原生js的参数是反的，它不对任何元素进行操作，所以返回值是arr本身
```

2. map

和each一样

```
$map(arr,function(index,item){})
和$each不同的是，它可以对元素进行操作，并返回操作后的数组
```

3. $isArray()判断是否为真数组
4. $isWindow()判断是否为window对象
5. $trim(),去除字符串两端空格
6. $isfunction
7. $holdReady()暂停jQuery的代码

### 二、各种增删查改

#### 1.内容元素选择器

之所以叫这个名字是因为它是根据元素的子元素内容来进行选择的

```
$("div:empty")	选择没有子元素的tag为div的元素
$("div:parent")   选择有子元素的tag为div的元素
$("div:contain("你好")")选择有指定内容的tag为div的元素
$("div:has("span")")选择有指定子元素的tag为div的元素
```

#### 2.属性（attrbute node）结点控制

原生js:

```
getAttribute(attr)

setAttrbute(attr, name)

removeAttrbute(attr)可以不用
```

jQuery

```
$().attr(attr)，获取找到的第一个指定元素指定结点，只找到第一个

$().attr(attr, name)，设置找到的指定所有元素的指定结点，若无则新增

$().removeAttr(attr)，删除所有指定元素指定结点
```

#### 3.属性（attr）的控制

prop，removeProp这个方法是用来设置属性的，当然它也可以像attr，removeAttr那样设置属性结点，与它的用法和特点都一样，不同的是，对于值为true or false像checked，disabled和selected，最好用prop来获取和设置，因为attr获取的不是true or false，其他的都用$attr

#### 4.类（class）的控制

addClass("clssname1 classname2 ...")添加类名

removeClass("clssname1 classname2 ...")

toggleClass(classname)，切换类，有就删除，没有就删除

#### 5.文本（text）的控制

html(html文本)和原生innerHTML一样  html()获取对应元素的innerHTML

text(普通文本)和原生的innerText一样 text()获取对应元素的innerText

val用于控制表单元素的value，value()获取对应表单原生的value属性

#### 6，样式（style）的控制

css(stylename,value)

如果有很多样式，

逐个设置：css("width":"20px");css("height":"20px");css("color":"red");

链式：css("width":"20px").("height":"20px").("color":"red");

批量对象设置：css({"width":"20px","height":"20px","color":"red"})

获取css(stylename)



#### 7.位置和尺寸的控制

width() 获取元素的宽

height() 获取元素的高

offset() 获取和设置元素距离窗口的（主要是上和左，相当于x，y）距离，返回的是个对象，里面有x和y属性，分别表示距离左和上的距离

postion()获取元素距离定位元素的偏移（主要是上和左，相当于x，y），**不能设置**



**获取滚动元素(带滑轮的)滚动偏移：**

scrollTop()

scrollTop(value)

也可以获取设置网页(html)的滚动偏移



## 三、事件

#### 1.事件的绑定与解绑

两种绑定：

- eventName(fn) 推荐
- on("eventName",fn)

它们本质都是addEventListener的封装



off("eventName", fn)

如果没有任何参数，则解绑该元素之前绑定的所有事件，或者指定eventName也可也解绑指定类型事件,或者指定2个参数，很明确指定解绑对应的事件和监听函数

#### 2.事件冒泡

- 阻止冒泡：默认是冒泡的

  ```javascript
  监听的回调函数中，最后返回false即可，默认是true，这样通过true不断地传递到上一层或者继续执行元素的默认行为代码；或者调用触发事件的event.stopPropogagion()，阻止冒泡
  click(function(event) {
      //do something
      return false;
  })
  ```

  

- 什么是默认行为

  如<a>点击默认跳转，submit点击默认提交

- 如何阻止默认行为

  监听的回调函数中，最后返回false即可，默认是true，这样通过true不断地传递到上一层或者继续执行元素的默认行为代码，所以返回false即可，或者event.preventDefault()

#### 3.事件自动触发

trigger("eventName")对某个元素设置自动触发某个事件，在页面加载过程中自动执行,特点默认会有冒泡行为并且会有元素的默认行为，**但是 <a> 和 trigger不具有默认行为自动触发，需要单独对待<a>元素**

triggerHandler("eventName")特点没有冒泡行为，不会有元素的默认行为

#### 4.自定义事件

trigger(fn) 就是让页面加载的时候自动执行某个函数，但是自定义事件的时候不能使用事件名绑定自定义事件，因为事件名变量必须是系统自定义的，而on函数内部可以是普通字符串

#### 5. 事件委托

自己做不了的事让别人来做，最典型的应用的是把父亲的事件分发给子元素（原理就是子元素上触发的事件会通过冒泡传递给父元素，这样就不用给每个子元素都添加事件了，此时触发元素和响应元素不是同一个元素）在jQuery中有显式的分发事件语法，可以很方便的指定想要委托的元素，而原生的js比较麻烦，因为还有取消其他不是想要绑定事件子元素的其他子元素的事件

delegate(子元素, 事件名, callback {这里的this是触发元素，而不是响应元素}) 

#### 6.命名空间

就是在事件名后面加后缀即可，相当于就是自定义事件，所以同样也是只能用于on中，如：

```javascript
$('#div').on("jmm.js", function)
$('#div').trigger("jmm.js")
```

自定义事件的冒泡只会引起父亲的自定义事件冒泡；但是默认事件的冒泡会全部执行，也包括子元素的默认事件



mouseover(移入)

mouseout(移出)

这两个事件在jQuery中都有冒泡效果, 一般不用



mouseenter

mouseleave

hover(fn1, fn2)：移入移出，fn1是移入的回调函数，fn2是移出的回调函数

