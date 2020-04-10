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



