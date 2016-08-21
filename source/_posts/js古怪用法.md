---
title: js古怪用法
date: 2016-07-19 20:34:39
tags: javascript
---

学习js已有一段时间，再读[JavaScript 秘密花园](http://www.jb51.net/onlineread/JavaScript-Garden-CN/)。

---

### 对象
#### 对象使用和属性
删除属性：唯一方法是用delete操作符；设置属性为null或者undefined并不能真正的删除属性，而只是移除了属性和值的关联。
#### 原型
js不包含传统的类继承模型，而是使用prototype原型继承模型。

<!-- more -->
  		  		
  		function Foo() {
		    this.value = 42;
		}
		Foo.prototype = {
		    method: function() {}
		};
		
		function Bar() {}
		
		// 设置Bar的prototype属性为Foo的实例对象
		Bar.prototype = new Foo();
		Bar.prototype.foo = 'Hello World';
		
		// 修正Bar.prototype.constructor为Bar本身
		Bar.prototype.constructor = Bar;
		
		var test = new Bar() // 创建Bar的一个新实例
		
		// 原型链
		test [Bar的实例]
		    Bar.prototype [Foo的实例] 
		        { foo: 'Hello World' }
		        Foo.prototype
		            {method: ...};
		            Object.prototype
		                {toString: ... /* etc. */};
		                
上面的例子中，test 对象从 Bar.prototype 和 Foo.prototype 继承下来；因此， 它能访问 Foo 的原型方法 method。同时，它也能够访问那个定义在原型上的 Foo 实例属性 value。 需要注意的是 new Bar() 不会创造出一个新的 Foo 实例，而是 重复使用它原型上的那个实例；因此，所有的 Bar 实例都会共享相同的 value 属性。

属性查找：在查找一个对象的属性时，JS会向上遍历原型链，直到找到为止。如果到达原型链的顶端也就是Object.prototype仍然没有找到指定的属性，就返回undefined。

性能：要警惕原型链过长带来的性能问题：如果一个属性在原型链顶端，则对于查找时间会带来不利影响，特别的，当试图获取一个 不存在的属性时会遍历整个原型链。此外，当使用for...in来循环遍历对象的属性时，原型链上的所有属性都将被访问。

##### hasOwnProperty函数
用来判断一个属性是自定义属性而不是原型链上的属性。hasOwnProperty是js中*唯一*处理属性但是不查找原型链的函数。

		// 修改Object.prototype
		Object.prototype.bar = 1; 
		var foo = {goo: undefined};
		
		foo.bar; // 1
		'bar' in foo; // true
		
		foo.hasOwnProperty('bar'); // false
		foo.hasOwnProperty('goo'); // true
		
使用for...in loop遍历对象属性时推荐使用hasOwnProperty方法来避免原型对象扩展带来的干扰。

#### for in循环
同in操作符一样，for in循环同样在查找对象属性时遍历整个原型链。

		// 修改 Object.prototype
		Object.prototype.bar = 1;
		
		var foo = {moo: 2};
		for(var i in foo) {
		    console.log(i); // 输出两个属性：bar 和 moo
		}
不可能改变for in自身的行为，但是可以用hasOwnProperty来过滤出那些不希望出现在循环体中的属性。

		// foo 变量是上例中的
		for(var i in foo) {
		    if (foo.hasOwnProperty(i)) {
		        console.log(i);
		    }
		}
		
这是唯一正确的写法。比如广泛使用的库Prototype就扩展了原生的js对象（不推荐该种破坏封装的做法）。因此，当使用这种类库时，如果不使用hasOwnProperty过滤的for in就难免会出现问题。

因此推荐总是使用hasOwnProperty，不要对代码的运行环境做任何假设，不要假设原生对象是否被扩展了。

### 函数
#### 函数声明与表达式
函数也是一个对象，因此可以作为参数传递或者作为返回值，一个常见的做法是把匿名函数作为回调函数传递到异步函数中。

有两种定义函数的方式：函数声明和函数表达式。主要区别是函数声明创建的方法会在执行前被解析，因此它存在于当前上下文的任何一个地方，即使在函数定义体的上面调用也是完全正确的。

		//函数声明
		foo(); // 正常运行，因为foo在代码运行前已经被创建
		function foo() {}
上面这段代码没有问题，因为使用了函数声明。下面这段代码会出错：

		//函数赋值表达式
		foo; // 'undefined'
		foo(); // 出错：TypeError
		var foo = function() {};
		
var定义了一个声明语句，对变量foo的解析是在运行之前，因此*foo变量在代码时已经被定义过*了，但是由于*赋值语句只在运行时执行*，因此在相应代码执行之前，foo的值为缺省undefined。

#### 关于this
this的指向有五种情况：

1. 全局范围内：指向全局对象。
2. 函数调用：
		
		foo();
		
	指向全局对象。
3. 方法调用：

		test.foo();
		
	指向test对象。
4. 调用构造函数：

		new Foo();
		
	如果函数和new关键词一起使用，称这个函数为构造函数，惯例首字母大写。
	
	此时的this指向新创建的对象。
5. 显示的设置this：

		function foo(a, b, c) {}
		
		var bar = {};
		foo.apply(bar, [1, 2, 3]); // 数组将会被扩展，如下所示
		foo.call(bar, 1, 2, 3); // 传递到foo的参数是：a = 1, b = 2, c = 3
		
	使用Function.prototype上的apply和call方法，this会被设置为第一个参数。可以认为这call和apply都是借用别人的方法来调用自己。
	
	这两个方法的区别在于传参方式不同，参数明确的时候可以用apply，参数不明确时可以使用apply结合arguments。
	
**-小心误解**：

第二个（直接调用函数时，this指向全局对象）规则被认为是js中的一个错误的设计，因为它从来就没有实际用途。

当使用函数别名时，可能会令人疑惑：

		var test = someObject.methodTest;
		test();
		
上例中将一个对象的方法赋值给test，调用test时就像在调用一个普通的函数，因此此时的this并不指向someObject对象，而是指向全局对象。

		function Foo() {}
		Foo.prototype.method = function() {};
		
		function Bar() {}
		Bar.prototype = Foo.prototype;
		
		new Bar().method();
		
当 method 被调用时，this 将会指向 Bar 的实例对象。

#### 闭包和引用
闭包是js一个非常重要的特性，使得当前作用域可以访问外部作用域中的变量。函数是js中唯一拥有自身作用域的结构，因此闭包的创建依赖于函数。

使用闭包可以模拟私有变量：

		function Counter(start) {
		    var count = start;
		    return {
		        increment: function() {
		            count++;
		        },
		
		        get: function() {
		            return count;
		        }
		    }
		}
		
		var foo = Counter(4);
		foo.increment();
		foo.get(); // 5
		
这里，构造函数Counter返回两个闭包：函数increment和函数get，这两个函数都维持着对外部作用域Counter的引用，因此总可以访问此作用域内定义的变量count。

js中不可以对作用域进行引用或赋值，因此没有办法在外部访问count变量，唯一途径就是通过那两个闭包。

		var foo = new Counter(4);
		foo.hack = function() {
		    count = 1337;
		}; //这里赋值foo.hack无效，因为foo.hack没有定义在当前作用域内，反而会创建或赋值全局count变量
		
**-小心循环中使用闭包**：

一个常见的错误就是在循环中使用闭包：

		for(var i = 0; i < 10; i++) {
		    setTimeout(function() {
		        console.log(i);  
		    }, 1000);
		}
		
上面代码并不会输出0-9，而是输出十次10。

当console.log被调用的时候，匿名函数保持了对外部i的调用，此时for循环已经结束，i的值被修改为了10。为了得到想要的结果，需要在每次循环中创建i的拷贝。

可以使用**匿名包装器**（也就是自执行匿名函数）。

		for(var i = 0; i < 10; i++) {
		    (function(e) {
		        setTimeout(function() {
		            console.log(e);  
		        }, 1000);
		    })(i);
		}
		
外部的匿名函数会立即执行，并把i作为它的参数，此时函数内e变量就拥有了i的拷贝。当传递给 setTimeout的匿名函数执行时，它就拥有了对e的引用，而这个值是不会被循环改变的。

或者从匿名包装器中返回一个函数，这和上面代码效果一样。

		for(var i = 0; i < 10; i++) {
		    setTimeout((function(e) {
		        return function() {
		            console.log(e);
		        }
		    })(i), 1000)
		}
		
#### arguments对象
js中每个函数都能访问一个特别变量：arguments。这个变量维护所有传递到这个函数的参数列表。

arguments变量有length属性，但是它不是一个数组，实际上它是一个对象（Object）。

可以使用for对其循环遍历，但是不能使用标准的数组方法，如push，pop或slice等。为了更好的使用数组方法，可以将其转换为数组。下面的代码会创建一个数组，包含所有arguments对象中的元素：

		Array.prototype.slice.call(arguments);
		
这个转换比较慢。

如果需要将参数从一个函数传递到另一个函数，推荐使用apply：

		function foo() {
		    bar.apply(null, arguments);
		}
		function bar(a, b, c) {
		    // 干活
		}
		
arguments为其内部属性以及函数形式参数创建getter和setter方法，因此改变形参的值会影响arguments的值，反之亦然。

		function foo(a, b, c) {
		    arguments[0] = 2;
		    a; // 2                                                           
		
		    b = 4;
		    arguments[1]; // 4
		
		    var d = c;
		    d = 9;
		    c; // 3
		}
		foo(1, 2, 3);
		
arguments.calle属性指向调用函数，会影响js引擎的性能。

#### 构造函数
*通过new 关键字调用的函数都是构造函数。*

在构造函数内部，this指向新创建的对象，这个新创建对象的prototype被指向到构造函数的prototype。

如果被调用的函数没有显式的return表达式，则*隐式的会返回this对象*——也就是新创建的对象。

		function Foo() {
		    this.bla = 1;
		}
		
		Foo.prototype.test = function() {
		    console.log(this.bla);
		};
		
		var test = new Foo();
		
上面代码把Foo作为构造函数调用，并设置新创建对象的prototype为Foo.prototype。

显式的return表达式将会影响返回结果，但*仅限于返回的是一个对象*。
		
		function Bar() {
		    return 2;
		}
		new Bar(); // 返回新创建的对象，而不是字面值2，因此new Bar().constructor===Bar
		
		function Test() {
		    this.value = 2;
		
		    return {
		        foo: 1
		    };
		}
		new Test(); // 返回创建的对象
		
#### 作用域与命名空间
js不支持块级作用域，只支持函数作用域。

		function test() { // 一个作用域
		    for(var i = 0; i < 10; i++) { // 不是一个作用域
		        // count
		    }
		    console.log(i); // 10
		}
		
js没有显示的命名空间定义，这意味着所有对象都定义在一个全局共享的命名空间下。

每次引用一个变量，js会向上遍历整个作用域直到找到这个变量为止。如果到达全局作用域仍未找到，则会抛出ReferenceError异常。

所有未用var关键字声明的变量都是全局变量。js声明局部变量只有两种方式，一个是作为函数参数，一个是用var关键字。

**-变量声明提升（Hoisting）**

		bar();
		var bar = function() {};
		var someValue = 42;
		
		test();
		function test(data) {
		    if (false) {
		        goo = 1;
		
		    } else {
		        var goo = 2;
		    }
		    for(var i = 0; i < 100; i++) {
		        var e = data[i];
		    }
		}
		
上面代码在运行之前将会被转化。JavaScript 将会把 var 表达式和 function 声明提升到当前作用域的顶部。但赋值语句不会被提升。

		// var 表达式被移动到这里
		var bar, someValue; // 缺省值是 'undefined'
		
		// 函数声明也会提升
		function test(data) {
		    var goo, i, e; // 没有块级作用域，这些变量被移动到函数顶部
		    if (false) {  //if 表达式看起来修改了全局变量 goo，实际上在提升规则被应用后，却是在修改局部变量。
		        goo = 1;
		
		    } else {
		        goo = 2;
		    }
		    for(i = 0; i < 100; i++) {
		        e = data[i];
		    }
		}
		
		bar(); // 出错：TypeError，因为 bar 依然是 'undefined'
		someValue = 42; // 赋值语句不会被提升规则（hoisting）影响
		bar = function() {};
		
		test();
		
如果没有提升规则（hoisting）的知识，下面的代码看起来会抛出异常 ReferenceError。

		// 检查 SomeImportantThing 是否已经被初始化
		if (!SomeImportantThing) {
		    var SomeImportantThing = {};
		}
		
实际上，上面的代码正常运行，因为 var 表达式会被提升到全局作用域的顶部。

		var SomeImportantThing;
		
		// 其它一些代码，可能会初始化 SomeImportantThing，也可能不会
		
		// 检查是否已经被初始化
		if (!SomeImportantThing) {
		    SomeImportantThing = {};
		}
		
下面这段代码执行结果也是由于hoisting的作用

		var myvar = 'my value';  
		
		(function() {  
		    alert(myvar); // undefined  
		    var myvar = 'local value';  
		})(); 
		
**-名称解析顺序**

当访问函数中的foo变量时，js会按照以下顺序查找：

1. 当前作用域内是否有 var foo 的定义。
2. 函数形式参数是否有使用 foo 名称的。
3. 函数自身是否叫做 foo。
4. 回溯到上一级作用域，然后从 #1 重新开始。

**-命名空间**

只有一个全局作用域常会导致命名冲突。在js中，可以通过*匿名包装器（也就是自执行的匿名函数）*来创建命空间。这样不仅可以防止命名冲突，而且有利于程序的模块化。

		(function() {
		    // 函数创建一个命名空间
		
		    window.foo = function() {
		        // 对外公开的函数，创建了闭包
		    };
		
		})(); // 立即执行此匿名函数
		
---
### 数组
#### 数组遍历和属性

js中的数组是对象，但是一般不推荐使用for in循环遍历数组，因为for in循环会枚举原型链上所有属性，这时需要用hasOwnProperty去过滤，因此速度会很慢。

推荐使用经典的for循环去遍历数组并缓存数组的length属性，以达到最佳性能。

		var list = [1, 2, 3, 4, 5, ...... 100000000];
		for(var i = 0, l = list.length; i < l; i++) {
		    console.log(list[i]);
		}
		
length属性的getter方式会获得数组的长度，setter方式有可能截断数组：为length属性设置一个更小的值会截断数组，但是增大length属性不会对数组产生影响。

#### Array构造函数

Array构造函数的参数有点复杂，因此总是推荐使用数组的字面语法[]来构造数组，代码可读性更好。

### 类型

#### 相等于比较

js有两种方式判断两个值是否相等：

1. 等于==

	js是弱类型语言，==操作符会为了比较两个值而进行强制类型转换。
	
	强制类型转换规则较为复杂，可能导致难以跟踪的问题。此外强制类型转换也会带来性能消耗。因此==被认为是一种不好的编程习惯。
	
2. 严格等于===

	严格等于操作符不会进行强制类型转换。
	
	用于比较对象时，比较的不是值是否相等，只有对象的同一个实例才被认为是相等的。
	
		{} === {};                   // false
		new String('foo') === 'foo'; // false
		new Number(10) === 10;       // false
		var foo = {};
		foo === foo;                 // true
		
因此建议总是使用严格等于操作符。如果类型需要转换，应该在比较之前进行显示转换，而不是使用语言本身复杂的强制转换规则。

#### typeof操作符

typeof或许是js最大的一个设计缺陷。可以认为它只有一个功能：用以检测对象是否被定义或赋值。并不能检测对象类型。

		typeof foo !== 'undefined'
		
上面代码会检测对象foo是否定义过，如果没有定义会抛出ReferenceError异常。

强烈建议用Object.prototype.toString检测对象的类型。这是js标准文档给出的唯一一种获取[[Class]]值的方法。

		Object.prototype.toString.call([])    // "[object Array]"
		Object.prototype.toString.call({})    // "[object Object]"
		Object.prototype.toString.call(2)    // "[object Number]"
		
Object.prototype.toString返回一种标准格式字符串，所以上例可以通过slice截取指定位置的字符串

		function is(type, obj) {
		    var clas = Object.prototype.toString.call(obj).slice(8, -1);
		    return obj !== undefined && obj !== null && clas === type;
		}
		
		is('String', 'test'); // true
		is('String', new String('test')); // true
		
#### instanceof操作符

instanceof操作符用来比较两个操作数的构造函数，只有在比较自定义对象的时候才有意义，如果用于比较内置对象，则像typeof操作符一样意义不大。
		
		









		

		
















