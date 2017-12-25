### AJAX

#### 简介
AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。  
AJAX 最大的优点是在不重新加载整个页面的情况下，可以与服务器交换数据并更新部分网页内容。  
AJAX 不需要任何浏览器插件，但需要用户允许JavaScript在浏览器上执行。

#### 创建对象
``` js
var xmlhttp;
if (window.XMLHttpRequest)
{
    //  IE7+, Firefox, Chrome, Opera, Safari 浏览器执行代码
    xmlhttp=new XMLHttpRequest();
}
else
{
    // IE6, IE5 浏览器执行代码
    xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
}

```

#### 发送请求
``` js
xmlhttp.open("GET","ajax_info.txt",true);
xmlhttp.send();
```

| 方法| 描述 |
------|-----|
open(method,url,async)|规定请求的类型、URL 以及是否异步处理请求。***async*** : ***true*** or ***false***
send(string) | 将请求发送给服务器

``` js
//为了避免获取缓存，可以请向 URL 添加一个唯一的 ID：
xmlhttp.open("GET","/try/ajax/demo_get.php?t=" + Math.random(),true);
xmlhttp.send();
```

#### POST请求
``` js
xmlhttp.open("POST","/try/ajax/demo_post2.php",true);
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Henry&lname=Ford");
```
#### 获取响应
属性|描述
---|---
responseText|获得字符串形式的响应数据
responseXML|形式的响应数据

解析XML：
``` js
xmlDoc=xmlhttp.responseXML;
txt="";
x=xmlDoc.getElementsByTagName("ARTIST");
for (i=0;i<x.length;i++)
{
    txt=txt + x[i].childNodes[0].nodeValue + "<br>";
}
document.getElementById("myDiv").innerHTML=txt;
```

#### 监听状态变化
属性|描述
----|----
onreadystatechange|存储函数（或函数名），每当 readyState 属性改变时，就会调用该函数。
readyState|0：未初始化 1：服务器已建立连接 2：请求已接受 3：请求已处理 4：请求完成
status|200和404

``` js
xmlhttp.onreadystatechange=function()
{
    if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
        document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
    }
}
```

***JS中函数可以作为参数传递***

#### 其他
1. HTML标签的`onkeyup`属性可接受函数，在操作完成后被调用：
``` html
//输入完成后将调用showHint()函数
<input type="text" id="txt1" onkeyup="showHint(this.value)" />
```
