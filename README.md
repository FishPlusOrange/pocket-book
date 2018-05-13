# Pocket Book

FishPlusOrange's pocket book. Just for recording.

Good good study, day day up.

## Table of Contents

### About Vue

关于 Vue。

<details>

<summary>View contents</summary>

- [Vuex 使用 commit 提交 mutation 修改 state 的原因](#vuex-使用-commit-提交-mutation-修改-state-的原因)
- [计算属性比较方法、侦听属性和过滤器](#计算属性比较方法侦听属性和过滤器)

</details>

### About Browser Compatibility

关于浏览器兼容性。

<details>

<summary>View contents</summary>

- [低版本 IE 浏览器下绝对定位的元素未设置背景时无法响应鼠标点击或悬浮事件](#低版本-ie-浏览器下绝对定位的元素未设置背景时无法响应鼠标点击或悬浮事件)
- [IE 浏览器下 iframe 弹窗中输入框光标丢失（无法输入）问题](#ie-浏览器下-iframe-弹窗中输入框光标丢失无法输入问题)
- [低版本 IE 浏览器下无法触发 oninput 事件](#低版本-ie-浏览器下无法触发-oninput-事件)
- [IE9 浏览器下在拖拽、剪切、删除操作时无法触发 propertychange 事件](#ie9-浏览器下在拖拽剪切删除操作时无法触发-propertychange-事件)
- [IE 浏览器下由于请求路径带中文参数导致发送 GET 请求时报400错误](#ie-浏览器下由于请求路径带中文参数导致发送-get-请求时报400错误)
- [IE 浏览器下由于请求路径过长导致发送 GET 请求时报500错误](#ie-浏览器下由于请求路径过长导致发送-get-请求时报500错误)
- [IE 浏览器下 td 边框消失](#ie-浏览器下-td-边框消失)
- [IE8 浏览器下 new Date('YYYY-MM-DD') 返回 NaN](#ie8-浏览器下-new-dateyyyy-mm-dd-返回-nan)

</details>

### About CSS

关于 CSS。

<details>

<summary>View contents</summary>

- [CSS3 -webkit-tap-highlight-color](#css3--webkit-tap-highlight-color)
- [CSS3 -webkit-appearance](#css3--webkit-appearance)

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

#### 计算属性比较方法、侦听属性和过滤器

Vue 使用了基于 HTML 的模板语法，在模板内我们一般只进行简单的数据处理。如果放入太多的逻辑会让模板过重且难以维护。所以，对于任何复杂逻辑，我们都应当使用[**计算属性**](https://cn.vuejs.org/v2/guide/computed.html)。

举一个简单的计算属性例子（假设是复杂逻辑）：

```html
<body>
    <div id="app">
        <span>{{ info }}</span>
    </div>
</body>

<script>
    let vm = new Vue({
        el: '#app',
        data: {
            name: 'FishPlusOrange',
            age: 24
        },
        computed: {
            info: function() {
                return this.name + 'is' + this.age + 'years old.';
            }
        }
    })
</script>
```

其中 info 即为计算属性，该值始终取决于 data 中 name 和 age 的值，因此当 name 和 age 发生改变时，计算属性 info 也会响应式地更新。

很多情况下我们发现计算属性（computed）能够实现的效果，通过方法（methods）、侦听属性（watch）和过滤器（filters）也分别能够实现。以下简单比较一下计算属性和它们的区别：

- ##### 计算属性和方法

以上计算属性例子通过方法实现：

```html
<body>
    <div id="app">
        <span>{{ showInfo() }}</span>
    </div>
</body>

<script>
    let vm = new Vue({
        el: '#app',
        data: {
            name: 'FishPlusOrange',
            age: 24
        },
        methods: {
            showInfo: function() {
                return this.name + 'is' + this.age + 'years old.';
            }
        }
    })
</script>
```

通过计算属性和方法实现的最终结果是完全相同的。不同的是**计算属性是基于它们的依赖进行缓存的**。

以上通过计算属性实现的例子中，只有当 name 和 age 发生改变时才会重新计算 info 的值，即只要 info 的相关依赖没有发生改变，多次访问 info 时将立即返回之前的计算结果，而不必再次执行 getter 函数。

相比之下，通过方法实现的话，每当触发重新渲染时，总会再次执行相应函数。

所以假设存在一个性能开销比较大的属性计算时，推荐使用计算属性。

- ##### 计算属性和侦听属性

以上计算属性例子通过侦听属性实现：

```html
<body>
    <div id="app">
        <span>{{ info }}</span>
    </div>
</body>

<script>
    let vm = new Vue({
        el: '#app',
        data: {
            name: 'FishPlusOrange',
            age: 24,
            info: 'FishPlusOrange is 24 years old'
        },
        watch: {
            name: function(val, oldVal) {
                this.info = val + 'is' + this.age + 'years old.';
            },
            age: function(val, oldVal) {
                this.info = this.name + 'is' + val + 'years old.';
            }
        }
    })
</script>
```

侦听属性中，当 name 和 age 发现改变时将重新计算 info 的值。但是，相比计算属性，代码是命令式且重复的。

所以，如果仅仅是属性计算，一般使用计算属性。而在属性值改变后再执行其他操作时可以使用侦听属性。

- ##### 计算属性和过滤器

以上计算属性例子通过过滤器实现：

```html
<body>
    <div id="app">
        <span>{{ name | info }}</span>
    </div>
</body>

<script>
    let vm = new Vue({
        el: '#app',
        data: {
            name: 'FishPlusOrange',
            age: 24
        },
        filters: {
            info: function(value) {
                if(!value) return '';
                return value + 'is' + this.age + 'years old.';
            }
        }
    })
</script>
```

过滤器虽然也可以实现以上计算属性例子的最终结果。但是，过滤器只能对 name 或 age 其中一个属性进行处理并返回相应结果。

所以，过滤器一般用于处理单个属性值。

[Back to TOC](#table-of-contents)

---

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

#### IE 浏览器下 iframe 弹窗中输入框光标丢失（无法输入）问题

在开发过程中遇到这样一个问题，IE 浏览器下弹出一个 iframe 弹窗，第一次可以正常获得输入框焦点，关闭弹窗后再打开（未刷新页面），无法获得输入框焦点，即无法输入。

网上查到一种方法，通过 IE 特有的方法 `CollectGarbage()` 来回收内存，经尝试问题依然没有得到解决。

个人理解造成该问题的主要原因是，IE 浏览器下在关闭（移除）弹窗（iframe 外层容器）时，iframe 并没有从 DOM 中移除，导致再打开弹窗时 DOM 冲突。经研究，主要有两种解决方案：

1. 在移除弹窗时，先移除 iframe：

```javascript
// window.parent.document 表示跨 iframe 操作父页面元素
$('iframe', window.parent.document).remove();
```

2. 每次打开弹窗时，手动获得输入框焦点：

```javascript
$('input').focus();
```

本人解决该问题时采用方案二，因为在实际解决过程中发现方案一在 IE10 版本下（非仿真模式）不可行，所以推荐**方案二**。

[Back to TOC](#table-of-contents)

#### 低版本 IE 浏览器下无法触发 oninput 事件

`oninput` 用于实时监听输入框 value 变化，不同于 `onchange` 事件要在值改变且失去焦点时触发。

`oninput` 是 HTML5 标准事件，低版本 IE 浏览器（IE9 以下）不支持，**可使用 IE 浏览器专有事件 `onpropertychange` 兼容**。

在 jQuery 中 `oninput` 和 `onpropertychange` 分别对应事件 `input` 和 `propertychange`。

二者区别：

- `oninput` 在 value 改变时实时触发（通过 js 改变 value 不触发），`onpropertychange` 在任何属性改变时均会触发。

- 在 JS 事件动态绑定方式上，`oninput` 可通过普通事件绑定方式和 `addEventListener` 注册。`onpropertychange` 只能通过普通事件绑定方式，所以在 jQuery 中其不能通过事件委托的方式绑定事件。

[Back to TOC](#table-of-contents)

#### IE9 浏览器下在拖拽、剪切、删除操作时无法触发 propertychange 事件

可在获得输入框焦点时设置定时器周期性触发 `propertychange` 事件以兼容该问题：

```javascript
$('input').each(function() {
    var $this = $(this);
    var _interval;
    $this.focus(function() {
        _interval = setInterval(function() {
            $this.trigger('propertychange');
        }, 300);
    });
    $this.blur(function() {
        clearInterval(_interval);
    });
});
```

[Back to TOC](#table-of-contents)

#### IE 浏览器下由于请求路径带中文参数导致发送 GET 请求时报400错误

可通过 `encodeURI` 函数对中文参数进行编码：

```javascript
// url 为请求路径
window.location.href = url + '?params=' + encodeURI(params);
```

[Back to TOC](#table-of-contents)

#### IE 浏览器下由于请求路径过长导致发送 GET 请求时报500错误

开发过程中通过 GET 请求下载或导出文件：

1. 通过 `window.open` 打开新窗口跳转请求：

```javascript
// url 为请求路径
window.open(url + '?params=' + encodeURI(params));
```

2. 通过 `window.location.href` 在本页面跳转请求：

```javascript
// url 为请求路径
window.location.href = url + '?params=' + encodeURI(params);
```

3. 通过模拟 form 表单提交打开新窗口跳转请求：

```javascript
// url 为请求路径
var form = document.createElement('form');
form.target = '_blank';
form.action = url;
form.method = 'get';
form.style.display = 'none';

var opt = document.createElement('textarea');
opt.name = 'params';
opt.value = params;
form.appendChild(opt);

document.body.appendChild(form);
form.submit();
fotm.remove();
```

> 方法1和3可配置 `target` 属性（方法1的 `target` 属性默认为 `_blank`，方法3的 `target` 属性默认为 `_self`），方法2仅能在本页面跳转（等同于 a 标签且 `target` 属性为 `_self`）。开发过程中发现部分版本 IE 浏览器下，打开新窗口跳转请求下载完成后，新窗口无法自动关闭，可通过配置 `target` 属性为 `_self` 解决该问题。

> 方法1和2使用 `encodeURI` 转码是因为 [IE 浏览器下请求路径带中文参数发送 GET 请求报400错误](#ie-浏览器下请求路径带中文参数发送-get-请求报400错误)。方法3不需要使用 `encodeURI` 转码是因为通过模拟 form 表单提交打开新窗口跳转请求会自动对中文参数进行转码。

测试过程中发现部分情况下 IE 浏览器会报500错误。

经研究，**IE 浏览器对最大请求路径长度进行了限制，限制长度为2048字节，这个限制对 POST 请求和 GET 请求的 URL 均适用**。因为 IE 浏览器下需要对请求路径中的中文参数进行转码，其可能导致请求路径长度超出限制，当发送 GET 请求时 IE 浏览器会报500错误。

解决方案是使用 POST 请求：

```javascript
// url 为请求路径
var form = document.createElement('form');
form.target = '_blank';
form.action = url;
form.method = 'post'; // 把 GET 请求改成 POST 请求
form.style.display = 'none';

var opt = document.createElement('textarea');
opt.name = 'params';
opt.value = params;
form.appendChild(opt);

document.body.appendChild(form);
form.submit();
fotm.remove();
```

[Back to TOC](#table-of-contents)

#### IE 浏览器下 td 边框消失

导致 td 边框消失的原因是该标签同时存在 `position: relative;` 与 `background-color` 属性。

推荐的解决方案是在该 td 下添加 div 并设置 `position: relative;` 把所有内容放在 div 中。

[Back to TOC](#table-of-contents)

#### IE8 浏览器下 new Date('YYYY-MM-DD') 返回 NaN

开发过程中，发现在 IE8 浏览器下通过 `new Date('YYYY-MM-DD')` 创建一个指定日期的 Date 对象，返回的却是 NaN：

```javascript
// IE8 浏览器下
var date = new Date('1994-04-05'); // NaN
```

在网上查找到了一个[解决方案](http://jibbering.com/faq/#parseDate)，即在 `new Date()` 前先调用 `parseISO8601` 方法：

```javascript
var date = new Date(parseISO8601('1994-04-05'));

function parseISO8601(dateStringInRange) {  
    var isoExp = /^\s*(\d{4})-(\d\d)-(\d\d)\s*$/,  
        date = new Date(NaN), month,  
        parts = isoExp.exec(dateStringInRange);  
    if(parts) {  
        month = +parts[2];  
        date.setFullYear(parts[1], month - 1, parts[3]);  
        if(month != date.getMonth() + 1) {  
            date.setTime(NaN);  
        }  
    }  
    return date;  
} 
```

[Back to TOC](#table-of-contents)

---

### About CSS

#### CSS3 -webkit-tap-highlight-color

在移动端开发过程中，发现一个问题：在 webkit 内核浏览器下，当点击一个链接时，其就会出现灰色高亮。

该样式由属性 [`-webkit-tap-highlight-color`](http://www.css88.com/webkit/-webkit-tap-highlight-color) 控制，可通过设置透明度为0禁用该属性：

```css
* {
    -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}
```

[Back to TOC](#table-of-contents)

#### CSS3 -webkit-appearance

在移动端开发过程中，发现一个问题：在 webkit 内核浏览器下，input 和 textarea 存在顶部内阴影。

该样式由属性 [`-webkit-appearance`](http://www.css88.com/webkit/-webkit-appearance) 控制，可通过设置值为 none 去除该样式：

```css
input,
textarea {
    -webkit-appearance: none;
}
```

[Back to TOC](#table-of-contents)
