# promise原理解析
#####promise模式

这几种方式的可维护性逐级递增，理解难度也逐级递增。这篇总结也是针对promise模式的。

promise模式那么多中异步编程的方式，为什么选择promise, 因为前面几种方式不够灵活，用起来不够痛快，不优雅。为了降低异步编程的复杂性，所以promise。

promise的核心是有一个promise对象，这个对象有一个重要的then()方法, 它用于指定回调函数，如：
f1().then(f2);

promise模式在任何时刻都有三种状态：已完成（resolved），未完成（unfulfilled），那么then()方法就是为状态变化指定不同的回调函数，并总是返回一个promise对象，方便链式调用。

那promise模式下，返回的数据如何在各个回调函数之间传播呢，通过resolve方法，你可以将一个函数的返回值作为参数传递给另一个函数，并且将另一个函数的返回值作为参数再传递给下一个函数……像一条“链”一样无限的这么做下去。

#####代码如下：

```
//constructor
var promise = function() {
	this.callbacks = [];
}
promise.prototype = {
  construct: promise,
	//成功的操作
	resolve: function(result) {
		this.complete('resolve', result);
	},
	//失败
	reject: function(result) {
		this.complete("reject", result);
	},
	//执行回调
	complete: function(type, result) {
		while(this.callbacks[0]) {
			this.callbacks.shift()[type](result);
		}
	},
	then: function(successHandler, failedHandler) {
		this.callbacks.push({
			resolve: successHandler,
			reject: failedHandler
		});
		return this;
	}
}

//运行下边是方法的调用
var pro = new promise();
var delay1 = function() {
	setTimeout(function() {
		pro.resolve('数据1');
	}, 1000);
	return pro;
};
var callback1 = function(re) {
	re = re + '数据2';
	console.log(re);
};


delay1().then(callback1)
console.log("我是最后吗");
 /*
 *以上方法输出的结果分别是
 *我是最后吗
 *数据1
 *数据2
 */
```
#####代码分析
我们可看到一个简单的promise对象的构造函数的结构：

	callbacks: 用于管理回调函数
	resolve: 请求成功时执行的方法
	reject:请求失败时执行的方法
	complete: 执行回调
	then：绑定回调函数

#####第一步
```
var delay1 = function() {
	setTimeout(function() {
		promise.resolve(‘数据1’);
	}, 1000);
	return promise;
};
//这个函数通过setTimeout方法， 异步传递一个数据1， 并返回一个promise对象(必须)。
```

#####第二步
```
var callback1 = function(re) {
	re = re + '数据2';
	console.log(re);
	promise.resolve(re);
};
//callback1和callback2都是要通过then方法注册的回调函数， 其中callback1通过resolve方法把数据往下传递。
```

#####第三步
```
delay1().then(callback1).then(callback2);
//delay1() 方法执行完， 因为返回了一个promise对象， 所以可以再调用then() 方法为delay1() 的setTimeout异步执行操作指定回调函数, 又因为then() 方法也返回的是promise对象， 所以还可以再调用then方法
```

#####第四步
```
setTimeout(function() {
	promise.resolve('数据1');
}, 1000);
//一秒之后， 当其他代码执行完成， 开始执行异步代码promise.resolve(‘数据1’);，
//这里调用promise的resolve() 方法， 指定了一个成功状态， 并把数据1作为参数。
```

#####第五步
```
resolve: function(result) {
  this.complete("resolve", result);
},
```

#####第六步：循环执行回调，将上一个回调的结果传递给下一个回调
```
complete: function(type, result) {
	while(this.callbacks[0]) {
		this.callbacks.shift()[type](result);
	}
},
```
