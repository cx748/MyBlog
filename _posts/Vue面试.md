## 单页应用SPA
1. 概念
整个项目只有一个页面，根据前端路由切换想要的页面内容
2. 优缺点
优点： 有很好的交互体验，用户不需要重新刷新页面。 良好的前后端分离模式。减轻服务器压力，服务器只需要提供数据
缺点：不能使用浏览器的前进后退管理；初次加载耗时较多。SEO较差

## 对于MVVM的理解
MVVM是Model-view-ViewModel的缩写  
Model代表的数据模型  
View代表UI组件，负责将数据模型转换成UI展现出来
ViewModel 监听模型数据的改变和控制视图的行为，处理用户交互，用来同步View和Model的对象  
ViewModel 通过双向数据绑定把View层和Model层连接了起来，而View和Model之间的同步工作完全是自动的。开发者只需关系业务逻辑，不需要手动操作DOM，不需要关注数据状态的同步问题，复杂的数据状态维护完全有MVVM来同一管理。

## vue的响应式原理--双向数据绑定
原理主要是，采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来劫持各个属性的setter和getter，当数据变动时发布消息给订阅者，然后触发监听回调。

## Vue的生命周期
beforeCreate(创建前) 数据观测和初始化还没有开始  
created(创建后) 完成数据观测，属性和方法的运算，初始化时间，还没有开始渲染  
beforeMount(载入前) 在挂载开始之前被调用。Vue实例完成编译模板，把data里面的数据和模板生成html，但是html并没有被挂载到页面上  
Mounted(载入后) 在el被新创建的vm.$el替换，并挂载到实例上去之后调用。此时vue实例已经完成把html渲染到html页面  
beforeUpdate(更新前) 数据更新前调用，这个时候不会触发附加的重渲染过程  
updated(更新后) 数据更改导致的虚拟Dom重新渲染之后调用。调用时组件DOM已经更新  
beforeDestroy(销毁前) 实例销毁之前调用，实例仍然可用  
destroyed(销毁后) 在实例销毁之后调用，调用后所有事件监听会被移除，子实例也会被销毁

### 什么是vue生命周期
Vue实例从创建到销毁的过程
### vue生命周期的作用是什么
生命周期中有多个事件钩子，让我们能更好的控制整个VUe实例的过程
### 第一次加载页面会触发哪些钩子
创建前后，载入前后
### Dom渲染在哪个周期中完成
mounted中完成Dom渲染

## 父子组件生命周期顺序


## 组件的data为什么是函数
在有多个组件情况下，如果data还是一个对象，那么所有组件都会共享这个对象的数据，不利于复用。而data是函数之后，每个组件实例都有自己的作用域，他们data返回的数据就不会相互影响了
## v-if和v-show
1. 手段：v-if是控制DOM树添加节点，v-show是通过设置CSS样式，将display设置为none。
2. 性能差别：v-if有更高的切换消耗，v-show有更高的渲染消耗
3. 使用场景：v-if适合条件不大可能改变；v-show适合频繁切换时使用
## nextTick原理

## 虚拟DOM
### 真实DOM及其解析过程。
浏览器渲染过程：创建DOM树--创建styleRules--创建render树--布局layout--绘制painting  
第一步，用HTML分析器，分析HTML元素，构建一颗DOM树  
第二步，用CSS分析器，生成页面的样式表  
第三步，将DOM树和样式表关联起来，构建一颗Render树  
第四步，有了render树，浏览器开始布局，为Render树上的每一个节点确定精确的坐标  
第五步，调用Render树节点的painting方法，绘制页面
![浏览器渲染过程](https://upload-images.jianshu.io/upload_images/4345378-b7ccad3bc808783f.png?imageMogr2/auto-orient/strip|imageView2/2/w/624/format/webp)
在传统的开发中，再操作DOM时，浏览器会从构建DOM树开始从头到尾执行一遍流程，如果我们需要更新10个DOM，则这个渲染过程会执行10次。
### 虚拟DOM
如上的操作，虚拟DOM不会立即操作执行渲染过程，而是将这十次更新的内容保存在本地的一个JS对象中，然后遍历JS对象进行渲染。
#### 为什么要用虚拟DOM
1. 在数据变化比较小的时候，有更好的性能，数据变化很多的时候，每次重绘的性能都可以接受；
2. 依然可以用类似 innerHTML 的思路去写你的应用。

## 自定义指令
### 全局指令
直接调用vue的的directive方法
### 局部指令
在vue实例里定义directive对象

## Vue组件间的参数传递
1. props/$emit, 适合`父子组件`传值  
父组件传给子组件：组件用v-bind传数据，子组件通过props方法接收数据
子组件传给父组件：`$emit`绑定一个自定义事件, 当这个语句被执行时, 就会将参数arg传递给父组件,父组件通过v-on监听并接收参数。
2. $parent/$childen：只能在父子间通信。不建议使用，主要是应急
3. 当项目比较大时，可以通过vuex通信
4. 把数据放在他们的父组件，通过父组件通信
5. 借助PubSubJs库  
订阅消息 PubSub.subscribe('msg',data){}
发布消息 PubSub.publish('msh',data)


## Vue-router
vue-router是vue.js的官方路由管理器，用于构建单页应用
### $router和route的区别
$route是路由信息对象，包括path,params,hash,name等路由信息参数，而$router是'路由实例'对象，包括了跳转方法和钩子函数。
### 怎么定义Vue-router的动态路由，如何获取传过来的值
在router目录下的index.js文件中，对path属性加上/:id，使用router对象的params.id获取
### vue-router的两种模式：hash模式和history模式
1. hash模式(vue-ruter的默认模式)
只能改变`#`后面的url片段, 在记录中只能添加短字符串，但是在hash模式下，仅hash符号前面的url会被包含在请求中，后端如果没有做到对路由的全覆盖，也不会返回404
2. history模式
新的url可以是与当前URL同源的任意URL, 可以添加任意类型的数据到记录中, history模式下，前端的url必须和实际向后端发起的请求url一致，否则会返回404.


## Vuex
Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态。
### State
### Getter
### Mutation
### Action
### Module
### commit()与dispatch()
commit是提交一个mutation，因为vuex的store是响应式的，commit只能使同步的。dispatch不是直接更改状态，而是触发action，可以执行异步操作。
