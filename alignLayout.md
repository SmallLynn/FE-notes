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
### 列数不固定 每项长度固定
#### 解决方法1: 使用grid布局
```js
.container {
    display: grid;
    justify-content: space-between;
    grid-template-columns: repeat(auto-fill, 100px);
    grid-gap: 10px;
}
.list {
    width: 100px;
    height:100px;
    background-color: skyblue;
    margin-top: 5px;
}
```
### 列数固定 每项长度固定
#### 解决方法1: 中间的gap间隙使用margin进行控制
假设一行4个 除去第4,8...个不加margin-right
```js
.container {
    display: flex;
    flex-wrap: wrap;
}
.list {
    width: 24%;
    height: 100px;
    background-color: skyblue;
    margin-top: 15px;
}
.list:not(:nth-child(4n)) {
    margin-right: calc(4% / 3);
}
```
#### 解决方法2: 根据个数最后一个元素动态margin
例如，假设每行4个元素，结果最后一行只有3个元素，则最后一个元素的margin-right大小是“列表宽度+间隙大小”的话，那最后3个元素也是可以完美左对齐的。
例如：
1. list:last-child:nth-child(4n - 1)说明最后一行，要么3个元素，要么7个元素……
2. list:last-child:nth-child(4n - 2)说明最后一行，要么2个元素，要么6个元素……
```js
.container {
    display: flex;
    /* 两端对齐 */
    justify-content: space-between;
    flex-wrap: wrap;
}
.list {
    width: 24%;
    height: 100px;
    background-color: skyblue;
    margin-top: 15px;
}
/* 如果最后一行是3个元素 */
.list:last-child:nth-child(4n - 1) {
    margin-right: calc(24% + 4% / 3);
}
/* 如果最后一行是2个元素 */
.list:last-child:nth-child(4n - 2) {
    margin-right: calc(48% + 8% / 3);
}
```
#### 参考链接
[让CSS flex布局最后一行列表左对齐的N种方法](https://www.zhangxinxu.com/wordpress/2019/08/css-flex-last-align/)