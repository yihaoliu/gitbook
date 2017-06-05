# 你不知道的js（上）
###LHS与RHS
>LHS可以理解为某变量被赋值，RHS可以理解为被调用

#####举个栗子
```
function foo(a){
  console.log(a); //2
}
foo(2)
```
>foo进行RHS  foo的形参相当于a=2  所以a进行LHS  console是RHS  console里的a是RHS


###理解作用于
#####作用于嵌套

>现在当前作用于找某变量，找不到时引擎会向外层嵌套的作用于中继续寻找，直到找到该变量，或抵达最外层的作用域（也就是全局作用于）为止。

#####注意一下代码
```
function foo (a){
  console.log(a + b);  //4
}
var b = 2;
foo(2);
```
----
```

function foo (a){
  console.log(a + b);  //NaN
}
foo(2);
var b = 2;
```
----
```
function foo (a){
  console.log(a + b); //NaN
  var b = 2
}
foo(2);
```
----
```
function foo (a){
  console.log(a + b); //ReferenceError: b is not defined
  b = 2
}
foo(2);
```
