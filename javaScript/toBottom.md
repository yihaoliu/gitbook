# 滚到底部加载
####其实这个功能的实现就是监听滚动条是否滚动到底部，如果滚动到底部则向后后台获取数据废话不多说上代码

浏览器视口的高度
```
getWindowHeight = () => {
　　var windowHeight = 0;
　　if(document.compatMode == "CSS1Compat"){
　　　　windowHeight = document.documentElement.clientHeight;
　　}else{
　　　　windowHeight = document.body.clientHeight;
　　}
　　return windowHeight;
}
```

文档的总高度
```
getScrollHeight = () => {
　　var scrollHeight = 0, bodyScrollHeight = 0, documentScrollHeight = 0;
　　if(document.body){
　　　　bodyScrollHeight = document.body.scrollHeight;
　　}
　　if(document.documentElement){
　　　　documentScrollHeight = document.documentElement.scrollHeight;
　　}
　　scrollHeight = (bodyScrollHeight - documentScrollHeight > 0) ? bodyScrollHeight : documentScrollHeight;
　　return scrollHeight;
}
```

滚动条在Y轴上的滚动距离
```
getScrollTop = () => {
　　var scrollTop = 0, bodyScrollTop = 0, documentScrollTop = 0;
　　if(document.body){
　　　　bodyScrollTop = document.body.scrollTop;
　　}
　　if(document.documentElement){
　　　　documentScrollTop = document.documentElement.scrollTop;
　　}
　　scrollTop = (bodyScrollTop - documentScrollTop > 0) ? bodyScrollTop : documentScrollTop;
　　return scrollTop;
}
```
那么剩下的就是在js里边进行判断这里主要说在react里的用法
这里把所有的操作写在一个方法里
```
onScrollListener = () =>{
  if(getScrollTop() + getWindowHeight() == getScrollHeight()){
    alert("滚动条已经滚动到了底部");
  }
}
```

在react的componentDidMount生命周期里写一下代码
```
componentDidMount(){
		//滚轮监听
		addEventListener("scroll",this.onscrollListener,false)
	}
```
在react的componentWillUnmount生命周期里写一下代码(注册事件要在组件销毁以后注销这样可以提高性能)

```
componentWillUnmount(){
		window.removeEventListener("scroll",this.onscrollListener)
}
```
