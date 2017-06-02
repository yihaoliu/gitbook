# CSS常见布局解决方案
### 水平居中布局
----
##### 1.margin + 定宽
```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .child {
    width: 100px;
    margin: 0 auto;
  }
</style>
```
##### 2. table + margin
```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .child {
    display: table;
    margin: 0 auto;
  }
</style>
```
display: table 在表现上类似 block 元素，但是宽度为内容宽。

- 无需设置父元素样式 （支持 IE 8 及其以上版本）兼容 IE 8 一下版本需要调整为 table

##### 3.inline-block + text-align

```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .child {
    display: inline-block;
  }
  .parent {
    text-align: center;
  }
</style>
```
- 兼容性佳（甚至可以兼容 IE 6 和 IE 7）
##### 4.absolute + margin-left

```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
.parent {
    position: relative;
  }
  .child {
    position: absolute;
    left: 50%;
    width: 100px;
    margin-left: -50px;  /* width/2 */
  }
</style>
```
- 宽度固定
- 相比于使用transform ，有兼容性更好

##### 5. absolute + transform

```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    position: relative;
  }
  .child {
    position: absolute;
    left: 50%;
    transform: translateX(-50%);
  }
</style>
```
- 绝对定位脱离文档流，不会对后续元素的布局造成影响。
- transform 为 CSS3 属性，有兼容性问题

##### 6. flex + justify-content

```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    display: flex;
    justify-content: center;
  }
</style>
```
- 只需设置父节点属性，无需设置子元素
- flex有兼容性问题

### 垂直居中
----
##### 1.table-cell + vertical-align

```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    display: table-cell;
    vertical-align: middle;
  }
</style>
```
- 兼容性好(IE 8以下版本需要调整页面结构至 table

##### 2.absolute + transform

```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    position: relative;
  }
  .child {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
  }
</style>
```
- 绝对定位脱离文档流，不会对后续元素的布局造成影响。但如果绝对定位元素是唯一的元素则父元素也会失去高度。
- transform 为 CSS3 属性，有兼容性问题

##### 3.flex + align-items
```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    display: flex;
    align-items: center;
  }
</style>
```

### 水平垂直居中
----
##### 1. absolute + transform
```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    position: relative;
  }
  .child {
    position: absolute;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
  }
</style>
```
- 绝对定位脱离文档流，不会对后续元素的布局造成影响。
- transform 为 CSS3 属性，有兼容性问题

##### 2. inline-block + text-align + table-cell + vertical-align

```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    text-align: center;
    display: table-cell;
    vertical-align: middle;
  }
  .child {
    display: inline-block;
  }
</style>
```
- 兼容性好

##### 3. flex + justify-content + align-items

```
<div class="parent">
  <div class="child">Demo</div>
</div>

<style>
  .parent {
    display: flex;
    justify-content: center; /* 水平居中 */
    align-items: center; /*垂直居中*/
  }
</style>
```
- 只需设置父节点属性，无需设置子元素
- 蛋疼的兼容性问题

### 一列定宽，一列自适应

----
##### 1.float + margin

```
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .left {
    float: left;
    width: 100px;
  }
  .right {
    margin-left: 100px
    /*间距可再加入 margin-left */
  }
</style>
```
###### IE 6 中会有3像素的 BUG，解决方法可以在 .left 加入 margin-left:-3px 当然也有解决这个小bug的方案如下：

```
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right-fix">
    <div class="right">
      <p>right</p>
      <p>right</p>
    </div>
  </div>
</div>

<style>
  .left {
    float: left;
    width: 100px;
  }
  .right-fix {
    float: right;
    width: 100%;
    margin-left: -100px;
  }
  .right {
    margin-left: 100px
    /*间距可再加入 margin-left */
  }
</style>
```
- 此方法不会存在 IE 6 中3像素的 BUG，但 .left 不可选择， 需要设置 .left {position: relative} 来提高层级。 注意此方法增加了不必要的 HTML 文本结构。

##### 2.float + overflow
```
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .left {
    float: left;
    width: 100px;
  }
  .right {
    overflow: hidden;
  }
</style>
```
- 设置 overflow: hidden 会触发 BFC 模式（Block Formatting Context）块级格式上下文。BFC是什么呢。用通俗的来讲就是，随便你在BFC 里面干啥，外面都不会受到影响 。此方法样式简单但不支持 IE 6

##### 3.table
```
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .parent {
    display: table;
    width: 100%;
    table-layout: fixed;
  }
  .left {
    display: table-cell;
    width: 100px;
  }
  .right {
    display: table-cell;
    /*宽度为剩余宽度*/
  }
</style>
```
- table 的显示特性为每列的单元格宽度和一定等与表格宽度。 table-layout: fixed 可加速渲染，也是设定布局优先。table-cell 中不可以设置 margin 但是可以通过 padding 来设置间距

##### 4.flex
```
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .parent {
    display: flex;
  }
  .left {
    width: 100px;
    margin-left: 20px;
  }
  .right {
    flex: 1;
  }
</style>
```
- 低版本浏览器兼容问题
- 性能问题，只适合小范围布局

### 等分布局
----

##### 1.float

```
<div class="parent">
  <div class="column">
    <p>1</p>
  </div>
  <div class="column">
    <p>2</p>
  </div>
  <div class="column">
    <p>3</p>
  </div>
  <div class="column">
    <p>4</p>
  </div>
</div>
<style>
  .parent {
    margin-left: -20px;
  }
  .column {
    float: left;
    width: 25%;
    padding-left: 20px;
    box-sizing: border-box;
  }
</style>
```
- 此方法可以完美兼容 IE8 以上版本


##### 2.flex

```
<div class="parent">
  <div class="column">
    <p>1</p>
  </div>
  <div class="column">
    <p>2</p>
  </div>
  <div class="column">
    <p>3</p>
  </div>
  <div class="column">
    <p>4</p>
  </div>
</div>


<style>
  .parent {
    display: flex;
  }
  .column {
    flex: 1;
  }
  .column+.column { /* 相邻兄弟选择器 */
    margin-left: 20px;
  }
</style>
```
- 强大简单，有兼容问题

##### 3.table

```
<div class='parent-fix'>
  <div class="parent">
    <div class="column">
      <p>1</p>
    </div>
    <div class="column">
      <p>2</p>
    </div>
    <div class="column">
      <p>3</p>
    </div>
    <div class="column">
      <p>4</p>
    </div>
  </div>
</div>

<style>
  .parent-fix {
    margin-left: -20px;
  }
  .parent {
    display: table;
    width: 100%;
    /*可以布局优先，也可以单元格宽度平分在没有设置的情况下*/
    table-layout: fixed;
  }
  .column {
    display: table-cell;
    padding-left: 20px;
  }
</style>
```
### 等高布局
----

##### 1.table

```
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .parent {
    display: table;
    width: 100%;
    table-layout: fixed;
  }
  .left {
    display: table-cell;
    width: 100px;
  }
  .right {
    display: table-cell
    /*宽度为剩余宽度*/
  }
</style>
```
##### 2.flex

```
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .parent {
    display: flex;
  }
  .left {
    width: 100px;
    margin-left: 20px;
  }
  .right {
    flex: 1;
  }
</style>
```
- 注意这里实际上使用了 align-items: stretch，flex 默认的 align-items 的值为 stretch

##### 3.float

```
<div class="parent">
  <div class="left">
    <p>left</p>
  </div>
  <div class="right">
    <p>right</p>
    <p>right</p>
  </div>
</div>

<style>
  .parent {
    overflow: hidden;
  }
  .left,
  .right {
    padding-bottom: 9999px;
    margin-bottom: -9999px;
  }
  .left {
    float: left;
    width: 100px;
    margin-right: 20px;
  }
  .right {
    overflow: hidden;
  }
</style>
```
##### 此方法为伪等高（只有背景显示高度相等），左右真实的高度其实不相等。 兼容性较好。
到此，我们了解常见的布局解决方案，这些只是参考，一样的布局实现方式多种多样。主要就使用position、flex 、table（从很久很久以前起，我们就抛弃了table布局页面，但display: table;是异常强大）、float等属性目前flex兼容性较差 傲娇的程序员应该放弃太低版本的浏览器
