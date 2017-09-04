---
title: websocket实现简单聊天程序
date: 2017-08-20 17:06:17
categories: 
tags:
	- websocket 
	- node.js
---


程序的流程图：
<!-- more -->

![](http://i.imgur.com/xNJdw2f.png)


## **主要代码：** ##

### 服务端 app.js ###

先加载所需要的通信模块：

	var express = require('express');
	var app = express();
	var http = require('http').createServer(app);
	var io = require('socket.io').listen(http);
	var fs = require('fs');

创建用户列表和消息列表：

	var person = [];
	var history = [];


绑定并监听80端口：

	app.get('/',function (req,res) {
	    res.sendFile(__dirname + '/login.html');
	});
	
	http.listen(80,function () {
	    console.log('listening on *:80');
	});

客户端连接成功后，触发响应事件connection，完成要绑定的事件并实现客户端出发的事件：

	io.sockets.on('connection',function (socket) {
	    var user = '';
	
	    socket.emit('history', history);
	    io.sockets.emit('updatePerson', person); 
	
	    socket.on('sendMsg', function (data) {
	        var obj = {};
	        obj.content = data;
	        obj.time = now();
	        obj.name = user;
	        if (history.length === history_num) {
	            history.shift();
	        }
	        history.push(obj);
	        io.sockets.emit('news', obj);
	    });
	
	    socket.on('setUserName', function (data) {
	        user = data;
	        person.push(user);
	        io.sockets.emit('loginsucess');
	        io.sockets.emit('updatePerson', person);
	        io.sockets.emit('news', {content: user + '进入房间', time: now(), name: '系统消息'});
	    });
	
	    socket.on('disconnect', function () {
	        if (user !== '') {
	            person.forEach(function (value, index) {
	                if (value === user) {
	                    person.splice(index, 1);
	                }
	            });
	            io.sockets.emit('news', {content: user + '离开房间', time: now(), name: '系统消息'});
	            io.sockets.emit('updatePerson', person);
	        }
	    });
	});

### 客户端 index.js： ###

先初始化用户信息：

	 $scope.data = [];     //消息队列  
	 $scope.name = '';    //用户名
	 $scope.content = '';  //用户输入的消息
	 $scope.personlist = []; //用户队列

然后连接服务器端：

	const  socket_url = 'http://localhost';
	var socket = io.connect(socket_url);
	
连接成功后，对用户昵称输入的验证：

	$scope.checkName = function () {
	    if($scope.name!==''){
	        if($scope.personlist.length!==0){
	            if($scope.personlist.indexOf($scope.name)>-1) {
	                document.getElementById("info").textContent = "该昵称已被占用，请重新输入";
	            }
	            else{
	                socket.emit('setUserName', $scope.name);
	            }
	        }
	        else{
	            socket.emit('setUserName', $scope.name);
	        }
	    }
	    else{
	        document.getElementById('name').focus();
	    }
	};

验证成功后，对用户输入消息要触发的事件：

	$scope.sendMsg = function(data){
	    var date = new Date();
	    data = $scope.content;
	    if($scope.content !== ''){
	        socket.emit('sendMsg',data);
	    }
	    $scope.content = '';
	};

#### 程序的部分运行测试结果： ####

浏览器输入localhost后展示的用户登录界面:

![](http://i.imgur.com/puk3YKy.png)

昵称重复后的提示：

![](http://i.imgur.com/nwZmF3S.png)

昵称输入成功后进入当前用户的聊天界面：

![](http://i.imgur.com/92hHDKg.png)

源码地址：[GitHub](https://github.com/FanWalker/webchat)

参考学习：

Node.js + Web Socket 打造即时聊天程序嗨聊：[http://www.cnblogs.com/Wayou/p/hichat_built_with_nodejs_socket.html](http://www.cnblogs.com/Wayou/p/hichat_built_with_nodejs_socket.html)

基于websocket的一个简单的聊天室：[https://github.com/ShanaMaid/websocket-express-webchat](https://github.com/ShanaMaid/websocket-express-webchat)
