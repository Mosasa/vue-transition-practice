## 写在前面的思考  
* 如何匹配到相应需要跳转的页面？ 
* 如何判断是“前进”还是“后退”而后使用不同的动画方式？
* 如何对不同的跳转设置动画效果？
## 实现过程  
### 一、vue路由匹配  
创建vue实例，匹配路由。  
用Vue.js + Vue Router创建单页应用，是非常简单的。使用Vue.js，我们可以通过组合组件来组成应用程序，将Vue Router 添加进来之后，我们需要做的是，将组件(components)映射到路由(routes),然后告诉Vue Router 在哪里渲染它们。

```
import Vue from 'vue'
import Router from 'vue-router'
Vue.use(Router)
//如果使用模块化机制编程，导入Vue和VueRouter，就需要调用Vue.use(Router)
```
接下来就可以进行路由组件的映射：  
(路由)组件的定义可以自行定义，当然，为了践行模块化组件化思想，多是从其他文件import进来。以下以简单的“登录->主页->点单->结算”四个页面的交互为例：

```
import Login from '@/components/login' 
import Index from '@/components/index' 
import PointList from '@/components/pointList/pointList' 
import SettLement from '@/components/pointList/settlement' 

//创建router实例，然后传入‘routes’配置
export default new Router({
  //routes配置可以直接传入，也可以先定义后使用
  //每个路由都应该映射一个组件，其中component可以是通过Vue.extend()创建的组件构造器，或者只是一个组件配饰对象。(今天暂时不考虑嵌套路由的情况)
  routes: [
    {
      path: '/', // 登录
      name: 'Login',
      component: Login
    },
    {
      path: '/index', // 主页
      name: 'Index',
      component: Index
    },
    {
      path: '/pointList', // 点单
      name: 'PointList',
      component: PointList
    },
    {
      path: '/settLement', // 结算
      name: 'SettLement',
      component: SettLement
    }
]
})
```  
### 二、路由跳转 $router  

```
组件路由除了使用全局组件 router-link 来实现点击跳转(相当于按钮)外，还可以使用组件本身具有的一个实例对象$router及其一些属性来达到目标。  
$router 是VueRouter的一个实例对象，相当于一个全局的路由器对象。在Vue实例内部，你可以通过$router访问路由实例，里面含有很多属性和子对象
，例如history对象，经常用到的跳转链接就可以调用this.$router.push，this.$router.push会往history栈中添加一个新记录。
```
声明式|编程式
---|:--:
<router-link :to="..."|router.push(...)
点击 <router-link :to="..."> 等同于调用 router.push(...)  
(...)该方法的参数可以是一个字符串，或者一个描述地址的对象：

```
// 字符串
router.push('home')
// 对象
router.push({ path: 'home' })
// 命名的路由
router.push({ name: 'user', params: { userId: 123 }})
// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```
组件路由跳转实例：  
1.
```
<router-link :to="{name:'PointList', params: { userId: 123 }}">
      <i class="icon"><img src="../assets/point.png" alt=""></i>
      <span>点单</span>
</router-link>
```
2.

```
<footer class="version" @click="goPage('Author')">v 1.0</footer>
//Js：
methods: {
    goPage(url, param) {
      this.$router.push({ name: url });
    }
}
```
### 三、vue路由对象$route(只读)
在使用了vue-router的应用中，路由对象会被注入每个组件中，赋值为this.$route,并且当路由切换时，路由对象会被更新。所以route相当于当前正在跳转的路由对象，可以从里面获取name,path,params,query等，即包含了当前URL解析得到的信息，还有URL匹配到的路由记录。  
路由对象暴露了以下属性(常见)：  
1. $route.path  

```
字符串(string)。等于当前路由对象的路径，会被解析为绝对路径，如:http://example.com/#/login?name=aa，this.$route.path
，输出“/login”，即对应上面1中路由匹配时routes配置中的“path”；
```
2. $route.name  
字符串(string)。有时候，通过一个名称来标识一个路由显得更加方便，特别是在链接一个路由，或者是执行一些跳转的时候。同样，这里的name也对应了routes配置中给某个路由设置名称的name值：  
要链接到一个命名路由，可以给router-link的to属性传一个对象：

```
<router-link :to="{name:'Order', params: { userId: 123 }}">
</router-link>
```
用在调用router.push()中也是一回事：

```
this.$router.push({ name: 'Order', params: { userId: 123 }})
```
3. $route.params  
   对象(object)。路由跳转携带参数： 

```
this.$router.push({ name: 'Order', params: { userId: 123 }})
console.log(this.$route.params.userId); //123
```
4. $route.query  
    对象(object)。可访问携带的查询参数：

```
this.$router.push({name: 'login', query:{name: 'userName'}});
this.$route.query.name;    //you
//此时路由为:"http://example.com/#/login?name=userName。"
```
5. $route.redirectedFrom  
  字符串(string)。重定向来源：
```
如：{ path: '*',redirect: {name: 'hello'}}
	此时访问不存在的路由http://example.com/#/a会重定向到hello，
在hello访问this.$route.redirectedFrom; 输出“/a”。
```
6. $route.matched  
    数组(array)。当前路由下路由声明的所有信息，从父路由(如果有)到当前路由为止。
7. $route.hash  
    	字符串(string)。当前路径的hash值。
### 四、vue监听$route的方式

```
watch：{‘$route’ (to, from) {}}
```  
$route 作为vue实例的一个响应式属性，和在data中写的属性本质上是一样的，都可以通过this的方式拿到。既然你可以监听data中的属性变化，同样也可以监听 $route 的变化。watch中监听的对象默认回调函数中的参数值就是newVal,oldVal。作为 $route 属性来说当然也就是 to 和 from 的概念了。  
Vue用router.push(传参)跳转页面，参数改变，在跳转后的路由观察路由变化，进行页面刷新，可对“from->to”的过程设置动画效果。  
该功能的难点就在于怎样获取“上一页”和“下一页”,即怎样分辨是“前进”还是“后退”？  
例：

```
// watch $route 决定使用哪种过渡
watch:{
    '$route' (to, from) {
      //此时假设从index页面跳转到pointList页面
      console.log(to); // "/pointList"
      console.log(from); // “/index”
      const routeDeep = ['/', '/index','/pointList', '/settLement'];
      const toDepth = routeDeep.indexOf(to.path)
      const fromDepth = routeDeep.indexOf(from.path)
      this.transitionName = toDepth > fromDepth ? 'fold-left' : 'fold-right'
    }
  },
```
to、from是最基本的路由对象，分别表示从(from)某个页面跳转到(to)另一个页面,to.path（表示要跳转到的路由地址），from.path同理。  
定义routeDeep数组，将路由目录按层级依次排序(暂不考虑嵌套路由的情况)，复杂单页应用里，同一层级（如同一页面上的多个导航按钮）顺序随意，然后依次排列每个导航的下一页、下下页…即保证每个“上一页”在“下一页”前面。  
总结下来就是：按照routeDeep数组里定义的路由目录的顺序，“toDepth > fromDepth”表示“上一页”跳转到“下一页”，同理可由此判断是“前进”还是“后退”。
### 五、Vue2.0中transition组件的使用

```
<transition :name="transitionName">
    <router-view class="view app-view"></router-view>
</transition>
```
* transition中只有name属性，不可添加其他标签属性。
* transition中只能有一个子元素并且该子元素需要有v-show或者v-if来控制是否显示。
##### 过渡CSS类名
transition中的name属性用于 替换 vue钩子函数中的类名transitionName- 
* transitionName-enter: 定义进入过渡的开始状态。在元素被插入时生效，在下一个帧移除。
* transitionName-enter-active: 定义进入过渡的结束状态。在元素被插入时生效，在transition/animation完成之后移除。
* transitionName-leave:定义离开过渡的开始状态。在离开过渡被触发时生效，在下一个帧移除。
* transitionName-leave-active: 定义离开过渡的结束状态。在离开过渡被触发时生效，在transition/animation完成之后移除。
```
this.transitionName = toDepth > fromDepth ? 'fold-left' : 'fold-right'
```
在“watch $route”中，判断页面跳转的“前进”和“后退”时，决定用不同的过渡效果（fold-left还是fold-right）。
### 六、animation、transform动画效果实现
在上一个主题中，判断页面跳转路径之后，为两种跳转的transition设置不同的类名“fold-left”、“fold-right”。  
然后在CSS中，为两种类名设置不同的动画效果(这里以“左滑动”和“右滑动”为例)：

```
.fold-left-enter-active {
    animation-name: fold-left-in;
    animation-duration: .3s;
  }
  .fold-left-leave-active {
    animation-name: fold-left-out;
    animation-duration: .3s;
  }
.fold-right-enter-active {
    animation-name: fold-right-in;
    animation-duration: .3s;
  }
  .fold-right-leave-active {
    animation-name: fold-right-out;
    animation-duration: .3s;
  }
```
animation 属性是一个简写属性，用于设置六个动画属性：
值|描述
---|:--:
animation-name|规定需要绑定到选择器的 keyframe 名称。
animation-duration|规定完成动画所花费的时间，以秒或毫秒计。
animation-timing-function|规定动画的速度曲线。
animation-delay|规定在动画开始之前的延迟。
animation-iteration-count|规定动画应该播放的次数。
animation-direction|规定是否应该轮流反向播放动画。

```
@keyframes fold-left-in {
    0% {
      transform: translate3d(100%, 0, 0);
    }
    100% {
      transform: translate3d(0, 0, 0);
    }
  }
  @keyframes fold-left-out {
    0% {
      transform: translate3d(0, 0, 0);
    }
    100% {
      transform: translate3d(-100%, 0, 0);
    }
  }
```
根据CSS3 @keyframes规则，创建动画。创建动画的原理即将一套CSS样式逐渐变化为另一套样式。在动画过程中，能够多次改变这套CSS样式。可以“百分比”来规定改变发生的时间，或者通过关键词“from”和“to”，等价于“0%”(动画的开始时间)和“100%”(动画的结束时间)。一般为了获得最佳的浏览器支持，应该始终定义0%和100%选择器。  
transform属性向元素应用2D或3D转换。该属性允许我们对元素进行旋转、缩放、移动或倾斜。translate3d(x,y,z)定义3D转换，如transform：translate3d(100%, 0, 0)只改变了x的值，即代表横向左滑动，同理可相应推出其他情况。
## 总结
以上就是vue页面跳转动画效果功能实现的6个步骤，即这个功能中所含括的6个大知识点，当然其中还包括许多扩展的知识点，学无止境，需慢慢深入挖掘…
