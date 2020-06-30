## input 框的调整

```css
input {
        border:rgba(107,115,130,0.5) 2px solid;
        border-radius: 10px;
        outline:none;
        background-color:rgba(107,115,130,0.5);
        color:#fff;
        font-family: Cambria, Cochin, Georgia, Times, 'Times New Roman', serif;
    }
/* 修改input其中 placeholder属性颜色 其他属性同理 */
    input::-webkit-input-placeholder {
       color:rgba(205,209,214,0.5)
    }

input::-webkit-input-placeholder, textarea::-webkit-input-placeholder { 
 /* WebKit browsers */ 
	color: #666; 
} 
input:-moz-placeholder, textarea:-moz-placeholder { 
/* Mozilla Firefox 4 to 18 */ 
	color: #666; 
} 
input::-moz-placeholder, textarea::-moz-placeholder { 
 /* Mozilla Firefox 19+ */ 
	color: #666; 
} 
input:-ms-input-placeholder, textarea:-ms-input-placeholder { 
 /* Internet Explorer 10+ */ 
	color: #666; 
}
```

## 原生js获取dom节点 并且赋予属性值

```js
var container = document.querySelector("#mobile1-0001");
var matches = container.querySelectorAll("div.ivu-date-picker-rel");
matches.forEach(dom=>{
  var temp = dom.querySelector("input")
  temp.setAttribute('readonly',true)
})
```



```
<input type="number" pattern="[0-9]*"> 

对表单验证来说，这两个正则的作用是一样的，表现的话差异就很大：
iOS中，只有[0-9]*才可以调起九宫格数字键盘，\d 无效
Android 4.4以下(包括X5内核)，两者都调起数字键盘；
Android 4.4.4以上，只认 type 属性，也就是说，如果上面的代码将 type=“number” 改为 type=“text” ，将调起全键盘而不会是九宫格数字键盘。
```

```css
// 超出部分 ...替代
overflow: hidden;
white-space: nowrap;
text-overflow: ellipsis;


// 多行换行 超出省略

display: block;
display: -webkit-box;
word-wrap:break-word;
margin: 0 auto;
line-height: 1.4;
-webkit-line-clamp: 3;
-webkit-box-orient: vertical;
overflow: hidden;
text-overflow: ellipsis;
```

### 关于border 边框长度自定义的解决方案 

```css
.right{
    position: relative;
}
.right::after {
    content: '';
    height: 120px;
    border-left: 1px solid rgba(218,218,218);
    position: absolute;
    top: 40px;
    left: 0px;
}// 使用伪类通过定位实现

#sidebarnav li:hover::after  这样 hover 变化
```

# css 动画



```css
animation: myMove1 3s linear infinite normal; // 循环
animation: name duration timing-function delay iteration_count direction

name: 是 @-webkit-keyframes 中需要指定的方法，用来执行动画。
duration: 动画一个周期执行的时长。
timing-function: 动画执行的效果，可以是线性的，也可以是"快速进入慢速出来"等。
delay: 动画延时执行的时长，如果不需要可以省略。
iteration_count: 动画循环执行次数，如果是infinite，则无限执行。
direction: 动画执行方向，省略的话默认顺时针。


@keyframes myMove1 {
	0% {     
            -webkit-transform: rotate(0deg);     
            transform: rotate(0deg);     
          }     
          100% {     
            -webkit-transform: rotate(360deg);     
            transform: rotate(360deg);     
          }    
}

@-webkit-keyframes myMove1 {
	0% {     
            -webkit-transform: rotate(0deg);     
            transform: rotate(0deg);     
          }     
          100% {     
            -webkit-transform: rotate(360deg);     
            transform: rotate(360deg);     
          }   
}
```

# 字体引入

```css
@font-face {
    font-family: '298CAI';
    src: url('../../fonts/298-CAI978.ttf');
  }
```

