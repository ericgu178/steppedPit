# Vuex 状态管理

###　核心玩法

1. state 是 Vuex 状态

2. getters 是解决computed方法  就是 很多组件都要用这个改变后的数据  如果有多个组件需要用到此属性，我们要么复制这个函数，或者抽取到一个共享函数然后在多处导入它——无论哪种方式都不是很理想。

   用到 视图界面的话 就是 computed 计算属性里写 `this.$store.getters.doneTodos` 方法 或者直接用vuex提供的辅助函数 mapGetters 映射使用 用法就是如下

   ```js
   import {mapGetters} from 'vuex'
   export default {
   	data(){
   		return{
              wenzhanglist:[],
              newwenzhanglist:[],
              page:0,
              current:1,
   		}
   	},
   	created(){ //初始化
       },
       computed: {
           ...mapGetters({
               todos:'doneTodos',
               doneTodosCount:'doneTodosCount'
           })
       },
   ```

   ```js
   const store = new Vuex.Store({
       state: {
           count: 0,
           todos: [
               { id: 1, text: '...', done: true },
               { id: 2, text: '...', done: true }
           ]
       },
       // 突变 只能 是 同步  异步的话 无法记录回调内状态的变化
       mutations: {
           increment (state,value) {
               state.todos.push(value)
           }
       },
       // 提交的是 突变  Action 可以包含任意异步操作。
       actions: {
   
       },
       // 是解决computed方法
       getters: {
           doneTodos: state => {//通过方法访问
               return state.todos.filter(todo => todo.done)
           },
           doneTodosCount: (state, getters) => {//通过属性访问
               return getters.doneTodos.length
           }
       }
   })
   
   ```

3. ### Mutation 突变 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation 只能是同步 不能够异步

   ```js
   // 你可以向 store.commit 传入额外的参数，即 mutation 的 载荷（payload）：下面的value就是 payload 大多数 都是要提交 object对象 如果对象里 有 type 的话 
   this.$store.commit({
     type: 'increment',
     amount: 10
   }) //就可以省略掉前面 的类型了
   // 大写 下划线_ 间隔 SET_ACCESS_TOKEN 例如
   mutations: { 
           increment (state,value) {
               state.todos.push(value)
           }
       },
   ```

4. Action 提交的是 mutation，而不是直接变更状态。

5. Action 可以包含任意异步操作。{commit,state,dispatch} commit 提交 mutation 修改状态 如果是在视图层 使用的话

   用 `this.$store.dispatch("xxxx",{xx,x,x})` 这样使用

   ```js
   const user = {
     actions: {
       // 根据用户名登录
       LoginByUsername({commit,state,dispatch}, userInfo) {
         return new Promise((resolve, reject) => {
           const user = encryption({
             data: userInfo,
             key: '1234567887654321',
             param: ['password']
           })
   
           loginByUsername(user.username, user.password, user.code, user.randomStr).then(response => {
             const data = response.data
             setToken(data.access_token)
             commit('SET_ACCESS_TOKEN', data.access_token)
             commit('SET_REFRESH_TOKEN', data.refresh_token)
             commit('CLEAR_LOCK')
             resolve()
           }).catch(error => {
             reject(error)
           })
         })
       },
       // 根据手机号登录
       LoginByPhone({
         commit,
         state,
         dispatch
       }, userInfo) {
         const mobile = userInfo.mobile.trim()
         return new Promise((resolve, reject) => {
           mobileLogin(mobile, userInfo.code).then(response => {
             const data = response.data
             setToken(data.access_token)
             commit('SET_ACCESS_TOKEN', data.access_token)
             commit('SET_REFRESH_TOKEN', data.refresh_token)
             commit('CLEAR_LOCK')
             resolve()
           }).catch(error => {
             reject(error)
           })
         })
       },
       GetTableData({
         commit,
         state,
         dispatch
       }, page) {
         return new Promise((resolve, reject) => {
           // 未定义
           // getTableData(page).then(res => {
           //   const data = res.data
           //   resolve(data)
           // })
         })
       },
       GetUserInfo({
         commit,
         state,
         dispatch
       }) {
         return new Promise((resolve, reject) => {
           getUserInfo(state.token).then(response => {
             const data = response.data.data
             commit('SET_ROLES', data.roles)
             commit('SET_USER_INFO', data.sysUser)
             commit('SET_PERMISSIONS', data.permissions)
             resolve(response)
           }).catch(error => {
             reject(error)
           })
         })
       },
       // 登出
       LogOut({
         commit,
         state
       }) {
         return new Promise((resolve, reject) => {
           logout(state.access_token, state.refresh_token).then(() => {
             // 清除菜单
             commit('SET_MENU', [])
             // 清除权限
             commit('SET_PERMISSIONS', [])
             // 清除用户信息
             commit('SET_USER_INFO', {})
             commit('SET_ACCESS_TOKEN', '')
             commit('SET_REFRESH_TOKEN', '')
             commit('SET_ROLES', [])
             commit('DEL_ALL_TAG')
             removeToken()
             resolve()
           }).catch(error => {
             reject(error)
           })
         })
       },
       // 注销session
       FedLogOut({
         commit
       }) {
         return new Promise(resolve => {
           // 清除菜单
           commit('SET_MENU', [])
           // 清除权限
           commit('SET_PERMISSIONS', [])
           // 清除用户信息
           commit('SET_USER_INFO', {})
           commit('SET_ACCESS_TOKEN', '')
           commit('SET_REFRESH_TOKEN', '')
           commit('SET_ROLES', [])
           commit('DEL_ALL_TAG')
           removeToken()
           resolve()
         })
       },
       // 获取系统菜单
       GetMenu({
         commit
       }) {
         return new Promise(resolve => {
           GetMenu().then((res) => {
             const data = res.data
             data.forEach(ele => {
               ele.children.forEach(child => {
                 if (!validatenull(child.component)) child.path = `${ele.path}/${child.path}`
               });
             });
             commit('SET_MENU', data)
             resolve(data)
           })
         })
       }
     },
     mutations: {
       SET_ACCESS_TOKEN: (state, access_token) => {
         state.access_token = access_token
         setStore({
           name: 'access_token',
           content: state.access_token,
           type: 'session'
         })
       },
       SET_MENU: (state, menu) => {
         state.menu = menu
         setStore({
           name: 'menu',
           content: state.menu,
           type: 'session'
         })
       },
       SET_USER_INFO: (state, userInfo) => {
         state.userInfo = userInfo
         setStore({
           name: 'userInfo',
           content: state.userInfo,
           type: 'session'
         })
       },
       SET_REFRESH_TOKEN: (state, rfToken) => {
         state.refresh_token = rfToken
         setStore({
           name: 'refresh_token',
           content: state.refresh_token,
           type: 'session'
         })
       },
       SET_ROLES: (state, roles) => {
         state.roles = roles
         setStore({
           name: 'roles',
           content: state.roles,
           type: 'session'
         })
       },
       SET_PERMISSIONS: (state, permissions) => {
         const list = {}
         for (let i = 0; i < permissions.length; i++) {
           list[permissions[i]] = true
         }
         state.permissions = list
         setStore({
           name: 'permissions',
           content: state.permissions,
           type: 'session'
         })
       }
     }
   }
   ```

6. 最后就是 module  支持 模块开发 不用写在一个文件中 最外层的 是根vuex 里面的是 模块vuex

7. 目录结构标准展示

   ```
   ├── index.html
   ├── main.js
   ├── api
   │   └── ... # 抽取出API请求
   ├── components
   │   ├── App.vue
   │   └── ...
   └── store			     # 写项目独立出一个store 文件夹 里面index.js 入口文件
       ├── index.js          # 我们组装模块并导出 store 的地方
       ├── actions.js        # 根级别的 action
       ├── mutations.js      # 根级别的 mutation
       └── modules
           ├── cart.js       # 购物车模块
           └── products.js   # 产品模块
   ```

```js

import Vue from 'vue'
import Vuex from 'vuex'
import user from './modules/user'
import common from './modules/common'
import tags from './modules/tags'
import errLog from './modules/errLog'
import getters from './getters'
// 入口文件示例如下
Vue.use(Vuex)
const store = new Vuex.Store({
  modules: {
    user,
    common,
    errLog,
    tags
  },
  getters,
})

export default store

```

