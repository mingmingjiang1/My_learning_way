# Root em布局

- flex布局中的文字是写死的,不论页面多大，文字大小是固定的
- flex和流式布局主要是针对宽度布局，高度是写死的，那高度如何设置动态变化？
- 怎么让屏幕发生变化时，元素的宽度和高度等比例缩放？

## 一、rem简介

rem是一个相对单位，

- em是相对父元素字体大小；
- rem是相对 html 元素的字体大小

优点：根元素只有一个，这样标准只有一个，所有的字体都以它为标准，较易控制

媒体查询引入外部链接：

```html
<link rel="css" media="screen and (max-width: 800px)">
```



媒体查询+rem原理：给每个媒体响应不同的html字体大小，从而控制不同媒体的子元素大小。

rem适配方案：

- 动态设置html根元素font-size，后面所有元素都是以这个font-size为单位
- 设置比例，根据比例计算出html的font-size，这里的比例是值划分的等份数，比如比例是15，屏幕是750px, 那么html的font-size就是50px，在320px的设备下，html的font-size就是（320/15）= 21.33px, 

因为以上的html这个字体通常不是写死的，需要动态设置，但是css本身没办法计算表达式，索引引入了less

**less**: css的预处理器，还有sass，stylus，这些扩展语言扩展了css的动态性

1. 变量：@变量名: 值；使用变量：@变量名
2. 嵌套：类名 {   儿子{}}；对于伪元素、伪类这种不属于子元素的附加元素，需要&开头，表示不是子元素，如a:hover{} => a{	&:hover{}	}，再如div::after{} => div{	&::after{}	}
3. 运算：运算符两边有空格，对于两边不同单位的值之间的运算，运算结果取第一个值的单位；如果只有一个值有单位，则运算结果就取该单位

## 二、案例

第一步：

新建common.less，设置常见的屏幕尺寸，利用媒体查询设置不同的html字体大小，因为除了首页其他页面也需要，规定比例为15

```html
//如果是pc端，不管你屏幕是多大，默认是字体单位都50px，注意在flexible.js中，
就是因为没有这一句，导致在pc端时，它是按照全屏计算的字体单位，所以在用flexible.js时
需要手动设置一下：当屏幕过大时，字体单位仍然按照750px
@media screen and (min-width:750px) {
	font-size: 75px
}
//
html {
	font-size: 50px
}

@num: 15;

//320px设备
@media screen and (min-width: 320px) {
	html {
		font-size： 320px / @num;
	}
}

//360px设备
@media screen and (min-width: 360px) {
	html {
		font-size： 360px / @num;
	}
}

//375px设备
@media screen and (min-width: 375px) {
	html {
		font-size： 375px / @num;
	}
}

...
```

> 以上是自己书写响应式的，实际上有一个flexible.js文件，可以让我们不用手动书写响应式的



@import和link的区别

@link用于css之间的导入，而link是把css引入到html文件中

```html
@import "文件名"
```

宽度：

- 如果设定了宽度，那么元素的宽度不会随浏览器窗口变化而变化
- 如果未设定宽度，或者说跟随浏览器100%或者类似块级元素默认占满整个视口，则会随浏览器窗口变化而变化；
- 设定了min-width和max-width，那么元素随浏览器窗口变化到指定值时，元素宽度就不再变化

css文件中资环转换未rem的插件：cssrem



元素 / @basefont = rem;

设计稿：750/50 = 15;

其他设备：320/14 = 15;

设计稿另一个元素：44/50=0.9 

其他设备对应的另一个元素：0.9*14 = 9 => 9/14 = 0.9