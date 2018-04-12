# Pocket Book

FishPlusOrange's pocket book. Just for recording.

Good good study, day day up.

## Table of Contents

### About Vue

关于 Vue。

<details>

<summary>View contents</summary>

- [Vuex 使用 commit 提交 mutation 修改 state 的原因](#vuex-使用-commit-提交-mutation-修改-state-的原因)

</details>

### About Browser Compatibility

关于浏览器兼容性。

<details>

<summary>View contents</summary>

- [低版本 IE 浏览器下绝对定位的元素未设置背景时无法响应鼠标点击或悬浮事件](#低版本-ie-浏览器下绝对定位的元素未设置背景时无法响应鼠标点击或悬浮事件)

</details>

## Contents

### About Vue

#### Vuex 使用 commit 提交 mutation 修改 state 的原因

首先，[Vuex 官方文档](https://vuex.vuejs.org/zh-cn/mutations.html)表示：

> 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。

但是在开发过程中发现直接修改 state 也是可行的，所以二者有什么区别呢。

Vuex 提供**严格模式**，查看源码发现，`commit` 函数内部通过 `_withCommit` 函数和 `_withCommit` 函数内部的 `_committing` 状态值来修改 state。而严格模式下由 `enableStrictMode` 函数通过观察 `_committing` 状态值，如果非 true 则调用 `assert` 函数抛出错误 **“Do not mutate vuex store state outside mutation handlers”**。

所以如果不使用 commit 提交 mutation 修改 state，那么在严格模式下就会报错。更重要是，[Vuex 官方文档](https://vuex.vuejs.org/zh-cn/strict.html)还表示：

> 在严格模式下，无论何时发生了状态变更且不是由 mutation 函数引起的，将会抛出错误。这能保证所有的状态变更都能被调试工具跟踪到。

**故直接修改 state 所发生的状态变更无法被 Vue 调试工具 vue-devtools 跟踪到**。

[Back to TOC](#table-of-contents)

### About Browser Compatibility

#### 低版本 IE 浏览器下绝对定位的元素未设置背景时无法响应鼠标点击或悬浮事件

推荐的解决方案是通过设置 background 属性 hack：

```css
div {
    background: url(about:blank);
}
```

其他解决方案：

- 不使用绝对定位
 
- 添加背景色
 
- 添加透明背景图片

[Back to TOC](#table-of-contents)
