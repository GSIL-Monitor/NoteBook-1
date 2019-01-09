# CSS选择器

## 元素选择器

最常见的 CSS 选择器是元素选择器。换句话说，文档的元素就是最基本的选择器。

如果设置 HTML 的样式，选择器通常将是某个 HTML 元素，比如 p、h1、em、a，甚至可以是 html 本身：

```html
*{color:red;}     
html {color: black;}
h1 {color: blue;}
h2 {color: silver}
h1, h2, h3, h4, h5, h6 {color:blue;}
h1 {
  font: 28px Verdana;
  color: blue;
  background: red;
  }
h1, h2, h3, h4, h5, h6 {
  color:gray;
  background: white;
  padding: 10px;
  border: 1px solid black;
  font-family: Verdana;
  }

```

## 类选择器

```html
p.important {color:red;}
```

如以上，选择器只会匹配 class 属性包含 important 的所有 p 元素，但是其他任何类型的元素都不匹配，不论是否有此 class 属性。选择器 p.important 解释为：“其 class 属性值为 important 的所有段落”。

```html
.important.urgent {background:silver;}
```

## CSS伪类

**css伪类用于向某些选择器添加特殊的效果**

伪类的语法：

```html
select：pseudo-class {property: value}
```

css类也可与伪类搭配使用：

```html
selector.class : pseudo-class {property: value}
```

锚伪类：

```html
a:link {color: #FF0000}		/* 未访问的链接 */
a:visited {color: #00FF00}	/* 已访问的链接 */
a:hover {color: #FF00FF}	/* 鼠标移动到链接上 */
a:active {color: #0000FF}	/* 选定的链接 */
```

:first-child伪类：
选择属于其父元素的首个子元素的每个\<p>元素，并为其设置样式：
```html
p:first-child {
  background-color:yellow;
}
```
如上，设置的为\<p>元素的样式，其为其父元素的首个子元素

## 后代选择器

**后代选择器可以选择作为某元素后代的元素**

在后代选择器中，规则左边的选择器一端包括两个或多个用空格分隔的选择器

选择器之间的空格是一种结合符（combinator），每个空格结合符可以解释为“... 在 ... 找到”、“... 作为 ... 的一部分”、“... 作为 ... 的后代”，但是要求必须从右向左读选择器。

```html
h1 em {color:red;}
```

以上，h1 em 选择器可以解释为 “作为 h1 元素后代的任何 em 元素”。

## 子类选择器

**与后代选择器相比，子元素选择器只能选择作为某元素子元素的元素。**

如果您不希望选择任意的后代元素，而是希望缩小范围，只选择某个元素的子元素，请使用子元素选择器

```html
h1 > strong {color:red;}
```

