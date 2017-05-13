###获取鼠标的位置
####获取鼠标的位置可以分为多个情况
1. 获取鼠标相对于浏览器窗口的位置
```
let x = event.clientX;
let y = event.clientY;
```

2. 获取鼠标相对于整个页面的位置
```
let x = event.pageX;
let y = event.pageY;
```

3. 获取鼠标相对点击元素的位置

  这一个获取有点难度，因为在JavaScript原生方法中没有这个方法，那么需要分析如何获取，突破点一获取点击元素的相对其父本元素的偏移量这个属性在event的target属性下边绑定可以循环的方法获取点击元素相对于页面的位置方法如下


  ```
  /*
  *该方法是获取点击元素相对于页面的距离
  *传入的参数为event.target
  */
  function getOffset(obj){
   var tmp={
      val:obj.offsetParent, //获取点击元素的父本
      x:obj.offsetLeft, //获取点击元素的相对于父本的水平偏移
      y:obj.offsetTop, //获取点击元素的相对于父本的垂直偏移
   }

   while(tmp.val != null){ //当父级不存在是停止循环
    tmp.x += tmp.val.offsetLeft; //水平偏移量累计
    tmp.y += tmp.val.offsetTop; //垂直偏移量累计
    tmp.val = tmp.val.offsetParent; //获取父本
   }
   return tmp; //返回点击元素相对于页面的偏移值
}
  ```
以上函数是获取了点击元素相对于页面的水平和垂直偏移，依赖以上获得的值，只需要鼠标相对于页面的偏移减去点击元素相对于页面的偏移方法如下：


```
/*
*该方法是获取鼠标相对于点击元素的位移
*传入的参数为event
*/
function getCurrent(elem){
  let off = getOffset(elem.target); //获取元素相对于页面的位置
  let tmp = {};
  return tmp = {
    x:elem.pageX - off.x,
    y:elem.pageY - off.y
  }
}
```
以上就是常用的获取鼠标位置的方法
