# javaScript的arguments, caller, callee

###arguments
arguments:  在函数调用时， 会自动在该函数内部生成一个名为 arguments的隐藏对象。 该对象类似于数组， 但又不是数组。可以使用[]操作符获取函数调用时传递的实参。
```
function testArg()  
{  
    alert("real parameter count: "+arguments.length);  
    for(var i = 0; i < arguments.length; i++)  
    {  
        alert(arguments[i]);  
    }  
}  
```
需要注意的是 argument 保存的实参的信息
arguments 不是一个数组,验证代码
```
(function () {  
  alert(arguments instanceof Array); // false  
  alert(typeof(arguments)); // object  
})();  
```
只有函数被调用时，arguments对象才会创建，未调用时其值为null：
```
alert(new Function().arguments);//return null  
```
arguments 的完整语法如下：
[function.]arguments[n]
参数function ：选项。当前正在执行的 Function 对象的名字。 n ：选项。要传递给 Function 对象的从0开始的参数值索引。
###caller
在一个函数调用另一个函数时，被调用函数会自动生成一个caller属性，指向调用它的函数对象。如果该函数当前未被调用，或并非被其他函数调用，则caller为null。
```
function testCaller() {  
    var caller = testCaller.caller;  
    console.log(caller);  
}  
testCaller();  //null
function aCaller() {  
    testCaller();  
}  

aCaller();  //function aCaller() {testCaller();}
```
###callee
当函数被调用时，它的arguments.callee对象就会指向自身，也就是一个对自己的引用。
由于arguments在函数被调用时才有效，因此arguments.callee在函数未调用时是不存在的（即null.callee），且解引用它会产生异常。
```
function aCallee(arg) {  
  console.log(arguments.callee);  
}  

function aCaller(arg1, arg2) {
  aCallee();
}  

aCaller();  
```
