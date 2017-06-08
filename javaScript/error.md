# javaScript各种错误类型

####1.SyntaxError（语法错误）

解析代码时发生的语法错误
```
eg:var 1a;

　　Uncaught SyntaxError: Unexpected number
```
####2.ReferenceError（引用错误）

a.引用了一个不存在的变量
```
eg: console.log(a);

　　Uncaught ReferenceError: a is not defined
```
b.将变量赋值给一个无法被赋值的对象
```
eg:console.log()= 1;

　　Uncaught ReferenceError: Invalid left-hand side in assignment
```


####3.RangeError（范围错误）

超出有效范围
```
eg:var a= new Array(-1);

　　Uncaught RangeError: Invalid array length
```
####4.TypeError（类型错误）

a.变量或参数不是预期类型，比如，对字符串、布尔值、数值等原始类型的值使用new命令，就会抛出这种错误，因为new命令的参数应该是一个构造函数。
```
eg: var a= new 123;

　　Uncaught TypeError: 123 is not a function
```
b.调用对象不存在的方法
```
eg:var a;a.aa();

　　Uncaught TypeError: Cannot read property 'aa' of undefined
```
####5.URLError（URL错误）

与url相关函数参数不正确，主要是encodeURI()、decodeURI()、encodeURIComponent()、decodeURIComponent()、escape()和unescape()这六个函数。
```
eg: decodeURI('%2')

　　Uncaught URIError: URI malformed
```


#####6.EvalError（eval错误）
eval函数没有被正确执行时，会抛出EvalError错误。该错误类型已经不再在ES5中出现了，只是为了保证与以前代码兼容，才继续保留。

以上这6种派生错误，连同原始的Error对象，都是构造函数。开发者可以使用它们，人为生成错误对象的实例。
```
new Error("出错了！");
new RangeError("出错了，变量超出有效范围！");
new TypeError("出错了，变量类型无效！");
```
上面代码表示新建错误对象的实例，实质就是手动抛出错误。可以看到，错误对象的构造函数接受一个参数，代表错误提示信息（message）。
