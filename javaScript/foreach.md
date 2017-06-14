# javaScript的forEach实现原理
```
function forEach(object, block, context, fn) {
	if(object == null) return;
	if(!fn) {
		if(typeof object == "function" && object.call) {
			//遍历普通对象
			fn = Function;
		} else if(typeof object.forEach == "function" && object.forEach != arguments.callee) {
			//如果目标已经实现了forEach方法，则使用它自己的forEach方法（如标准游览器的Array对象）
			object.forEach(block, context);
			return;
		} else if(typeof object.length == "number") {
			// 如果是类数组对象或IE的数组对象
			_Array_forEach(object, block, context);
			return;
		}
	}
	_Function_forEach(fn || Object, object, block, context);
};

function _Array_forEach(array, block, context) {
	if(array == null) return;
	var i = 0,
		length = array.length;
	if(typeof array == "string") {
		for(; i < length; i++) {
			block.call(context, array.charAt(i), i, array);
		}
	} else {
		for(; i < length; i++) {
			block.call(context, array[i], i, array);
		}
	}
};
_Function_forEach = function(fn, object, block, context) {
	// 这里的fn恒为Function
	for(var key in object) {
		//只遍历本地属性
		if(object.hasOwnProperty(key)) {
			//相当于 block(object[key], key)
			block.call(context, object[key], key, object);
		}
	}
};
```
