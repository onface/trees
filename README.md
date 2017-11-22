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
    使用书写顺序覆盖 .m-box 的样式,
    不要通过增加选择器权重的方式覆盖样式
    bad: html .m-box--on {}
*/
````

**trees(树状结构)对应DOM结构**

```
box on
|-- hd
|  `-- title
`-- bd
```

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

多个子模块组合在一起就是家族模块 `family modules`，下例就是 `text` 家族的模块分别有 `text-a` `text-b`。

家族模块可以放在一个文件夹下管理 `/m/text/a.css` `/m/text/b.css`

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

## 继承

例如 form 模块中提交按钮需要使用 m-btn 的样式则通过 less/sass 继承的方式实现

```html
<div class="m-form">
    <button class="m-form-submit" type="submit" >Submit</button>
</div>
```

```less
.m-form-submit {
    .m-btn();
    .m-btn--primary();
    box-shadow: 0px 0px 10px skyblue;
}
```

## 组合

一个 html  tag 的 class 不允许出现2个不同模块，但可以嵌套 （**模块中禁止**，**页面中避免**）

**bad**
```html
<div class="m-form m-list">
    <div class="m-list-item m-form-item">
        <input class="m-form-item-input" type="text" />
    </div>
</div>
```
**good**
```html
<div class="m-form">
    <div class="m-list">
        <div class="m-list-item">
            <div class="m-form-item">
                <input class="m-form-item-input" type="text" />
            </div>
        </div>
    </div>
</div>
```

因为如何一个 html tag 有2个不同模块的 class 应该使用上节的继承来做。


## 页面

与**模块**命名规则一致，但不需要 `.m-` 前缀，若项目是SPA类型则以页面 `url` 作为前缀

| 页面地址 | class |
|--------|----------------|
| `/user/login` `/user#/login` | `.user_login-box` `.user_login-box-title` |

> 在SPA场景可以使用 `_` 作为 `url` 中 `/` 的替代符号，建议使用 [CSS Modules](http://www.ruanyifeng.com/blog/2016/06/css_modules.html)。


## 命名格式

**驼峰命名：**
class 允许出现驼峰命名 `box-mainPhoto` `.m-box-mainPhoto` `.m-box-mainPhoto--lastChild`。

**下划线：**  如果你使用了 css-module 则可以使用 `_` 代替 `-` 。 `.m-box-title` `=>` `.m_box_title`


## free

`.m-head-F-inner`

`F = free`

允许出现以下情况：

```html
<div class="m-head">
    <div class="m-head-F-inner">
        <img src="./logo.png" alt="" class="m-head-logo">
        <div class="m-head-nav"></div>
    </div>
</div>
```
```css
.m-head {}
.m-head-F-inner {
    width:1200px;
    margin-left:auto;
    margin-right: auto;
}
```

当模块已经开发完成并上线后，需要在现有的 HTML 的结构上再加上一层也可以使用 `m-模块名-F-名称` 的命名方式。

自由模块可以出现在模块的任意位置，甚至包裹住模块，或者与模块同级。但是不兼容滥用自由模块。自由模块是为了在开发效率和维护性中找到一个平衡点而存在的。
