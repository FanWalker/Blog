---
title: DOM学习
date: 2017-08-10 12:46:45
categories: 
tags:
	- html
	- JavaScript
---


###  一、节点层次  ###
 &nbsp;&nbsp;&nbsp;节点分为几种不同的类型，每种类型分别表示文档中不同的信息及标记,节点之间的关系构成了层次，文档节点是每个文档的根节点。节点之间的关系：
![](http://i.imgur.com/2wKDqZc.png)

<!--more-->
#### 操作节点 ####
常用的方法有:

1、appendChild()：向childNodes列表的末尾添加一个节点，参数为要插入的节点，返回新增的节点；</br>

    var returnedNode = someNode.appendChild(newNode);
    alert(returnedNode == newNode);  //true
    alert(someNode.lastChild == newNode); //true


2、insertBefore()：把节点放在childNodes列表中某个特定的位置上，接受两个参数：要插入的节点和作为参照的节点，如果参照节点是null，则与appendChild()执行效果相同；</br>

    //插入后成为最后一个子节点
    var returnedNode = someNode.insertBefore(newNode,null);
    alert(newNode == someNode.lastChild); //true
    
    //插入后成为第一个子节点
    var returnedNode = someNode.insertBefore(newNode,someNode.firstChild);
    alert(returnedNode == newNode); //true
    alert(newNode == someNode.firstChild); //true


3、replaceChild()：替换节点，接受两个参数：要插入的节点和要替换的节点，要替换的节点将会被这个方法从这个文档树中移除，但被替换的节点还在文档中，只是在文档中已经没有了自己的位置；</br>

    //替换第一个节点
    var returnedNode = someNode.replaceChild(newNode,someNode.firstChild);


4、removeChild()：移除节点，接受参数为要移除的节点；

    //移除第一个子节点
    var returnedNode = someNode.removeChild(someNode.firstChild);
    alert(returnedNode == someNode.firstChild); //true


#### Document类型 ####

JavaScript通过Document类型表示文档，document对象是HTMLDocument的一个实例，HTMLDocument继承自Document类型，在浏览器中document对象表示整个HTML页面，document对象是window对象的一个属性，可以作为全局对象访问。

**查找元素**

getElementById()：接受一个参数:要取得元素id，区分大小写：

    
    <div id="mydiv">Some text</div>
   

取得这个元素
    
    var div = document.getElementById("mydiv");
    
getElementsByTagName()：接受一个参数：要取得的元素标签名：
    
    var images = document.getElementsByTagName("img");
    
    alert(images.length);//输出图像的数量
    
    alert(images[0].src);//输出第一个图像的src特性
    
getElementsByName()：接受一个参数：要取得的元素的name特性：

	<div name="color">Some text1</div>

    <div name="color">Some text2</div>

    <div name="color">Some text3</div>


取得“name=color”的元素：

    var divs = document.getElementsByName("color");


#### Element类型 ####
Element类型可以理解为HTML元素，提供了元素标签名、子节点及特性的访问。

HTML元素常用的特性有:id、title、className

可以使用getAttribute()、setAttribute()和removeAttribute()方法操作元素特性，如：getAttribute("id")、setAttribute("id")、removeAttribute("id")

使用document.createElement()创建元素，接受一个参数：要创建的元素的标签名，如：
    var div = document.createElement("div");

#### Text类型 ####
Text类型表示文本节点，创建文本节点：
    
    var element = document.createElement("div");
    element.className = "message";<br>
    var textNode = document.createTextNode("Hello World");
    element.appendChild(textNode);<br>
    document.body.appendChild(element);


### 二、Dom操作技术 ###
#### 动态脚本 ####
创建动态脚本有两种方法：

1：插入外部JavaScript文件

    <script type-"text/javascript" src="client.js"></script>

相应的DOM代码：

    var script = document.createElement("script");
    script.type = "text/jacascript";
    script.src = "client.js";
    document.body.appendChild(script);

2：直接插入JavaScript代码：
  
    <script type="text/javascript">
     function sayHi(){
    	alert("hi");}
    </script>

#### 动态样式 ####
页面加载完成后动态的将样式加载到页面中：
    
    <link rel="stylesheet" type="text/css" href="styles.css">

相应的DOM代码：

    var link = document.createElement("link");
    link.rel = "stylesheet";
    link.type = "text/css";
    link.href = "style.css";
    var head = document.getElementsByTagName("head")[0];
    head.appendChild(link);

直接插入样式：
    <style type="text/css">
    	body{
    	font-size:14px;
    	}
    </style>

#### **小结** ####

最基本的节点类型：Node，所有其他类型都继承自Node；
Document类型表示整个文档，document对象是Document的一个实例，使用document对象，有很多种方式可以查询和取得节点</br>
Element节点可以用来操作HTML元素或XML元素的内容和特性。
DOM操作是JavaScript程序中开销最大的部分，而访问NodeList导致的问题最多，NodeList对象都是“动态的”，每次访问NodeList对象都会运行一次查询，所以，尽量减少DOM操作。