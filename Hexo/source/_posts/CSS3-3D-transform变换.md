---
title: CSS3 3D transform变换
date: 2017-08-15 13:34:29
categories: 
tags:
	- css
---
 在学习慕课网H5+JS+CSS3实现圣诞情缘的时候遇到css3的3D转换，虽然也学习过css3的3d变化教程，但都是基于理论上，没有自己亲自应用过，所以现在使用的时候就有点吃力。今天就重新学习了一遍css3的3D变换。


### 一、rotateX, rotateY, rotateZ ###

先介绍rotateX(), rotateY(), rotateZ()这三个方法，在介绍这三个方法之前我们先来一张图：

<!-- more -->
![](http://i.imgur.com/mhm6zD7.png)

这是3d转换的基本图例，标志出了实物中x、y、z轴的分布，如果实在看不懂的画，可以找来一张纸，在纸上面横竖各放一只笔，横着的代表x轴，竖着代表y轴，最后立着放一只，代表z轴，这样就比较形象了。

现在就来看看rotateX(), rotateY(), rotateZ()这三个方法:
rotateX():图像绕着X轴旋转；rotateY()：图像绕着Y轴旋转；rotateZ()：图像绕着Z轴旋转；实际的效果图如下:

![](http://i.imgur.com/DxLH8yA.gif)


### 二、perspective属性 ###

perspective直译成中文就是透视，也可以理解为视角，它的透视方式为近大远小，举个例子，在生活中，我们站在很远的地方看物体，这时的物体近似为一个点，但我们走近一点看的时候，物体棱角分明很立体长宽高都看的清清楚楚。

perspective属性设置镜头到元素平面的距离，这个镜头可以认为就是我们的眼睛，css3的3D transform的视点是在浏览器的前方，近似为我们眼睛所在的地方。举个例子，一个1680像素宽的显示器中有张美女图片，perspective值为2000像素，浏览器展示出来的效果就和你看距离你2000像素那么远的美女的效果是一样的。

设置perspective属性有两种方法，一种是用在动画元素的父辈元素上即舞台元素，如：

<pre>
.stage{
   perspective:300px;
}
</pre>

另一种就是写在当前的动画元素上,与transform的其他属性写在一起：
<pre>
.curAnim{
   transform: perspective(300px) rotateX(90deg);
}
</pre>

他们在展示一个元素的时候效果是一样的，但舞台上元素多起来的时候，效果就不一样了，更详细的demo展示，参考张鑫旭的博文[好吧，CSS3 3D transform变换，不过如此！](http://www.zhangxinxu.com/wordpress/2012/09/css3-3d-transform-perspective-animate-transition/)


### 三、translateZ属性 ###

translateZ属性是展示了近大远小的原理，translateZ的值越大，可以理解为元素距离我们的眼睛越近，看到的元素也越大，translateZ的值越小，元素距离我们就越远，看到的也就越小，如设置

<pre>
perspective:300px;
</pre>

当translateZ=300px时，元素会撑满整个页面，translateZ=-300px时，我们看到的元素就很小。


### 四、perspective-origin属性 ###

perspective-origin属性设置的是你观看的位置，引用[Mozilla 开发者网络（MDN）的描述](https://developer.mozilla.org/en-US/docs/Web/CSS/perspective-origin)：

> The perspective-origin CSS property determines the position at which the viewer is looking. 

其默认值为perspective-origin:50% 50%;

可以通过实例来感受一下：[https://www.w3cschool.cn/tryrun/showhtml/trycss3_perspective-origin1](https://www.w3cschool.cn/tryrun/showhtml/trycss3_perspective-origin1)


### 五、transform-style: preserve-3d ###

transform-style属性是决定其子元素上用3d效果展示还是以2d平面效果展示，下面是来自[Mozilla 开发者网络（MDN）的描述](https://developer.mozilla.org/en-US/docs/Web/CSS/transform-style)：

> The transform-style CSS property determines if the children of the element are positioned in the 3D-space or are flattened in the plane of the element.

perspective-style有两个参数flat|preserve-3d，flat是设置为平面展示没有3d效果，而preserve-3d则是以3d效果展示；

perspective-style一般是声明在3D变换的兄弟元素们的父元素上，也就是舞台元素。


### 六、backface-visibility ###

backface-visibility属性设置的是我们能否看到元素的背面，可设置的值有两个：visible | hidden：

backface-visibility : visible 效果图：

![](http://i.imgur.com/JZylKXh.png)

backface-visibility : hidden 效果图：

![](http://i.imgur.com/knyXZYV.png)


### 七、总结 ###

这些属性虽然理解起来并不是很难，但如果没有用到实际中去也会很快的遗忘，重在实践。

给自己定个小目标，每天进步一点点！


### 参考文章： ###

张鑫旭的博文：[好吧，CSS3 3D transform变换，不过如此！](http://www.zhangxinxu.com/wordpress/?p=2592)

Mozilla 开发者网络（MDN）：[CSS Transforms](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transforms)