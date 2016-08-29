---
title: AngularJS系列（三）：双向数据绑定
date: 2016-07-22
tags:
---

双向数据绑定是AngularJS中一个非常吸引人的特性，极大方便了form表单的交互实现。顾名思义，双向数据绑定存在从数据模型和视图之间的双向绑定：

* 控制器修改数据模型后，数据模型的变更会反映到视图上
* 视图上发生了数据的变化，能够自动同步到模型上

#### 数据绑定：取值表达式、ng-bind、ng-model

		{{ greeting.text }}
		
这是一种单向绑定，将数据模型绑定到视图上。缺点是刷新或者网络条件不太好时可能会显示原始代码，常称初始化闪烁。

可以用ng-bind指令取代双括号的取值表达式来绑定数据，能够避免出现闪烁。
	
		<p ng-bind='greeting.text'></p>

一般在首页index里使用ng-bind绑定，因为一般是在这个页面加载库。

使用ng-model就可以实现双向的数据绑定：

		<input type='username' ng-model='userInfo.username'>

#### 利用数据绑定动态控制样式：ng-class、ng-show、ng-hide
		
利用数据绑定可以修改样式：
		
		<p class='text-{{ color }}'></p>
		
利用ng-class可以实现同样的效果：

		<p ng-class='{error: isError, warning: isWarning}'></p>
		
利用ng-show、ng-hide是两个相反的应用：

		<p ng-show='pState.Show'></p>		
		



