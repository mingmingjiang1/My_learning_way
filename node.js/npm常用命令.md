# npm包管理以及node.js的导入和导出

## 一、npm包管理及其命令

查看版本：npm --version

升级：npm install --global npm 

生成package.json文件：

- npm init   
- npm init -y 一步生成

npm install 包名

npm install --save(保存依赖项且在package.json中会保存)

npm install(一步安装pkg文件里的所有文件)

**Tips:** i == install   -S == save

普通删除: npm uninstall 

删除对应的依赖项：npm uninstall --save

查看帮助信息：npm --help

对某个具体命令查看帮助信息：npm command --help

安装npm对应的镜像版本：npm install --gloabl cnpm

使用cnpm安装：cnpm install 包名

如果不像安装cpnm但是又想用淘宝源，在安装的时候加参数：--registry-

但是每次在命令中添加url特别麻烦，所以可以全局配置一下：

npm config set registry "源"

npm config list 查看npm配置信息



## 二、npm模块的加载规则

**模块的导入规则：**

加载模块的时候，如果这个模块之前已经加载过了，则不会重复执行其内部的代码，而是从缓存中加载



**包的加载类型及顺序：**

- 加载类型
  - 优先从缓存加载
  - 按路径加载，一般多是自己写；
  - 内置包，这些包是node自带的，如fs，http；
  - 第三方包，需要自己安装，第三方包一般有全局的和局部的，
- 加载顺序：这个加载顺序仅对于第三方包而言，在加载第三方包的时候，如果是局部的，会在当前目录下的node_modules中寻找该包，找到了之后，会在其中的一个package.json文件中找到一个main属性，这才是该包真正的入口，然后找到对应的文件，在这个文件里导出的才是真正的导出对象；如果main函数没有对应的文件或者没有指定对应的入口，则静默寻找当前目录下的index.js文件，如果有则直接使用这个index.js文件；否则则认为在当前目录下不存在该第三方包；则继续向上一层寻找，寻找规则也是如此，直到寻找到根目录。
- 如：art-template包；先找node_modules，再找package.json文件，再找main属性，main属性的值对应的文件就是art-template真正入口模块。



**模块的导入：**

Common.js导入导出：其实每一个文件都有一个module.exports对象，module.exports才是真正一个文件return的一个模块对象；如果没有需要导出的变量，那么为空，如果需要导出多个变量：

``` javascript
exports.exports.a = 6;
exports.exports.b=y;

//另一个文件中
var obj = requires("");
obj.a
obj.b都可以获取
```

导出单个变量：

``` javascript
exports.exports=x

var obj = requires("")
//obj即是x
```

但是这样在导出多个变量的时候书写太过于冗余，后来有了一个module.exports的别名exports，其实是一个引用：exports = module.exports，所以后面书写多个变量的时候，可以这样：

```javascript
exports.a = 6;
exports.b=y;
```

注：不能随便切断exports和module.exports之间的引用关系，如：

```javascript
exports = x;//exports现在已经指向了另一个对象，x无法导出
module.exports = y;//同样地module.exports指向了另一个对象，之后在export挂载的任何属性都无法导出


最后看一个例子：
exports = 1;
exports.a = 12;
module.exports.a = "hello";
exports = module.exports;
exports.y = 12
最终的导出是 {
    y:12,
    a:"hello"
}
```



## 三、express

介绍及安装

express是一个http包，这个包高度封装关于http的方法，特点是特别快，安装npm install express --save

