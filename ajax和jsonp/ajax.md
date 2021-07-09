# Ajax

## 一、Ajax简介

- AJAX = 异步 JavaScript 和 XML。
- AJAX 是一种用于创建快速动态网页的技术。
- 通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
- 传统的网页（不使用 AJAX）如果需要更新内容，必需重载整个网页面。
- 有很多使用 AJAX 的应用程序案例：新浪微博、Google 地图

## 二、Ajax基本使用

1. 建立XHR对象

```javascript
现代浏览器：
xmlhttp = new XMLHttpRequest()
旧浏览器
xmlhttp = new ActiveObject()
```

2. 向服务器发送请求

```
xmlhttp.open(method, url, async)
method要么是"get",要么"post",
url
async：True或者False，False就是不异步，这没啥意义
open方法并没有发送请求，真正发送请求的是send
xmlhttp.send()
```

**Tips**:Content-type ?

3. 该变请求数据的类型，即content-type, 使用该方法：setRequestHeader(*header,value*)

   

   

4. 获取服务器的响应：

   ```
   xml.responseText---------字符串格式的数据，如果来自服务器的响应并非 XML，请使用 responseText 属性
   xml.responseXML---------XML格式的数据，如果来自服务器的响应是 XML，而且需要作为 XML 对象进行解析，请使用 responseXML 属性
   ```

   | 属性               | 描述                                                         |
   | ------------------ | ------------------------------------------------------------ |
   | onreadystatechange | 存储函数（或函数名），每当 readyState 属性改变时，就会调用该函数 |
   | readyState         | 4表示成功                                                    |
   | status             | 200: 完成 404:未找到页面                                     |

   onreadystatechange 事件被触发 4 次（0 - 4）, 分别是： 0-1、1-2、2-3、3-4

通用的Ajax代码：

```JavaScript
  if (window.XMLHttpRequest)
  {
    // IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
    xmlhttp=new XMLHttpRequest();
  }
  else
  {
    // IE6, IE5 浏览器执行代码
    xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
  xmlhttp.onreadystatechange=function()
  {
    if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
        xmlDoc=xmlhttp.responseXML;
        对返回结构的处理的相关代码
  }
```

