## 工具类函数
#### 计算总页数
1. 判断总数是否大于0
2. 判断总数是否比每页数量少
```js
/* 计算页数 */
getPageCount(total, pageSize) {
    return total > 0 ? ((total <= pageSize) ? 1 : ((total + pageSize - 1) / pageSize)) : 0;
},
```