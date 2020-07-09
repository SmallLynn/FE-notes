## 让CSS flex布局最后一行列表左对齐的N种方法
##### 遇到的问题：
在CSS flex布局中，justify-content属性可以控制列表的水平对齐方式，例如space-between值可以实现两端对齐。

但是，如果最后一行的列表的个数不满，则就会出现最后一行没有完全垂直对齐的问题。
```js
/* css */
.container {
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
}
.list {
    width: 24%; 
    height: 100px;
    background-color: skyblue;
    margin-top: 15px;
}
/* html */
<div class="container">
    <div class="list"></div>
    <div class="list"></div>
    <div class="list"></div>
    <div class="list"></div>
    <div class="list"></div>
    <div class="list"></div>
    <div class="list"></div>
</div
```
#### 解决方法1: 列数不固定 使用Grid布局
```js
.container {
    display: grid;
    justify-content: space-between;
    grid-template-columns: repeat(auto-fill, 100px);
    grid-gap: 10px;
}
.list {
    width: 100px; height:100px;
    background-color: skyblue;
    margin-top: 5px;
}
```

#### 参考链接
[让CSS flex布局最后一行列表左对齐的N种方法](https://www.zhangxinxu.com/wordpress/2019/08/css-flex-last-align/)