# Bootstrap

## 一、bootstrap基本使用

下载：

- npm下载：npm i bootstrap@version
- 手动下载到本地，script引入其中的css样式文件即可

响应式都需要有容器，这样这个容器就可以使用媒体查询随着窗口的大小而变化，bootstrap已经给我们手动配置了两个容器类，而不需要手动写书写媒体查询语法

.container类

- 固定宽度
- 大屏（>=1200px）宽度定为1170px
- 中屏（>=992px）宽度定为970px
- 小屏（>=768px）宽度定为750px
- 超小屏（100%）

.container-fluid类（就是百分比布局）

- 流式布局百分百宽度
- 占据全部是视口
- 适合单独做移动端开发



## 二、bootstrap栅格系统

### 格栅系统的基本使用

与rem将屏幕划分为不同等分不同，bootstrap是将容器(.container)划分为一个个列，最多划分为12列。随着设备的不同，container的尺寸也不同（共4种尺寸），每一列所占的宽度也不同；

相比于flex，flex不能自适应，bootstrap是可以自适应的调整自己的宽度

```html
<div class="container">
    <div class="col-lg-3"></div>一行
    <div class="col-lg-3"></div>一行
    <div class="col-lg-3"></div>一行
    <div class="col-lg-3"></div>一行
</div>
每个占3份

如果容器的子元素的占的列数<12, 那么就占不满整个container
如果容器的子元素的占的列数>12, 那么会另起一行
```

**noting：**

- 行是子元素
- .col-xs-; .col-sm-; .col-md-; .col-lg-
- 每一列默认有左右15元素的padding
- 可以同时为一列指定多个设备名，以便在不同的设备显示不同的样式：如class="col-md-4 col-sm-6"
- 每一行默认有左右15元素的padding，如果显示的提供了.row类名，则这个15px padding则消失；
- 所以不过自己不处理的话，每一个单元格相当于是有30px的padding

### 列嵌套

列嵌套的时候最好都指定.row类名，表示每一行

```html
<div class-".container">
    <div class="row">
        <div class="col-md-4">
            <div class="row">
                <div class="col-md-6">
                <div class="col-md-6">
            </div>
        </div>
        <div class"col-md-4"></div>
        <div class"col-md-4"></div>
    </div>
</div>
```



### 列偏移

<img src="C:\Users\Mmjiang\AppData\Roaming\Typora\typora-user-images\image-20210624162748383.png" alt="image-20210624162748383" style="zoom:50%;" />

如果不用列偏移很难左到上面的效果，因为栅格系统默认是左浮动的。

```html
<div class=".container">
	<div class="row">
        	<div class="col-md-4"></div>
        	<div class="col-md-4 col-md-offset-4"></div>
        	向右偏移4列，偏移份数 = 12 - 2*一个盒子的偏移份数
	</div>
</div>
```

### 列排序

希望对列进行交换顺序

.col-md-push-m：将盒子往右边推m列

.col-md-pull-m：将盒子往左边拉m列

```html
<div class=".container">
	<div class="row">
        	<div class="col-md-4 col-md-push-8">左侧</div>
        	<div class="col-md-8 col-md-pull-4">右侧</div>
        	这就实现了左右盒子交换顺序
	</div>
</div>
```

### 响应式工具

|    类名    | 超小屏 | 小屏 | 中屏 | 大屏 |
| :--------: | :----: | :--: | :--: | :--: |
| .hidden-xs |  隐藏  | 可见 | 可见 | 可见 |
| .hidden-sm |  可见  | 隐藏 | 可见 | 可见 |
| .hidden-md |  可见  | 可见 | 隐藏 | 可见 |
| .hidden-lg |  可见  | 可见 | 可见 | 隐藏 |

与之相反的一个功能是visible-xs, visible-sm, visible-md, visible-lg, 

## 三、案例（阿里百秀）

分析：

- 阿里百秀中屏幕和大屏幕布局是一致的，因此只需定义col-md即可吗，md是大于等于970以上的
- 当屏幕缩放到小屏幕时，布局发生变化，因此需要为小屏幕设计布局
- 同样超小屏幕也是如此

**Tips:**

- 绝对定位的元素是相对于第一个具有定位属性的元素定位的，也包括width这样的属性，比如，现有一个元素为绝对定位，其宽度为100%，那么其占比是相对于它的第一个具有定位属性的父级元素而言的；
- max-width的百分比是相对于自己，比如img {max-width: 100%}，那么它的最大宽度就是它自己；

- 行内元素包裹块级元素后，自己就是块级元素了，独占一行

## 四、bootstrap

### 常用基本样式

- 字体图标
- 字体颜色
- 代码
- 表格
- 表单
- 缩略图和面板

### 插件

- 导航
- 分页
- 下拉菜单
  - 包裹的大盒子(div)
    - 按钮(button)
    - 下拉的一些菜单(ul)
      - head(介绍，li)
      - 选项(li)
- 模态框