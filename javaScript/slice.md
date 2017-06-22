# Array.prototype.slice应用和原理探析
###问题由来
Array.prototype.slice常见于两种调用场景：

#####一是对函数arguments对象的转换

Array.prototype.slice.call(arguments)

#####二是类似jQuery原型对象的定义中

toArray: function() {
return slice.call( this, 0 );
},

有必要探究一番其使用方式和内部原理。
###问题拆解
Array.prototype.slice作为Array.prototype对象的一个方法，对其调用在API中有详细说明，并不费解，对Array.prototype.slice的疑问可拆解为两个方面：一是该函数究竟返回什么对象？二是该函数可应用于什么对象？即非数组对象需要满足什么条件才能借用(复用)该函数？
###该函数究竟返回什么对象
为了探究返回对象的类型信息，写一个简单的对象工具集合：
```
var objs = {  
    "descType":function(o){  
        console.info(o);  
        console.info("typeof o:" + (typeof o));  
        console.info("o.constructor:" + (o.constructor));  
        console.info("o instanceof Object:" + (o instanceof Object));  
        console.info("o instanceof Array:" + (o instanceof Array));  
    }  
};  
```
然后测试几个简单的例子：
```
var v = ['a',true,9].slice(0);  
objs.descType(v);
// typeof o:object
// o.constructor:function Array() { [native code] }
// o instanceof Object:true
// o instanceof Array:true
```
数组对象自身上调用slice方法当然不必再使用“var v = Array.prototype.slice.call(['a',true,9]);”这种方法，虽然是可以这样用的，结果表明返回的对象的确是Array类型的对象。
###该函数可应用于什么对象
1 既然该方法是作为Array.prototype的方法，那么数组对象本身当然可以调用该方法。

2 Arguments类型的对象可正确调用该函数。

同样用一个简单的例子测试下arguments对象借用该函数后的对象类型。
```
function f() {  
    objs.descType(arguments);  
    var newArg = Array.prototype.slice.call(arguments, 0);  
    objs.descType(newArg);  
}  
f('aa','bb','cc');

// ["aa", "bb", "cc"]  
// typeof o:object  
/// o.constructor:function Object() { [native code] }  
// o instanceof Object:true  
// o instanceof Array:false  


// ["aa", "bb", "cc"]  
// typeof o:object  
// o.constructor:function Array() { [native code] }  
// o instanceof Object:true  
// o instanceof Array:true    
```
可以看到arguments对象本身并不是Array类型的--o instanceof Array:false，进一步展开该对象细节，可以看到其属性列表如下：

```
["aa", "bb", "cc"]  
0:"aa"  
1:"bb"  
2:"cc"  
callee:function f()  
length:3  

```
有length属性，有'0','1','2'属性，有'callee'属性，是一个Arguments类型的一个对象实例。而经过Array.prototype.slice.call调用后返回的对象就是一个真真正正的Array对象：

```
["aa", "bb", "cc"]  
0:"aa"  
1:"bb"  
2:"cc"  
length:3  
```

这说明arguments对象和转换后的array对象非常类似--不仅属性类似，连用法也类似，比如arguments对象也支持[]运算符调用其属性值(其实本质是对象元素访问的通用语法，只不过属性名刚好为转换为字符串的整数而已)。所以结论是在js语言中，对arguments对象调用Array.prototype.slice.call(arguments, 0)纯属“闲的蛋疼”的做法(除非设计的函数用法非常的动态，比如需要对参数做shift等之类的数组操作)。

那么除了arguments对象之外，其他类型的对象可以借用Array.prototype.slice函数么？继续简单的测试几个例子：
```
objs.descType(Array.prototype.slice.call(true,0));  
objs.descType(Array.prototype.slice.call('abc',0));  
objs.descType(Array.prototype.slice.call(256,0));  
objs.descType(Array.prototype.slice.call({},0));  
objs.descType(Array.prototype.slice.call(undefined,0));  
objs.descType(Array.prototype.slice.call(null,0));  
objs.descType(Array.prototype.slice.call(NaN,0));
```
对undefined和null调用slice抛出异常，这是预期的行为，对NaN/true/256/{}返回一个空数组对象，对字符串'abc'返回一个数组：["a", "b", "c"]，现在的重点是对对象调用的探究，传入一个初始化为空的对象直接量返回的是一个空数组对象，而上面测试在arguments对象上调用slice返回一个正常的数组--两者有何区别？

一个很容易联想的点子是该调用对象应该具备length属性才可以被slice正确处理，测试一下：
```
objs.descType(Array.prototype.slice.call({'length':5},0));
```
结果果然是一个length值为5的数组对象，但其中5个元素值都为undefined。那么更进一步的想法应该是：调用对象除了有length属性之外，是不是也应该有索引属性名？即自动转换为字符类型的整数数字属性名？再试一下：
```
objs.descType(Array.prototype.slice.call({'length':5,'0':'gebilaowang','1':'ximenqin'},0));  
```
ok，大功告成，结果果然是预期数组：["gebilaowang","ximenqin",undefined,undefined,undefined]。因此结论是：只有具备"可转换为数值型的length属性"并且同时具备"索引属性"的对象才可以正确被slice函数处理(返回或者说转换为预期数组)，这种类型的对象经过查阅果然有一种专属称谓--Array-like Object。

###3 Array-like对象可正确调用该函数。

现在来探究jQuery中对Array.prototype.slice方法的使用场景，jQuery对各种原型方法的应用当然是非常牛逼的甚至达到炉火纯青的境界，其实jQuery对象本身就是一个Array-like对象，因为其有length属性，也有索引属性，分析其初始化构造函数(1.6.1版本)即可明白(特别注意对this.length和this[index]属性的设置)：
```
jQuery.prototype.init: function( selector, context, rootjQuery ) {  
            var match, elem, ret, doc;  

            // Handle $(""), $(null), or $(undefined)  
            if ( !selector ) {  
                return this;  
            }  

            // Handle $(DOMElement)  
            if ( selector.nodeType ) {  
                this.context = this[0] = selector;  
                this.length = 1;  
                return this;  
            }  

            // The body element only exists once, optimize finding it  
            if ( selector === "body" && !context && document.body ) {  
                this.context = document;  
                this[0] = document.body;  
                this.selector = selector;  
                this.length = 1;  
                return this;  
            }  

            // Handle HTML strings  
            if ( typeof selector === "string" ) {  
              // Are we dealing with HTML string or an ID?  
               if ( selector.charAt(0) === "<" && selector.charAt( selector.length - 1 ) === ">" && selector.length >= 3 ) {  
                   // Assume that strings that start and end with <> are HTML and skip the regex check  
                   match = [ null, selector, null ];  

               } else {  
                   match = quickExpr.exec( selector );  
               }  

               // Verify a match, and that no context was specified for #id  
               if ( match && (match[1] || !context) ) {  

                   // HANDLE: $(html) -> $(array)  
                   if ( match[1] ) {  
                       context = context instanceof jQuery ? context[0] : context;  
                       doc = (context ? context.ownerDocument || context : document);  

                       // If a single string is passed in and it's a single tag  
                       // just do a createElement and skip the rest  
                       ret = rsingleTag.exec( selector );  

                       if ( ret ) {  
                           if ( jQuery.isPlainObject( context ) ) {  
                               selector = [ document.createElement( ret[1] ) ];  
                               jQuery.fn.attr.call( selector, context, true );  

                           } else {  
                               selector = [ doc.createElement( ret[1] ) ];  
                           }  

                       } else {  
                           ret = jQuery.buildFragment( [ match[1] ], [ doc ] );  
                           selector = (ret.cacheable ? jQuery.clone(ret.fragment) : ret.fragment).childNodes;  
                       }  

                       return jQuery.merge( this, selector );  

                   // HANDLE: $("#id")  
                   } else {  
                        elem = document.getElementById( match[2] );  

                        // Check parentNode to catch when Blackberry 4.6 returns  
                        // nodes that are no longer in the document #6963  
                        if ( elem && elem.parentNode ) {  
                            // Handle the case where IE and Opera return items  
                            // by name instead of ID  
                            if ( elem.id !== match[2] ) {  
                                return rootjQuery.find( selector );  
                            }  

                            // Otherwise, we inject the element directly into the jQuery object  
                            this.length = 1;  
                            this[0] = elem;  
                        }  

                        this.context = document;  
                        this.selector = selector;  
                        return this;  
                    }  

                // HANDLE: $(expr, $(...))  
                } else if ( !context || context.jquery ) {  
                    return (context || rootjQuery).find( selector );  

                // HANDLE: $(expr, context)  
                // (which is just equivalent to: $(context).find(expr)  
                } else {  
                    return this.constructor( context ).find( selector );  
                }  

            // HANDLE: $(function)  
            // Shortcut for document ready  
            } else if ( jQuery.isFunction( selector ) ) {  
               return rootjQuery.ready( selector );  
           }  

           if (selector.selector !== undefined) {  
               this.selector = selector.selector;  
               this.context = selector.context;  
           }  

           return jQuery.makeArray( selector, this );  
       },  
```
这是分析并理解jQuery背后原理的最基础最前提的一步。
###原理探析
```
function slice(start, end) {   
    var startToUse = start || 0,   
        endToUse = end || ToUint32(this.length),   
        result = [];   
    for(var i = startToUse; i < endToUse; i++) {   
        result.push(this[i]);   
    }  
    return result;   
}  

```
