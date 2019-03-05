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

