---
title: AngularJS系列（四）：指令
date: 2016-08-22
tags:
---

指令是AngularJS的四大特性中最复杂的一个特性。

#### 最简单的指令hello

使用AngularJS时应避免在Controller等中操作DOM，不符合设计思想。如果要操作DOM，可以在指令中进行。下面是一个最简单的指令hello使用方式。
	
HTML：

		<hello></hello>
		
JS：
		
		var myModule=angular.module("myModule",[]);
	    myModule.directive("hello",function(){
	      	return {
	        	restrict: 'AE',
	        	template: '<div>Hi everyone!</div>',
	        	replace: true
	      	}
	    });

##### restrict
配置项restrict表示匹配模式，一共有四个属性“AEMC” ，A:attribute, E:element, M:comment, C:class。下面是这四种匹配模式对应的元素写法，推荐使用前两种方式，具体来说当使用现有标签时用attribute模式，新建标签使用element模式。本例使用第一种element匹配模式。

	  <hello></hello>
	  <div hello></div>
	  <!-- directive:hello -->		<!-- 注意中间的空格 -->
	  <div></div>
	  <div class="hello"></div>	  
	  
##### template
配置项template表示最终要显示出来的html标签，显然采用这种在js中编写模板的方式在编写和修改方面都不是很方便，AngularJS给我们提供了另外一个配置项templateUrl，采用这个配置项切出独立的html代码，就不用将html代码写到js中。

		templateUrl: 'hello.html'
		
除此之外，AngularJS还提供了templateCache配置项，当我们编写的模板不仅仅在hello指令中使用，还需要在其他地方使用，可以使用templateCache将模板缓存起来，让多个指令使用。

##### 嵌套：replace & transclude
replace：

		var myModule=angular.module("myModule",[]);
	    myModule.directive("hello",function(){
	      	return {
	        	restrict: 'AE',
	        	replace: true,
	        	template: '<div>Hi everyone!</div>',
	      	}
	    });
			
使用replace时，下面代码中嵌套的内容将不会显示出来

		<hello>
			<div>这是嵌套的内容！</div>
		</hello>
		
如果需要嵌套，要使用transclude配置项：	  
			    
transclude:
	
		var myModule=angular.module("myModule",[]);
	    myModule.directive("hello",function(){
	      	return {
	        	restrict: 'AE',
	        	transclude: true,
	        	template: '<div>Hi everyone!<div ng-transclude>这是嵌套内容！</div></div>'
	      	}
	    });
		
		

#### 指令运行：compile & link

指令运行需要三个阶段：加载——编译——链接

		var myModule=angular.module("myModule",[]);
	    myModule.directive("hello",function(){
	      	return {
	        	restrict: 'AE',
	        	replace: true,
	        	template: '<div>Hi everyone!</div>',
	        	compile: function(){
	        	}
	        	link: function(){
	        	}
	      	}
	    });

1. 加载
	* 加载angular.js，找到ng-app指令，确定应用边界
2. 编译
	* 遍历DOM，找到所有指令
	* 根据指令代码中的directive、replace、transclude转换DOM结构
	* 存在自定义compile函数则调用(调用自定义compile时还要调用内置compile，否则会被覆盖，因此一般不会自定义compile函数)
3. 链接
	* 对每条指令运行自定义link函数 
	* link函数一般用来操作DOM、绑定事件监听器

#### 指令和控制器之间的交互

ng-controller

#### 指令和指令之间的交互

#### scope类型和独立scope

#### 内置指令

#### 第三方指令库：angular-ui




