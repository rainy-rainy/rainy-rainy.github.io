---
title: js中this指向问题
date: 2016-08-17 22:48:05
tags: javascript
---

js中this的指向分为四种情况：

1. 全局范围内或者普通函数调用中，this指向全局对象，在浏览器中即是window对象。 

	    foo();	
	    
2. 调用对象的方法时，this指向该对象。

		obj.foo();
		
3. 用new关键词调用的构造方法中，this指向新构造的对象。

		new Foo();


<!-- more -->

		
4. 通过call或apply函数显示设置this，this被显示设置为函数调用的第一个参数。

		foo.call(obj,arg1,arg2);
    	foo.apply(obj,[arg1,arg2]);
    	
    	
全局代码或者普通函数调用中的this指向全局对象，这其实是js设计的一个缺陷，常会导致误解。例如以下代码：

		Foo.method = function() {
		    function test() {
		        // 这里的this指向全局对象，而非Foo对象
		    }
		    test();
		}
如果确实需要在test中获取对Foo对象的引用，可以创建一个局部变量暂存Foo对象引用：

		Foo.method = function() {
    		var that = this;
		    function test() {
		        // 使用that来指向Foo对象
		    }
		    test();
		}
		
再看下面使用了函数表达式的例子：

	var test = someObject.methodTest;
	test();	//这里的this指向全局对象而非someObject对象
	
为了避免误解，当在确认函数中this中指向的时候，我们应该避免从函数定义处寻找答案，而应**仅**根据函数被调用的方式来确定。也就是说，函数里this的指向**仅由其被调用的方式决定**。

具体来说，当看到以下代码：
	
		obj.foo();
		
就可以确定foo里的this指向obj对象。

而对于如下代码：

		foo();
		
则可以确定foo里的this指向全局对象，而与函数foo的定义方式或者闭包等没有关系。（此处不考虑bind及严格模式）。

this是由被调用方式决定而跟定义方式无关，这使得this可以被改变，增加了编程的灵活性。