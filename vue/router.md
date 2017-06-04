# vue-router
>Vue.js的一大特色就是构建单页面应用十分方便，既然要方便构建单页面应用那么自然少不了路由，vue-router就是vue官方提供的一个路由框架。总体来说，vue-router设计得简单好用，下面就来聊聊我实际用到过的一些方法，文章没有提到的可以去查看官方文档。vue-router的安装这里就不提了，相信会来看这篇博客同学，这些基本能力都是有的。

先上例子
```
<!-- html部分，省略head -->
<div id="app">
    <div class="content">
        <router-link to="/goods">商品</router-link>
        <router-link to="/ratings">评论</router-link>
        <router-link to="/seller">商家</router-link>
    </div>
    <router-view></router-view>
</div>
<script src="path-to-vue"></script>
<script src="path-to-vue-router"></script>
// 首先定义或者引入路由的组件
// 方法一：直接定义路由组件
const goods = { template: '<p>goods</p>' };
const ratings = { template: '<p>ratings</p>' };
const seller = { template: '<p>seller</p>' };
// 方法二：import引入路由组件
import goods from 'components/goods/goods';
import ratings from 'components/ratings/ratings';
import seller from 'components/seller/seller';
// 然后定义路由(routes)，components还可以是Vue.extend()创建的
const routes = [
  { path: '/goods', component: goods },
  { path: '/ratings', component: ratings },
  { path: '/seller', component: seller }
];
// 接着创建路由实例
const router = new VueRouter({
  // ES6缩写语法，相当于routes:routes
  routes  
});
// 最后创建vue实例并挂载
const app = new Vue({
  el: '#app',
  router
});
// 或者
const app = new Vue({
  router
}).$mount('#app')
```
到这里就可以用vue-router轻松搭建一个单页面应用了。我一般都是使用模块化编程的形式，用.vue单文件，不知道在模块化编程里怎么加载vue-router的可以参考我的某个项目源码。

router-link和router-view
看了上面的例子，一定对router-link和router-view很感兴趣。

###router-link
从上面例子中的书写形式就可以看出，router-link是一个组件，它默认会被渲染成一个带有链接的a标签，通过to属性指定链接地址。
注意：被选中的router-link将自动添加一个class属性值.router-link-active。

#####router-link属性配置
#####to


这是一个必须设置的属性，否则路由无法生效。它表示路由的链接，可以是一个字符串也可以是一个描述目标位置的对象。
```
<router-link to="goods"></router-link>
<router-link to="{path='goods'}"></router-link>
```
#####replace

一个布尔类型，默认为false。如果replace设置为true，那么导航不会留下history记录，点击浏览器回退按钮不会再回到这个路由。
```
<router-link to="goods" replace></router-link>
```
#####tag

router-link默认渲染成a标签，也有方法让它渲染成其他标签，tag属性就用来设置router-link渲染成什么标签的。
```
<!-- 渲染成li标签 -->
<router-link to="goods" tag="li"></router-link>
```
#####active-class

上面说了被选中的router-link将自动添加一个class属性值.router-link-active，这个属性就是来修改这个class值的。

###router-view
这个组件十分关键，它就是用来渲染匹配到的路由的。
可以给router-view组件设置transition过渡，具体用法见Vue2.0 Transition常见用法全解惑。
还可以配合<keep-alive>使用，keep-alive可以缓存数据，这样不至于重新渲染路由组件的时候，之前那个路由组件的数据被清除了。比如对当前的路由组件a进行了一些DOM操作之后，点击进入另一个路由组件b，再回到路由组件a的时候之前的DOM操作还保存在，如果不加keep-alive再回到路由组件a时，之前的DOM操作就没有了，得重新进行。如果你的应用里有一个购物车组件，就需要用到keep-alive。
```
<transition>
  <keep-alive>
    <router-view></router-view>
  </keep-alive>
</transition>
```
###一些小需求怎么实现
不同路由不同页面标题
多页面应用我们可以给每一个页面都设置一个不同的标题，但是如果是单页面应用的路由呢？其实也是可以实现的，实现的方法不止一种，我之前用的是结合命名路由和导航钩子函数的方法。如下：
```
// 定义路由的时候如下定义，name也可为中文
const routes = [
  { path: '/goods', component: goods, name: 'goods' },
  { path: '/ratings', component: ratings, name: 'ratings' },
  { path: '/seller', component: seller, name: 'seller' }
];
// 创建路由实例
const router = new VueRouter({
  routes: routes
})
// 关键在这里，设置afterEach钩子函数
router.afterEach((to, from, next) => {
  document.title = to.name;
})
```
###命名路由
既然用到了命名路由，这里就提一下吧。命名路由就是用一个名称来标识一个路由，在定义路由的时候设置一个name属性即可。在router-link中也可以用路由的名字来链接到一个路由。
```
<router-link :to="{ name: 'seller'}">seller</router-link>
```
导航钩子
这个我还没怎么用过，它主要是用来拦截导航的，想使用的参考官方文档吧。

怎么刚进入应用就渲染某个路由组件
刚进入应用都是进入到“/”这个路由的，如果想直接进入到“/goods”怎么办，这里提供两种方法。一种是利用重定向，另一种是利用vue-router的导航式编程。

###重定向
```
const routes = [
  { path: '/', redirect: '/goods'}
]
```
是不是很简单呢？重定向的目标也可以是一个命名的路由。
```
const routes = [
  { path: '/', redirect: { name: 'goods' }}
]
```
###导航式编程
利用vue-router的导航式编程的router.push方法也可以实现上面的需求。
```
// 在创建vue实例并挂载后调用
router.push('/goods')
```
router.push方法就是用来动态导航到不同的链接的。它会向history栈添加一个新的记录，点击<router-link :to="...">等同于调用router.push(...)。

vue-router中还有router.replace方法和router.go方法，概念及用法可参考[https://router.vuejs.org/zh-cn](https://router.vuejs.org/zh-cn/)。
