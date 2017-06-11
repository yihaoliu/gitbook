# javaScript的this
###this的常见绑定形式
1.默认绑定
2.隐式绑定
3.显示绑定
4.new绑定
>在弄清楚这些绑定之前来弄清楚几个概念调用栈和调用位置

下面我们来看看到底什么是调用栈和调用位置:
```
function baz() {
// 当前调用栈是:baz
// 因此，当前调用位置是全局作用域
         console.log( "baz" );
bar(); // <-- bar 的调用位置 }
function bar() {
// 当前调用栈是 baz -> bar
// 因此，当前调用位置在 baz 中
         console.log( "bar" );
foo(); // <-- foo 的调用位置 }
function foo() {
// 当前调用栈是 baz -> bar -> foo // 因此，当前调用位置在 bar 中
         console.log( "foo" );
     }
baz(); // <-- baz 的调用位置
```
上面的代码让我们初步明白了调用栈，和调用位置

###默认绑定(指绑定全局变量)
思考下面的代码
```
function foo() {
   console.log( this.a );
}
var a = 2;
foo(); // 2
```
上面函数的当前当前调用位置是全局作用域，所以this默认绑定的是window
*如果使用严格模式(strict mode)，那么全局对象将无法使用默认绑定，因此 this 会绑定 到 undefined:*
```
function foo() { "use strict";
         console.log( this.a );
     }
var a = 2;
foo(); // TypeError: this is undefined
```
###隐式绑定
在一些复杂的结构中我们经常会碰到隐式绑定
先来看下面的代码：
```
function foo() {
  console.log( this.a );
}
var obj = {
  a: 2,
  foo: foo
};
obj.foo(); // 2
```
foo函数绑定在了obj下边的foo属性上边，所以他的当前调用位置在obj中所以this指向obj。

*对象属性引用链中只有最顶层或者说最后一层会影响调用位置*
```
function foo() {
   console.log( this.a );
}
var obj2 = {
  a: 42,
  foo: foo
};
var obj1 = {
   a: 2,
   obj2: obj2
};
obj1.obj2.foo(); // 42
```
###隐式丢失
看下面的例子：
```
function foo() {
  console.log( this.a );
}
var obj = {
  a: 2,
  foo: foo
};
var bar = obj.foo; // 函数别名!
var a = "oops, global"; // a 是全局对象的属性 bar(); // "oops, global"
```
是不是结果让人诧异
虽然 bar 是 obj.foo 的一个引用，但是实际上，它引用的是 foo 函数本身，因此此时的 bar() 其实是一个不带任何修饰的函数调用，因此应用了默认绑定。（这是你不知道的JavaScript上中的解释，但是我感觉没有说服力，后面会查明真象）

*以形参传入其实就是隐式赋值*
```
function foo() {
  console.log( this.a );
}
function doFoo(fn) {
  // fn 其实引用的是 foo
  fn();
  // <-- 调用位置!
}
var obj = {
  a: 2,
  foo: foo
};
var a = "oops, global"; // a 是全局对象的属性
doFoo( obj.foo ); // "oops, global"
```

如果把函数传入语言内置的函数而不是传入你自己声明的函数，也会隐式赋值

```
function foo() {
  console.log( this.a );
}
var obj = { a: 2,
foo: foo };
var a = "oops, global"; // a 是全局对象的属性
setTimeout( obj.foo, 100 ); // "oops, global"
```
通过上面的代码可以看出来不做显示绑定还是比较危险的而且隐式绑定还是很危险的

###显式绑定

1. 硬绑定（常用的是call，apply，bind）三种方式的使用

**call**

```
function foo() {
  console.log( this.a );
}
var obj = {
  a:2
};
var bar = function() {
  foo.call( obj );
};
 bar(); // 2
 setTimeout( bar, 100 ); // 2
// 硬绑定的 bar 不可能再修改它的
this bar.call( window ); // 2
```
**apply**

```
function foo(something) {
  console.log( this.a, something );
  return this.a + something;
}
var obj = {
  a:2
};
var bar = function() {
  return foo.apply( obj, arguments );
};
var b = bar( 3 ); // 2 3
console.log( b ); // 5

```
另一种使用方法是创建一个 i 可以重复使用的辅助函数:
```
function foo(something) {
  console.log( this.a, something );
  return this.a + something;
}
// 简单的辅助绑定函数
function bind(fn, obj) {
  return function() {
    return fn.apply( obj, arguments );
  };
}
var obj = {
  a:2
};
var bar = bind( foo, obj );
var b = bar( 3 ); // 2 3
console.log( b ); // 5
```

**bind**

在ES5中内置了bind方法

```
function foo(something) {
   console.log( this.a, something );
   return this.a + something;
}
var obj = {
  a:2
};
var bar = foo.bind( obj );
var b = bar( 3 ); // 2 3
console.log( b ); // 5
```
这里说明一下bind返回的是方法名要想运行样加();比如foo.bind( obj )();这才是运行状态；
(bind(..) 会返回一个硬编码的新函数，它会把参数设置为 this 的上下文并调用原始函数)你不知道的JavaScript中的解释
2. API调用的“上下文”

```
function foo(el) {
  console.log( el, this.id );
}
var obj = {
  id: "awesome"
};
// 调用 foo(..) 时把 this 绑定到 obj
[1, 2, 3].forEach( foo, obj );
// 1 awesome 2 awesome 3 awesome
```

有没有一丝懵逼，看解释
第三方库的许多函数，以及 JavaScript 语言和宿主环境中许多新的内置函数，都提供了一 个可选的参数，通常被称为“上下文”(context)，其作用和 bind(..) 一样，确保你的回调 函数使用指定的 this。这些函数实际上就是通过 call(..) 或者 apply(..) 实现了显式绑定，这样你可以少些一些 代码。

###new绑定

**在 JavaScript 中，构造函数只是一些 使用 new 操作符时被调用的函数。它们并不会属于某个类，也不会实例化一个类。实际上， 它们甚至都不能说是一种特殊的函数类型，它们只是被 new 操作符调用的普通函数而已。**
使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。
1. 创建(或者说构造)一个全新的对象。
2. 这个新对象会被执行[[原型]]连接。
3. 这个新对象会绑定到函数调用的this。
4. 如果函数没有返回其他对象，那么new表达式中的函数调用会自动返回这个新对象。
思考下面的代码:
```
function foo(a) {
  this.a = a;
}
var bar = new foo(2);
console.log( bar.a ); // 2
```
使用 new 来调用 foo(..) 时，我们会构造一个新对象并把它绑定到 foo(..) 调用中的 this 上。new 是最后一种可以影响函数调用时 this 绑定行为的方法，我们称之为 new 绑定。
