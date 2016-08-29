---
title: AngularJS系列（一）：MVC
date: 2016-07-22
tags: javascript angularjs
---

AngularJS是一个前端MVC框架，采用MVC有以下几个目的：

1. 切分职责
2. 复用
3. 便于后期维护

总的来说，MVC是一种手段，主要是为了实现模块化和复用。

抛开AngularJS，前端MVC框架都会遭遇以下困难：；

1. 浏览器首先加载脚本，之后再JIT编译执行。因此必须等待整个页面加载完成才能操作DOM
2. js没有提供机制来解决多个文件之间的依赖问题，需要自己解决
3. js的原型继承也为前端编程带来很多困难

AngularJS实现MVC有以下几种方式：

![image](source/images/2016-07-22-1.png)

一个控制器控制多个视图，适用于小型项目

![image](source/images/2016-07-22-2.png)

一个控制器只负责一个视图

如果控制器1和控制器2实现相同的内容，可以将公共部分作为一个服务service，不要采用继承的方式：

![image](source/images/2016-07-22-3.png)
![image](source/images/2016-07-22-4.png)

        <!doctype html>
        <html ng-app>
        <head>
            <meta charset="utf-8">
        </head>
        <body>
            <div ng-controller="HelloAngular">
			  <input ng-model="greeting.text" />
            <p>{{greeting.text}},Angular</p>
            </div>
        </body>
        <script src="js/angular-1.3.0.js"></script>
        <script src="HelloAngular_MVC.js"></script>    
        </html>
        
ng-controller赋值的HelloAngular函数就是Controller。View是页面上我们看到的东西，本例中就是<p>标签。<p>标签中内容是数据模型Model。

##### Controller

使用Controller要注意以下几点：

1. 不要试图复制Controller,一个Controller一般只负责一小块视图
2. 不要在Controller里操作DOM
3. 不要在Controller里做数据格式化，ng有很好用的表单控件
4. 不要在Controller里做数据过滤操作，ng有$filter服务
5. 应尽量避免Controller之间的互相调用，因为这是一种强耦合，Controller之间的交互通过事件进行

##### Model

通过ng-model指令来绑定

		<input ng-model="greeting.text" />
		<p>{{greeting.text}},Angular</p>
		
		
##### View

##### $scope

AngularJS的MVC是通过$scope来实现的。$scope是MVC和实现双向数据绑定的基础

1. $scope是表达式的执行环境（作用域）
2. $scope是一个树形结构，与DOM结构平行，通过这种结构可以实现子标签继承父标签的属性和方法
3. 提供了一些方法$watch()/$apply()对数据进行监控
4. 每一个angular应用只有一个根$scope对象，一般位于ng-app上
5. $scope可以向上或向下传播事件













#### 
