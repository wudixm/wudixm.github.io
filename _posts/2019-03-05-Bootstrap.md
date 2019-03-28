## sr-only 属性

#### Conveying meaning to assistive technologies

[原文](https://getbootstrap.com/docs/3.4/css/)

Using color to add meaning to a table row or individual cell only provides a visual indication, which will not be conveyed to users of assistive technologies – such as screen readers. Ensure that information denoted by the color is either obvious from the content itself (the visible text in the relevant table row/cell), or is included through alternative means, such as additional text hidden with the `.sr-only` class.

## justify-content

justify-content 用于设置或检索弹性盒子元素在主轴（横轴）方向上的对齐方式。

**提示：**使用 align-content 属性对齐交叉轴上的各项（垂直）。

| 默认值：          | flex-start                                                   |
| ----------------- | ------------------------------------------------------------ |
| 继承：            | 否                                                           |
| 可动画化：        | 否。请参阅 [*可动画化（animatable）*](http://www.runoob.com/cssref/css-animatable.html)。 |
| 版本：            | CSS3                                                         |
| JavaScript 语法： | *object*.style.justifyContent="space-between"[尝试一下](http://www.runoob.com/try/try.php?filename=trycss3_js_justify-content) |

### CSS 语法

```
justify-content: flex-start|flex-end|center|space-between|space-around|initial|inherit;
```

### 属性值

| 值            | 描述                                                         | 测试                                                         |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| flex-start    | 默认值。项目位于容器的开头。                                 | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_justify-content&preval=flex-start) |
| flex-end      | 项目位于容器的结尾。                                         | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_justify-content&preval=flex-end) |
| center        | 项目位于容器的中心。                                         | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_justify-content&preval=center) |
| space-between | 项目位于各行之间留有空白的容器内。                           | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_justify-content&preval=space-between) |
| space-around  | 项目位于各行之前、之间、之后都留有空白的容器内。             | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_justify-content&preval=space-around) |
| initial       | 设置该属性为它的默认值。请参阅 [*initial*](http://www.runoob.com/cssref/css-initial.html)。 | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_justify-content&preval=initial) |
| inherit       | 从父元素继承该属性。请参阅 [*inherit*](http://www.runoob.com/cssref/css-inherit.html)。 |                                                              |

### 注意

**注意:** Internet Explorer 和 Safari 浏览器不支持 justify-content 属性。

## align-items 属性

###定义和用法

align-items 属性定义flex子项在flex容器的当前行的侧轴（纵轴）方向上的对齐方式。

**提示：**使用每个弹性对象元素的 align-self 属性可重写 align-items 属性。

| 默认值：          | stretch                                                      |
| ----------------- | ------------------------------------------------------------ |
| 继承：            | 否                                                           |
| 可动画化：        | 否。请参阅 [*可动画化（animatable）*](http://www.runoob.com/cssref/css-animatable.html)。 |
| 版本：            | CSS3                                                         |
| JavaScript 语法： | *object*.style.alignItems="center"[尝试一下](http://www.runoob.com/try/try.php?filename=trycss3_js_align-items) |

### CSS 语法

```
align-items: stretch|center|flex-start|flex-end|baseline|initial|inherit;
```

### 属性值

| 值         | 描述                                                         | 测试                                                         |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| stretch    | 默认值。元素被拉伸以适应容器。如果指定侧轴大小的属性值为'auto'，则其值会使项目的边距盒的尺寸尽可能接近所在行的尺寸，但同时会遵照'min/max-width/height'属性的限制。 | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_align-items&preval=stretch) |
| center     | 元素位于容器的中心。弹性盒子元素在该行的侧轴（纵轴）上居中放置。（如果该行的尺寸小于弹性盒子元素的尺寸，则会向两个方向溢出相同的长度）。 | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_align-items&preval=center) |
| flex-start | 元素位于容器的开头。弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴起始边界。 | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_align-items&preval=flex-start) |
| flex-end   | 元素位于容器的结尾。弹性盒子元素的侧轴（纵轴）起始位置的边界紧靠住该行的侧轴结束边界。 | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_align-items&preval=flex-end) |
| baseline   | 元素位于容器的基线上。如弹性盒子元素的行内轴与侧轴为同一条，则该值与'flex-start'等效。其它情况下，该值将参与基线对齐。 | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_align-items&preval=baseline) |
| initial    | 设置该属性为它的默认值。请参阅 [*initial*](http://www.runoob.com/cssref/css-initial.html)。 | [测试 »](http://www.runoob.com/try/playit.php?f=playcss_align-items&preval=initial) |
| inherit    | 从父元素继承该属性。请参阅 [*inherit*](http://www.runoob.com/cssref/css-inherit.html)。 |                                                              |