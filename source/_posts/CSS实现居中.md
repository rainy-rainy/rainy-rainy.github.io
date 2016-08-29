---
title: CSS实现居中
date: 2016-08-28 17:07:32
tags:
---

#### 水平居中
##### inline元素
在块级父元素上设置text-align:center

	.center-children {
	  text-align: center;
	}
	
##### block元素

1. 设置该块级元素的margin-left和margin-right为auto（首先需要设置该元素的宽度，否则将撑满整个屏幕宽度）。：

		.center-me {
		  margin: 0 auto;
		  width: 200px;
		}	
	
2. 将block块display设置为inline-block，在块级父元素上设置text-align:center。
	
		<main class="inline-block-center">
		  <div>
		    I'm an element that is block-like with my siblings and we're centered in a row.
		  </div>
		  <div>
		    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
		  </div>
		  <div>
		    I'm an element that is block-like with my siblings and we're centered in a row.
		  </div>
		</main>

	CSS样式：
	
		.inline-block-center {
		  text-align: center;
		}
		.inline-block-center div {
		  display: inline-block;
		  text-align: left;
		}
	
	

3. 使用弹性盒布局

		<main class="flex-center">
		  <div>
		    I'm an element that is block-like with my siblings and we're centered in a row.
		  </div>
		  <div>
		    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
		  </div>
		  <div>
		    I'm an element that is block-like with my siblings and we're centered in a row.
		  </div>
		</main>
	
	CSS样式：
	
		.flex-center {
		  display: flex;
		  justify-content: center;
		}

#### 垂直居中
垂直居中需要使用一些技巧
##### inline元素、文本

1. 设置padding
	对于行内元素，只要padding-top和padding-bottom相等即可实现居中。

		<main>
		  <a href="#0">We're</a>
		  <a href="#0">Centered</a>
		  <a href="#0">Bits of</a>
		  <a href="#0">Text</a>
		</main>
	
	CSS样式：
	
		main a {
		  padding: 40px 30px;
		}
	
	该方法对于单行或者多行内联元素都行得通，但是如果因为某些原因不能设置padding，要使用不同的方法。

2. 单行文本

	使用一个trick：设置line-height和height相等，就可以垂直居中文本。

		<main>
		  <div>
		    I'm a centered line.
		  </div>
		</main>
	
	CSS样式：

		ain div {
		 height: 100px;
		 line-height: 100px;
		}
		
	
	使用该方法时，一定要确保文本不会换行，否则不是单行将无效果。

3. 多行文本

	使用弹性盒布局

		<div class="flex-center">
		  <p>I'm vertically centered multiple lines of text in a flexbox container.</p>
		</div>

	CSS样式：

		.flex-center {
		  display: flex;
		  flex-direction: column;
		  justify-content: center;
	  	  height: 200px;
		}
	
	记住一定要设置父容器的高度。
	
4. 伪元素

	如果上述方法都不可行，可以使用伪元素来解决，添加伪元素，设置高度为父级容器高度，然后为文本添加vertical-middle: middle.

		<div class="ghost-center">
		  <p>I'm vertically centered multiple lines of text in a container. Centered with a ghost pseudo element</p>
		</div>
		
	CSS样式：
	
		.ghost-center::before {
		  content: " ";
		  display: inline-block;
		  height: 100%;
		  width: 1%;
		  vertical-align: middle;
		}
		.ghost-center p {
		  display: inline-block;
		  vertical-align: middle;
		}
	




##### block元素

1. 通过绝对定位实现居中（该方法同样可用于水平居中）。

	* 高度已知

			.parent {
			  position: relative;
			}
			.child {
			  position: absolute;
			  top: 50%;
			  height: 100px;
			  margin-top: -50px; 
			}
		
	* 高度未知, 利用CSS3变形
			
			.parent {
				position: relative;
			}
			.child {
				position: absolute;
				top: 50%;
				transform: translateY(-50%);
			}
			
2. 使用Flex弹性盒布局

		.parent {
		  display: flex;
		  flex-direction: column;
		  justify-content: center;
		}
		
#### 水平垂直同时居中

1. 绝对定位居中

	* 高度已知
	
			.parent {
			  position: relative;
			}
			
			.child {
			  width: 300px;
			  height: 100px;
			  padding: 20px;
			
			  position: absolute;
			  top: 50%;
			  left: 50%;
			
			  margin: -70px 0 0 -170px;	//注意padding值
			}
			
	
	* 高度未知
	
			.parent {
			  position: relative;
			}
			.child {
			  position: absolute;
			  top: 50%;
			  left: 50%;
			  transform: translate(-50%, -50%);
			}
			
2. 使用Flex弹性盒布局

		.parent {
		  display: flex;
		  justify-content: center;
		  align-items: center;
		}


		
	


	