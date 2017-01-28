# Trees

## 使用 normalize.css 而不是 reset.css

使用 [normalize.css 5.0](http://necolas.github.io/normalize.css/) (Chrome, Firefox, Opera, Safari 6+, IE 8+)

> 若要兼容IE6则使用 [normalize.css 1.0 ](https://github.com/necolas/normalize.css/tree/v1) (IE6+)

此规范的核心理念是通过 `{grandpaElement}-{FatherElement}-{SonElement}` 的方式书写 class ，以**降低选择器权重**和**明确样式命中元素**。  
并提供 `html .m-box--on` 状态机制来管理同一个模块的多种展现形式。

## 模块 `/m/`

模块代码以 `.m-` 作为 class 前缀。（若担心 `m` 出现冲突可换成其他单词）


````html
<h2>基础</h2>
<div class="m-box">
    <div class="m-box-hd">
        <span class="m-box-hd-title">title</span>
    </div>
    <div class="m-box-bd">
        text
    </div>
</div>

<h2>高亮</h2>
<div class="m-box m-box--on">
    <div class="m-box-hd">
        <span class="m-box-hd-title">title</span>
    </div>
    <div class="m-box-bd">
        text
    </div>
</div>
````

````css
/* 模块代码均以 .m- 作为前缀 */
.m-box {
    border: 1px solid #CCC;
    background-color: #EEE;
}
/* .m-{模块名}-{子元素} */
.m-box-hd {
    line-height: 30px;
    border-bottom:1px solid #CCC;
}
/* .m-{模块名}-{子元素}-{子元素} */
.m-box-hd-title {
    font-size: 28px;
}
/* .m-{模块名}-{子元素} */
.m-box-bd {
    font-size: 12px;
    background-color: white;
}
/* .m-{模块名}--{状态} */
html .m-box--on {
    border-color:orange;
    background-color: #FFF1D9;
}
html .m-box--on .m-box-hd {
    border-bottom-color: orange;
}
/* 选择器前加 html 是为了提高状态的CSS权重 */
````

### 多级状态

```html
<div class="m-some m-some--night">
    <div class="m-some-item">A</div>
    <div class="m-some-item m-some-item--light">B</div>
    <div class="m-some-item">C</div>
</div>
```
```css
html .m-some-item-light {
    font-weight:bold;
}
html .m-some--night .m-some-item--light {
    text-shadow:0,0,10px,white;
}
```

### 子模块

**非常相似的** 的 **简单** 模块可使用多个子模块开发


```html
<div class="m-text-a">
    <div class="m-text-a-title">审核中</div>
    <div class="m-text-a-text">任务正在审核</div>
</div>
```
```css
/* /m/text/a.less */
.m-text-a {}
.m-text-a-title {font-size:2em;}
.m-text-a-text {color:gray;}
```

```html
<div class="m-text-b">
    <div class="m-text-b-title">备案</div>
    <div class="m-text-b-text">请填写您的企业信息提交备案</div>
</div>
<div class="m-text-b m-text-b--error">
    <div class="m-text-b-title">备案未通过</div>
    <div class="m-text-b-text">无法查询到你的营业执照</div>
</div>
```
```css
/* /m/text/b.less */
.m-text-b {}
.m-text-b-title {text-align: center;}
.m-text-b-text {font-size:12px;}
html .m-text-b--error .m-text-b-title{
    color:red;
}
```

子模块可以嵌套在任意位置

```html
<div class="m-head">
    <div class="m-head-inner">
        <div class="m-head-nav">...</div>
        <div class="m-head-login">...</div>
    </div>
</div>
```
```css
/* free  */
.m-head-inner {
    width:1100px;margin-left: auto;margin-right: auto;
}
```

**在任何位置嵌套的子模块需要在选择增加 `/* free */` 或者 `// free` 的注释**

## 不允许出现以下代码


### 不允许 class 中只有状态 `class="m-some-item--on"`

```html
<!-- Bad -->
<div class="m-some">
    <div class="m-some-item--on">A</div>
    <div class="m-some-item">B</div>
</div>
```
```html
<!-- Good -->
<div class="m-some">
    <div class="m-some-item m-some-item--on">A</div>
    <div class="m-some-item">B</div>
</div>
```

### 不允许出现 element 选择器

**Bad**
```css
p {/* ... */}
.m-demo strong {/* ... */}
```
**good**
```css
.m-section {/* ... */}
.m-demo-import {/* ... */}
```

## table 可以使用子选择器选择元素

```css
.m-table>thead {/* ... */}
.m-table>thead>tr {/* ... */}
.m-table>thead>tr>th {/* ... */}
.m-table>tbody {/* ... */}
.m-table>tbody>tr {/* ... */}
.m-table>tbody>tr>td {/* ... */}
```

```html
<table class="m-table">
    <thead>
        <tr>
            <th>Name</th>
            <th>Age</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>nimo</td>
            <td>24</td>
        </tr>
        <tr>
            <td>judy</td>
            <td>24</td>
        </tr>
    </tbody>
</table>
```

## 页面

与**模块**命名规则一致，但不需要 `.m-` 前缀，若项目是SPA类型则以页面 `url` 作为前缀

| 页面地址 | class |
|--------|----------------|
| `/user/login` `/user#/login` | `.user_login-box` `.user_login-box-title` |

> 在SPA场景可以使用 `_` 作为 `url` 中 `/` 的替代符号，建议使用 [CSS Modules](http://www.ruanyifeng.com/blog/2016/06/css_modules.html)。

## 细则

**驼峰命名：**  
class 允许出现驼峰命名 `box-mainPhoto` `.m-box-mainPhoto` `.m-box-mainPhoto--lastChild`。
但绝不允许在第一个单词中使用驼峰，例如： `Box-mainPhoto` `.m-Box-mainPhoto`

**下划线：**  如果你使用了 css-module 则可以使用 `_` 代替 `-` 。 `.m-box-title` `=>` `.m_box_title`
