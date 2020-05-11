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

```

