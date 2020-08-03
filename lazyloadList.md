## 列表滚动下拉懒加载
#### PerfectScrollbar插件监听容器滚动到底事件
```js
<PerfectScrollbar
    class="el-main canvas-guide-content"
    :options="{ suppressScrollX: true, wheelPropagation: false }"
    @ps-y-reach-end="handleContentScroll"
    id="guideContent"
>
 </PerfectScrollbar>
```
```js
/* 判断是否是最后一页 */
templateNoMore() {
    return this.templateCurPage * this.templatePageSize >= this.templateTotal;
},
/* 是否加载下一页的条件是 -> 正在加载数据或者是最后一页 */
templateLoadingDisabled() {
    return this.isTemplateLoading || this.templateNoMore;
},
```
```js
handleTemplateLazyLoad() {
    /* 符合条件的事件才会被触发 */
    if(!this.templateLoadingDisabled) {
        this.queryTemplate(this.templateCurPage + 1, this.activeGroup, {...this.curSort})
    }
},
```
#### 问题1: 初始化第一页列表长度过短未出现滚动条导致不能看到后几页
在列表下方添加手动加载按钮 
```js
<div class="load-more-text">
    <span class="has-more" @click="handleLoadMore" v-if="!templateLoadingDisabled">点击加载更多</span>
    <span class="no-more" v-if="templateNoMore">没有更多了</span>
</div>
```