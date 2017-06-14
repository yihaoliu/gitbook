# javaScript的this
###this的常见绑定形式
1.默认绑定
2.隐式绑定
3.显示绑定
4.new绑定

>判断this 现在我们可以根据优先级来判断函数在某个调用位置应用的是哪条规则。可以按照下面的顺序来进行判断:
1. 函数是否在new中调用(new绑定)?如果是的话this绑定的是新创建的对象。
     var bar = new foo()
2. 函数是否通过call、apply(显式绑定)或者硬绑定调用?如果是的话，this绑定的是 指定的对象。
     var bar = foo.call(obj2)
3. 函数是否在某个上下文对象中调用(隐式绑定)?如果是的话，this 绑定的是那个上 下文对象。
     var bar = obj1.foo()
4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到undefined，否则绑定到 全局对象。
     var bar = foo()

关于call、apply、bind的区别：

|函数|参数|返回|

|:-----:|:-----:|:-----:|

|call|第一个参数是context,后边可传多个参数|立即调用|

|apply|第一个参数是context,后边是一个数组|立即调用|

|bind|第一个参数是context,后边可传多个参数|返回对应函数|

在弄清楚这些绑定之前来弄清楚几个概念调用栈和调用位置
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
###优先级
1.先比较一下隐式绑定和显示绑定

看下面代码

```
function foo() {
  console.log( this.a );
}
var obj1 = {
  a: 2,
  foo: foo
};
var obj2 = {
  a: 3,
  foo: foo
};
obj1.foo(); // 2
obj2.foo(); // 3

obj1.foo.call( obj2 ); // 3
obj2.foo.call( obj1 ); // 2
```
可以明显的看到显示绑定比隐式绑定的优先级高

2.现在比较一下new 绑定和隐式绑定
看如下代码
```
function foo(something) {
  this.a = something;
}
var obj1 = {
  foo: foo
};
var obj2 = {};
obj1.foo( 2 );
console.log( obj1.a ); // 2
obj1.foo.call( obj2, 3 );
console.log( obj2.a ); // 3
var bar = new obj1.foo( 4 );
console.log( obj1.a ); // 2
console.log( bar.a ); // 4
```
可以看到 new 绑定比隐式绑定优先级高
3.new 绑定和显式绑定
在看代码之前先回忆一下硬绑定是如何工作的。Function.prototype.bind(..) 会创建一个 新的包装函数，这个函数会忽略它当前的 this 绑定(无论绑定的对象是什么)，并把我们 提供的对象绑定到 this 上。
看下面的代码
```
function foo(something) { this.a = something;
}
var obj1 = {};
var bar = foo.bind( obj1 );
bar( 2 );
console.log( obj1.a ); // 2
var baz = new bar(3);
console.log( obj1.a ); // 2
console.log( baz.a ); // 3
```
出乎意料! bar 被硬绑定到 obj1 上，但是 new
修改为 3。相反，new 修改了硬绑定(到 obj1 的)调用 bar(..) 中的 this。因为使用了 new 绑定，我们得到了一个名字为 baz 的新对象，并且 baz.a 的值是 3。
ES5 中内置的 Function.prototype.bind(..) 更加复杂。下面是 MDN 提供的一种
bind(..) 实现
```
if (!Function.prototype.bind) {
  Function.prototype.bind = function(oThis) {
      if (typeof this !== "function") {
        // 与 ECMAScript 5 最接近的
        // 内部 IsCallable 函数
          throw new TypeError("Function.prototype.bind - what is trying " +"to be bound is not callable");
      }
      var aArgs = Array.prototype.slice.call( arguments, 1 ),
          fToBind = this,
          fNOP = function(){},
          fBound = function(){
            return fToBind.apply(
              (
                this instanceof fNOP &&
                oThis ? this : oThis
              ),
             aArgs.concat(
               Array.prototype.slice.call( arguments )
             );
          };
      fNOP.prototype = this.prototype;
      fBound.prototype = new fNOP();
      return fBound;
  };
}
```
>这种 bind(..) 是一种 polyfill 代码(polyfill 就是我们常说的刮墙用的腻 子，polyfill 代码主要用于旧浏览器的兼容，比如说在旧的浏览器中并没 有内置 bind 函数，因此可以使用 polyfill 代码在旧浏览器中实现新的功 能)，对于 new 使用的硬绑定函数来说，这段 polyfill 代码和 ES5 内置的 bind(..) 函数并不完全相同(后面会介绍为什么要在 new 中使用硬绑定函 数)。由于 polyfill 并不是内置函数，所以无法创建一个不包含 .prototype 的函数，因此会具有一些副作用。如果你要在 new 中使用硬绑定函数并且依 赖 polyfill 代码的话，一定要非常小心。

###绑定例外
####1.把 null 或者 undefined 作为 this 的绑定对象传入 call、apply 或者 bind
看下面代码
```
function foo() {
  console.log( this.a );
}
var a = 2;
foo.call( null ); // 2
```
应用场景
使用 apply(..) 来“展开”一个数组
```
function foo(a,b) {
  console.log( "a:" + a + ", b:" + b );
}
// 把数组“展开”成参数
foo.apply( null, [2, 3] ); // a:2, b:3
// 使用 bind(..) 进行柯里化
var bar = foo.bind( null, 2 );
bar( 3 ); // a:2, b:3
```
####2.间接引用
另一个需要注意的是，你有可能(有意或者无意地)创建一个函数的“间接引用”，在这 种情况下，调用这个函数会应用默认绑定规则。

间接引用最容易在赋值时发生:
```
function foo() {
  console.log( this.a );
}
var a = 2;
var o = {
          a: 3,
          foo: foo
        };
var p = { a: 4 };
o.foo(); // 3
(p.foo = o.foo)(); // 2
```
赋值表达式 p.foo = o.foo 的返回值是目标函数的引用，因此调用位置是 foo() 而不是 p.foo() 或者 o.foo()。根据我们之前说过的，这里会应用默认绑定。
注意:对于默认绑定来说，决定 this 绑定对象的并不是调用位置是否处于严格模式，而是 函数体是否处于严格模式。如果函数体处于严格模式，this 会被绑定到 undefined，否则 this 会被绑定到全局对象。

####3.软绑定
之前我们已经看到过，硬绑定这种方式可以把 this 强制绑定到指定的对象(除了使用 new 时)，防止函数调用应用默认绑定规则。问题在于，硬绑定会大大降低函数的灵活性，使 用硬绑定之后就无法使用隐式绑定或者显式绑定来修改 this。
如果可以给默认绑定指定一个全局对象和 undefined 以外的值，那就可以实现和硬绑定相 同的效果，同时保留隐式绑定或者显式绑定修改 this 的能力。
可以通过一种被称为软绑定的方法来实现我们想要的效果:
```
if (!Function.prototype.softBind) {
  Function.prototype.softBind = function(obj) {
      var fn = this;
      // 捕获所有 curried 参数
      var curried = [].slice.call( arguments, 1 );
      var bound = function() {
          return fn.apply(
          (!this || this === (window || global)) ? obj : this;
          curried.concat.apply( curried, arguments );
      };
      bound.prototype = Object.create( fn.prototype );
      return bound;
  };
}
除了软绑定之外，softBind(..) 的其他原理和 ES5 内置的 bind(..) 类似。它会对指定的函 数进行封装，首先检查调用时的 this，如果 this 绑定到全局对象或者 undefined，那就把 指定的默认对象 obj 绑定到 this，否则不会修改 this。此外，这段代码还支持可选的柯里化
```
下面我们看看 softBind 是否实现了软绑定功能:
```
function foo() {
  console.log("name: " + this.name);
}
var obj = { name: "obj" },
    obj2 = { name: "obj2" },
    obj3 = { name: "obj3" };
var fooOBJ = foo.softBind( obj );
    fooOBJ(); // name: obj
    obj2.foo = foo.softBind(obj);
    obj2.foo(); // name: obj2 <---- 看!!!
    fooOBJ.call( obj3 ); // name: obj3 <---- 看!
    setTimeout( obj2.foo, 10 );
    // name: obj <---- 应用了软绑定
```
可以看到，软绑定版本的 foo() 可以手动将 this 绑定到 obj2 或者 obj3 上，但如果应用默
认绑定，则会将 this 绑定到 obj。
###this词法
**（箭头函数没有执行上下为this指向父级）箭头函数不使用 this 的四种标准规则，而是根据外层(函数或者全局)作用域来决定 this**
看下面的代码
```
function foo() {
// 返回一个箭头函数
  return (a) => {
  //this 继承自 foo()
    console.log( this.a );
  };
}
var obj1 = {
  a:2
};
var obj2 = {
  a:3
};
var bar = foo.call( obj1 );
bar.call( obj2 ); // 2, 不是 3 !
```
箭头函数最常用于回调函数中，例如事件处理器或者定时器:
```
function foo() {
  setTimeout(() => {
    // 这里的 this 在此法上继承自 foo()
    console.log( this.a );
  },100);
}
var obj = {
      a:2
     };
foo.call( obj ); // 2

```
上面代码不用箭头函数的写法
```
function foo() {
var self = this; // lexical capture of this
  setTimeout( function(){
     console.log( self.a );
  }, 100 );
}
var obj = {
  a: 2
};
foo.call( obj ); // 2
```
