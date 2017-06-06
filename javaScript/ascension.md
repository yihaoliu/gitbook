# 变量提升与函数提升
>在JavaScript中存在提升的情况，这种提升有两种提升分别是变量提升与函数提升，而且是函数会首先被提升，然后才是变量。
###变量提升
看下面的例子：

```
console.log( a ); //undefined。
var a = 2;
```
上面代码可以写成下面的形式

```
var a;
console.log( a ); //undefined
a=2;
```
再看下面的例子：

```
var a=2;
console.log(a); //2
```
上面的代码可以写成下面的形式：

```
var a;
a=2;
console.log(a) //2
```
###函数提升
看下面的例子：

```
foo();
function foo() {
  console.log( a ); // undefined var a = 2;
}
```
上面的例子可以转化成下面的形式

```
function foo() {
  console.log( a ); // undefined var a = 2;
}
foo();
```
再看下面的例子：
####函数声明会被提升，但是函数表达式却不会被提升
```
foo(); // TypeError
bar(); // ReferenceError
var foo = function bar() {
  ...
};
```
上面的代码可以转化下面的代码
```
var foo;
foo(); // TypeError
bar(); // ReferenceError
foo = function() {
  var bar = foo;
}
```
或者是转化成下面的代码
```
var foo;
foo(); // TypeError
bar(); // ReferenceError
foo = function() {
  var bar = ...self...;
}
```
再看下面的例子
```
foo(); // 3
function foo() {
  console.log( 1 );
}
var foo = function() {
  console.log( 2 );
};
function foo() {
  console.log( 3 );
}
foo(); //2
```
上面的代码可以转化下面的代码
```
var foo;
function foo() {
  console.log( 1 );
}
function foo() {
  console.log( 3 );
}
foo(); // 3
var foo = function() {
  console.log( 2 );
};
foo(); //2
```
#####非常有意思的现象
