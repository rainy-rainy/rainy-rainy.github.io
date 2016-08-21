---
title: ES6-Promise对象初探
date: 2016-08-16 22:12:53
tags: javascript
---

ES6标准中的Promise是异步编程的一种解决方案，除此之外还有回调函数，事件监听及发布/订阅。Promise对象保存未来事件（通常是一个异步操作）的结果，提供了API对各种异步操作做同样的处理。

Promise对象有以下几个状态：

1. Pending：进行中
2. Resolved：又称Fulfilled，已完成
3. Rejected：已失败

Promise对象有以下特点：

1. 对象的状态不受外界影响。只有异步操作的结果决定了当前的状态，其他任何操作都无法改变。这也是Promise名字的由来，意即“承诺”。
2. 一旦状态改变，就不会再变，且任何时候都可以得到这个结果。具体来说，Promise对象的状态改变只有两种情况：从Pending到Resolved，或者从Pending到Rejected，只要这两种情况之一发生了，状态就会一直保持。且如果一个任务完成（状态改变），再添加回调函数，该回调函数会立即执行。这与事件不同，如果在触发事件之后监听将没有效果。

<!-- more -->

有了Promise对象，就可以将异步操作以同步的流程表达出来，避免了层层嵌套的回调函数。且统一API使得对异步操作的控制更加方便。

下面是一种简单的使用方式：

        var promise = new Promise(function(resolve, reject) {
          setTimeout(function(){
            resolve('val')
          });
        });
        
        promise.then(onFulfilled,onRejected).catch(onRejected)

Promise.prototype.then(onFulfilled，onRejected)为Promise实例添加状态改变时的回调函数，第二个参数是可选的。


Promise.prototype.catch(onRejected)指定异步操作发生错误时的回调函数，此外，then方法指定的回调函数运行时抛出的错误也会触发该回调函数。

