# vue 开发中所遇问题解决

### vue 行内样式背景图片不显示问题

通过 require 先引入图片 直接在行内引入图片路径不会解析 没有深入了解 ~~貌似是dom没有加载完毕~~ 在style里写的css样式的话 会有 css-loader 帮助处理

```js
export default {
        data() {
            return {
                 bg:{
                      background:'url(' + require('../../../public/images/details/组1.png') + ') no-repeat',				backgroundSize:'cover',
                    },
             }
        },
}

// html 这样使用
<div :style="bg"></div>

```

### created 和 mounted 的区别

**created** <font color="red" size="5">在模板渲染成html之前调用，即通常初始化某些属性值，然后在渲染成视图。比如 页面加载的loading 就可以在这里让loading消失</font>。

**mounted** <font color="red" size="5">在模板渲染成html后调用，即通常是初始化页面完成后，再对html的dom节点进行业务操作。像 图表echarts 就需要放在mounted里操作 不然页面还没加载完毕 就调取的话 是找不到对应的dom节点操作的 </font>



## 关于打包没起作用 px2rem-loader 

remUnit为1rem=75px，remPrecision为转化后小数点后位数

首先 webpack 的 rule 读取 是从后往前读取的。

这个本地开发界面是挺好的 但是打包后页面就乱掉了 这是因为像素转rem的原因 

```js
chainWebpack: config => {
        config.module
            .rule('css')
            .test(/\.css$/)
            .oneOf('vue')
            .resourceQuery(/\?vue/)
            .use('px2rem')
            .loader('px2rem-loader')
            .options({
                remUnit: 192, // 1px = 192rem
                remPrecision: 8 // 为转化后小数点后位数
            })
    }
```

这是我之前的设置 没有起作用 因为 规则被覆盖掉了 在github vue-cli 中 isuse 中 找到答案

```js
// 这里打包不起作用？？？本地开发起作用？？？
    chainWebpack: config => {
        config.module
            .rule('css')
            .test(/\.css$/)
            .oneOf('vue')
            .resourceQuery(/\?vue/)
            .use('px2rem')
            .loader('px2rem-loader')
            .before('postcss-loader') // 这个 插入的语法就是用 before() 指定插在哪个 use 之前。
            .options({
                remUnit: 192, // 1px = 192rem
                remPrecision: 8 // 为转化后小数点后位数
            })
    }
```

顺序问题 就解决了 

## router-view 组件的使用

解释 路由嵌套 子路由切换 主页面不变化局部页面发生变化就要用到路由嵌套 router-view

这是 index.vue

```vue
<template>
    <div id="mobileIndex">
        <router-view></router-view> 
        <div style="margin-top:10px;height:60px;width:100%;"></div>
        <Affix class="bot">
            <Menu mode="horizontal" theme="light" @on-select="chooseResult" active-name="mobile_apply_manage">
                <Menu-item :name="item.key" style="font-size:.2rem;text-align:center;width:calc(100% / 3);" :key="item.key" v-for="item in menu">
                    <Icon :type="item.icon" />{{item.title}}
                </Menu-item>
            </Menu>
        </Affix>
    </div>
</template>
<script>
export default {
    data() {
        return {
            menu:[{
                title:'我的申请',
                key:"mobile_apply_manage",
                icon:"ios-color-filter"
            },{
                title:'我的待办',
                key:"mobile_todo_manage",
                icon:"ios-pulse"
            },{
                title:'我的已办',
                key:"mobile_done_manage",
                icon:"ios-game-controller-a-outline"
            }],
        }
    },
    methods:{
        chooseResult(e) {
            return this.$router.push({ path: `/${e}` }, onComplete => { }, onAbort => { })
            console.log(e)
        },
    }
}
</script>
<style scoped>
.bot {
    position:fixed;
    bottom:0;
    width:100%;
}
</style>
```

路由的设置 主路由如下 子路由如下

```js
export const mobileRouter = {
    path: '/index',
    name: 'mobile_index',
    meta: {
        title: '移动端'
    },
    redirect: '/mobile_apply_manage',
    component: mobile, // 这个mobile指的是 index.vue页面
    children:[ // 下面是子路由
        { path: '/mobile_apply_manage', title: '我的申请', name: 'mobile_apply_manage', component: () => import('@/views/activiti/mobile/mobile_apply_manage.vue') },
        { path: '/mobile_todo_manage', title: '我的待办', name: 'mobile_todo_manage', component: () => import('@/views/activiti/mobile/mobile_todo_manage.vue') },
        { path: '/mobile_done_manage', title: '我的已办', name: 'mobile_done_manage', component: () => import('@/views/activiti/mobile/mobile_done_manage.vue') },
    ],
}
```

总结 router-view 一定是路由嵌套才可以使用 否则它找不到页面路由 也跳转不过去

vue 中 跳转新标签页方法

```js
let newpage = this.$router.resolve({ 
    path: 'home',  
})
console.log(newpage)
window.open(newpage.href, '_blank');
```

