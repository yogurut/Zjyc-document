# 页面打印时强行分页的处理table换行处理

设置在表格元素之后始终进行分页的分页行为：

```html
<html>
<head>
<style>
@media print
{
table {page-break-before:always;}
}
</style>
</head>

<body>
....
</body>
</html>
```

## 浏览器支持

所有浏览器都支持 page-break-before 属性。

注释：任何的版本的 Internet Explorer （包括 IE8）都不支持属性值 "left"、"right" 以及 "inherit"。

注释：Firefox、Chrome 以及 Safari 不支持属性值 "avoid"、"left" 以及 "right"。

## 定义和用法

page-break-before 属性设置元素前的 page-breaking 行为。

尽管可以用 always 强制放上分页符，但是无法保证避免分页符的插入，创作人员最多只能要求用户代理尽可能避免插入分页。

应用于：position 值为 relative 或 static 的非浮动块级元素。

注释：请尽可能少地使用分页属性，并且避免在表格、浮动元素、带有边框的块元素中使用分页属性。

| 默认值：          | auto                                    |
| :---------------- | --------------------------------------- |
| 继承性：          | no                                      |
| 版本：            | CSS2                                    |
| JavaScript 语法： | *object*.style.pageBreakBefore="always" |

## 可能的值

| 值      | 描述                                                |
| :------ | :-------------------------------------------------- |
| auto    | 默认值。如果必要则在元素前插入分页符。              |
| always  | 在元素前插入分页符。                                |
| avoid   | 避免在元素前插入分页符。                            |
| left    | 在元素之前足够的分页符，一直到一张空白的左页为止。  |
| right   | 在元素之前足够的分页符，一直到一张空白的右页为止。  |
| inherit | 规定应该从父元素继承 page-break-before 属性的设置。 |