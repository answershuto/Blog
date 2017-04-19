## 左列固定宽度，右列自适应

### 使用flex布局实现

```css
.container{
    display: flex;
}
.l{
    width: 100px;
}
.r{
    flex: 1;
}
```

[Demo](../examples/CSS常见布局/demo1.html)

### 使用float以及margin实现

```css
.l{
    width: 100px;
    float: left;
}
.r{
    margin-left: 100px;
}
```

[Demo](../examples/CSS常见布局/demo2.html)

### 使用float以及overflow实现

```css
.l{
    width: 100px;
    float: left;
}
.r{
    overflow: hidden;
}
```

通过设置overflow创建了一个(BFC)[http://www.cnblogs.com/cjlalala/p/5556855.html]，可以形成一个独立的容器，浮动盒区域不叠加在BFC上，容器内子元素不会影响到外面的元素，反之亦然。设置overflow:auto;等都能达到同样效果，只需要形成一个BFC。

[Demo](../examples/CSS常见布局/demo3.html)

### 使用table实现

```css
.container{
    display: table;
    table-layout: fixed;
    width: 100%;
}
.l{
    width: 100px;
}
.r, .l{
    display: table-cell;
}
```

[Demo](../examples/CSS常见布局/demo4.html)

## 右列固定宽度，左列自适应

### 使用flex布局实现

```css
.container{
    display: flex;
}
.l{
    flex: 1;
}
.r{
    width: 100px;
}
```

[Demo](../examples/CSS常见布局/demo5.html)

### 使用float以及margin实现

```css
.l{
    float: left;
    margin-right: -100px;
    width: 100%;
}
.r{
    float: right;
    width: 100px;
}
```

[Demo](../examples/CSS常见布局/demo6.html)

### 使用table实现

```css
.container{
    display: table;
    table-layout: fixed;
    width: 100%;
}
.r{
    width: 100px;
}
.l, .r{
    display: table-cell;
}
```

[Demo](../examples/CSS常见布局/demo7.html)

## 两侧固定宽度，中间自适应

### 使用flex布局

```css
.container{
    display: flex;
}
.l{
    width: 100px;
}
.c{
    flex: 1;
}
.r{
    width: 100px;
}
```

[Demo](../examples/CSS常见布局/demo8.html)

### 使用float以及margin布局

```css
 .container{
            
}
.l{
    float: left;
    width: 100px;
}
.c{
    float: left;
    margin-right: -200px;
    width: 100%;
}
.r{
    float: left;
    width: 100px;
}
```

[Demo](../examples/CSS常见布局/demo9.html)

### 使用table布局

```css
.container{
    display: table;
    table-layout: fixed;
    width: 100%;
}
.l{
    width: 100px;
}
.r{
    width: 100px;
}
.l, .r, .c{
    display: table-cell;
}
```

[Demo](../examples/CSS常见布局/demo10.html)

## 左列不定宽，右列自适应

### 利用flex实现

```css
.container{
    display: flex;
}
.r{
    flex: 1;
}
```

[Demo](../examples/CSS常见布局/demo11.html)

### 利用table实现

```css
.container{
    display: table;
}
.l{
    background-color: red;
    width: 1%;
}
.r{
    background-color: gainsboro;
}
.l, .r{
    display: table-cell;
}
```

[Demo](../examples/CSS常见布局/demo12.html)

### 利用float实现

左侧元素浮动，右侧元素通过overflow形成BFC。

```css
.l{
    float: left;
    background-color: blanchedalmond;
}
.r{
    overflow: hidden;
    background-color: red;
}
```

[Demo](../examples/CSS常见布局/demo13.html)

## 多列等分布局

### 利用flex实现

```css
.container{
    display: flex;
}
.ele{
    flex: 1;
}
```
[Demo](../examples/CSS常见布局/demo14.html)

### 利用float实现

```css
.ele{
    float: left;
    width: 25%;
}
```

[Demo](../examples/CSS常见布局/demo15.html)

### 利用table实现

```css
.container{
    display: table;
    width: 100%;
    table-layout: fixed;
}
.ele{
    display: table-cell;
}
```

[Demo](../examples/CSS常见布局/demo16.html)