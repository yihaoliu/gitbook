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
3.getBoundingClientRect用于获取某个html元素相对于页面的位置集合
执行 object.getBoundingClientRect();会得到元素的top、right、bottom、left、width、height属性，这些属性以一个对象的方式返回。

主要是left和bottom要解释一下，left是指右边到页面最左边的距离，bottom是指底边到页面顶边的距离。

######(ie5以上都能支持，但是又一点点地方需要修正一下，IE67的left、top会少2px,并且没有width、height属性。)

![图解](http://www.51xuediannao.com/uploads/allimg/160811/2340295W5-0.jpg?_=6179964)

```
<div id="test" style="width: 100px; height: 100px; background: #ddd;"></div>
<script>
    function getObjXy(obj){
        var xy = obj.getBoundingClientRect();
        var top = xy.top-document.documentElement.clientTop+document.documentElement.scrollTop,//document.documentElement.clientTop 在IE67中始终为2，其他高级点的浏览器为0
            bottom = xy.bottom,
            left = xy.left-document.documentElement.clientLeft+document.documentElement.scrollLeft,//document.documentElement.clientLeft 在IE67中始终为2，其他高级点的浏览器为0
            right = xy.right,
            width = xy.width||right - left, //IE67不存在width 使用right - left获得
            height = xy.height||bottom - top;

        return {
            top:top,
            right:right,
            bottom:bottom,
            left:left,
            width:width,
            height:height
        }
    }

    var test = getObjXy(document.getElementById('test'));
    alert("top:" + test.top + ", right:" + test.right + ", bottom:" + test.bottom + ", left:" + test.left);
</script>
```
以上就是常用的获取鼠标位置的方法
