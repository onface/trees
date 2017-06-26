# Trees

## 使用 normalize.css 而不是 reset.css

使用 [normalize.css 5.0](http://necolas.github.io/normalize.css/) (Chrome, Firefox, Opera, Safari 6+, IE 8+)

> 若要兼容IE6则使用 [normalize.css 1.0 ](https://github.com/necolas/normalize.css/tree/v1) (IE6+)

此规范的核心理念是通过 `{grandpaElement}-{FatherElement}-{SonElement}` 的方式书写 class ，以**降低选择器权重**和**明确样式命中元素**。  
并提供 `.m-box--on` 状态机制来管理同一个模块的多种展现形式。

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
.m-box--on {
    border-color:orange;
    background-color: #FFF1D9;
}
.m-box--on .m-box-hd {
    border-bottom-color: orange;
}
/*
    注意书写顺序已覆盖 .m-box 的样式,
    不要通过增加选择器权重的方式覆盖样式
    bad: html .m-box--on {}
*/
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
.m-some-item-light {
    font-weight:bold;
}
.m-some--night .m-some-item--light {
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
.m-text-b--error .m-text-b-title{
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

### 不允许在子选择器后出现 class 选择器

```css
// Bad
.m-table>thead>tr>.m-table-some {}
```

这种情况应该使用子模块

```css
// free
.m-table-some{}
```

子选择器后面不出现 class 选择器则可以**快速计算样式的权重** `.m-table>thead>tr>th` `0.0.1.3` 甚至不用管最后一级权重。直接认定为 `0.0.1.*`。

需要**快速计算样式的权重**的原因是某些情况下需要覆盖 `.m-table>thead>tr>td` 样式。

```html
<table class="m-table" >
  <thead>
    <tr><th class="some" ></th></tr>
  </thead>
</table>
```

```css
// 0.0.1.3
.m-table>thead>tr>th {
    max-width:100px
}
// 0.0.2.0
.m-table .some {
    max-width: 30px
}
```

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

## table

简化 table class 的命名

```
.m-table-thead-tr-th            .m-table-th
.m-table-tbody-tr               .m-table-tr
.m-table-tbody-tr-td            .m-table-td
.m-table-tbody-tr-td-table      .m-table-2
.m-table-thead-tr-td-table      .m-table-thead-2
```

````html
<table class="m-table" >
    <thead class="m-table-thead">
        <tr class="m-table-thead-tr">
            <th class="m-table-th">Name</th>
            <th class="m-table-th">Age</th>
        </tr>
    </thead>
    <tbody class="m-table-tbody" >
        <tr class="m-table-tr">
            <td class="m-table-td">
                Nimo
            </td>
            <td class="m-table-td">
                24
            </td>
        </tr>
        <tr class="m-table-tr">
            <td class="m-table-td">
                Nico
            </td>
            <td class="m-table-td">
                <!-- 2 === level tow -->
                <table class="m-table-2" >
                    <thead class="m-table-2-thead">
                        <tr class="m-table-2-thead-tr" >
                            <th class="m-table-2-th" >Some</th>
                        </tr>
                    </thead>
                    <tbody class="m-table-2-tbody">
                        <tr class="m-table-2-tr" >
                            <td class="m-table-2-td" >
                                <table class="m-table-3">...</table>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </td>
        </tr>
    </tbody>
</table>
````

## 页面

与**模块**命名规则一致，但不需要 `.m-` 前缀，若项目是SPA类型则以页面 `url` 作为前缀

| 页面地址 | class |
|--------|----------------|
| `/user/login` `/user#/login` | `.user_login-box` `.user_login-box-title` |

> 在SPA场景可以使用 `_` 作为 `url` 中 `/` 的替代符号，建议使用 [CSS Modules](http://www.ruanyifeng.com/blog/2016/06/css_modules.html)。


## 细则

**驼峰命名：**  
class 允许出现驼峰命名 `box-mainPhoto` `.m-box-mainPhoto` `.m-box-mainPhoto--lastChild`。

**下划线：**  如果你使用了 css-module 则可以使用 `_` 代替 `-` 。 `.m-box-title` `=>` `.m_box_title`
