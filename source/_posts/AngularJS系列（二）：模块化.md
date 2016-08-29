---
title: AngularJS系列（二）：模块化
date: 2016-07-22
tags: javascript angularjs
---

#### AngularJS的模块化实现

为了避免全局空间污染，AngularJS提供了module

		var helloModule = angular.module('HelloAngular', []);
		helloModule.controller('HelloNgCtrl', ['$scope',
		    function($scope) {
		        $scope.greeting={
		          text: 'hello'
		        };
		    }
		]);

任何一个ng应用都是由控制器、指令、服务、路由、过滤等有限的模块类型构成的。控制器、指令、服务、路由、过滤等分别放在一个模块里，用一个总的app模块作为入口点，它依赖其他所有模块（一般是在app.js中配置依赖模块及路由）。对于AngularJS，**一切都是从模块开始**。

#### 路由

Ajax缺点：
	
* 不会留下历史记录
* 用户无法通过url直接进入某些页面
* 难以seo

一般我们的项目里会有很多视图，AngularJS使用ngRoute进行视图之间的路由，根据访问路径的不同显示不同的view。
		
		bookStoreApp.config(function($routeProvider) {
		    $routeProvider.when('/hello', {  //路径
		        templateUrl: 'tpls/hello.html', //相应的视图
		        controller: 'HelloCtrl' //对应的控制器
		    }).when('/list:bookId',{	//路径里传参bookId
		    	templateUrl:'tpls/bookList.html',
		    	controller:'BookListCtrl'
		    }).otherwise({	 //其他路径
		        redirectTo: '/hello'
	         		    })
		});

例如url为**#hello 时展现第一个视图。用#告诉浏览器在页面内进行跳转，不向后台请求。

AngularJS为我们封装好的路由工具ngRoute靠url改变去驱动视图。只能路由一个view。

除此之外，angularUI也为我们封装了一个独立的路由模块[ui-router](https://ui-router.github.io/)，它靠状态state 来驱动视图。可以定义多个view，且可以嵌套。





#### 依赖注入

与Spring类似的理念，AngularJS在启动时会检测模块是否加载。
AngularJS中自带的模块以ng开头。
		
		var bookStoreApp = angular.module('bookStoreApp', [
		    'ngRoute', 'ngAnimate', 'bookStoreCtrls', 'bookStoreFilters',
		    'bookStoreServices', 'bookStoreDirectives'
		]);

#### AngularJS本身没有实现AMD(异步模块加载)

AngularJS本身没有实现异步加载器。




  