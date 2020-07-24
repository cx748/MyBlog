# 项目介绍
这个项目是利用vue框架开发的一个待办事项清单SPA(single-page application)网页。其中用到了vue的核心插件vue-router和Vuex, 并且用mock模拟后端的数据与ajax请求的接口。
# 项目分析
## 项目需求
1. 查询所有的待办事项，以及完成这些待办事项所需要的待办单项
2. 新增，修改待办事项
3. 删除，锁定待办事项 (锁定之后，待办事项的数据不能被改动)
4. 新增，修改，删除待办单项
5. 显示为完成的待办单项的计数
## 数据分析
需要在页面显示的数据有
* 待办事项的标题
* 待办事项的待办单项的文字内容
* 未完成的待办单项数目
隐式数据有
* 待办事项的锁的图标
* 待办事项的删除图标
* 待办事项的id
* 待办单项的删除图标
* 待办单项的完成状态
## 单个待办事项的数据结构
```text
{
  id: String,
  title: String,
  count: Number,
  idDelete: Boolean,
  locked: Boolean,
  record: [
    text: String,
    isDelete: Boolean,
    checked: Boolean
  ]
}
```
## api分析
用户主要有以下这些这些操作
* 首次进入页面，查看待办事项的列表
* 点击新增按钮，新增一个待办事项
* 点击一个待办事项，右边显示待办事项的详细内容
* 点击标题，修改待办事项的标题
* 点击删除图标，删除待办事项
* 点击锁定图标，锁定待办事项
* 点击加号图标，新增待办单项
* 点击待办单项内容，修改待办单项
* 点击待办单项旁的删除图片，删除待办单项
* 点击已完成按钮完成单项
### 待办事项列表
api: 接口名，params：传入的数据，data：返回的数据
```text
api: /todo/list(get)
params: {}
/*
左边的列表肯定是一个数组对象，因为我们每次点击待办事项的标题，就会显示
待办事项的详细内容，所以我们需要一个id来标识
*/
data: [
  {
    id: String,
    title: String,
    count: Number,
    locked: Boolean
  },
  {}
]
```
### 新增待办事项
```text
api: /todo/addTodo (post)
params: {}
data: {}
```
### 单个待办事项查询
```text
api: /todo/listId (get)
params: {id: xxx} //传入id，根据id返回待办事项的所有数据
data：{
  id: String,
  title: String,
  count: Number,
  isDelete: Boolean,
  locked: Boolean,
  record: [
    text: String,
    isDelete: Boolean,
    checked: Boolean
  ]
}
```
### 修改待办事项标题，删除，锁定待办事项
```text
api: /todo/editTodo (post)
/*
这里的是哪个操作都是修改的同一条待办事项，所以可以把这些合并在同一个接口
*/
params: {
  id: String,
  title: String,
  isDelete: Boolean,
  locked: Boolean
}

data: {}
```
### 新增待办单项
```text
api: /todo/addRecord (post)
params: {}
data: {}
```
### 修改、删除、完成待办单项
```text
api: /todo/editRecord (post)
params: {
  text: String,
  isDelete: Boolean,
  checked: Boolean
}
data: {}
```
## 组件分析
* SPA引用本身是一个大组件
* 里面的布局是一个组件，它里面包含了其他组件
* 左边的待办事项列表是一个组件，包含列表与新增
* 右边的单个待办事项算一个组件，它包含上半部分与下半部分
* 待办单项是一个组件，他要被循环渲染多次
所以最终可以得到：
```text
app.vue // 最外层根组件
layouts.vue // 布局组件，接入其他组件
menus.vue // 左边待办事项列表组件
todos.vue // 右边待办事项的内容
item.vue // 事项的待办单项组件
```
# mock模拟数据与接口
## 安装mock、axios依赖
```text
npm install mockjs
npm install axios
npm install axios-mock-adapter --save-dev // 这是axios与mock的配置器，把二者结合起来
```
## 创建mock
### 新建src/mock/data/todoList.js  
todoList.js文件是初始化我们需要的数据，再进行导出
```js
import Mock from 'mockjs'
let Todos = []
const COUNT = [1, 2, 3, 4, 5]
for (let i =1; i <= COUNT.length; i ++){
  Todos.push(Mock.mock({
    id: Mock.Random.guid(),
    title: Mock.Random.first(),
    isDelete: false,
    locked: Mock.Random.boolean(),
    record: COUNT.map(() => {
      return {
        text: Mock.Random.cparagraph(2),
        idDelete: false,
        checked: Mock.Random.boolean()
      }
    })
  }))
}
export { //导出列表数据
  Todos
}
```
### 新建src/mock/mock.js
核心代码，作用是模拟ajax请求，生成并返回模拟数据
```js
import axios from 'axios'
import MockAdapter from 'axios-mock-adapter' // axios与mock的配置
import Mock from 'mockjs'
import {Todos} from './data/todoList' // 导入Todos数据
export default {
  start () { // 初始化函数
    let mock = new MockAdapter(axios); // 创建MockAdapter实例
    // 获取todo列表
    mock.onGet('/todo/list').reply(config => {
      let mockTodo = Todos.map(todo => { // 从todoList中获取左边列表所需要的的数据，并赋值给mockTodo
        return {
          id: todo.id,
          title: todo.title,
          count: todo.record.filter((data) => {
            if (data.checked === false) {
              return true
            }
            return false
          }).length, // 过滤record里面checked为true的数据
          locked: todo.locked,
          isDelete: todo.isDelete
        }
      }).filter(todo => {
        if (todo.isdelete === true) {
          return false
        }
        return true
      }) // 过滤掉isDelete为true的todo，因为已经被删除了
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve([200, {todos: mockTodo}]) //返回状态为200，并且返回todos数据
        }, 200)
      })
    })

    // 新增一条todo
    mock.onPost('/todo/addTodo').reply(config => {
      Todos.push({
        id: Mock.Random.guid(),
        title: 'newList',
        isDelete: false,
        locked: false,
        record: []
      })
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve([200])
        }, 200)
      })
    })

    // 获取待办事项todo的详细信息,即页面应用的右上部分
    mock.onGet('/todo/listId').reply(config => { // 有参get请求
      let {id} = config.params // 根据传入的参数，确定id
      // 然后根据id和现有的Todos匹配，找出id相等的数据，再进行返回
      let todo = Todos.find(todo => {
        return id && todo.id === id
      })
      // todo.count(等待完成数目)等于todo.record的没被勾选的单项的数目
      todo.count = todo.record.filter((data) => {
        return data.checked === false
      }).length
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resovle([200, {todo: todo}]) //返回状态码200和todo数据
        }, 200)
      })
    })

    // 新增一条待办事项的待办单项
    mock.onPost('/todo/addRecord').reply(config => {
      // id是传进来的值唯一待办事项的id。text是用户新增输入的文本数据
      let {id, text} = JSON.parse(config.data) // JSON.parse将字符串转换成对象
      Todos.some((t, index) => {
        if (t.id === id) {
          t.record.push({
            text: text,
            isDelete: false,
            checked: false
          })
          return true
        }
      })
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve([200])
        }, 200)
      })
    })

    // 修改待办事项的标题
    mock.onPost('/todo/editTodo').reply(config => {
      let {todo} = JSON.parse(config.data)
      Todos.some((t,index) => {
        if (t.id === todo.id) {
          t.title = todo.title
          t.locked = todo.locked
          t.idDelete = todo.isDelete
          return true
        }
      })
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve([200])
        }, 200)
      })
    })

    //修改待办单项的标题
    mock.onPost('/todo/editRecord').reply(config => {
      let {id, record, index} = JSON.parse(config.data)
      Todos.some((t) => {
        if (t.id === id) {
          t.record[index] = record
          return true
        }
      })
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve([200])
        }, 200)
      })
    })
  }
}
```
## 导出mock
新建src/mock/index.js，方便其他文件引入接口api函数
```js
import mock from './mock'; 
export default mock; // 导入同级下mock.js的内容，并且导出
```
## 封装api函数，新建src/api/api.js
把我们需要调用的接口写到同一个文件里面，封装成一个一个的函数，需要使用时直接调用这个函数
```js
import axios from 'axios'

export const getTodoList = params => { // 封装一个函数，名为getTodoList
  return axios.get(`/todo/list`, {params: params}) // 请求的路径是'/todo/list'，成功会返回待办事项列表数据
}

export const addTodo = params => { // 封装一个函数，名为addTodo
  return axios.post(`/todo/addTodo`, params).then(res => res.data) // 请求的路径是'/todo/addTodo'，成功会新增加一条title为newList的待办事项，并且返回状态码200
}

export const getTodo = params => { // 封装一个函数，名为getTodo
  return anxios.get(`/todo/listId`, {params: params}) // 请求的路径是'/todo/listId'，成功会返回id下的待办事项的数据详情
}

export const addRecord = params => { //封装一个函数，名为addRecord
  return anxios.post(`/todo/addRecord`, params).then(res => res.data) // 请求的路径是'/todo/addRecord'，成功会返回id下的待办事项的数据详情
}

export const editTodo = params => {
  return axios.post(`/todo/editTodo`, params).then(res => res.data) // 请求的路径是'/todo/listId'，成功会返回id下的待办事项的数据详情
}

export const editRecord = params => {
  return axios.post(`/todo/editRecord`, params).then(res => res.data) // 请求的路径是'/todo/listId'，成功会返回id下的待办事项的数据详情
}
```
`至此，我们已经完成了数据与接口的模拟，接下来我们开始着手用VUE全家桶来调用这些数据和接口，来完成我们的单页SPA项目`

# 项目布局与组件
## 在/src/main.js文件里创建单页应用的根实例
```js
import Vue from 'vue'
import App from './App'
import router from './router'
import Mock from './mock'
Mock.start() // 生成模拟数据与ajax请求的接口

new Vue({ // 创建一个Vue的根实例
  el: '#app', // 挂载id，这个实例下的所有内容都会在index.html的一个id为app的div下显示
  router, // 注入路由配置
  components: { App }, // 配置根模板
  template: '<App/>' // 注入组件
})
```
## 新建layouts.vue(布局组件)
在 src/components/目录下新建一个文件 layouts.vue
```vue
<template>
  <section class="container" > <!--最外层容器-->
    <section class="menu"> <!--左边的容器-->
    </section>
    <section class="content-container"><!--右边的容器-->
    </section>
  </section>
</template>
<script>
  export  default {};
</script>
<style lang="less">
@import '../common/style/layouts.less';
</style>
```
忘了提醒，样式是用less写的，我们需要先下载好less和webpack的loader:  
在终端输入
```text
npm install --save-dev less-loader style-loader less
```
在webpack的配置文件 /build/webpack.base.conf.js 加上
```js
module.exports = {
  module: {
    rules: [
      {   //把这个对象添加在里面
        test: /\.less$/,
        use: [
          'style-loader',
          { loader: 'css-loader', options: { importLoaders: 1 } },
          'less-loader'
        ]
      }
    ]
  }
}
```
## menus.vue(菜单组件)
在src/components/目录下新建一个menus.vue
```vue
<template>
  <div class="list-todos">  <!--菜单容器-->
      <a v-for="(item, index) in items" :key="index"  class="list-todo activeListClass list" > <!--单个菜单容器-->
        <span v-if="item.locked" class="icon-lock" ></span>  <!--锁的图标-->
        <span v-if="item.count" class="count-list">{{item.count}}</span><!--数字-->
        {{item.title}}
      </a>
    <a class=" link-list-new" > <!--新增菜单-->
      <span class="icon-plus">
      </span>
      新增
    </a>
  </div>
</template>
<script>
export default {
  data () { // data函数
    return {
      items: [{ title: '星期一', count: 1, locked: true }, // 菜单的模拟数据
        { title: '星期二', count: 2, locked: true },
        { title: '星期三', count: 3, locked: false }
      ]
    }
  }
}
</script>
<style lang="less">
@import '../common/style/menu.less';
</style>
```
## 新建todo.vue(待办事项详情组件)
```vue
<template>
  <div class="page lists-show"><!--最外层容器-->
    <nav><!--容器上半部分-->
      <div class="nav-group"> <!--移动端的菜单图标-->
        <a class="nav-item">
          <span class="icon-list-unordered">
          </span>
        </a>
      </div>
      <h1 class="title-page">
        <span class="title-wrapper">{{todo.title}}</span> <!-- 标题-->
        <span class="count-list">{{todo.count}}</span><!-- 数目-->
      </h1>
      <div class="nav-group right"><!-- 右边的删除，锁定图标容器-->
        <div class="options-web">
          <a class=" nav-item"> <!-- 锁定图标-->
            <span class="icon-lock" v-if="todo.locked"></span>
            <span class="icon-unlock" v-else>
            </span>
          </a>
          <a class=" nav-item"><!-- 删除图标-->
            <span class="icon-trash">
            </span>
          </a>
        </div>
      </div>
      <div class=" form todo-new input-symbol"> <!-- 新增单个代办单项输入框,监听了回车事件，双向绑定text值,监听了disabled属性，在todo.locked为true的情况下无法编辑-->
        <input type="text" v-model="text" placeholder='请输入' @keyup.enter="onAdd" :disabled="todo.locked" />
        <span class="icon-add"></span>
      </div>
    </nav>
    <div class="content-scrollable list-items">
      <!--容器下半部分-->
      <div v-for="(item, index) in items" :key="index">
        <item :item="item"/>
      </div>
    </div>
  </div>
</template>
<script>
import item from './item'
export default {
  data () {
    return {
      todo: { // 详情内容
        title: '星期一',
        count: 12,
        locked: false
      },
      items: [ // 代办单项列表
        { checked: false, text: '新的一天', isDelete: false },
        { checked: false, text: '新的一天', isDelete: false },
        { checked: true, text: '新的一天', isDelete: false }
      ],
      text: '' // 新增代办单项绑定的值
    }
  },
  components: {
    item
  },
  methods: {
    onAdd () {
      this.items.push({
        checked: false, text: this.text, isDelete: false
      }) // 当用户点击回车时候 ，给items的值新增一个对象，this.text 即输入框绑定的值
      this.text = '' // 初始化输入框的值。
    }
  }
}
</script>
<style lang = "less">
@import '../common/style/nav.less';
@import '../common/style/form.less';
@import '../common/style/todo.less';
</style>
```
## item.vue(单个待办单项组件)
```vue
<template>
  <div class="list-item editingClass editing"> <!-- 最外层容器-->
    <label class="checkbox"> <!--自定义的多选框-->
      <input type="checkbox" v-model="item.checked"> <!--item.checked-->
      <span class="checkbox-custom"></span>
    </label>
    <input type="text" v-model="item.text">  <!--绑定item.text-->
    <a class="delete-item"> <!--删除图标-->
      <span class="icon-trash"></span>
    </a>
  </div>
</template>
<script>
export default {
  props: ['item'] // 子组件显式的用 props 选项声明它期待获得的数据
  // 这里申明 它想要一个叫做 ’item‘的数据。
}
</script>
<style lang="less">
@import '../common/style/list-items.less';
</style>
```
## 更新layouts组件
```vue
<template>
  <section class="container" > <!--最外层容器-->
    <section class="menu"> <!--左边的容器-->
      <menus/>
    </section>
    <section class="content-container"><!--右边的容器-->
      <todo />
    </section>
  </section>
</template>
<script>
import menus from './menus'
import todo from './todo'
export default {
  components: {
    menus,
    todo
  }
}
</script>
<style lang="less">
@import '../common/style/layouts.less';
</style>
```
`此时，我们已经通过VUE与vue-router完成了静态页面的开发。接下来我们要从接口获取数据，并且用vuex集中管理这些数据`
# 将之前的静态数据换成我们之前设计的接口
## 更改menu.vue
我们首先更改menu.vue的代码，将我们之前手动设置的items数组列表设置为从后台接口获取待办事项列表数据
```vue
<template>
  <div class="list-todos">  <!--菜单容器-->
      <a @click="goList(item.id)" v-for="(item, index) in items" :key="index"  class="list-todo activeListClass list" > <!--单个菜单容器-->
        <span v-if="item.locked" class="icon-lock" ></span>  <!--锁的图标-->
        <span v-if="item.count" class="count-list">{{item.count}}</span><!--数字-->
        {{item.title}}
      </a>
    <a @click="addTodoList" class=" link-list-new" > <!--新增菜单-->
      <span class="icon-plus">
      </span>
      新增
    </a>
  </div>
</template>
<script>
import {getTodoList, addTodo} from '../api/api'
export default {
  data () { // data函数
    // return {
    //   items: [{ title: '星期一', count: 1, locked: true }, // 菜单的模拟数据
    //     { title: '星期二', count: 2, locked: true },
    //     { title: '星期三', count: 3, locked: false }
    //   ]
    // }
    return {
      items: [], // 待办事项列表数组数据
      todoId: '' // 默认选中id
    }
  },
  watch: { // 监听todoId的值，todoId发生改变之后切换子路由
    'todoId' (id) {
      this.$router.push({name: 'todo', params: {id: id} }) // 更换当前的子路由
    }
  }, 
  created () { // 在实例创建完成后，我们立即向模拟的后台请求数据
    getTodoList({}).then(res => {
      console.log(res)
      const TODOS = res.data.todos // 把返回数据的todos对象赋给TODOS
      this.items = TODOS // 把从接口获得的待办事项列表数据赋给this.items
      this.todoId = TODOS[0].id // 默认选中待办事项列表的第一项待办事项
    })
  },
  methods: {
    goList (id) { // 点击待办事项时，替换选中的id
      this.todoId = id
    },
    addTodoList () { // 点击增加按钮，调用新增菜单的接口，在接口调用成功后请求数据
      addTodo({}).then(() => { // 接口调用成功后会更新后台里的待办事项数据
        // console.log(data)
        getTodoList({}).then(res => { // 请求更新后的待办事项数据
          console.log(res)
          const TODOS = res.data.todos
          this.items = TODOS
          this.todoId = TODOS[TODOS.length - 1].id
        })
      })
    }
  }
}
</script>
<style lang="less">
@import '../common/style/menu.less';
</style>
```
## 更新todo.vue
```vue
<template>
  <div class="page lists-show"><!--最外层容器-->
    <nav><!--容器上半部分-->
      <div class="nav-group"> <!--移动端的菜单图标-->
        <a class="nav-item">
          <span class="icon-list-unordered">
          </span>
        </a>
      </div>
      <h1 class="title-page">
        <span class="title-wrapper">{{todo.title}}</span> <!-- 标题-->
        <span class="count-list">{{todo.count}}</span><!-- 数目-->
      </h1>
      <div class="nav-group right"><!-- 右边的删除，锁定图标容器-->
        <div class="options-web">
          <a class=" nav-item"> <!-- 锁定图标-->
            <span class="icon-lock" v-if="todo.locked"></span>
            <span class="icon-unlock" v-else>
            </span>
          </a>
          <a class=" nav-item"><!-- 删除图标-->
            <span class="icon-trash">
            </span>
          </a>
        </div>
      </div>
      <div class=" form todo-new input-symbol"> <!-- 新增单个代办单项输入框,监听了回车事件，双向绑定text值,监听了disabled属性，在todo.locked为true的情况下无法编辑-->
        <input type="text" v-model="text" placeholder='请输入' @keyup.enter="onAdd" :disabled="todo.locked" />
        <span class="icon-add"></span>
      </div>
    </nav>
    <div class="content-scrollable list-items">
      <!--容器下半部分-->
      <div v-for="(item, index) in items" :key="index">
        <!-- 向子组件item传递数据items待办事项的代办单项 -->
        <item :item="item" :index="index" :id="todo.id" :init="init" :locked="todo.locked"></item>
      </div>
    </div>
  </div>
</template>
<script>
import item from './item'
import { getTodo } from '../api/api'
export default {
  data () {
    return {
      todo: { // 详情内容
        title: '帕吉的小肚腩',
        count: 24,
        locked: false
      },
      items: [ // 代办单项列表
      ],
      text: '' // 新增代办单项绑定的值
    }
  },
  components: {
    item
  },
  watch: {
    '$route.params.id' () {
      this.init()
    }
  },
  created () {
    this.init()
  },
  methods: {
    onAdd () {
      this.items.push({
        checked: false, text: this.text, isDelete: false
      }) // 当用户点击回车时候 ，给items的值新增一个对象，this.text 即输入框绑定的值
      this.text = '' // 初始化输入框的值。
    },
    init () {
      // 获取到$route.params下的id，当前所在路由的id，即我们在menus.vue组件处传入的数据
      console.log(this.$route)
      const ID = this.$route.params.id
      getTodo({ id: ID }).then(res => {
        // 根据当前所在待办事项的id获得待办事项详情
        console.log(res)
        let {id, title, count, isDelete, locked, record} = res.data.todo
        // 请求成功，将record赋值给待办单项列表this.items
        this.items = record
        // 请求成功，将待办事项列表的详情数据赋给todo
        this.todo = {
          id: id,
          title: title,
          count: count,
          isDelete: isDelete,
          locked: locked
        }
      })
    }
  }
}
</script>
<style lang = "less">
@import '../common/style/nav.less';
@import '../common/style/form.less';
@import '../common/style/todo.less';
</style>
```
## 修改路由
```js
...
...
export default new Router({
  routes: [{
    path: '/',
    name: 'Layouts',
    component: Layouts,
    children: [{
      path: '/todo/:id',
      name: 'todo',
      component: todo
    }]
  }]
})
```
## 更新item.vue
```vue
<template>
  <div class="list-item editingClass editing"> <!-- 最外层容器-->
    <label class="checkbox"> <!--自定义的多选框-->
      <input type="checkbox" v-model="item.checked"> <!--item.checked-->
      <span class="checkbox-custom"></span>
    </label>
    <input type="text" v-model="item.text">  <!--绑定item.text-->
    <a class="delete-item"> <!--删除图标-->
      <span class="icon-trash"></span>
    </a>
  </div>
</template>
<script>
// item是todo的子组件，他接受一个对象item，来进行处理
export default {
  props: { // 子组件显式的用 props 选项声明它期待获得的数据,这里申明 它想要一个叫做 'item'的数据。
    item: {
      type: Object,
      default: () => {
        return {
          checked: false,
          text: '你好,世界'
        }
      }
    }
  }
}
</script>
<style lang="less">
@import '../common/style/list-items.less';
</style>

```
# vuex改造项目并完成剩余交互
我们知道，在这个项目中的menu.vue与todo.vue不是父子组件的关系，而我们想让他们共享一组数据并且互相通信，除了，将数据放在他们共同的父组件中，然后再相互传递数据，我们还可以用vuex。Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。
## 安装vuex
```
npm install vuex --save
```
## 新建src/vuex/store.js
```js
import Vue from 'vue'
import Vuex from 'vuex'
import * as actions from './actions.js' // gettes，actions，mutations，state在另外的文件定义，容易维护
import * as getters from './getters'
import * as mutations from './mutaitions'
import * as state from './state'

Vue.use(Vuex)

// 创建store实例并且导出
export default new Vuex.Store({
  actions,
  getters,
  state,
  mutations
})

```
### 新建src/vuex/state.js
```js
// 创建初始应用的全局状态变量
export const state = {
  todoList: [] // 我们待办事项的列表数据
}

```
### 新建src/vuex/mutations.js
```js
export const mutations = {
  EDITTODE (state, data) { // 定义mutation操作用于改变todoList的值
    state.todoList = data
  }
}
```
### 新建src/vuex/getters.js
```js
export const getTodoList = state => {
  return state.todoList // 派生状态下的todoList
}

```
### 新建src/vuex/actions.js
```js
import {getTodoList} from '../api/api' // 引入接口函数getTodoList，用于请求最新的待办事项列表数据

export const getTodo = ({
  commit
}) => {
  return new Promise((resolve, reject) => {
    // 调用getTodo这个函数的时候，会调用ajax请求函数，函数返回值后，在调用store.js里定义的mutations里的
    // EDITTODE方法，并且把值传给EDITTODE
    getTodoList().then(res => {
      commit('EDITTODE', res.data.todos) // 将返会值的todos传给EDITTODE
      resolve()
    })
  })
}
```
## 在main.js中注入vuex的store配置
```js
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue' // 引入vue模块
import App from './App' // 引入vue组件
import router from './router' // 引入路由
import Mock from './mock' // 引入mock插件
import store from '../vuex/store'
Mock.start() // 生成模拟数据与ajax请求的接口

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({ // 创建一个Vue的根实例
  el: '#app', // 挂载id，这个实例下的所有内容都会在index.html的一个id为app的div下显示
  router, // 注入路由配置
  store, // 注入store配置
  components: { App }, // 配置根模板
  template: '<App/>' // 注入组件
})

```