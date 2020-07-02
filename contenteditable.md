## vue示例 可切换编辑的span

#####contenteditable 元素基本使用
初步实现 v-model 和 contenteditable 子组件双向绑定
```js
/* 点击编辑触发focus函数 将span切换编辑状态
 * contenteditable -> true
 * 在外部点击触发blur函数取消编辑状态
 * contenteditable -> false
 */
<span
    ref="textEl"
    class="text"
    v-text="currentValue"
    @focus="handleInputFocus"
    @input="handleInput"
    @blur="handleInputBlur"
    @compositionstart="handleCompositionStart"
    @compositionend="handleCompositionEnd"
    @keydown.enter.prevent="$event.target.blur()"
></span>
<i class="icon-right iconfont iconbianjibianji" v-if="editable && !isFocus" @click="handleEdit"></i>
```
```js
/* 进入编辑状态 */
handleEdit(evt) {
    this.$refs.textEl.contentEditable = 'plaintext-only'
    this.$refs.textEl.focus();
    this.$emit('edit', evt);
    evt && evt.stopPropagation(); // 防止冒泡触发click
},
/* 取消编辑状态 */
handleInputBlur() {
    this.isFocus = false;
    this.$refs.textEl.contentEditable = false;
    this.$emit('blur', this.$refs.textEl.textContent);
},
```
#####问题1: 如何让contenteditable元素只能输入纯文本
```js
contenteditable="plaintext-only"
```
Tip: 目前仅Chrome浏览器支持
#####问题2：回避回车带来的问题
回车问题比较复杂，我在实际应用中其实只需要单行 contenteditable 元素。
因此监听回车键后让 contenteditable 元素失去焦点，回避了这个问题的复杂性。
```js
@keydown.enter.prevent="$event.target.blur()"
```
#####问题3：中文输入法带来的问题及解决
当开始中文输入法时停止执行 input 事件处理
当结束中文输入法时执行input 事件处理类似的逻辑
```js
/* html */
@compositionstart="handleCompositionStart"
@compositionend="handleCompositionEnd"
```
```js
handleInput(){
    if(!this.compositionStart) {
        /* maxlength限制输入长度 */
        this.maxlength && (this.$refs.textEl.textContent = this.$refs.textEl.textContent.substr(0, this.maxlength));
        this.currentValue = this.$refs.textEl.textContent;
        /* 输入时保持光标在最后 */
        this.$nextTick(()=>{
            this.keepLastIndex(this.$refs.textEl)
        })
        /* 实现v-model双向绑定*/
        this.$emit('input', this.$refs.textEl.textContent);
    }
},
/* 解决中文输入法问题 */
handleCompositionStart() {
    this.compositionStart = true;
},
handleCompositionEnd() {
    /* maxlength限制输入长度 */
    this.maxlength && (this.$refs.textEl.textContent = this.$refs.textEl.textContent.substr(0, this.maxlength));
    this.currentValue = this.$refs.textEl.textContent;
    /* 输入时保持光标在最后 */
    this.$nextTick(()=>{
        this.keepLastIndex(this.$refs.textEl)
    })
    /* 实现v-model双向绑定*/
    this.$emit('input', this.$refs.textEl.textContent);
    this.compositionStart = false;
},
```
#####问题4：光标定位到前面问题解决
出现的错误是因为直接修改父组件 data 后，子组件重新渲染，光标回到了开始的地方。
```js
/**
*  编辑时光标移到最后
*  obj => element元素
*/
keepLastIndex(obj) {
    if (window.getSelection){
        obj.focus(); // 解决firefoxf不获取焦点无法定位问题
        var range = window.getSelection(); // 创建range
        range.selectAllChildren(obj); // range 选择obj下所有子内容
        range.collapseToEnd(); // 光标移至最后
    }
}
```
```js
handleInput(){
    if(!this.compositionStart) {
        /* maxlength限制输入长度 */
        this.maxlength && (this.$refs.textEl.textContent = this.$refs.textEl.textContent.substr(0, this.maxlength));
        this.currentValue = this.$refs.textEl.textContent;
        this.$nextTick(()=>{
            this.keepLastIndex(this.$refs.textEl)
        })
        /* 实现v-model双向绑定*/
        this.$emit('input', this.$refs.textEl.textContent);
    }
},
/* 获得焦点光标移到最后位置 */
handleInputFocus() {
    this.isFocus = true;
    this.keepLastIndex(this.$refs.textEl);
    this.$emit('focus', this.$refs.textEl.textContent);
},
```